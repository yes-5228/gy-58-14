# 羽毛球场地预约系统

基于 FastAPI + React 的羽毛球场地预约系统，包含场地时段管理、在线预约、费用结算和会员折扣。项目采用前后端分离和模块化目录结构，便于继续扩展数据库、登录权限和管理端能力。

## 功能

- 场地管理：查看场地、场地材质和每日时段。
- 时段管理：按日期查看时段，可停用或恢复时段。
- 在线预约：选择可预约时段、填写预约人、选择会员折扣后提交订单。
- 费用结算：根据会员等级自动计算折扣价，订单支持结算和取消。
- 会员折扣：内置普通、银卡、金卡、教练等折扣示例。

## 项目结构

```text
.
├── backend
│   ├── app
│   │   ├── data          # 内存数据与初始化种子
│   │   ├── models        # 领域模型
│   │   ├── routes        # API 路由
│   │   ├── services      # 业务逻辑
│   │   ├── main.py       # FastAPI 入口
│   │   └── schemas.py    # 请求模型
│   ├── Dockerfile
│   └── requirements.txt
├── frontend
│   ├── src
│   │   ├── api           # API 客户端
│   │   ├── components    # 可复用组件
│   │   ├── pages         # 页面组合
│   │   ├── utils         # 工具函数
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── Dockerfile
│   └── package.json
└── docker-compose.yml
```

## Docker 启动

### 快速启动

```bash
docker compose up --build
```

### 环境变量配置

项目支持通过环境变量自定义服务地址和端口，推荐使用 `.env` 文件进行配置。

在项目根目录创建 `.env` 文件：

```bash
# 后端服务端口（宿主机端口:容器端口）
BACKEND_PORT=8000

# 前端服务端口（宿主机端口:容器端口）
FRONTEND_PORT=5173

# 前端调用后端 API 的基础地址（构建时传入）
# 注意：此地址是用户浏览器访问后端的地址，需与后端实际暴露地址一致
VITE_API_BASE_URL=http://localhost:8000/api
```

使用 `.env` 文件启动：

```bash
docker compose --env-file .env up --build
```

### 启动后访问

容器启动成功后，可通过以下地址访问：

| 服务 | 地址 | 说明 |
|------|------|------|
| 前端应用 | http://localhost:5173 | 用户端预约界面 |
| 后端 API | http://localhost:8000/api | API 接口根路径 |
| 健康检查 | http://localhost:8000/api/health | 验证后端服务是否正常 |
| API 文档 | http://localhost:8000/docs | Swagger 交互式文档 |
| API 文档（备用） | http://localhost:8000/redoc | ReDoc 文档 |

### 验证部署

启动后可依次验证：

1. 验证后端健康状态：
```bash
curl http://localhost:8000/api/health
# 预期返回：{"status":"ok"}
```

2. 验证 API 文档：
   浏览器访问 http://localhost:8000/docs 查看 Swagger 界面

3. 验证前端页面：
   浏览器访问 http://localhost:5173 查看预约系统界面

### 自定义端口

如需修改默认端口，修改 `.env` 文件中的端口配置，或直接修改 `docker-compose.yml` 中的 `ports` 映射。

注意：修改后端端口后，需要同步修改 `VITE_API_BASE_URL` 中的端口号，否则前端无法正常调用后端 API。

## 本地开发

后端：

```bash
cd backend
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

前端：

```bash
cd frontend
npm install
npm run dev -- --host 127.0.0.1 --port 5173
```

如需指定 API 地址：

```bash
VITE_API_BASE_URL=http://localhost:8000/api npm run dev
```

## 主要接口

- `GET /api/courts`：场地列表
- `GET /api/time-slots?date=YYYY-MM-DD`：时段列表
- `PATCH /api/time-slots/{slot_id}`：更新时段价格或状态
- `GET /api/members`：会员列表
- `POST /api/bookings`：提交预约
- `POST /api/bookings/{booking_id}/settle`：结算订单
- `POST /api/bookings/{booking_id}/cancel`：取消订单

## 说明

当前版本使用内存数据，适合演示和评测；重启后预约数据会恢复到初始状态。生产化可将 `backend/app/data/store.py` 替换为数据库仓储层，并继续复用现有路由和服务层。
