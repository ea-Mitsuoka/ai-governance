# OWASP「Top 10 for LLM Applications 2025」— 構造化要約

| 項目       | 内容                                                                                                                                                                                       |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 原典       | `2024-11_owasp_top10-for-llm-applications-2025.pdf`(全45ページ・英語・Version 2025)                                                                                                        |
| 発行元     | OWASP GenAI Security Project / Top 10 for Large Language Model Applications。2024年11月18日(2025年版)。ライセンス CC BY-SA 4.0(翻案・商用可、継承・帰属表記要)                             |
| 区分       | **中立的な業界標準**。LLMアプリケーション単体の上位10リスク(LLM01-10)。エージェント版([ASI Top 10](2025-12_owasp_top10-for-agentic-applications-2026.md))の**土台となるLLM層のリスク定義** |
| 要約作成日 | 2026-06-13                                                                                                                                                                                 |

> ★ リスク台帳の二層構造における**LLM単体層の定義書**。[ASI Top 10(ASI01-10)](2025-12_owasp_top10-for-agentic-applications-2026.md)が「エージェント上位リスク」、[T&M v1.1(T1-17)](2025-12_owasp_agentic-ai-threats-and-mitigations_v1.1.md)が「エージェント詳細タクソノミ」なら、本書は**その下にあるLLMアプリ単体の上位10リスク**。総務省AIセキュリティGLの脅威(PI直接/間接・DoS・ポイズニング・モデル抽出)と同じLLM層をカバーし、[01のリスク台帳分類体系](../docs/to-be/01-risk-classification-and-grading.md)で「LLM単体=総務省GL+OWASP LLM Top10」と位置づけた従属マッピングの一角。

______________________________________________________________________

## 0. 2025年版の改訂ポイント

2023年版からの主な変更(Letter from the Project Leadsより):

- **新規追加**: LLM07 System Prompt Leakage(実在の流出事例多発を受け)、LLM08 Vector and Embedding Weaknesses(RAG普及への対応)。
- **拡張**: LLM10 Unbounded Consumption(旧DoSを資源管理・コスト枯渇=Denial of Walletまで拡張)、LLM06 Excessive Agency(エージェント型アーキテクチャの普及で重要度上昇)。

______________________________________________________________________

## ★ 1. LLM01-10(2025)一覧 × 起点3点

| LLM       | リスク                                                      | 要点                                                                                                                        | 起点 |
| --------- | ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ---- |
| **LLM01** | Prompt Injection(プロンプトインジェクション)                | 直接/間接/マルチモーダル/jailbreak。RAGやfine-tuningでも完全には防げない。人間に不可視でもモデルが解釈すれば成立            | ①②③  |
| **LLM02** | Sensitive Information Disclosure(機密情報の漏えい)          | PII・営業秘密・独自アルゴリズム・認証情報の出力経由漏えい。モデル反転攻撃・学習データ抽出                                   | ①    |
| **LLM03** | Supply Chain(サプライチェーン)                              | 脆弱な事前学習モデル・LoRAアダプタ汚染・モデルマージ悪用・オンデバイス供給網。弱いモデル来歴(provenance)                    | ①③   |
| **LLM04** | Data and Model Poisoning(データ・モデル汚染)                | 学習/fine-tune/embedding段階の汚染。バックドア=スリーパーエージェント化・悪性pickling                                       | ①③   |
| **LLM05** | Improper Output Handling(不適切な出力処理)                  | LLM出力を下流へ渡す前の検証不足。XSS/CSRF/SSRF/権限昇格/RCE/SQLi。出力を非信頼として扱う                                    | ②③   |
| **LLM06** | Excessive Agency(過剰なエージェンシー)                      | 過剰な機能/権限/自律性。エージェント型で被害拡大。**最小権限+人間承認+complete mediation**が対策                            | ①②③  |
| **LLM07** | System Prompt Leakage(システムプロンプト漏えい)             | システムプロンプトに秘密(認証情報・内部ルール・権限構造)を埋めるのが根本問題。プロンプト自体は秘密にできない前提で設計      | ①②   |
| **LLM08** | Vector and Embedding Weaknesses(ベクトル・埋め込みの脆弱性) | RAGのベクトル/埋め込みの欠陥。**マルチテナントでのクロスコンテキスト漏出**・埋め込み反転で原文復元・RAGデータ汚染・行動変容 | ①③   |
| **LLM09** | Misinformation(誤情報)                                      | ハルシネーション+overreliance(過信)。事実誤り・根拠なき主張・専門性の偽装・**実在しないコードライブラリの提案**             | ②③   |
| **LLM10** | Unbounded Consumption(無制限消費)                           | 推論の過剰実行。DoS・**Denial of Wallet(コスト枯渇)**・モデル抽出/盗用・サービス劣化                                        | ②    |

