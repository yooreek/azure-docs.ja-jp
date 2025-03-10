---
title: Azure Cosmos DB NoSQL データベースとリレーショナル データベースの違いについて
description: この記事では、NoSQL データベースとリレーショナル データベースの違いを列挙します
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93088473"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>NoSQL データベースとリレーショナル データベースの違いについて
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

この記事では、リレーショナル データベースに対する NoSQL データベースの主な利点をいくつか列挙します。 また、NoSQL の使用に関するいくつかの課題についても説明します。 存在するさまざまなデータ ストアの詳細については、[適切なデータ ストアの選択](/azure/architecture/guide/technology-choices/data-store-overview)に関する記事を参照してください。

## <a name="high-throughput"></a>高スループット

リレーショナル データベース システムを保守する際の最も明白な課題の 1 つは、ほとんどのリレーショナル エンジンで、厳密な [ACID セマンティクス](https://en.wikipedia.org/wiki/ACID)を強制するためにロックとラッチが適用されていることです。 このアプローチには、データベース内で一貫性のあるデータの状態を確保するという点で利点があります。 ただし、コンカレンシー、待機時間、および可用性に関して大きなトレードオフがあります。 これらの基本的なアーキテクチャ上の制限により、トランザクション ボリュームが多い場合、手動でのデータのシャードが必要になることがあります。 手動シャーディングの実装には、時間と労力がかかることがあります。

これらのシナリオでは、[分散データベース](https://en.wikipedia.org/wiki/Distributed_database)を使用すると、よりスケーラブルなソリューションを提供できます。 ただし、メンテナンスにはやはりコストと時間がかかる可能性があります。 管理者は、システムの分散の性質において透過性を確保するために、追加の作業を行うことが必要になる場合があります。 また、データベースの “切断” の性質についても考慮する必要があります。

[Azure Cosmos DB](./introduction.md) は、Azure のすべてのリージョンにわたって世界規模でデプロイされることにより、これらの課題を単純化します。 パーティション範囲は、アプリケーションに合わせてデータベースをシームレスに拡大するために動的に再分割でき、同時に高可用性を維持することができます。 きめ細かなマルチテナントと厳密に制御されたクラウドネイティブ リソースの管理により、[驚異的な待機時間が保証され](./consistency-levels.md#consistency-levels-and-latency)、パフォーマンスが予測可能になります。 パーティション分割は完全に管理されているため、管理者がコードを書いたりパーティションを管理したりする必要はありません。

トランザクション ボリュームが極端なレベル (1 秒あたり数千のトランザクションなど) に達している場合は、分散 NoSQL データベースを検討する必要があります。 効率を最大化し、メンテナンスを容易にし、総保有コストを削減するためには、Azure Cosmos DB をご検討ください。

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="バックエンド" border="false":::

## <a name="hierarchical-data"></a>階層データ

データベース内のトランザクションに多数の親子リレーションシップを含めることができる多くのユース ケースがあります。 これらのリレーションシップは、時間の経過と共に大幅に増加し、管理が困難になる可能性があります。 [階層型データベース](https://en.wikipedia.org/wiki/Hierarchical_database_model)の形式は 1980 年代に登場しましたが、ストレージの非効率性が原因で一般的ではありませんでした。 また、[Ted Codd のリレーショナル モデル](https://en.wikipedia.org/wiki/Relational_model)が、事実上すべてのメインストリーム データベース管理システムで使用されるデファクト スタンダードになったことで、けん引力を失いました。

しかし今日では、ドキュメント スタイル データベースの人気が著しく高まっています。 これらのデータベースは、ディスクへのデータの格納コストに関する懸念から解放された、階層型データベース パラダイムの再発明と考えることができます。 そのため、リレーショナル データベース内に多くの複雑な親子エンティティ リレーションシップを維持することは、最新のドキュメント指向の方法と比較して、現在ではアンチパターンと見なすことができます。

[オブジェクト指向設計](https://en.wikipedia.org/wiki/Object-oriented_design)の出現、およびそれをリレーショナル モデルと組み合わせたときに生じる[インピーダンスの不一致](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)も、特定のユース ケースに対するリレーショナル データベースのアンチパターンを浮き彫りにしています。 結果として、隠れてはいるが、多くの場合多大なメンテナンス コストが発生する可能性があります。 [ORM のアプローチ](https://en.wikipedia.org/wiki/Object-relational_mapping)は、これを部分的に緩和するために進化してきましたが、それでもドキュメント指向のデータベースは、オブジェクト指向のアプローチとの融合がはるかに優れています。 このアプローチでは、開発者は、ORM ドライバーやカスタム言語固有の [OO データベース エンジン](https://en.wikipedia.org/wiki/Object_database)にコミットすることを強制されません。 データに多数の親子リレーションシップと深い階層レベルが含まれている場合は、[Azure Cosmos DB SQL API](./introduction.md) などの NoSQL ドキュメント データベースの使用を検討してください。

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>複雑なネットワークとリレーションシップ

皮肉なことに、リレーショナル データベースは、その名前にもかかわらず、深く複雑なリレーションシップをモデル化するための最適なソリューションとは言えません。 これは、エンティティ間のリレーションシップが実際にはリレーショナル データベースに存在しないためです。 それらは、クエリを使用したマッピングを可能にするためには、デカルト結合を必要とする複雑なリレーションシップを使用して、実行時に計算する必要があります。 その結果、リレーションシップが増加するにつれて、演算は計算の点で指数関数的に高価になります。 場合によっては、そのようなエンティティを管理しようとするリレーショナル データベースは使用不能になります。

リレーショナル データベースが出現した頃には、さまざまな形式の "ネットワーク" データベースが出現しましたが、階層型データベースと同様に、これらのシステムもなかなか人気を得ることができませんでした。 なかなか採用されなかったのは、当時のユース ケースが不足していたことと、ストレージの非効率性が原因でした。 今日、グラフ データベース エンジンは、ネットワーク データベース パラダイムの再出現と見なすことができます。 これらのシステムの主な利点は、リレーションシップがデータベース内に "第一級データ" として格納されることです。 それにより、リレーションシップの走査は、新しい結合やクロス積のたびに時間の複雑さが増すのではなく、一定の時間で実行できます。

データベース内にリレーションシップの複雑なネットワークを維持している場合は、このデータを管理するために、[Azure Cosmos DB Gremlin API](./graph-introduction.md) などのグラフ データベースを検討することをお勧めします。

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="複数の従業員と部門が相互に接続していることを示すデータベース ダイアグラム。":::

Azure Cosmos DB は、すべての主要な NoSQL モデルの種類、列ファミリ、ドキュメント、グラフ、およびキー値に対して API プロジェクションを提供するマルチモデル データベース サービスです。 [Gremlin (グラフ)](./gremlin-support.md) と SQL (Core) Document API レイヤーは、完全に相互運用可能です。 これには、プログラミング レベルでの異なるモデル間の切り替えに利点があります。 グラフ ストアには、複雑なネットワーク トラバーサルと、同じストア内のドキュメント レコードとしてモデル化されたトランザクションという両方の観点からクエリを実行できます。

## <a name="fluid-schema"></a>流動的なスキーマ

リレーショナル データベースのもう 1 つの特性は、デザイン時にスキーマを定義する必要があることです。 これには、参照整合性とデータの適合性という点で利点があります。 ただし、アプリケーションが拡大するにしたがって制限が生じることもあります。 同じテーブルやデータベース定義を共有する、論理的に異なるモデル間でスキーマの変更に対応することは、時間の経過と共に複雑になる可能性があります。 このようなユース ケースでは、多くの場合、スキーマがアプリケーションに委譲され、レコード単位で管理されることで利点が得られます。 このためには、データベースが "スキーマにとらわれない" ようにし、レコードが、含まれているデータに関して "自己記述型" になるようにする必要があります。

構造が頻繁に変化するデータを管理している場合、特にトランザクションが外部ソースから発生する可能性があり、データベース間の整合性を強制するのが難しい場合は、Azure Cosmos DB のような、マネージド NoSQL データベース サービスを使用する、よりスキーマにとらわれないアプローチを検討することをお勧めします。

## <a name="microservices"></a>マイクロサービス

[マイクロサービス](https://en.wikipedia.org/wiki/Microservices) パターンは、近年著しく拡大しました。 このパターンのルーツは、[サービス指向アーキテクチャ](https://en.wikipedia.org/wiki/Service-oriented_architecture)にあります。 これらの最新のマイクロサービス アーキテクチャにおけるデータ転送のデファクト スタンダードは [JSON](https://en.wikipedia.org/wiki/JSON)です。これは、ドキュメント指向 NoSQL データベースの大部分の記憶媒体でもあります。 これにより、NoSQL ドキュメント ストアは、複雑なマイクロサービスの実装全体での永続化と同期 ([イベント ソーシング パターン](https://en.wikipedia.org/wiki/Event-driven_architecture)を使用) の両方に、よりシームレスに適合するようになります。 これらのアーキテクチャでは、より従来型のリレーショナル データベースの保守がはるかに複雑になる可能性があります。 これは、API 間の状態と同期の両方で、より多くの量の変換が必要になるためです。 特に Azure Cosmos DB には、JSON ベースのマイクロサービス アーキテクチャに、多くの NoSQL データベースよりもシームレスに適合する、多くの機能があります。

* 純粋な JSON データ型の選択
* データベースに組み込まれた JavaScript エンジンと[クエリ API](./javascript-query-api.md)。
* コンテナーに対する変更の通知を受け取るためにクライアントがサブスクライブできる最新の[変更フィード](./change-feed.md)。

## <a name="some-challenges-with-nosql-databases"></a>NoSQL データベースに関するいくつかの課題

NoSQL データベースを実装する場合、いくつかの明確な利点がありますが、考慮すべき課題もいくつかあります。 これらは、リレーショナル モデルを扱うときには、同じ程度に存在しない場合があります。

* 同じエンティティをポイントする多くのリレーションシップを持つトランザクション。
* データセット全体で強力な一貫性を必要とするトランザクション。

最初の課題に目を向けると、NoSQL データベースの経験則は一般的に非正規化です。これにより、前に説明したように、分散システムでより効率的な読み取りが実現します。 ただし、このアプローチには、設計上の課題がいくつかあります。 1 つのカテゴリと複数のタグに関連する製品の例を見てみましょう。

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="結合":::

NoSQL ドキュメント データベースでのベスト プラクティスのアプローチは、カテゴリ名とタグ名を "製品ドキュメント" で直接非正規化することです。 ただし、カテゴリ、タグ、および製品の同期を維持するために、これを容易にする設計オプションでは、"1 対多" リレーションシップの単純な更新やデータを取得するための結合ではなく、データが製品内の複数のレコード間で複製されるため、メンテナンスの複雑さが増しています。 

トレードオフは、非正規化されたレコードでは読み取りがより効率的になり、概念的に結合されたエンティティの数が増えるにつれてさらに効率性が増すことです。 しかし、非正規化レコード内の結合されたエンティティの数が増加するにしたがって、読み取り効率が向上するのと同時に、エンティティの同期を維持するためのメンテナンスの複雑さも増します。このトレードオフを軽減する 1 つの方法は、[ハイブリッド データ モデル](./modeling-data.md#hybrid-data-models)を作成することです。

NoSQL データベースでは、これらのトレードオフに対応するための柔軟性が向上していますが、柔軟性の向上により、設計上の決定も増加する可能性があります。 「[現実の例を使用して Azure Cosmos DB のデータをモデル化およびパーティション分割する方法](./how-to-model-partition-example.md)」という記事を参照してください。これには、異なるパーティションだけでなく、異なるコンテナーにユーザーが配置される[非正規化されたユーザー データを同期化](./how-to-model-partition-example.md#denormalizing-usernames)するためのアプローチが含まれています。

強力な整合性に関して、これがデータセット全体で必要になることはまれです。 ただし、これが必要な場合は、分散データベースでの課題となる可能性があります。 強力な一貫性を確保するには、クライアントにデータの読み取りを許可する前に、すべてのレプリカとリージョンでデータを同期する必要があります。 これにより、読み取りの待機時間が増加する可能性があります。

この場合も、Azure Cosmos DB は、ここで関連するさまざまなトレードオフに関して、リレーショナル データベースよりも優れた柔軟性を提供しますが、小規模な実装では、このアプローチによって設計上の考慮事項が増える可能性があります。 このトピックの詳細については、「[整合性、可用性、パフォーマンスのトレードオフ](./consistency-levels.md)」に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cosmos アカウントの管理方法とその他の概念を確認します。

* [Azure Cosmos アカウントを管理する方法](how-to-manage-database-account.md)
* [グローバル配信](distribute-data-globally.md)
* [一貫性レベル](consistency-levels.md)
* [Azure Cosmos のコンテナーおよび項目の操作](account-databases-containers-items.md)
* [Azure Cosmos アカウントの VNET サービス エンドポイント](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos アカウントの IP ファイアウォール](how-to-configure-firewall.md)
* [Azure Cosmos アカウントに Azure リージョンを追加および削除する方法](how-to-manage-database-account.md)
* [Azure Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)