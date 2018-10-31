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
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244192"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>Poziomy spójności i Cosmos DB z interfejsów API

Pięcioma modelami spójności są natywnie obsługiwane przez interfejs API SQL, co jest domyślny interfejs API, gdy za pomocą usługi Cosmos DB. Oprócz interfejsu API SQL Cosmos DB zapewnia natywną obsługę protokół przewodowy zgodnych interfejsów API dla popularnych baz danych, takich jak bazy danych MongoDB, Apache Cassandra, Gremlin i tabele platformy Azure. Te bazy danych oferują ani dokładnie zdefiniowanych modeli spójności ani objętym umową SLA gwarancje dotyczące poziomów spójności i zwykle zapewniają tylko podzbiór pięcioma modelami spójności oferowanych przez usługi Cosmos DB. Interfejs API SQL, interfejs API Gremlin i interfejsu API tabel jest używany domyślny poziom spójności skonfigurowane na konto usługi Cosmos.

W poniższej tabeli przedstawiono mapowania między spójności danych wymagany przez sterownik klienta OSS dla bazy danych Apache Cassandra 4.x i MongoDB 3.4, korzystając z interfejsu API rozwiązania Cassandra i interfejsu API usługi MongoDB, odpowiednio i odpowiadającymi im poziomami spójności usługi Cosmos DB.

## <a id="cassandra-mapping"></a>Mapowanie poziomów spójności bazy danych Apache Cassandra i Cosmos DB

W poniższej tabeli przedstawiono mapowania dla odczytu spójności między klientem 4.x bazy danych Apache Cassandra i Cosmos DB "Default" poziomu spójności dla wielu regionów i jednym regionie wdrażania.

| **Bazy danych Apache Cassandra 4.x** | **Usługa cosmos DB (wielu regionów)** | **Usługa cosmos DB (w jednym regionie)** |
| - | - | - |
| RAZ DWA TRZY | Spójny prefiks | Spójny prefiks |
| LOCAL_ONE | Spójny prefiks | Spójny prefiks |
| KWORUM, WSZYSTKIE, SERYJNY | Powiązana nieaktualność (ustawienie domyślne) lub silne hasło (w prywatnej wersji zapoznawczej) | Silna |
| LOCAL_QUORUM | Powiązana nieaktualność | Silna |
| LOCAL_SERIAL | Powiązana nieaktualność | Silna |

## <a id="mongo-mapping"></a>Mapowanie między poziomami spójności bazy danych MongoDB 3.4 i Cosmos DB

W poniższej tabeli przedstawiono mapowania "odczytu dotyczy" MongoDB 3.4 i Cosmos DB poziomu "Default" spójności dla wielu regionów i jednym regionie wdrażania.

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