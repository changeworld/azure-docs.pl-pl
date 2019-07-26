---
title: Poziomy spójności i interfejsy API usługi Azure Cosmos DB
description: Informacje o poziomach spójności w interfejsach API w Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 26cea6243a8b6d06c132325f0b2fe830c4030e9d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467775"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsy API usługi Azure Cosmos DB

Azure Cosmos DB zapewnia natywną obsługę interfejsów API zgodnych z protokołem przewodowym dla popularnych baz danych. Obejmują one MongoDB, Apache Cassandra, Gremlin i Azure Table Storage. Te bazy danych nie oferują precyzyjnie zdefiniowanych modeli spójności ani gwarancji objętych umową SLA dla poziomów spójności. Zwykle zapewniają tylko podzestaw pięciu modeli spójności oferowanych przez Azure Cosmos DB. 

W przypadku korzystania z interfejsu API SQL, interfejsu API Gremlin i interfejs API tabel, używany jest domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. 

W przypadku korzystania z interfejsu API interfejs API Cassandra lub Azure Cosmos DB dla MongoDB aplikacje uzyskają pełny zestaw poziomów spójności oferowanych przez Apache Cassandra i MongoDB, a nawet silniejsze gwarancje spójności i trwałości. W tym dokumencie przedstawiono odpowiednie Azure Cosmos DB poziomów spójności dla poziomów spójności Apache Cassandra i MongoDB.


## <a id="cassandra-mapping"></a>Mapowanie między Cassandra Apache i Azure Cosmos DB poziomów spójności

W przeciwieństwie do AzureCosmos DB, Apache Cassandra nie zapewnia natywnej obsługi precyzyjnie określonych gwarancji spójności.  Zamiast tego Apache Cassandra zapewnia poziom spójności zapisu i poziom spójności odczytu, aby zapewnić wysoką dostępność, spójność i wady opóźnienia. W przypadku korzystania z interfejs API Cassandra Azure Cosmos DB: 

* Poziom spójności zapisu usługi Apache Cassandra jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. 

* Azure Cosmos DB dynamicznie mapuje poziom spójności odczytu określony przez sterownik klienta Cassandra na jeden z Azure Cosmos DB poziomów spójności skonfigurowanych dynamicznie na żądanie odczytu. 

W poniższej tabeli przedstawiono, jak natywne poziomy spójności Cassandra są mapowane na poziomy spójności Azure Cosmos DB podczas korzystania interfejs API Cassandra:  

[![Mapowanie modelu spójności Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mapowanie między MongoDB i Azure Cosmos DB poziomów spójności

W przeciwieństwie do Azure Cosmos DB, natywny MongoDB nie zapewnia precyzyjnie zdefiniowanych gwarancji spójności. Zamiast tego natywny MongoDB umożliwia użytkownikom skonfigurowanie następujących gwarancji spójności: uwagi dotyczące zapisu, Odczyt i dyrektywa IsMaster — aby skierować operacje odczytu do replik podstawowych lub pomocniczych w celu osiągnięcia żądanego poziomu spójności. 

W przypadku korzystania z interfejsu API Azure Cosmos DB dla MongoDB, sterownik MongoDB traktuje region zapisu jako replikę podstawową i wszystkie pozostałe regiony są odczytywane z repliki. Możesz wybrać region skojarzony z kontem usługi Azure Cosmos jako replikę podstawową. 

Podczas korzystania z interfejsu API Azure Cosmos DB dla MongoDB:

* Problem dotyczący zapisu jest mapowany na domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos.
 
* Azure Cosmos DB dynamicznie mapuje problem odczytu określony przez sterownik klienta MongoDB na jeden z Azure Cosmos DB poziomów spójności skonfigurowanych dynamicznie na żądanie odczytu. 

* Można dodać adnotacje do określonego regionu skojarzonego z kontem usługi Azure Cosmos jako "Master", tworząc region jako pierwszy zapisywalny region. 

W poniższej tabeli pokazano, w jaki sposób natywne zagadnienia dotyczące zapisu/odczytu MongoDB są mapowane na poziomy spójności usługi Azure Cosmos w przypadku korzystania z interfejsu API Azure Cosmos DB dla MongoDB:

[![Mapowanie modelu spójności MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat poziomów spójności i zgodności między interfejsami API Azure Cosmos DB przy użyciu interfejsów API Open Source. Zobacz następujące artykuły:

* [Dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Funkcje MongoDB obsługiwane przez interfejs API Azure Cosmos DB dla MongoDB](mongodb-feature-support.md)
* [Funkcje Apache Cassandra obsługiwane przez Azure Cosmos DB interfejs API Cassandra](cassandra-support.md)