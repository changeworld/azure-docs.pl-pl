---
title: Kroki optymalizacji po migracji za pomocą interfejsu API usługi Azure Cosmos DB dla usługi MongoDB
description: Ten doc zapewnia techniki optymalizacji po migracji z MongoDB do APi usługi Azure Cosmos DB dla mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063612"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Kroki optymalizacji po migracji podczas korzystania z interfejsu API usługi Azure Cosmos DB dla usługi MongoDB

Po migracji danych przechowywanych w bazie danych MongoDB do interfejsu API usługi Azure Cosmos DB dla usługi MongoDB można połączyć się z usługą Azure Cosmos DB i zarządzać danymi. Ten przewodnik zawiera kroki, które należy wziąć pod uwagę po migracji. Zobacz [Migrowanie usługi MongoDB do interfejsu API usługi Azure Cosmos DB dla mongodb samouczka](../dms/tutorial-mongodb-cosmos-db.md) kroków migracji.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- [Łączenie aplikacji](#connect-your-application)
- [Optymalizacja zasad indeksowania](#optimize-the-indexing-policy)
- [Konfigurowanie dystrybucji globalnej dla interfejsu API usługi Azure Cosmos DB dla usługi MongoDB](#globally-distribute-your-data)
- [Ustawianie poziomu spójności](#set-consistency-level)

> [!NOTE]
> Jedynym obowiązkowym krokiem po migracji na poziomie aplikacji jest zmiana ciągu połączenia w aplikacji, aby wskazać nowe konto usługi Azure Cosmos DB. Wszystkie inne kroki migracji są zalecane optymalizacje.
>

## <a name="connect-your-application"></a>Łączenie aplikacji

1. W nowym oknie zaloguj się do [witryny Azure portal](https://www.portal.azure.com/)
2. Z [witryny Azure portal](https://www.portal.azure.com/)— w lewym okienku otwórz menu **Wszystkie zasoby** i znajdź konto usługi Azure Cosmos DB, do którego zostały zmigrowane dane.
3. Otwórz blok **Ciąg połączenia.** Prawe okienko zawiera wszystkie informacje potrzebne do pomyślnego połączenia z kontem.
4. Użyj informacji o połączeniu w konfiguracji aplikacji (lub innych odpowiednich miejscach), aby odzwierciedlić interfejs API usługi Azure Cosmos DB dla połączenia MongoDB w aplikacji.
![Connection-String](./media/mongodb-post-migration/connection-string.png)

Aby uzyskać więcej informacji, zobacz [connect aplikacji MongoDB do usługi Azure Cosmos DB](connect-mongodb-account.md) strony.

## <a name="optimize-the-indexing-policy"></a>Optymalizacja zasad indeksowania

Wszystkie pola danych są domyślnie indeksowane automatycznie podczas migracji danych do usługi Azure Cosmos DB. W wielu przypadkach ta domyślna zasada indeksowania jest dopuszczalna. Ogólnie rzecz biorąc, usunięcie indeksów optymalizuje żądania zapisu i o domyślne zasady indeksowania (tj. automatyczne indeksowanie) optymalizuje odczytu żądań.

Aby uzyskać więcej informacji na temat indeksowania, zobacz [indeksowanie danych w interfejsie API usługi Azure Cosmos DB dla mongodb,](mongodb-indexing.md) a także [indeksowanie w witrynie Azure Cosmos DB](index-overview.md) artykułów.

## <a name="globally-distribute-your-data"></a>Globalna dystrybucja danych

Usługa Azure Cosmos DB jest dostępna we wszystkich [regionach platformy Azure](https://azure.microsoft.com/regions/#services) na całym świecie. Po wybraniu domyślnego poziomu spójności dla konta usługi Azure Cosmos DB można skojarzyć co najmniej jeden region platformy Azure (w zależności od potrzeb dystrybucji globalnej). Aby uzyskać wysoką dostępność i ciągłość działania, zawsze zalecamy uruchamianie w co najmniej 2 regionach. Możesz zapoznać się ze wskazówkami dotyczącymi [optymalizacji kosztów wdrożeń wieloregionasowych w usłudze Azure Cosmos DB.](optimize-cost-regions.md)

Aby globalnie rozpowszechniać dane, zobacz [Dystrybucja danych globalnie w interfejsie API usługi Azure Cosmos DB dla mongodb.](tutorial-global-distribution-mongodb.md)

## <a name="set-consistency-level"></a>Ustawianie poziomu spójności

Usługa Azure Cosmos DB oferuje 5 dobrze zdefiniowanych [poziomów spójności.](consistency-levels.md) Aby dowiedzieć się więcej o mapowaniu między poziomami spójności usługi MongoDB i usługi Azure Cosmos DB, [odczytuj poziomy spójności i interfejsy API usługi Azure Cosmos DB](consistency-levels-across-apis.md). Domyślnym poziomem spójności jest poziom spójności sesji. Zmiana poziomu spójności jest opcjonalna i można go zoptymalizować dla aplikacji. Aby zmienić poziom spójności przy użyciu witryny Azure portal:

1. Przejdź do **bloku Spójność domyślna** w obszarze Ustawienia.
2. Wybierz [swój poziom spójności](consistency-levels.md)

Większość użytkowników opuszcza swój poziom spójności przy domyślnym ustawieniu spójności sesji. Istnieją jednak [kompromisy w zakresie dostępności i wydajności dla różnych poziomów spójności.](consistency-levels-tradeoffs.md)

## <a name="next-steps"></a>Następne kroki

* [Łączenie aplikacji bazy danych MongoDB z usługą Azure Cosmos DB](connect-mongodb-account.md)
* [Łączenie się z kontem usługi Azure Cosmos DB przy użyciu programu Studio 3T](mongodb-mongochef.md)
* [Jak globalnie rozpowszechniać odczyty przy użyciu interfejsu API usługi Azure Cosmos DB dla usługi MongoDB](mongodb-readpreference.md)
* [Wygasanie danych za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-time-to-live.md)
* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
* [Indeksowanie w usłudze Azure Cosmos DB](index-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)