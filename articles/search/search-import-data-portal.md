---
title: Azure portal を使用して検索インデックスにデータをインポートする
titleSuffix: Azure Cognitive Search
description: Azure portal でデータのインポート ウィザードを使用して、Cosmos DB、Blob Storage、Table Storage、SQL Database、SQL Managed Instance、Azure VM 上の SQL Server から Azure データをクロールします。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935213"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Azure Cognitive Search のデータのインポート ウィザード

Azure portal では、インデックスのプロトタイプ作成および読み込みを行うための **データのインポート** ウィザードが Azure Cognitive Search ダッシュボードに用意されています。 この記事では、ウィザードの使用の利点と制限事項、入力と出力、および使用方法に関する情報について説明します。 組み込みのサンプル データを使用したウィザードのステップ実行に関する実践的なガイダンスについては、クイックスタート「[Azure portal を利用して Azure Cognitive Search インデックスを作成する](search-get-started-portal.md)」を参照してください。

このウィザードで実行される操作は次のとおりです。

1 - サポートされている Azure データ ソースに接続します。

2 - ソース データのサンプリングによって推論されるインデックス スキーマを作成します。

3 - 必要に応じて、内容と構造を抽出または生成する AI エンリッチメントを追加します。

4 - ウィザードを実行して、オブジェクトの作成、データのインポート、スケジュールやその他の構成オプションの設定を行います。

ウィザードでは、検索サービスに保存されている多数のオブジェクトが出力されます。これらのオブジェクトには、プログラムまたは他のツールでアクセスできます。

## <a name="advantages-and-limitations"></a>利点と制限事項

コードを記述する前に、このウィザードをプロトタイプ作成と概念実証テストに使用できます。 ウィザードでは、外部データ ソースに接続し、データをサンプリングして初期インデックスを作成します。次に、データを JSON ドキュメントとして Azure Cognitive Search のインデックスにインポートします。 

サンプリングはインデックス スキーマを推論するプロセスであり、これには制限がいくつかあります。 データ ソースが作成されると、ウィザードによってドキュメントのサンプルが選択され、データ ソースの一部である列が決定されます。 非常に大規模なデータ ソースの場合はこの処理に時間がかかる可能性があるため、すべてのファイルが読み取られるわけではありません。 ドキュメントが選択されると、フィールド名や種類などのソース メタデータを使用して、インデックス スキーマにフィールド コレクションが作成されます。 ソース データの複雑さに応じて、正確さを求めて初期スキーマを編集したり、完全を期すために拡張したりすることが必要になる場合があります。 インデックスの定義のページで、変更をインラインで行うことができます。

概して、ウィザードを使用する利点は明らかです。要件が満たされていれば、数分以内にクエリ可能なインデックスのプロトタイプを作成できます。 JSON ドキュメントとしてのデータの提供など、インデックス作成の複雑な処理の一部は、ウィザードによって行われます。

既知の制限事項は次のとおりです。

+ このウィザードでは、イテレーションと再利用はサポートされていません。 ウィザードをパススルーするたびに、新しいインデックス、スキルセット、およびインデクサー構成が作成されます。 ウィザード内で保持および再利用できるのは、データ ソースのみです。 他のオブジェクトを編集または調整するには、REST API または .NET SDK を使用して、構造を取得して変更する必要があります。

+ ソース コンテンツは、サポートされている Azure データ ソース内にある必要があります。

+ サンプリングは、ソース データのあるサブセットについて行われます。 大規模なデータ ソースの場合、ウィザードでフィールドが見逃される可能性があります。 サンプリングが不十分な場合は、スキーマを拡張するか、推論されたデータ型を修正することが必要になる場合があります。

+ ポータルで公開されている AI エンリッチメントは、いくつかの組み込みのスキルに限定されています。 

+ ウィザードで作成できる[ナレッジ ストア](knowledge-store-concept-intro.md)は、いくつかの既定のプロジェクションに限定されています。 ウィザードによって作成されたエンリッチメントされたドキュメントを保存する場合、BLOB コンテナーとテーブルは既定の名前と構造を持ちます。

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>データ ソースの入力

**データのインポート** ウィザードは、Azure Cognitive Search インデクサーによって提供される内部ロジックを使用して外部データ ソースに接続します。Azure Cognitive Search インデクサーは、ソースのサンプリング、メタデータの読み取り、内容と構造を読み取るためのドキュメントの解読の機能を備え、その後の Azure Search へのインポートのために JSON として内容をシリアル化することもできます。

インポート元として指定できるのは、単一のテーブル、データベース ビュー、または同等のデータ構造体のみですが、構造体には、階層または入れ子になったのサブ構造体を含めることができます。 詳細については、[複合型のモデル化の方法に関するページ](search-howto-complex-data-types.md)を参照してください。

ウィザードを実行する前に、この単一のテーブルまたはビューを作成し、内容を含める必要があります。 言うまでもなく、空のデータ ソースで **データのインポート** ウィザードを実行することは意味がありません。

