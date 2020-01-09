---
title: Kroki optymalizacji po migracji z interfejsem API Azure Cosmos DB dla MongoDB
description: Ten dokument zawiera techniki optymalizacji po migracji z MongoDB do interfejsu APi usługi Mongo DB Azure Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c521546bedf1ebfd42bce4c50aa79b199553fd5a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441559"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Kroki optymalizacji po migracji podczas korzystania z interfejsu API Azure Cosmos DB dla MongoDB 

Po przeprowadzeniu migracji danych przechowywanych w bazie danych MongoDB Azure Cosmos DB do interfejsu API programu MongoDB, można nawiązać połączenie z Azure Cosmos DB i zarządzać danymi. Ten przewodnik zawiera opis kroków, które należy wziąć pod uwagę po migracji. Zapoznaj się z samouczkiem dotyczącym migracji [MongoDB Azure Cosmos DB do interfejsu API usługi MongoDB](../dms/tutorial-mongodb-cosmos-db.md) w celu zaplanowania czynności związanych z migracją.

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:
- [Łączenie aplikacji](#connect-account)
- [Optymalizowanie zasad indeksowania](#indexing)
- [Skonfiguruj dystrybucję globalną dla interfejsu API Azure Cosmos DB dla MongoDB](#distribute-data)
- [Ustaw poziom spójności](#consistency)

> [!NOTE]
> Jedynym wymaganym etapem po migracji na poziomie aplikacji jest zmiana parametrów połączenia w aplikacji w taki sposób, aby wskazywały nowe konto Azure Cosmos DB. Wszystkie inne kroki migracji to zalecane optymalizacje.
>

## <a id="connect-account"></a>Łączenie aplikacji 

1. W nowym oknie Zaloguj się do [Azure Portal](https://www.portal.azure.com/)
2. W obszarze [Azure Portal](https://www.portal.azure.com/)w lewym okienku Otwórz menu **wszystkie zasoby** i Znajdź konto Azure Cosmos DB, do którego przeprowadzono migrację danych.
3. Otwórz blok **parametrów połączenia** . Prawe okienko zawiera wszystkie informacje potrzebne do pomyślnego połączenia z kontem.
4. Użyj informacji o połączeniu w konfiguracji aplikacji (lub innych odpowiednich miejscach), aby odzwierciedlić interfejs API Azure Cosmos DB dla połączenia MongoDB w aplikacji. 
![](./media/mongodb-post-migration/connection-string.png) parametrów połączenia

Aby uzyskać więcej informacji, zobacz stronę [łączenie aplikacji MongoDB na Azure Cosmos DB](connect-mongodb-account.md) .

## <a id="indexing"></a>Optymalizowanie zasad indeksowania

Wszystkie pola danych są automatycznie indeksowane domyślnie podczas migracji danych do Azure Cosmos DB. W wielu przypadkach ta domyślna zasada indeksowania jest akceptowalna. Ogólnie rzecz biorąc, usuwanie indeksów optymalizuje żądania zapisu i ma domyślne zasady indeksowania (tj. Automatyczne indeksowanie) optymalizuje żądania odczytu.

Aby uzyskać więcej informacji na temat indeksowania, zobacz [indeksowanie danych w interfejsie API Azure Cosmos DB MongoDB](mongodb-indexing.md) oraz [indeksowanie w](index-overview.md) artykułach Azure Cosmos DB.

## <a id="distribute-data"></a>Dystrybuuj globalnie dane

Azure Cosmos DB jest dostępna we wszystkich [regionach świadczenia usługi Azure](https://azure.microsoft.com/regions/#services) na całym świecie. Po wybraniu domyślnego poziomu spójności dla konta Azure Cosmos DB można skojarzyć jeden lub więcej regionów świadczenia usługi Azure (w zależności od potrzeb związanych z dystrybucją globalną). Aby zapewnić wysoką dostępność i ciągłość biznesową, zawsze zalecamy uruchamianie w co najmniej dwóch regionach. Możesz zapoznać się ze wskazówkami dotyczącymi [optymalizacji kosztów wdrożeń wieloregionowych w Azure Cosmos DB](optimize-cost-regions.md).

Aby globalnie dystrybuować dane, zobacz [dystrybuowanie danych globalnie w interfejsie API Azure Cosmos DB MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Ustaw poziom spójności
Azure Cosmos DB oferuje 5 dobrze zdefiniowanych [poziomów spójności](consistency-levels.md). Aby zapoznać się z mapowaniem między MongoDB i Azure Cosmos DB poziomów spójności, odczyt [poziomów spójności i Azure Cosmos DB interfejsów API](consistency-levels-across-apis.md). Domyślny poziom spójności to poziom spójności sesji. Zmiana poziomu spójności jest opcjonalna i można ją zoptymalizować dla aplikacji. Aby zmienić poziom spójności przy użyciu Azure Portal:

1. Przejdź do **domyślnego bloku spójności** w obszarze Ustawienia.
2. Wybierz [poziom spójności](consistency-levels.md)

Większość użytkowników opuszcza poziom spójności w domyślnym ustawieniu spójności sesji. Istnieją jednak [kompromisy dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Następne kroki

* [Łączenie aplikacji bazy danych MongoDB z usługą Azure Cosmos DB](connect-mongodb-account.md)
* [Nawiązywanie połączenia z kontem Azure Cosmos DB przy użyciu programu Studio 3T](mongodb-mongochef.md)
* [Jak globalnie dystrybuować odczyty przy użyciu interfejsu API Azure Cosmos DB dla MongoDB](mongodb-readpreference.md)
* [Wygasanie danych za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-time-to-live.md)
* [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
* [Indeksowanie w usłudze Azure Cosmos DB](index-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)





