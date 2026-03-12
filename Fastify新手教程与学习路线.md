# Fastify 新手教程与学习路线（前端转全栈）

## 定位与目标

你已确定从 `Fastify` 入门，并更看重工程化能力。本路线围绕“每周备菜助手”小程序场景，目标是在 1-2 周内跑通后端 MVP，并评估“从学习到运用”的真实难度。

---

## 一、官方文档学习顺序（建议按序）

- 官方文档总入口（Latest）：<https://fastify.dev/docs/latest/>
- 快速开始：<https://fastify.dev/docs/latest/Guides/Getting-Started/>
- 路由：<https://fastify.dev/docs/latest/Reference/Routes/>
- 校验与序列化：<https://fastify.dev/docs/latest/Reference/Validation-and-Serialization/>
- 插件机制：<https://fastify.dev/docs/latest/Reference/Plugins/>
- 生命周期 Hooks：<https://fastify.dev/docs/latest/Reference/Hooks/>
- 错误处理：<https://fastify.dev/docs/latest/Reference/Errors/>
- TypeScript：<https://fastify.dev/docs/latest/Reference/TypeScript/>
- 测试指南：<https://fastify.dev/docs/latest/Guides/Testing/>

---

## 二、7 天新手上手教程

### Day 1：跑通最小服务

1. 初始化 TS 项目并安装 `fastify`
2. 编写 `GET /health` 接口
3. 启动服务，理解 `listen` 与 `logger`

目标：理解后端服务最小运行方式。

### Day 2：掌握声明式 API

1. 编写 `POST /recipes`、`GET /recipes`
2. 为请求体和响应定义 `schema`
3. 体验 Fastify 自动校验（无效参数直接返回 400）

目标：理解 Fastify 的核心优势（校验 + 序列化）。

### Day 3：插件化拆分模块

1. 拆分模块：`recipes`、`plans`、`shopping-list`
2. 使用 `register` + 路由前缀
3. 理解封装（encapsulation）

目标：建立可维护工程结构，避免单文件臃肿。

### Day 4：接入数据库（PostgreSQL）

1. ORM 二选一：`Drizzle`（轻量）或 `Prisma`（成熟）
2. 跑通 `recipes` 表 CRUD
3. 了解 migration 基本流程

目标：从内存数据切换到持久化数据。

### Day 5：鉴权与安全

1. 接入 `@fastify/jwt`（登录签发 token）
2. 使用 `preHandler` 做受保护路由
3. 加入 `@fastify/cors`、`@fastify/helmet`

目标：具备基础生产安全能力。

### Day 6：错误处理、日志与配置

1. `setErrorHandler` 统一错误格式
2. 使用 `@fastify/env` 管理环境变量
3. 规范日志字段（requestId、userId）

目标：提升可维护性与排障效率。

### Day 7：测试与文档

1. 使用 `fastify.inject()` 编写接口测试
2. 使用 `@fastify/swagger` 输出 API 文档
3. 最小 CI：`lint + test`

目标：形成工程闭环。

---

## 三、每周备菜助手 MVP 业务边界

建议先做 4 个模块：

- `auth`：登录、鉴权
- `recipes`：菜谱 CRUD
- `plans`：每周计划（按天/餐次）
- `shopping-list`：从周计划聚合采购清单

最小业务闭环：

`创建菜谱 -> 生成周计划 -> 自动汇总采购清单`

---

## 四、推荐技术栈（Fastify 入门友好）

- 服务框架：`fastify`
- 语言：`TypeScript`
- 数据库：`PostgreSQL`
- ORM：
  - 轻量优先：`Drizzle`
  - 成熟优先：`Prisma`
- 常用插件：
  - `@fastify/jwt`
  - `@fastify/cors`
  - `@fastify/helmet`
  - `@fastify/swagger` + `@fastify/swagger-ui`
  - `@fastify/env`

---

## 五、项目目录建议