|  [選択] | 説明 |
| ---------- | ----------- |
| **既存のデータ ソース** |検索サービスに定義済みのインデクサーが既にある場合は、再利用できるデータ ソース定義が既に存在することがあります。 Azure Cognitive Search では、データ ソース オブジェクトはインデクサーでのみ使用されます。 データ ソース オブジェクトは、プログラムで、または **データのインポート** ウィザードを使用して作成でき、必要に応じて再利用できます。|
| **サンプル**| Azure Cognitive Search には、チュートリアルとクイックスタートで使用される 2 つの組み込みサンプル データ ソースが用意されています。これは、Cosmos DB でホストされている不動産 SQL データベースとホテル データベースです。 ホテル サンプルに基づくチュートリアルについては、クイックスタートの「[Azure portal でインデックスを作成する](search-get-started-portal.md)」を参照してください。 |
| [**Azure SQL Database または SQL Managed Instance**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |サービス名、読み取り権限を持つデータベース ユーザーの資格情報、データベース名は、このページで、または ADO.NET 接続文字列を使用して指定できます。 接続文字列のオプションを選択して、プロパティを表示またはカスタマイズします。 <br/><br/>行セットを提供するテーブルまたはビューは、このページで指定する必要があります。 このオプションは接続に成功すると表示され、ドロップダウン リストから選択できます。|
| **Azure VM 上の SQL Server** |接続文字列として、完全修飾サービス名、ユーザー ID とパスワード、データベースを指定します。 このデータ ソースを使用するには、接続を暗号化する証明書をローカル ストアにあらかじめインストールしておく必要があります。 手順については、[Azure Cognitive Search への SQL VM 接続](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)に関する記事をご覧ください。 <br/><br/>行セットを提供するテーブルまたはビューは、このページで指定する必要があります。 このオプションは接続に成功すると表示され、ドロップダウン リストから選択できます。 |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|要件には、アカウント、データベース、コレクションが含まれます。 コレクション内のすべてのドキュメントはインデックスに含まれます。 行セットをフラット化またはフィルター処理するクエリを定義することも、クエリを空白のままにすることもできます。 このウィザードでは、クエリは必要はありません。|
| [**Azure Blob Storage**](search-howto-indexing-azure-blob-storage.md) |要件には、ストレージ アカウントとコンテナーが含まれます。 BLOB 名がグループ化のために仮想名前付け規則に従っている場合は、必要に応じて、コンテナーの下のフォルダーとして名前の仮想ディレクトリの部分を指定できます。 詳しくは、[Blob Storage のインデックス作成](search-howto-indexing-azure-blob-storage.md)に関する記事をご覧ください。 |
| [**Azure Table Storage**](search-howto-indexing-azure-tables.md) |要件には、ストレージ アカウントとテーブル名が含まれます。 必要に応じて、クエリを指定してテーブルのサブセットを取得できます。 詳しくは、[Table Storage のインデックス作成](search-howto-indexing-azure-tables.md)に関する記事をご覧ください。 |

## <a name="wizard-output"></a>ウィザードの出力

ウィザードでは、バックグラウンドで次のオブジェクトの作成、構成、呼び出しを行います。 ウィザードが実行された後、ポータル ページにその出力が表示されます。 サービスの [概要] ページには、インデックス、インデクサー、データ ソース、およびスキルセットの一覧が表示されます。 インデックスの定義は、完全な JSON でポータルに表示できます。 その他の定義については、[REST API](/rest/api/searchservice/) を使用して特定のオブジェクトを取得できます。

| Object | 説明 | 
|--------|-------------|
| [データ ソース](/rest/api/searchservice/create-data-source)  | ソース データに対する接続情報を資格情報を含めて保持します。 データ ソース オブジェクトは、インデクサーでのみ使用されます。 | 
| [Index](/rest/api/searchservice/create-index) | フルテキスト検索やその他のクエリに使用される物理データ構造です。 | 
| [スキルセット](/rest/api/searchservice/create-skillset) | 画像ファイルからの情報の分析と抽出を含む、コンテンツの操作、変換、および整形を行うための手順の完全なセットです。 非常に単純で限定されている構造の場合を除き、エンリッチメントを提供する Cognitive Services リソースへの参照が含まれます。 オプションで、ナレッジ ストアの定義が含まれる場合もあります。  | 
| [Indexer](/rest/api/searchservice/create-indexer)  | データ ソース、ターゲット インデックス、オプションのスキルセット、オプションのスケジュール、およびエラー処理と Base-64 エンコード用のオプションの構成設定を指定する構成オブジェクトです。 |


## <a name="how-to-start-the-wizard"></a>ウィザードの起動方法

データのインポート ウィザードは、サービスの [概要] ページのコマンド バーから起動されます。

1. [Azure portal](https://portal.azure.com) でダッシュボードから検索サービス ページを開くか、サービスの一覧で[ご自分のサービスを見つけ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)ます。

2. 上部にあるサービスの概要ページで、 **[データのインポート]** をクリックします。

   ![ポータルの [データのインポート] コマンド](./media/search-import-data-portal/import-data-cmd2.png "データのインポート ウィザードを開始する")

**[データのインポート]** は、Azure Cosmos DB、Azure SQL Database、SQL Managed Instance、Azure Blob Storage を含む、他の Azure サービスから起動することもできます。 サービスの概要ページの左側にあるナビゲーション ウィンドウで、 **[Add Azure Cognitive Search]\(Azure Cognitive Search の追加\)** を見つけます。

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>ウィザードでインデックス スキーマを編集または完了する方法

ウィザードでは不完全なインデックスが生成され、入力データ ソースから取得されるドキュメントが取り込まれます。 関数インデックスについては、以下の要素が定義されていることを確認します。

1. フィールドの一覧は完了していますか。 サンプリングされなかった新しいフィールドを追加し、検索エクスペリエンスに値を追加しないものと、[フィルター式](search-query-odata-filter.md)にも[スコアリング プロファイル](index-add-scoring-profiles.md)にも使用されないものをすべて削除します。

1. データ型は受信データに適していますか。 Azure Cognitive Search では、[Entity Data Model (EDM) データ型](/rest/api/searchservice/supported-data-types)がサポートされています。 Azure SQL データについては、同等の値を示している[マッピング表](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping)があります。 詳細な背景については、[フィールドのマッピングと変換](search-indexer-field-mappings.md)に関する記事をご覧ください。

1. "*キー*" として使用できるフィールドが 1 つありますか。 このフィールドは、Edm.string でなければならず、ドキュメントを一意に識別する必要があります。 リレーショナル データの場合は、主キーにマップされていることがあります。 BLOB の場合、`metadata-storage-path` であることがあります。 フィールドの値に空白またはダッシュが含まれている場合は、**インデクサーの作成** 手順の **[詳細オプション]** で **[Base-64 エンコード キー]** オプションを設定し、これらの文字の検証チェックを抑制する必要があります。

1. 属性を設定して、インデックスでのこのフィールドの使用方法を指定します。 

   インデックス内のフィールドの物理的な表現が属性によって決定されるため、この手順に時間をかけてください。 後で属性を変更するときには、プログラムで行う場合も、ほとんどの場合にインデックスを削除して再構築する必要があります。 **Searchable** や **Retrievable** などのコア属性では、[ストレージへの影響は無視できる程度](search-what-is-an-index.md#index-size)です。 フィルターを有効にして suggester を使用すると、ストレージの要件が増えます。 
   
   + **Searchable** では、全文検索が有効になります。 自由形式のクエリまたはクエリ式で使用されるすべてのフィールドに、この属性が必要です。 **Searchable** としてマークしたフィールドごとに、逆インデックスが作成されます。

   + **Retrievable** の場合、検索結果にフィールドが返されます。 検索結果にコンテンツを提供するすべてのフィールドに、この属性が必要です。 このフィールドを設定しても、インデックス サイズに大きな影響はありません。

   + **Filterable** は、フィルター式でフィールドを参照できるようにします。 **$filter** 式で使用されるすべてのフィールドに、この属性が必要です。 このフィルター式は完全一致用です。 テキスト文字列はそのまま残るため、逐語的なコンテンツに対応するには、追加のストレージが必要です。

   + **Facetable** は、ファセット ナビゲーションにフィールドを使用できるようにします。 **Filterable** としてもマークされているフィールドのみを、**Facetable** としてマークできます。

   + **Sortable** は、並べ替えでフィールドを使用できるようにします。 **$Orderby** 式で使用されるすべてのフィールドに、この属性が必要です。

1. [字句解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)が必要ですか。 **Searchable** である Edm.string フィールドの場合、言語拡張インデックス作成とクエリの実行が必要な場合は、**アナライザー** を設定できます。 

   既定値は *標準 Lucene* ですが、不規則名詞や動詞形式の解決など、高度な字句処理のために Microsoft のアナライザーを使用する必要がある場合は、*Microsoft の英語* を選択できます。 ポータルでは、言語アナライザーのみを指定できます。 カスタム アナライザーや、キーワード、パターンなどの非言語アナライザーを使用する場合は、プログラムで実行する必要があります。 アナライザーの詳細については、[言語アナライザーの追加](search-language-support.md)に関する記事をご覧ください。

1. オートコンプリートまたは候補の結果の形式の先行入力機能が必要ですか。 **[Suggester]** チェックボックスを選択し、選択したフィールドで [先行入力クエリ候補とオートコンプリート](index-add-suggesters.md)を有効にします。 suggester は、インデックス内のトークン化された用語の数を増加させるため、より多くのストレージを消費します。


## <a name="next-steps"></a>次のステップ

ウィザードの利点と制限事項を理解する最善の方法は、これを段階を追って実行することです。 次のクイックスタートでは、各ステップについて説明します。

> [!div class="nextstepaction"]
> [Azure portal を利用して Azure Cognitive Search インデックスを作成する](search-get-started-portal.md)