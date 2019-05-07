---
title: Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB
description: Szablony usługi Azure Resource Manager umożliwia tworzenie i konfigurowanie usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078463"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Azure Cosmos DB:

|**Typ interfejsu API** | **Link do przykładu**| **Opis** |
|---|---| ---|
|Interfejs API Core (SQL)| [Tworzenie konta usługi Azure Cosmos DB (Multi-master)](manage-sql-with-resource-manager.md) | Ten szablon tworzy konto interfejsu API SQL w dwóch regionach, z Multi-Master włączone. Konto usługi Cosmos Azure ma dwa kontenery, które współużytkują przepływności na poziomie bazy danych. |
| Interfejs API usługi MongoDB | [Tworzenie konta usługi Azure Cosmos DB (Multi-master)](manage-mongodb-with-resource-manager.md) | Ten szablon tworzy konto usługi przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB w dwóch regionach z Multi-Master włączone. Konto usługi Cosmos Azure ma dwa kontenery, które współużytkują przepływności na poziomie bazy danych. |
| Interfejs API rozwiązania Cassandra | [Tworzenie konta usługi Azure Cosmos DB (Multi-master)](manage-cassandra-with-resource-manager.md) | Ten szablon tworzy konto interfejsu API rozwiązania Cassandra w dwóch regionach, z Multi-Master włączone. Konto usługi Cosmos Azure mają dwie tabele, które współużytkują przestrzeń kluczy poziom przepływności. |
| Interfejs API języka Gremlin| [Tworzenie konta usługi Azure Cosmos DB (Multi-master)](manage-gremlin-with-resource-manager.md) | Ten szablon tworzy konto interfejsu API języka Gremlin w dwóch regionach, z Multi-Master włączone. Konto usługi Cosmos Azure będzie mieć dwa wykresy, które współużytkują przepływności na poziomie bazy danych. |
| Interfejs API tabel | [Tworzenie konta usługi Azure Cosmos DB (Multi-master)](manage-table-with-resource-manager.md) | Ten szablon tworzy konto interfejsu API tabeli w dwóch regionach za pomocą Multi-Master włączone. Konto usługi Cosmos Azure ma jedną tabelę. |

> [!TIP]
> Aby włączyć udostępnionej przepływności, korzystając z interfejsu API tabel, Włącz przepływności na poziomie konta w witrynie Azure Portal.

Zobacz [ARM odwołanie do usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) strony, aby uzyskać dokumentację referencyjną.