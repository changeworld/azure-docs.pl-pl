---
title: Przykłady programu Azure PowerShell dla usługi Azure Cosmos DB
description: Przykłady dla programu Azure PowerShell — skrypty ułatwiające tworzenie kont usługi Azure Cosmos DB i zarządzanie nimi.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 3498ac6a2a4aaa1682d7b5bc5aae5383866d5bcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926166"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Przykłady sla programu Azure PowerShell dla usługi Azure Cosmos DB

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla usługi Azure Cosmos DB. W tej chwili za pomocą programu PowerShell można zarządzać tylko kontem usługi Azure Cosmos DB. Innymi zasobami, takimi jak bazy danych i kontenery, nie można zarządzać za pomocą programu PowerShell.

| |  |
|---|---|
|**Tworzenie konta usługi Azure Cosmos DB**||
|[Tworzenie i konfigurowanie konta usługi Cosmos przy użyciu interfejsu API SQL](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy jedno konto usługi Azure Cosmos DB do użycia z interfejsem API SQL. |
|[Tworzenie i konfigurowanie konta usługi Cosmos przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy jedno konto usługi Cosmos przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. |
|[Tworzenie i konfigurowanie konta usługi Cosmos przy użyciu interfejsu API języka Gremlin](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy jedno konto usługi Azure Cosmos DB do użycia z interfejsem Gremlin API. |
|[Tworzenie i konfigurowanie konta usługi Cosmos przy użyciu interfejsu API Cassandra](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy jedno konto usługi Azure Cosmos DB do użycia z interfejsem Cassandra API. |
|[Tworzenie i konfigurowanie konta usługi Cosmos przy użyciu interfejsu API tabel](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy jedno konto usługi Azure Cosmos DB do użycia z interfejsem API tabel. |
|**Skalowanie usługi Azure Cosmos DB**||
|[Replikowanie konta usługi Azure Cosmos DB w wielu regionach i konfigurowanie priorytetów trybu failover](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Globalnie replikuje dane konta w wielu regionach z określonym priorytetem trybu failover.|
|**Zabezpieczanie usługi Azure Cosmos DB**||
| [Uzyskiwanie kluczy kont](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Uzyskuje podstawowe i pomocnicze klucze główne zapisu oraz klucze podstawowe i pomocnicze tylko do odczytu dla konta.|
| [Uzyskiwanie parametrów połączenia bazy danych MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Uzyskuje parametry połączenia umożliwiające połączenie aplikacji bazy danych MongoDB z Twoim kontem usługi Azure Cosmos DB.|
|[Ponowne generowanie kluczy konta](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Ponownie generuje klucz główny lub klucz tylko do odczytu dla konta.|
|[Tworzenie zapory](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy zasady kontroli dostępu do adresów IP dla połączeń przychodzących w celu ograniczenia dostępu do konta z zatwierdzonego zestawu maszyn i/lub usług w chmurze.|
|**Wysoka dostępność, odzyskiwanie po awarii, tworzenie i przywracanie kopii zapasowej**||
|[Konfigurowanie zasad trybu failover](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Ustawia priorytet trybu failover poszczególnych regionów, w których konto jest replikowane.|
|||
