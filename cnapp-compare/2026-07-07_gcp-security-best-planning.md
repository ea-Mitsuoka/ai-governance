# GCP中心セキュリティ基盤 設計書(統合版)

> 本ドキュメント1枚で全体像が把握できるようにまとめた最終設計案です。
> 「製品中心」ではなく「**セキュリティデータ基盤中心**」を核とし、GCP専用で開始しつつ、将来のマルチクラウド・AI-SPM拡張とベンダーロックイン回避を両立させることを目的とします。

---

## 0. 前提条件(再掲)

- GCPがメイン(AWS/Azure対応は将来の拡張要件)
- 運用コスト・ライセンスコストを抑えたい
- 初期開発コスト・自社開発工数はある程度許容する
- ベンダーロックインを避ける
- 10年以上運用できるアーキテクチャを目指す
- 将来 AI-SPM / DSPM / Attack Path Analysis / Exposure Management を追加できる構成にする
- SOC運用まで見据える
- Infrastructure as Code / GitOps を前提とする

---

## 0.1 設計を左右する市場前提:Wizはすでに Google の一部

設計判断に直結する重要な事実:

- **2026年3月11日、Google は Wiz の買収を完了**(調整後 約295億ドル、Alphabet 史上最大の買収)。
- Wiz は Google Cloud に参加し、**独自ブランドとマルチクラウド対応の方針を維持**。
- Google は Google Threat Intelligence・Google SecOps と Wiz を統合した**統一プラットフォーム**を提供する方針を表明。

### この事実が意味すること

- 「Google純正 vs Google+Wiz」という対立軸は消滅しつつあり、**Google純正の将来像に Wiz が含まれる**構図になった。
- SCC(特に SCC Enterprise)と Wiz は機能が全面的に重複しており、今後 Google ポートフォリオ内で統合・整理される可能性が高い。
- したがって **SCC と Wiz の二重投資は避け、CNAPP はどちらかに一本化すべき**。
- 一本化の最終判断は急がず、**2026年内に Google 側の統合ロードマップを見極めてから**行うのが最も無駄が少ない。

> 補足:Cloudbase(国内CNAPP)は日本語サポート・国内コンプラ対応が強みだが、Wiz/SCC と機能がほぼ全面重複する。**4製品(SCC + SecOps + Wiz + Cloudbase)併用は明確に過剰**であり、Findings の二重管理・アラート疲れ・コスト多重払いを招く。CNAPP は 1 つに絞る。

---

## 1. アーキテクチャパターン比較

| 観点 | ①Google純正中心<br>(SCC Ent + SecOps) | ②Google + Wiz | ③Google + 自社開発<br>(データ基盤中心) | ④OSS組合せ | ⑤**推奨:ハイブリッド**<br>(BQ Data Lake + 選択的Buy) |
|---|---|---|---|---|---|
| 初期コスト | 低(有効化中心) | 低〜中 | 高(開発工数大) | 中(構築工数) | 中〜高 |
| ランニングコスト | 中〜高 | 高(Wizは高価格帯) | 極めて低(GCP従量のみ) | 低(人件費増) | **低(BQ+必要SaaSのみ)** |
| 保守性 | ◎(ベンダー任せ) | ◎ | △(自社責任) | ✕(OSS寿命リスク) | ○(dbt/SQL標準化) |
| 拡張性 | ○(Google依存) | ◎ | ◎(自由) | ○ | **◎** |
| AI-SPM対応 | ○(Wiz統合で強化見込) | ◎(業界先行) | △(自作要) | ✕(成熟OSSほぼ無) | **◎(BQ上のデータに直接適用)** |
| ロックイン耐性 | ✕(最大) | △(Google傘下で実質同群) | ◎ | ◎ | **◎** |
| マルチクラウド | △(統合で改善見込) | ◎ | ○(コネクタ追加) | ○ | **○〜◎** |
| エンタープライズ実績 | ◎ | ◎(Fortune100の50%) | △(大手テック系) | △ | ◎(構成要素が全て実績あり) |
| 10年運用 | ○ | ○ | ◎(思想が製品非依存) | △ | **◎** |

### 結論

