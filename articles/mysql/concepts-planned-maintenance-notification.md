---
title: 計画メンテナンス通知 - Azure Database for MySQL - 単一サーバー
description: この記事では、Azure Database for MySQL - 単一サーバーの計画メンテナンス通知機能について説明します
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: ff197f8add65782a594d64661ffecdaced4598c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94919626"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 単一サーバーの計画メンテナンス通知

Azure Database for MySQL に関する計画メンテナンス イベントの準備方法について説明します。

## <a name="what-is-a-planned-maintenance"></a>計画メンテナンスとは

Azure Database for MySQL サービスでは、基になるハードウェア、OS、データベース エンジンの自動修正が実行されます。 この修正プログラムには、新しいサービス機能、セキュリティ、ソフトウェア更新プログラムが含まれています。 MySQL エンジンの場合、マイナー バージョンのアップグレードは自動的に行われ、パッチ サイクルの一部として含まれます。 パッチの適用に必要なユーザー アクションや構成設定はありません。 修正プログラムは広範にテストされ、安全なデプロイ プラクティスを使用してロールアウトされます。

計画メンテナンスは、特定の Azure リージョン内のサーバーに対してこれらのサービスの更新がデプロイされるメンテナンス期間です。 計画メンテナンス中は、お客様のサーバーをホストしている Azure リージョンにサービスの更新がいつデプロイされるかをお客様に知らせる通知イベントが作成されます。 計画メンテナンスの間隔は、最短で 30 日です。 お客様は、次回のメンテナンス期間の 72 時間前に通知を受信します。

## <a name="planned-maintenance---duration-and-customer-impact"></a>計画メンテナンス - 期間とお客様に与える影響

特定の Azure リージョンに対する計画メンテナンスは、通常は 15 時間実行されることが想定されています。 この期間には、必要に応じてロールバック プランを実行するためのバッファー時間も含まれています。 計画メンテナンス中にデータベース サーバーの再起動またはフェールオーバーが発生する可能性があり、これにより、エンドユーザーがデータベース サーバーを短時間利用できなくなる場合があります。 Azure Database for MySQL サーバーはコンテナー内で実行されているため、データベース サーバーの再起動は、通常は迅速に行われ、60 秒から 120 秒で完了することが想定されています。 各サーバーの再起動を含む計画メンテナンス イベント全体が、エンジニアリング チームによって注意深く監視されます。 サーバーのフェールオーバー時間はデータベースの復旧時間に依存します。このため、フェールオーバー時にサーバーで処理されているトランザクションの量が多い場合は、データベースがオンラインになるまで時間がかかる可能性があります。 再起動時間が長くなるのを避けるために、計画メンテナンス イベント中は実行時間の長いトランザクション (一括読み込み) を行わないようにすることをお勧めします。

要約すると、計画メンテナンス イベントは 15 時間実行されますが、個々のサーバーへの影響は、サーバー上のトランザクション アクティビティに応じて通常は 60 秒間継続します。 計画メンテナンスが開始される 72 カレンダー時間前に通知が送信され、特定のリージョンではメンテナンス中に別の通知が送信されます。

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>計画メンテナンスの通知を受け取るにはどうすればよいですか?

計画メンテナンス通知機能を利用して、まもなく実行される計画メンテナンスに関するアラートを受信できます。 お客様は、まもなく実行されるメンテナンスについての通知を、そのイベントが開始される 72 カレンダー時間前に受信し、特定のリージョンではメンテナンス中に別の通知を受信します。

### <a name="planned-maintenance-notification"></a>計画メンテナンスの通知

> [!IMPORTANT]
> 計画メンテナンスの通知は、現在、米国中西部 **を除く** すべてのリージョンでプレビューとして利用できます

**計画メンテナンス通知** によって、Azure Database for MySQL に対してまもなく実行されるメンテナンスに関するアラートを受信できます。 これらの通知は [Service Health の](../service-health/overview.md)計画メンテナンスに統合されており、サブスクリプションに対してスケジュールされたすべてのメンテナンスを 1 か所に表示できます。 また、異なるリソースに対しては異なる連絡先が必要になる場合があるため、さまざまなリソース グループに対して適切なユーザーへの通知をスケーリングすることも可能です。 まもなく実行されるメンテナンスに関する通知は、そのイベントの 72 カレンダー時間前に受信します。

