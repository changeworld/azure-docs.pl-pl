---
title: Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB
description: Tworzenie i konfigurowanie usługi Azure Cosmos DB za pomocą szablonów usługi Azure Resource Manager.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961851"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB

Poniższe tabele zawierają łącza do szablonów usługi Azure Resource Manager dla usługi Azure Cosmos DB:

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

|**Szablonu**|**Opis**|
|---|---|
|[Tworzenie konta, bazy danych, kontenera usługi Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Ten szablon tworzy konto interfejsu API SQL (Core) w dwóch regionach z dwoma kontenerami z przepływnością udostępnionej bazy danych i kontenerem z dedykowaną przepływnością. Przepływność można zaktualizować, ponownie przeprowadzając szablon ze zaktualizowaną wartością właściwości przepływności. |
|[Tworzenie konta, bazy danych i kontenera usługi Azure Cosmos za pomocą procedury składowanej, wyzwalacza i udf](manage-sql-with-resource-manager.md#create-sproc) | Ten szablon tworzy konto interfejsu API SQL (Core) w dwóch regionach z procedurą składowaną, wyzwalaczem i UDF dla kontenera. |

## <a name="mongodb-api"></a>Interfejs API usługi MongoDB

|**Szablonu**|**Opis**|
|---| ---|
|[Tworzenie konta, bazy danych, kolekcji usługi Azure Cosmos](manage-mongodb-with-resource-manager.md#create-resource) | Ten szablon tworzy konto przy użyciu interfejsu API usługi Azure Cosmos DB dla usługi MongoDB w dwóch regionach z włączoną obsługą wielu wzorów. Konto usługi Azure Cosmos będzie miało dwa kontenery, które współużytkują przepływność na poziomie bazy danych. |

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

|**Szablonu**|**Opis**|
|---| ---|
|[Tworzenie konta, przestrzeni kluczy, tabeli usługi Azure Cosmos](manage-cassandra-with-resource-manager.md#create-resource) | Ten szablon tworzy konto interfejsu API Cassandra w dwóch regionach z włączoną obsługą wielu wzorów. Konto usługi Azure Cosmos będzie miało dwie tabele współużytkuje przepływność na poziomie przestrzeni kluczy. |

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

|**Szablonu**|**Opis**|
|---| ---|
|[Tworzenie konta, bazy danych, wykresu usługi Azure Cosmos](manage-gremlin-with-resource-manager.md#create-resource) | Ten szablon tworzy konto interfejsu API Gremlin w dwóch regionach z włączoną obsługą wielu wzorów. Konto usługi Azure Cosmos będzie miało dwa wykresy, które współużytkują przepływność na poziomie bazy danych. |

## <a name="table-api"></a>Interfejs API tabel

|**Szablonu**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Ten szablon tworzy konto interfejsu API tabeli w dwóch regionach z włączoną obsługą wielu wzorów. Konto usługi Azure Cosmos będzie miało jedną tabelę. |

> [!TIP]
> Aby włączyć przepływność współużytkowaną podczas korzystania z interfejsu API tabeli, włącz przepływność na poziomie konta w witrynie Azure Portal.

Zobacz [odwołanie usługi Azure Resource Manager dla usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) strony dla dokumentacji referencyjnej.
