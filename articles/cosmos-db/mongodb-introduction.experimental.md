---
title: 'Wprowadzenie do platformy Azure rozwiązania Cosmos bazy danych: baza danych MongoDB interfejsu API | Dokumentacja firmy Microsoft'
description: Poznaj wykorzystania bazy danych rozwiązania Cosmos Azure do przechowywania i bardzo dużych woluminów dokumentów JSON zapytania z niskim opóźnieniem, korzystając z popularnych interfejsów API bazy danych MongoDB OSS.
keywords: Co to jest MongoDB
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: anhoh
ms.openlocfilehash: cd80f349f2fb376178e02d947db1fefbcbe3ac88
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-azure-cosmos-db-mongodb-api"></a>Wprowadzenie do platformy Azure rozwiązania Cosmos bazy danych: baza danych MongoDB interfejsu API

[Azure Cosmos DB](../cosmos-db/introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Udostępnia bazę danych systemu Azure rozwiązania Cosmos [dystrybucji globalne gotowe](distribute-data-globally.md), [elastyczne skalowanie przepływność i magazyn](partition-data.md) na całym świecie, jednocyfrowej milisekundy opóźnienia 99-ty percentyl i wysokiej gwarantowane dostępność, wszystkie kopie przez [SLA branży](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Usługa Azure Cosmos DB [automatycznie indeksuje dane](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności zarządzania schematami i indeksami. To usługa wielomodelowa, obsługująca modele dokumentowe, klucz-wartość, wykresy i kolumny. 

![Azure rozwiązania Cosmos bazy danych: Baza danych MongoDB interfejsu API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Azure DB rozwiązania Cosmos baz danych może służyć do przechowywania danych dla aplikacji napisanych dla [MongoDB](https://docs.mongodb.com/manual/introduction/). Ta funkcja oznacza, że przy użyciu istniejących [sterowniki](https://docs.mongodb.org/ecosystem/drivers/), aplikacji tworzonych na potrzeby bazy danych MongoDB mogą teraz komunikować się z bazy danych Azure rozwiązania Cosmos i użyć bazy danych DB rozwiązania Cosmos Azure zamiast bazy danych MongoDB. W wielu przypadkach można przełączać z przy użyciu bazy danych MongoDB do bazy danych Azure rozwiązania Cosmos, zmieniając po prostu ciąg połączenia. Korzystanie z tej funkcji umożliwia łatwe tworzenie i uruchamianie aplikacji bazy danych MongoDB globalnie rozproszone na platformie Azure w chmurze z bazy danych Azure rozwiązania Cosmos i jej [kompleksowe SLA branży](https://azure.microsoft.com/support/legal/sla/cosmos-db), przy użyciu znanych umiejętności i narzędzia bazy danych mongodb.

**Zgodność z bazy danych MongoDB**: korzystania z istniejącej bazy danych MongoDB wiedzy, kod aplikacji i narzędziami bazy danych Azure rozwiązania Cosmos implementuje ten protokół przewodowy 3.4 bazy danych MongoDB (wersja 5) i obsługuje [potoku agregacji bazy danych MongoDB](mongodb-feature-support.md#aggregation-pipeline). Można tworzyć aplikacje przy użyciu bazy danych MongoDB i wdrożyć je do produkcji, używając w pełni zarządzana i globalnie rozproszone usługi bazy danych Azure rozwiązania Cosmos.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Jakie są zalety korzystania z bazy danych rozwiązania Cosmos Azure dla aplikacji bazy danych MongoDB?

**Elastycznie skalowalne przepływność i Magazyn:** spełniają wymagania aplikacji wymaga przez łatwe skalowanie w górę lub w dół bazy danych MongoDB. Dane są przechowywane na dyskach półprzewodnikowych (SSD, solid-state drive) dla zapewnienia przewidywalnych, niskich opóźnień. Azure DB rozwiązania Cosmos obsługuje bazy danych MongoDB kolekcje, które można skalować do niemal nieograniczonego rozmiaru magazynu i udostępnionej przepływności. W miarę wzrostu aplikacji usługę Azure Cosmos DB można bezproblemowo elastycznie skalować z przewidywalną wydajnością. 

**W przypadku replikacji:** bazy danych Azure rozwiązania Cosmos niewidocznie są replikowane do wszystkich regionach jest skojarzony z Twoim kontem bazy danych MongoDB, umożliwiając tworzenie aplikacji, które wymagają globalny dostęp do danych, zapewniając wady i zalety między spójności, dostępność i wydajność, wszystkie odpowiednie gwarancje. Usługa Azure Cosmos DB zawiera przezroczyste rozwiązania regionalnej pracy awaryjnej z międzyregionalnymi interfejsami API oraz możliwość elastycznego skalowania przepływności i magazynów na całym świecie. Dowiedz się więcej w [globalnie dystrybucji danych](distribute-data-globally.md).

**Nie zarządzania serwerem**: nie masz, zarządzanie i skalowania bazy danych MongoDB. Azure DB rozwiązania Cosmos jest w pełni zarządzana usługa, co oznacza, że nie trzeba zarządzać infrastruktury ani maszyn wirtualnych. Azure DB rozwiązania Cosmos jest dostępna w 30 + [regiony platformy Azure](https://azure.microsoft.com/regions/services/).

**Dostosowywalne poziomy spójności:** bazy danych Azure rozwiązania Cosmos implementuje obecnie bazy danych MongoDB w wersji 3.4, mają dwa ustawienia spójności silne i "ostateczna". Ponieważ bazy danych rozwiązania Cosmos Azure multi-api, ustawienia zgodności są stosowane na poziomie konta i wymuszania spójności jest kontrolowany przez każdego interfejsu API. Do 3,6 bazy danych MongoDB, nie było żadnych koncepcji spójność sesji, więc jeśli ustawisz konto bazy danych MongoDB interfejsu API, które będzie używane spójność sesji spójności jest obniżona do ostatecznego podczas korzystania z bazy danych MongoDB interfejsów API. Jeśli potrzebujesz gwarancji your właścicielem zapisu i odczytu konta bazy danych MongoDB interfejsu API, domyślny poziom spójności dla konta powinien być ustawiony na silne lub ograniczonych nieaktualności. Aby dowiedzieć się więcej, zobacz [Maksymalizowanie dostępności i wydajności za pomocą poziomów spójności](consistency-levels.md).

| Rozwiązania Cosmos Azure DB domyślny poziom spójności |   Interfejs API MONGO (3.4) |
|---|---|
|Ostateczna| Ostateczna |
|Spójny prefiks| Ostateczna z kolejnością spójne |
|Sesja| Ostateczna z kolejnością spójne |
|Powiązana nieaktualność| Silna |
| Silna | Silna |

**Automatyczne indeksowanie**: Domyślnie bazy danych rozwiązania Cosmos Azure automatycznie indeksuje wszystkie właściwości w dokumentach w Twojej bazy danych MongoDB bazy danych i nie oczekuje ani nie wymaga żadnego schematu lub tworzenia indeksów pomocniczych. Ponadto funkcja unikatowy indeks umożliwia ograniczenie unikatowości dla wszystkich pól dokumentu, które już są automatycznie indeksowane w usłudze Azure DB rozwiązania Cosmos.

**Klasy Enterprise**: bazy danych rozwiązania Cosmos Azure obsługuje wiele replik lokalnych do dostarczania 99,99% dostępności i ochrony danych w wypadku niepowodzenia lokalne i regionalne. Azure DB rozwiązania Cosmos ma klasy enterprise [certyfikaty zgodności](https://www.microsoft.com/trustcenter) i funkcje zabezpieczeń. 

Dowiedz się więcej na ten film z Azure rozwiązania Cosmos DB starszy Menedżer programu, Aleksey Savateyev.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T136/player]
> 

## <a name="how-to-get-started"></a>Jak zacząć

Wykonaj poradniki Szybki Start bazy danych MongoDB, aby utworzyć konto bazy danych Azure rozwiązania Cosmos i migracji istniejącej aplikacji bazy danych MongoDB do używania bazy danych Azure rozwiązania Cosmos, lub utworzyć nowy:

* [Migrowanie istniejących aplikacji sieci web Node.js bazy danych MongoDB](create-mongodb-nodejs.md).
* [Tworzenie aplikacji sieci web interfejsu API bazy danych MongoDB z usług .NET i portalu Azure](create-mongodb-dotnet.md)
* [Tworzenie aplikacji konsoli bazy danych MongoDB interfejsu API języka Java i portalu Azure](create-mongodb-java.md)

## <a name="next-steps"></a>Kolejne kroki

Informacje o interfejsie API Azure rozwiązania Cosmos bazy danych MongoDB jest zintegrowany z ogólną dokumentację bazy danych rozwiązania Cosmos Azure, ale poniżej przedstawiono kilka wskazówek, które ułatwią rozpoczęcie pracy:

* Postępuj zgodnie z [Połącz z kontem bazy danych MongoDB](connect-mongodb-account.md) samouczkiem, aby dowiedzieć się, jak uzyskać informacje o parametrach połączenia konta.
* Postępuj zgodnie z [3T Użyj Studio (MongoChef) z bazy danych Azure rozwiązania Cosmos](mongodb-mongochef.md) samouczkiem, aby dowiedzieć się, jak utworzyć połączenie między usługą Azure DB rozwiązania Cosmos bazy danych i aplikacji bazy danych MongoDB w Studio 3 T.
* Postępuj zgodnie z [migrowanie danych do bazy danych rozwiązania Cosmos Azure z protokołem obsługuje bazy danych mongodb](mongodb-migrate.md) samouczka w celu zaimportowania danych do interfejsu API dla bazy danych MongoDB.
* Połączyć z interfejsem API dotyczące korzystania z bazy danych MongoDB konta [Robomongo](mongodb-robomongo.md).
* Dowiedz się, ile RUs operacji za pomocą [GetLastRequestStatistics polecenia i portalu Azure metryki](request-units.md#GetLastRequestStatistics).
* Dowiedz się, jak [Konfigurowanie preferencji odczytu dla aplikacji rozproszonych globalnie](../cosmos-db/tutorial-global-distribution-mongodb.md).
