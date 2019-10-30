---
title: Szablony Azure Resource Manager dla Azure Cosmos DB
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7861196daf3b04430200096a0d42bdcc6e6d5cf9
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053217"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony Azure Resource Manager dla Azure Cosmos DB

W poniższych tabelach uwzględniono linki do Azure Resource Manager szablonów dla Azure Cosmos DB:

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

|**Szablon**|**Opis**|
|---|---|
|[Tworzenie konta, bazy danych, kontenera usługi Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejsu API SQL (rdzeń) w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwa kontenery, które współużytkują przepływność na poziomie bazy danych. |
|[Utwórz konto usługi Azure Cosmos, bazę danych i kontener przy użyciu procedury składowanej, wyzwalacza i UDF](manage-sql-with-resource-manager.md#create-sproc) | Ten szablon służy do tworzenia konta interfejsu API SQL (rdzeń) w dwóch regionach z procedurą składowaną, wyzwalaczem i formatem UDF dla kontenera. |
|[Aktualizowanie przepływności (RU/s) dla bazy danych](manage-sql-with-resource-manager.md#database-ru-update) | Ten szablon służy do aktualizowania przepływności dla bazy danych w ramach konta interfejsu API programu SQL (Core). |
|[Aktualizacja przepływności (RU/s) dla kontenera](manage-sql-with-resource-manager.md#container-ru-update) | Ten szablon służy do aktualizowania przepływności dla kontenera w ramach konta interfejsu API SQL (rdzeń). |

## <a name="mongodb-api"></a>Interfejs API bazy danych MongoDB

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
