---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla interfejsu API mongodb usługi Azure Cosmos DB
description: Przykłady interfejsu wiersza polecenia platformy Azure dla interfejsu API mongodb usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77524567"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla interfejsu API mongodb usługi Azure Cosmos DB

Poniższa tabela zawiera łącza do przykładowych skryptów interfejsu wiersza polecenia platformy Azure dla interfejsu API mongodb usługi Azure Cosmos DB. Strony odwołań dla wszystkich poleceń interfejsu wiersza polecenia bazy danych usługi Azure Cosmos DB są dostępne w [odwołaniu interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure.](/cli/azure/cosmosdb) Wszystkie przykłady skryptów interfejsu wiersza polecenia usługi Azure Cosmos DB można znaleźć w [repozytorium github bazy danych usługi Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Obecnie można utworzyć tylko wersję 3.2 (czyli konta przy użyciu `*.documents.azure.com`punktu końcowego w formacie) interfejsu API usługi Azure Cosmos DB dla kont MongoDB przy użyciu szablonów programu PowerShell, CLI i Resource Manager. Aby utworzyć 3.6 wersji kont, należy użyć witryny Azure portal zamiast tego.

| |  |
|---|---|
| [Tworzenie konta, bazy danych i kolekcji usługi Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy konto usługi Azure Cosmos DB, bazy danych i kolekcji dla interfejsu API MongoDB. |
| [Zmienianie przepływności](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Aktualizowanie usług RU/s w bazie danych i kolekcji.|
| [Dodawanie lub trybu failover](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Dodaj region, zmień priorytet pracy awaryjnej, wyzwalaj ręczne tryb failover.|
| [Klucze konta i parametry połączenia](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Lista kluczy konta, kluczy tylko do odczytu, ponowne generowanie kluczy i listy ciągów połączeń.|
| [Zabezpiecz za pomocą zapory IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto usługi Cosmos ze skonfigurowaną zaporą IP.|
| [Zabezpiecz nowe konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Utwórz konto usługi Cosmos i zabezpiecz z punktami końcowymi usługi.|
| [Zabezpiecz istniejące konto za pomocą punktów końcowych usługi](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Zaktualizuj konto usługi Cosmos, aby zabezpieczyć za pomocą punktów końcowych usługi, gdy podsieć jest ostatecznie skonfigurowany.|
|||
