# the world of java card

@moznion
CompletableFuture

go-u Java Carrd Appletが書けるようになる

## JavaCard

クレジットカード
SIMカード
B-CASカード

今回SIMカードの話が多そう？

JavaCardではfloatが扱えない
charが扱えない
byteだったら扱える

## SmartCard

JavaCardの前身
- ISO7816
  - Intelligent Smart Card
  - Memory Card
- JavaCardはIntelligent Smart Card

- Smart Card Memory

### interface

金色のチップとカードリーダーとの接点でやりとりする
外の世界とは APDU というプロトコルでやりとり

## APDU

ApplicationProtocolDataUnits

- command apdu
- response apdu

### command apdu

- CLA classの識別子
- INS 命令コード
- P1P2 命令パラメータ
- Lc Data fieldのバイト長 (content-length)
- Data Field バイト列
- Le レスポンスのData Fieldに許される最大バイト列

### response apdu

- Data Field レスポンスとしてバイト列
- SW1/SW2 ステータス 成功または成功かも？

## UICC

- Intelligent Smart Cardの一種
- ある程度演算できる
  - たしざん かけざん

- CPU 32bit
- 電気特性 1.8/3/5V
- クロック 1~5Mhz
- ROM 16kb~
- EEPROM 8kb~
- RAM 256b~

## UICCアーキテクチャ

- UICC
  - Core O.S

## JavaCard

SmartCardの一種

Ver2.1から存在 (1999)
Ver1はない？

暗号化や鍵交換アルゴリズムのアプデによって更新される

### UICC上のJavaランタイム JCRE

- かなり制限されている
- 基本16bit等
  - primitive type
  - native methods
- GCがあったりなかったりする カードごと

- サンドボックス機能
  - 一つのカード上で複数applet環境が立てられる
  - appletを特定のオブジェクトに明示的に共有できる
- capability control

### メモリ

- ROM
- EEPROM
- RAM

- インスタンス変数は全てEEPROMに書き込まれる
  - 電源が切れてもインスタンス変数は残る
  - 電源が切れてもJVMは止まらない
- インスタンス変数に書き込みまくるとカードが死ぬ

- RAMは非常に小さい
  - 奪い合いによる例外
- 本当に一時的なデータや本当にセンシティブなデータを入れるときだけ使う

### JVM Lifecycle

- 一生動き続ける
  - シャットダウンの概念がない
- 電源がない場合は「無限のクロックサイクル」としてみなしている
  - ずっとフリーズしている

#### phaze

- initialization phaze
  - ROMに書き込む
  - 発行者名, 製造者名等
- Personalization phaze
  - ROMに書き込む
  - ユーザー名、鍵、PINなど
だいたいinitialization phazeはすんでいる

### Applet

いわゆるJava Appletではない
  - Runtime上で動かすものの単位

## how

すごい環境
- JavaCard3.0(not classic)移行でないとintは使えない
  - 基本はshortを使う
- float, doubleは使えない、ようきゅうするbitが長い
- string使えない
  - byte[]を使う

- EEPROM は 100,000writes
- インスタンス変数はEEPROMに保存される

- java -> compile -> class -> convert -> exp, cap, jca
- compile
  - jdk1.6しか動かない

- IDE: Eclipse
  - javacard pluginがある唯一のIDE
- JDK: JDK 1.6
- JCDL

toolchain
- Live testing
- Writer

基本動作
JCRE - Applet
->: install
<-: register
->: select
->: process
->: deselect

JavaCard.framework.Applet

HelloWorld: https://gist.github.com/moznion/3bfbc5121afceaebcc77964b4b94517a

## write

Cardの書き込みには鍵が必要
- 認証機関から払い出される鍵がいる
- 趣味の範囲ではもらえないのでは
AIDも必要
- ISOでかんりされてるRIDが必要
- 趣味の範囲ではもらえないのでは

## どこで買えるの？

AmazonやAliで売ってる
仕事であれば

## どうテストするの？

バイトコードでコンパイルする部分は普通のJDK
ふつうにJavaでテスとを書く

# Q & A

- SIMカードはどう動いているか
  - 個体認識や何らかのパケットのペイロードを持っていたり新しいプログラムをダウンロードさせるための処理
  - 認証系のシステムが動いていることが多いのでは
- エミュレータとかはないか
  - あるが、フリーではない
    - windowsでしか動かない
- githubとかでコードを見たときそれがJavaCardのものかどうか見分けられる方法
  - `import javacard.framework`
- 別会社のSIMが入るような時はどう処理されるか
  - CLAチェック時にinvalidであると認識される
- 
