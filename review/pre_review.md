## Pre-review comments (IEEE-style journal submission)

1) 研究目的（activity level）の定義が曖昧で、ラベルが“代理変数”になりすぎている。本文では「activity level」を SQL学習動画の視聴＋メモ＋クイズ＋アンケートから作っている（**Abstract**の単一段落、**Section 4「Description of evaluation」> Subsection「Overview」**の冒頭段落、**同節 > Subsection「Training dataset」**の**「Data acquisition for labeling」**および**「Data processing for evaluation」**）。しかしこれは「身体活動量」よりも **理解度・既知度・学習成果**に強く結びつく指標で、タイトル/要旨/導入で述べる “physical activity level” と整合しない（**Section 1「Introduction」**の**第1段落**（オフィスでの身体活動モニタリングの需要）、**同節第5段落**（提案システムと評価実験・ラベル生成の概要））。

[原因]
1節「Introduction」4段落目 "We obtained several indicators representing activity levels of people during work using tests and questionnaires on the educational video content."
acticity 

4節「Description of evaluation」subsection「Overview」"We measured activity levels from both subjective and objective perspectives. "
4節 subsection「Evaluation scenario」
"The proposed system estimates activity levels by extracting speciﬁc regions and multiple dynamic features from point-cloud data of individuals engaged in deskwork."
activity levelの定義に近い箇所は上記の箇所。
activity levelを主観（既知度・理解度）＋客観（クイズ得点）で測るという記述がされている。avtivity levelを「複数の数値指標」として扱うという定義と捉えられるが、その根拠は示されていない。

activity levelがどういうものか、という明確な定義もなく、評価方法だけが示されたように捉えられる。

[AI修正案]
**修正対象（節・段落）**:
- **Abstract**の1段落を、ラベルの意味に合わせて用語と主張強度を調整
- **Section 1「Introduction」**の**第1段落**（身体活動の文脈）と**第5段落**（提案と実験の要約）で、目的変数の定義を明確化
- **Section 4「Description of evaluation」>「Overview」**の冒頭段落で、指標が “physical activity” の proxy である根拠・限界を追記

**具体的に**:
- もし本当に “physical activity level” を主張するなら、クイズ/アンケートは主目的変数ではなく補助的評価（例: engagement の補助指標）として位置付け、**身体動作の ground truth**（姿勢遷移回数、頭部移動量、作業動作の頻度など）を別途定義・取得する、または少なくとも「本研究は身体活動そのものではなく *task engagement* を推定する」と用語を変更する。
- クイズ/アンケートを activity label として残す場合は、**Introduction**の**第5段落**の提案文に「本研究の activity level は *learning-task engagement / attentiveness* の proxy である」等を明記し、**「Overview」**の冒頭段落に先行研究・仮説（動作量と理解・集中の関係）と限界（認知バイアス/知識バイアス）を明確化する。


2) ラベル設計（k-means の k=3 固定）が恣意的に見える。**Section 4「Training dataset」>「Data processing for evaluation」**の第1段落で k-means を用い k=3 と固定しているが根拠が示されていない。
[原因]
4節「Data processing for evaluation」
"We used k-means … We set k=3."

`k=3` の根拠（妥当性評価）が本文中に見当たらない。

[修正案]
**修正対象（節・段落）**:
- **「Training dataset」>「Data processing for evaluation」**の第1段落（GL・k-means）と**「Test dataset」>「Data processing for evaluation」**の前半（同様のクラスタ設定を**「Training dataset」**節の記述に言及して要約している部分）に、k選定根拠を追加

**具体的に**:
- `k=3` について、例えばシルエット係数やエルボー法などの結果（図/表/本文）を追加し、なぜ3が妥当かを示す。


3) 二値ラベル(ML)生成が恣意的に見える。ML を agreement/disagreement と説明するだけで、GL→ML の変換規則が再現可能なレベルで定義されていない（**「Training dataset」>「Data processing for evaluation」**第2段落末尾のML定義文、**「Machine-learning model」**サブセクション冒頭～中盤のMLを出力・ground truth とする定義の叙述）。
[原因]
4節「Data processing for evaluation」
"The activity levels were converted into binary labels for agreement and disagreement, referred to as matching labels (ML). The ML were set to 1 for agreement and 0 for disagreement."

GL→ML（二値）変換の手続きが文章のみで、**「agreement/disagreement が何の一致を指すか」**（同一クラスタか、ペアの一致率か、閾値か等）が再現可能なレベルで定義されていない。

[AI修正案]
**修正対象（節・段落）**:
- **「Training dataset」>「Data processing for evaluation」**（GLからMLに至る第2段落全体）に、ML 定義の形式化を追加
- **「Machine-learning model」**サブセクション（入出力と ground truth の定義を述べる段落）に、ラベル生成手順の参照・前提（MLの意味）を明確化