**⑤ ハイブリッド構成を採用する。**
すなわち「**BigQuery Security Data Lake を中核**に、CNAPP は Google(SCC Enterprise、Wiz統合の行方を見て一本化)、SIEM は SecOps、分析の頭脳(Risk / Policy / Attack Graph / Compliance)は自社開発」。

前提(初期工数は許容、ランニング最小、10年運用、製品入替前提)に唯一すべて合致する。

---

## 2. データ基盤中心設計(Security Data Lake on BigQuery)の評価

### 結論:大賛成。10年運用ならこれが最適解。

Netflix、Airbnb、Uber、Spotify、GitLab などモダンな大規模テック企業は、市販 SIEM から BigQuery/Snowflake ベースの自社製 Security Data Lake へ移行済み。国内でも金融・決済系メガベンチャーや技術力あるエンタープライズで、dbt を用いたセキュリティデータ変換 + BigQuery の採用が進む。業界的にも **SIEM → Security Data Lake** は明確なトレンドで、OCSF(Open Cybersecurity Schema Framework)の登場が後押ししている。

### メリット

- **単一の真実(Single Source of Truth):** 製品を替えてもデータとロジックが残る。ロックイン耐性の核。
- **圧倒的なコスト効率:** BigQuery の従量課金は SIEM の GB 課金より桁違いに安く、長期保持に強い。
- **Detection-as-Code:** SQL で検知ルール・リスク分析を書け、GitOps と完全親和。
- **AI/ML との親和性:** BigQuery ML / Vertex AI(Gemini)をデータに直接適用でき、アタックパス解析やアノマリ検知を自社実装・拡張できる。
- **増築可能:** AI-SPM / DSPM / Exposure Management は「新データソース + 新分析エンジン」として後から足せる。

### デメリット・注意点

- **リアルタイム性のトレードオフ:** BigQuery はバッチ分析向き。リアルタイム検知は SecOps に、リアルタイム防御は GCP ネイティブ(Cloud Armor、IAM 拒否ポリシー)に委ねる割り切りが必要。
- **グラフ表現:** IAM 権限昇格やネットワークホップの「グラフ構造」を純 SQL で書くと再帰クエリが複雑化する。
- **スキーマ設計が最重要:** OCSF ベースの正規化スキーマを最初に間違えると 10 年響く。
- **データガバナンス:** ETL の保守と、データ分類・アクセス制御の設計が必要。

---

## 3. AI-SPM を考慮したデータモデル

AI 資産は通常アセットとは別エンティティとして扱い、**ノード + エッジのプロパティグラフ**として BigQuery に正規化して保持する。個々の資産テーブルの羅列ではなく、関係性で持つことが要点。

### ノード(資産)タイプ

| エンティティ | 主要属性 | ソース例 |
|---|---|---|
| AIEndpoint | resource_url, model_ref, auth方式, network_exposure(Public/Private) | Vertex AI API, Cloud Run |
| Model | provider(Vertex/OpenAI/Anthropic/自社ホスト), version, is_oss, fine-tune元, ライセンス, risk_score | Model Registry |
| Prompt | prompt_id, system_instruction_hash, version, allowed_tools(実行可能な関数/API) | Git / Prompt管理基盤 |
| Dataset | 分類(PII/機密), 由来, 用途(学習/RAG) | DSPM, Data Catalog |
| VectorDB | technology(pgvector/AlloyDB/Vertex Vector Search等), embedding model, data_sensitivity, アクセス制御 | Vector DB |
| Agent/Tool | MCPサーバ, 許可ツール一覧, 権限スコープ | LangGraph定義, MCP設定 |
| Secret | 種別, ローテーション, 参照元(Secret Managerハッシュ) | Secret Manager |
| Identity | SA/ユーザ, ロール | IAM |

### エッジ(関係)

`uses_model` / `trained_on` / `retrieves_from` / `can_invoke` / `has_secret` / `exposed_to_internet` / `can_impersonate` など。

### 依存関係の例

```
[Dataset] ──(finetune/train)──> [Model] ──(deploy)──> [AIEndpoint]
    │                              │                       │
 (contains)                   (uses_prompt)          (secured_by)
    ▼                              ▼                       ▼
[PII / Secret]              [Prompt Template]        [IAM / Network Policy]
```

