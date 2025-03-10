---
title: 自動 ML 分類モデルを作成する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の自動機械学習 (自動 ML) インターフェイスを使用して分類モデルをトレーニングおよびデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: ad8a9f7af9ddabe969d090f80378ba5ff891d7f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691945"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>チュートリアル:Azure Machine Learning の自動 ML で分類モデルを作成する


このチュートリアルでは、Azure Machine Learning Studio で自動機械学習を使用して、1 行のコードも記述せずに単純な分類モデルを作成する方法について説明します。 この分類モデルは、クライアントが金融機関に定期預金を申し込むかどうかを予測します。

自動機械学習を使用することで、時間がかかるタスクを自動化することができます。 自動機械学習では、アルゴリズムとハイパーパラメーターのさまざまな組み合わせをすばやく反復し、選択された成功のメトリックに基づいて最適なモデルを効率的に発見します。

時系列予測の例については、「[チュートリアル: 需要予測と AutoML](tutorial-automated-ml-forecast.md)」を参照してください。

このチュートリアルでは、次のタスクを実施する方法について説明します。

> [!div class="checklist"]
> * Azure Machine Learning ワークスペースを作成します。
> * 自動機械学習の実験を実行します。
> * 実験の詳細を表示します。
> * モデルをデプロイします。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://aka.ms/AMLFree)を作成してください。

* [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) データ ファイルをダウンロードします。 **[y]** 列では、このチュートリアルの予測対象列として後で識別される定期預金に顧客が申し込んだかどうかが示されます。 

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、サービス内の簡単に使用できるオブジェクトに結び付けます。 

[ワークスペースを作成する方法](how-to-manage-workspace.md)は多数あります。 このチュートリアルでは、Azure リソースを管理するための Web ベースのコンソールである Azure portal を使用してワークスペースを作成します。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> お使いの **ワークスペース** と **サブスクリプション** をメモしておきます。 これらは、適切な場所に実験を作成するために必要になります。 

## <a name="get-started-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio で開始する

https://ml.azure.com で Azure Machine Learning Studio を使用して、次の実験の設定を完了し、ステップを実行します。Azure Machine Learning Studio は、あらゆるスキル レベルのデータ サイエンス実務者がデータ サイエンス シナリオを実行するための機械学習ツールを含む統合 Web インターフェイスです。 Internet Explorer ブラウザーでは、Studio はサポートされません。

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. お使いのサブスクリプションと、作成したワークスペースを選択します。

1. **[Get started]\(作業を開始する\)** を選択します。

