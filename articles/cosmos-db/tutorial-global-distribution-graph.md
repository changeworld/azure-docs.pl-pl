---
title: Samouczek dotyczący dystrybucji globalnej usługi Azure Cosmos DB dla interfejsu API programu Graph | Microsoft Docs
description: Dowiedz się, jak skonfigurować dystrybucję globalną usługi Azure Cosmos DB przy użyciu interfejsu API programu Graph.
services: cosmos-db
keywords: global distribution, graph, gremlin
author: luisbosquez
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 61004a735f731cfd1303cf40b6e60cba496e942a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763684"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Konfigurowanie dystrybucji globalnej usługi Azure Cosmos DB przy użyciu interfejsu API programu Graph

W tym artykule przedstawiono, jak za pomocą witryny Azure Portal skonfigurować dystrybucję globalną usługi Azure Cosmos DB, a następnie nawiązać połączenie przy użyciu interfejsu API programu Graph.

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu [interfejsów API programu Graph](graph-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Nawiązywanie połączenia z preferowanym regionem przy użyciu interfejsu API programu Graph i zestawu SDK platformy .NET

Interfejs API programu Graph jest udostępniany jako biblioteka rozszerzenia na podstawie interfejsu API SQL.

Aby można było korzystać z [dystrybucji globalnej](distribute-data-globally.md), w aplikacjach klienckich można określić uporządkowaną listę preferencji regionów, która będzie używana do wykonywania operacji na dokumentach. Można to zrobić, ustawiając zasady połączenia. Na podstawie konfiguracji konta usługi Azure Cosmos DB, bieżącej dostępności regionalnej i określonej listy preferencji zestaw SDK wybiera najbardziej optymalny punkt końcowy, w którym będą wykonywane operacje zapisu i odczytu.

Lista preferencji jest określana podczas inicjowania połączenia przy użyciu zestawów SDK. Zestawy SDK akceptują opcjonalny parametr „PreferredLocations”, to znaczy uporządkowaną listę regionów świadczenia usługi Azure.

* **Writes**: zestaw SDK automatycznie wysyła wszystkie operacje zapisu do bieżącego regionu zapisu.
* **Reads**: wszystkie operacje odczytu są wysyłane do pierwszego dostępnego regionu na liście PreferredLocations. Jeśli żądanie zakończy się niepowodzeniem, klient przejdzie do następnego regionu z listy itd. Zestawy SDK podejmują próby odczytu tylko z regionów określonych na liście PreferredLocations. Na przykład jeśli konto usługi Cosmos DB jest dostępne w trzech regionach, ale klient określa tylko dwa regiony bez odczytu na liście PreferredLocations, operacje odczytu z regionu zapisu nie są obsługiwane, nawet w przypadku przejścia w tryb failover.

Aplikacja może zweryfikować bieżący punkt końcowy zapisu i punkt końcowy odczytu wybrany przez zestaw SDK, sprawdzając dwie właściwości, WriteEndpoint i ReadEndpoint, dostępne w zestawie SDK w wersji 1.8 i nowszych. Jeśli właściwość PreferredLocations nie została ustawiona, wszystkie żądania są obsługiwane z bieżącego regionu zapisu.

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Na przykład w zestawie SDK platformy .NET parametr `ConnectionPolicy` konstruktora `DocumentClient` ma właściwość o nazwie `PreferredLocations`. Wartość tej właściwości można ustawić na listę nazw regionów. Nazwy wyświetlane [regionów platformy Azure][regions] można określić jako cześć listy `PreferredLocations`.

> [!NOTE]
> Adresów URL punktów końcowych nie należy traktować jako długotrwałych stałych. Usługa może zaktualizować je w dowolnym momencie. Zestaw SDK obsługuje tę zmianę automatycznie.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

To wszystko — na tym kończy się ten samouczek. Aby dowiedzieć się, jak zarządzać spójnością globalnie replikowanego konta, przeczytaj [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md). Natomiast aby uzyskać więcej informacji na temat sposobu działania globalnej replikacji w usłudze Azure Cosmos DB, zobacz [Dystrybuowanie danych globalnie za pomocą usługi Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsów API SQL

Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak programować lokalnie przy użyciu lokalnego emulatora usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Programowanie lokalnie za pomocą emulatora](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

