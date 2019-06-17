---
title: Krokach optymalizacji po przeprowadzeniu migracji przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
description: Ten dokument zawiera techniki optymalizacji po migracji z bazy danych MongoDB do interfejsu APi usługi Azure Cosmos DB dla Mongo DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61331222"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Krokach optymalizacji po przeprowadzeniu migracji przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB 

Po przeprowadzeniu migracji danych przechowywanych w bazie danych MongoDB do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, można połączyć z usługą Azure Cosmos DB i zarządzania danymi. Ten przewodnik zawiera kroki, które należy wziąć pod uwagę po zakończeniu migracji. Zobacz [migracji bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB, samouczek bazy danych MongoDB](../dms/tutorial-mongodb-cosmos-db.md) kroków migracji.

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:
- [Połącz aplikację](#connect-account)
- [Optymalizowanie zasad indeksowania](#indexing)
- [Konfigurowanie dystrybucji globalnej dla interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](#distribute-data)
- [Ustaw poziom spójności](#consistency)

> [!NOTE]
> Tylko obowiązkowe kroku po migracji na poziomu aplikacji jest zmiana parametrów połączenia w aplikacji w taki sposób, aby wskazywał nowego konta usługi Azure Cosmos DB. Wszystkie kroki migracji są zalecane optymalizacji.
>

## <a id="connect-account"></a>Połącz aplikację 

1. W oknie logowania do [witryny Azure portal](https://www.portal.azure.com/)
2. Z [witryny Azure portal](https://www.portal.azure.com/), w okienku po lewej stronie, otwórz **wszystkie zasoby** menu i Znajdź konto usługi Azure Cosmos DB, do którego zostały zmigrowane dane.
3. Otwórz **parametry połączenia** bloku. Prawe okienko zawiera wszystkie informacje potrzebne do pomyślnego połączenia z kontem.
4. Informacje o połączeniu w konfiguracji aplikacji (lub innych odpowiednich miejsc), aby odzwierciedlić interfejsu API usługi Azure Cosmos DB na użytek połączenia bazy danych MongoDB w swojej aplikacji. 
![Parametry połączenia](./media/mongodb-post-migration/connection-string.png)

Aby uzyskać więcej informacji, zobacz [połączyć aplikację MongoDB w usłudze Azure Cosmos DB](connect-mongodb-account.md) strony.

## <a id="indexing"></a>Optymalizowanie zasad indeksowania

Wszystkie pola danych są automatycznie indeksowane, domyślnie podczas migracji danych do usługi Azure Cosmos DB. W wielu przypadkach ta domyślnych zasad indeksowania jest dopuszczalne. Ogólnie rzecz biorąc usunięcie indeksów optymalizuje żądania zapisu i o domyślnych zasad (czyli automatyczne indeksowanie) indeksowania optymalizuje żądaniami odczytu.

Aby uzyskać więcej informacji na temat indeksowania, zobacz [danych indeksowanie w interfejsie API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-indexing.md) , jak również [indeksowanie w usłudze Azure Cosmos DB](index-overview.md) artykułów.

## <a id="distribute-data"></a>Globalnie Dystrybuuj dane

Usługa Azure Cosmos DB jest dostępna we wszystkich [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/#services) na całym świecie. Po wybraniu domyślnego poziomu spójności dla swojego konta usługi Azure Cosmos DB, możesz skojarzyć co najmniej jeden region platformy Azure (w zależności od potrzeb dystrybucji globalnej). Aby uzyskać wysoką dostępność i ciągłość prowadzenia działalności biznesowej zawsze zalecamy działające w co najmniej 2 regionach. Zapoznaj się z poradami dotyczącymi [optymalizacji kosztów wdrażania w wielu regionach w usłudze Azure Cosmos DB](optimize-cost-regions.md).

Globalnie Dystrybuuj dane, można znaleźć [dystrybuować dane globalnie na interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Ustaw poziom spójności
Usługa Azure Cosmos DB oferuje 5 dobrze zdefiniowanych [poziomów spójności](consistency-levels.md). Aby przeczytać o mapowaniu między poziomami spójności bazy danych MongoDB i usługi Azure Cosmos DB, przeczytaj [poziomy spójności i interfejsów API usługi Azure Cosmos DB](consistency-levels-across-apis.md). Domyślny poziom spójności jest poziom spójności sesji. Zmiana poziomu spójności jest opcjonalny, a następnie można dokonać optymalizacji aplikacji. Aby zmienić poziom spójności za pomocą witryny Azure portal:

1. Przejdź do **ustawienie domyślna spójność** bloku w obszarze Ustawienia.
2. Wybierz swoje [poziomu spójności](consistency-levels.md)

Większość użytkowników należy pozostawić ich poziomu spójności, domyślne ustawienie spójności sesji. Istnieją jednak [dostępność i wydajność kompromisy dla różnych poziomów spójności](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Kolejne kroki

* [Łączenie aplikacji bazy danych MongoDB z usługą Azure Cosmos DB](connect-mongodb-account.md)
* [Łączenie się z kontem usługi Azure Cosmos DB korzystanie z programu Studio 3T](mongodb-mongochef.md)
* [Jak globalnie dystrybuować odczytuje przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-readpreference.md)
* [Wygasanie danych za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-time-to-live.md)
* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
* [Indeksowanie w usłudze Azure Cosmos DB](index-overview.md)
* [Jednostki żądań w usłudze Azure Cosmos DB](request-units.md)





