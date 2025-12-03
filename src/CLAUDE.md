# 开发指南

> **前置条件**：`solution/app_spec.txt` 已生成

---

## 技术栈

| 服务 | 平台 |
|-----|------|
| 代码托管 | GitHub |
| 鉴权 | Clerk |
| 支付 | Stripe |
| 数据库 | Supabase |
| 后端 | Railway (Python + FastAPI) |
| 前端 | Vercel (Next.js) |

---

## 项目结构

```text
src/
├── frontend/           # Next.js
│   ├── src/app/        # 页面
│   ├── src/components/ # 组件
│   ├── src/lib/        # 工具
│   └── .env.example
├── backend/            # FastAPI
│   ├── api/routes/     # 路由
│   ├── services/       # 业务逻辑
│   ├── models/         # 数据模型
│   └── .env.example
├── verification/       # 验证截图
│   └── screenshots/    # Puppeteer 截图存储
├── feature_list.json   # 功能列表（自动生成）
└── progress.md         # 开发进度
```

---

## 启动开发

**前端**：
```bash
cd frontend && npm install && npm run dev
# http://localhost:3000
```

**后端**（使用 uv）：
```bash
cd backend && uv sync && uv run uvicorn main:app --reload
# http://localhost:8000/docs
```

### 后端包管理规范（uv）

