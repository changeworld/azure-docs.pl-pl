---
title: Poziomy spójności i interfejsy API usługi Azure Cosmos DB
description: Zrozumienie poziomów spójności między interfejsami API w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: b620ca76cfea296e504afffd91852308a01575db
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002010"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsy API usługi Azure Cosmos DB

Pięć modeli spójności oferowanych przez usługę Azure Cosmos DB są natywnie obsługiwane przez interfejs API SQL. Gdy używasz usługi Azure Cosmos DB, interfejs API SQL jest ustawieniem domyślnym. 

Usługa Azure Cosmos DB również zapewnia natywną obsługę sieci zgodnego z protokołem interfejsów API na potrzeby popularnych baz danych. Bazy danych obejmują bazy danych MongoDB, Apache Cassandra, Gremlin i Azure Table storage. Te bazy danych nie ma możliwości dokładnie zdefiniowanych modeli spójności lub objętym umową SLA gwarancje dotyczące poziomów spójności. Zwykle zapewniają tylko podzbiór pięcioma modelami spójności oferowanych przez usługę Azure Cosmos DB. Dla interfejsu API SQL, interfejs API Gremlin i interfejsu API tabel jest używany domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. 

W poniższych sekcjach przedstawiono mapowanie między spójności danych, żądane przez sterownik klienta OSS dla bazy danych Apache Cassandra, MongoDB i odpowiadającymi im poziomami spójności w usłudze Azure Cosmos DB.

## <a id="cassandra-mapping"></a>Mapowanie między poziomami spójności bazy danych Apache Cassandra usługi Azure Cosmos DB

Poniższa tabela zawiera opis różnych kombinacji spójności, które umożliwia korzystanie z interfejsu API rozwiązania Cassandra i równoważne spójności natywne mapowanie poziomu usługi Cosmos DB. Połączenie wszystkich trybach odczytu i zapisu bazy danych Apache Cassandra są natywnie obsługiwane przez usługi Cosmos DB. W przypadku każdej kombinacji zapisu bazy danych Apache Cassandra i modelu spójności odczytu usługi Cosmos DB zapewnia gwarancje spójności równym lub większym niż bazy danych Apache Cassandra. Ponadto usługi Cosmos DB zapewnia gwarancje większą trwałość niż bazy danych Apache Cassandra, nawet w trybie najsłabsza, liczby operacji zapisu.

W poniższej tabeli przedstawiono **zapisu mapowanie spójności** między usługi Azure Cosmos DB i bazy danych Cassandra:

| Cassandra | Azure Cosmos DB | Gwarancja |
| - | - | - |
|WSZYSTKIE|Silna  | Operacje atomowe |
| EACH_QUORUM   | Silna    | Operacje atomowe | 
| KWORUM SZEREGOWEJ |  Silna |    Operacje atomowe |
| LOCAL_QUORUM, 3, DWÓCH, JEDEN, LOCAL_ONE, WSZYSTKIE | Spójny prefiks |Globalne spójny prefiks |
| EACH_QUORUM   | Silna    | Operacje atomowe |
| KWORUM SZEREGOWEJ |  Silna |    Operacje atomowe |
| LOCAL_QUORUM, 3, DWÓCH, JEDEN, LOCAL_ONE, WSZYSTKIE | Spójny prefiks | Globalne spójny prefiks |
| KWORUM SZEREGOWEJ | Silna   | Operacje atomowe |
| LOCAL_QUORUM, 3, DWÓCH, JEDEN, LOCAL_ONE, WSZYSTKIE | Spójny prefiks | Globalne spójny prefiks |
| LOCAL_QUORUM, LOCAL_SERIAL, DWÓCH, TRZECH    | Powiązana nieaktualność | <ul><li>Powiązana nieaktualność.</li><li>Co najwyżej K wersji lub czasu (t) za zaporą.</li><li>Odczytaj najnowsze zatwierdzone wartość w regionie.</li></ul> |
| JEDEN, LOCAL_ONE, WSZYSTKIE   | Spójny prefiks | Spójny prefiks regionu |

W poniższej tabeli przedstawiono **mapowanie spójności odczytu** między usługi Azure Cosmos DB i bazy danych Cassandra:

| Cassandra | Azure Cosmos DB | Gwarancja |
| - | - | - |
| WSZYSTKIE KWORUM, SERYJNY LOCAL_QUORUM, LOCAL_SERIAL, 3, DWÓCH, JEDEN, LOCAL_ONE | Silna  | Operacje atomowe|
| WSZYSTKIE KWORUM, SERYJNY LOCAL_QUORUM, LOCAL_SERIAL, TRZY, DWA   |Silna |   Operacje atomowe |
|LOCAL_ONE, PO JEDNYM | Spójny prefiks | Globalne spójny prefiks |
| WSZYSTKIM KWORUM, SERYJNY   | Silna    | Operacje atomowe |
| LOCAL_ONE, JEDNYM, LOCAL_QUORUM LOCAL_SERIAL, DWÓCH, TRZECH |  Spójny prefiks   | Globalne spójny prefiks |
| LOCAL_ONE, JEDNEGO, DWÓCH, TRZECH LOCAL_QUORUM, KWORUM |    Spójny prefiks   | Globalne spójny prefiks |
| WSZYSTKIE KWORUM, SERYJNY LOCAL_QUORUM, LOCAL_SERIAL, TRZY, DWA   |Silna |   Operacje atomowe |
| LOCAL_ONE, PO JEDNYM    | Spójny prefiks | Globalne spójny prefiks|
| WSZYSTKIE KWORUM, SERIAL silne atomowych
LOCAL_ONE, JEDNYM, LOCAL_QUORUM LOCAL_SERIAL, DWÓCH, TRZECH  |Spójny prefiks  | Globalne spójny prefiks |
|WSZYSTKIE    |Silna |Operacje atomowe |
| LOCAL_ONE, JEDNEGO, DWÓCH, TRZECH LOCAL_QUORUM, KWORUM  |Spójny prefiks  |Globalne spójny prefiks|
|WSZYSTKIE KWORUM, SERIAL silne atomowych
LOCAL_ONE, JEDNYM, LOCAL_QUORUM LOCAL_SERIAL, DWÓCH, TRZECH  |Spójny prefiks  |Globalne spójny prefiks |
|WSZYSTKIE    |Silna | Operacje atomowe |
| LOCAL_ONE, JEDNEGO, DWÓCH, TRZECH LOCAL_QUORUM, KWORUM  | Spójny prefiks | Globalne spójny prefiks |
| KWORUM, LOCAL_QUORUM, LOCAL_SERIAL, DWA, TRZY |  Powiązana nieaktualność   | <ul><li>Powiązana nieaktualność.</li><li>Co najwyżej K wersji lub czasu (t) za zaporą. </li><li>Odczytaj najnowsze zatwierdzone wartość w regionie.</li></ul>
| LOCAL_ONE, PO JEDNYM |Spójny prefiks | Spójny prefiks regionu |
| LOCAL_ONE, JEDNEGO, DWÓCH, TRZECH LOCAL_QUORUM, KWORUM  | Spójny prefiks | Spójny prefiks regionu |


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