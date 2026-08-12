# ACI Pre-Upgrade Validator

[English](README.md) | 日本語

## 「ACI-Pre-Upgrade-Validation-Script」の問題点

[Cisco ACI](https://www.cisco.com/site/jp/ja/products/networking/cloud-networking/application-centric-infrastructure/index.html)をバージョンアップする際、事前に以下のスクリプトを実行し、アップグレード前に[チェック項目](https://datacenter.github.io/ACI-Pre-Upgrade-Validation-Script/)を確認することが出来ます。検出された項目を事前に確認することで、アップグレードに伴う懸念点・リスクを事前に解消することが出来ます。**但し、このスクリプトはAPIC内へコピーしてから実行する必要があり、事前準備がやや手間です。**

- [ACI-Pre-Upgrade-Validation-Script](https://github.com/datacenter/ACI-Pre-Upgrade-Validation-Script)

## 「ACI Pre-Upgrade Validator」の特徴

「ACI Pre-Upgrade Validator」は以下の特徴があります。

1. **APIC内へスクリプトやファイルをコピーする必要はありません。リモートホストから実行可能です。**
2. 「CLI版」と「GUI版」のビルド済みバイナリを提供しています。ご自身でのビルドは不要で、すぐに利用可能です。
3. 単独のバイナリだけで動作します。追加のランタイムやソフトウェアのインストールは不要です。
4. 確認項目は[ACI-Pre-Upgrade-Validation-Script](https://github.com/datacenter/ACI-Pre-Upgrade-Validation-Script)と完全に同一です。
5. 実行後、APIC内に生成されるバンドルファイル(.tgz)を自動的にダウンロードします。
6. 画面に表示された確認結果と同じ内容をHTMLファイルとして保存します。

## 動作環境

このツールを実行するには「実行するコンピュータがAPICへSSHログインできる」必要があります。その他の要件はありません。

## インストール

CLI版/GUI版、各々のバイナリはこのページの[リリースページ](https://github.com/pd-labs-admins/ACI-Pre-Upgrade-Validator/releases)からダウンロード可能です。ご利用中のOS/アーキテクチャに合わせて適切なバイナリをダウンロードしてください。ダウンロードしたバイナリは任意のディレクトリへ保存してください。

### GUI版ツール

| OS | アーキテクチャ | ファイル名 |
| --- | --- | --- |
| macOS (darwin) | Intel (amd64 / x86_64) | `ACI-Pre-Upgrade-Validator_darwin_amd64.app` |
| macOS (darwin) | Apple Silicon (arm64 / AArch64) | `ACI-Pre-Upgrade-Validator_darwin_arm64.app` |
| Windows | 64ビット (amd64 / x86_64) | `ACI-Pre-Upgrade-Validator_windows_amd64.exe` |
| Windows | ARM 64ビット (arm64) | `ACI-Pre-Upgrade-Validator_windows_arm64.exe` |

### CLI版ツール

| OS | アーキテクチャ | ファイル名 |
| --- | --- | --- |
| macOS (darwin) | Intel (amd64 / x86_64) | `ACI-Pre-Upgrade-Validator-CLI_darwin_amd64` |
| macOS (darwin) | Apple Silicon (arm64 / AArch64) | `ACI-Pre-Upgrade-Validator-CLI_darwin_arm64` |
| Linux | 32ビット (386 / x86) | `ACI-Pre-Upgrade-Validator-CLI_linux_386` |
| Linux | 64ビット (amd64 / x86_64) | `ACI-Pre-Upgrade-Validator-CLI_linux_amd64` |
| Linux | ARM 64ビット (arm64) | `ACI-Pre-Upgrade-Validator-CLI_linux_arm64` |
| Windows | 32ビット (386 / x86) | `ACI-Pre-Upgrade-Validator-CLI_windows_386.exe` |
| Windows | 64ビット (amd64 / x86_64) | `ACI-Pre-Upgrade-Validator-CLI_windows_amd64.exe` |
| Windows | ARM 64ビット (arm64) | `ACI-Pre-Upgrade-Validator-CLI_windows_arm64.exe` |

## 設定ファイル

設定ファイルが無くてもツールを実行することは可能です。しかし、予め設定ファイルを作成しておくことでツール実行時のパラメータ入力を省略することが出来ます。設定ファイルはCLI版またはGUI版ツールと同じディレクトリに「`config.ini`」というファイル名で保存します。設定ファイルのサンプルは以下の通りです。

```ini
APIC_ADDRESS="apic.example.com"
APIC_PORT="22"
APIC_USER="admin"
APIC_PASS="change-me!"
APIC_PRIV_KEY=""

TARGET_VERSION=""
CURRENT_VERSION=""
VALIDATION_TIMEOUT="600"
MAX_THREADS="0"
API_ONLY=""
SSH_INSECURE=""
```

各項目の意味は以下の通りです。

| 設定項目 | 必須 | デフォルト | 設定例 | 説明 |
| --- | --- | --- | --- | --- |
| `APIC_ADDRESS` | 必須 | - | `apic.example.com` | 接続先 APIC のホスト名または IP アドレス |
| `APIC_PORT` | - | `22` | `22` | APIC への SSH 接続ポート |
| `APIC_USER` | 必須 | - | `admin` | APIC のユーザー名 |
| `APIC_PASS` | 条件付き必須 | - | `change-me` | APIC のパスワード。`APIC_PRIV_KEY` を使用しない場合に必須です |
| `APIC_PRIV_KEY` | 条件付き必須 | - | `/home/user/.ssh/id_rsa` | SSH 秘密鍵ファイルのパス。`APIC_PASS` を使用しない場合に必須です |
| `TARGET_VERSION` | - | - | `6.1(5e)` | アップグレード先の ACI バージョン |
| `CURRENT_VERSION` | - | - | `6.0(2h)` | 現在稼働している ACI バージョン |
| `VALIDATION_TIMEOUT` | - | `600` | `1200` | 検証処理のタイムアウト時間（秒） |
| `MAX_THREADS` | - | `0` | `10` | 検証に使用する最大スレッド数 |
| `API_ONLY` | - | `false` | `true` | `true` にすると API チェックのみ実行します |
| `SSH_INSECURE` | - | `true` | `false` | `false` にすると `known_hosts` に登録されていない APIC への接続を拒否します |
| `DEBUG` | - | `false` | `true` | `true` にするとデバッグメッセージを表示します |

## 実行

ツールの実行方法を説明します。

### GUI版ツール

### Step.1

ご自身の環境にあわせたバイナリをOSのGUI上からダブルクリックして起動します。ツールが起動してGUIが表示されたら接続先APICのIPアドレスやユーザ名、パスワードなど、必須情報を入力します。

![image](assets/gui-01.webp)

### Step.2

必須パラメータを入力したら「`Run validation`」ボタンをクリックし、APICへの接続を開始します。処理が完了するまでしばらく待機します。

![image](assets/gui-02.webp)

### Step.3

「`Validation Log`」ログに確認結果が表示されます。「`Open report`」ボタンをクリックすることで同じ内容をHTML形式で参照することが出来ます。

![image](assets/gui-03.webp)

### CLI版ツール

「`-a`でAPICのアドレス」「`-u`でユーザ名」「`-p`でパスワード」を指定して実行します。ツールと同じディレクトリに`config.ini`が存在する場合、`config.ini`で設定したパラメータが自動的に参照される為、入力を省略することが出来ます。

```sh
% ./ACI-Pre-Upgrade-Validator-CLI_darwin_arm64 -a 172.20.0.200 -u admin -p 'change-me'
    ==== 2026-08-10T16-17-11+0900, Script Version v4.1.1  ====

!!!! Check https://github.com/datacenter/ACI-Pre-Upgrade-Validation-Script for Latest Release !!!!

Gathering Node Information...

Current APIC Version...6.1(5e)
Lowest Switch Version...4.2(7u)

Gathering APIC Versions from Firmware Repository...

[1]: aci-apic-dk9.6.1.5e.bin

What is the Target Version?     :
You have chosen version "6.1(5e)"

Collecting VPC Node IDs...201, 202

Progress: |████████████████████████████████████████████████████████████████████████████████████████████████████| 96/96 checks completed


=== Check Result (failed only) ===

[... snip ...]

=== Summary Result ===

PASS                        : 68
FAIL - OUTAGE WARNING!!     :  0
FAIL - UPGRADE FAILURE!!    :  4
MANUAL CHECK REQUIRED       :  3
POST UPGRADE CHECK REQUIRED :  0
N/A                         : 21
ERROR !!                    :  0
TOTAL                       : 96

    Pre-Upgrade Check Complete.
    Next Steps: Address all checks flagged as FAIL, ERROR or MANUAL CHECK REQUIRED

    Result output and debug info saved to below bundle for later reference.
    Attach this bundle to Cisco TAC SRs opened to address the flagged checks.

      Result Bundle: /home/admin/preupgrade_validator_2026-08-10T16-17-11+0900.tgz

==== Script Version v4.1.1 FIN ====
Report saved to /home/user/report/20260810-161719/report.html
```

その他、CLI版ツールでは以下のオプションを指定可能です。

```sh
% ./dist/ACI-Pre-Upgrade-Validator-CLI_darwin_arm64 -help
ACI-Pre-Upgrade-Validator-CLI dev

Usage: ACI-Pre-Upgrade-Validator-CLI [options]

Runs Cisco ACI pre-upgrade validation remotely over SSH and always writes an HTML report.

Based on Cisco Systems' ACI-Pre-Upgrade-Validation-Script:
https://github.com/datacenter/ACI-Pre-Upgrade-Validation-Script
Original copyright: Copyright (c) Cisco Systems, Inc. and/or its affiliates.
https://www.cisco.com/
Modified portions copyright: Copyright (c) PROGDENCE CO., LTD.
https://www.progdence.co.jp/

Options:
  -a, -address host    APIC address (config.ini: APIC_ADDRESS)
      -api-only        Run API checks only
      -current ver     Override current ACI version
      -debug           Print timestamped verbose output
  -h, -help            Show usage information
  -k, -insecure        Allow an APIC host not present in known_hosts
      -key file        SSH private key; takes priority over the password
      -max-threads n   Maximum validator threads
  -p, -pass value      APIC password
      -port number     SSH port (default: 22)
      -target ver      Target ACI version
      -timeout sec     Validator timeout (default: 600)
  -u, -user name       APIC username
      -update          Update this tool to the latest GitHub release
  -v, -version         Show version information

Example:
  ACI-Pre-Upgrade-Validator-CLI -address apic.example.com -target '6.2(1a)' -insecure
```

## HTML形式のレポートファイル

ツールが正常終了した場合、ツールと同じディレクトリに「`report`ディレクトリ」を作成し、その中に確認結果のレポートを保存します。

```sh
├── ACI-Pre-Upgrade-Validator_darwin_arm64.app
├── ACI-Pre-Upgrade-Validator-CLI_darwin_arm64
├── config.ini
└── report
    └── 20260810-161719
        ├── preupgrade_validator_logs
        │   ├── json_results
        │   │   ├── access_untagged_check.json
        │   │   ├── aes_encryption_check.json

[... snip ...]

        │   │   ├── vpc_paired_switches_check.json
        │   │   └── vzany_vzany_service_epg_check.json
        │   ├── meta.json
        │   ├── preupgrade_validator_2026-08-10T16-17-11+0900.txt
        │   ├── preupgrade_validator_debug.log
        │   └── summary.json
        └── report.html
```

HTML形式のレポートファイルは上部に結果の要約(Summary Result)を表示します。サマリーや詳細内容を確認し、必要に応じて対処を行います。

![image](assets/repot-01.webp)

## ライセンスと謝辞

本ソフトウェアは **Apache License 2.0** のもとで提供されています。詳細は同梱の [LICENSE](LICENSE) ファイルをご参照ください。

また、本ツールは [Cisco Systems, Inc.](https://www.cisco.com/) が開発・公開している [ACI-Pre-Upgrade-Validation-Script](https://github.com/datacenter/ACI-Pre-Upgrade-Validation-Script)（Apache License 2.0）をベースに、改変・機能追加を行って作成された派生ツールです。素晴らしいベースツールをオープンソースとして公開・維持されているオリジナルの開発者・コミュニティの皆様に心より感謝申し上げます。

* **元コードの著作権:** Copyright (c) [Cisco Systems, Inc.](https://www.cisco.com/) and/or its affiliates.
* **改変部分の著作権:** Copyright (c) [PROGDENCE CO., LTD.](https://www.progdence.co.jp/)
