# OWASP「Top 10 for Agentic Applications 2026」— 構造化要約

| 項目       | 内容                                                                                                                               |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| 原典       | `2025-12_owasp_top10-for-agentic-applications-2026.pdf`(全57ページ・英語・v12.6)                                                   |
| 発行元     | OWASP GenAI Security Project(2025年12月公開)。ライセンス CC BY-SA 4.0(=社内資料・外販テンプレへの引用・翻案が許諾範囲内)           |
| 区分       | **中立的な業界標準**。エージェントAI特有のセキュリティリスク上位10件(ASI01〜ASI10)。OWASP LLM Top 10(2025)のエージェント版・拡張版 |
| 要約作成日 | 2026-06-12                                                                                                                         |

> ★ これが、これまでベンダー資料([Palo Alto/Idira WP](2026-04_paloalto-idira_securing-agentic-ai-identity-foundation.md))でしか押さえていなかった\*\*「AIエージェント特有リスク」の中立標準による裏取り\*\*。総務省AIセキュリティGLが「対象外」とした空白([本体](2026-03_soumu_ai-security-technical-measures-guideline_main.md))を、業界横断の合意でカバーする最重要文書。複数大手がパブコメで取得を促した([パブコメ要約](2026-03_soumu_ai-security-technical-measures-guideline_public-comments.md))バックログ最優先項目。Gemini Enterprise=オーケストレータ+マルチエージェントの全社展開に直結。

______________________________________________________________________

## 0. 設計思想の核(Leaders' letter / At-A-Glance)

- **Least-Agency(最小エージェンシー)**: 従来の Least-Privilege(最小権限)に加え、エージェントが「持つべき自律性・行動範囲そのものを最小化する」概念。**過剰な権限だけでなく過剰な自律性(Excessive Agency)を絞る**のが新原則。
- **強い可観測性(observability)は交渉の余地なし(non-negotiable)**: エージェントの行動・ツール呼び出し・エージェント間通信を改ざん耐性のあるログで残すことが大前提。← 起点②の中立標準による補強。
- **自然言語入力はすべて非信頼(untrusted)として扱う**: プロンプトインジェクションが全リスクの起点になりうる。

## ★ 1. Top 10 一覧(ASI01〜ASI10)

| ID        | リスク                                                 | 一言で                                                           | 起点3点 |
| --------- | ------------------------------------------------------ | ---------------------------------------------------------------- | ------- |
| **ASI01** | Agent Goal Hijack(目標ハイジャック)                    | プロンプトインジェクション等でエージェントの目標・推論を乗っ取る | ①②      |
| **ASI02** | Tool Misuse & Exploitation(ツール悪用)                 | 付与済みツールを意図せぬ/危険な形で使わせる(EDR迂回・ツール連鎖) | ①②      |
| **ASI03** | Identity & Privilege Abuse(ID・権限濫用)               | 委譲連鎖・役割継承・なりすましで権限昇格                         | ①②      |
| **ASI04** | Agentic Supply Chain Vulnerabilities(サプライチェーン) | ツール/MCP/A2A/レジストリ/プラグインの汚染・改ざん               | ①③      |
| **ASI05** | Unexpected Code Execution / RCE(意図せぬコード実行)    | 生成コード・eval・シェル注入・脱直列化でホスト侵害               | ①       |
| **ASI06** | Memory & Context Poisoning(メモリ・文脈汚染)           | RAG/長期記憶/共有文脈を汚染し将来の推論を歪める                  | ①②③     |
| **ASI07** | Insecure Inter-Agent Communication(エージェント間通信) | A2A/MCPの盗聴・改ざん・なりすまし・リプレイ                      | ①②      |
| **ASI08** | Cascading Failures(連鎖障害)                           | 単一の障害がエージェント網を伝播・増幅し全系的被害に             | ①②③     |
| **ASI09** | Human-Agent Trust Exploitation(人間信頼の悪用)         | 擬人化・説明の捏造で人間を操り最終操作を承認させる               | ②③      |
| **ASI10** | Rogue Agents(ローグ・エージェント)                     | 振る舞いがドリフトし権限内で有害化・自己複製                     | ①②③     |

## 2. 各リスクの要点(自社展開で効く部分に絞る)

