# 00. 基本原則・適用対象・設計判断(To-Be)

S2の土台。すべての指針・禁止事項・基準がここから演繹される。
S1で方向性が出ている大きな設計判断を、確定済み前提として記録する。

______________________________________________________________________

## 1. AI利用の基本原則(8原則)

総務省AI事業者ガイドラインの共通指針10項目を、e-Agencyの言葉で8原則に圧縮。すべての判断の上位規範。
この8原則は[ISO/IEC 23894の8原則](../../sources/2023-02_iso-iec-23894-ai-risk-management-guidance_preview.md)(Inclusive/Dynamic/Human & cultural factors/Continual improvement 等)とも整合し、国内GL+国際標準の両系統で裏付けられる。

| # | 原則 | 意味 | 出典 |
|---|---|---|---|
| P1 | **人間中心** | AIは人間の判断を補助する。最終責任と重要判断は人間が負う。 | AI事業者GL共通指針/広島指針/[NIST(人間-AI構成)](../../sources/2024-07_nist_ai-rmf-generative-ai-profile_ai-600-1.md)/SAIF(エージェント行動への承認) |
| P2 | **合法性・権利尊重** | 法令・契約・第三者の権利(著作権・個人情報・営業秘密)を守る。 | 文化庁/個情法/不競法/NIST GAI10(知財) |
| P3 | **安全性・セキュリティ確保** | 不正操作による機密漏えい・意図せぬ変更/停止を生じさせない。 | [総務省セキュリティGL](../../sources/2026-03_soumu_ai-security-technical-measures-guideline_main.md)/OWASP/SAIF/ISO 27001 |
| P4 | **透明性・追跡可能性** | 誰が・何を入力し・何が返り・何を実行したかを記録・説明できる。 | T8/observability非交渉/[SAIF(エージェント可観測性)](../../sources/2025_google-saif-secure-ai-framework_web.md) |
| P5 | **最小権限・最小エージェンシー** | 権限も自律性も「必要最小限」に絞る。 | [OWASP ASI](../../sources/2025-12_owasp_top10-for-agentic-applications-2026.md)/[SAIF(エージェント権限)](../../sources/2025_google-saif-secure-ai-framework_web.md)/Palo Alto |
| P6 | **Secure by Design** | 後付けでなく設計段階から統制を組み込む。点でなく面(ライフサイクル全体)で守る。 | Palo Alto「Secure AI by Design」/SAIF(セキュア・バイ・デフォルトのML基盤)/NIST GOVERN1.2 |
| P7 | **公平性・品質** | 差別・偏見を避け、ハルシネーションを前提に出力を検証する。 | AI事業者GL共通指針/[NIST GAI6(バイアス)](../../sources/2024-07_nist_ai-rmf-generative-ai-profile_ai-600-1.md)/ISO TR24027 |
| P8 | **アジャイル・ガバナンス** | 作って終わりにせず、外部基準・事故・新技術に応じて継続改訂する。 | AI事業者GL第2部E/NIST(4機能)/ISO 23894(8原則)/ISO 42001(PDCA) |

> ★ ゼロトラスト前提:**自然言語の入力も、AIの出力も、ともに非信頼として扱う**。入力=社内ユーザも攻撃者になりうる(OWASP ASI/総務省GL想定事例1)。**出力=ハルシネーション・不適切出力の前提で下流に渡す前に検証する**([OWASP LLM05 不適切な出力処理](../../sources/2024-11_owasp_top10-for-llm-applications-2025.md))。性善説で組まない。
>
> 横断的な根拠対応は [framework-comparison.md](../framework-comparison.md) §5(P原則×基準)を参照。

______________________________________________________________________

## 2. 適用対象(誰に・何に)

### 2.1 人(適用される者)

- 全役職員(正社員・契約・派遣)、業務委託先・協力会社のうち当社AI環境を利用する者。
- 役割αβγ(別添7C準拠):**α=利用判断者(現場)/β=承認・管理者/γ=統制・監査(当チーム/BSM室)**。

### 2.2 システム(対象となるAI)

| 区分 | 例 | 主な立場 |
|---|---|---|
| 社内導入AI基盤 | **Gemini Enterprise**(社内RAG・エージェント) | 利用者+開発者 |
| 外部SaaS型AI | ChatGPT, Claude, 各種AI機能付きSaaS | 利用者 |
| 自社開発AIエージェント | Geminiベースの業務エージェント | 開発者 |
| 外販AI(将来) | 顧客向けに提供するAIソリューション | 提供者 |

