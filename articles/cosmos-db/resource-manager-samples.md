---
title: Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB
description: Szablony usługi Azure Resource Manager umożliwia tworzenie i konfigurowanie usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969184"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB

Poniższe tabele zawierają linki do szablonów usługi Azure Resource Manager dla usługi Azure Cosmos DB:

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, bazy danych, kontenerów](manage-sql-with-resource-manager.md#create-resource) | Ten szablon tworzy konto interfejsu API SQL (rdzenie) w dwóch regionach, z Multi-Master włączone. Konto usługi Cosmos Azure ma dwa kontenery, które współużytkują przepływności na poziomie bazy danych. |
|[Aktualizuj przepływność (RU/s) dla bazy danych](manage-sql-with-resource-manager.md#database-ru-update) | Ten szablon zaktualizuje przepływności bazy danych w ramach konta interfejsu API SQL (rdzenie). |
|[Aktualizuj przepływność (RU/s) dla kontenera](manage-sql-with-resource-manager.md#container-ru-update) | Ten szablon zaktualizuje przepływność dla kontenera w ramach konta interfejsu API SQL (rdzeni). |

## <a name="mongodb-api"></a>Interfejs API usługi MongoDB

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, bazy danych, kolekcji](manage-mongodb-with-resource-manager.md#create-resource) | Ten szablon tworzy konto usługi przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB w dwóch regionach z Multi-Master włączone. Konto usługi Cosmos Azure ma dwa kontenery, które współużytkują przepływności na poziomie bazy danych. |
|[Aktualizuj przepływność (RU/s) dla bazy danych](manage-mongodb-with-resource-manager.md#database-ru-update) | Ten szablon zaktualizuje przepływności bazy danych w ramach konta interfejsu API usługi MongoDB. |
|[Aktualizuj przepływność (RU/s) dla kolekcji](manage-mongodb-with-resource-manager.md#collection-ru-update) | Ten szablon zaktualizuje przepływność dla kontenera w ramach konta interfejsu API usługi MongoDB. |

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, przestrzeń kluczy, tabeli](manage-cassandra-with-resource-manager.md#create-resource) | Ten szablon tworzy konto interfejsu API rozwiązania Cassandra w dwóch regionach, z Multi-Master włączone. Konto usługi Cosmos Azure mają dwie tabele, które współużytkują przestrzeń kluczy poziom przepływności. |
|[Aktualizowanie przestrzeni kluczy przepływność (RU/s)](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Ten szablon zaktualizuje przepływności na przestrzeń kluczy, w ramach konta interfejsu API rozwiązania Cassandra. |
|[Aktualizuj tabelę przepływność (RU/s)](manage-cassandra-with-resource-manager.md#table-ru-update) | Ten szablon zaktualizuje przepływności dla tabeli w ramach konta interfejsu API rozwiązania Cassandra. |

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, bazy danych, wykres](manage-gremlin-with-resource-manager.md#create-resource) | Ten szablon tworzy konto interfejsu API języka Gremlin w dwóch regionach, z Multi-Master włączone. Konto usługi Cosmos Azure będzie mieć dwa wykresy, które współużytkują przepływności na poziomie bazy danych. |
|[Aktualizuj przepływność (RU/s) dla bazy danych](manage-gremlin-with-resource-manager.md#database-ru-update) | Ten szablon zaktualizuje przepływności bazy danych w ramach konta interfejsu API języka Gremlin. |
|[Aktualizowanie wykresu przepływność (RU/s)](manage-gremlin-with-resource-manager.md#graph-ru-update) | Ten szablon zaktualizuje przepływność dla wykresu w ramach konta interfejsu API języka Gremlin. |

## <a name="table-api"></a>Interfejs API tabel

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos w tabeli](manage-table-with-resource-manager.md#create-resource) | Ten szablon tworzy konto interfejsu API tabeli w dwóch regionach za pomocą Multi-Master włączone. Konto usługi Cosmos Azure ma jedną tabelę. |
|[Aktualizuj tabelę przepływność (RU/s)](manage-table-with-resource-manager.md#table-ru-update) | Ten szablon zaktualizuje przepływności dla tabeli w ramach konta interfejsu API tabel. |

> [!TIP]
> Aby włączyć udostępnionej przepływności, korzystając z interfejsu API tabel, Włącz przepływności na poziomie konta w witrynie Azure Portal.

Zobacz [ARM odwołanie do usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) strony, aby uzyskać dokumentację referencyjną.