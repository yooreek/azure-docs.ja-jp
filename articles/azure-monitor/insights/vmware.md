---
title: Azure Monitor の VMware Monitoring ソリューション | Microsoft Docs
description: VMware の監視ソリューションを利用して ESXi ホストのログ管理と監視を行う方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: 9ade5a51e2251669daee6fbaca9aa4c50f7e9bfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704363"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Azure Monitor の VMware Monitoring (非推奨) ソリューション

![VMware のシンボル](./media/vmware/vmware-symbol.png)

> [!NOTE]
> VMware の監視ソリューションは非推奨になりました。  このソリューションを既にインストール済みのユーザーは引き続き使用できますが、新しいワークスペースに VMware Monitoring を追加することはできません。

Azure Monitor の VMware Monitoring ソリューションは、大規模な VMware ログに対する一元化されたログ記録と監視のアプローチを作成するのに役立つソリューションです。 この記事では、このソリューションを使用して 1 つの場所で ESXi ホストのトラブルシューティング、キャプチャ、管理を行う方法を説明します。 このソリューションでは、すべての ESXi ホストの詳細なデータを 1 つの場所に表示できます。 ESXi ホストのログを通じて、VM および ESXi ホストの上位のイベント数、状態、傾向を知ることができます。 一元化された ESXi ホストのログを表示および検索して、トラブルシューティングを実行できます。 また、ログ検索クエリに基づくアラートを作成することもできます。

このソリューションでは、ESXi ホストのネイティブの syslog 機能を使用して、Log Analytics エージェントがインストールされたターゲット VM にデータをプッシュします。 ただし、ターゲット VM 内の syslog にはファイルは書き込まれません。 Log Analytics エージェントがポート 1514 を開き、このポートをリッスンします。 Log Analytics エージェントは、データを受信すると、Azure Monitor にデータをプッシュします。

## <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

