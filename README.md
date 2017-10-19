# dotmatrix_64x48
Portal for WiFi NTP Dot Matrix Clock (as known as MazoClock3)

ここはマゾ時計3のポータルです。

# 傘下プロジェクト
 * Hardware 3.0 series - [https://github.com/w-dee/dotmatrix_64x48_hw_3.0](https://github.com/w-dee/dotmatrix_64x48_hw_3.0)
 * Hardware 3.5 series - Abandoned
 * Firmware (for 3.0 series) - [https://github.com/w-dee/dotmatrix_64x48_fw](https://github.com/w-dee/dotmatrix_64x48_fw)

# News

WiFiの認証に関わる一連の脆弱性(CRACK)を修正するファームウェアを公開しました。https://github.com/w-dee/dotmatrix_64x48/tree/master/firmwares/20171019 より combined_rom.bin をダウンロードし、マゾ時計のWeb画面のFirmware upgradeにこのファイルを指定して"Update"ボタンを押してください。アップデートには1分ほどかかります。その間ブラウザは画面遷移しないまま応答がないかのようになってしまいますが、何もせずに待ち続けてください。

# HW production-rev.1 情報

コミックマーケット92 (2017 夏コミ) 配布バージョンに対する情報です

## サイト構造が変わりました

異なる複数のハードウェアリビジョンや、それらに共通するファームウェアを管理するために、プロジェクトの構造を変えました。詳しくは上の「傘下プロジェクト」を参照してください。

 * それに伴い、回路図はこちらに移動してあります - [https://github.com/w-dee/dotmatrix_64x48_hw_3.0/raw/production-rev1/hardware/dotmatrix_64x48.pdf](https://github.com/w-dee/dotmatrix_64x48_hw_3.0/raw/production-rev1/hardware/dotmatrix_64x48.pdf)
 * その他の回路図データー、ガーバーなどはこちらへ [https://github.com/w-dee/dotmatrix_64x48_hw_3.0/tree/production-rev1/hardware](https://github.com/w-dee/dotmatrix_64x48_hw_3.0/tree/production-rev1/hardware)


## 重大なプリント基板のミスについて

プリント基板の製造データーのミスにより変なところでパターンがショートしています。下の画像の赤線のところをカッターなどでカットしてください。周りの配線を傷つけないように慎重におねがいします。
<img src="Screenshot from 2017-08-16 11-36-40.jpg" alt="HW3.0 producton-rev.1 errata-1">
<img src="Screenshot from 2017-08-16 11-37-07.png" alt="HW3.0 producton-rev.1 errata-2">

## プライバシーセパレーターについて

WiFi APに接続されている機材同士が直接通信できないようにする仕組みを導入しているWiFi APが存在します。
たとえば

 * Buffalo - [無線パソコン同士の通信を禁止する（プライバシーセパレータ）](http://buffalo.jp/download/manual/html/air851/router/whrg54s/chapter11.html)

このようなAPだと、マゾ時計上のWebインターフェースに、他のPCなどからアクセスできないことがありますので、この機能を切ってご使用ください。

## ファームウェアのシリアル経由での書き込みについて

ファームウェアをどうしてもシリアル経由で(つまり、ウェブブラウザーからのアップロードではなく)行わなければならない場合の手順です。

### ファームウェアについて

ファームウェアは以下の３つのファイルで構成されます
 * firmware.bin - プログラム
 * FS.spiffs - メインファイルシステム
 * settings_fs.spiffs - 設定情報ファイルシステム

これらをダウンロードしてください。

ファイルは [https://github.com/w-dee/dotmatrix_64x48/tree/master/firmwares](https://github.com/w-dee/dotmatrix_64x48/tree/master/firmwares) とかにおいてあります。

ちなみに、ウェブブラウザーからアップロードできる形式のファームウェアのバイナリーは、上記の firmware.bin と FS.spiffs を特殊な方式でつなぎあわせたもので、combined_rom.bin というファイル名になっています。これはウェブブラウザーからのアップロード専用の形式で、ここで説明するシリアル経由での書き込みでは使えません。

### 書き込み用プログラムについて

ESP8266 Arduino core とともに付属している esptool が必要です。**esptoolで検索すると Python で記述された esptool.py というのもみつかりますが、それはちがうやつです。**

[https://github.com/esp8266/Arduino/blob/master/package/package_esp8266com_index.template.json](https://github.com/esp8266/Arduino/blob/master/package/package_esp8266com_index.template.json) をみると、packages.platforms.tools の esptool のところに各プラットフォームのバイナリーが列挙されていますので、ダウンロードして展開してください。ここに乗ってないプラットフォームの場合は多分ソースからコンパイルすればつかえるとおもいますが割愛します。

### 書き込み

本体と付属のUSB-シリアル変換基板、それとPCを接続してください。CP2104(USB-シリアル変換チップ)のドライバーはOSが認識しないようならばどこかから探してきてください。

以下の3つのコマンドで書き込んでください。

 1. `"/workspace/esp8266/Arduino/tools/esptool/esptool" -v -cd nodemcu -cb 921600 -cp "/dev/ttyUSB0" -ca 0x00000 -cf "firmware.bin"`
 1. `"/workspace/esp8266/Arduino/tools/esptool/esptool" -v -cd nodemcu -cb 921600 -cp "/dev/ttyUSB0" -ca 0x100000 -cf "FS.spiffs"`
 1. `"/workspace/esp8266/Arduino/tools/esptool/esptool" -v -cd nodemcu -cb 921600 -cp "/dev/ttyUSB0" -ca 0x3c0000 -cf "settings_fs.spiffs"`

ただし、

 * "/workspace/esp8266/Arduino/tools/esptool/esptool" はesptoolを配置した場所に書き換えて打ち込んでください。
 * "/dev/ttyUSB0" はマゾ時計が接続されているシリアルデバイスを示します。
 * 最後のコマンドは設定用ファイルシステムを初期化してしまいます。設定を初期化したくない場合は最後のコマンドはスキップしてください。
 * 各コマンドは一回実行すると最高で３回まで本体をリセットして書き込みを試行します。まれに、3回のリトライのあと失敗することがあります。もう一度コマンドを実行してみてください。
 * 何度実行しても3回のリトライのあとに失敗する場合は、リセット周りの回路の不具合の可能性があります。どうしても不具合が治らなければ、本体基板の裏の 「To enter bootloader manually」の手順を踏む必要があるかもしれません。

正常に実行できた場合のコマンドラインの表示の例を示します。

    "/workspace/esp8266/Arduino/tools/esptool/esptool" -v -cd nodemcu -cb 921600 -cp "/dev/ttyUSB0" -ca 0x00000 -cf "/tmp/mkESP/firmware_generic/firmware.bin"
    esptool v0.4.9 - (c) 2014 Ch. Klippel <ck@atelier-klippel.de>
    opening port /dev/ttyUSB0 at 921600
    opening bootloader
    resetting board
    trying to connect
    trying to connect
    trying to connect
    resetting board
    trying to connect
    trying to connect
    Uploading 408208 bytes from /tmp/mkESP/firmware_generic/firmware.bin to flash at 0x00000000
    ................................................................................ [ 20% ]
    ................................................................................ [ 40% ]
    ................................................................................ [ 60% ]
    ................................................................................ [ 80% ]
    ...............................................................................  [ 100% ]
    starting app without reboot
    closing bootloader

