# AGENTS.md

## Scope

Switchboard is the LLM access layer of the TurLamAI product family: one
chat/tool/embedding surface in front of hosted model APIs. It is not an agent
framework, a prompt library, a vector store, or a retry/backoff engine. Those
live above it.

## Dependency direction

```text
product -> switchboard
sonar adapter -> switchboard

switchboard -X-> product
switchboard -X-> sonar
```

Switchboard must never depend on a consumer.

## Invariants

- A model string always names its provider explicitly. No hidden default
  provider exists; a bare model name fails loudly.
- Every provider failure crosses the boundary as an `exn:fail:switchboard`
  subtype. Callers never parse provider error bodies to recover.
- Tool execution never throws. Failures become `"Error: ..."` output strings —
  the model's feedback channel. One bad tool call must not abort an agent loop.
- No hidden retries, no hidden backoff. Policy above the library owns timing.
- All HTTP flows through `current-post-json`. Tests stub this parameter; no
  test in this repository touches the network.
- Resolved API keys never appear in exception messages, logs, or reprs of any
  value defined here.
- Public API changes land in the same change as their scribble documentation
  and both READMEs.

## 设计流程（原型先行）

所有 UI 开发与界面改造一律先原型后代码：brief → `design/DESIGN.md`
视觉契约 → HTML 原型 → 按 HIG 基准评审通过才开工；改界面先改原型再改
代码。契约与原型放本仓 `design/`（尚未建立时随首个 UI 切片建立），
实物截图验收后才能作为对外宣传素材。
