# aasegs-gov-plane — AASEGS 治理控制面

**用途**：治理执行器（executor 六组件）的独立信任域仓库，对应控制 **VD-01**（信任域分离）、规则 **GOV-14**。

## 信任域声明

- 本仓库承载强制性执行逻辑（fencing 写锁、幂等台账、记忆状态机、检查点绑定、熔断、交接契约）及其负向测试。
- 被治理仓库 `slow-is-fast/engineering_manager` 中的普通 Agent 凭证**不应**拥有本仓库写权限；本仓库变更只能经 Owner 通道 + 分支保护进入。
- 数据面仓库通过 `executor_pin.json` 将执行器版本**绑定到本仓库的不可变 commit SHA**；数据面治理 CI（T-VD-01）校验 pin 与本仓库实际内容一致，篡改 pin 即失败。
- 边界诚实声明：当前控制面与数据面同属 GitHub 账号 `slow-is-fast`，Owner 人类凭证横跨两面——这与真实组织的"人横跨两域、Agent 凭证分域"模型一致。**Agent 凭证分域**由 fine-grained PAT 作用域保证（只授数据面）。账号级隔离（独立 org/账号）是 verified 授予的进一步要求。

## 内容

- `executor/`：audit、lock(fencing)、ledger(幂等)、memory(状态机)、checkpoint(摘要绑定)、breaker(熔断)、handoff(七要素契约)
- `tests/test_executor_negative.py`：10 条负向用例
- `.github/workflows/gov-plane.yml`：控制面自检（负向用例必须全绿）

## 变更规则

任何对本仓库执行器源码的修改都必须走 PR + Owner 审批 + CI 绿。数据面 pin 更新（bump）是显式动作，不得随本仓库任意提交自动漂移。
