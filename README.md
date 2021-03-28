# Circuit analysis

ソレノイド（33Ω, 55mH：値は参考値）に12V、100mAの電流を流したい。

最初FETを使ったこういう回路を考えた。
![](misc/1.png)図1

図2の回路（上記からソレノイドを外した回路）を組んでドレイン電流IDを測定してみたところ、Vgsがしきい値(図中のSIGNALが約2.0V)でのドレイン電流の下限が160mAとなり、仕様の100mAを超えてしまうことになった。
![](misc/2.png)
図2

よって図3のように電流制限抵抗をソレノイドに直列に入れることを考えたが、これだと分圧が発生してソレノイドの端子間電圧が降下してしまう。おまけに気づいたのが、そもそもソレノイドにある抵抗分（33Ω）があるので、図2で160mAのドレイン電流が流れるときのソレノイドの端子間電圧はV=IR=ID x R=0.16 x 33=5.28[V]となり、12Vの端子間電圧にならないのではないか。　
![](misc/3.png)
図3

ここまでの議論をLTspiceのシミュレーションで確認する。
BS170のSPICEモデルを動かすのが難しかったので、類似の2N7002を使ってシミュレーションしてみる。
入力電圧（SIGNAL）として3.15Vを与えると、160mA程度のドレイン電流が流れた。
![](misc/sim1.png)
図4

さて、この回路にソレノイドを挿入してみる。
コイルの両端に流れるドレイン電流は160mAとなり、FETのドレイン電流IDが問題なく流れているのでOK。
![](misc/sim2.png)
図5

問題は端子間電圧である。コイルの両端の端子間電圧は0なので、コイルと抵抗を合わせたソレノイド間の電圧を測定する。図6のV(V1,Vd)（緑）がソレノイドの端子間電圧、V(Vd)（青）がドレイン＝ソース間電圧Vdsを表す。FETがONになると、ソレノイド端子間電圧は5.5V程度、ドレイン＝ソース間電圧は6.5V程度となり、合計すると12Vとなる。つまり、ソレノイドに流れる電流に応じて端子間電圧が決まり(V=IR=160mA*33mΩ=5.28V)、それに応じてドレイン＝ソース間電圧が降下するという仕組みになっている。
![](misc/sim3.png)
図6

この仕組みはうまくなさそうなので、別の手を考える。アプローチとして以下を考えた。
１）カレントミラー回路を使って定電流にする方法
２）BJTをスイッチング素子として使う方法


（＊＊＊ところで＊＊＊）

ソレノイドバルブの表面には12V, 100mAという表示があった。そもそもこの解釈は、電圧かつ電流指定の仕様（＝12V、100mAを流せ）だと思っていた。一方で、ソレノイドの端子間電圧はソレノイド内部抵抗Rと流れる電流Iの2変数で決まる。Rはソレノイドに固有の定数なので変更できず、いまIを100mAとして与えると、電圧が12Vであるためには内部抵抗はR=V/I=12/0.1=120Ωでなければならないはずである。もしこれが正しければ、以下のようなリレーで単純にON/OFFすれば済んでしまう話である。抵抗はテスターで計測できるはずなので、あらためて現物を確認してみる。
![](misc/4.png)
図7

個人的な予想では、内部抵抗Rは120Ωになっていない気がする。この場合、12V, 100mAは電流・電圧の絶対的な値の指定ではなく、消費電力P=IV=1.2Wを与えるための参考値ということではないだろうか。であれば、電源電圧12Vを固定として、内部抵抗RにしたがってP=I^2 R=1.2Wになるような電流値を求め、その電流値を達成するFETのゲート電圧を逆算して求めれば、上記の回路でも動作する気がする。

（＊＊＊本題に戻る＊＊＊）











