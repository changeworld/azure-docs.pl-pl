---
title: Samouczek dotyczący dystrybucji globalnej usługi Azure Cosmos DB dla interfejsu API tabel | Microsoft Docs
description: Dowiedz się, jak skonfigurować dystrybucję globalną usługi Azure Cosmos DB przy użyciu interfejsu API tabel.
services: cosmos-db
keywords: global distribution, Table
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: f877baa33d94dad07250da9a10209555dbca65c9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Jak skonfigurować dystrybucję globalną usługi Azure Cosmos DB przy użyciu interfejsu API tabel

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsów [API tabel](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Nawiązywanie połączenia z preferowanym regionem przy użyciu interfejsu API tabel

Aby można było korzystać z [dystrybucji globalnej](distribute-data-globally.md), w aplikacjach klienckich można określić uporządkowaną listę preferencji regionów, która będzie używana do wykonywania operacji na dokumentach. Można to zrobić, ustawiając właściwość [TableConnectionPolicy.PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.table.tableconnectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_CosmosDB_Table_TableConnectionPolicy_PreferredLocations). Na podstawie konfiguracji konta, bieżącej dostępności regionalnej i określonej listy preferencji zestaw SDK interfejsu API tabel usługi Azure Cosmos DB wybiera najbardziej optymalny punkt końcowy do komunikacji.

Lista PreferredLocations powinna zawierać rozdzielaną przecinkami listę preferowanych lokalizacji (z obsługą wielu regionów) dla operacji odczytu. Każde wystąpienie klienta może określić podzbiór tych regionów w kolejności preferencji w celu zminimalizowania opóźnienia odczytu. Regiony muszą być nazwane za pomocą ich [nazw wyświetlanych](https://msdn.microsoft.com/library/azure/gg441293.aspx), na przykład `West US`.

Wszystkie operacje odczytu są wysyłane do pierwszego dostępnego regionu na liście PreferredLocations. Jeśli żądanie kończy się niepowodzeniem, klient przechodzi do następnego regionu z listy itd.

Zestawy SDK podejmują próby odczytu z regionów określonych na liście PreferredLocations. Na przykład jeśli konto bazy danych jest dostępne w trzech regionach, ale klient określa tylko dwa regiony bez odczytu na liście PreferredLocations, operacje odczytu z regionu zapisu nie są obsługiwane, nawet w przypadku przejścia w tryb failover.

Zestaw SDK automatycznie wysyła wszystkie operacje zapisu do bieżącego regionu zapisu.

Jeśli właściwość PreferredLocations nie została określona, wszystkie żądania są obsługiwane z bieżącego regionu zapisu.

To wszystko — na tym kończy się ten samouczek. Aby dowiedzieć się, jak zarządzać spójnością globalnie replikowanego konta, przeczytaj [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md). Natomiast aby uzyskać więcej informacji na temat sposobu działania globalnej replikacji w usłudze Azure Cosmos DB, zobacz [Dystrybuowanie danych globalnie za pomocą usługi Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsu API tabel usługi Azure Cosmos DB

