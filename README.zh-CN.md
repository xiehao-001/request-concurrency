# 模型请求并发控制（@eduwork/dsh-request-concurrency）

> 用一个全局 FIFO 队列限制同时进行的模型请求数，主会话与子代理共享名额。
>
> Global FIFO queue capping concurrent model requests across sessions, subagents and workflows.

## 功能

挂在官方 llm/stream 扩展点上，默认总计 3 路。主会话、普通/后台/嵌套子代理、工作流内置 spawn 子代理以及压缩等辅助请求共同使用名额，没有固定保留槽位。适合多工作区同时使用、避免上游限流。

## 安装

```bash
# 在 DSH 中把本目录作为本地 bundle 安装（绝对路径）
```

安装后由 DSH 插件系统加载，条目 id 为 `request-concurrency`。

## 依赖

- `@deepseek-ai/cordis`
- `@deepseek-ai/dsh-llm`
- `@deepseek-ai/dsh-agent`
- `@deepseek-ai/schemastery`
- `@deepseek-ai/dsh-settings`
- `@deepseek-ai/dsh-session`

## 兼容性

- `engines.dsh`: `>=0.2.1 <0.3.0`
- 与 EduWork 发行版同源，来源于 ECNU/EduWork 仓库。

## 许可

MulanPSL-2.0 AND MIT（与上游 EduWork 一致）。
