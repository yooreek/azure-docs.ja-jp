---
title: Azure Backup Server のトラブルシューティング
description: Azure Backup Server のインストールと登録、およびアプリケーション ワークロードのバックアップと復元をトラブルシューティングします。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 09e5fe5da7e316257cbbdcb89074fe8a4bc692c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91403009"
---
# <a name="troubleshoot-azure-backup-server"></a>Azure Backup Server のトラブルシューティング

次の表の情報を使用して、Azure Backup Server の使用中に発生したエラーのトラブルシューティングを行います。

## <a name="basic-troubleshooting"></a>基本的なトラブルシューティング

Microsoft Azure Backup Server (MABS) のトラブルシューティングを開始する前に、以下の検証を実行することをお勧めします。

- [Microsoft Azure Recovery Services (MARS) エージェントが最新であることを確認する](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS エージェントと Azure の間にネットワーク接続が存在することを確認する](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Microsoft Azure Recovery Services が (サービス コンソールで) 実行されていることを確認します。 必要に応じて、再起動して操作をやり直します
- [スクラッチ フォルダーの場所に 5 から 10% の空きボリューム領域があることを確認する](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- 登録が失敗する場合は、Azure Backup Server をインストールしようとしているサーバーが別のコンテナーにまだ登録されていないことを確認します
- プッシュ インストールが失敗する場合は、DPM エージェントが既に存在するかどうかを確認してください。 その場合は、エージェントをアンインストールしてからインストールをやり直してください。
- [別のプロセスまたはウイルス対策ソフトウェアによって Azure Backup が妨げられていないことを確認する](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- SQL Agent サービスが実行中で、MABS サーバーで自動に設定されていることを確認する<br>

## <a name="configure-antivirus-for-mabs-server"></a>MABS サーバーのウイルス対策を構成する

MABS は、ほとんどの一般的なウイルス対策ソフトウェア製品との互換性を備えています。 競合を回避するため、次の手順をお勧めします。

1. **リアルタイム監視の無効化** - 次を対象としたウイルス対策ソフトウェアによるリアルタイム監視を無効にします。
    - `C:\Program Files<MABS Installation path>\XSD` フォルダー
    - `C:\Program Files<MABS Installation path>\Temp` フォルダー
    - Modern Backup Storage ボリュームのドライブ文字
    - レプリカと転送ログ: これを行うには、`Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` フォルダーにある **dpmra.exe** のリアルタイム監視を無効にします。 リアルタイム監視を実行すると、ウイルス対策ソフトウェアが、保護されたサーバーに MABS が同期するたびにレプリカをスキャンし、MABS がレプリカに変更を適用するたびに影響されたファイルをすべてスキャンするため、パフォーマンスが低下します。
    - 管理者コンソール: パフォーマンスへの影響を回避するには、**csc.exe** プロセスのリアルタイム監視を無効にします。 **csc.exe** プロセスは C\# コンパイラです。リアルタイム監視を実行すると、**csc.exe** プロセスで XML メッセージが生成されるときに出力されるファイルをウイルス対策ソフトウェアがスキャンするため、パフォーマンスが低下します。 **CSC.exe** は、次の場所にあります。
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - MABS サーバーにインストールされている MARS エージェントの場合は、次のファイルと場所を除外することをお勧めします。
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` (プロセス)
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - スクラッチの位置 (標準の場所を使用していない場合)
2. **保護されたサーバーのリアルタイム監視の無効化**: 保護されたサーバーの `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` フォルダーにある **dpmra.exe** のリアルタイム監視を無効にします。
3. **保護されたサーバーおよび MABS サーバーにある感染したファイルの削除を目的としたウイルス対策ソフトウェアの構成**: レプリカおよび回復ポイントのデータ破損を回避するため、感染ファイルを自動的にクリーニングまたは検疫するのではなく、削除するようにウイルス対策ソフトウェアを構成します。 自動的にクリーニングまたは検疫すると、ウイルス対策ソフトウェアによってファイルが変更され、MABS が検出できない変更が行われる可能性があります。

整合性のある同期を手動で実行する必要があります。 レプリカが不整合とマークされていても、ウイルス対策ソフトウェアがレプリカからファイルを削除するたびにジョブを確認します。

### <a name="mabs-installation-folders"></a>MABS インストール フォルダー

DPM の既定のインストール フォルダーは次のとおりです。

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

次のコマンドを実行して、インストール フォルダーのパスを検索することもできます。

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>無効なコンテナーの資格情報が指定されました

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| コンテナーへの登録 | 無効なコンテナーの資格情報が指定されました。 ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。 | 推奨される操作: <br> <ul><li> コンテナーから最新の資格情報ファイルをダウンロードしてから、やり直します。 <br>(または)</li> <li> 前記の方法で解決できなかった場合は、資格情報を別のローカル ディレクトリにダウンロードするか、新しいコンテナーを作成します。 <br>(または)</li> <li> [この記事](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided)で説明されているように、日時の設定を更新します。 <br>(または)</li> <li> c:\windows\temp にあるファイルの数が 65,000 を超えているか確認します。 古いファイルを別の場所に移動するか、Temp フォルダー内のアイテムを削除します。 <br>(または)</li> <li> 証明書の状態を確認します。 <br> a. (コントロール パネルで) **[コンピューター証明書の管理]** を開きます。 <br> b. **[個人]** ノードとその子ノードの **[証明書]** を展開します。<br> c.  **Microsoft Azure Tools** 証明書を削除します。 <br> d. Azure Backup クライアントで登録を再試行します。 <br> (または) </li> <li> グループ ポリシーが存在するかどうか確認します。 </li></ul> |

## <a name="replica-is-inconsistent"></a>レプリカに整合性がありません

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| バックアップ | レプリカに整合性がありません | 保護グループ ウィザードで自動の整合性チェック オプションが有効になっていることを確認してください。 レプリケーション オプションと整合性チェックの詳細については、[この記事](/system-center/dpm/create-dpm-protection-groups)を参照してください。<br> <ol><li> システム状態または BMR のバックアップの場合は、保護されるサーバーに Windows Server バックアップがインストールされているか確認してください。</li><li> DPM または Microsoft Azure Backup Server 上の DPM 記憶域プールにおいて領域関連の問題が存在するか確認し、必要に応じて記憶域を割り当てます。</li><li> 保護されるサーバーのボリューム シャドウ コピー サービスの状態を確認します。 無効な状態である場合は、手動で開始するように設定します。 サーバーでサービスを開始します。 DPM または Microsoft Azure Backup Server コンソールに戻り、整合性チェック ジョブとの同期を開始します。</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>オンライン回復ポイントを作成できませんでした

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| バックアップ | オンライン回復ポイントを作成できませんでした | **エラー メッセージ**:Windows Azure Backup エージェントが、選択されたボリュームのスナップショットを作成できませんでした。 <br> **回避策**:レプリカと復旧ポイント ボリュームの領域を増やしてください。<br> <br> **エラー メッセージ**:Windows Azure Backup エージェントが、OBEngine サービスに接続できませんでした <br> **対処法**: コンピューター上で実行中のサービス一覧に OBEngine が存在することを確認してください。 OBEngine サービスが実行されていない場合は、"net start OBEngine" コマンドを使用して、OBEngine サービスを開始します。 <br> <br> **エラー メッセージ**:このサーバーの暗号化パスフレーズは設定されていません。 暗号化パスフレーズを構成してください。 <br> **回避策**:暗号化パスフレーズを構成してください。 失敗した場合は、次の手順を実行します。 <br> <ol><li>スクラッチ場所が存在することを確認します。 レジストリ **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config** に **ScratchLocation** という名前で場所が存在している必要があります。</li><li> このスクラッチ場所が存在する場合は、以前のパスフレーズを使用して再登録します。 *暗号化のパスフレーズを構成したら、必ず安全な場所に保管してください。*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>元の DPM サーバーと外部の DPM サーバーを同じコンテナーに登録する必要があります

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| 復元 | **エラー コード**:CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error:100110 <br/> <br/>**エラー メッセージ**:元の DPM サーバーと外部の DPM サーバーを同じコンテナーに登録する必要があります | **原因**:この問題は、外部 DPM の回復オプションを使用して配信元サーバーから代替サーバーにファイルを復元しようとしているときに、復旧先のサーバーと、データのバックアップ元のサーバーが同じ Recovery Services コンテナーに関連付けられていない場合に発生します。<br/> <br/>**対処法**: この問題を解決するには、元のサーバーと代替サーバーの両方を必ず同じコンテナーに登録します。|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>VMware VM のオンライン復旧ポイント作成ジョブが失敗します

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| バックアップ | VMware VM のオンライン復旧ポイント作成ジョブが失敗します。 DPM での ChangeTracking 情報の取得中に VMware でエラーが発生しました。 ErrorCode - FileFaultFault (ID 33621) |  <ol><li> 影響を受けた仮想マシンの VMWare で CTK をリセットします。</li> <li>独立したディスクが VMware 上にないことを確認します。</li> <li>影響を受けた仮想マシンの保護を停止し、 **[更新]** ボタンで再保護します。 </li><li>影響を受けた仮想マシンに CC を実行します。</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>サーバー上の DPM エージェント コーディネーター サービスとの通信エラーのため、エージェント操作に失敗しました

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| 保護されたサーバーへのエージェントのプッシュ | \<ServerName> の DPM エージェント コーディネーター サービスとの通信エラーのため、エージェント操作に失敗しました | **製品が推奨する対処法でうまくいかない場合は、次の手順を実行します**。 <ul><li> 信頼されていないドメインのコンピューターを接続している場合は、[こちらのステップ](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains)に従います。 <br> (または) </li><li> 信頼されているドメインのコンピューターを接続している場合は、[このブログ](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726)で説明されているステップを使用してトラブルシューティングを行います。 <br>(または)</li><li> トラブルシューティングの一環としてウイルス対策ソフトウェアを無効にします。 それで問題が解決する場合は、[この記事](/system-center/dpm/run-antivirus-server)で推奨されているとおりにウイルス対策の設定を変更します。</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>セットアップでレジストリのメタデータを更新できません

| 操作 | エラーの詳細 | 回避策 |
|-----------|---------------|------------|
|インストール | セットアップでレジストリのメタデータを更新できませんでした。 この更新エラーによって記憶域の消費量が過剰になる可能性があります。 これを回避するには、ReFS Trimming レジストリ エントリを更新します。 | レジストリ キー **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim** を調整します。 Dword の値を 1 に設定します。 |
|インストール | セットアップでレジストリのメタデータを更新できませんでした。 この更新エラーによって記憶域の消費量が過剰になる可能性があります。 これを回避するには、Volume SnapOptimization レジストリ エントリを更新します。 | 空の文字列値を持つ **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** レジストリ キーを作成します。 |

## <a name="registration-and-agent-related-issues"></a>登録とエージェントに関する問題

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| 保護されたサーバーへのエージェントのプッシュ | サーバーに対して指定された資格情報が無効です。 | **製品が推奨する対処法でうまくいかない場合は、次の手順を実行します**: <br> [この記事](/system-center/dpm/deploy-dpm-protection-agent)に示されているように、保護エージェントを運用サーバーに手動でインストールします。|
| Azure Backup エージェントは、Azure Backup サービスに接続できませんでした (ID:100050) | Azure Backup エージェントは、Azure Backup サービスに接続できませんでした | **製品が推奨する対処法でうまくいかない場合は、次の手順を実行します**: <br>1.管理者特権のコマンド プロンプトから、コマンド **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe** を実行します。 Internet Explorer のウィンドウが開きます。 <br/> 2. **[ツール]**  >  **[インターネット オプション]**  >  **[接続]**  >  **[LAN の設定]** の順に移動します。 <br/> 3.プロキシ サーバーを使用するように設定を変更します。 その後、プロキシ サーバーの詳細を指定します。<br/> 4.マシンのインターネットへのアクセスが制限されている場合は、マシンまたはプロキシのファイアウォール設定によって次の [URL](install-mars-agent.md#verify-internet-access) と [IP アドレス](install-mars-agent.md#verify-internet-access)が許可されることを確認します。|
| Azure Backup エージェントインストールに失敗しました | Microsoft Azure Recovery Services のインストールに失敗しました。 Microsoft Azure Recovery Services のインストールによってシステムに対して実行されたすべての変更はロールバックされました。 (ID: 4024) | Azure エージェントを手動でインストールします。

## <a name="configuring-protection-group"></a>保護グループの構成

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| 保護グループの構成 | DPM は、保護されたコンピューター (保護されたコンピューター名) 上のアプリケーション コンポーネントを列挙できませんでした。 | 保護グループの構成 UI 画面で、関連するデータソースまたはコンポーネント レベルで **[更新]** を選択します。 |
| 保護グループの構成 | 保護を構成できません | 保護されるサーバーが SQL Server の場合は、[この記事](/system-center/dpm/back-up-sql-server)で説明されているように、保護されたコンピューターのシステム アカウント (NTAuthority\System) に sysadmin ロールのアクセス許可が付与されていることを確認してください。
| 保護グループの構成 | この保護グループの記憶域プールには十分な空き領域がありません。 | 記憶域プールに追加するディスクには、[パーティションを含めることはできません](/system-center/dpm/create-dpm-protection-groups)。 ディスク上の既存のボリュームを削除します。 それから記憶域プールに追加します。|
| ポリシーの変更 |バックアップ ポリシーを変更できませんでした。 エラー:サービスの内部エラー [0x29834] が発生したため、現在の操作を実行できませんでした。 しばらくしてから、操作を再試行してください。 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。 | **原因:**<br/>このエラーが発生する状況は 3 つあります。セキュリティ設定が有効になっている場合、保持期間を前に指定した最小値より小さくなるように減らす場合、そしてサポートされていないバージョンを使用している場合です。 (サポートされていないバージョンは、Microsoft Azure Backup Server バージョン 2.0.9052 より前のバージョンおよび Azure Backup Server 更新プログラム 1 です。) <br/>**推奨される操作:**<br/> ポリシーに関連する更新プログラムを続行するには、指定した最小リテンション期間を超えるようにリテンション期間を設定します。 (最小リテンション期間は、毎日だと 7 日、毎週だと 4 週間、毎月だと 3 週間、毎年だと 1 年です。) <br><br>他にもお勧めの方法として、バックアップ エージェントおよび Azure Backup Server を更新して、すべてのセキュリティ更新プログラムを適用する方法があります。 |

## <a name="backup"></a>バックアップ

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| バックアップ | ジョブの実行中に予期しないエラーが発生しました。 デバイスの準備ができていません。 | **製品が推奨する対処法でうまくいかない場合は、次の手順を実行します:** <br> <ul><li>保護グループ内の項目でシャドウ コピーの記憶域の領域を無制限に設定してから、整合性チェックを実行します。<br></li> (または) <li>既存の保護グループを削除して、複数の新しいグループを作成します。 新しい保護グループのそれぞれに、個々 の項目が必要です。</li></ul> |
| バックアップ | システム状態だけをバックアップする場合は、保護されたコンピューターにシステム状態のバックアップを格納するのに十分な空き領域があることを確認してください。 | <ol><li>保護されたマシンに Windows Server バックアップがインストールされていることを確認します。</li><li>保護されたコンピューターにシステム状態のための十分な領域があることを確認します。 これを確認する最も簡単な方法として、保護されたコンピューターに移動し、Windows Server バックアップを開き、選択項目をクリックして BMR を選択します。 必要な領域量が UI に表示されます。 **WSB** >  **[ローカル バックアップ]**  >  **[バックアップのスケジュール]**  >  **[バックアップの構成の選択]**  >  **[サーバー全体]** (サイズが表示される) と開きます。 このサイズを検証に使用します。</li></ol>
| バックアップ | BMR のバックアップの失敗 | BMR サイズが大きい場合は、一部のアプリケーション ファイルを OS ドライブに移動して、再試行してください。 |
| バックアップ | 新しい Microsoft Azure Backup Server 上の VMware VM を再保護するオプションが、追加可能と表示されません。 | VMware のプロパティが、Microsoft Azure Backup Server の提供終了になった古いインスタンスを参照しています。 この問題を解決するには、次の手順を実行します。<br><ol><li>VCenter (SC-VMM に相当) で、 **[概要]** タブ、 **[カスタム属性]** と移動します。</li>  <li>**DPMServer** 値から、古い Microsoft Azure Backup Server 名を削除します。</li>  <li>新しい Microsoft Azure Backup Server に戻り、PG を変更します。  **[更新]** ボタンを選択すると、保護の追加に使用できるチェック ボックスが VM に表示されます。</li></ol> |
| バックアップ | ファイル/共有フォルダーへのアクセス中のエラー | 「[DPM サーバーでのウイルス対策ソフトウェアの実行](/system-center/dpm/run-antivirus-server)」という記事で提案されているようにウイルス対策の設定を変更してみてください。|

## <a name="change-passphrase"></a>パスフレーズの変更

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| パスフレーズの変更 |入力されたセキュリティ PIN が正しくありません。 この操作を完了するには、正しいセキュリティ PIN を指定してください。 |**原因:**<br/> このエラーは、重要な操作 (パスフレーズの変更など) の実行中に、無効または有効期限が切れたセキュリティ PIN を入力したときに発生します。 <br/>**推奨される操作:**<br/> 操作を完了するには、有効なセキュリティ PIN を入力する必要があります。 PIN を取得するには、Azure Portal にサインインし、Recovery Services コンテナーに移動します。 **[設定]**  >  **[プロパティ]**  >  **[セキュリティ PIN の生成]** と移動します。 この PIN を使用してパスフレーズを変更します。 |
| パスフレーズの変更 |操作に失敗しました。 ID: 120002 |**原因:**<br/>このエラーは、セキュリティ設定が有効な場合、または、サポートされていないバージョンの使用中にパスフレーズを変更しようとした場合に発生します。<br/>**推奨される操作:**<br/> パスフレーズを変更するには、最初に Backup エージェントを最小バージョン (2.0.9052) に更新する必要があります。 また、Azure Backup Server を最低限の更新プログラム 1 に更新してから、有効なセキュリティ PIN を入力する必要があります。 PIN を取得するには、Azure Portal にサインインし、Recovery Services コンテナーに移動します。 **[設定]**  >  **[プロパティ]**  >  **[セキュリティ PIN の生成]** と移動します。 この PIN を使用してパスフレーズを変更します。 |

## <a name="configure-email-notifications"></a>電子メール通知の構成

| 操作 | エラーの詳細 | 回避策 |
| --- | --- | --- |
| 職場または学校アカウントを使用した電子メール通知の設定 |エラー ID: 2013| **原因:**<br> 職場または学校アカウントを使用しようとしています <br>**推奨される操作:**<ol><li> まず、Exchange で DPM サーバーが "受信コネクタで匿名のリレーを許可する" ように設定されていることを確認します。 これを構成する方法の詳細については、「[受信コネクタの匿名の中継を許可する](/exchange/mail-flow/connectors/allow-anonymous-relay)」を参照してください。</li> <li> 内部 SMTP リレーを使用できず、Office 365 サーバーを使用して設定する必要がある場合は、リレーとして IIS を設定することができます。 DPM サーバーが [IIS を使用して SMTP を Office 365 にリレーする](/exchange/mail-flow/test-smtp-with-telnet)ように設定します。<br><br>  ドメイン\ユーザー *ではなく*、必ず user\@domain.com 形式を使用してください。<br><br><li>DPM が、SMTP サーバーとしてローカル サーバー名 (およびポート 587) を使用するようにします。 次に、これを電子メールの送信元となるユーザーの電子メール アドレスに向けます。<li> DPM の SMTP セットアップ ページ上のユーザー名とパスワードは、DPM があるドメイン内のドメイン アカウントのものである必要があります。 </li><br> SMTP サーバーのアドレスを変更するときは、新しい設定を変更し、設定ボックスを閉じてからもう一度開いて、新しい値が反映されていることを確認してください。  変更してテストしただけでは、新しい設定が反映されていない可能性があるため、この方法でテストすることをお勧めします。<br><br>DPM コンソールを閉じて次のレジストリ キーを編集すれば、この操作中にいつでもこれらの設定を削除できます。**HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword and SMTPUserName keys**。 もう一度起動したときに、UI にそれらを追加できます。

## <a name="common-issues"></a>一般的な問題

ここでは、Azure Backup Server の使用中に発生する一般的なエラーについて説明します。

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

エラー メッセージ | 推奨される操作 |
-- | --
ディスク バックアップ レプリカが無効または不足しているため、バックアップが失敗しました。 | この問題を解決するには、次の手順を確認し、操作を再試行してください。 <br/> 1.ディスク復旧ポイントを作成する<br/> 2.データソースで整合性チェックを実行する <br/> 3.データソースの保護を停止してから、このデータ ソースの保護を再構成する

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

エラー メッセージ | 推奨される操作 |
-- | --
レプリカのメタデータが無効なため、ソース ボリュームのスナップショットに失敗しました。 | このデータソースのディスク復旧ポイントを作成し、オンラインバックアップをもう一度お試しください

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

エラー メッセージ | 推奨される操作 |
-- | --
データソース レプリカに一貫性がないため、ソース ボリュームのスナップショットに失敗しました。 | このデータソースで整合性チェックを実行し、もう一度お試しください

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

エラー メッセージ | 推奨される操作 |
-- | --
ディスクバックアップ レプリカを複製できなかったため、バックアップに失敗しました。| 以前のディスクバックアップ レプリカ ファイル (.vhdx) がすべてマウント解除されており、オンライン バックアップ中、ディスク間のバックアップが進行していないことを確認してください