______________________________________________________________________

## 2. 各リスクの要点メモ(自社に効く所だけ)

- **LLM01 Prompt Injection**: 対策は①モデル挙動の制約(役割・能力・限界をシステムプロンプトで明示)②出力フォーマット定義と検証③入出力フィルタ(RAG Triadで関連性・根拠性・QA関連性を評価)④最小権限⑤高リスク操作は人間承認⑥外部コンテンツの分離・明示⑦敵対的テスト。**「完全な防止は不可能」と明言**=多層防御前提。
- **LLM02 Sensitive Information Disclosure**: データサニタイズ・厳格なアクセス制御・連合学習/差分プライバシー・**利用者教育(機密入力を避ける)**・システムプロンプトに機密を置かない。
- **LLM03 Supply Chain**: モデルカード/来歴の検証・第三者AIレッドチーミング・**SBOM/AIBOM(OWASP CycloneDX)**・ライセンスBOM・モデル署名・HuggingFace等のマージ/変換サービスの監査。
- **LLM04 Data and Model Poisoning**: データ来歴追跡(CycloneDX/ML-BOM)・サンドボックス・**DVC(データ版管理)**・異常検知・推論時RAG+グラウンディング。**バックドアはトリガーまで休眠=テスト困難(sleeper agent)**。
- **LLM05 Improper Output Handling**: **モデル出力をゼロトラストで扱う**・OWASP ASVS準拠の検証/サニタイズ・コンテキスト別の出力エンコード・パラメータ化クエリ・CSP。
- **LLM06 Excessive Agency**: 拡張機能/権限/自律性の最小化・open-ended拡張の回避・**ユーザコンテキストでの実行(OAuth最小スコープ)**・人間承認・**complete mediation(下流側で認可)**。エージェントが異なるアクセスレベルを要するなら複数エージェントに分割。
- **LLM07 System Prompt Leakage**: **システムプロンプトを秘密・セキュリティ統制に使わない**・機密データは外部化・ガードレールはLLMの外に独立配置・認可は決定論的でLLMに委譲しない。
- **LLM08 Vector and Embedding Weaknesses**: **permission-aware vector store(きめ細かいアクセス制御)**・データ検証/出所認証・結合時のタグ付け分類・**不変(immutable)な検索ログ**。マルチテナントのクロスコンテキスト漏出が代表シナリオ。
- **LLM09 Misinformation**: RAG・fine-tuning(PET/chain-of-thought)・**人間によるクロス検証/監督**・自動検証・リスク周知・セキュアコーディング・UI設計でAI生成物を明示・教育。Air Canadaチャットボット訴訟・ChatGPT架空判例が実例。
- **LLM10 Unbounded Consumption**: レート制限・入力検証・リソース割当管理・Denial of Wallet対策(コスト上限・監視)・モデル抽出防止。

______________________________________________________________________

## 自社(e-Agency / 内部統制分科会)への適用示唆

