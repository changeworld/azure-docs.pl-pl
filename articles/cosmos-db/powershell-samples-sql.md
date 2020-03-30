---
title: Przykłady programu Azure PowerShell dla usługi Azure Cosmos DB — interfejs API SQL (Core)
description: Pobieranie przykładów programu Azure PowerShell do wykonywania różnych typowych zadań na kontach interfejsu API SQL usługi Azure Cosmos DB DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366185"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Przykłady programu Azure PowerShell dla usługi Azure Cosmos DB — interfejs API SQL (Core)

Poniższa tabela zawiera łącza do często używanych skryptów programu Azure PowerShell dla usługi Azure Cosmos DB dla interfejsu API SQL (Core). Jeśli chcesz rozwidlić te przykłady programu PowerShell dla usługi Cosmos DB z naszego repozytorium GitHub, odwiedź [przykłady programu Cosmos DB PowerShell w witrynie GitHub.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)

Aby uzyskać dodatkowe przykłady programu Cosmos DB PowerShell dla interfejsu API i dokumentacji SQL (Core), zobacz [Zarządzanie zasobami interfejsu API SQL usługi Azure Cosmos DB za pomocą programu PowerShell](manage-with-powershell.md). W przypadku przykładów programu Cosmos DB PowerShell dla innych interfejsów API zobacz [Interfejs API cassandra](powershell-samples-cassandra.md), [interfejs API mongodb](powershell-samples-mongodb.md), [interfejs API gremlin](powershell-samples-gremlin.md)i [interfejs API tabeli](powershell-samples-table.md).

> [!NOTE]
> W przykładach użyto poleceń cmdlet zarządzania [usługą Az.CosmosB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Należy pamiętać, `Az.CosmosDB` że polecenia cmdlet są nadal w wersji zapoznawczej i mogą ulec zmianie przed wydaniem. Proszę sprawdzić aktualizacje `Az.CosmosDB` regularnie.

| | |
|---|---|
|[Tworzenie konta, bazy danych i kontenera](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto, bazę danych i kontener usługi Azure Cosmos DB. |
|[Tworzenie kontenera z dużym kluczem partycji](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz kontener z dużym kluczem partycji. |
|[Wyświetlanie listy lub ponajmowania baz danych lub kontenerów](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista lub pobierz bazę danych lub kontenery. |
|[Pobierz RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz ru/s dla bazy danych lub kontenera. |
|[Aktualizacja RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizacja programu RU/s dla bazy danych lub kontenera. |
|[Tworzenie kontenera bez zasad indeksu](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Utwórz kontener usługi Azure Cosmos z wyłączonymi zasadami indeksu.|
|[Aktualizowanie konta](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zaktualizuj domyślny poziom spójności konta usługi Cosmos DB. |
|[Aktualizowanie regionów konta](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie regionów konta usługi Cosmos DB. |
|[Zmiana priorytetu trybu failover lub wyzwalania trybu failover](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień regionalny priorytet pracy awaryjnej konta usługi Azure Cosmos lub wyzwolić ręczne tryb failover. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie wygeneruj klucz konta konta usługi Azure Cosmos DB. |
|[Tworzenie konta usługi Cosmos z zaporą IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto usługi Azure Cosmos DB z włączoną zaporą IP. |
|||
