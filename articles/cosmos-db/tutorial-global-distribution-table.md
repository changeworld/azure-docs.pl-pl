---
title: Samouczek dystrybucji globalnej usługi Azure Cosmos DB dla interfejsu API tabel
description: Dowiedz się, jak skonfigurować dystrybucję globalną usługi Azure Cosmos DB przy użyciu interfejsu API tabel.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/13/2017
ms.reviewer: sngun
ms.openlocfilehash: d7d68c2dbdf5ca32fb2936e92daafac838c97a06
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627137"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Konfigurowanie dystrybucji globalnej usługi Azure Cosmos DB przy użyciu interfejsu API tabel

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

