---
title: Poziomy spójności i interfejsów API usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Zrozumienie poziomów spójności między interfejsami API w usłudze Azure Cosmos DB.
keywords: spójność, usługi azure cosmos db, modeli, tabeli, bazy danych mongodb, programu graph, azure, bazy danych cassandra, platformy Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956387"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsów API usługi Azure Cosmos DB

Pięcioma modelami spójności oferowanych przez usługę Azure Cosmos DB są natywnie obsługiwane przez Cosmos DB SQL API, czyli domyślnego interfejsu API, korzystając z usługi Cosmos DB. Oprócz interfejsu API SQL Cosmos DB zapewnia natywną obsługę protokół przewodowy zgodnych interfejsów API dla popularnych baz danych, takich jak bazy danych MongoDB, Apache Cassandra, Gremlin i tabele platformy Azure. Te bazy danych, ani oferować dokładnie zdefiniowanych modeli spójności ani objętym umową SLA gwarancje dotyczące poziomów spójności. Te bazy danych zwykle zapewniają tylko podzbiór pięcioma modelami spójności oferowanych przez usługi Cosmos DB. Interfejs API SQL, interfejs API Gremlin i interfejsu API tabel jest używany domyślny poziom spójności, który został skonfigurowany na konto usługi Cosmos.

W poniższych sekcjach przedstawiono mapowanie między spójności danych wymagany przez sterownik klienta OSS dla bazy danych Apache Cassandra 4.x i MongoDB 3.4, korzystając z interfejsu API rozwiązania Cassandra i interfejsu API usługi MongoDB, odpowiednio i odpowiadającymi im poziomami spójności usługi Cosmos DB.

## <a id="cassandra-mapping"></a>Mapowanie między poziomami spójności bazy danych Apache Cassandra usługi Cosmos DB

W poniższej tabeli przedstawiono "spójności odczytu" mapowanie między klientem 4.x bazy danych Apache Cassandra i domyślny poziom spójności w usłudze Cosmos DB, w przypadku wdrożeń zarówno w wielu regionach, jak i w jednym regionie.

| **Bazy danych Apache Cassandra 4.x** | **Usługa cosmos DB (wielu regionów)** | **Usługa cosmos DB (w jednym regionie)** |
| - | - | - |
| RAZ DWA TRZY | Spójny prefiks | Spójny prefiks |
| LOCAL_ONE | Spójny prefiks | Spójny prefiks |
| KWORUM, WSZYSTKIE, SERYJNY | Powiązana nieaktualność (ustawienie domyślne) lub silne hasło (w prywatnej wersji zapoznawczej) | Silna |
| LOCAL_QUORUM | Powiązana nieaktualność | Silna |
| LOCAL_SERIAL | Powiązana nieaktualność | Silna |

## <a id="mongo-mapping"></a>Mapowanie między poziomami spójności bazy danych MongoDB 3.4 i Cosmos DB

W poniższej tabeli przedstawiono "Przeczytaj uwagi" mapowanie między MongoDB 3.4 i domyślny poziom spójności w usłudze Cosmos DB, w przypadku wdrożeń zarówno w wielu regionach, jak i w jednym regionie.

| **MongoDB 3.4** | **Usługa cosmos DB (wielu regionów)** | **Usługa cosmos DB (w jednym regionie)** |
| - | - | - |
| Linearizable | Silna | Silna |
| Większość | Powiązana nieaktualność | Silna |
| Lokalna | Spójny prefiks | Spójny prefiks |

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat poziomów spójności i zgodność między Cosmos DB z interfejsów API za pomocą interfejsów API typu open source w następujących artykułach:

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Funkcje bazy danych MongoDB, obsługiwane przez interfejsu API MongoDB usługi Cosmos DB](mongodb-feature-support.md)
* [Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API Cosmos DB Cassandra](cassandra-support.md)