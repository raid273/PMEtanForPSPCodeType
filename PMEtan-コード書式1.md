## PMEtanForPSP コード書式1

### _CN0 CodeName コード名
~~~
 _CN0 コード名
~~~
文字コードShift_JIS、日本語可、半角64文字まで。

---
### _END END 読み込み終了
~~~
_END
~~~
ファイルの最後に**必ず**付けてください。  
これ以降の内容は読み込まれなくなります。

---
### _NWR NormalWrite 通常書き込み
~~~
_NWR 0xABCD0000 0xaaaaaaaa 0xvvvvvvvv
~~~
アドレスaにvをA桁分書き込む。  
B=0 :通常書き込み  
B=2 :最下位4bitをMask(一番下の桁を書き込まない)  
B=4 :PMEのメニューを開いたときに書き込んだ値を元の値に復元する  
C=0 :通常書き込み  
C=1 :And  
C=2 :Or  
C=3 :Xor  

---
### _MLS MultiLineSystem ループ処理
~~~
_MLS 0x00000000 0xaaaaaaaa 0xLLLLvvvv
~~~
AddressOffset = a , ValueOffset=0 , PointerOffset = 0をセットし、上L行をv回**追加で**実行(実行したい回数-1を指定する)。  
その際にAddressOffset、ValueOffset、PointerOffsetの分だけ書き込みアドレス、書き込み値、読み込みポインタ位置をずらす。

---
### _OFF Offset オフセット
~~~
_OFF 0x00CD0000 0xaaaaaaaa 0xvvvvvvvv
~~~
- D=0 :Offset設定  
    - C=0 :AddressOffsetにaをセット  
    - C=1 :ValueOffsetにvをセット  
    - C=2 :PointerOffsetにaをセット  
- D=1 :AddressShift  
    - AddressShiftに*a\*v*をセット  
    ループに関わらず、以降の書き込みアドレスがAddressShiftの分だけずれます。  
    C=0-3の範囲で4つ同時に設定できます。

---
### _CPY Copy コピー
~~~
_CPY 0xABCD0000 0xaaaaaaaa 0xvvvvvvvv
~~~
アドレスaからA桁分読み込み、Cで指定した方法でvと演算をする。  
B=0 :通常読み込み  
B=2 :最下位4bitをMask(一番下の桁が0になる)  
C=0 :加算  
C=1 :減算  
C=2 :乗算  
C=3 :除算  
C=4 :剰余  
C=5 :And  
C=6 :Or  
C=7 :Xor  
D=1 :コピーした値と*v<<a*をCで指定した方法で演算  

---
### _PST Paste ペースト
~~~
_PST 0xABCD0000 0xaaaaaaaa 0x00000000
~~~
コピーした値をアドレスaにA桁分ペーストする。  
B=0 :通常書き込み  
B=2 :最下位4bitをMask(一番下の桁を書き込まない)  
C=0 :通常書き込み  
C=1 :And  
C=2 :Or  
C=3 :Xor

---
### _TES Test 比較
~~~
_TES 0x00CD0000 0xaaaaaaaa 0xvvvvvvvv
~~~
コピーした値が条件Cなら下a行を実行する。  
C=0 :vと等しければ  
C=1 :vと等しくなければ  
C=2 :vより大きければ  
C=3 :vより小さければ  
C=4 :v以上なら  
C=5 :v以下なら  
D=0 :判定開始  
D=1 :Orを設定

---
### _PAD Pad パッド判定
~~~
_PAD 0x00C00000 0xaaaaaaaa 0xvvvvvvvv
~~~
vを押している間、下a行を実行またはスキップする。  
C=0 :押している間実行  
C=1 :押している間スキップ  
v=下の表の数値を加算

|key|value|
|:-:|:-:|
|SELECT|0x0001|
|START|0x0008|
|↑|0x0010|
|→|0x0020|
|↓|0x0040|
|←|0x0080|
|L|0x0100|
|R|0x0200|
|△|0x1000|
|○|0x2000|
|×|0x4000|
|□|0x8000|
|HOLD|0x20000|
|WLAN UP|0x40000|
|NOTE|0x80000|
|VOLUME +|0x100000|
|VOLUME -|0x200000|
|SCREEN|0x400000|
|REMOTE HOLD|0x800000|

---
### _PIN PointerInit ポインタ初期設定
~~~
_PIN 0x80000000 0xaaaaaaaa 0x00000000
~~~
aから読み込んだ値をベースアドレスとする。

---
### _PTR Pointer ポインタ
~~~
_PTR 0x8B000000 0xaaaaaaaa 0x00000000
~~~
ベースアドレス+/-aから読み込んだ値をベースアドレスとする。  
多重ポインタに使う。  
B=0 :通常  
B=1 :リバースポインタ  

---
### _PWR PointerWrite ポインタ書き込み
~~~
_PWR 0xABCD0000 0xaaaaaaaa 0xvvvvvvvv
~~~
ベースアドレス+/-aにvをA桁分書き込む。  
B=0 :通常  
B=1 :リバースポインタ  
B=2 :最下位4bitをMask(一番下の桁を書き込まない)  
B=4 :PMEのメニューを開いたときに書き込んだ値を元の値に復元する  
C=0 :通常書き込み  
C=1 :And  
C=2 :Or  
C=3 :Xor

---
### _PCP PointerCopy ポインタコピー
~~~
_PCP 0xABC00000 0xaaaaaaaa 0xvvvvvvvv
~~~
ベースアドレス+/-aからA桁分読み込み、Cで指定した方法でvと演算をする。  
B=0 :通常  
B=1 :リバースポインタ  
B=2 :最下位4bitをMask(一番下の桁が0になる)  
C=0 :加算  
C=1 :減算  
C=2 :乗算  
C=3 :除算  
C=4 :剰余  
C=5 :And  
C=6 :Or  
C=7 :Xor

---
### _PPS PointerPaste ポインタペースト
~~~
_PPS 0xABCD0000 0xaaaaaaaa 0x00000000
~~~
ベースアドレス+/-aにコピーした値をA桁分ペーストする。  
B=0 :通常  
B=1 :リバースポインタ  
B=2 :最下位4bitをMask(一番下の桁を書き込まない)  
C=0 :通常書き込み  
C=1 :And  
C=2 :Or  
C=3 :Xor

---
### _SPE シフト演算書き込み
~~~
_SPE 0xs0000000 0xaaaaaaaa 0xffffvvvv
~~~
アドレスaから2Byte分読み込み、その値とfのNotをAndし、それに*v<<s*をOrして書き込む。
