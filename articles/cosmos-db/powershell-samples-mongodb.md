---
title: Przykłady programu Azure PowerShell dla usługi Azure Cosmos DB — interfejs API usługi MongoDB
description: Pobieranie próbek programu Azure PowerShell do wykonywania różnych typowych zadań w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366209"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Przykłady programu Azure PowerShell dla interfejsu API mongodb usługi Azure Cosmos DB

Poniższa tabela zawiera łącza do przykładowych skryptów programu Azure PowerShell dla usługi Azure Cosmos DB dla interfejsu API mongodb.

> [!NOTE]
> Obecnie można utworzyć tylko wersję 3.2 (czyli konta przy użyciu `*.documents.azure.com`punktu końcowego w formacie) interfejsu API usługi Azure Cosmos DB dla kont MongoDB przy użyciu szablonów programu PowerShell, CLI i Resource Manager. Aby utworzyć 3.6 wersji kont, należy użyć witryny Azure portal zamiast tego.

> [!NOTE]
> W przykładach użyto poleceń cmdlet zarządzania [usługą Az.CosmosB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Należy pamiętać, `Az.CosmosDB` że polecenia cmdlet są nadal w wersji zapoznawczej i mogą ulec zmianie przed wydaniem. Proszę sprawdzić aktualizacje `Az.CosmosDB` regularnie.

| | |
|---|---|
|[Tworzenie konta, bazy danych i kolekcji](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy konto, bazę danych i kolekcję usługi Azure Cosmos. |
|[Wyświetlanie listy lub pobieranie baz danych lub kolekcji](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista lub pobierz bazę danych lub kolekcję. |
|[Pobierz RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz ru/s dla bazy danych lub kolekcji. |
|[Aktualizacja RU/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualizowanie usług RU/s dla bazy danych lub kolekcji. |
|[Aktualizowanie konta lub dodawanie regionu](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Dodawanie regionu do konta usługi Cosmos. Może również służyć do modyfikowania innych właściwości konta, ale muszą one być oddzielone od zmian w regionach. |
|[Zmiana priorytetu trybu failover lub wyzwalania trybu failover](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień regionalny priorytet pracy awaryjnej konta usługi Azure Cosmos lub wyzwolić ręczne tryb failover. |
|[Klucze kont lub parametry połączenia](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze, parametry połączenia lub ponownie wygeneruj klucz konta konta usługi Azure Cosmos. |
|[Tworzenie konta usługi Cosmos z zaporą IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Utwórz konto usługi Azure Cosmos z włączoną zaporą IP. |
|||