1. 左側のウィンドウの **[Author]\(作成者\)** セクションで **[Automated ML]\(自動 ML\)** を選択します。

   これは初めての自動 ML 実験であるため、空のリストとドキュメントへのリンクが表示されます。

   ![開始ページ](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. **[+New automated ML run]\(+新しい自動 ML の実行\)** を選択します。 

## <a name="create-and-load-dataset"></a>データセットを作成して読み込む

実験を構成する前に、Azure Machine Learning データセットの形式でデータ ファイルをワークスペースにアップロードします。 そうすることで、データを実験に適した形式にすることができます。

1. 新しいデータセットを作成するには、 **[+ データセットの作成]** ドロップダウンで **[From local files]\(ローカル ファイルから\)** を選択します。 

    1. **[基本情報]** フォームでデータセットに名前を付け、必要に応じて説明を入力します。 自動 ML インターフェイスでは、現在、TabularDatasets だけがサポートされています。そのため、データセットの種類は既定で "*表形式*" に設定されます。

    1. 左下の **[次へ]** を選択します

    1. **[データストアとファイルの選択]** フォームで、ワークスペースの作成時に自動的に設定された既定のデータストア、**workspaceblobstore (Azure Blob Storage)** を選択します。 データ ファイルは、ここにアップロードすることで、ワークスペースから利用できるようになります。

    1. **[参照]** を選択します。
    
    1. ローカル コンピューター上の **bankmarketing_train.csv** ファイルを選択します。 これは、[前提条件](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)としてダウンロードしたファイルです。

    1. データセットに一意の名前を付け、必要に応じて説明を入力します。 

    1. 左下の **[次へ]** を選択して、ワークスペースの作成時に自動的に設定された既定のコンテナーにアップロードします。  
    
       アップロードが完了すると、ファイルの種類に基づいて [Settings and preview]\(設定とプレビュー\) フォームが事前設定されます。 
       
    1. **[Settings and preview]\(設定とプレビュー\)** フォームが次のように設定されていることを確認し、 **[次へ]** を選択します。
        
        フィールド|説明| チュートリアルの値
        ---|---|---
        ファイル形式|ファイルに格納されているデータのレイアウトと種類を定義します。| 区切り記号
        区切り記号|プレーンテキストまたは他のデータ ストリーム内の個別の独立した領域の間の境界を指定するための&nbsp;1 つ以上の文字。 |コンマ
        エンコード|データセットの読み取りに使用する、ビットと文字のスキーマ テーブルを識別します。| UTF-8
        列見出し| データセットの見出しがある場合、それがどのように処理されるかを示します。| すべてのファイルのヘッダーを同じものにする
        行のスキップ | データセット内でスキップされる行がある場合、その行数を示します。| なし

    1. **[スキーマ]** フォームを使用すると、この実験用にデータをさらに構成できます。 この例では、**day_of_week** のトグル スイッチを選択して、これを含めないようにします。 **[次へ]** を選択します。
         ![[スキーマ] フォーム](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)
    1. **[詳細の確認]** フォームで、 **[基本情報]、[データストアとファイルの選択]** および **[Settings and preview]\(設定とプレビュー\)** のフォームに入力された情報が一致していることを確認します。
    
    1. **[作成]** を選択して、データセットの作成を完了します。
    
    1. リストにデータセットが表示されたら、それを選択します。
    
    1. **[Data preview]\(データのプレビュー)** を確認して **[day_of_week]** が含まれていないことを確保してから、 **[閉じる]** を選択します。

    1. **[次へ]** を選択します。

## <a name="configure-run"></a>実行を構成する

データを読み込んで構成したら、実験を設定できます。 このセットアップには、ご使用のコンピューティング環境のサイズの選択や予測する列の指定など、実験の設計タスクが含まれます。 

1. **[新規作成]** をクリックします。

1. 次のように **[Configure Run]\(構成の実行\)** フォームに入力します。
    1. この実験の名前として「`my-1st-automl-experiment`」と入力します。

    1. 予測するターゲット列として、 **[y]** を選択します。 この列には、クライアントが定期預金を申し込むかどうかが示されます。
    
    1. **[+Create a new compute]\(+ 新しいコンピューティングの作成\)** を選択し、コンピューティング先を構成します。 コンピューティング先とは、トレーニング スクリプトを実行したりサービスのデプロイをホストしたりするために使用されるローカルまたはクラウド ベースのリソース環境です。 この実験では、クラウド ベースのコンピューティングを使用します。 
        1. **[仮想マシン]** フォームに必要事項を入力してコンピューティングを設定します。

            フィールド | 説明 | チュートリアルの値
            ----|---|---
            仮想マシンの優先度 |実験の優先度を選択します。| 専用
            仮想マシンの種類&nbsp;&nbsp;| コンピューティング用の仮想マシンの種類を選択します。|CPU (中央処理装置)
            仮想マシンのサイズ&nbsp;&nbsp;| コンピューティングの仮想マシン サイズを選択します。 指定したデータと実験の種類に基づいて、推奨サイズの一覧が提供されます。 |Standard_DS12_V2
        
        1. **[次へ]** を選択して、 **[Configure settings]\(構成の設定\)** フォームに必要事項を入力します。
        
            フィールド | 説明 | チュートリアルの値
            ----|---|---
            コンピューティング名 |  コンピューティング コンテキストを識別する一意名。 | automl-compute
            最小/最大ノード| データをプロファイリングするには、1 つ以上のノードを指定する必要があります。|最小ノード: 1<br>最大ノード: 6
            スケール ダウンする前のアイドル時間 (秒) | クラスターが最小ノード数に自動的にスケールダウンされるまでのアイドル時間。|120 (既定値)
            詳細設定 | 実験用の仮想ネットワークを構成および承認するための設定。| なし               

        1. **[作成]** を選択してコンピューティング先を作成します。 

            **完了するまでに数分かかります。** 

             ![[設定] ページ](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. 作成後、ドロップダウン リストから新しいコンピューティング先を選択します。

    1. **[次へ]** を選択します。

1. **[Task type and settings]\(タスクの種類と設定\)** フォームで、機械学習のタスクの種類と構成設定を指定して、自動 ML 実験の設定を完了します。
    
    1.  機械学習のタスクの種類として **[分類]** を選択します。

    1. **[View additional configuration settings]\(追加の構成設定を表示\)** を選択し、次のようにフィールドを設定します。 これらは、トレーニング ジョブをより細かく制御するための設定です。 設定しない場合、実験の選択とデータに基づいて既定値が適用されます。

        追加の構成&nbsp;|説明|チュートリアル用の値&nbsp;&nbsp;
        ------|---------|---
        主要メトリック| 機械学習アルゴリズムを測定される評価メトリック。|AUC_weighted
        最適なモデルの説明| 自動 ML で作成された最適なモデルの説明を自動的に表示します。| 有効化
        ブロックされたアルゴリズム | トレーニング ジョブから除外するアルゴリズム| なし
        終了条件| 条件が満たされると、トレーニング ジョブが停止します。 |トレーニング&nbsp;ジョブ時間 (時間):&nbsp;1 <br> メトリック&nbsp;スコアしきい値:&nbsp;なし
        検証 | クロス検証タイプとテストの回数を選択します。|検証タイプ:<br>&nbsp;k 分割交差検証&nbsp; <br> <br> 検証の数: 2
        コンカレンシー| イテレーションごとに実行される並列イテレーションの最大数| &nbsp;最大同時イテレーション数:&nbsp;5
        
        **[保存]** を選択します。
    
1. **[完了]** を選択して実験を実行します。 実験の準備が開始されると、 **[Run Detail]\(実行の詳細\)** 画面が開いて、一番上に **[Run status]\(実行の状態\)** が表示されます。 この状態は、実験の進行に応じて更新されます。 スタジオの右上隅にも、実験の状態を表す通知が表示されます。

>[!IMPORTANT]
> 実験の実行の準備に、**10 から 15 分** かかります。
> 実行の開始後、**各イテレーションのためにさらに 2、3 分** かかります。  <br> <br>
> 運用環境では、しばらく席を離れるかもしれません。 ただし、このチュートリアルでは、他のイテレーションが実行中でも、アルゴリズムのテストが終わりしだい、 **[モデル]** タブで調査することをお勧めします。 

##  <a name="explore-models"></a>モデルを調査する

**[モデル]** タブに移動し、テストされたアルゴリズム (モデル) を確認します。 既定では、モデルは完了時のメトリック スコアで並べ替えられます。 このチュートリアルでは、選択した **AUC_weighted** メトリックに基づいて最も高いスコアを獲得したモデルがリストの一番上に表示されます。

すべての実験モデルが終了するのを待っている間に、完了したモデルの **[アルゴリズム名]** を選択して、そのパフォーマンスの詳細を調査します。 

次の例では、 **[詳細]** と **[メトリック]** のタブを移動して、選択したモデルのプロパティ、メトリック、およびパフォーマンス グラフを表示しています。 

![イテレーションの実行の詳細](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="model-explanations"></a>モデルの説明

モデルが完成するまで待つ間、モデルの説明を参照し、どのデータの特徴 (未加工または設計済み) が特定のモデルの予測に影響したかを確認することもできます。 

これらのモデルの説明は、オンデマンドで生成することができます。モデルの説明ダッシュボードに **[説明 (プレビュー)]** タブの一部として概要が表示されます。

モデルの説明を生成するには、 
 
1. 上部にある **[実行 1]** を選択し、 **[モデル]** 画面に戻ります。 
1. **[モデル]** タブを選択します。
1. このチュートリアルでは、最初の **MaxAbsScaler、LightGBM** モデルを選択します。
1. 上部にある **[Explain model]\(モデルの説明\)** ボタンを選択します。 右側には **[Explain model]\(モデルの説明\)** ペインが表示されます。 
1. 以前に作成した **automl-compute** を選択します。 このコンピューティング クラスターにより、モデルの説明を生成する子の実行が開始されます。
1. 下部にある **[作成]** を選択します。 画面の上部に緑色の成功メッセージが表示されます。 
    >[!NOTE]
    > 説明可能性の実行が完了するまでに約 2 分から 5 分かかります。
1. **[説明 (プレビュー)]** ボタンを選択します。 説明可能性の実行が完了すると、このタブに設定されます。
1. 左側にあるペインを展開し、 **[特徴]** の下にある **raw** という行を選択します。 
1. 右側の **[Aggregate feature importance]\(特徴量の重要度の集計\)** タブを選択します。 このグラフは、選択したモデルの予測に影響を与えたデータの特徴を示しています。 

    この例では、"*期間*" がこのモデルの予測に最も影響を与えているように見えます。
    
    ![モデルの説明ダッシュボード](media/tutorial-first-experiment-automated-ml/model-explanation-dashboard.png)

## <a name="deploy-the-best-model"></a>最適なモデルをデプロイする

自動機械学習インターフェイスを使用すると、わずかな手順で最良のモデルを Web サービスとしてデプロイすることができます。 デプロイとは、新しいデータを予測したり、潜在的な機会領域を特定したりできるようにモデルを統合することです。 

この実験における Web サービスへのデプロイは、定期預金の潜在顧客を特定するためのスケーラブルな反復 Web ソリューションを金融機関が持つことを意味します。 

実験の実行が完了したかどうかを確認します。 これを行うには、画面の上部にある **[Run 1]\(実行 1\)** を選択して、親の実行ページに戻ります。 画面の左上に **完了** 状態が表示されます。 

実験の実行が完了すると、 **[詳細]** ページに **[Best model summary]\(最適なモデルの概要\)** セクションが設定されます。 この実験では、**VotingEnsemble** は **AUC_weighted** メトリックに基づいて最適なモデルと見なされます。  

このモデルをデプロイしますが、デプロイには完了まで約 20 分かかることにご留意ください。 デプロイ プロセスには、モデルを登録したり、リソースを生成したり、Web サービス用にそれらを構成したりすることを含む、いくつかの手順が伴います。

1. **[VotingEnsemble]** を選択して、モデル固有のページを開きます。

1. 左上にある **[デプロイ]** ボタンを選択します。

1. **[Deploy a model]\(モデルのデプロイ\)** ペインに次のように入力します。

    フィールド| 値
    ----|----
    デプロイ名| my-automl-deploy
    デプロイの説明| 初めての自動機械学習実験のデプロイ
    コンピューティングの種類 | Azure のコンピューティング インスタンス (ACI) を選択します。
    認証を有効にする| 無効。 
    Use custom deployments (カスタム デプロイを使用する)| 無効。 既定のドライバー ファイル (スコアリング スクリプト) と環境ファイルが自動的に生成されます。 
    
    この例では、 *[Advanced]\(詳細\)* メニューに指定されている既定値を使用します。 

1. **[デプロイ]** を選択します。  

    **[実行]** 画面の上部に成功を示す緑色のメッセージが表示され、 **[モデルの概要]** ペインの **[Deploy status]\(デプロイ状態\)** にステータス メッセージが表示されます。 **[最新の情報に更新]** を定期的にクリックして、デプロイの状態を確認します。
    
これで、予測を生成するための実稼働 Web サービスが作成されました。 

新しい Web サービスの使い方、Azure Machine Learning サポートに組み込まれている Power BI を使用した予測のテスト方法の詳細については、[**次のステップ**](#next-steps)に進みます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

デプロイ ファイルはデータ ファイルと実験ファイルよりも大きいため、格納コストは高くなります。 アカウントのコストを最小限に抑える場合、またはワークスペースと実験ファイルを保持する場合は、デプロイ ファイルだけを削除します。 それ以外の場合で、いずれのファイルも使用する予定がない場合は、リソース グループ全体を削除します。  

### <a name="delete-the-deployment-instance"></a>デプロイ インスタンスの削除

他のチュートリアルや探索用にリソース グループとワークスペースを維持する場合は、https:\//ml.azure.com/ で Azure Machine Learning からデプロイ インスタンスだけを削除します。 

1. [Azure Machine Learning](https://ml.azure.com/) に移動します。 お使いのワークスペースに移動し、左側の **[アセット]** ウィンドウの下の **[エンドポイント]** を選択します。 

1. 削除するデプロイを選択し、 **[削除]** を選択します。 

1. **[続行]** を選択します。

### <a name="delete-the-resource-group"></a>リソース グループを削除します

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

この自動機械学習チュートリアルでは、Azure Machine Learning の自動 ML インターフェイスを使用して分類モデルの作成とデプロイを行いました。 詳細と次の手順については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Web サービスを使用する](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ [自動機械学習](concept-automated-ml.md)についてさらに理解を深める。
+ 分類メトリックとグラフの詳細については、「[自動化機械学習の結果の概要](how-to-understand-automated-ml.md)」の記事を参照してください。
+ [特徴付け](how-to-configure-auto-features.md#featurization)についてさらに理解を深める。
+ [データ プロファイル](how-to-connect-data-ui.md#profile)についてさらに理解を深める。


>[!NOTE]
> この Bank Marketing データセットは、[クリエイティブ コモンズ (CCO:パブリック ドメイン) ライセンス](https://creativecommons.org/publicdomain/zero/1.0/)により利用できます。 データベースの個々のコンテンツに含まれる権限は、[データベース コンテンツ ライセンス](https://creativecommons.org/publicdomain/zero/1.0/)によりライセンス供与され、[Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) で入手できます。 このデータセットのオリジナルは、[UCI Machine Learning データベース](https://archive.ics.uci.edu/ml/datasets/bank+marketing)から入手できます。<br><br>
> [Moro その他、2014 年] S. Moro、P. Cortez、Rita。 銀行のテレマーケティングの成功を予測するためのデータドリブン アプローチ。 意思決定支援システム、Elsevier、62:22-31、2014 年 6 月。
