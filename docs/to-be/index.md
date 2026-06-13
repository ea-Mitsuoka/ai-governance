# S2 理想形(To-Be)— 索引と決定状態マップ

ロードマップ [S2](../../roadmap.md) の成果物。**「あるべき姿(To-Be)」と判断の枠組み・基準**を、出典で裏付けて定める。
**社内の制約(リソース・既存規程・スケジュール)はまだ考えない**——それらと突き合わせた実行可能な落とし所はS3で確定する。

> 本ディレクトリの指針・禁止事項・基準は **To-Be(理想版)**。S3で As-Is と突き合わせ Must/Should/Later に段階適用する前提。

______________________________________________________________________

## 文書構成

| # | 文書 | 内容 |
|---|---|---|
| 00 | [基本原則・適用対象・設計判断](00-principles-and-scope.md) | AI利用の基本原則/誰・何に適用するか/体制・ポリシー型・自律度方針 |
| 01 | [リスク分類・格付け基準](01-risk-classification-and-grading.md) | ユースケース3区分の判断軸/リスク台帳分類体系/データ機密区分×利用可否 |
| 02 | [AI利用指針・禁止事項](02-acceptable-use-and-prohibitions.md) | ★親PJ中核。基本指針/禁止事項/承認制事項/自由利用範囲 |
| 03 | [起点3点の理想統制像](03-three-pillars-to-be.md) | ①機密データフィルタリング ②監査ログ監視 ③生成物の法的適合性 |
| 04 | [運用フロー・体制のTo-Be](04-operational-flows.md) | 申請審査/HITL/インシデント/シャドーAI/ベンダー評価/教育 |

______________________________________________________________________

## 決定状態マップ

| 決定事項 | 状態 | 主な根拠(S1) |
|---|---|---|
| ガバナンス体制類型(Functional→Centralized移行) | 暫定確定 | [Palo Alto WP](../../sources/2026-04_paloalto-idira_securing-agentic-ai-identity-foundation.md) |
| ポリシー構成型(既存ポリシー基盤型) | 暫定確定 | [別添2A本体](../../sources/2026-03-31_meti-soumu_ai-business-guideline_v1.2_attachment-main.md) |
| エージェント自律度方針(Least-Agency/単一境界) | 暫定確定 | [OWASP ASI](../../sources/2025-12_owasp_top10-for-agentic-applications-2026.md) |
| 社内版/外販版の二形態設計 | 方針確定 | ロードマップ基本思想 |
| ユースケース・リスク格付け基準 | ドラフト | EU AI Act階層/総務省GLリスクベース |
| リスク台帳分類体系(ASI×T二層) | 確定 | [ASI](../../sources/2025-12_owasp_top10-for-agentic-applications-2026.md)/[T&M](../../sources/2025-12_owasp_agentic-ai-threats-and-mitigations_v1.1.md) |
| データ機密区分×利用可否マトリクス | ドラフト | 営業秘密(不競法)/個情法/総務省GL |
| 機密データフィルタリング To-Be | ドラフト | [総務省GL別添](../../sources/2026-03_soumu_ai-security-technical-measures-guideline_attachment.md)/ASI06 |
| 監査ログ監視フロー To-Be | ドラフト | T8/observability非交渉/PB1 |
| 生成物の法的適合性 To-Be | ドラフト | [文化庁](../../sources/2024-03-15_bunkacho_ai-and-copyright-approach.md) |
| 運用フロー(申請審査/HITL/インシデント等) | ドラフト | 別添2A/各プレイブック |

> 各決定の**全基準にわたる根拠の束ね**は[framework-comparison.md](../framework-comparison.md)(§4 起点3点×基準、§5 P原則×基準)を参照。S1で読んだ6資料は本文へ統合済(下記「反映状況」)。

______________________________________________________________________

## S1新規読込の反映状況(2026-06-14 反映済み)

To-Be成果物(00〜04)作成後に読み込んだS1資料6件を、本文へ統合済み。横断的な対応関係は[framework-comparison.md](../framework-comparison.md)に集約した。

| 反映元の資料 | 反映先(主な統合箇所) |
|---|---|
| [OWASP LLM Top 10 2025](../../sources/2024-11_owasp_top10-for-llm-applications-2025.md) | 00(出力ゼロトラスト=LLM05)/01 A-2(LLM単体層を明記)/02(F13システムプロンプト=LLM07、出力ゼロトラスト)/03 B-1(LLM08ベクトル漏出) |
| [NIST 生成AIプロファイル(AI 600-1)](../../sources/2024-07_nist_ai-rmf-generative-ai-profile_ai-600-1.md) | 00(P原則出典・8原則)/01 A-2(GAI12リスク従属層)/03 B-3(コンテンツ来歴)・B-2(配備後監視)/04 C-3(外部報告・ニアミスDB)・C-5(SSAE) |
| [NIST AI RMF Playbook](../../sources/2023-01_nist_ai-rmf-1.0-playbook.md) | 01 A-1(risk≈impact×likelihood裏付け)/00 D-2(GOVERN1.2網羅チェック)/04 §7(γ自己評価設問) |
| [サンノゼ市 AI RMF 自己評価](../../sources/2024_san-jose_ai-rmf-self-assessment.md) | 04 C-1(公開アルゴリズム登録簿)・§7(成熟度1〜4採点・As-Is方法論)/外販(組織成熟度テンプレ) |
| [ISO 42001(AIMS)](../../sources/2025-05_kpmg_iso-iec-42001-aims-certification-overview.md)+[ISO 23894](../../sources/2023-02_iso-iec-23894-ai-risk-management-guidance_preview.md) | 00(8原則の国際整合・D-2のISO27001統合)/01 A-2(台帳→SoA昇格設計)/04 C-5(契約論点=継続学習・データ所有権) |
| [Google SAIF](../../sources/2025_google-saif-secure-ai-framework_web.md) | 00 D-3・P4/P5(エージェント3統制)/01 A-2(SAIF15リスク)/03 B-1・B-2(データ統制・可観測性・S4写像)/04(承認材料にSAIF統制) |

> 残課題(S3以降): ①ISO規格本文の有償部分(SoA詳細・23894附属書A/B)取得判断 ②体制確定後にγ自己評価・As-Is棚卸しを実施 ③LLM07/F13など開発者向け禁止のS3具体化。

______________________________________________________________________

## 優先順位(親PJ F2=2026/7納期)

ロードマップの「起点3点を先行縦断」方針に従う。

1. **基本原則・設計判断の正式記録**(S1で答えが出ている→確認のみ)
1. **リスク格付け基準**(全ルールの背骨)
1. **利用指針・禁止事項**(親PJ要求の中核納品物)
1. **起点3点の理想統制像**
1. 申請審査・HITL・インシデント対応フローを優先、シャドーAI対策等は並行

______________________________________________________________________

> 文書は依存関係の上流から作った——基本原則・設計判断 → 格付け基準 → 利用指針 → 起点3点統制 → 運用フロー。上位が固まるほど下位がブレないため。
