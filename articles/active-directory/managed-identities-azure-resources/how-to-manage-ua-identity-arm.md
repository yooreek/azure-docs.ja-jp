---
title: Azure Resource Manager を使用してユーザー割り当てマネージド ID を作成および削除する
description: Azure Resource Manager を使用して、ユーザー割り当てマネージド ID を作成および削除する手順について説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbeb8e0fc638955995cc5aaf00f58d40adc69a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184879"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Azure Resource Manager を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する


Azure リソースのマネージド ID は、Azure Active Directory で管理される ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure Resource Manager を使用してユーザー割り当てマネージド ID を作成します。

Azure Resource Manager テンプレートを使用して、ユーザー割り当てマネージド ID を一覧表示および削除することはできません。  ユーザー割り当てマネージド ID の作成と一覧表示については、次の記事を参照してください。

- [ユーザー割り当てマネージド ID を一覧表示する](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [ユーザー割り当てマネージド ID を削除する](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="template-creation-and-editing"></a>テンプレートの作成と編集

Azure Portal とスクリプトの場合と同じように、Azure Resource Manager テンプレートは、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイする機能を提供します。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。

- [Azure Marketplace のカスタム テンプレート](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)を使用します。これにより、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)に基づいてテンプレートを作成したりできます。
- [元のデプロイ](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)または[デプロイの現在の状態](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
- ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) を使用してから、PowerShell または CLI を使用してアップロードおよびデプロイします。
- Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)を使用して、テンプレートを作成およびデプロイします。 

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を作成するには、次のテンプレートを使用します。 `<USER ASSIGNED IDENTITY NAME>` は実際の値に置き換えます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>次のステップ

Azure Resource Manager テンプレートを使用して Azure VM にユーザー割り当てマネージド ID を割り当てる方法については、[テンプレートを使用したAzure VM 上の Azure リソースのマネージド ID の構成](qs-configure-template-windows-vm.md)に関するページを参照してください。


