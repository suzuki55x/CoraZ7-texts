# Cora Z7 Zynqベアメタル動作確認手順

[TOC]

## 今回やること

CoraZ7を使って、Zynqのサンプルを作成します。
今回は、PS部のUARTから、"Hello, World"を出力します。

基本的には、[こちら](https://qiita.com/iwatake2222/items/24a8a94741fdbb80f62a)の手順を参考に、CoraZ7とVitis2020.1に合わせて手順を変更しています。

## 環境構築

必要なものを以下に示します。

- CoraZ7

- Vivado

- Vitis

- FAT32形式でフォーマットされたSDカード

  > SDカードの読み書き環境も必要です。

- Teratermなどのシリアルコンソール

今回は、Vivado2020.1 と Vitis2020.1を使用します。
Vivadoのインストールは、2019.1と同様です。

※非常に大きなファイルのダウンロードを伴いますので、高速なインターネット環境で行うことを推奨します。

### インストーラのダウンロード

1. [Vivado ダウンロードページ](https://japan.xilinx.com/support/download.html)に移動します。
2. "Vivado Design Suite - HLx Edition - 2020.1 Full Product Installation"の、[ザイリンクス統合インストーラー 2020.1: Windows 用自己解凍型ウェブ インストーラー](https://japan.xilinx.com/member/forms/download/xef.html?filename=Xilinx_Unified_2020.1_0602_1208_Win64.exe) をダウンロードします。
   ※Xilinxアカウントでのログイン・会社情報の入力が必要です。

### Vivadoのインストール

1. ダウンロードしたインストーラを起動します。
2. Xilinxアカウントの入力と、ライセンスアグリーメントの確認を行います。
3. インストールソフトウェアの選択では、Vivadoを選択します。
4. Vivado HL Webpackを選択
5. 画面の指示に従ってインストール（特に設定変更の必要はありません）

### ボードファイルの追加

VivadoでCoraZ7を表示するための設定を行います。

1. [こちらのページ](https://reference.digilentinc.com/vivado/installing-vivado/start)の3.1節に示されているリンクから`vivado-boards-master.zip`をダウンロード
2. 解凍し、`vivado-boards-master¥new¥board_files`以下のファイルを全てコピー
3. Vivadoインストール場所のboard_files内に貼り付けます。
   例：`C:¥Xilinx¥Vivado¥2020.1¥data¥boards¥board_files¥`

### Vitisのインストール

1. Windowsツールバーから、Xilinx Infomation Centerを開きます。
   ※起動していない場合、スタートメニュー->Xilinxから起動します。
2. Manage Installsタブをクリック
3. `Xilinx Design Tools Vitis Unified Software Platform 2020.1`欄の`Add Tools/Devices`をクリック
4. Xilinxアカウントを入力します。
5. "Vitis"を選択します。
6. 画面の指示に従ってインストール（特に設定変更の必要はありません）

`Vivado 2020.1`と`Xilinx Vitis 2020.1`が起動できれば完了です。

## Vivadoにてデザイン作成

今回のメインはVitisを使ったPS側なので、最低限のデザインのみ作成します。

### プロジェクト作成

1. Vivado 2020.1を起動
   起動時に以下のようなエラーが出る場合、無視して結構です。
2. 新規でプロジェクトを作るので、`Create Project`をクリック
3. 最初のページでは何もせずNextをクリック
4. プロジェクト名、保存ディレクトリを指定してNextをクリック

   > 任意の名前・場所で構いませんが、後ほど参照するため、わかりやすいものにしてください。
5. "RTL Project"を選択してNextをクリック
6. "Add Sources", "Add Constraints"では何もせずNextをクリック
7. "Default Part"では、まず"Boads"タブをクリックし、"CoraZ7-10"を選択しNext。
   ※検索ボックスを使うと見つけやすいです。
8. 以下の画面にて、Finishをクリック

### ブロックデザイン作成

1. Create Block Designをクリック
2. 特に何も変更せずOKをクリック
3. ＋ボタンをクリック
4. "ZYNQ7 Processing system"をダブルクリック
5. FCLK_CLK0をM_AXI_GP0_ACLKに接続（ピンをドラッグすれば繋がります）
   M_AXI_GP0_ACLKは、他のIPと接続するときに使うクロック。
   今回は使わないので、PSから出ている50MHzをそのまま入力とします。
6. PS上で右クリック->Validate designで、エラーが出ていないことを確認

### デザイン完成

1. Design Sourcesのデザイン名で右クリック、Generate Output Products -> Generate
2. 同じく、Design Sourcesのデザイン名で右クリックし、Create HDL Wrapper -> OK
3. デザインの上位にwrapperができていることを確認。

### ハードウェアのエクスポート

1. Generate Bitstreamをクリック
   論理合成・配置配線を行い、bitstreamデータを生成します。
   ※ ここで出力した.bitファイルはVitisにて使用します。
2. File -> Export -> Export Hardware
3. "Platform Type"は"Fixed"を指定してNext
4. "Output"は、"include bitstream"を指定してNext
5. 出力ファイル名と、出力先を指定してNext
   ※ ここで出力したXSAファイルはVitisにて使用します。
6. 内容を確認して、Finish

### Vitis起動

Vitisは、Vivado上から起動できます。

1. Vivadoのメニューバーより、Tools -> Launch Vitis をクリックします。
2. workspaceの作成を行います。任意の場所に作成してください。

   > ここで指定した場所に出力ファイルが生成されるので、わかりやすい場所にしてください。
3. Vitisのスタートページが表示されます。

### 出力ファイル確認

以下の2ファイルができていることを確認しておきます。
以下のファイルは、Vitisにて使用します。

- bitファイル：FPGAのデザイン情報が入ったファイル
  
  生成したプロジェクト内、runsディレクトリの中のimplies_1内に生成されます。
  例：`C:¥work¥vivado¥project_1¥project_1.runs¥impl_1¥design_1_wrapper.bit`
- XSAファイル：Vitis用のハードウェア定義ファイル
  
  作成したプロジェクト直下に生成されます。
  例：`C:¥work¥vivado¥project_1¥design_1_wrapper.xsa`

## Vitisにてアプリケーション作成

Vivadoにてハードウェアのデザインを作成しました。
Vitisでは、ソフトウェアを作成していきます。

### プロジェクト作成、ビルド

1. "Create Application Project"をクリックします。
2. 任意のプロジェクト名を入力します。
3. "Platform"では、Vivadoにて出力したXSAファイルを選択します。
4. "Domain"では、何も変更せずNextをクリックします。
5. "template"では、サンプルファイルが選択できます。
   今回は、「Hello, World」のサンプルを選択して、そのまま使用します。
6. Explolerウィンドウのプロジェクト名を右クリックして、"Build Project"をクリックしてビルドできます。

### 出力ファイル確認

以下のファイルができていることを確認します。

- elfファイル：vitisで作成したプログラムファイル

  作成したプロジェクト直下の`Debug`ディレクトリ内に生成されます。
  例：`C:¥work¥vitis¥project_1¥Debug¥hello.elf`

### JTAG経由で動作確認

まずは、JTAGブートにて動作確認してみます。

> CoraZ7は、JTAG経由のブートとSDカードからの起動が選択できます。
> JP2がOFF(外れている状態)でJTAGモード、ONでSDカードモードです。

1. CoraZ7のJP2を外します。

2. CoraZ7をPCに接続します。

3. Teratermを起動し、CoraZ7が接続されているCOMポートを選択します。

   > USB Serial Portと表示されているポートです。
   
4. Teratermのメニューバーより、設定 -> シリアルポートをクリックし、スピードを115200に設定します。

5. Vitisのメニューバーから、Xilinx -> Program FPGAを選択し、そのままProgramをクリックします。

6. Explolerウィンドウのプロジェクト名を右クリックし、Run As -> Launch on Hardwareをクリックします。

7. TeratermにHello, worldが表示されていれば、成功です。

## SDカードからの起動

今度は、SDカードからの起動を試してみます。
SDカードのブートイメージには、以下が含まれます。

- FSBL(First Stage Boot Loader)：起動のためのファイルです。本項で作成します。
- FPGAのデザイン情報：Vivadoにて作成した.bitファイルです。
- アプリケーション：Vitisにて作成した.elfファイルです。

### SDカードブートのため、FSBL作成

まずは、VitisにてFSBLを作成します。

1. Hello, Worldのプロジェクトを開いたまま、Vitisメニューバーから File -> New -> Application Projectを選択します。
2. プロジェクト名は、わかりやすく「Hello,Worldのプロジェクト名_fsbl」としておきます。
3. "Templates"にて、**Zynq FSBL**を選択して、Finishをクリックします。
4. 内容はそのまま使用できるので、そのままBuild Projectを行います。
5. 生成された.elfファイルの場所を確認しておいてください。

### ブートイメージ作成

次は、今まで作成してきたファイルを集めて、SDカードに実際に書き込むイメージを作成します。
引き続きVitisで作業します。

1. Explolerウィンドウにて、Hello,Worldプロジェクトをクリックして選択しておきます。

2. Vitisメニューバーから、Xilinx -> Create Boot Image を選択します。

3. "Output Path"には、プロジェクト直下などわかりやすい場所を指定します。

   > ここにSDカードに書き込むイメージが生成されます。

4. "Boot Image Partision"にて、"Add"をクリックし、以下のイメージを追加します。

   > 順番が大切です。必ず以下の順番で追加してください。

   1. FSBLの.elfファイル
   2. Vivadoにて生成した.bitファイル
   3. Vitisにて生成したHello,Worldの.elfファイル

5. 3つのファイルを順番通り追加したら、"Create Image"をクリックします。

6. 指定した場所に`BOOT.BIN`ができているか確認してください。

### SDカード起動にて動作確認

1. FAT32形式でフォーマットされたSDカードにBOOT.BINをコピーします。

2. CoraZ7の電源を切り、SDカードをCoraZ7に挿入します。

3. CoraZ7のJP2をON(取り付けた状態)にして、SDカードモードにします。

4. CoraZ7をPCに接続します。

5. Teratermを起動し、CoraZ7が接続されているCOMポートを選択します。

   > USB Serial Portと表示されているポートです。
   >
   > CoraZ7を繋いでから認識されるまで30秒ほどかかることもあります。

6. Teratermのメニューバーより、設定 -> シリアルポートをクリックし、スピードを115200に設定します。

7. 一度リセットするため、CoraZ7のSRSTボタンを押します。

8. TeratermにHello, worldが表示されていれば、成功です。