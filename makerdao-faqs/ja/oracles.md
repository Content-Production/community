# オラクル

## オラクルとは？

オラクルは、オフチェーンとオンチェーンの両方のデータをスマートコントラクトで使用できるようにします。例えば、ETHとMKRの価格のフィードデータです。システムは、各資産の価格を安全に提供するためにオラクルに依存しているため、システムのスマートコントラクトは、Collaterized Debt Position \(CDP\)が生み出すことが出来るDaiの量、清算価格、清算価格にヒットしたかどうかなど、その他多くの変数を決定することが出来ます。

## オラクルは、なぜ悪意ある利用者の攻撃対象になるのか？

もしETHの参照価格が、単一の組織によって決まっていたら、彼らは不正確な価格を不正に報告し、多くの問題を引き起こす可能性があります。たとえば、ETHの価格が0.01ドルと、不正に低くされたデータが提供されれば、システム内のすべてのCDPが清算され、過少担保であるとして清算ペナルティが課されます。一方、ETHの価格が人為的に高く提供された場合、たとえば100万ドルとすると、実際より多くの担保価値があるとシステムが判断するため、CDPユーザーは過剰な金額のDaiを発行することが出来ます。これはつまり、過剰に発行されたDaiが適切に担保されておらず、システム破綻につながることを意味します。

## どのようにしてオラクルのメカニズムの安全性が保たれているのか？

