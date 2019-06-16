---
title: Poziomy spójności i interfejsy API usługi Azure Cosmos DB
description: Zrozumienie poziomów spójności między interfejsami API w usłudze Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: sngun
ms.openlocfilehash: 1129152c1823fbffb3d6c9ec918d7b8cb4426bbd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235625"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsy API usługi Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia natywną obsługę sieci zgodnego z protokołem interfejsów API na potrzeby popularnych baz danych. Obejmują one bazy danych MongoDB, Apache Cassandra, Gremlin i Azure Table storage. Te bazy danych, które nie oferują dokładnie zdefiniowanych modeli spójności lub objętym umową SLA gwarancje dotyczące poziomów spójności. Zwykle zapewniają tylko podzbiór pięcioma modelami spójności oferowanych przez usługę Azure Cosmos DB. 

Korzystając z interfejsu API SQL, interfejs API Gremlin i interfejsu API tabel, jest używany domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. 

Korzystając z interfejsu API rozwiązania Cassandra API lub usługi Azure Cosmos DB dla bazy danych MongoDB, pełny zestaw poziomów spójności oferowanych przez bazy danych Apache Cassandra i bazy danych MongoDB, odpowiednio, jeszcze lepsze spójności i gwarancje niezawodności uzyskiwania aplikacji. Ten dokument zawiera odpowiednie poziomy spójności w usłudze Azure Cosmos DB dla bazy danych Apache Cassandra i poziomów spójności bazy danych MongoDB.


## <a id="cassandra-mapping"></a>Mapowanie między poziomami spójności bazy danych Apache Cassandra usługi Azure Cosmos DB

W odróżnieniu od AzureCosmos DB bazy danych Apache Cassandra nie zawierają dokładnie zdefiniowane ustawienia spójności gwarancji.  Zamiast tego bazy danych Apache Cassandra zapewnia poziom spójności zapisu i poziomu spójności odczytu do włączenia wysokiej dostępności, spójności i opóźnienia stosowania kompromisów. W przypadku korzystania z interfejsu API rozwiązania Cassandra usługi Azure Cosmos DB: 

* Poziom spójności zapisu bazy danych Apache Cassandra jest zamapowana na domyślny poziom spójności skonfigurowane na Twoim koncie usługi Azure Cosmos. 

* Usługa Azure Cosmos DB będzie mapować dynamicznie poziomu spójności odczytu określonego przez sterownik klienta bazy danych Cassandra do jednego z poziomów spójności usługi Azure Cosmos DB, które są skonfigurowane dynamicznie, na żądanie odczytu. 

W poniższej tabeli przedstawiono, jak natywne poziomów spójności bazy danych Cassandra są mapowane na poziomy spójności usługi Azure Cosmos DB, korzystając z interfejsu API rozwiązania Cassandra:  

[![Mapowanie modelu spójności bazy danych Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mapowanie między poziomami spójności bazy danych MongoDB i usługi Azure Cosmos DB

W przeciwieństwie do usługi Azure Cosmos DB MongoDB natywnych nie gwarancje dokładnie zdefiniowane ustawienia spójności. Zamiast tego natywnej bazy danych MongoDB umożliwia użytkownikom na konfigurowanie następujące gwarancje spójności: kwestią zapisu, dotyczą odczytu i dyrektywy ismaster czy — w celu przekierowania operacji odczytu do repliki podstawowej lub dodatkowej do osiągnięcia poziomu spójności żądaną. 

Korzystając z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, sterownika bazy danych MongoDB traktuje region zapisu jako repliki podstawowej i wszystkie inne regiony odczytu replik. Można wybrać regionu, który został skojarzony z Twoim kontem usługi Azure Cosmos jako repliki podstawowej. 

Podczas korzystania z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB:

* Zastrzeżenia zapisu jest zamapowana na domyślny poziom spójności skonfigurowane na Twoim koncie usługi Azure Cosmos.
 
* Usługa Azure Cosmos DB będzie mapować dynamicznie odczytu kwestią, określonego przez sterownik klienta bazy danych MongoDB na jeden z poziomów spójności usługi Azure Cosmos DB, skonfigurowane dynamicznie na żądanie odczytu. 

* Możesz dodawać adnotacje do konkretnego regionu skojarzonych z Twoim kontem usługi Azure Cosmos, jako "Master", wprowadzając regionie, co pierwszy region zapisu. 

W poniższej tabeli przedstawiono, jak natywnej bazy danych MongoDB zapisu/odczytu uwagi są mapowane na poziomy spójności w usłudze Azure Cosmos, korzystając z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB:

[![Mapowanie modelu spójności bazy danych MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat poziomów spójności i zgodności usługi Azure Cosmos DB API za pomocą interfejsów API typu open source. Zobacz następujące artykuły:

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Funkcje bazy danych MongoDB, obsługiwane przez interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-feature-support.md)
* [Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API rozwiązania Cassandra usługi Azure Cosmos DB](cassandra-support.md)