### AI Attack Surface の例(グラフで保持)

```
Prompt Injection → Tool → Cloud Run → Service Account → BigQuery → Customer Data
```

### 設計のポイント

- 上記により「インターネット公開された AIEndpoint → PII 含む VectorDB を参照 → 過剰権限の SA で動作」といった **AI Attack Path をグラフクエリ 1 本で抽出**できる。
- Attack Path Analysis と**同じ基盤(同じグラフ)**に載せる。
- AI-SPM 製品(Wiz / Palo Alto / Microsoft 等)を後から導入しても、**製品の Findings をこのグラフのノード/エッジに正規化して取り込むだけ**で済み、入替が容易。
- スキーマは **OCSF を基底**に、AI 固有部分は独自拡張(OCSF の AI カテゴリ拡張は進行中)。
- DSPM の「どこに PII があるか」の検出は **Sensitive Data Protection(旧 DLP)** を使い、結果を資産グラフに紐付ける。「それがビジネス上どれだけ重要か」の判断は自社モデルに置く。

---

## 4. ベンダーロックイン回避:レイヤー分割

ロックイン回避の本質は「**製品を"センサー"に格下げし、頭脳を自社データ基盤に置く**」こと。製品は Adapter を必ず経由させ、分析ロジックが製品固有 API を直接呼ばないようにする。

```
L5  対応・ワークフロー   … チケット / SOAR(自社定義playbook、製品非依存)
L4  分析・判断ロジック   … Risk Scoring, 相関, Attack Graph(SQL/OPA/独自コード) ★自社資産
L3  正規化スキーマ       … OCSF + 独自拡張 ★交換可能性の要 / 自社資産
L2  取込パイプライン     … Pub/Sub + Dataflow + Adapter(製品ごとのコネクタのみ書換え)
L1  センサー製品         … SCC / Wiz / Cloudbase / Defender ← ここだけ交換
```

### リプレイス時の挙動

CSPM を SCC → Wiz → Defender と替えても、**変わるのは L2 の Adapter/コネクタ(数週間の工数)だけ**。L3 以上の検知ルール・リスクモデル・コンプラマッピング・運用フローは無傷。

### 補強ルール

- **Policy-as-Code** は製品独自 DSL でなく **OPA/Rego(または CEL)** で自社リポジトリに保持。
- **検知ルール**は **Sigma** など可搬な表現で書き、SecOps(YARA-L)へは変換して配備。
- コンプラ要件は NIST CSF / CIS ベンチマークへの**自社マッピング表**を持ち、製品のコンプラダッシュボードに依存しない。

---

## 5. Build vs Buy の分類

判断基準は「10 年維持できるか」「汎用規格があるか」「組織固有の文脈が価値の源泉か」。

### 買うべきもの(コモディティ、自作の差別化価値なし)

| 機能 | 採用 |
|---|---|
| CSPM / CNAPP スキャナ(脆弱性・ミスコンフィグ検知) | SCC Enterprise または Wiz(一本化) |
| SIEM リアルタイム検知基盤 | Google SecOps |
| 脅威インテリジェンス | Google Threat Intelligence |
| DSPM の分類エンジン | Sensitive Data Protection(旧DLP) |
| EDR / WAF / 脆弱性DB 等のシグナル生成系 | 各製品 |
| 検知ルール | 製品ルール + Sigma で独自追加 |

### 作るべきもの(組織文脈が価値、10年資産になる)

| 機能 | 理由 |
|---|---|
| **Security Data Model / 正規化層(L3)** | 最重要。全ての土台。OCSF ベースで自社定義 |
| **Asset Inventory(AI含む)** | CAI + SaaS(GitHub/IdP)+ AI資産グラフの統合ビュー。ビジネス文脈は製品に入れられない |
| **Risk Scoring** | 「自社にとっての重要度 × 露出 × 脅威」の重み付けは自社固有。製品には不可能 |
| **Correlation / Attack Graph** | 複数製品の Findings を横断相関できるのは自社データ基盤だけ |
| **Compliance Engine のマッピング層** | 製品交換耐性のため |
| **Dashboard(業務特化)** | Looker / Looker Studio。SQL が BQ にあるのでいつでも作り直せる |