* [監視ソリューションのインストール](./solutions.md#install-a-monitoring-solution)に関するページで説明されている手順を使って、VMware Monitoring ソリューションをサブスクリプションに追加します。

#### <a name="supported-vmware-esxi-hosts"></a>サポートされる VMware ESXi ホスト
vSphere ESXi Host 5.5、6.0、6.5

#### <a name="prepare-a-linux-server"></a>Linux サーバーの準備
ESXi ホストからのすべての syslog データを受信する Linux オペレーティング システムの VM を作成します。 [Log Analytics Linux エージェント](../vm/quick-collect-linux-computer.md)は、すべての ESXi ホスト syslog データの収集ポイントです。 次の例に示すように、複数の ESXi ホストを使用して 1 台の Linux サーバーにログを転送できます。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![syslog のフロー](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>syslog の収集の構成
1. VSphere の syslog 転送を設定します。 syslog 転送の設定方法について詳しくは、「[Configuring syslog on ESXi 5.0 and higher (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322)」(ESXi 5.0 以降での syslog の構成 (2003322)) をご覧ください。 **ESXi ホストの [構成]**  >  **[ソフトウェア]**  >  **[詳細設定]**  >  **[Syslog]** に移動します。
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. *Syslog.global.logHost* フィールドに、Linux サーバーとポート番号 *1514* を追加します。 たとえば、`tcp://hostname:1514` や `tcp://123.456.789.101:1514` のようにします。
1. ESXi ホストの syslog 用ファイアウォールを開きます。 **ESXi ホストの [構成]**  >  **[ソフトウェア]**  >  **[セキュリティ プロファイル]**  >  **[ファイアウォール]** をクリックし、 **[プロパティ]** を開きます。  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. vSphere コンソールを調べて、syslog が正しく設定されていることを確認します。 ESXI ホストで、ポート **1514** が構成されていることを確認します。
1. Linux 用 Log Analytics エージェントをダウンロードし、Linux サーバーにインストールします。 詳細については、[Linux 用の Log Analytics エージェントのドキュメント](https://github.com/Microsoft/OMS-Agent-for-Linux)をご覧ください。
1. Linux 用 Log Analytics エージェントをインストールしたら、/etc/opt/microsoft/omsagent/sysconf/omsagent.d ディレクトリに移動して vmware_esxi.conf ファイルを /etc/opt/microsoft/omsagent/conf/omsagent.d ディレクトリにコピーし、ファイルの所有者/グループとアクセス許可を変更します。 次に例を示します。

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. `sudo /opt/microsoft/omsagent/bin/service_control restart` を実行して Linux 用 Log Analytics エージェントを再起動します。
1. ESXi ホストで `nc` コマンドを使用して、Linux サーバーと ESXi ホスト間の接続をテストします。 次に例を示します。

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Azure portal で、`VMware_CL` のログ クエリを実行します。 Azure Monitor が syslog データを収集するときは、syslog 形式が維持されます。 ポータルでは、*Hostname* や *ProcessName* など、いくつかの特定のフィールドがキャプチャされます。  

    ![スクリーンショットには、タイムスタンプの結果と一緒に Type = VMware_CL のログ クエリが示されています。](./media/vmware/type.png)  

    上の図のようなログ検索結果が表示される場合は、VMware Monitoring ソリューション ダッシュボードを使うように設定されています。  

## <a name="vmware-data-collection-details"></a>VMware のデータ収集の詳細
VMware Monitoring ソリューションは、有効にしている Linux 用 Log Analytics エージェントを使用して、ESXi ホストからさまざまなパフォーマンス メトリックとログ データを収集します。

次の表は、データの収集手段と、データの収集方法に関する各種情報をまとめたものです。

| プラットフォーム | Linux 用 Log Analytics エージェント | System Center Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |3 分おき |

次の表は、VMware の監視ソリューションによって収集されるデータ フィールドの例を示しています。

| フィールド名 | description |
| --- | --- |
| Device_s |VMware ストレージ デバイス |
| ESXIFailure_s |エラーのタイプ |
| EventTime_t |イベントが発生した時刻 |
| HostName_s |ESXi ホスト名 |
| Operation_s |VM の作成または VM の削除 |
| ProcessName_s |イベント名 |
| ResourceId_s |VMware ホストの名前 |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware の SCSI ステータス |
| SyslogMessage_s |syslog データ |
| UserName_s |VM を作成または削除したユーザー |
| VMName_s |VM 名 |
| Computer |ホスト コンピューター |
| TimeGenerated |データが生成された時刻 |
| DataCenter_s |VMware データセンター |
| StorageLatency_s |ストレージの待機時間 (ミリ秒) |

## <a name="vmware-monitoring-solution-overview"></a>VMware の監視ソリューションの概要
Log Analytics ワークスペースに VMware のタイルが表示されます。 このタイルでは、エラーの概要が示されます。 タイルをクリックすると、ダッシュボード ビューが表示されます。

![スクリーンショットには、9 件のエラーを表示した VMware タイルが示されています。](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>ダッシュボード ビューのナビゲーション
**[VMware]** ダッシュボード ビューでは、ブレードが次のように分類されています。

* Failure Status Count (失敗状態の数)
* Top Host by Event Counts (イベント数が上位のホスト)
* Top Event Counts (イベント数の上位)
* Virtual Machine Activities (仮想マシンのアクティビティ)
* ESXi Host Disk Events (ESXi ホスト ディスクのイベント)

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

いずれかのブレードをクリックすると、そのブレードに固有の詳細情報を表示する Log Analytics 検索ウィンドウが開きます。

ここでログ クエリを編集して、特定の情報が見つかるように変更することができます。 ログ クエリの作成について詳しくは、[Azure Monitor でログ クエリを使ってデータを探す](../logs/log-query-overview.md)方法に関する記事をご覧ください。

#### <a name="find-esxi-host-events"></a>ESXi ホストのイベントの検索
1 台の ESXi ホストで、そのプロセスに基づいて複数のログが生成されます。 VMware の監視ソリューションは、それらを一元化してイベント数を集計します。 この一元化されたビューを利用して、環境内のイベント数の多い ESXi ホストや、最も頻繁に発生しているイベントを把握できます。

![イベント](./media/vmware/events.png)

いずれかの ESXi ホストまたはイベント タイプをクリックすると、さらに詳しい情報を表示できます。

ESXi ホスト名をクリックすると、その ESXi ホストからの情報が表示されます。 イベント タイプで結果を絞り込む場合は、検索クエリに `“ProcessName_s=EVENT TYPE”` を追加します。 検索フィルターで **ProcessName** を選択できます。 これにより、情報が絞り込まれます。

![VMware Monitoring ダッシュボード ビューの [イベント数あたりの ESXi ホスト] および [イベントの種類ごとの明細] ブレードのスクリーンショット。](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>高い VM アクティビティの検索
どの ESXi ホストでも、仮想マシンを作成および削除することができます。 ESXi ホストで作成される VM の数を把握することは、管理者にとって役立ちます。 これはまた、パフォーマンスと容量計画を理解するのにも役立ちます。 VM アクティビティ イベントの追跡は、環境を管理する際に非常に重要です。

![VMware Monitoring ダッシュボードの [仮想マシンのアクティビティ] ブレードのスクリーンショット。ESXi ホストによる VM の作成と削除のグラフが表示されます。](./media/vmware/vmactivities1.png)

ESXi ホストの VM 作成データの詳細を表示したい場合は、ESXi ホスト名をクリックします。

![ESXi ホストによって作成された各仮想マシンのデータ行を含むテーブルが表示されている、VMware Monitoring ダッシュボードのウィンドウのスクリーンショット。](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>一般的なログ クエリ
このソリューションには、ESXi ホストの管理に役立つその他の便利なクエリ (高いストレージ使用量、ストレージの待機時間、パスの障害など) も含まれています。

![スクリーンショットには、役立つ格納されたクエリである推奨される検索が示されています。](./media/vmware/queries.png)


#### <a name="save-queries"></a>クエリの保存
ログ クエリの保存は Azure Monitor の標準機能であり、便利なクエリを保存しておくのに役立ちます。 作成したクエリが便利であることがわかったら、 **[お気に入り]** をクリックして保存します。 保存したクエリは、後で [[マイ ダッシュボード]](../visualize/tutorial-logs-dashboards.md) ページから簡単に再利用できます。このページでは、独自のカスタム ダッシュボードを作成できます。

![スクリーンショットには、[ログ検索] のラベルと、[元に戻す]、[エクスポート]、[アラート]、[保存]、[お気に入り]、および [履歴] のアイコンが表示されたカスタム ダッシュボードの一部が示されています。](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>クエリからのアラートの作成
クエリを作成したら、そのクエリを使用して、特定のイベントが発生したときにアラートを受け取ることもできます。 アラートの作成方法については、「[Log Analytics のアラート](../alerts/alerts-overview.md)」をご覧ください。 アラート クエリおよびその他のクエリの例については、ブログ記事「[Monitor VMware using Log Analytics](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics)」(Log Analytics を使用した VMware の監視) をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>ESXi ホストの設定で必要なことは何ですか。 現在の環境にどのような影響がありますか。
ソリューションでは、ESXi ホストのネイティブの syslog 転送メカニズムを使用します。 ログを記録するために、ESXi ホストに Microsoft ソフトウェアを追加する必要はありません。 既存の環境への影響はわずかですが、 ESXi の機能である syslog 転送を設定する必要があります。

### <a name="do-i-need-to-restart-my-esxi-host"></a>ESXi ホストを再起動する必要がありますか。
いいえ。 このプロセスでは再起動は不要です。 vSphere で syslog が適切に更新されない場合があります。 このような場合は、ESXi ホストにログオンし、syslog を再読み込みします。 この場合もホストを再起動する必要はないので、このプロセスが環境に悪影響を及ぼすことはありません。

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Log Analytics に送信されるログ データの量を増減することはできますか。
はい、できます。 vSphere の ESXi ホストのログ レベル設定を使用します。 ログの収集は *info* レベルに基づいています。 そのため、VM の作成や削除を監査する場合は、Hostd で *info* レベルを維持する必要があります。 詳細については、[VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658) をご覧ください。

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Hostd から Log Analytics にデータが提供されていないのはなぜですか。 ログ設定は info に設定されています。
ESXi ホストに syslog タイムスタンプのバグがありました。 詳細については、[VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202) をご覧ください。 この解決策を適用すると、Hostd が正常に機能するようになります。

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>複数の ESXi ホストから、omsagent を実行している 1 つの VM に syslog データを転送することはできますか。
はい。 複数の ESXi ホストから、omsagent を実行している 1 つの VM に転送できます。

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Log Analytics へのデータ フローが表示されないのはなぜですか。
複数の理由が考えられます。

* omsagent を実行している VM に、ESXi ホストがデータを正しくプッシュしていません。 これをテストするには、次の手順を実行します。

  1. 確認するには、ssh を使用して ESXi ホストにログオンし、`nc -z ipaddressofVM 1514` コマンドを実行します。

      このコマンドが失敗した場合は、[詳細構成] の vSphere 設定に誤りがあると考えられます。 syslog 転送用に ESXi ホストを設定する方法については、「[syslog の収集の構成](#configure-syslog-collection)」をご覧ください。
  1. syslog ポートの接続は成功しているにも関わらず、データが引き続き表示されない場合は、ssh を使用して `esxcli system syslog reload` コマンドを実行し、ESXi ホストで syslog を再読み込みします。
* Log Analytics エージェントがインストールされた VM が正しく設定されていません。 これをテストするには、次の手順を実行します。

  1. Log Analytics は、ポート 1514 をリッスンします。 このポートが開いていることを確認するには、`netstat -a | grep 1514` コマンドを実行します。
  1. ポート `1514/tcp` が開いていることがわかります。 この情報が表示されない場合は、omsagent が正しくインストールされていることを確認します。 ポート情報が表示されない場合、VM で syslog ポートが開いていません。

    a. `ps -ef | grep oms` を使用して、Log Analytics エージェントが実行されていることを確認します。 実行されていない場合は、`sudo /opt/microsoft/omsagent/bin/service_control start` コマンドを実行してプロセスを開始します。

     b. `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` ファイルを開きます。

     c. `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf` のように、適切なユーザーおよびグループ設定が有効であることを確認します。

     d. このファイルがない場合や、ユーザーおよびグループ設定に問題がある場合は、[Linux サーバーを準備](#prepare-a-linux-server)して、修正措置を講じます。

## <a name="next-steps"></a>次のステップ
* Log Analytics の[ログ クエリ](../logs/log-query-overview.md)を使用して、VMware ホストの詳細データを表示します。
* VMware ホストのデータを表示する[独自のダッシュ ボードを作成](../visualize/tutorial-logs-dashboards.md)します。
* VMware ホストの特定のイベントが発生した場合の[アラートを作成](../alerts/alerts-overview.md)します。

