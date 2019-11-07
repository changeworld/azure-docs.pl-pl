---
title: Szablony Azure Resource Manager dla Azure Cosmos DB
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: ecdfe89e899e0d416784ae32a0d66b335c09e2b6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582806"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony Azure Resource Manager dla Azure Cosmos DB

W poniższych tabelach uwzględniono linki do Azure Resource Manager szablonów dla Azure Cosmos DB:

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

|**Szablon**|**Opis**|
|---|---|
|[Tworzenie konta, bazy danych, kontenera usługi Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejsu API programu SQL (rdzeń) w dwóch regionach z dwoma kontenerami z przepływem danych udostępnionej usługi Database i kontenerem o dedykowanej przepływności. Przepływność można zaktualizować przez ponowne przesłanie szablonu ze zaktualizowaną wartością właściwości przepływności. |
|[Utwórz konto usługi Azure Cosmos, bazę danych i kontener przy użyciu procedury składowanej, wyzwalacza i UDF](manage-sql-with-resource-manager.md#create-sproc) | Ten szablon służy do tworzenia konta interfejsu API SQL (rdzeń) w dwóch regionach z procedurą składowaną, wyzwalaczem i formatem UDF dla kontenera. |

## <a name="mongodb-api"></a>Interfejs API usługi MongoDB

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, bazę danych, kolekcję](manage-mongodb-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta przy użyciu interfejsu API Azure Cosmos DB dla MongoDB w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwa kontenery, które współużytkują przepływność na poziomie bazy danych. |
|[Aktualizowanie przepływności (RU/s) dla bazy danych](manage-mongodb-with-resource-manager.md#database-ru-update) | Ten szablon służy do aktualizowania przepływności dla bazy danych w ramach konta interfejsu API MongoDB. |
|[Aktualizacja przepływności (RU/s) dla kolekcji](manage-mongodb-with-resource-manager.md#collection-ru-update) | Ten szablon służy do aktualizowania przepływności dla kontenera na koncie interfejsu API MongoDB. |

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy, tabeli](manage-cassandra-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejs API Cassandra w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwie tabele, które współdzielą przepływność na poziomie przestrzeni kluczy. |
|[Aktualizacja przepływności (RU/s) dla przestrzeni kluczy](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Ten szablon służy do aktualizowania przepływności dla przestrzeni kluczy w ramach konta interfejs API Cassandra. |
|[Aktualizacja przepływności (RU/s) dla tabeli](manage-cassandra-with-resource-manager.md#table-ru-update) | Ten szablon służy do aktualizowania przepływności tabeli na koncie interfejs API Cassandra. |

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, bazę danych, wykres](manage-gremlin-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejsu API Gremlin w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwa wykresy, które współdzielą przepływność na poziomie bazy danych. |
|[Aktualizowanie przepływności (RU/s) dla bazy danych](manage-gremlin-with-resource-manager.md#database-ru-update) | Ten szablon służy do aktualizowania przepływności dla bazy danych w ramach konta interfejsu API Gremlin. |
|[Aktualizacja przepływności (RU/s) dla wykresu](manage-gremlin-with-resource-manager.md#graph-ru-update) | Ten szablon służy do aktualizowania przepływności wykresu na koncie interfejsu API Gremlin. |

## <a name="table-api"></a>Interfejs API tabel

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejs API tabel w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało jedną tabelę. |
|[Aktualizacja przepływności (RU/s) dla tabeli](manage-table-with-resource-manager.md#table-ru-update) | Ten szablon służy do aktualizowania przepływności tabeli na koncie interfejs API tabel. |

> [!TIP]
> Aby włączyć współdzieloną przepływność przy korzystaniu z interfejs API tabel, należy włączyć przepływność na poziomie konta w witrynie Azure Portal.

Aby uzyskać dokumentację referencyjną, zobacz sekcję dotyczącą usługi [ARM na stronie Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) .
