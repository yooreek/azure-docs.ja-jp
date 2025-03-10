---
title: Azure AD Connect 構成設定をインポートおよびエクスポートする方法
description: この記事では、クラウド プロビジョニングに関してよく寄せられる質問について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67460c654c854c5a855560dde1d67732fa818c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681957"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Azure AD Connect 構成設定をインポートおよびエクスポートする 

Azure Active Directory (Azure AD) Connect のデプロイは、1 つのフォレストの簡易モード インストールから、カスタム同期ルールを使用して複数のフォレスト間で同期する複雑なデプロイまでさまざまです。 構成オプションとメカニズムの数が多いため、どの設定が有効であるかを理解し、同じ構成を使用してサーバーを迅速にデプロイできることが重要です。 この機能により、特定の同期サーバーの構成をカタログ化し、その設定を新しいデプロイにインポートできるようになります。 異なる同期設定のスナップショットを比較して、2 つのサーバー間の違いや、同じサーバーの経時的変化を簡単に視覚化することができます。

Azure AD Connect ウィザードから構成が変更されるたびに、新しいタイム スタンプ付きの JSON 設定ファイルが  **%ProgramData%\AADConnect** に自動的にエクスポートされます。 設定ファイル名の形式は **Applied-SynchronizationPolicy-*.JSON** です。ファイル名の最後の部分はタイム スタンプです。

> [!IMPORTANT]
> Azure AD Connect によって行われた変更のみが自動的にエクスポートされます。 PowerShell、Synchronization Service Manager、または Synchronization Rules Editor を使用して行った変更は、最新のコピーを維持するために必要に応じてオンデマンドでエクスポートする必要があります。 また、オンデマンドのエクスポートは、ディザスター リカバリーの目的で安全な場所に設定のコピーを配置することにも使用できます。

## <a name="export-azure-ad-connect-settings"></a>Azure AD Connect 設定のエクスポート 

構成設定の概要を表示するには、Azure AD Connect ツールを開き、 **[現在の構成を表示する]** という名前の追加のタスクを選択します。 サーバーの完全な構成をエクスポートする機能と共に、設定の簡単な概要が表示されます。

既定では、設定は **%ProgramData%\AADConnect** にエクスポートされます。 障害が発生した場合の可用性を確保するために、設定を保護された場所に保存することもできます。 設定は JSON ファイル形式を使用してエクスポートされるので、論理的な一貫性を確保するために手動で作成したり編集したりしないでください。 手動で作成または編集されたファイルのインポートはサポートされていないため、予期しない結果が生じる可能性があります。

## <a name="import-azure-ad-connect-settings"></a>Azure AD Connect 設定のインポート

以前にエクスポートした設定をインポートするには:
 
