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

______________________________________________________________________

## S1新規読込の反映待ち

To-Be成果物(00〜04)作成後に読み込んだS1資料で、**まだ本文に反映していない**もの。次回改訂で取り込む。

| 未反映の資料 | 影響する文書 | 取り込むべき論点 |
|---|---|---|
| [OWASP LLM Top 10 2025(LLM01-10)](../../sources/2024-11_owasp_top10-for-llm-applications-2025.md)(2026-06-13読込) | 01(A-2台帳)/03(B-1)/02(禁止事項) | ・01のリスク台帳に「LLM単体層=総務省GL+OWASP LLM Top10」を明記(現状はT&M要約への参照のみで、LLM Top10の要約はまだ紐づけていない)<br>・03 B-1にLLM08(ベクトル・埋め込み脆弱性=クロステナント漏出と同論点)を出典として追加<br>・LLM07(システムプロンプトに秘密を置かない)をGeminiエージェント構築の禁止事項候補としてS3で検討<br>・LLM05(出力もゼロトラスト)を00のゼロトラスト原則に追記検討 |
| [NIST AI RMF 生成AIプロファイル(AI 600-1)](../../sources/2024-07_nist_ai-rmf-generative-ai-profile_ai-600-1.md)(2026-06-13読込) | 01(A-2台帳)/03(B-3)/04(C-3,C-5)/00(改訂ループ) | ・01のA-2台帳の従属マッピングにNIST GAI 12リスク(環境影響・有害バイアス・情報完全性・知財・CBRN等の組織/社会リスク軸)を追加<br>・03 B-3に「コンテンツ来歴(電子透かし・合成コンテンツ検出)」統制を追加<br>・04 C-3に「インシデントの外部報告(規制当局)」観点、C-5に「SSAE報告」確認項目を追加<br>・GOVERN/MAP/MEASURE/MANAGEを00の改訂ループ・P8の国際標準裏付けとして併記<br>・別添7Cの「具体的アプローチ」列にNIST GV/MP/MS/MG番号を併記する案(外販クロス参照) |
| [NIST AI RMF Playbook(AI RMF 1.0)](../../sources/2023-01_nist_ai-rmf-1.0-playbook.md)(2026-06-13読込) | 01(A-1)/04(γ運用)/02(網羅チェック) | ・01 A-1に「risk≈impact×likelihood/RAGスケール」の国際裏付けを脚注追加<br>・各サブカテゴリの「文書化すべき自己質問」を内部監査(γ)の自己評価チェックリスト/別添7C設問に転用(S3)<br>・GOVERN 1.2「方針に含むべき項目リスト」で02・D-2の抜け漏れ検証 |
| [サンノゼ市 AI RMF 自己評価](../../sources/2024_san-jose_ai-rmf-self-assessment.md)(2026-06-13読込) | S0 As-Is/04(C-1,γ)/外販テンプレ | ・As-Is棚卸し(S0)を成熟度1〜4採点方式で実施(xlsxテンプレ流用、体制確定後)<br>・04 C-1台帳に「公開アルゴリズム登録簿(対外公開版台帳)」の発想を追加検討<br>・改訂ループKPIに「成熟度スコアの推移」<br>・外販に「組織成熟度自己評価」テンプレを別添7C(個別ユースケース評価)と二本立てで用意 |
| [ISO/IEC 42001(AIMS, KPMG解説)](../../sources/2025-05_kpmg_iso-iec-42001-aims-certification-overview.md)+[ISO/IEC 23894(リスク管理)](../../sources/2023-02_iso-iec-23894-ai-risk-management-guidance_preview.md)(2026-06-13読込) | 00(原則・D-2)/01(台帳→SoA)/04(C-5)/全体 | ・00の8原則を「ISO 23894の8原則と整合」と明記(Inclusive/Dynamic/Human&cultural/Continual improvement)<br>・01のリスク台帳を将来「SoA(適用宣言書)=全リスク→統制目標」に昇格できる粒度で設計<br>・D-2(既存基盤型)に「ISO 27001 ISMSにAIMSを統合」の国際定石を裏付けとして追記<br>・C-5/外販契約に「継続学習が契約義務に影響/学習データ所有権」(23894 5.4.1)を追加<br>・将来のISO 42001認証取得を外販訴求・自社成熟度証明の選択肢としてS3以降で検討(規格本文は有償) |
| [Google SAIF](../../sources/2025_google-saif-secure-ai-framework_web.md)(2026-06-13巡回) | 01(A-2台帳)/03(B-1,B-2)/00(D-3)/04(C-2)/S0,S4 | ・01のA-2台帳にSAIF 15リスク(Google実装視点の従属層、特にRA/SDD)を追加<br>・03 B-1出力検証の「PIJ/RA/SDD/ISDを一手に緩和」二重機能をSAIFで補強、B-2にAgent Observabilityを明記<br>・D-3/C-2をSAIFエージェント3統制(Permissions/User Control/Observability)で裏付け補強<br>・S0 As-IsをSAIF Risk Self-Assessment(Consumer向け12設問)でも実施(NIST成熟度と二段)<br>・S4でSAIF統制25種→Google Cloud機能(DLP/VPC-SC/IAM/Audit Logs/Model Armor)へ写像 |

> 注:01のA-2分類体系では「OWASP LLM Top 10(2025)」を従属マッピングの枠として既に**置いて**いるが、[要約](../../sources/2024-11_owasp_top10-for-llm-applications-2025.md)の具体内容(各LLM項目)とのリンクづけは未。

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