**具体的に**:
- ML を **数式または擬似コード**で明記する（例: “dataset A と B の GL が一致なら ML=1、異なれば0” など）。現状の “agreement/disagreement” だけだと、査読者が正しく再現できない。


4) データ拡張（61→1830）がサンプル独立性を壊し、評価リーク（過大評価）を招く懸念がある。特徴差分や参加者類似度（相関行列距離）に基づく拡張により 1830 datasets を得ているが（**「Training dataset」>「Data processing for evaluation」**第2段落）、その後の評価がランダム 80/20 split と書かれており（**「Machine-learning model」**サブセクション内の「短動画データのみで80/20に分割」に関する文）、同一起源の情報が train/test に跨る可能性が高い。
[原因]
4節 subsection「Data processing for evaluation」2段落目
"data extension … extracted two datasets from 61 … absolute value of difference … similarity … correlation matrix … distance … ML … obtained 1830 datasets."

4節「Machine-learning model」
"randomly split the dataset into two parts: 80% for training and 20% for evaluation."

拡張は参加者間類似度・特徴差分などに基づく「組合せ」由来。ランダム分割だと、同一参加者/同一起源の情報が train/test に混入する可能性が高い。これにより推定性能が過大評価される（データ依存性によるリーク）と疑われる。

[AI修正案]
**修正対象（節・段落）**:
- **「Training dataset」>「Data processing for evaluation」**第2段落に、拡張データ生成の単位（被験者/動画/セッション）と依存性の扱いを明記
- **「Machine-learning model」**サブセクション（短動画の学習・評価分割を述べる部分）に、分割方法を「被験者単位（Group split）」へ変更／追記（少なくとも本文で明示）

**具体的に**:
- 評価は GroupKFold や Leave-One-Subject-Out 等の **被験者独立**を基本とし、拡張データが同一被験者に由来する場合は必ず同じfoldに束ねる、と明記する。
- 併せて「拡張なし（元61のみ）」結果も提示し、拡張が性能に与える影響と限界を分離して示す。


5) 評価指標が不足しており、Accuracy 主導の結論になっている。**Section 5「Evaluation result」> Subsection「Evaluation with short-video data」**内の**「Accuracy using RF (short-video data)」表**および**「Accuracy using XGBoost (short-video data)」表**（混同行列に続く指標の提示）しかなく、クラス不均衡や閾値依存を十分に議論できない。特に long-video の RF で **「Accuracy using RF (long-video data)」表**の Centroid 条件で FNR=1.000, FPR=0.000 は「片方クラスしか予測しない退化モデル」の可能性があり、Accuracy だけでは誤解を招く。
[原因]
5節「Evaluation result」の表群: Accuracy/FNR/FPR

Accuracy/FNR/FPR だけだと、クラス不均衡や「全件0予測」のような退化モデルが良く見える可能性があり、査読者に評価設計の弱さを突かれる。

[AI修正案]
**修正対象（節・段落）**:
- **「Evaluation result」**全体（特に**「Evaluation with short-video data」**および**「Evaluation with long-video data」**）の評価指標定義・表を拡張
- long-video の退化挙動が疑われる箇所（**「Evaluation with long-video data」**内の RF 指標表と、その直後の**subsubsection「Results of RF evaluation」**における解釈段落）に解釈と対処（指標追加、閾値調整、クラス比提示）を追記

**具体的に**:
- Precision/Recall/F1（macro/weighted）、balanced accuracy、MCC、ROC-AUC/PR-AUC 等を追加し、クラス比も併記する。
- 可能ならブートストラップ等で信頼区間を提示し、ばらつきを示す。


6) “significantly improved” と有意差を示唆する表現に統計的根拠がない。結論で「精度が significantly improved」と述べているが（**Section 6「Conclusion」**の**第1段落**）、本文中に統計検定・信頼区間・効果量の提示がなく、査読者には過大主張と受け取られる。
[原因]
**Section 5「Evaluation result」> Subsection「Results analysis」> Subsubsection「Influence of feature distribution differences for short and long videos on estimation accuracy」**内、**Fig. cdf_ZNCC50_dy**（左右方向のZNCC 50th のCDF）に対する解説ブロック
"The magnitude of the feature values showed no significant difference in the two set of data..."
Fig.9は単純に平均を示したものだが、有意差があるように記述している。significantというならば、統計検定が必要。

6節
"The estimation results demonstrated that the estimation accuracy of activity levels signiﬁcantly improved by using multiple features..."
accuracyに有意差があるような記述だが、accuracyに対して統計検定は行っていない。