```text
meal-prep-assistant-api/
  src/
    app.ts
    server.ts
    plugins/
      env.ts
      jwt.ts
      swagger.ts
      db.ts
    common/
      errors.ts
      response.ts
      auth.ts
    modules/
      auth/
        auth.route.ts
        auth.service.ts
        auth.schema.ts
      recipes/
        recipes.route.ts
        recipes.service.ts
        recipes.schema.ts
      plans/
        plans.route.ts
        plans.service.ts
        plans.schema.ts
      shopping-list/
        shopping-list.route.ts
        shopping-list.service.ts
        shopping-list.schema.ts
    db/
      schema/
        user.ts
        recipe.ts
        weeklyPlan.ts
      index.ts
  tests/
    auth.test.ts
    recipes.test.ts
  drizzle/
  package.json
  tsconfig.json
  .env.example
  docker-compose.yml
```

---

## 六、第一批依赖清单

```bash
# runtime
npm i fastify @fastify/cors @fastify/helmet @fastify/jwt @fastify/swagger @fastify/swagger-ui @fastify/env zod

# db
npm i drizzle-orm pg
npm i -D drizzle-kit

# dev
npm i -D typescript tsx @types/node vitest eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

---

## 七、.env 示例

```env
NODE_ENV=development
PORT=3000
HOST=0.0.0.0

DATABASE_URL=postgres://postgres:postgres@localhost:5432/mealprep
JWT_SECRET=replace_me
JWT_EXPIRES_IN=7d
```

---

## 八、MVP 接口清单（第一批）

- `POST /auth/login`：手机号 + 验证码模拟登录，返回 token
- `GET /auth/me`：获取当前用户
- `POST /recipes`：新增菜谱（含食材数组）
- `GET /recipes`：查询菜谱列表
- `POST /plans/weekly`：创建周计划
- `GET /plans/weekly?week=2026-W11`：查询周计划
- `GET /shopping-list?week=2026-W11`：按周聚合采购清单（去重 + 分组）

---

## 九、最小数据模型建议

- `users`：`id`, `phone`, `nickname`, `created_at`
- `recipes`：`id`, `user_id`, `name`, `steps`, `created_at`
- `recipe_ingredients`：`id`, `recipe_id`, `name`, `amount`, `unit`, `category`
- `weekly_plans`：`id`, `user_id`, `week_code`
- `weekly_plan_items`：`id`, `weekly_plan_id`, `day_of_week`, `meal_type`, `recipe_id`

---

## 十、团队工程规范（建议统一）

- 模块固定三层：`route -> service -> schema`
- 所有接口都定义 `schema`（body/query/response）
- 受保护接口统一挂 `preHandler: [authenticate]`
- 错误统一走 `setErrorHandler`
- 路由层不写 SQL，数据库操作放在 service 层
- 响应结构统一：`{ code, message, data }`

---

## 十一、7 天任务看板（可执行）

- Day 1：初始化项目，跑通 `/health`，接入 Swagger
- Day 2：接 PostgreSQL + ORM，建 `users/recipes`
- Day 3：完成 `auth`（登录 + 鉴权）
- Day 4：完成 `recipes` CRUD
- Day 5：完成 `plans` 周计划
- Day 6：完成 `shopping-list` 聚合逻辑
- Day 7：补测试、完善错误处理、补 README

---

## 十二、学习到运用难度评估（前端团队视角）

- 第 1 周：可独立开发 CRUD + 参数校验（低到中）
- 第 2 周：可完成鉴权、模块化、迁移（中）
- 第 3-4 周：可建立测试、文档、部署流程（中到中高）

常见难点通常不在 Fastify 本身，而在：

- 数据建模（关系与边界）
- 鉴权与权限设计
- 错误处理与测试习惯

---

## 十三、验收标准（用于评估是否可团队推广）

- 新人 2 天内可独立新增一个模块接口
- `npm run dev` 可稳定一次启动
- Swagger 可直接调试核心接口
- 至少 6 条核心接口测试通过
- 可演示闭环：`菜谱 -> 周计划 -> 采购清单`