### 中間(小さく作る / 薄く買う / OSS活用)

| 機能 | 方針 |
|---|---|
| Policy Engine | 自作せず **OPA/Rego** を採用(IaC 静的解析〜BQ 構成チェックまで共通化) |
| Workflow Engine | Cloud Workflows / Cloud Run + Eventarc で薄く自作、または軽量 SOAR |
| API Gateway | 自作不要。Cloud API Gateway / Apigee |
| Attack Graph | 初期は BQ 再帰CTE で自作。SQL で限界が来たら Neo4j か Wiz/SCC の機能を部分導入 |
| Correlation | BigQuery SQL + dbt/Dataform |

---

## 6. 最終推奨構成

### 全体アーキテクチャ / データフロー図

```
[GCP / GKE / Vertex AI]        [GitHub / CI (Trivy, Checkov)]
        │                                │
  Audit Logs / CAI /                スキャン結果
  VPC Flow Logs / SCC Findings          │
        └────────────┬──────────────────┘
                     ▼
        Pub/Sub (+ Cloud Run コネクタ / Adapter)   ← L2: 製品追加時はここだけ
                     ▼
   ┌────────── BigQuery Security Data Lake ──────────┐
   │ raw_* テーブル(GCP, GitHub, CI/CD, 将来Wiz/AWS)  │
   │      ↓ dbt / Dataform(OCSF正規化・Git管理)★中核 │
   │ normalized_* / asset_graph(AI資産含む)          │
   │      ↓ 検知(Sigma→SQL) / OPA評価 /              │
   │        Risk Scoring / 再帰CTE Attack Path        │
   │ findings_* / risk_views                          │
   └──────────┬─────────────────────┬─────────────────┘
              ▼                     ▼
   Looker / Looker Studio      Eventarc → Cloud Run
   (資産・リスク・コンプラ)      → Jira / PagerDuty(自製ワークフロー/SOAR)

   [Phase 3〜] Google SecOps:リアルタイム検知・ケース管理・IR・脅威インテリ
   [Phase 4〜] CNAPP / AI-SPM 製品:Adapter 経由で Findings 供給のみ(センサー化)

   ※ 全構成は Terraform + GitOps(GitHub Actions / Cloud Build)で管理
```

### コンポーネントごとの責務

| コンポーネント | 責務 |
|---|---|
| Cloud Logging / CAI / Pub/Sub | 低コストでログ・構成変更イベントを集約・配信 |
| Adapter(Cloud Run) | 各製品 API の差異を吸収し raw テーブルへ投入 |
| dbt / Dataform | 生 JSON を OCSF モデルへ変換(GitOps でコード管理)★変換の主役 |
| Dataflow | ストリーミング必須経路のみ(薄く運用) |
| BigQuery | 10年分の保存、超高速検索、相関・ハンティング、AI/ML 分析基盤 |
| OPA / Rego(Cloud Run) | 構成・IaC のポリシー違反(CIS 等)を横断評価 |
| Risk / Attack Path / Compliance(SQL) | 自社コアロジック層 |
| Google SecOps | リアルタイム検知・SOC ケース管理・IR(Phase 3〜) |
| Looker / Looker Studio | 資産・リスク・コンプラ可視化 |
| Eventarc + Cloud Run | 検知後の通知・チケット起票(自製 SOAR) |

### 推奨技術スタック

| レイヤ | 技術 |
|---|---|
| 収集 | Cloud Logging, Cloud Audit Logs, Cloud Asset Inventory, VPC Flow Logs, SCC, Pub/Sub |
| 変換 | **dbt または Dataform(主役)** + Dataflow(ストリーム経路のみ)+ Cloud Run + Eventarc |
| データ基盤 | BigQuery, Data Catalog, BigLake(必要に応じ) |
| モデリング | dbt/Dataform, SQL, Python |
| ポリシー | OPA, Rego, CEL |
| 検知 | Sigma → SQL 変換 / 後に SecOps(YARA-L) |
| DSPM分類 | Sensitive Data Protection(旧DLP) |
| グラフ | BigQuery 再帰CTE(将来必要なら Neo4j) |
| 可視化 | Looker, Looker Studio |
| IaC | Terraform, GitHub Actions / Cloud Build, GitOps(Config Sync / Argo CD) |
| SOC | Google SecOps + SOAR 連携 |