后端使用 [uv](https://docs.astral.sh/uv/) 进行包管理，**禁止使用 pip**。

**常用命令**：
```bash
uv sync                    # 安装依赖（根据 uv.lock）
uv add fastapi             # 添加依赖
uv add --dev pytest        # 添加开发依赖
uv run uvicorn main:app    # 运行命令
uv run pytest              # 运行测试
```

**项目初始化**：
```bash
cd backend
uv init                    # 生成 pyproject.toml
uv add fastapi uvicorn python-dotenv supabase stripe pyjwt
uv add --dev pytest httpx
```

**文件说明**：
| 文件 | 作用 | 是否提交 Git |
|-----|------|-------------|
| `pyproject.toml` | 依赖声明 | ✅ |
| `uv.lock` | 锁定版本 | ✅ |
| `.venv/` | 虚拟环境 | ❌ |

**注意**：
- 不要手动编辑 `uv.lock`
- 不要使用 `pip install`，统一用 `uv add`
- CI/CD 中使用 `uv sync --frozen` 确保一致性

---

## 自主开发模式

当 `app_spec.txt` 确认后，进入自主开发模式。

### 第一次会话：初始化

1. **阅读规格**
```bash
cat solution/app_spec.txt
```

2. **生成 feature_list.json**

从 `app_spec.txt` 生成功能列表：

```json
[
  {
    "id": "F001",
    "name": "用户登录",
    "category": "auth",
    "priority": 1,
    "status": "pending",
    "acceptance": [
      "点击登录按钮跳转 Clerk",
      "登录成功后跳转首页",
      "首页显示用户头像"
    ]
  },
  {
    "id": "F002",
    "name": "用户注册",
    "category": "auth",
    "priority": 2,
    "status": "pending",
    "acceptance": [...]
  }
]
```

**要求**：
- 按优先级排序
- 每个功能有清晰的验收标准
- 状态：`pending` → `in_progress` → `done`

3. **初始化项目结构**
- 创建前后端基础代码
- 配置环境变量模板
- 初始化 git

4. **创建 progress.md**
```markdown
## 开发进度

总功能数：20
已完成：0
进行中：无

## 会话记录

### Session 1 - 初始化
- 生成 feature_list.json（20 个功能）
- 创建项目结构
- 下一步：实现 F001 用户登录
```

---

### 后续会话：开发循环

每次新会话，按此流程：

#### Step 1: 恢复上下文
```bash
cat solution/app_spec.txt
cat feature_list.json
cat progress.md
git log --oneline -10
```

#### Step 2: 启动服务
```bash
cd frontend && npm run dev &
cd backend && uvicorn main:app --reload &
```

#### Step 3: 回归验证

用 Puppeteer 验证已完成功能：
```
puppeteer_navigate → http://localhost:3000
puppeteer_screenshot → 检查页面正常
```

**发现问题立即修复**，再继续新功能。

#### Step 4: 选择功能

从 `feature_list.json` 选优先级最高的 `pending` 功能。

#### Step 5: 实现功能

1. 写后端 API + 测试
2. 写前端页面
3. 用 Puppeteer 验证

**Puppeteer 验证流程**：
```
puppeteer_navigate → 打开页面
puppeteer_fill → 填写表单
puppeteer_click → 点击按钮
puppeteer_screenshot → 截图保存到 verification/screenshots/F001_登录成功.png
```

**截图命名规则**：`{功能ID}_{验证点}.png`，如：
- `F001_登录按钮.png`
- `F001_登录成功.png`
- `F002_注册表单.png`

#### Step 6: 自我 Code Review

功能实现后，切换到 Reviewer 视角检查：

**Review 清单**：
- [ ] 安全性：无 SQL 注入、XSS、敏感信息泄露
- [ ] 错误处理：边界情况、异常捕获
- [ ] 代码规范：命名一致、无重复代码
- [ ] API 契约：与 `3_structure.md` 定义一致
- [ ] 数据库：与 ER 图定义一致

**发现问题**：立即修复，重新验证。

**Review 通过**：继续下一步。

---

#### Step 7: 更新状态

功能验证 + Review 通过后：

**更新 feature_list.json**：
```json
{
  "id": "F001",
  "status": "done"  // pending → done
}
```

**更新 progress.md**：
```markdown
### Session 3
- 完成 F001 用户登录
- Puppeteer 验证通过
- 下一步：F002 用户注册
```

#### Step 7: 提交代码
```bash
git add .
git commit -m "feat: 完成 F001 用户登录"
```

#### Step 8: 干净退出

会话结束前确保：
- [ ] 所有代码已提交
- [ ] feature_list.json 已更新
- [ ] progress.md 已更新
- [ ] 服务可正常启动
- [ ] 无半成品代码

---

## 后端测试

每个 API 写完后添加测试：

```python
# tests/test_users.py
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_health():
    response = client.get("/health")
    assert response.status_code == 200
```

运行：`pytest`

---

## 前端要点

### Clerk 鉴权

```typescript
// 服务端
import { auth } from '@clerk/nextjs/server'
const { userId } = await auth()

// 客户端
import { useUser } from '@clerk/nextjs'
const { user } = useUser()
```

### API 调用

```typescript
const res = await fetch(`${process.env.NEXT_PUBLIC_API_URL}/api/xxx`, {
  headers: { Authorization: `Bearer ${token}` }
})
```

---

## 后端要点

### Clerk JWT 验证

```python
from fastapi import Depends, HTTPException, Security
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
import jwt
from jwt import PyJWKClient

security = HTTPBearer()
jwks_client = PyJWKClient(os.getenv("CLERK_JWKS_URL"))

async def get_current_user(cred: HTTPAuthorizationCredentials = Security(security)):
    try:
        key = jwks_client.get_signing_key_from_jwt(cred.credentials)
        return jwt.decode(cred.credentials, key.key, algorithms=["RS256"])
    except:
        raise HTTPException(401, "Invalid token")
```

### Supabase

```python
from supabase import create_client
supabase = create_client(os.getenv("SUPABASE_URL"), os.getenv("SUPABASE_KEY"))

supabase.table("users").select("*").eq("id", user_id).execute()
```

### Stripe Webhook

```python
@app.post("/api/webhook")
async def webhook(request: Request):
    event = stripe.Webhook.construct_event(
        await request.body(),
        request.headers.get("stripe-signature"),
        os.getenv("STRIPE_WEBHOOK_SECRET")
    )
```

---

## 部署

**前端 (Vercel)**：Root Directory → `src/frontend`

**后端 (Railway)**：Root Directory → `src/backend`，Start Command → `uvicorn main:app --host 0.0.0.0 --port $PORT`

---

## Puppeteer 工具参考

| 工具 | 用途 |
|-----|------|
| `puppeteer_navigate` | 打开 URL |
| `puppeteer_screenshot` | 截图 |
| `puppeteer_click` | 点击元素 |
| `puppeteer_fill` | 填写输入框 |
| `puppeteer_select` | 选择下拉框 |
| `puppeteer_hover` | 悬停元素 |
| `puppeteer_evaluate` | 执行 JS（调试用）|

**验证原则**：像真实用户一样操作，不走捷径。
