---
title: Poziomy spójności i interfejsy API usługi Azure Cosmos DB
description: Opis mapowania poziomu spójności między różnymi interfejsami API w usłudze Azure Cosmos DB i Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131472"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsy API usługi Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia natywną obsługę interfejsów API zgodnych z protokołem przewodowym dla popularnych baz danych. Należą do nich MongoDB, Apache Cassandra, Gremlin i Azure Table Storage. Te bazy danych nie oferują dokładnie zdefiniowanych modeli spójności ani gwarancji opartych na umowy SLA dla poziomów spójności. Zazwyczaj zapewniają one tylko podzbiór pięciu modeli spójności oferowanych przez usługę Azure Cosmos DB. 

Podczas korzystania z interfejsu API SQL, interfejsu API gremlin i interfejsu API tabeli używany jest domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. 

Podczas korzystania z interfejsu API Cassandra lub interfejsu API usługi Azure Cosmos DB dla mongodb, aplikacje uzyskać pełny zestaw poziomów spójności oferowanych przez Apache Cassandra i MongoDB, odpowiednio, z jeszcze większą spójność i trwałość gwarancji. W tym dokumencie przedstawiono odpowiednie poziomy spójności usługi Azure Cosmos DB dla poziomów spójności Apache Cassandra i MongoDB.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Mapowanie między poziomami spójności Usługi Apache Cassandra i Usługi Azure Cosmos DB

W przeciwieństwie do usługi Azure Cosmos DB Apache Cassandra nie zapewnia natywnie precyzyjnie zdefiniowanych gwarancji spójności.  Zamiast tego Apache Cassandra zapewnia poziom spójności zapisu i poziom spójności odczytu, aby włączyć wysokiej dostępności, spójności i opóźnienia kompromisów. Podczas korzystania z interfejsu API Cassandra usługi Azure Cosmos DB: 

* Poziom spójności zapisu Apache Cassandra jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. Spójność dla operacji zapisu (CL) nie można zmienić na podstawie żądania.

* Usługa Azure Cosmos DB będzie dynamicznie mapować poziom spójności odczytu określony przez sterownik klienta Cassandra do jednego z poziomów spójności usługi Azure Cosmos DB skonfigurowanych dynamicznie na żądanie odczytu. 

W poniższej tabeli przedstawiono, jak natywne poziomy spójności Cassandra są mapowane do poziomów spójności usługi Azure Cosmos DB podczas korzystania z interfejsu API Cassandra:  

[![Mapowanie modelu spójności Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mapowanie między poziomami spójności usługi MongoDB i Usługi Azure Cosmos DB

W przeciwieństwie do usługi Azure Cosmos DB natywnej bazy danych MongoDB nie zapewnia dokładnie zdefiniowane gwarancje spójności. Zamiast tego natywne MongoDB umożliwia użytkownikom skonfigurować następujące gwarancje spójności: problem zapisu, problem odczytu i isMaster dyrektywy — do kierowania operacji odczytu do replik podstawowych lub pomocniczych, aby osiągnąć żądany poziom spójności. 

Podczas korzystania z interfejsu API usługi Azure Cosmos DB dla mongodb sterownik MongoDB traktuje region zapisu jako replikę podstawową i wszystkie inne regiony są odczytywane repliki. Można wybrać region skojarzony z kontem usługi Azure Cosmos jako replika podstawowa. 

Podczas korzystania z interfejsu API usługi Azure Cosmos DB dla mongodb:

* Problem zapisu jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos.
 
* Usługa Azure Cosmos DB będzie dynamicznie mapować problem odczytu określony przez sterownik klienta mongodb do jednego z poziomów spójności usługi Azure Cosmos DB, który jest skonfigurowany dynamicznie na żądanie odczytu.  

* Możesz dodawać adnotacje do określonego regionu skojarzonego z kontem usługi Azure Cosmos jako "Master", czyniąc region jako pierwszy region zapisywalny. 

W poniższej tabeli przedstawiono, jak natywne problemy z zapisem/odczytem usługi MongoDB są mapowane na poziomy spójności usługi Azure Cosmos podczas korzystania z interfejsu API usługi Azure Cosmos DB dla usługi MongoDB:

[![Mapowanie modelu spójności mongodb](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o poziomach spójności i zgodności między interfejsami API bazy danych usługi Azure Cosmos z interfejsami API typu open source. Zobacz następujące artykuły:

* [Kompromisy w zakresie dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Funkcje mongodb obsługiwane przez interfejs API usługi Azure Cosmos DB dla mongodb](mongodb-feature-support.md)
* [Funkcje Apache Cassandra obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB](cassandra-support.md)
