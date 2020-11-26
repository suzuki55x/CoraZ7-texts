# Cora Z7 Zynqベアメタル動作確認手順

[TOC]

## 今回の目的

CoraZ7を使って、Zynqのサンプルを作成します。
今回は、PS部のUARTから、"Hello, World"を出力します。

<div style="page-break-after:always"></div>

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

<div style="page-break-after:always"></div>

### インストーラのダウンロード

1. [Vivado ダウンロードページ](https://japan.xilinx.com/support/download.html)に移動します。
   <img src="./images/0_Vivado_install_1.PNG"  />
2. "Vivado Design Suite - HLx Edition - 2020.1 Full Product Installation"の、[ザイリンクス統合インストーラー 2020.1: Windows 用自己解凍型ウェブ インストーラー](https://japan.xilinx.com/member/forms/download/xef.html?filename=Xilinx_Unified_2020.1_0602_1208_Win64.exe) をダウンロードします。
   ※Xilinxアカウントでのログイン・会社情報の入力が必要です。
   <img src="./images/0_Vivado_install_2.PNG"  />

<div style="page-break-after:always"></div>

### Vivadoのインストール

1. ダウンロードしたインストーラを起動します。

2. Xilinxアカウントの入力と、ライセンスアグリーメントの確認を行います。
   <img src="./images/0_Vivado_install_5.PNG"/>  
   
<img src="./images/0_Vivado_install_6.PNG"/>
   
3. インストールソフトウェアの選択では、Vivadoを選択します。
   <img src="./images/0_Vivado_install_7.PNG"/>

4. Vivado HL Webpackを選択します。
   <img src="./images/0_Vivado_install_8.PNG"/>

5. 画面の指示に従ってインストール（特に設定変更の必要はありません）

   > 30~40GBほどのダウンロードが走ります。

   

   <img src="./images/0_Vivado_install_9.PNG"/>

<div style="page-break-after:always"></div>

### ボードファイルの追加

VivadoでCoraZ7を表示するための設定を行います。

1. [こちらのページ](https://reference.digilentinc.com/vivado/installing-vivado/start)の3.1節に示されているリンクから`vivado-boards-master.zip`をダウンロード
   <img src="./images/0_install_boards.PNG"/>
2. 解凍し、`vivado-boards-master¥new¥board_files`以下のファイルを全てコピー
3. Vivadoインストール場所のboard_files内に貼り付けます。
   例：`C:¥Xilinx¥Vivado¥2020.1¥data¥boards¥board_files¥`

<div style="page-break-after:always"></div>

### Vitisのインストール

1. Windowsツールバーから、Xilinx Infomation Centerを開きます。
   ※起動していない場合、スタートメニュー->Xilinxから起動します。  
   <img src="./images/0_Vitis_install_0.PNG"/>
2. Manage Installsタブをクリック  
3. "Xilinx Design Tools Vitis Unified Software Platform 2020.1"欄の"Add Tools/Devices"をクリック  
   <img src="./images/0_Vitis_install_1.PNG"/>
4. Xilinxアカウントを入力します。  
   <img src="./images/0_Vitis_install_2.PNG"/>
5. "Vitis"を選択します。
6. 画面の指示に従ってインストール（特に設定変更の必要はありません）

   > 20GBほどのダウンロードが走ります。

`Vivado 2020.1`と`Xilinx Vitis 2020.1`が起動できれば完了です。

<div style="page-break-after:always"></div>

## Vivadoにてデザイン作成

今回のメインはVitisを使ったPS側なので、最低限のデザインのみ作成します。

### プロジェクト作成

1. Vivado 2020.1を起動
   起動時に以下のようなエラーが出る場合、無視して結構です。
   <img src="./images/1_error.PNG"  />
   
2. 新規でプロジェクトを作るので、`Create Project`をクリック
   <img src="./images/2_start_page.PNG"  />
   
3. 最初のページでは何もせずNextをクリック
   <img src="./images/3_new_prj_1.PNG"  />
   
4. プロジェクト名、保存ディレクトリを指定してNextをクリック

   > 任意の名前・場所で構いませんが、後ほど参照するため、わかりやすいものにしてください。

   <img src="./images/3_new_prj_2.PNG"  />
   
5. "RTL Project"を選択してNextをクリック
   <img src="./images/3_new_prj_3.PNG"  />
   
6. "Add Sources", "Add Constraints"では何もせずNextをクリック

7. "Default Part"では、まず"Boads"タブをクリックし、"CoraZ7-10"を選択しNext。

   > 検索ボックスを使うと見つけやすいです。

   <img src="./images/3_new_prj_4.PNG"  />
   
8. Finishをクリック

   

   <div style="page-break-after:always"></div>

### ブロックデザイン作成

1. Create Block Designをクリック
   <img src="./images/4_create_block_1.PNG"  />

2. 特に何も変更せずOKをクリック
   <img src="./images/4_create_block_2.PNG"  />

3. ＋ボタンをクリック
   <img src="./images/4_create_block_3.PNG"  />

4. "ZYNQ7 Processing system"をダブルクリック
   <img src="./images/4_create_block_4.PNG"  />

5. 画面上に"ZYNQ7 Processing system"が配置されます。

6. 上部の"Run Block Automation"をクリックします。
   <img src="./images/4_create_block_5.PNG"  />

7. 表示されたウィンドウにて、「Apply Board Preset」にチェックされていることを確認して、OKを押します。
   <img src="./images/4_create_block_5_5.PNG"  />

8. FCLK_CLK0をM_AXI_GP0_ACLKに接続（ピンをドラッグすれば繋がります）

   > M_AXI_GP0_ACLKは、他のIPと接続するときに使うクロック。
   > 今回は使わないので、PSから出ている50MHz(FCLK_CLK0)をそのまま入力とします。

   <img src="./images/4_create_block_5_7.PNG"  />

9. PS上で右クリック->Validate designで、エラーが出ていないことを確認します。
   <img src="./images/4_create_block_6.PNG"  />

10. PS上でダブルクリックしてコンフィグを開き、"PS-PL Configration"からUART0のボーレートを確認しておきます。
      <img src="./images/4_create_block_7.PNG"  />

<div style="page-break-after:always"></div>

### デザイン完成

1. Design Sourcesのデザイン名で右クリック、Generate Output Products -> Generateをクリックします。
   <img src="./images/5_HW_1.PNG"  />
2. 同じく、Design Sourcesのデザイン名で右クリックし、Create HDL Wrapper -> OK
   <img src="./images/5_HW_2.PNG"  />
3. デザインの上位にwrapperができていることを確認します。
   <img src="./images/5_HW_3.PNG"  />

<div style="page-break-after:always"></div>

### ハードウェアのエクスポート

1. Generate Bitstreamをクリック
   論理合成・配置配線を行い、bitstreamデータを生成します。
   ※ ここで出力した.bitファイルはVitisにて使用します。
   <img src="./images/6_gen_0.PNG"  />
   <img src="./images/6_gen_1.PNG"  />
   
2. 完了後に表示されるBitstream Generation Completedウィンドウでは、結果レポートの表示などが選択できます。
   今回は不要なので、Cancelをクリックします。
   <img src="./images/6_gen_1_5.PNG"  />
   
3. File -> Export -> Export Hardware
   <img src="./images/7_export_0.PNG"  />
   
4. "Platform Type"は"Fixed"を指定してNext
   <img src="./images/7_export_1.PNG"  />
   
5. "Output"は、"include bitstream"を指定してNext
   <img src="./images/7_export_2.PNG"  />
   
6. 出力ファイル名と、出力先を指定してNext
   ※ ここで出力したXSAファイルはVitisにて使用します。
   <img src="./images/7_export_3.PNG"  />
   
7. 内容を確認して、Finish

   <div style="page-break-after:always"></div>

### Vitis起動

Vitisは、Vivado上から起動できます。

1. Vivadoのメニューバーより、Tools -> Launch Vitis をクリックします。
   <img src="./images/8_Launch_vitis_0.PNG"  />
2. workspaceの作成を行います。任意の場所に作成してください。

   > ここで指定した場所に出力ファイルが生成されるので、わかりやすい場所にしてください。

   <img src="./images/8_Launch_vitis_1.PNG"  />
3. Vitisのスタートページが表示されます。
   <img src="./images/8_Launch_vitis_2.PNG"  />

<div style="page-break-after:always"></div>

### vivado出力ファイル確認

以下の2ファイルができていることを確認しておきます。
以下のファイルは、Vitisにて使用します。

- bitファイル：FPGAのデザイン情報が入ったファイル
  
  生成したプロジェクト内、runsディレクトリの中のimplies_1内に生成されます。
  例：`C:¥work¥vivado¥project_1¥project_1.runs¥impl_1¥design_1_wrapper.bit`

   <img src="./images/6_gen_2.PNG"  />
- XSAファイル：Vitis用のハードウェア定義ファイル
  
  作成したプロジェクト直下に生成されます。
  例：`C:¥work¥vivado¥project_1¥design_1_wrapper.xsa`

   <img src="./images/7_export_4.PNG"  />

<div style="page-break-after:always"></div>

## Vitisにてアプリケーション作成

Vivadoにてハードウェアのデザインを作成しました。
Vitisでは、ソフトウェアを作成していきます。

### プロジェクト作成、ビルド

1. "Create Application Project"をクリックします。
2. "Platform"では、"Create a new platform from hardware(XSA)"タブをクリックして切り替えます。
   <img src="./images/8_Launch_vitis_4.PNG"  />
3. "Hardware specification"欄の"Browse"ボタンから、Vivadoにて出力したXSAファイルを選択してNextをクリックします。
   <img src="./images/8_Launch_vitis_5.PNG"  />
   <img src="./images/8_Launch_vitis_6.PNG"  />
   <img src="./images/8_Launch_vitis_7.PNG"  />
4. "Application Project Details"では、任意のプロジェクト名を入力して、Nextをクリックします。
   <img src="./images/8_Launch_vitis_8.PNG"  />
5. "Domain"では、何も変更せずNextをクリックします。
   <img src="./images/8_Launch_vitis_9.PNG"  />
6. "Templates"では、サンプルファイルが選択できます。
   今回は、「Hello, World」のサンプルを選択して、そのまま使用します。
   <img src="./images/8_Launch_vitis_10.PNG"  />
7. 以下の様な画面になれば、プロジェクト作成完了です。
   <img src="./images/9_vitis_1.PNG"  />
8. 今回は、サンプルファイルを変更せずそのまま使用します。

   > ソースコードは、プロジェクト内の`src`ディレクトリに入っています。

   Explolerウィンドウのプロジェクト名を右クリックして、"Build Project"をクリックしてビルドします。
   <img src="./images/9_vitis_2.PNG"  />

<div style="page-break-after:always"></div>

### vitis出力ファイル確認

以下のファイルができていることを確認します。

- elfファイル：vitisで作成したプログラムファイル

  作成したプロジェクト直下の`Debug`ディレクトリ内に生成されます。
  例：`C:¥work¥vitis¥project_1_hello¥Debug¥project_1_hello.elf`

   <img src="./images/9_vitis_3.PNG"  />

<div style="page-break-after:always"></div>

### JTAG経由で動作確認

まずは、JTAGブートにて動作確認してみます。

> CoraZ7は、JTAG経由のブートとSDカードからの起動が選択できます。
> JP2がOFF(外れている状態)でJTAGモード、ONでSDカードモードです。

1. CoraZ7のJP2を外します。
   <img src="./images/cora_jtag.jpg"  />

2. CoraZ7をPCに接続します。

3. Teratermを起動し、CoraZ7が接続されているCOMポートを選択します。

   > USB Serial Portと表示されているポートです。

   <img src="./images/10_jtag_1.PNG"  />

4. Teratermのメニューバーより、設定 -> シリアルポートをクリックし、スピードを115200に設定します。

   <img src="./images/10_jtag_2.PNG"  />
   <img src="./images/10_jtag_3.PNG"  />

5. Vitisのメニューバーから、Xilinx -> Program FPGAを選択し、そのままProgramをクリックします。

   <img src="./images/10_jtag_4.PNG"  />
   <img src="./images/10_jtag_5.PNG"  />

6. Explolerウィンドウのプロジェクト名を右クリックし、Run As -> Launch on Hardwareをクリックします。
   <img src="./images/10_jtag_6.PNG"  />

7. TeratermにHello, worldが表示されていれば、成功です。
   <img src="./images/10_jtag_7.PNG"  />

<div style="page-break-after:always"></div>

## SDカードからの起動

今度は、SDカードからの起動を試してみます。
SDカードのブートイメージには、以下が含まれます。

- FSBL(First Stage Boot Loader)：起動のためのファイルです。本項で作成します。
- FPGAのデザイン情報：Vivadoにて作成した.bitファイルです。
- アプリケーション：Vitisにて作成した.elfファイルです。

### SDカードブートのため、FSBL作成

まずは、VitisにてFSBLを作成します。

1. Hello, Worldのプロジェクトを開いたまま、Vitisメニューバーから File -> New -> Application Projectを選択します。
   <img src="./images/11_fsbl_1.PNG"  />
2. Platformは、アプリケーションと同じ様にXSAファイルを指定します。
   <img src="./images/11_fsbl_2.PNG"  />
3. プロジェクト名は、わかりやすく「Hello,Worldのプロジェクト名_fsbl」としておきます。
   <img src="./images/11_fsbl_3.PNG"  />
4. "Templates"にて、**Zynq FSBL**を選択して、Finishをクリックします。
   <img src="./images/11_fsbl_4.PNG"  />
5. 内容はそのまま使用できるので、そのままBuild Projectを行います。
   <img src="./images/11_fsbl_5.PNG"  />
6. 生成された.elfファイルの場所を確認しておいてください。
   <img src="./images/11_fsbl_6.PNG"  />

<div style="page-break-after:always"></div>

### ブートイメージ作成

次は、今まで作成してきたファイルを集めて、SDカードに実際に書き込むイメージを作成します。
引き続きVitisで作業します。

1. Explolerウィンドウにて、Hello,Worldプロジェクトをクリックして選択しておきます。
   <img src="./images/12_boot_1.PNG"  />

2. Vitisメニューバーから、Xilinx -> Create Boot Image を選択します。
   <img src="./images/12_boot_2.PNG"  />

3. "Output Path"には、プロジェクト直下などわかりやすい場所を指定します。

   > ここにSDカードに書き込むイメージが生成されます。


4. "Boot Image Partision"にて、"Add"をクリックし、以下のイメージを追加します。

   > 順番が大切です。必ず以下の順番で追加してください。

   1. FSBLの.elfファイル
   2. Vivadoにて生成した.bitファイル
   3. Vitisにて生成したHello,Worldの.elfファイル

5. 3つのファイルを順番通り追加したら、"Create Image"をクリックします。
   <img src="./images/12_boot_3.PNG"  />

6. 指定した場所に`BOOT.BIN`ができているか確認してください。
   <img src="./images/12_boot_4.PNG"  />

<div style="page-break-after:always"></div>

### SDカード起動にて動作確認

1. FAT32形式でフォーマットされたSDカードにBOOT.BINをコピーします。
   <img src="./images/12_boot_5.PNG"  />

2. CoraZ7の電源を切り、SDカードをCoraZ7に挿入します。

3. CoraZ7のJP2をON(取り付けた状態)にして、SDカードモードにします。
   <img src="./images/cora_sd.jpg"  />

4. CoraZ7をPCに接続します。

5. Teratermを起動し、CoraZ7が接続されているCOMポートを選択します。

   > USB Serial Portと表示されているポートです。
   >
   > CoraZ7を繋いでから認識されるまで30秒ほどかかることもあります。

6. Teratermのメニューバーより、設定 -> シリアルポートをクリックし、スピードを115200に設定します。

7. 一度リセットするため、CoraZ7のSRSTボタンを押します。
   <img src="./images/cora_srst.jpg"  />

8. TeratermにHello, worldが表示されていれば、成功です。
   <img src="./images/12_boot_6.PNG"  />