統計検定や信頼区間が提示されていないため、"significantly" は査読上「有意差を主張しているのに根拠がない」と受け取られる。

[AI修正案]
**修正対象（節・段落）**:
- **Section 6「Conclusion」**の**第1段落**の表現

**具体的に**:
- 統計検定（差分のCI、適切な検定）を入れない限り、"significantly" を避け、"improved" / "showed higher accuracy" など根拠に見合う表現に弱める。
- もしくは、本文の **「Evaluation result」**に検定/CI を追加して主張を支える。


7) long-video データが小規模で、短動画→長動画の汎化（domain shift）主張が難しい。**Section 4「Test dataset」>「Data acquisition by LiDAR」**の後半段落（6 participants の記述）、**同 >「Data processing for evaluation」**の後半段落（15 datasets・ML生成まで）。それにも関わらず shortで学習→longで評価する設定（**「Machine-learning model」**サブセクション内の「短動画全量で学習し長動画で評価」に関する文）の解釈範囲が本文で十分に限定されていない。
[原因]
4.5「Test dataset」4.5.2「Data acquisition by LiDAR」
"We obtained point-cloud datasets for six
participants for the learning video."
4.5.4「Data processing for evaluation」
"We obtained 15 datasets"

被験者6名・データ15は、短→長の汎化を主張するには統計的に非常に弱い。加えて、拡張を含むため独立性の議論も必要になる。

[AI修正案]
**修正対象（節・段落）**:
- **「Test dataset」**サブセクション全体（**「Data acquisition by LiDAR」**～**「Data processing for evaluation」**）に、サンプル制約と解釈範囲（case study）を明記
- **「Machine-learning model」**で、汎化評価の限界を明確化

**具体的に**:
- long-video の結果はケーススタディとして位置付け、主張強度を調整する。
- 可能なら被験者数を増やす。増やせない場合は、分布差の定量（KS等）・感度分析・追加実験条件で補強する。


8) 頭部領域抽出がブラックボックスで、提案手法（centroid 特徴）の再現性・運用性が担保されていない。**Section 3「Proposed system」> Subsection「Methodology」**の第1段落末尾（領域抽出がスコープ外である旨）、**同節 > Subsection「Procedure」> Subsubsection「Feature creation」**の**centroid 特徴**に関する手順段落（初期フレームから頭部領域を抽出する前提）。
[原因]
3.2「Methodology」1段落目
"identifying and extracting specific regions … beyond the scope"
3.3.2「Feature creation」3段落目
"extract … head region from an initial frame …"

centroid 特徴は頭部領域抽出に依存するが、その抽出方法がスコープ外とされているため、実装・再現・運用の要点が不明確になる。

[AI修正案]
**修正対象（節・段落）**:
- **「Methodology」**の第1段落と**「Feature creation」**内の centroid 手法の手順段落

**具体的に**:
- 少なくとも「実験ではどう抽出したか」（手動選択、固定ROI、初期フレーム指定、センサ座標系での範囲等）を明記し、抽出誤差が特徴量へ与える影響を議論する。
- 実運用での自動抽出を想定するなら、候補手法（先行研究）を引用して最低限の見通しを示す。

9) training/test の実験手順説明が重複しており、重要な差分が埋もれている。**Section 4「Training dataset」>「Experiment」**の手順を述べる第1段落と参加者・動画条件を述べる第2段落、**「Test dataset」>「Experiment」**の同様の2段落がほぼ同文で、紙面を消費する一方で、動画条件・回数・期間・被験者数など「比較に必要な差分」が見えにくい。
[原因]
4.4「Training dataset」
4.4.1～4.4.4
4.5「Test dataset」
4.4.1～4.4.4

ほぼ同じ手順説明が繰り返されており、紙面効率が悪く、主要な差分（動画条件・被験者数・期間）が埋もれる。

[修正案]
**修正対象（節・段落）**:
- **「Training dataset」>「Experiment」**の第1段落（手順）および**「Test dataset」>「Experiment」**の第1段落（手順）

**具体的に**:
- 共通手順を1箇所（例えば **「Description of evaluation」**内）にまとめ、training/test は差分（動画本数/長さ、回数、期間、被験者数、クイズ制限時間等）だけを箇条書きで示す。

