---
title: 'Azure クイックスタート: Azure portal で最初の Batch ジョブを実行する'
description: このクイックスタートでは、Azure portal を使用して、Batch アカウント、コンピューティング ノードのプール、そのプールで基本的なタスクを実行するジョブを作成する方法について説明します。
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: mvc
ms.openlocfilehash: 1234a932a732cdb6fda1c412a423ae0b1ea089e9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184017"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>クイック スタート: Azure Portal で最初の Batch ジョブを実行する

Azure portal を使用して Batch アカウント、コンピューティング ノード (仮想マシン) のプール、そのプールでタスクを実行するジョブを作成することによって、Azure Batch の使用を開始します。 このクイックスタートを完了すると、Batch サービスの主要な概念を理解し、より大規模でより現実的なワークロードで Batch を試せるようになります。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-batch-account"></a>Batch アカウントを作成する

テスト目的でサンプルの Batch アカウントを作成するには、次の手順に従います。 プールおよびジョブを作成するには Batch アカウントが必要です。 ここで示すように、Azure ストレージ アカウントを Batch アカウントとリンクできます。 このクイック スタートには必要ありませんが、ストレージ アカウントは、アプリケーションをデプロイしたり、ほとんどの実際のワークロードの入力データと出力データを保存したりする際に役立ちます。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[Compute]**  >  **[Batch サービス]** の順に選択します。 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Azure Marketplace の Batch サービスのスクリーンショット。":::

1. **[リソース グループ]** フィールドで、 **[新規作成]** を選択し、リソース グループの名前を入力します。

1. **[アカウント名]** に値を入力します。 選択した Azure の **[場所]** 内で一意となる名前を使用してください。 使用できるのは小文字と数字のみです。文字数は 3 から 24 文字にする必要があります。

1. **[ストレージ アカウント]** で、既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。

1. 他の設定は変更しないでください。 **[確認および作成]** 、 **[作成]** の順に選択して Batch アカウントを作成します。

"**展開が成功しました**" というメッセージが表示されたら、作成した Batch アカウントに移動します。

## <a name="create-a-pool-of-compute-nodes"></a>コンピューティング ノードのプールの作成

Batch アカウントが用意できたら、テスト目的で Windows コンピューティング ノードのサンプル プールを作成します。 この簡単な例のプールは、Azure Marketplace の Windows Server 2019 イメージを実行する 2 つのノードで構成されます。

1. Batch アカウントで、 **[プール]**  >  **[追加]** の順に選択します。

1. *mypool* という **プール ID** を入力します。

1. **[オペレーティング システム]** で、次の設定を選択します (他のオプションを見つけることができます)。
  
   |設定  |値  |
   |---------|---------|
   |**イメージの種類**|マーケットプレース|
   |**発行元**     |microsoftwindowsserver|
   |**プラン**     |windowsserver|
   |**SKU**     |2019-datacenter-core-smalldisk|

1. 下にスクロールして、**[ノード サイズ]** と **[スケール]** の設定を入力します。 推奨されるノード サイズは、この簡単な例についてパフォーマンスとコストのバランスが取れています。
  
   |設定  |値  |
   |---------|---------|
   |**ノード価格レベル**     |Standard A1|
   |**ターゲットの専用ノード数**     |2|

1. 残りの設定は既定値のままにし、**[OK]** を選択して、プールを作成します。

Batch によってすぐにプールが作成されますが、コンピューティング ノードを割り当てて開始するには数分かかります。 この間は、プールの **[割り当ての状態]** が **[サイズ変更中]** になります。 プールのサイズ変更中に、先に進んでジョブやタスクを作成できます。

数分後、割り当ての状態が **[安定]** に変わり、ノードが開始されます。 ノードの状態を確認するには、プールを選択し、 **[ノード]** を選択します。 ノードの状態が **[アイドル]** の場合は、タスクを実行する準備が整っています。

## <a name="create-a-job"></a>ジョブの作成

プールを作成できたら、そこで実行するジョブを作成します。 Batch ジョブは、1 つ以上のタスクの論理グループです。 ジョブには、優先度やタスクの実行対象プールなど、タスクに共通する設定が含まれています。 最初、ジョブにはタスクがありません。

1. Batch アカウント ビューで、 **[ジョブ]**  >  **[追加]** の順に選択します。

1. *myjob* という **ジョブ ID** を入力します。 **[プール]** で *mypool* を選択します。 残りの設定は既定値のままにして、**[OK]** を選択します。

## <a name="create-tasks"></a>タスクを作成する

次に、ジョブを選択して **[タスク]** ページを開きます。 ここは、ジョブで実行するサンプル タスクを作成する場所です。 通常、複数のタスクを作成します。これらのタスクは、各コンピューティング ノードで実行できるように Batch によってキューに入れられ、分散されます。 この例では、同じタスクを 2 つ作成します。 各タスクでコマンド ラインを実行してコンピューティング ノードで Batch 環境変数を表示した後、90 秒待ちます。

Batch を使用する場合、コマンド ラインは、アプリまたはスクリプトを指定する場所です。 Batch には、アプリやスクリプトをコンピューティング ノードにデプロイする方法が多数用意されています。

最初のタスクを作成するには:

1. **[追加]** を選択します。

1. *mytask* という **タスク ID** を入力します。

1. **[コマンド ライン]** に、「`cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`」と入力します。 残りの設定は既定値のままにして、 **[送信]** を選択します。

作成したタスクは、プールで実行するために Batch によってキューに登録されます。 ノードが実行できるようになると、タスクが実行されます。

2 番目のタスクを作成するには、上記の手順を繰り返します。 別の **タスク ID** を入力しますが、同じコマンド ラインを指定します。 最初のタスクがまだ実行中の場合、Batch は、プール内の別のノードに対して 2 つ目のタスクを開始します。

## <a name="view-task-output"></a>タスク出力の表示

作成したタスクの例は、数分で完了します。 完了したタスクの出力を表示するには、タスクを選択してから **[ノード上のファイル]** を選択します。 `stdout.txt` ファイルを選択して、タスクの標準出力を表示します。 内容は次のようになります。

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="完了したタスクの出力のスクリーンショット。":::

内容は、ノードで設定されている Azure Batch 環境変数を示します。 独自の Batch ジョブとタスクを作成すると、これらの環境変数は、タスクのコマンド ラインのほか、コマンド ラインにより実行されるプログラムとスクリプトで参照できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Batch のチュートリアルとサンプルを続行する場合は、このクイック スタートで作成した Batch アカウントとリンクされているストレージ アカウントを使用します。 Batch アカウント自体の料金は発生しません。

ジョブがスケジュールされていない場合でも、ノードの実行中はプールに対して料金が発生します。 プールは不要になったら、削除してください。 アカウント ビューで、**[プール]** およびプールの名前を選択します。 次に、 **[削除]** を選択します。  プールを削除すると、ノード上のタスク出力はすべて削除されます。

リソース グループ、Batch アカウント、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、Batch アカウントのリソース グループを選択し、**[リソース グループの削除]** を選択してください。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Batch アカウント、Batch プール、Batch ジョブを作成しました。 このジョブによってサンプル タスクが実行されたため、いずれかのノード上に作成された出力が表示されました。 Batch サービスの主要な概念を理解できたので、より大規模でより現実的なワークロードを使用して Batch を試す準備が整いました。 Azure Batch の詳細については、Azure Batch のチュートリアルを続行してください。

> [!div class="nextstepaction"]
> [Azure Batch のチュートリアル](./tutorial-parallel-dotnet.md)