1. 新しいサーバーに **Azure AD Connect** をインストールします。
1. **ウェルカム** ページの後に **[カスタマイズ]** を選択します。
1. **[同期設定をインポート]** を選択します。 以前にエクスポートした JSON 設定ファイルを参照します。
1. **[インストール]** を選択します。

   ![[必須コンポーネントのインストール] 画面を示すスクリーンショット](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> LocalDB の代わりに SQL Server を使用したり、既定の VSA の代わりに既存のサービス アカウントを使用したりするなど、このページの設定をオーバーライドします。 これらの設定は、構成設定ファイルからインポートされません。 これらは、情報と比較のために用意されています。

### <a name="import-installation-experience"></a>インストール エクスペリエンスのインポート 

インストール エクスペリエンスのインポートは、既存のサーバーの再現性を容易に提供するために、ユーザーからの最小限の入力で意図的にシンプルに保持されています。

インストール エクスペリエンス中に行うことができるのは、以下の変更だけです。 インストールした後に Azure AD Connect ウィザードから、その他のすべての変更を加えることができます。
- **Azure Active Directory の資格情報**:既定では、元のサーバーを構成するために使用された Azure 全体管理者のアカウント名が提案されます。 情報を新しいディレクトリに同期する場合は、変更する "*必要があります*"。 
- **ユーザー サインイン**:元のサーバーに構成されたサインオン オプションが既定で選択され、資格情報や構成時に必要なその他の情報が自動的に要求されます。 まれに、アクティブなサーバーの動作が変更されないように、別のオプションを使用してサーバーを設定する必要がある場合があります。 それ以外の場合は、 **[次へ]** を選択して同じ設定を使用します。
- **オンプレミス ディレクトリ資格情報**:同期設定に含まれるオンプレミス ディレクトリごとに、同期アカウントを作成するための認証情報を提供するか、事前に作成したカスタム同期アカウントを指定する必要があります。 この手順は、ディレクトリを追加または削除できないことを除いて、クリーン インストール エクスペリエンスと同じです。
- **[構成オプション]** : クリーン インストールの場合と同様に、自動同期を開始するか、ステージング モードを有効にするかの初期設定を構成することができます。 主な違いは、結果を Azure に積極的にエクスポートする前に、構成と同期の結果を比較できるように、ステージング モードが意図的に既定で有効になっていることです。

![[ディレクトリの接続] 画面を示すスクリーンショット](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> プライマリ ロールにして、構成の変更を Azure にアクティブにエクスポートできるのは、1 つの同期サーバーだけです。 その他のサーバーはすべて、ステージング モードにする必要があります。

## <a name="migrate-settings-from-an-existing-server"></a>既存のサーバーから設定を移行する 

既存のサーバーで設定管理がサポートされていない場合は、サーバーをインプレース アップグレードするか、新しいステージング サーバーで使用するために設定を移行するかを選択できます。

移行には、新しいインストールで使用するために既存の設定を抽出する PowerShell スクリプトを実行する必要があります。 既存のサーバーの設定をカタログ化し、それを新しくインストールしたステージング サーバーに適用するには、この方法を使用します。 元のサーバーの設定を新しく作成したサーバーと比較すると、サーバー間の変更がすぐに視覚化されます。 いつもと同じように、組織の認定プロセスに従って、追加の構成が不要であることを確実にします。

### <a name="migration-process"></a>移行プロセス 
設定を移行するには:

1. 新しいステージング サーバーで **AzureADConnect.msi** を開始し、Azure AD Connect の **ウェルカム** ページで停止します。

1. **MigrateSettings.ps1** を、Microsoft Azure AD Connect\Tools ディレクトリから既存のサーバー上の場所にコピーします。 例は C:\setup になっています。この setup は既存のサーバー上に作成されたディレクトリです。

   ![Azure AD Connect ディレクトリを示すスクリーンショット。](media/how-to-connect-import-export-config/migrate1.png)

1. ここに示されているようにスクリプトを実行し、下位レベルのサーバー構成ディレクトリ全体を保存します。 このディレクトリを新しいステージング サーバーにコピーします。 **Exported-ServerConfiguration-** * フォルダー全体を新しいサーバーにコピーする必要があります。

   ![Windows PowerShell でスクリプトが示されているスクリーンショット。](media/how-to-connect-import-export-config/migrate2.png)
   ![Exported-ServerConfiguration-* フォルダーをコピーしているところを示しているスクリーンショット。](media/how-to-connect-import-export-config/migrate3.png)

1. デスクトップのアイコンをダブル クリックして **Azure AD Connect** を起動します。 Microsoft ソフトウェア ライセンス条項に同意し、次のページで **[カスタマイズ]** を選択します。
1. **[同期設定をインポート]** チェック ボックスをオンにします。 **[参照]** を選択して、コピーした Exported-ServerConfiguration-* フォルダーを参照します。 MigratedPolicy.json を選択して、移行した設定をインポートします。

   ![[同期設定をインポート] オプションが示されているスクリーンショット。](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>インストール後の検証 

新しくデプロイされたサーバーの最初にインポートされた設定ファイルを、エクスポートされた設定ファイルと比較することは、意図していたデプロイと結果のデプロイとの違いを理解するうえで不可欠な手順です。 お気に入りの横並びのテキスト比較アプリケーションを使用すると、必要な変更または偶発的な変更をすばやく強調表示する瞬時の視覚化が得られます。

以前は手動だった多くの構成手順が廃止されましたが、引き続き、組織の認定プロセスに従って、追加の構成が不要であることを確実にします。 この構成は、このリリースの設定管理には現在取り込まれていない詳細設定を使用した場合に発生する可能性があります。

既知の制限事項を次に示します。
- **同期規則**:Microsoft の標準ルールとの競合を避けるために、カスタム ルールの優先順位は予約済みの 0 から 99 の範囲にする必要があります。 予約済みの範囲外にカスタム ルールを配置すると、標準ルールが構成に追加されるときに、カスタム ルールが移動される可能性があります。 構成に変更された標準ルールが含まれている場合も、同様の問題が発生します。 標準ルールを変更することは避けてください。ルールの配置が正しくなくなる可能性があります。
- **デバイス ライトバック**:これらの設定は、カタログ化されています。 これらは現在、構成時には適用されません。 元のサーバーでデバイス ライトバックが有効になっている場合は、新しくデプロイされたサーバーでこの機能を手動で構成する必要があります。
- **同期されたオブジェクトの種類**:Synchronization Service Manager を使用して同期されたオブジェクトの種類 (ユーザー、連絡先、グループなど) のリストを制限することは可能ですが、この機能は現在、同期設定ではサポートされていません。 インストールが完了したら、詳細構成を手動で再適用する必要があります。
- **カスタム実行プロファイル**:Synchronization Service Manager を使用して実行プロファイルの既定のセットを変更することはできますが、この機能は現在、同期設定ではサポートされていません。 インストールが完了したら、詳細構成を手動で再適用する必要があります。
- **プロビジョニング階層の構成**:Synchronization Service Manager のこの高度な機能は、同期設定ではサポートされていません。 初期デプロイの完了後に手動で再構成する必要があります。
- **Active Directory Federation Services (AD FS) と PingFederate 認証**:これらの認証機能に関連付けられているサインオン方法は自動的に事前選択されます。 その他の必要なすべての構成パラメーターを対話形式で指定する必要があります。
- **無効なカスタム同期規則が有効としてインポートされる**:無効なカスタム同期規則が有効としてインポートされます。 新しいサーバーでも必ず無効にしてください。

 ## <a name="next-steps"></a>次のステップ

- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [カスタマイズした設定](how-to-connect-install-custom.md)
- [Azure AD Connect Health エージェントをインストールする](how-to-connect-health-agent-install.md) 