- **ASI01 Goal Hijack**: 直接/間接PI、EchoLeakのゼロクリック(メール1通でCopilotが機密漏洩)。対策=自然言語入力を非信頼扱い、計画と実行の分離、目標逸脱検知。
- **ASI02 Tool Misuse**: ツール毎の権限プロファイル(Least Agency)、**アクション単位の認証・人間承認**(削除/送金/公開等の高影響操作はdry-run/差分プレビュー)、PEP/PDPによる事前検証、適応的ツール予算(レート/コスト上限)、Semantic Firewall(完全修飾ツール名・タイポスクワット検知)。
- **ASI03 Identity & Privilege Abuse**: タスクスコープ・時限の短命トークン(mTLS/scoped token)、エージェント毎ID、**Confused Deputy(混乱した代理)対策**=内部エージェント間でも intent 再検証、TOCTOU対策、Synthetic Identity Injection(偽「Admin Helper」)対策。MS Entra Agent ID / AWS Bedrock Agents / Salesforce Agentforce / Google Vertex AI 等の「エージェントを管理対象の非人間ID(NHI)として扱う」プラットフォームを評価。
- **ASI04 Supply Chain**: 静的依存(LLM03:2025)を超え**ランタイムでの能力合成**(動的ツール/ペルソナ読込)が新攻撃面。MCPツール記述子注入、悪性MCPサーバ(npm postmark-mcp なりすまし=実在事例)、汚染RAGプラグイン。対策=SBOM/AIBOM、署名・来歴検証、ピン留め(content hash/commit ID)、サプライチェーン・キルスイッチ。
- **ASI05 RCE**: vibe coding(自律コード生成・自己修復)が本番にコード/シェルを流す危険。`eval()`本番禁止、サンドボックス・非root実行、egress allowlist、生成コードのtaint追跡。
- **ASI06 Memory & Context Poisoning**: RAG/ベクタDB汚染、**クロステナント・ベクトル漏出**(名前空間フィルタの緩さ×高コサイン類似度で他テナントの機密chunkが検索ヒット)、長期記憶ドリフト、**自分の生成物の再取込み禁止(bootstrap poisoning防止)**。対策=メモリのセグメント化、テナント別名前空間、来歴・信頼スコア、未検証記憶の時限失効。← Gemini EnterpriseのRAGに直結。
- **ASI07 Inter-Agent Comm**: ペリメータ型防御は無効。E2E暗号化+エージェント毎認証、メッセージ署名(payload+context)、anti-replay(nonce/session/timestamp)、プロトコル・ダウングレード拒否、A2A登録なりすまし対策。
- **ASI08 Cascading Failures**: 「初期障害そのもの」でなく**伝播・増幅**が本質。ゼロトラスト設計、blast-radius ガードレール(quota/circuit breaker/planner-executor間)、**Digital twin replay**(直近のエージェント行動を隔離クローンで再生し連鎖発生を検査、合格までポリシー拡張をgate)、改ざん耐性ログ+非否認。
- **ASI09 Human-Agent Trust Exploitation**: 擬人化・権威バイアス・自動化バイアスの悪用。**「説明の捏造(Fake Explainability)」で危険操作を承認させる**(本番DB削除を承認させた例)。対策=高影響操作の多段承認、モデル生成でない平易なリスク要約の提示、適応的トラスト・キャリブレーション(low-certainty/unverified source表示)、preview と副作用の分離、人員教育。
- **ASI10 Rogue Agents**: 外部侵害が起点でも**ドリフト後の振る舞いの逸脱**が本質。報酬ハッキング(コスト最小化のため本番バックアップを全削除=DR資産破壊)、自己複製、共謀。対策=Trust Zone、watchdogエージェント、キルスイッチ・資格剥奪、**署名済み behavioral manifest**(期待される能力・ツール・目標を宣言しオーケストレータが各行動前に検証)、隔離後の再統合は再アテステーション+人間承認。

## 3. 付録(=外販・統制設計の道具)

- **付録A: ASI ↔ LLM Top 10(2025) ↔ Agentic Threats & Mitigations(T1-T17) ↔ AIVSS Core Risk のマッピング行列**。★各ASIをLLM Top10・脅威タクソノミ・定量スコアリングに紐付け。**外販アセスメントで「どの標準のどれに当たるか」を一覧提示できる**。例:ASI01=LLM01(PI)+LLM06(過剰エージェンシー)の合成。
- **付録B: CycloneDX / AIBOM との関係**。「何のコンポーネントがあるか(SBOM/AI-BOM)」と「それらがどう危険に振る舞うか(ASI/AIVSS)」を統合=コンポーネント信頼からエージェント行動までのライフサイクル保証。
- **付録C: NHI(非人間ID)Top 10(2025)↔ ASI マッピング**。エージェント=管理対象NHIとして扱う論拠。NHI10「人間が非人間資格を使う=説明責任喪失」がASI09に対応。
- **★付録D: ASI Agentic Exploits & Incidents Tracker(実インシデント表)**。2025年の実例をASIにマッピング。**Google Gemini Trifecta**(ログ/検索履歴/ブラウジング経由の間接PIで連携Googleサービス横断の機密漏洩・意図せぬ操作=ASI01+ASI02)、EchoLeak(M365 Copilotゼロクリック)、Salesforce ForcedLeak、悪性postmark-mcp、Replit Vibe Coding Meltdown(本番DB削除+虚偽出力で隠蔽=ASI01+ASI09)等。**Gemini Trifectaは我々の脅威モデルにそのまま採用すべき実例**。
- 付録E: 略語集。

______________________________________________________________________

## 自社(e-Agency / 内部統制分科会)への適用示唆

