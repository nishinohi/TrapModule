# ハードウェア
狩猟用罠検知モジュール
## 概要
携帯電波網からインターネットに接続して罠の検知情報を通知するシステム。  
wifi規格のローカルメッシュネットワークを構築して同時に広範囲、複数の狩猟用罠の作動状況を監視する。  
## 注意事項
モジュールに配線する場合、回路図の通りに配線した後、モジュールやその他マイコン等を接続する前に必ずテスター等で入力電圧を測り、想定している通りの電圧が供給されているか確認しましょう。  
守らなければ死ぞ。
## 環境構築
環境構築は下記のリンクを参照。  
[ESP-WROOM-02を買ったので、環境構築をメモしておく](https://tpedia.tech.gr.jp/20160111434/)  
**■ 注意**  
ESP-WROOM-02のボードは2.3.0のものをインストール使用すること。2.4.0は後述するeasymeshライブラリが非対応のため。


■ 必須のもの
* [ブレッドボード](http://akizukidenshi.com/catalog/g/gP-00315/) - 回路を組むためのもの
* [ESP-WROOM-02](http://akizukidenshi.com/catalog/g/gK-09758/) - 子機同士の通信と罠検知
* [USBシリアル変換モジュール](http://akizukidenshi.com/catalog/g/gK-01977/) - 
ESPモジュールにデータを書き込むのに必要
* [3.3Vレギュレーター](http://akizukidenshi.com/catalog/g/gI-09261/) - 3.3Vならなんでもいい  
* [10kΩ * 5](http://akizukidenshi.com/catalog/g/gR-25103/) - 
プルアップ、プルダウン用
* コンデンサ - 適当な電気容量のもの(0.1μF、47μF一本づつくらい)

■ あると便利なもの
* [短めジャンパワイヤ](https://www.amazon.co.jp/gp/product/B00J3E1206/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1) - 短いのあると便利
* [1.5kΩ * 1、7.5kΩ * 1] - バッテリ残量確認のための分圧用抵抗  
（1:5の抵抗比であればよい。ただし、合計抵抗値が5kΩ以上はあったほうがいい）
* [LED](http://akizukidenshi.com/catalog/g/gI-00562/) - 動作確認用LED（取り敢えず買っ
とけ）
* [200Ω](http://akizukidenshi.com/catalog/g/gR-25201/) - LED用抵抗（取り敢えず買っ
とけ）
* [単4電池](https://www.amazon.co.jp/Amazon%E3%83%99%E3%83%BC%E3%82%B7%E3%83%83%E3%82%AF-AmazonBasics-HR-4UTG-AMZN-%E5%85%85%E9%9B%BB%E5%BC%8F%E3%83%8B%E3%83%83%E3%82%B1%E3%83%AB%E6%B0%B4%E7%B4%A0%E9%9B%BB%E6%B1%A0-%E6%9C%80%E5%B0%8F%E5%AE%B9%E9%87%8F750mAh%E3%80%81%E7%B4%841000%E5%9B%9E%E4%BD%BF%E7%94%A8%E5%8F%AF%E8%83%BD/dp/B00CWNMXQW/ref=sr_1_2?s=electronics&ie=UTF8&qid=1497778153&sr=1-2) - モジュールはニカド電池で動かす想定（リチウム電池は使用しない）
* [電池ボックス](http://akizukidenshi.com/catalog/g/gP-03087/) - バッテリーで駆動する場合必要

■ 利用ライブラリ  
* [easyMesh](https://github.com/Coopdis/easyMesh) - メッシュネットワークI/Fのラッパ（機能修正予定）
* [simpleList](https://github.com/blackhack/ArduLibraries) - easyMeshで使用  
Relaseフォルダ配下のものはなぜかエラーがでるので、ルートディレクトリのSimpleListという名前のフォルダをダウンロードし、zip化してArduino IDEのライブラリマネージャーから追加する。
* [ArduinoJson](https://github.com/bblanchon/ArduinoJson) - easyMeshで使用
## モジュール機能
**■ 基本機能**
* **携帯電波網接続機能**  
親モジュールのみの機能。罠の検知情報をSORACOM Beamを用いて閉域網のMQTTサーバーからTLS1.2でAWS IoTに送信する。
* **メッシュネットワーク**  
モジュールでメッシュネットワークを作成し、直接ネットワークで接続されていないモジュールに対しても、検知情報の送受信をおこなえる。
* **罠検知**  
GPIO14の入力がHIGHになった場合、検知情報を送信する。
* **モジュール設定確認・変更**  
子機のwifiに接続し、子機のIPにブラウザで接続することで現在の設定値の確認、変更が可能。

**■ 回路図**  
回路図(TrapModule/schematic/childModule.fzz)を参照。  

**■ バッテリ残量チェックについて**  
分圧用の抵抗が無い場合は「#define BATTERY_CHECK」をコメントアウトし、バッテリ残量チェックを無効化すること。ただし、バッテリ残量チェックを無効化した状態でバッテリでモジュールを動かすと、放電終止電圧を超えても動作し続けるため、稼働したまま放置すると電池が壊れるので注意。開発等でPCとUSBで接続して使うなど、バッテリ以外で起動させる場合には機能をOFFにしといたほうがいい。