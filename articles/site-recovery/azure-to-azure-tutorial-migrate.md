---
title: Azure Site Recovery を使用して Azure VM を別の Azure リージョンに移動する
description: Azure Site Recovery を使用して、Azure VM を異なる Azure リージョン間で移動します。
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 076adbfd4cecf7dae9ffc490e911fcb7ffce48e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394834"
---
# <a name="move-vms-to-another-azure-region"></a>VM を別の Azure リージョンに移動する

既存の Azure IaaS 仮想マシン (VM) を異なるリージョン間で移動する必要が生じるさまざまなシナリオがあります。 たとえば、既存の VM の信頼性と可用性を高めるケースや、管理を容易にするケース、またガバナンス上の理由で移動するケースも考えられます。 詳細については、[Azure VM の移動の概要](azure-to-azure-move-overview.md)に関するページを参照してください。 

[Azure Site Recovery](site-recovery-overview.md) サービスを使用すると、Azure VM をセカンダリ リージョンに移動できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> 
> * 移動の前提条件を確認する
> * ソース VM とターゲット リージョンを準備する
> * データをコピーしてレプリケーションを有効にする
> * 構成をテストして移動を実行する
> * ソース リージョンのリソースを削除する


> [!IMPORTANT]
> Azure VM を別のリージョンに移動する場合、[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) を使用することをお勧めします。 Resource Mover は、現在、パブリック プレビュー段階にあり、以下を実現します。
> - 単一のハブを使用してリージョン間でリソースを移動できます。
> - 移動時間と複雑さを軽減できます。 必要なものをすべて 1 か所にまとめることができます。
> - シンプルかつ一貫性のあるエクスペリエンスによって、さまざまな種類の Azure リソースを移動できます。
> - 移動するリソース間の依存関係を簡単に識別できます。 これにより、関連リソースをまとめて移動することができるため、移動後にすべてのリソースがターゲット リージョンで期待どおりに動作します。
> - 移動後にソース リージョンのリソースを削除する場合は、自動的にクリーンアップされます。
> - テスト。 移動を試してみて、完全に移動することは望まない場合は破棄することができます。



