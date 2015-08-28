""※現在、翻訳中です。しばらくお待ちください。

これらのツールはすべて異なりますが、共通した特性やよく似た部分があります。

言語ワークベンチが最も強力なのは、
プログラムの編集とコンパイルとの関係を変化させる点です。
基本的に、テキストファイルの編集からプログラムの抽象形の編集へとシフトします。
これからこのことについて説明します。

従来のプログラミングでは、エディタを使ってプログラムのテキストファイルを編集します。
次に、トランスレータを使ってコンピュータが理解できる形へと変換させ、そのテキストファイルを実行可能にします。
PythonやRubyのようなスクリプト言語の場合、この変換は実行時に行われます。
JavaやC#やCのような言語の場合は、コンパイル時に行われます。

[[http://capsctrl.que.jp/kdmsnr/wiki/bliki/img/compile.gif]]

''図1：伝統的なコンパイル方法''

このプロセスについてもう少し詳しくお話します。
図1はコンパイル プロセスを簡単に表しています。
foo.csを実行可能にするために、コンパイラを実行しています。
ここでは話を分かりやすくするために、編集プロセスを2つのステップに分けます。
最初のステップで、テキスト ファイルであるfoo.csをパースし、抽象構文ツリー(abstract syntax tree: AST)へと変換します。
そして、次のステップで、CLRバイトコードを生成しながら構文ツリーを走査していきます。
生成されたバイトコードはアセンブリ（exeファイル）に格納されます。

■ 関連した記事をご覧下さい：[[GeneratingCodeForDSLs]]{{br}}
外部DSLからどのようにコードを生成するかについて詳しく述べています。

プログラムは様々な形を持っており、コンパイラがその形を変換していると考えることができます。
ソースファイルは編集可能形で、プログラムを変更したいときはこれを編集します。
ソースファイルは保存形でもあり、ソースコードコントロールに保存して、使いたいときに取り出して使います。
コンパイラを実行すると、まず編集可能形を抽象形（抽象構文ツリー）に変換し、それから、コードジェネレータが実行可能形（CLRバイトコード）に変換します。

（実際に実行可能な形にするには、他にもいくつかの変換が必要です。しかし、一度バイトコードにしてしまうと、コンパイラの仕事は終わり、残りの仕事はコンパイラの範疇から外れます。）

抽象形は一時的なもので、コンパイラが実行される間のみ存在します。
また、これは、編集工程を論理な2つのステップに分けるためだけに存在しています。
外部DSLとシンボリック インテグレーションを行うのが難しいのは、この変換があるためです。
各言語は別々にコンパイルを行うため、抽象形とのリンクは存在しません。
生成されたコードで初めて両者は統合されます。そのとき、抽象形はもう存在していません。

より洗練されたポストIntelliJ IDEでは、このモデルに大幅な変更を加えています。
ファイルを読み込んだとき、IDEはメモリ上に抽象形を作成します。
これは、ユーザーがファイルを編集する際に使用されます（Smalltalkも似たようなことを行っていました）。
この抽象形は、メソッド名の補完などの簡単なものから、リファクタリングのような洗練されたものまで幅広く使用されます（自動リファクタリング機能は抽象形を変換します）。

これがどれだけ凄かったのか、同僚のMatt Foemmelが述べてくれました。
こうした機能に強力にアシストされ、彼は自分がテキストを入力していなかったことに後から気付いたというのです。
テキストを入力する代わりに、抽象形にコマンドを実行していたのです。
彼の行った変更はIDEによってテキストファイルに書き戻されますが、彼が操作していたのはまさしく抽象形でした。
現代のIDEを使っていて同じような感覚を持ったことがある人なら、
言語ワークベンチがどんなものなのか、なんとなく分かることでしょう。

[[http://capsctrl.que.jp/kdmsnr/wiki/bliki/img/workbench.gif]]

''図2：言語ワークベンチを使って様々な形を操作する。''

図2は、言語ワークベンチを使用した図です。
先ほどとは、「ソース」が編集可能なテキストファイルではない点が異なります。
ここでは直接、抽象形を操作します。
抽象形を編集するためには、言語ワークベンチが抽象形を何らかの編集可能な形へと投影(project)します。
この編集可能形は一時的なものです——人間を助けるためのものに過ぎません。
本当のソースは永続化された抽象形となります。

編集可能形は単に抽象形の投影に過ぎないため、何点か注意する必要があります。
最も重要なことは、編集可能形を完成させる必要がないことでしょう——差し当たり重要でないのであれば、その部分は無視することができます。
また、抽象形の投影を複数持つことができます——それぞれ、異なる側面を投影しています。
投影は言語ワークベンチの一部なので、
テキストファイルと比べると、動的に編集可能形を変化させることができます。
この投影エディタは言語と密接に結びついています。
結果としてユーザーは、エディタがどのように編集可能形を操作するのかを積極的に考えるようになります。
テキストファイルのような受身な編集可能形に比べ、
様々な考えをもたらしてくれるでしょう。

言語ワークベンチでは、保存形と編集形とを分けています。
保存形は抽象形のシリアライゼーションです。
よくやるのはXMLに保存する方法です。
ただしこのXMLは、人間が編集できるようにはデザインされていません。
XMLを保存形にすると、ツール間の互換性が高くなります。
しかし、そのような互換性は非常に難しいのが通例です。

従来ソースを実行可能形として扱う違いはありますが、
コード生成の部分は全く一緒です。
生成されるファイルは通常の言語ソースファイルと同じものですが、
これはソースではなく、直接編集できないコードです。
言語ワークベンチが成熟していくにつれ、
バイトコードのような編集できない構造を生成するほうがよいでしょう。

あまり気付かれないことですが、言語ワークベンチにとって重要なこととして、
抽象形はエラーや曖昧性に配慮すべきであるという点が挙げられます。
抽象形を操作するのであれば、常に抽象形を正常な状態にしておかなければなりません——ならば、不正確な情報をそこに入れられないようにすべきです。
しかしそれでは、ユーザビリティがひどくなってしまいます。
ポストIntelliJ IDEはこのことを認識しており、エラー状態をグラフィカルに表示するようにしています。
たとえば、コンパイルエラーのあるプログラムをリファクタリングするようなこともできます（ユーザビリティ向上のためには必要です）。

様々なソースから複雑な情報を取得するようになると、このことはより重要となってきます。
常にすべての一貫性をとり、正しく保つようなことはできません。
つまり、曖昧な状態やエラーのある状態とうまく付き合っていく必要があるのです——入力を制限するよりも、エラーを強調するのです。
また、ドキュメントのような計算不可能な情報も、モデルに簡単に入力できるようにしなければなりません。
この方法であれば、スキャニングした手書きのナプキンも、直接DSLコードにつながります。

!!新しいDSLの定義

このような構成にすると、
新しいDSLの定義は3つの主な部分に分けられます。

* まず、抽象構文を定義します。これは抽象形の「スキーマ」です。
* 次に「エディタ」を定義し、抽象形を投影を通じて操作可能にします。
* それから「ジェネレータ」を定義します。これは抽象形をどのように実行可能形に変換するかを定義しています。実際には、ジェネレータはDSLのセマンティクスを定義します。

これが主なトリオです。ただし、それぞれバリエーションが色々とあります。
先ほど述べたように、エディタやジェネレータを複数使っても問題ありません。
エディタが複数あることは普通のことです。
人によって編集方法の好き好きはあるものです。
例えば、Intentionalのエディタは同じモデルの異なる投影に簡単にスイッチすることができます。
ですから、階層的データ構造をLispのようなリスト形式、ネストされたボックス形式、またはツリー形式などにすることができます。

複数のジェネレータを使用するのには、いくつかの理由があります。
似たようなことを行う異なるフレームワークを構築するときに必要になることがあります。
例えば、あのイライラするSQLの方言が例として挙げられます。
他にも、パフォーマンス特性が違うとか、ライブラリ依存が違うとかで、実装のトレードオフが行われるときに必要でしょう。
3つ目の理由は、異なる言語を生成するためです。
つまり、ひとつのDSLからJavaやC#などを生成するのです。

他にも、保存形のトランスレータを定義することができます。
言語ワークベンチは抽象形を自動的にシリアライズしたデフォルト保存スキーマを使っていますが、
ツール間でやり取りするために他の保存形式を用いたり、その都度変換したりすることもできます。
この場合、通常のジェネレータとは異なり、変換は双方向に行われます。

人間が読めるドキュメントを生成するジェネレータもあります——javadocに相当する言語ワークベンチです。
言語ワークベンチとのやり取りは、ほとんどエディタ経由で行われますが、
Web文書や紙文書を生成する必要がまだあるのです。

■ 関連する記事をご覧下さい。：[[ALanguageWorkbenchIAction]]{{br}}
JetBrainsのMeta-Programming Systemを使ってDSLを定義する例です。言語ワークベンチがどう動くかの具体的な例です。