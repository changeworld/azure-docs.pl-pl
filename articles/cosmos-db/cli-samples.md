---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB | Microsoft Docs
description: Przykłady interfejsu wiersza polecenia platformy Azure — tworzenie kont, baz danych, kontenerów, regionów i zapór usługi Azure Cosmos DB oraz zarządzanie nimi.
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/29/2017
ms.author: sngun
ms.openlocfilehash: 42cfe71210b95732b4b69f7ca21a8b647e187a38
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858864"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB

Poniższa tabela zawiera linki do przykładowych skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB. Strony referencyjne dla wszystkich poleceń dostępnych w interfejsie wiersza polecenia usługi Azure Cosmos DB są dostępne w [dokumentacji dotyczącej interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/cosmosdb).

| |  |
|---|---|
|**Tworzenie konta, bazy danych i kontenerów usługi Azure Cosmos DB**||
|[Tworzenie konta interfejsu API SQL](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy pojedyncze konto, bazę danych i kontener interfejsu API usługi Azure Cosmos DB do użytku z interfejsem API SQL. |
| [Tworzenie konta interfejsu API bazy danych MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy pojedyncze konto, bazę danych i kolekcję interfejsu API bazy danych MongoDB w usłudze Azure Cosmos DB. |
| [Tworzenie konta interfejsu Gremlin API](scripts/create-graph-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy pojedyncze konto, bazę danych i kontener interfejsu Gremlin API w usłudze Azure Cosmos DB. |
|**Skalowanie usługi Azure Cosmos DB**||
| [Skalowanie przepływności kontenerów](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Zmienia aprowizowaną przepływność w kontenerze.|
|[Replikowanie konta bazy danych usługi Azure Cosmos DB w wielu regionach i konfigurowanie priorytetów trybu failover](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globalnie replikuje dane konta w wielu regionach z określonym priorytetem trybu failover.|
|**Zabezpieczanie usługi Azure Cosmos DB**||
| [Uzyskiwanie kluczy kont](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Uzyskuje podstawowe i pomocnicze klucze główne zapisu oraz klucze podstawowe i pomocnicze tylko do odczytu dla konta.|
| [Uzyskiwanie parametrów połączenia bazy danych MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Uzyskuje parametry połączenia umożliwiające połączenie aplikacji bazy danych MongoDB z Twoim kontem usługi Azure Cosmos DB.|
|[Ponowne generowanie kluczy konta](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Ponownie generuje klucz główny lub klucz tylko do odczytu dla konta.|
|[Tworzenie zapory](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy zasady kontroli dostępu do adresów IP dla połączeń przychodzących w celu ograniczenia dostępu do konta z zatwierdzonego zestawu maszyn i/lub usług w chmurze.|
|**Wysoka dostępność, odzyskiwanie po awarii, tworzenie i przywracanie kopii zapasowej**||
|[Konfigurowanie zasad trybu failover](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Ustawia priorytet trybu failover poszczególnych regionów, w których konto jest replikowane.|
|**Łączenie usługi Azure Cosmos DB z zasobami**||
|[Łączenie aplikacji internetowej z usługą Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Utwórz i połącz bazę danych usługi Azure Cosmos DB oraz aplikację internetową platformy Azure.|
|||