1. **リスク台帳の「LLM単体層」が確定**: [01のA-2分類体系](../docs/to-be/01-risk-classification-and-grading.md)で従属マッピングに置いた「OWASP LLM Top10(2025)」が本書で具体化。総務省GL脅威(PI/DoS/ポイズニング/モデル抽出)と粒度が揃い、**LLM層=総務省GL+OWASP LLM Top10**の二本立てで参照根拠を出せる。
2. **ASI(エージェント)との接続関係が明確**: LLM01=ASI01/T6(目標乗っ取り)の土台、LLM06 Excessive Agency=ASIのLeast-Agency思想そのもの、LLM08=ASI06(RAG汚染・漏出)、LLM05 Improper Output=ASI05(RCE)。\*\*「LLM層で理解→エージェント層で増幅を評価」\*\*という二層の読み筋が、ASI/T&Mと完全に整合。
3. **起点①の裏取りが厚くなった**: LLM02(機密漏えい)+LLM07(システムプロンプト漏えい)+LLM08(ベクトル漏出)。特に**LLM08のクロスコンテキスト漏出は[B-1のクロステナント・ベクトル漏出防止](../docs/to-be/03-three-pillars-to-be.md)と同一論点**。permission-aware vector storeが中立標準でも必須要件と確認できた=確度が高い。
4. **起点②の具体化**: LLM05(出力監視)・LLM10(リソース消費監視/Denial of Wallet)。**LLM10のコスト枯渇は監視ダッシュボード(S4)のKPIに「コスト異常」を入れる根拠**。
5. **起点③の裏取り**: LLM09(誤情報・ハルシネーション・**実在しないコードライブラリ提案**)+LLM05(不適切な出力処理)+LLM02(出力経由の著作権/IP漏えい)。文化庁の生成物適法性確認と、LLM09の「unsafe code generation」が接続(ライセンススキャンF6の補強)。
6. **「LLM出力をゼロトラストで扱う」が明文(LLM05)**: [00のゼロトラスト原則](../docs/to-be/00-principles-and-scope.md)を、入力(自然言語)だけでなく**出力にも**拡張する根拠。出力検証=基本指針3の技術的裏付け。
7. **システムプロンプトに秘密を置かない(LLM07)**: 自社でGeminiエージェントを構築する際の設計規範。認証情報・内部ルール・権限構造をプロンプトに書かない=開発者向けの禁止事項候補(S3で具体化)。
8. **CC BY-SA 4.0で翻案可**: ASI/T&Mと同ライセンス。外販テンプレで「LLM単体リスク早見表」として和訳・自社化できる(継承表記要)。

## 起点3点との関連

- **① 機密データ** → LLM02(機密漏えい)/LLM07(システムプロンプト漏えい)/LLM08(ベクトル漏出)/LLM03・04(供給網・汚染経由)。
- **② 監査ログ/監視** → LLM05(出力監視)/LLM10(消費・コスト監視)。LLM01の敵対的テストも監視設計に接続。
- **③ 著作権/法的適合性** → LLM09(誤情報・unsafe code)/LLM05(不適切出力)/LLM02(IP・著作権の出力漏れ)。

## 関連

- 上位フレーム(エージェント): [OWASP Top 10 for Agentic Applications(ASI01-10)](2025-12_owasp_top10-for-agentic-applications-2026.md)/ [OWASP Agentic AI Threats & Mitigations(T1-17)](2025-12_owasp_agentic-ai-threats-and-mitigations_v1.1.md)
- 同じLLM層: [総務省 AIセキュリティ技術対策GL 本体](2026-03_soumu_ai-security-technical-measures-guideline_main.md)(PI/DoS/ポイズニング/モデル抽出=LLM層の脅威)
- 法的接続: [文化庁 AIと著作権](2024-03-15_bunkacho_ai-and-copyright-approach.md)(LLM09 unsafe code/誤情報経由で生成物適法性)
- 自社文書: [01 リスク分類・格付け基準](../docs/to-be/01-risk-classification-and-grading.md)(A-2でLLM層に位置づけ)/ [03 起点3点To-Be](../docs/to-be/03-three-pillars-to-be.md)(B-1=LLM08と同論点)