Microsoft では、**計画メンテナンスの通知** の 72 時間での通知をすべてのイベントに対して提供するために、あらゆる試みを行います。 ただし、重大時やセキュリティ更新プログラムに関する場合には、イベントが迫ってから通知が送信されたり、あるいは通知が省略されたりすることがあります。

Azure portal で計画メンテナンス通知を確認するか、通知を受信するようにアラートを構成できます。 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Azure portal で計画メンテナンス通知を確認する

1. [Azure portal](https://portal.azure.com) で、 **[サービス正常性]** を選択します。
2. **[計画メンテナンス]** タブを選択します
3. 計画メンテナンス通知を確認する **サブスクリプション**、**リージョン**、**サービス** を選択します。 
   
### <a name="to-receive-planned-maintenance-notification"></a>計画メンテナンスの通知を受信するには

1. [ポータル](https://portal.azure.com)で、 **[サービス正常性]** を選択します。
2. **[アラート]** セクションで、 **[正常性アラート]** を選択します。
3. **[+ サービス正常性アラートの追加]** を選択し、フィールドに入力します。
4. 必須フィールドに入力します。 
5. **[イベントの種類]** を選択し、 **[計画メンテナンス]** または **[すべて選択]** を選択します
6. **[アクション グループ]** で、アラートの受信方法 (電子メールの取得、ロジック アプリのトリガーなど) を定義します。  
7. [ルールの作成時に有効にする] を確実に [はい] に設定します。
8. **[アラート ルールの作成]** を選択してアラートを完成させます

**サービス正常性アラート** の作成方法の詳細な手順については、「[サービス通知のアクティビティ ログ アラートを作成する](../service-health/alerts-activity-log-service-notifications-portal.md)」を参照してください。

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>計画メンテナンスのキャンセルや延期は可能ですか?

お使いのサーバーをセキュリティで保護された安定した最新の状態に保つには、メンテナンスが必要です。 計画メンテナンス イベントは、キャンセルすることも延期することもできません。 特定の Azure リージョンに通知が送信された後、そのリージョン内の個々のサーバーに対する修正プログラムの適用スケジュールを変更することはできません。 修正プログラムは、リージョン全体に一度にロールアウトされます。 Azure Database for MySQL - 単一サーバー サービスは、サービスのきめ細かい制御やカスタマイズを必要としないクラウド ネイティブ アプリケーション向けに設計されています。 お使いのサーバーに対するメンテナンスをスケジュールする機能が必要な場合は、[フレキシブル サーバー](./flexible-server/overview.md)を検討することをお勧めします。

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Azure リージョンへの修正プログラムの適用はすべて同時に適用されるのですか?

いいえ。Azure リージョンへの修正プログラムの適用は、すべてデプロイの時間枠のタイミングで実行されます。 デプロイの時間枠は、通常は特定の Azure リージョンで現地時間の午後 5 時から翌日午前 8 時までになります。 Geo ペアの Azure リージョンについては、別の日に修正プログラムが適用されます。 データベース サーバーの高可用性とビジネス継続性を実現するために、[リージョン間の読み取りレプリカ](./concepts-read-replicas.md#cross-region-replication)を活用することをお勧めします。

## <a name="retry-logic"></a>再試行ロジック

一時的なエラーとは自然に解決するエラーのことで、一過性の障害とも呼ばれます。 メンテナンス中に[一時的なエラー](./concepts-connectivity.md#transient-errors)が発生する可能性があります。 こうした事象の大半は、システムにより、60 秒かからず自動的に緩和されます。 一時的なエラーは、[再試行ロジック](./concepts-connectivity.md#handling-transient-errors)を使用して処理する必要があります。


## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL についての質問や提案は、Azure Database for MySQL チーム (AskAzureDBforMySQL@service.microsoft.com) にメールでお送りください
- メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-alert-on-metric.md)に関するページをご覧ください。
- [Azure Database for MySQL - 単一サーバーへの接続に関する問題のトラブルシューティング](howto-troubleshoot-common-connection-issues.md)
- [一時的なエラーを処理して Azure Database for MySQL - 単一サーバーに効率的に接続する](concepts-connectivity.md)
