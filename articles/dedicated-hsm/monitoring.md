---
title: 監視オプション - Azure 専用 HSM | Microsoft Docs
description: Azure 専用 HSM の監視オプションと監視責任の概要です
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c3d062d615208177fcb9c7df511f598613bf6a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092705"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure Dedicated HSM の監視

Azure Dedicated HSM サービスでは、完全な管理制御と管理責任が備えられた、お客様専用の物理デバイスが提供されます。 使用できるようになったデバイスは、[Thales Luna 7 HSM モデル A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) です。  お客様がプロビジョニングを行った後は、監視の役割で物理シリアル ポートに接続することを除き、Microsoft による管理アクセスはできなくなります。 その結果、包括的な監視やログ分析などの一般的な運用作業はお客様が担当することになります。
お客様は、HSM を使用しているアプリケーションについての全責任を負い、サポートまたはコンサルティングの支援を受けるために Thales と連携する必要があります。 運用上の健全性についてお客様が所有する範囲に基づき、Microsoft はこのサービスについていかなる高可用性も保証することはできません。 高可用性を実現するためにアプリケーションが確実に正しく構成されているようにするのは、お客様の責任です。 Microsoft は、デバイスの正常性とネットワーク接続の監視および管理を行います。

## <a name="microsoft-monitoring"></a>Microsoft による監視

使用中の Thales Luna 7 HSM デバイスには、デバイスを監視するためのオプションとして既定で SNMP とシリアル ポートがあります。 Microsoft は、物理的な手段としてシリアル ポート接続を使用してデバイスに接続し、デバイスの正常性に関する基本的なテレメトリを取得します。 これには、温度などの項目と、電源やファンなどのコンポーネントの状態が含まれます。
これを実現するために、Microsoft は Thales デバイスに設定された管理者ではない "モニター" ロールを使用しています。 このロールによってテレメトリを取得する機能が付与されますが、管理タスクの観点で、または暗号化に関する情報を表示できる方法でデバイスにアクセスすることはできません。 お客様は、ご自分のデバイスで安心して機密性の高い暗号キー ストレージを管理、統制、使用することができます。 基本的な正常性監視に対するこのような最小限のアクセスでは不満をお持ちのお客様がいる場合は、監視アカウントを無効にするオプションがあります。 その結果、当然ながら Microsoft は情報を取得できなくなり、デバイスの正常性の問題を事前に通知できなくなります。 このような状況では、お客様がデバイスの正常性に責任を負います。
モニター機能自体は、正常性データを取得するために 10 分ごとにデバイスをポーリングするように設定されています。 シリアル通信にはエラーが発生しやすい性質があるため、1 時間に複数回の負の正常性インジケーターが認められた場合にのみ、アラートが生成されます。 このアラートは、最終的には、問題を通知する事前のお客様への通信になります。
問題の性質によっては、影響を軽減し、リスクの低い修正を確実に行うために適切な一連の操作が行われます。 たとえば、電源障害は、結果として改ざんイベントが発生しないホットスワップ手順なので、運用に対する影響が少なく、最小限のリスクで実行できます。 他の手順では、お客様に対するセキュリティ リスクを最小限に抑えるために、デバイスのゼロ化処理とプロビジョニング解除が必要になる可能性があります。 このような場合、お客様は代替デバイスをプロビジョニングし、高可用なペアリングに再び参加することで、デバイスの同期をトリガーします。 通常の運用は、中断を最小限に抑え、セキュリティ リスクを最小限に抑えて、最短の時間で再開されます。  

## <a name="customer-monitoring"></a>お客様による監視

Dedicated HSM サービスの価値提案は、お客様が、特にクラウド配信デバイスであることを考慮して、デバイスを制御できることです。 この制御の結果として、デバイスの正常性を監視および管理する責任があります。 Thales Luna 7 HSM デバイスには、SNMP および Syslog 実装のガイダンスが付属しています。 Dedicated HSM サービスのお客様には、Microsoft モニター アカウントがアクティブのままであってもこれを使用することをお勧めします。また、Microsoft モニター アカウントを無効にしている場合は必須と考えてください。
いずれの手法を使用する場合でも、お客様は問題を特定し、Microsoft のサポートに連絡して適切な修正作業を始めることができます。

## <a name="next-steps"></a>次のステップ

どのようなデバイスでも、プロビジョニングやアプリケーションの設計、アプリケーションのデプロイ前に、高可用性やセキュリティなど、サービスのすべての主要概念を十分に理解しておくことをお勧めします。 その他の概念レベルのトピックを次に示します。

* [高可用性](high-availability.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [サポート可能性](supportability.md)