> [!NOTE]
> このチュートリアルでは、Azure VM をそのまま別のリージョンに移動する方法を説明しています。 可用性セット内の VM を別のリージョンのゾーン固定 VM に移動することによって可用性を高める必要がある場合は、[Azure VM を Availability Zones に移動するチュートリアル](move-azure-vms-avset-azone.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure VM が存在することを確認する。
- 選択した[ソース リージョンとターゲット リージョンの組み合わせがサポート対象](./azure-to-azure-support-matrix.md#region-support)であることを確認し、十分な情報を得たうえでターゲット リージョンを決定する。
- [シナリオのアーキテクチャとコンポーネント](azure-to-azure-architecture.md)を理解している。
- [サポートの制限と要件](azure-to-azure-support-matrix.md)を確認する。
- アカウントのアクセス許可を確認する。 自分で無料の Azure アカウントを作成した場合、自分がそのサブスクリプションの管理者になっています。 サブスクリプションの管理者でなければ、管理者に協力を求め、必要なアクセス許可を割り当てます。 VM のレプリケーションを有効にし、基本的に Azure Site Recovery を使用してデータをコピーするには、次の要件を満たす必要があります。

    - VM を Azure リソースに作成するアクセス許可。 "仮想マシン共同作成者" 組み込みロールには次のアクセス許可が与えられています。
    - 選択したリソース グループ内に VM を作成するためのアクセス許可
    - 選択した仮想ネットワーク内に VM を作成するためのアクセス許可
    - 選択したストレージ アカウントに書き込むためのアクセス許可
    
    - Azure Site Recovery の操作を管理するためのアクセス許可。 "Site Recovery 共同作成者" ロールには、Recovery Services コンテナーでの Site Recovery の操作の管理に必要なアクセス許可がすべて付与されています。

- 移動する Azure VM に、最新のルート証明書がすべて存在することを確認します。 最新のルート証明書が VM に存在しない場合、セキュリティ上の制約により、ターゲット リージョンへのデータのコピーが禁止されます。

- Windows VM については、最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows Update プロセスおよび証明書更新プロセスに従ってください。
    
- Linux VM の場合は、Linux ディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と証明書失効リストを取得します。
- 移動したい VM のネットワーク接続を制御するために認証プロキシを使用していないことを確認します。

- 移動しようとしている VM にインターネットへのアクセスが存在しない場合や、ファイアウォール プロキシを使用してアウトバウンド アクセスを制御している場合は、[要件を確認](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity)してください。

- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 これにはロード バランサー、ネットワーク セキュリティ グループ (NSG)、パブリック IP が含まれますが、それらに限定されません。

- 自分の Azure サブスクリプションで、ディザスター リカバリーに使用されるターゲット リージョンに VM を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 自分のサブスクリプションに、ソース VM と一致するサイズの VM をサポートできるだけのリソースがあることを確認します。 Site Recovery を使用してターゲットにデータをコピーする場合は、Site Recovery によって、ターゲット VM に対して同じサイズか、可能な限り近いサイズが選択されます。

- ソース ネットワーク レイアウトから特定されたすべてのコンポーネントについてターゲット リソースを作成します。 この手順は、ソース リージョンにあったすべての機能をターゲット リージョンの VM に確保するうえで重要となります。

     > [!NOTE] 
     > ソース VM のレプリケーションを有効にすると、Azure Site Recovery によって仮想ネットワークが自動的に検出されて作成されます。 また、自分でネットワークを事前に作成しておき、レプリケーションを有効にするユーザー フローの中で VM に割り当てることもできます。 その他のリソースは、後述のようにターゲット リージョンに手動で作成する必要があります。

    実際の環境に適した、最も一般的に使用されるネットワーク リソースをソース VM の構成に基づいて作成する場合は、次のドキュメントを参照してください。
    - [ネットワーク セキュリティ グループ](../virtual-network/manage-network-security-group.md)
    - [ロード バランサー](../load-balancer/index.yml)
    -  [パブリック IP](../virtual-network/virtual-network-public-ip-address.md)
    - その他のネットワーク コンポーネントについては、[ネットワークに関するドキュメント](../index.yml?pivot=products&panel=network)を参照してください。



## <a name="prepare"></a>準備
次の手順では、Azure Site Recovery をソリューションとして利用し、仮想マシンの移動を準備する方法を示します。 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>任意のリージョン (ソース リージョンを除く) にコンテナーを作成する

1. [Azure ポータル](https://portal.azure.com)
1. 検索で、「Recovery Services」と入力し、[Recovery Services コンテナー] をクリックします
1. [Recovery Services コンテナー] メニューの [+追加] をクリックします。
1. **[名前]** に、フレンドリ名 **ContosoVMVault** を指定します。 複数のサブスクリプションがある場合は、適切なものを選択します。
1. リソース グループ **ContosoRG** を作成します。
1. Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域を参照してください。
1. **[Recovery Services コンテナー]** で、 **[ContosoVMVault]** 、 **[レプリケートされたアイテム]** 、 **[+ 複製]** の順に選択します。
1. ドロップダウンで、 **[Azure Virtual Machines]** を選択します。
1. **[ソースの場所]** で、現在 VM が実行されているソースの Azure リージョンを選択します。
1. Resource Manager デプロイ モデルを選択します。 次に、**ソース サブスクリプション** と **ソース リソース グループ** を選択します。
1. **[OK]** を選択して設定を保存します。

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure VM のレプリケーションを有効にしてデータのコピーを開始する

Site Recovery は、サブスクリプションとリソース グループに関連付けられている VM の一覧を取得します。

1. 次の手順で、移動する VM を選択し、 **[OK]** を選択します。
1. **[設定]** で、 **[ディザスター リカバリー]** を選択します。
1. **[ディザスター リカバリーの構成]**  >  **[ターゲット リージョン]** で、レプリケート先のターゲット リージョンを選択します。
1. このチュートリアルでは、他の既定の設定をそのまま使用します。
1. **[レプリケーションを有効にする]** を選択します。 この手順により VM レプリケーションを有効にするジョブが開始されます。



## <a name="move"></a>[詳細ビュー]

次の手順では、ターゲット リージョンに移動する方法を示します。

1. コンテナーに移動します。 **[設定]**  >  **[レプリケートされたアイテム]** で目的の VM を選択してから、 **[フェールオーバー]** を選択します。
2. **[フェールオーバー]** で **[最新]** を選択します。
3. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recovery は、フェールオーバーを開始する前にソース VM をシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。
4. ジョブが完了したら、ターゲット Azure リージョンに VM が正しく表示されることを確認します。


## <a name="discard"></a>破棄 

移動した VM を確認した後、フェールオーバー ポイントを変更するか、元のポイントに戻す場合、 **[レプリケートされたアイテム]** で VM を右クリックし、 **[復旧ポイントの変更]** を選択します。 この手順で、別の復旧ポイントを指定し、そこにフェールオーバーする選択肢が与えられます。 


## <a name="commit"></a>Commit 

移動後の VM を確認し、変更をコミットする準備ができたら、 **[レプリケートされたアイテム]** で VM を右クリックし、 **[コミット]** を選択します。 これでターゲット リージョンへの移動プロセスは完了です。 コミット ジョブが完了するまでお待ちください。

## <a name="clean-up"></a>クリーンアップ

次の手順では、ソース リージョンと、移動に利用された関連リソースをクリーンアップする方法を段階的に説明します。

移動に使用されたすべてのリソースの場合:

- VM に移動します。 **[レプリケーションの無効化]** を選択します。 これで VM のデータをコピーするプロセスが停止します。

   > [!IMPORTANT]
   > これは、Azure Site Recovery レプリケーションの請求を避けるうえで重要な手順となります。

ソース リソースを再利用する予定がない場合、次の追加手順を実行します。

1. 「[前提条件](#prerequisites)」で確認した、ソース リージョンの関連ネットワーク リソースをすべて削除します。
1. 対応するストレージ アカウントをソース リージョンから削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure VM を別の Azure リージョンに移動しました。 引き続き、移動した VM のディザスター リカバリーを構成することができます。

> [!div class="nextstepaction"]
> [移行後のディザスター リカバリーのセットアップ](azure-to-azure-quickstart.md)