### 2.3 e-Agencyの3立場(立場で適用条項が変わる)

- **AI利用者**(既定):ベンダー評価・適正利用・入力データ管理が中心。
- **AI提供者**(外販):顧客への情報提供・適合性・契約上の責任。
- **AI開発者**(自社エージェント):広島AIプロセス国際行動規範レベルの開発規範。

______________________________________________________________________

## 3. 設計判断(確定済み前提)

S1の蓄積で方向性が確定しているもの。S2で前提として固定し、以降の文書はこれに従う。

### 判断1 ガバナンス体制類型

- **結論:Functional 起点、将来 Centralized へ移行。**
  - 当面:既存機能(当チーム=内部統制分科会/BSM室)に責任を埋め込む Functional 型。小〜中規模に現実的。
  - 移行条件:AIエージェント利用が事業の中核化したら Centralized(AI倫理委員会/Chief AI Officer)へ寄せる。
  - 接続:AI事業者GL別添2A 1-1(AI倫理委員会論)。
  - 根拠:[Palo Alto WP](../../sources/2026-04_paloalto-idira_securing-agentic-ai-identity-foundation.md) ガバナンス3類型。

### 判断2 ポリシー文書の構成型

- **結論:既存ポリシー基盤型。**
  - ゼロから新ポリシー群を作らず、既存の情報セキュリティ規程・情報管理規程・就業規則にAI条項を接ぎ木する。
  - 根拠:[別添2A本体](../../sources/2026-03-31_meti-soumu_ai-business-guideline_v1.2_attachment-main.md)(e-Agencyに最適と判断済)。
  - 国際的にも同方向:[ISO/IEC 42001(AIMS)はISO 27001(ISMS)と整合するよう設計](../../sources/2025-05_kpmg_iso-iec-42001-aims-certification-overview.md)され、「既存ISMSにAIMSを統合」が定石。将来27001を持つ/取るならAI統制を載せやすい。
  - 方針に盛り込むべき項目の網羅チェックは[NIST Playbook GOVERN 1.2](../../sources/2023-01_nist_ai-rmf-1.0-playbook.md)のリスト(変更管理・内部通報者方針・インシデント対応・第三者AIの包含等)を使う。

### 判断3 エージェント自律度の許容方針

- **結論:Least-Agency + 単一エージェント境界維持。**
  - 最小権限に加え「そもそもどこまで自律させるか」を絞る(Least-Agency)。
  - 高自律・オーケストレータ型の多段エージェント連携は慎重に。マルチエージェントは封じ込め不能=リスクが連鎖伝播するため。
  - 全社展開のペースを、レジストリ・監視・キルスイッチの整備状況に律速させる(自律性が封じ込めを追い越さない)。
  - 根拠:[OWASP ASI](../../sources/2025-12_owasp_top10-for-agentic-applications-2026.md)/[Palo Alto WP](../../sources/2026-04_paloalto-idira_securing-agentic-ai-identity-foundation.md)。**Gemini提供元Googleの[SAIF](../../sources/2025_google-saif-secure-ai-framework_web.md)もエージェント3統制(最小権限=エージェント権限/ユーザ承認/可観測性)を中核に据え、本方針と同結論**=確度が高い(具体統制は[03 B-2](03-three-pillars-to-be.md)・[04 C-2](04-operational-flows.md)へ)。

### 判断4 社内版/外販版の二形態設計

- **結論:各文書を二層で書く。**
  - 「自社の答え(社内適用版)」と「顧客が穴埋めする雛形(外販テンプレート版)」を同時に意識。
  - 外販テンプレは別添7C(ワークシート)+ OWASP ASI付録A(マッピング行列)を骨格に。
  - 根拠:ロードマップ基本思想。

______________________________________________________________________

## 関連

- 上位規範: [AI事業者GL本編](../../sources/2026-03-31_meti-soumu_ai-business-guideline_v1.2_main.md)/[別添2A本体](../../sources/2026-03-31_meti-soumu_ai-business-guideline_v1.2_attachment-main.md)
- 原則の国際的裏付け: [ISO/IEC 23894(8原則)](../../sources/2023-02_iso-iec-23894-ai-risk-management-guidance_preview.md)/ 横断対応表 [framework-comparison.md](../framework-comparison.md)
- 次: [01 リスク分類・格付け基準](01-risk-classification-and-grading.md)
