---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB
description: Przykłady interfejsu wiersza polecenia platformy Azure — tworzenie kont, baz danych, kontenerów, regionów i zapór usługi Azure Cosmos DB oraz zarządzanie nimi.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a6348024d4e84c27610f1294f916cca9a851b6b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892629"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB

Poniższa tabela zawiera linki do przykładowych skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB. Strony referencyjne dla wszystkich poleceń dostępnych w interfejsie wiersza polecenia usługi Azure Cosmos DB są dostępne w [dokumentacji dotyczącej interfejsu wiersza polecenia platformy Azure](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Tworzenie konta, bazy danych i kontenerów usługi Azure Cosmos DB**||
| [Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu API SQL](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy pojedyncze konto, bazę danych i kontener usługi Azure Cosmos DB. |
| [Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu API usługi Cosmos DB dla bazy danych MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy pojedyncze konto usługi Azure Cosmos DB, bazę danych i kolekcję. |
| [Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu API języka Gremlin](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy pojedyncze konto, bazę danych i graf usługi Azure Cosmos DB. |
| [Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu API Cassandra](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy pojedyncze konto i bazę danych usługi Azure Cosmos DB. |
| [Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu API tabel](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy pojedyncze konto, bazę danych i tabelę usługi Azure Cosmos DB. |
|**Skalowanie usługi Azure Cosmos DB**||
| [Skalowanie przepływności kontenerów](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Zmienia aprowizowaną przepływność w kontenerze.|
| [Replikowanie konta bazy danych usługi Azure Cosmos DB w wielu regionach i konfigurowanie priorytetów trybu failover](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globalnie replikuje dane konta w wielu regionach z określonym priorytetem trybu failover.|
|**Zabezpieczanie usługi Azure Cosmos DB**||
| [Uzyskiwanie kluczy kont](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Uzyskuje podstawowe i pomocnicze klucze główne zapisu oraz klucze podstawowe i pomocnicze tylko do odczytu dla konta.|
| [Pobieranie parametrów połączenia dla konta usługi Cosmos skonfigurowanego za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Uzyskuje parametry połączenia umożliwiające połączenie aplikacji bazy danych MongoDB z Twoim kontem usługi Azure Cosmos DB.|
| [Ponowne generowanie kluczy konta](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Ponownie generuje klucze konta.|
| [Tworzenie zapory](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy zasady kontroli dostępu do adresów IP dla połączeń przychodzących w celu ograniczenia dostępu do konta z zatwierdzonego zestawu maszyn i/lub usług w chmurze.|
|**Wysoka dostępność, odzyskiwanie po awarii, tworzenie i przywracanie kopii zapasowej**||
| [Konfigurowanie zasad trybu failover](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Ustawia priorytet trybu failover poszczególnych regionów, w których konto jest replikowane.|
|**Łączenie usługi Azure Cosmos DB z zasobami**||
| [Łączenie aplikacji internetowej z usługą Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Utwórz i połącz bazę danych usługi Azure Cosmos DB oraz aplikację internetową platformy Azure.|
|||
