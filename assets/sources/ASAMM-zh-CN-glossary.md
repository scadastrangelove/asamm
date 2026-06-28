# ASAMM zh-CN Glossary

Status: community translation draft for ASAMM v0.5.0-draft.

| English | zh-CN | Note / source |
|---|---|---|
| Agentic SAMM | 智能体化 SAMM | ASAMM project term; keep English acronym/brand visible |
| OWASP SAMM | OWASP 软件保障成熟度模型（SAMM） | OWASP China SAMM 2.0 uses 软件保障成熟度模型 |
| Governance | 治理 | OWASP SAMM 2.0 中文版 |
| Design | 设计 | OWASP SAMM 2.0 中文版 |
| Implementation | 实施 | OWASP SAMM 2.0 中文版 |
| Verification | 验证 | OWASP SAMM 2.0 中文版 |
| Operations | 运营 | OWASP SAMM 2.0 中文版 |
| Maturity level | 成熟度级别 | OWASP SAMM terminology |
| Threat modeling / threat assessment | 威胁建模 / 威胁评估 | OWASP SAMM 2.0 uses 威胁评估; ASAMM keeps 威胁建模 where modeling process is meant |
| Access control | 访问控制 | OWASP Top 10 2021 中文版 |
| Identification and authentication | 身份识别和身份验证 | OWASP Top 10 2021 中文版 |
| Software and data integrity failures | 软件和数据完整性故障 | OWASP Top 10 2021 中文版 |
| Security logging and monitoring | 安全日志和监控 | OWASP Top 10 2021 中文版 |
| Supply chain | 供应链 | OWASP Top 10/ASAMM usage |
| Agentic system | 智能体系统 | ASAMM-specific term; avoid 代理系统 |
| Agent | 智能体 | ASAMM-specific term; avoid generic 代理 except where proxy is meant |
| Autonomy window | 自主窗口 | ASAMM-specific term |
| Temporal blast radius | 时间性影响半径 | ASAMM-specific term |
| Blast radius | 影响半径 | ASAMM-specific term |
| Agent Environment Profile | 智能体环境画像 | ASAMM v0.5 methodology supplement |
| Runtime composition inventory | 运行时组合清单 | ASAMM v0.5 methodology supplement |
| Protocol Checklist | 协议检查清单 | ASAMM v0.5 methodology supplement |
| Action provenance logging | 行动溯源日志 | ASAMM AO-01 |
| Evidence taxonomy | 证据分类法 | ASAMM evidence terms |
| Empirical evidence | 经验证据 | ASAMM evidence terms |
| Inferred evidence | 推断证据 | ASAMM evidence terms |
| Credential governance | 凭据治理 | ASAMM AI-06 |
| Non-human identity | 非人类身份（NHI） | AI identity/security term |
| Inter-agent trust protocol | 智能体间信任协议 | ASAMM AG-04 |

## Evidence Tags

Evidence tags are intentionally preserved in English for machine readability and cross-language audit comparison.

| Tag | 中文说明 |
|---|---|
| `[empirical]` | 通过测试、命令、日志或直接观察验证 |
| `[empirical absence]` | 经过测试并确认不存在 |
| `[config]` | 来自配置、系统提示词或文档 |
| `[inferred]` | 基于事实的逻辑推断，但没有直接验证 |
| `[not testable]` | 在本审计范围内无法验证 |
| `[unknown]` | 未测试、未记录、没有评估依据 |
