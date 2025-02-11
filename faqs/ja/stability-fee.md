# 安定化手数料

## 安定化手数料とは？

Makerのスマートコントラクトは、CDPから引き出されたDaiの総額に応じて計算された安定化手数料を徴収します。 安定化手数料はMakerDAOの暫定リスクチームによって出された提案にMKRホルダーが投票することによって変動する金利手数料です。

## いつ安定化手数料を支払う必要があるか？

DaiをCDPに返済することによって債務を支払うとき、返済されるDaiの額に比例して手数料（安定化手数料）が請求されます。この手数料はMKRまたはDaiで支払うことができます。

## 過去の債務に対して新たな手数料が請求されることはありますか？

ありません。安定化手数料は過去に遡って適用されることはありません。手数料が変更された後は、ローンの変動金利と同じように、その時点から新しい料金で安定化手数料が発生します。

## 安定化手数料の目的は何か？

安定化手数料は、暗号通貨市場の低成長またはマイナス成長の期間が原因で発生しうるDaiトークンの需要と供給の不均衡に対処するために設計されたリスクパラメータです。

手数料の背後にあるメカニズムは単純で、Daiに対する市場の需要が減少すると、新しいDaiの発行に対する手数料が増えます。市場の需要が増えるとその逆も成り立ちます。このようなリバランスは、CDP所有者がDaiを発行または焼却するインセンティブを変え、ソフトペグを安定化させる効果をもたらすことができます。

## 安定化手数料はなぜ変わるのか？

Daiが1ドルという目標価格の上または下で一貫して取引されていることが確認された場合、これはマクロで見た時のDaiの需要と供給の間の不均衡を示す可能性があります。安定化手数料は、Daiを作るコストに変化を与えることになるため、主にDaiの供給に影響を与える利率です。Daiをより安く借りられるほど、より多くのユーザーがそうするように動機付けされます。逆に、手数料が高い場合、ほとんどのユーザはDaiを借りたいと思わないでしょう。MKRホルダーは、ペグの健全さを維持するためにこの利率を設定することができます。

Daiが常に1ドルを超えて取引されている場合は、需要が供給を上回っており、市場参加者にDaiを購入するためにプレミアムを支払う意思があることを意味します。これがあまりにも一貫して起こっているならば、安定化手数料を下げてより多くのDaiの新規発行をしなければならないことを意味します。

もしDaiが常に1ドル以下で取引されているなら、供給が需要を上回っていて、市場にあまりにも多くのDaiが流通していることを意味します。もしこれがあまりにも一貫して起こっているのであれば、安定化手数料を上げてDaiの新規発行のスピードを遅くしなければならないことを意味するでしょう。

残念ながら、結果に関しては完全に市場の反応によるため、実装する前に手数料の変更の影響を完全に予測することは不可能です。時間が経つにつれて、時間が経つにつれて、予測モデルを立てるのに利用できるより良いデータが得られるか、あるいは安定化手数料を微調整するのに役立つ堅牢な対応モデルができてくるでしょう。

## 安定化手数料はどのように計算されるのか？

安定化手数料は_連続的_に計算されます。安定化手数料はDai建てであり、DaiまたはMKRで支払うことができます。以下の式に示すように、このタイプの複利は、週や月、年ではなく、とても小さな期間ごとに計算されます。この計算方法は、年間の複利計算から予想されるものに非常に近い手数料を生み出します。CDPのライフタイムを自由化するために、この種の計算方法が選択されました。CDPをオープンにしておく期間に関して、最低限ラインに関して制限がないので、システムが非常に小さい見越し額を効率的に追跡することが重要です。

365日間保有されていた10万Daiの債務を例にとって、様々な種類の複利構造を適用した場合の色々な結果を見てみましょう。

#### 計算式：

ここでの各記号の意味：

**A** =利子

**P** =元本投資額（初期の預金またはローン金額）

**r** =年利（10進数）

**n** =1年あたりの利子が合成される回数

**t** =お金が投資、または借り入れられた年数

