## 64.再現性(Reproducibility)について

現代のコンピュータにおける浮動小数点演算は，IEEE 754に準拠した基礎的な数値演算が完全には規定されていないため，追試が難しいことで有名です。根本的な問題点は演算の精度がハードウェアプラットフォームやソフトウェアの実装により異なっていることにあります。
Stanは完全な再現性を許容すべく設計されています。しかしながら，それはあくまで浮動小数点計算により課せられた外的制約により左右されます。

Stanの結果は以下のすべての要素が一致しているときにのみ厳密に再現可能となります：

- Stanのバージョン
- Stanのインターフェイス(Rstan, PyStan, CmdStan) およびそのバージョン，さらにインターフェイス言語(R, Python, shell)のバージョン
- インクルードされたライブラリのバージョン(BoostおよびEigen)
- OSのバージョン
- CPU，マザーボード，メモリを含むコンピュータのハードウェア
- C++コンパイラのバージョン，コンパイル時のフラグ，リンクされたライブラリ
- 乱数の種，チェーンのID，初期化およびデータを含むStan呼び出し時の設定

これはStanの安定リリースを使っているか，特定の Git ハッシュタグを持つバージョンを使っているかには関係ありません。インターフェイスやコンパイラなどについても同様です。重要なのはもしこれらのどれか一つでも何らかの違いがあれば，浮動小数点計算の結果は変わる可能性があるということです。

具体的には，もしあるStanプログラムをCmdStanでコンパイルするときに，最適化フラグを(-O3 から -O2 または -O0へ)変更した場合，変更前と変更後の一連の結果は必ずしも一致しません。このため，クラスターや，IT部門に管理されている，もしくは自動更新がONになっているデスクトップのように外部に管理されたハードウェア上で再現性を保証するのは極めて困難です。

しかしながら，もしStanプログラムを一組のフラグを使ってコンパイルし，そのコンピュータをインターネットから取り外して一切アップデートしないようにし，10年後に戻ってきて同じように再コンパイルした場合，同じ結果が得られます。

データについてもビットレベルで同じである必要があります。例えば，もしRStanであればRcppがRの浮動小数点数とC++の倍精度小数の間の変換を行います。もしRcppが変換のプロセスを変更したり異なる型を使うと，結果はビットレベルで同じであることは保証されません。

コンパイラとコンパイラの設定も同じ問題を起こす可能性があります。インテル製のプロプライエタリなコンパイラで再現性をいかにコントロールするかについての素敵な議論はCoden and Kreirzer(2014)を読んでください。