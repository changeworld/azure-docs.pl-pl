---
title: Poziomy spójności i interfejsy API usługi Azure Cosmos DB
description: Zrozumienie poziomów spójności między interfejsami API w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 76ebbc8cc8dbea4b7f8f8226cf1d8570a421e8cf
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034339"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsy API usługi Azure Cosmos DB

Pięć modeli spójności oferowanych przez usługę Azure Cosmos DB są natywnie obsługiwane przez interfejs API SQL usługi Azure Cosmos DB. Gdy używasz usługi Azure Cosmos DB, interfejs API SQL jest ustawieniem domyślnym. 

Usługa Azure Cosmos DB również zapewnia natywną obsługę sieci zgodnego z protokołem interfejsów API na potrzeby popularnych baz danych. Bazy danych obejmują bazy danych MongoDB, Apache Cassandra, Gremlin i Azure Table storage. Te bazy danych nie ma możliwości dokładnie zdefiniowanych modeli spójności lub objętym umową SLA gwarancje dotyczące poziomów spójności. Zwykle zapewniają tylko podzbiór pięcioma modelami spójności oferowanych przez usługę Azure Cosmos DB. Dla interfejsu API SQL, interfejs API Gremlin i interfejsu API tabel jest używany domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos DB. 

W poniższych sekcjach przedstawiono mapowanie między spójności danych wymagany przez sterownik klienta OSS dla bazy danych Apache Cassandra 4.x i MongoDB 3.4. Ten dokument zawiera również odpowiadającymi im poziomami spójności usługi Azure Cosmos DB dla bazy danych Apache Cassandra i bazy danych MongoDB.

## <a id="cassandra-mapping"></a>Mapowanie między poziomami spójności bazy danych Apache Cassandra usługi Azure Cosmos DB

W poniższej tabeli przedstawiono "spójności odczytu" mapowanie między klientem 4.x bazy danych Apache Cassandra i domyślny poziom spójności w usłudze Azure Cosmos DB. W tabeli przedstawiono wdrożenia w wielu regionach i jednym regionie.

| **Bazy danych Apache Cassandra 4.x** | **Usługa Azure Cosmos DB (wielu regionów)** | **Usługa Azure Cosmos DB (jednym regionie)** |
| - | - | - |
| RAZ DWA TRZY | Spójny prefiks | Spójny prefiks |
| LOCAL_ONE | Spójny prefiks | Spójny prefiks |
| KWORUM, WSZYSTKIE, SERYJNY | Powiązana nieaktualność jest ustawieniem domyślnym. Silne znajduje się w prywatnej wersji zapoznawczej. | Silna |
| LOCAL_QUORUM | Powiązana nieaktualność | Silna |
| LOCAL_SERIAL | Powiązana nieaktualność | Silna |

## <a id="mongo-mapping"></a>Mapowanie między poziomami spójności bazy danych MongoDB 3.4 i Azure Cosmos DB

W poniższej tabeli przedstawiono "Przeczytaj uwagi" mapowanie między MongoDB 3.4 i domyślny poziom spójności w usłudze Azure Cosmos DB. W tabeli przedstawiono wdrożenia w wielu regionach i jednym regionie.

| **MongoDB 3.4** | **Usługa Azure Cosmos DB (wielu regionów)** | **Usługa Azure Cosmos DB (jednym regionie)** |
| - | - | - |
| Linearizable | Silna | Silna |
| Większość | Powiązana nieaktualność | Silna |
| Lokalna | Spójny prefiks | Spójny prefiks |

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat poziomów spójności i zgodności usługi Azure Cosmos DB API za pomocą interfejsów API typu open source. Zobacz następujące artykuły:

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Funkcje bazy danych MongoDB, obsługiwane przez interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-feature-support.md)
* [Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API rozwiązania Cassandra usługi Azure Cosmos DB](cassandra-support.md)