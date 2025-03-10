---
title: Azure 仮想マシン スケール セットの概要
description: Azure Virtual Machine Scale Sets についての説明に加えて、アプリケーションの自動スケーリングを行う方法についても説明します
author: mimckitt
ms.author: mimckitt
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 76d3bc1e1736e648316bcd81bf8897d1d2f272a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672585"
---
# <a name="what-are-virtual-machine-scale-sets"></a>仮想マシン スケール セットとは
Azure Virtual Machine Scale Sets では、負荷分散が行われる VM のグループを作成して管理することができます。 需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。 スケール セットは、アプリケーションの高可用性を実現します。また、多数の VM の一元的な管理、構成、更新を可能にします。 仮想マシン スケール セットを使用すると、コンピューティング、ビッグ データ、コンテナー ワークロードなどの分野で大規模なサービスを構築できます。


## <a name="why-use-virtual-machine-scale-sets"></a>仮想マシン スケール セットを使用する理由
冗長性とパフォーマンスの向上を実現するには、アプリケーションを複数のインスタンスに分散するのが一般的です。 顧客は、アプリケーション インスタンスのいずれかに要求を分散するロード バランサー経由でアプリケーションにアクセスできます。 メンテナンスの実行またはアプリケーション インスタンスの更新が必要な場合、使用可能な別のアプリケーション インスタンスに顧客を分散する必要があります。 顧客の需要の増加に対応するには、アプリケーションを実行するアプリケーション インスタンスの数を増加させることが必要な場合があります。

Azure Virtual Machine Scale Sets は、多数の VM で実行されるアプリケーションの管理機能、[リソースの自動スケーリング](virtual-machine-scale-sets-autoscale-overview.md)、トラフィックの負荷分散を備えています。 スケール セットには、次のような主な利点があります。

- **複数の VM の作成と管理が容易である**
    - アプリケーションを実行する VM が多数ある場合、環境全体で一貫した構成を維持することが重要です。 アプリケーションのパフォーマンスについて高い信頼性を実現するには、VM サイズ、ディスク構成、アプリケーション インストールがすべての VM で一致している必要があります。
    - スケール セットでは、すべての VM インスタンスが同一のベース OS イメージと構成から作成されます。 この方法を使用すると、追加の構成タスクまたはネットワーク管理を行うことなく、数百台の VM を容易に管理できます。
    - スケール セットでは、基本のレイヤー 4 トラフィック分散を実現する [Azure Load Balancer](../load-balancer/load-balancer-overview.md) と、より高度なレイヤー 7 トラフィック分散と TLS 終了を実現する [Azure Application Gateway](../application-gateway/overview.md) がサポートされています。

- **高可用性とアプリケーションの回復性を実現する**
    - スケール セットは、複数のインスタンスのアプリケーションを実行するために使用されます。 これらの VM インスタンスの 1 つに問題があっても、他のいずれかの VM インスタンスを通じて、顧客は最小限の中断で引き続きアプリケーションにアクセスできます。
    - 可用性を高めるために、[Availability Zones](../availability-zones/az-overview.md) を使用できます。単一のデータセンター内または複数のデータセンターで、スケール セットの VM インスタンスを自動的に分散します。

- **リソースの需要の変化に応じた、アプリケーションの自動スケーリングを可能にする**
    - アプリケーションに対する顧客の需要は、終日にわたってまたは一週間の中で変化することがあります。 スケール セットでは、顧客の需要に対応するために、アプリケーションの需要の増加に応じて VM インスタンスの数を自動的に増やしたり、需要の減少に応じて VM インスタンスの数を減らしたりできます。
    - また、自動スケーリングを行うと、需要が少ないときに、アプリケーションを実行する不要な VM インスタンスの数を最小限に抑えることができます。その一方で、需要の増加に応じて VM インスタンスが自動的に追加されるため、顧客は満足できるレベルのパフォーマンスの提供を受け続けることができます。 この機能は、必要に応じてコストを削減し、Azure リソースを効率的に作成するのに役立ちます。

- **大規模に動作する**
    - スケール セットでは、最大 1,000 個の VM インスタンスがサポートされます。 独自のカスタム VM イメージを作成してアップロードする場合、上限は 600 個の VM インスタンスになります。
    - 運用環境のワークロードで最高のパフォーマンスを実現するには、[Azure Managed Disks](../virtual-machines/managed-disks-overview.md) を使用してください。


## <a name="differences-between-virtual-machines-and-scale-sets"></a>仮想マシンとスケール セットの違い
スケール セットは、仮想マシンをベースに構築されます。 スケール セットには、アプリケーションの実行とスケーリングを行うための管理レイヤーと自動化レイヤーがあります。 代わりに、個別の VM を手動で作成して管理したり、既存のツールを統合して同様のレベルの自動化を構築したりすることもできます。 次の表では、複数の VM インスタンスを手動で管理する場合と比較した、スケール セットの利点をまとめています。

| シナリオ                           | 手動の VM グループ                                                                    | 仮想マシン スケール セット |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| 補助 VM インスタンスの追加        | 作成、構成、コンプライアンスの遵守が手動プロセス                             | 一元化された構成から自動で作成 |
| トラフィックのバランス調整と分散 | Azure ロード バランサーまたはアプリケーション ゲートウェイの作成と構成が手動プロセス      | Azure ロード バランサーまたはアプリケーション ゲートウェイの作成と統合を自動で実行可能 |
| 高可用性と冗長性   | 可用性セットの作成、または可用性ゾーンでの VM の分散および追跡が手動 | 可用性ゾーンまたは可用性セットでの VM インスタンスの自動分散 |
| VM のスケーリング                     | 手動による監視と Azure Automation                                                 | ホスト メトリック、ゲスト内メトリック、Application Insights、またはスケジュールに基づいた自動スケーリング |

スケール セットで追加料金が発生することはありません。 ユーザーは、VM インスタンス、ロード バランサー、管理ディスク ストレージなど、基本的なコンピューティング リソースに対してのみ支払います。 自動スケーリングや冗長性など、管理および自動化機能では、VM の使用以外に関する追加の料金は発生しません。

## <a name="how-to-monitor-your-scale-sets"></a>スケール セットを監視する方法

シンプルなオンボーディング プロセスを備え、スケール セット内の VM から CPU、メモリ、ディスク、ネットワークの重要なパフォーマンス カウンターを自動的に収集する [Azure Monitor for VMs](../azure-monitor/vm/vminsights-overview.md) を使用します。 他にもさまざまな監視機能や定義済みの視覚化機能が備わっているため、スケール セットの可用性とパフォーマンスを重点的に監視することができます。

ページ ビュー、アプリケーションの要求、例外など、アプリケーションに関する詳細情報を収集するには、Application Insights を使った[仮想マシン スケール セット アプリケーション](../azure-monitor/app/azure-vm-vmss-apps.md)の監視を有効します。 さらにアプリケーションの可用性を検証するには、ユーザー トラフィックをシミュレートする[可用性テスト](../azure-monitor/app/monitor-web-app-availability.md)を構成します。

## <a name="data-residency"></a>データの保存場所

Azure では、顧客データを 1 つのリージョンに格納できるようにする機能は現在、アジア太平洋地域の東南アジア リージョン (シンガポール) と、ブラジル地域のブラジル南部リージョン (サンパウロ州) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは geo 内に格納されます。 詳細については、[セキュリティ センター](https://azure.microsoft.com/global-infrastructure/data-residency/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
まずは、Azure Portal で最初の仮想マシン スケール セットを作成します。

> [!div class="nextstepaction"]
> [Azure Portal でスケール セットを作成する](quick-create-portal.md)
