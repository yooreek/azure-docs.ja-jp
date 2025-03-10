---
title: クイックスタート - Azure CLI を使用して Azure IoT Hub Device Provisioning Service をセットアップする
description: クイックスタート - Azure CLI を使用して Azure IoT Hub Device Provisioning Service (DPS) をセットアップする
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 611068fa020321be88be6e1d6da663266029c658
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660187"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>クイック スタート:Azure CLI で IoT Hub Device Provisioning Service をセットアップする

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイックスタートでは、Azure CLI を使用して IoT ハブと IoT Hub Device Provisioning Service を作成し、2 つのサービスをリンクする方法について詳しく説明します。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> このクイックスタートで作成する IoT ハブとプロビジョニング サービスは、どちらも DNS エンドポイントとして公開されます。 これらのリソースに使用される名前を変更する場合は、機密性の高い情報を含めないようにしてください。
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*my-sample-resource-group* という名前のリソース グループを場所 *westus* に作成します。

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> この例では、米国西部にリソース グループが作成されます。 `az account list-locations -o table` コマンドを実行すると、利用できる場所を一覧表示できます。
>
>

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) コマンドを使用して、IoT Hub を作成します。

次の例では、*my-sample-hub* という名前の IoT ハブを場所 *westus* に作成します。 IoT ハブ名は Azure でグローバルに一意である必要があるため、一意のプレフィックスまたはサフィックスを例の名前に追加するか、まったく新しい名前を選択できます。 名前は、IoT ハブの適切な名前付け規則に従う必要があります。長さは 3 から 50 文字にする必要があるほか、使用できるのは大文字または小文字の英数字とハイフン ("-") のみです。 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Device Provisioning Service の作成

[az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) コマンドを使用して、Device Provisioning Service を作成します。 

次の例では、*my-sample-dps* という名前のプロビジョニング サービスを場所 *westus* に作成します。 また、独自のプロビジョニング サービスに対して、グローバルに一意の名前を選択する必要があります。 名前は、IoT Hub Device Provisioning Service の適切な名前付け規則に従う必要があります。長さは 3 から 64 文字にする必要があるほか、使用できるのは大文字または小文字の英数字とハイフン ("-") のみです。

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> この例では、米国西部にプロビジョニング サービスが作成されます。 使用可能な場所の一覧を表示するには、`az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` コマンドを実行するか、[Azure の状態](https://azure.microsoft.com/status/)ページに移動して "Device Provisioning Service" を検索します。 コマンドでは、場所は 1 単語または複数単語の形式で指定できます。たとえば、westus、West US、WEST US などです。この値で、大文字と小文字は区別されません。 複数単語形式で場所を指定する場合は、値を引用符で囲みます。たとえば、`--location "West US"` のようにします。
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>IoT ハブの接続文字列の取得

IoT ハブを Device Provisioning サービスとリンクするには、IoT ハブの接続文字列が必要です。 [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) コマンドを使用して接続文字列を取得し、それを使用して、2 つのリソースをリンクするときに使用する変数を設定します。 

次の例では、*hubConnectionString* 変数を、ハブの *iothubowner* ポリシー (`--policy-name` パラメーターを使用すると、別のポリシーを指定できます) の主キーの接続文字列の値に設定します。 *my-sample-hub* を、前に選択した一意の IoT ハブ名に交換します。 このコマンドでは、Azure CLI の [query](/cli/azure/query-azure-cli) と [output](/cli/azure/format-output-azure-cli#tsv-output-format) オプションを使用して、コマンド出力から接続文字列を抽出します。

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

接続文字列を表示するには、`echo` コマンドを使用します。

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> この 2 つのコマンドは、Bash で実行されているホストに対して有効です。
> 
> ローカルの Windows (CMD) シェルまたは PowerShell ホストを使用している場合は、その環境での正しい構文を使用するようにコマンドを変更してください。
>
> Azure Cloud Shell を使用している場合は、シェル ウィンドウの左側にある環境ドロップダウンで **[Bash]** が選択されていることを確認します。
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>IoT ハブとプロビジョニング サービスのリンク

IoT ハブとプロビジョニング サービスをリンクするには、[az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create) コマンドを使用します。 

次の例では、場所 *westus* の *my-sample-hub* という名前の IoT ハブと、*my-sample-dps* という名前の Device Provisioning Service をリンクします。 これらの名前を、前に選択した一意の IoT ハブ名および Device Provisioning Service 名に交換します。 このコマンドは、前の手順で *hubConnectionString* 変数に格納された IoT ハブの接続文字列を使用します。

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

コマンドが完了するまでに数分かかる場合があります。

## <a name="verify-the-provisioning-service"></a>プロビジョニング サービスの確認

[az iot dps show](/cli/azure/iot/dps#az-iot-dps-show) コマンドを使用して、プロビジョニング サービスの詳細を取得します。

次の例では、*my-sample-dps* という名前のプロビジョニング サービスの詳細を取得します。 この名前を、独自の Device Provisioning Service 名に交換します。

```azurecli-interactive
az iot dps show --name my-sample-dps
```
リンクされた IoT ハブは、*properties.iotHubs* コレクションに表示されます。

![プロビジョニング サービスの確認](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイックスタートは、このクイックスタートに基づいています。 引き続きクイックスタートまたはチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 作業を続けない場合は、以下のコマンドを使用して、プロビジョニング サービス、IoT ハブ、またはリソース グループとそのすべてのリソースを削除することができます。 以下に記述されているリソースの名前を、独自のリソースの名前に置き換えてください。

プロビジョニング サービスを削除するには、[az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete) コマンドを実行します。

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
IoT ハブを削除するには、[az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete) コマンドを実行します。

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

リソース グループとそのすべてのリソースを削除するには、[az group delete](/cli/azure/group#az-group-delete) コマンドを実行します。

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT ハブと Device Provisioning Service インスタンスをデプロイし、この 2 つのリソースをリンクしました。 ここで行った設定を使用して、シミュレートされたデバイスをプロビジョニングする方法については、シミュレートされたデバイスの作成に関するクイックスタートを参照してください。

> [!div class="nextstepaction"]
> [シミュレートされたデバイスを作成するためのクイックスタート](./quick-create-simulated-device.md)