### 段階的導入ロードマップ

| Phase | 期間 | ゴール / タスク |
|---|---|---|
| **Phase 1** | 〜2ヶ月 | 基礎データレイクと可視化。CAI + Audit Logs を BQ へ集約、Looker Studio で資産ダッシュボード。Terraform 化。→ SCC Standard 以上の可視性がほぼタダで手に入る |
| **Phase 2** | 2〜6ヶ月 | 正規化と GitOps ポリシー運用。dbt 導入・OCSF 化、OPA で CIS チェック、CI/CD スキャン(Trivy/Checkov)を統合、自社 Risk Scoring v1 |
| **Phase 3** | 6〜12ヶ月 | 検知運用の自動化。SecOps 導入(リアルタイム検知 + SOC)、Detection-as-Code(Git管理)、Eventarc/Cloud Run で通知・チケット自動化、SQL 版 Attack Path |
| **Phase 4** | 12〜18ヶ月 | AI-SPM & DSPM。AI 資産グラフ実装(§3)、Sensitive Data Protection で DSPM、Gemini によるログ要約を SOC フローへ。**この時点で SCC/Wiz 統合後の市場を見て CNAPP/AI-SPM を一本選定**(Adapter 経由) |
| **Phase 5** | 18ヶ月〜 | マルチクラウド拡張と部分買い。AWS/Azure ログ(CloudTrail 等)を BigQuery Omni / 転送で取込、Exposure Management、AI エージェントによる分析支援・自動修復。SQL で限界が来た領域のみ「データは BQ から供給」を条件に製品を部分プラグイン |

---

## 7. 各設計判断の根拠(なぜこの構成か / 他案を採らなかった理由)

- **純正フル依存(①のみ)を主軸にしない:** L4/L5 まで製品に握られ交換不能になる。10 年スパンで SaaS ベンダーに主導権を渡すべきでない。
- **Wiz + SCC + Cloudbase 併給を採らない:** 機能重複への多重払い。加えて Wiz の Google 傘下入りにより「ロックイン分散」の意味も薄れた。
- **OSS 全振りを採らない:** AI-SPM 領域に成熟した選択肢がなく、10 年運用のメンテリスクが大きい。
- **変換の主役を Dataflow でなく dbt/Dataform に:** ストリーミングが本当に必要なのは一部のみ。大半の正規化・検知は BQ 内 SQL 変換の方が保守性・GitOps 親和性・人材調達すべてで勝る。
- **Neo4j を初期導入しない:** グラフ DB 運用は専任負荷が大きい。初期 Attack Path は再帰 CTE で十分。限界時に Neo4j か統合後の Wiz/SCC を「Adapter 経由で買う」。
- **自製 SOC のみで済ませない:** BQ バッチ検知は遅延あり。リアルタイム検知・ケース管理・IR は SIEM の領分。ただし Phase 1〜2 は BQ 自製検知で開始し、Phase 3 で SecOps を入れて初期コストを抑える。
- **AI-SPM / Attack Path 製品の即断購入を保留:** 買う対象(Wiz? SCC? 第三ベンダー?)が今まさに再編中。だからこそ AI 資産グラフ(自社データモデル)を先に作り、市場を見て後から接続する。

### 設計の背骨(3者の意見が完全一致した核心)

> **データモデルと検知ロジックを Git 管理の自社資産にし、製品はセンサーに格下げする。**

これがコストパフォーマンス・変化耐性ともに最も高いエンタープライズの理想形。

---

## 8. 次の一歩

1. Phase 1 の **ログシンク + CAI エクスポートの Terraform** を書く。
2. BigQuery に raw データを流し、Looker Studio で最初の資産ダッシュボードを作る。
3. **OCSF ベースの dbt プロジェクト雛形**を用意し、正規化を小さく開始する。

> 上記コード雛形(Terraform / dbt プロジェクト / OCSF テーブル DDL / AI 資産グラフのテーブル設計)は、必要に応じて具体的に書き起こし可能。
