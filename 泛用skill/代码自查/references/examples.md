# 示例

本文件提供 code-self-review 的快速模式与标准模式完整示例，作为输出质量基准。

> 定位说明：本文件的**示意代码仅作审查对象演示，不是代码风格基准**；输出格式与报告结构才是质量基准。

素材为示意代码（非真实项目代码），技术栈贴合个人主力栈：FastAPI + SQLAlchemy 2.x async。

## 示意代码

```python
# app/services/user_service.py
from sqlalchemy import text
from sqlalchemy.ext.asyncio import AsyncSession


async def get_user(db: AsyncSession, uid: int):
    """按 id 查用户。"""
    result = await db.execute(
        text(f"SELECT * FROM users WHERE id={uid}")
    )
    return result.scalar_one_or_none()


async def list_active_users(db: AsyncSession):
    """列出所有活跃用户（含其最近订单）。"""
    users = (await db.execute(text("SELECT * FROM users WHERE active=1"))).scalars().all()
    for u in users:
        u.recent_orders = await db.execute(
            text(f"SELECT * FROM orders WHERE user_id={u.id} ORDER BY created_at DESC LIMIT 3")
        ).scalars().all()
    return users
```

## 快速模式输出（改动范围 ≈ 15 行）

```
## 审查摘要
需修改——存在 SQL 注入（致命）与 N+1 查询（中）。

## 发现的问题（最多 5 条，按严重度排序）
| # | 位置 | 问题 | 严重度 | 修复建议 |
|---|------|------|--------|---------|
| 1 | get_user 第 9 行 | SQL 注入：uid 直接拼接进 f-string | 致命 | 参数化：text("SELECT * FROM users WHERE id=:uid"), {"uid": uid} |
| 2 | list_active_users 第 17 行 | N+1：循环内逐用户查订单 | 中 | 改为一次关联查询（selectinload）或批量 IN 查询 |

## 工程级提醒（0 条）
无

## 优先修复清单
1. 参数化查询（致命）
2. 订单查询改为批量（中）
```

## 标准模式输出（改动范围 ≈ 25 行）

```
## 审查摘要
有条件通过——存在 SQL 注入（致命）与 N+1 查询（中），修复后可用。

## 发现的问题（按严重度）

### 致命（1 个）
| # | 位置 | 问题 | 证据 | 修复建议 |
|---|------|------|------|---------|
| 1 | get_user 第 9 行 | SQL 注入：uid 直接拼接进 f-string，用户可控输入直达 SQL | f-string 内嵌 {uid} 且无参数化 | 改用 text("SELECT * FROM users WHERE id=:uid") + {"uid": uid} |

### 高（0 个）
无

### 中（1 个）
| # | 位置 | 问题 | 证据 | 修复建议 |
|---|------|------|------|---------|
| 2 | list_active_users 第 17 行 | N+1 查询：每用户一次订单查询，用户数 N 则 N+1 次 DB 往返 | 循环体内执行 await db.execute | 一次查询：IN (SELECT id FROM users WHERE active=1) 或 selectinload |

### 低（0 个）
无

### 工程级提醒（0 个）
无

## 亮点
- 函数职责单一（get_user 查单用户、list_active_users 查列表）
- 返回类型标注（AsyncSession）清晰

## 优先修复清单
1. 参数化查询（致命，阻塞提交）
2. 订单查询批量化为一次往返（中）
```
