# Grokbot Agent Skills（BOSS Lee 选用包）

来源：[ofershap/grok-bot-skills](https://github.com/ofershap/grok-bot-skills)（MIT）。由 Jarvis 按岗位筛选安装到 Grok Bot 私有 skill 库，并备份于此。

## 全岗核验五件套
| Skill | 用途 |
| --- | --- |
| claim-check | 事实主张核验 |
| source-audit | 引用来源侧向核验 |
| research-review | 研究稿/数字建议独立复核 |
| assumption-audit | 拆解问题里的未证前提 |
| competitor-compare | 产品/供应商可比对比 |

## 按岗增补
| 岗位 | Skill | 用途 |
| --- | --- | --- |
| Jarvis 幕僚长 | distill-need | 澄清真实需求再协调 |
| CNO 小葵 | morning-brief | 日简/承诺与缺口拆分（出站仍只走 wechat_send） |
| CRO 广志 | consensus-map | 争议议题共识/分歧图 |
| COO 小新 | distill-need | 运营需求澄清（与 Jarvis 共用） |
| CCO 风间 | rubric-rewrite | 按明确标准改稿 |
| CFO 妮妮 | monthly-checkpoint | 月度 keep/change/stop（资金动作仍须审批） |
| CTO 阿呆 | root-cause-debug, code-walkthrough | 排障与代码走读 |
| CLO 园长 | quote-grounded-answer | 长文/制度原文引据作答 |
| 正南 | — | 待任命，本轮不加 |

## 安装说明（Grok Bot）
在对话中让助手读取 `skills/<name>/SKILL.md` 并用 UpdateSkill 写入私有库；技能库全局共享，各 bot 按 description 触发使用。

## 边界
不改变 V1.3 审批边界：资金/法律争议/账号安全/不可逆操作/微信正式出站仍须 BOSS 原话或制度规定流程。