データの不正な提供を防ぐために、データ提供は分散化されています。互いに独立した複数のデータ提供者がいます。現在のETH価格フィードには、データを[Medianizer](https://github.com/makerdao/medianizer)コントラクトに送信する、[14の価格フィードのプロバイダー](https://mkr.tools/system/feeds)がいます。Medianizerは、レポートされた価格の中央値を、公式の参考価格として使用します。不正な価格を公表するためには、フィードプロバイダーの半分をコントロールする必要があるため、平均値の代わりに中央値を利用することで、参照価格を操作するのが難しくなります。さらに、中央値を使用することで、異常値が自動的に除外されます。

これに加えて、Oracle Security Module（OSM）は、価格フィード・データを、1時間遅らせることによって、このプロセスを保護します。これにより、MKRトークンのホルダーは、価格フィードのシステムに対するバグや攻撃を識別するための時間を確保できます。現在、OSMはMKRの価格フィードではアクティブですが、ETHフィードに関してはアクティブになっていません。これは、Single Collateral Daiの場合は、CDPが清算されると、担保はフィードに従って現在の市場価格で売却されるため、ETH価格のフィードは遅れることが出来ないからです。これは、価格フィードを遅らせられるように、Multi Collateral Daiでは変更されます。

## Oracle Securirty Moduleとは？

Oracle Security Module（OSM）は、新しい参照価格の公表を1時間遅らせます。これにより、MKRトークンホルダーは、価格フィードのシステムに対するバグや攻撃に対応する時間を確保できます。現在、OSMはMKR価格フィードでは、アクティブですが、ETHの価格フィードに関しては、アクティブになっていません。これは、CDPが清算された時、Single Collateral Daiの場合は、担保がフィードに従って現在の市場価格で売られるのに対し、Multi-Collateral Dai \(MCD\)では、価格フィードを必要としないオークションメカニズムを通じて売却されるためです。

## 価格フィードは誰が提供するか？

価格フィードプロバイダーは匿名で、Makerの内部の人、より大きな暗号通貨コミュニティの影響力のある人々、そして活動的なコミュニティメンバーで構成されています。

## オラクルの価格フィードを提供している人々の身元はわかっているか？

Maker財団内のごく少数の人々が、現在のすべての価格フィードプロバイダーが誰であるかを知っています。

## 価格フィードシステムはどのように機能するか？

ETHの参照価格は、[14の価格フィードプロバイダー](https://mkr.tools/system/feeds)によって提供されている、Medianizerコントラクトによって公表されています。各フィードプロバイダーは、次の場合に、Medianizerに価格の更新を送信します。

1. 提供元の価格が、直近に送信された価格に対して、決められた額以上の違いがある（現在はフィードプロバイダーに応じて1％から2％の間）。
2. 最後の価格更新は、t時間以上前（各フィードプロバイダーによって設定されます）。

各価格フィードプロバイダーは、[Setzer](https://github.com/makerdao/setzer)と呼ばれるツールを使用します。これは、1組の取引所から価格の中央値を引っ張り、それをMedianizerに送ります。次に、Medianizerはレポートされた複数の価格フィードの中央値からさらなる中央値を取ります。価格フィードプロバイダーは、自分が選んだ取引所から価格を取得し、Setzerを設定することができます。

## Medianizerとは？

[Medianizer](https://github.com/makerdao/medianizer)は、価格フィード情報を収集し、各資産の参照価格を公開する、オラクルシステムのスマートコントラクトです。これは、価格フィードプロバイダーのホワイトリストと、各アドレスによって提供された最近の価格の記録を管理しています。Medianizerが、新しい価格の更新を受け取るたびに、参照価格が再計算されて公開されます。

## Medianizerは、どのくらいの頻度で最新の参考価格を公表するか？

中央値の変更が個々の価格フィードで発生するたびに、Medianizerは更新された価格で「突かれ」ます。この変更された価格は、他のフィード価格と一緒に1つにまとめられ、変更がある場合は、中央値が再計算され、参照価格が更新されます。

## MakerDAOガバナンスは、OSMによる価格フィードの遅延を変更することが出来るか？

はい、このパラメータは "Price Feed Delay"と呼ばれています。

## フィードプロバイダーになるにはどのようなプロセスがあるか？

現在、フィードプロバイダーになるための正式な方法はありません。Makerがフィードの設定をし始めた当時、Maker財団の中に、ボランティアでフィードプロバイダーになってくれる人たちがいました。後からさらに、フィードプロバイダーは増え、コミュニティからなった人もいれば、他のプロジェクトからなった人もいます。また、提供者の身元は知られていません。

## オラクルシステムは、Sybil攻撃に対して耐性があるか？

[ウィキペディア](https://en.wikipedia.org/wiki/Sybil_attack)から、Sybil攻撃を引用すると、「Sybil攻撃では、攻撃者は多数の偽名アイデンティティを作成し、そしてそれらを利用することによって過度に大きな影響力を得て、ピアツーピアネットワークのレピュテーションシステムを破壊します。Sybil攻撃に対するレピュテーションシステムの脆弱性は、アイデンティティをどれだけ安価に生成できるか、信用のある存在にリンクする信頼のチェーンを持たない存在から、システムが入力を受け入れる度合い、そしてレピュテーションシステムが、すべての存在を同じように扱うかどうかによります。」

簡単な答えは「はい」になります。オラクルのシステムは、価格フィードプロバイダーのホワイトリストが存在するため、Sybil攻撃に対して耐性があります。それは価格フィードプロバイダーになることは、簡単ではありません。あなたのデータを受け入れられる為には、Medianizerコントラクトに承認される必要があります。したがって、フィードプロバイダーとして機能する安価な擬似IDを作成しても、大きな影響力を得ることは出来ません。

## 取引所でフラッシュクラッシュが発生した場合はどうなるか？

Dai Creditシステムで使用される参照価格は、14の異なるフィードプロバイダーによって提供されている中央値のさらなる中央値なので、異常値は自動的に除外されます。実際には、もし1つ取引所で「フラッシュクラッシュ」が発生した場合、一連の価格が次のように現れます。

\[0.70, 104.00, 104.11, 104.13, 104.49, 105.02, 105.45\]

この価格群の中央値は、資産の実際の市場全体の価格を反映しています。1つ取引所でフラッシュクラッシュが起こっても、公表されている参照価格には影響しません。