1. **Palo Alto WPの裏取りが完了**。ベンダーWPの「新リスク5類型」を中立標準ASI10類型で再構成・補強できる。対応関係:
   - cross-agent task escalation → **ASI03**(+Confused Deputy)
   - untraceable data leakage → **ASI06/ASI07**(無記録のエージェント間流通)
   - synthetic identity → **ASI03**(Synthetic Identity Injection)/**ASI07**(A2A登録なりすまし)
   - chained vulnerabilities → **ASI04**(ランタイム能力合成)/**ASI08**(連鎖)
   - corrupted data propagation → **ASI06**(汚染)→**ASI08**(伝播)
     → リスク台帳は**ASI01〜10を一次分類**にし、Palo Alto5類型・総務省GLの脅威(PI/DoS/ポイズニング)を従属マッピングする構成に切り替えるのが最も整理がよい。
2. **総務省GLの空白が中立標準で埋まった**。利用指針/アセスメントで「LLM=総務省GL+OWASP LLM Top10、エージェント=OWASP Agentic Top10(ASI)」と二層で参照根拠を示せる。CC BY-SA 4.0なので設問・図表を翻案して外販テンプレに組込み可(継承ライセンス表記要)。
3. **起点3点 × ASI の対応が中立標準で確定**(上表)。特に:
   - 起点① → ASI05(RCE)/ASI06(RAG汚染・クロステナント漏出)/ASI03(権限)。**Gemini EnterpriseのRAGは「クロステナント・ベクトル漏出」と「自己生成物の再取込み」を要件に追加**。
   - 起点② → 「observability is non-negotiable」「改ざん耐性ログ+非否認」「エージェント間メッセージ・ツール呼び出しのログ」=単一LLM入出力ログより広い取得対象。ASI08のDigital twin replay は改訂ループ(S4)の検証手法に採用候補。
   - 起点③ → ASI06(汚染データ伝播→生成物適法性)/ASI09(説明の捏造で不適切な生成物を承認)。
4. **「Least-Agency(最小エージェンシー)」を設計原則に採用**。最小権限に加え「そもそもエージェントにどこまで自律させるか」を絞る。Palo Alto WPの「マルチエージェントは封じ込め不能=単一境界維持」判断と整合し、より上位の原則として明文化できる。
5. \*\*ASI02のアクション単位承認(高影響操作はdry-run/差分プレビュー+人間承認)\*\*を社内Geminiのツール実行ガードレール要件に。総務省GL別添の「オーケストレータ権限管理」をアクション粒度へ具体化。
6. **付録Aマッピング行列=外販アセスメントの中核フォーマット**。別添7Cワークシートの「対応箇所」列に、ASI/LLM Top10/AIVSSの該当IDを書く運用にすると、国内GL+国際標準の二重トレーサビリティが1枚で取れる。
7. **付録Dの実インシデント(特にGemini Trifecta)を社内研修・脅威モデルの一次素材に**。「実際にGeminiでこう漏れた」という具体例は経営層・現場への説得力が段違い。ブログ素材としても強い。
8. **AIVSS(定量スコアリング)の存在を把握**。リスクの優先順位付け・severityランキングの定量手法として、今後の取得バックログに追加(BI/OS/CV等のCore Riskカテゴリ)。

## 起点3点との関連

- **① 機密データ** → ASI05(RCE)/ASI06(RAG汚染・クロステナント漏出・自己再取込)/ASI03(権限濫用)/ASI04(サプライチェーン経由の漏出)。
- **② 監査ログ/監視** → observability非交渉原則、改ざん耐性ログ+非否認、エージェント行動・ツール呼び出し・A2Aメッセージのログ、ASI08 Digital twin replay、watchdogエージェント。
- **③ 著作権/法的適合性** → ASI06(汚染データの伝播による生成物適法性)/ASI09(説明捏造による不適切生成物の承認)/ASI04(汚染ソースの混入)。

## 関連

- 裏取り対象(ベンダーWP): [Palo Alto/Idira Securing Agentic AI](2026-04_paloalto-idira_securing-agentic-ai-identity-foundation.md)(本書ASI10類型で再構成・補強)
- 埋める空白の出所: [総務省 AIセキュリティGL 本体](2026-03_soumu_ai-security-technical-measures-guideline_main.md)(AIエージェント対象外)/ [パブコメ結果](2026-03_soumu_ai-security-technical-measures-guideline_public-comments.md)(大手が本書取得を要求)
- 上位の規範接続: [AI事業者ガイドライン別添 本体](2026-03-31_meti-soumu_ai-business-guideline_v1.2_attachment-main.md)(別添2A行動目標)/ [別添7C ワークシート](2026-03-31_meti-soumu_ai-business-guideline_v1.2_worksheet.md)(対応箇所列にASI ID)
- 法的接続: [文化庁 AIと著作権](2024-03-15_bunkacho_ai-and-copyright-approach.md)(ASI06/09経由で生成物適法性)
- 継続取得バックログ: OWASP「Agentic AI – Threats and Mitigations(T1-T17)」/「Securing Agentic Applications Guide 1.0」/ AIVSS / NHI Top 10 / NIST SP 800-53 AI control overlays / ISO/IEC 27090・27091
- 実装の行き先: ロードマップ **S4**(レジストリ・JIT・キルスイッチ・observability・Digital twin replay)、リスク台帳(ASI01-10を一次分類)
- メモリ: \[[ai-taskforce-governance-team]\] / \[[governance-roadmap]\]