**e** = [オイラー数](https://www.mathsisfun.com/numbers/e-eulers-number.html)

* **P** （1 + r / n ）^ nt - P = A：年間複利計算
* **P** （1 + r / n ）^ nt - P = A：月ごとの複利計算
* **P** （e ）^ rt - P = A：連続的な複利計算

**簡略化された計算**

年間複利を計算すると、将来の安定化手数料は次のようになります。

`
100,000×（1 +（12.5％/ 1））^（1×1） - 100,000 = 12,500 DAI
`

月ごとの複利計算で計算される将来の安定化手数料は、

`
100,000×（1 +（12.5％/ 12））^（12×1） - 100,000 = 13,241.60 DAI
`

連続的な複利計算で計算される将来の安定化手数料は、

`
10０，０００×2.7183 ...^（12.5％×1） - 100,000 = 13,314.94 DAI
`

12.5％APYで考えると、10万Daiの負債に対する年間および連続的な複利計算料金の差は、約**814.94 DAI**になります。安定化手数料が実際にどのように計算されるかについてもう少し例を見てみましょう。

### 簡単な例

下記を仮定すると:

* CDPは**1000** **DaiAI**の負債として存在する
* CDPは**30日間**オープンしています
* MKRの現在の値は**1000 DaiAI**です。
*安定化手数料は **5％** です
*ユーザーは **50 DaiAI**の債務から一部を返済します

**30日間 1000 DaiAI**の債務で**50 DaiAI**を返済するための総Dai建て費用は**0.208 DaiAI**、またはUSD換算でだいたい21セントです。

そしてDai建て債務を、支払いのためにMKRに変換する必要があります。つまりCDPの所有者は0.00021 MKRという負担を背負うということです。

### 詳細な例

CDPで発生する安定化手数料の合計は、次のように計算できます。

>（（（借りたDAI  * （1 +10進数形式における現在の安定化手数料）））^ （DAIを借りている日数/ 365 ）） - 借りたDAI ）= DAI建てで計算された全安定化手数料

上の例ですでに計算した値を代入すると、 手数料はDaiAI建てで、

`
（1000 *（1 + 0.05）^（30÷365）） - 1000 = 4.018 DAI
`

Dai建ての手数料の総計を得たので、金額をMKRに変換できます。 1 MKRが1000 Daiの価値であるような仮の取引レートで考えます。

`
4.018 ÷ 1000 = 0.004018 MKR
`

そして、ユーザーが50 DaiAIを返済中のとき、彼らはその金額に見込まれる手数料を支払うことを求められます。

`
（50 *（1 + 0.05）^（30÷365）） - 50 = 0.2009 DAI
`

MKRに変換を行うと

`
0.2009 ÷ 1000 = 0.0002009 MKR
`

**30日後** **50 DAI**の未返済手数料をカバーするには、ユーザーのウォレットに**0.0002009 MKR**が必要です。

取引が完了した後、CDPに残っている手数料の総額は以下のようになります。

0.004018 - 0.0002009 = **0.0038171 MKR**

## 徴収された手数料はシステムによってどう扱われるか？

手数料が徴収されると、スマートコントラクトプラットフォームはMKRを[Burner](https://etherscan.io/token/0x9f8f72aa9304c8b593d555f12ef6589cc3a579a2)と呼ばれるコントラクトに送金します。

実際に破壊される前にBurnerのウォレットに存在していたMKRは、そのアドレスからは資金を誰も引き出すことができないため、永久に流通からは除外されることになります。

## 現在支払われている安定化手数料はどこで見られるのか？

[旧バージョンのCDP Dashboard](https://dai.makerdao.com/)：CDPの未返済残高は、Daiダッシュボードの[Governance Debt]列に表示されます。

[新しいバージョンのCDPポータル](https://cdp.makerdao.com/)：CDPの未返済残高は、[Payback]をクリックすると表示される右側のパネルに表示されます。

[自分のDaiセクション](https://github.com/makerdao/awesome-makerdao/blob/master/README.md#watch-your-dai)には、Awesome-MakerDAOリポジトリに載っているサードパーティ製のツールも多数あります。

## 手数料は需要と供給をどのように変化させるのか？

安定化手数料が増加することで、CDP利用者対して高い借り入れコストが課されます。そしてそれは、CDPの利用を魅力的でないものにし、それによってDaiの供給量が削減されることに繋がります。逆に言えば、安定化手数料（借り入れの費用）の減少は、Daiの追加発行のインセンティブを提供することになり、Daiの供給の成長を引き上げる金融政策ツールとして機能することになります。

## 変動する安定化手数料の影響を計算するにはどうすればいいか？

下記に示すような単純化された計算式を使うことで、発生した安定化手数料を決定できます。

> （（借りたDAI  * （1 + 10進数形式における現在の安定化手数料））^ （借りた日数/ 365 ）） - 借りたDAI ）= DAIで支払う安定化手数料の総額

31日かけて10,000 DAIの債務を5.0％の利率で支払う場合の手数料：

`
（10000 ×（2.7183 ...）^（5.0％*（31/365）） - 10000 = 42.474 DAI
`

10.0％の利率の場合は：

`
（10000 ×（2.7183 ...）^（10.0％*（31/365）） - 10000 = 85.2937 DAI
`

## リスクチームについてもっと学び、現在もしくは将来のシステムの変更について、財団とコミュニケーションをとるにはどうすればよいか？

[ガバナンスとリスク](https://calendar.google.com/calendar/embed?src=makerdao.com_3efhm2ghipksegl009ktniomdk%40group.calendar.google.com&ctz=America%2FLos_Angeles)に関する週次ミーティングへの参加を検討してください。週次ミーティングでは、より詳細にガバナンスとリスクに関する問題について議論が行われています。議題は[r / MakerDAO](https://www.reddit.com/r/MakerDAO/)に定期的に投稿されています。それか、Awesome-MakerDAOレポジトリの[Governance section](https://github.com/makerdao/awesome-makerdao/blob/master/README.md#governance)もチェックしてみてください。

## 安定化手数料の変更の範囲に制限はあるか？

リスクチームは、安定化手数料を変更するためのしきい値（時間の経過による変化率、ペグの偏差、サンプリング時間）を提案し、投票権を持つMKRホルダーが承認してくれるように提案します。

## CDP所有者はどうすれば手数料変更リスクを減少させるのか？

ユーザーが手数料をヘッジするには、いくつかの選択肢があります。

MKRの需要は安定化手数料の増加と正の相関関係があります。その相関関係が意味のあるもので、持続的であれば、CDP参加者はMKRを保有することで安定化手数料のリスクに晒されている度合いを一部をヘッジできるかもしれません。

固定期間もしくは固定金利のローンを探しているユーザーは、他の貸出プラットフォームで定期期間のローンを行う意志がある相手方を見つけることによって、将来の手数料の変動による損失を一部補填することができます。

ただ、一番のリスク管理戦略は、ガバナンスのプロセスに参加し、提案が承認された際に確実に投票を行えるようにすることです。

## 手数料変更が投票で却下された場合はどうなるのか？

手数料率変更の投票が失敗した場合、そのまま現在の不均衡が続くと、Daiの価格が下がる可能性があります。どうしても解決しない場合は、財団に残された唯一の選択肢は、Daiの保有者の経済的安全を確保するために緊急停止を発動することです。

投票のメカニズムは、金融政策ツールの微調整を通じてコミュニティがペグを管理するための主な方法です。このメカニズムには「バックドア」はありません。そして、誰しもシステム内のリスクパラメータを一方的に変更することはできません。

## 安定化手数料はどういった頻度で変更されるのか？

手数料がいつ変更され、レートがいくらになるのかに関して、スケジュールを設定したり、予測を立てることはできません。

暫定リスクチームは、以前の変更の結果を継続的に監視しています。そして、必要に応じて同程度または異なる規模の調整の提案を行います。市場がどれほど迅速に、そしてどれほど大きさで反応するかを見極めるのはとても難しいことです。そのため、リスクチームはケースバイケースである前提で、あらゆる調整に取り組みます。

恣意的な操作が行われる危険性があるため、安定性手数料をどう調整するかを正確に決定する際には、ある程度の裁量とシグナルの処理が常に必要になってきます。全自動でアルゴリズムにより制御されたプロセスは恣意的な操作に対してはむしろ脆弱なので、導入に関しては慎重に検討する必要があります。