10) “training/estimation phase” と “training/test dataset” と “short/long video evaluation” の対応関係が本文で明示されず、用語が揺れて見える。**Section 3「Proposed system」> Subsection「System model」**の本文（図の直後の導入2文、学習フェーズの構成説明段落、推定フェーズの構成説明段落）、**Section 4「Description of evaluation」**の**「Training dataset」／「Test dataset」**各サブセクション、**「Machine-learning model」**サブセクション（短動画内80/20と短→長評価の併記）が並立しており、読者が“どれが学習フェーズ/推定フェーズに対応するのか”を推測しなければならない。
[原因]
3.1「System model」の提案システムtraining / estimation
4.4「Training dataset」のtraining dataset / test dataset（短ビデオ実験 / 長ビデオ実験）
4.5「Test dataset」
以下二種類の実験
短ビデオの80%をtraining data、短ビデオの20%test dataとした場合
短ビデオ全件をtraining、長ビデオをtest dataとした場合

各節において、training, testが別の意味が用いられているため、読み手が解釈を誤りやすい。

[AI修正案]
**修正対象（節・段落）**:
- **「System model」**の末尾（推定フェーズ段落の終わり）、または**「Description of evaluation」>「Overview」**の冒頭段落の末尾に「用語対応」と「評価プロトコル」を1段落で明示

**具体的に**:
- “training/estimation phase” はシステム運用概念であり、実験では “short-video dataset / long-video dataset” を用いた2つの評価（(i) short内分割、(ii) shortで学習→longで評価）を行う、という対応関係を本文で明示する（用語の統一、または「以降は dataset と呼ぶ」などの宣言）。


11) 式(1)（ZNCC）の \(x,y\) の定義が誤解を招く（フレーム間比較の画素値なのに「1次元目/2次元目の輝度」と書かれている）。ZNCCを “\(i\)番目と\(i+1\)番目フレーム間”で計算すると説明している一方で、**「Procedure」>「Feature creation」**の image-based 手法の説明のうち、**式(1)（ZNCC）の "where" 節と、その直後のフレーム間ZNCCの説明**（同一の長い段落内の連続記述）で \(x,y\) が“比較する2画像の対応画素値”なのか“画像の軸方向の値”なのか分からなくなる。
[原因]
「Feature creation」内、image-based feature modeling の ZNCC 定義（式と直後の数文）
ZNCCの標準的な記述では \(x_i, y_i\) は2枚の画像（ここでは隣接フレーム）の対応画素の輝度値を指すことが多いが、本文の “1st and 2nd dimensions” は画像の縦横（インデックス）と混線しやすく、数式と文章が整合しない。

[AI修正案]
**修正対象（節・段落）**:
- ZNCCの “where” 節と、その直後のフレーム間計算説明（**「Feature creation」**の image-based 段落内）

**具体的に**:
- \(x_i\) を “\(i\)番目フレーム（画像）における画素 \(i\) の輝度”、\(y_i\) を “\(i+1\)番目フレーム（画像）における対応画素の輝度” のように明示し、\(M,N\) は画像サイズ（幅・高さ）として説明する。必要なら添字（画素インデックス）とフレームインデックスを区別する記号を導入する。

12) 評価結果（5章相当）の記述が冗長で、かつ定性的形容（moderate/low 等）に基準がなく、さらに最小値の主張が表と矛盾する箇所がある。**Section 5「Evaluation with short-video data」> Subsubsection「Results of RF evaluation」**の混同行列の逐次説明段落、**同 >「Results of XGBoost evaluation」**の同様の段落で “moderate FNR / slightly lower FPR” のような表現が繰り返されるが、比較基準（他モデル比/目標値/ベースライン比）が提示されないため説得力が弱い。また short-video の XGBoost の結論で「最も低いFNR」が**「Accuracy using XGBoost (short-video data)」表**の数値と一致しない（**「Results of XGBoost evaluation」**内の比較結論の段落 vs 同表）。

[原因]
5.1.1 Results of RF evaluation
5.1.2 Results of XGBoost evaluation
XGBoost(short)の “lowest FNR” 記述と「Accuracy using XGBoost (short-video data)」表
“中程度/低い” などの形容が、何を基準にした相対評価か分からず、査読者には主観的に見える。

5.1.2 Results of XGBoost evaluation
- 表（Table result_xg_train）: FNR最小 = Image-50th (0.558)
- 本文（Results of XGBoost evaluation）: FNR最小 = Centroid-90th と主張
表と本文の主張の不一致

[AI修正案]
**修正対象（節・段落）**:
- **「Evaluation with short-video data」**内の**「Results of RF evaluation」**および**「Results of XGBoost evaluation」**の説明段落一式

**具体的に**:
- 形容を減らし、(i) Tableの数値の直接比較（差分）か (ii) ベースライン/目標値に対する位置づけ、のどちらかを必ず添える。
- “lowest FNR/FPR” 等の断定は表の値と突き合わせ、整合しない場合は修正する（参照している表・条件が異なるならその条件を明記する）。
