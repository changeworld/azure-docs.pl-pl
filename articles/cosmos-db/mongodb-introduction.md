---
title: 'Wprowadzenie do usługi Azure Cosmos DB: interfejs API usługi MongoDB'
description: Dowiedz się, jak korzystać z usługi Azure Cosmos DB do przechowywania i wysyłania zapytań do ogromnych wolumenów dokumentów JSON z małym opóźnieniem przy użyciu popularnych interfejsów API MongoDB OSS.
keywords: co to jest MongoDB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: overview
ms.date: 02/12/2018
ms.author: sclyon
ms.openlocfilehash: 9acad93382a7a88b7af7c72a7308e64dc428cef5
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863996"
---
# <a name="introduction-to-azure-cosmos-db-mongodb-api"></a>Wprowadzenie do usługi Azure Cosmos DB: interfejs API usługi MongoDB

[Azure Cosmos DB](../cosmos-db/introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Usługa Azure Cosmos DB zapewnia [kompleksową globalną dystrybucję](distribute-data-globally.md), [elastyczne skalowanie przepływności i pamięci](partition-data.md) w skali globalnej, milisekundowe opóźnienia w 99. percentylu oraz gwarantowaną wysoką dostępność, wspierane przez [wiodące w branży umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Usługa Azure Cosmos DB [automatycznie indeksuje dane](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności zarządzania schematami i indeksami. To usługa wielomodelowa, obsługująca modele dokumentowe, klucz-wartość, wykresy i kolumny. 

![Azure Cosmos DB: interfejs API usługi MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Bazy danych usługi Azure Cosmos DB mogą służyć jako magazyn danych dla aplikacji napisanych dla usługi [MongoDB](https://docs.mongodb.com/manual/introduction/). Ta funkcja oznacza, że przy użyciu istniejących [sterowników](https://docs.mongodb.org/ecosystem/drivers/) aplikacja napisana dla usługi MongoDB może się teraz komunikować z usługą Azure Cosmos DB i używać baz danych usługi Azure Cosmos DB zamiast baz danych MongoDB. W wielu przypadkach można zastąpić usługę MongoDB usługą Azure Cosmos DB, po prostu zmieniając parametry połączenia. Dzięki tej funkcji można w prosty sposób kompilować i uruchamiać rozproszone globalnie aplikacje baz danych usługi MongoDB w chmurze platformy Azure przy użyciu usługi Azure Cosmos DB i [kompleksowych, wiodących w branży umów SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db), używając nabytych umiejętności i znanych narzędzi do pracy z usługą MongoDB.

**Zgodność z bazą danych MongoDB**: można korzystać ze zdobytej już wiedzy na temat usługi MongoDB, kodu aplikacji oraz narzędzi, a usługę Azure Cosmos DB wykorzystać do wdrażania protokołu przewodowego usługi MongoDB. Można tworzyć aplikacje przy użyciu usługi MongoDB i wdrażać je do produkcji, używając w pełni zarządzanej i globalnie rozproszonej usługi Azure Cosmos DB. Aby uzyskać więcej informacji na temat obsługiwanych wersji, zobacz sekcję [Obsługa protokołów bazy danych MongoDB](mongodb-feature-support.md#mongodb-protocol-support).

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Jakie są zalety korzystania z usługi Azure Cosmos DB do aplikacji usługi MongoDB?

**Elastycznie skalowalne przepływność i magazyn**: spełnienie wymagań aplikacji przez łatwe skalowanie w górę lub w dół usługi MongoDB. Dane są przechowywane na dyskach półprzewodnikowych (SSD, solid-state drive) dla zapewnienia przewidywalnych, niskich opóźnień. Usługa Azure Cosmos DB obsługuje kolekcje usługi MongoDB, które można skalować do niemal nieograniczonego rozmiaru magazynu i aprowizowanej przepływności. W miarę wzrostu aplikacji usługę Azure Cosmos DB można bezproblemowo elastycznie skalować z przewidywalną wydajnością. 

**Replikacja w wielu regionach:** Usługa Azure Cosmos DB w sposób przezroczysty replikuje dane do wszystkich regionów skojarzonych z kontem usługi MongoDB, umożliwiając tworzenie aplikacji wymagających globalnego dostępu do danych, a jednocześnie zapewniając kompromis między spójnością, dostępnością i wydajnością z odpowiednimi gwarancjami. Usługa Azure Cosmos DB zawiera przezroczyste rozwiązania regionalnej pracy awaryjnej z międzyregionalnymi interfejsami API oraz możliwość elastycznego skalowania przepływności i magazynów na całym świecie. Dowiedz się więcej z artykułu [Distribute data globally](distribute-data-globally.md) (Globalna dystrybucja danych).

**Brak zarządzania serwerem**: nie trzeba zarządzać bazami danych MongoDB ani ich skalować. Azure Cosmos DB jest w pełni zarządzaną usługą, co oznacza, że nie trzeba zarządzać samodzielnie żadną infrastrukturą ani maszynami wirtualnymi. Usługa Azure Cosmos DB jest dostępna w ponad 30 [regionach świadczenia usług platformy Azure](https://azure.microsoft.com/regions/services/).

**Poziomy spójności z możliwością dostosowywania**: ponieważ usługa Azure Cosmos DB obsługuje wielomodelowe interfejsy API, ustawienia spójności mają zastosowanie na poziomie konta, a wymuszanie spójności jest kontrolowane przez poszczególne interfejsy API. Przed wprowadzeniem usługi MongoDB 3.6 koncepcja spójności sesji nie istniała, więc w przypadku ustawienia konta interfejsu API usługi MongoDB do korzystania ze spójności sesji spójność zostanie obniżona do ostatecznej podczas używania interfejsów API usługi MongoDB. Jeśli potrzebujesz gwarancji odczytu własnego zapisu do konta interfejsu API usługi MongoDB, domyślny poziom spójności dla konta powinien zostać ustawiony na silny lub na powiązaną nieaktualność. Aby dowiedzieć się więcej, zobacz [Maksymalizowanie dostępności i wydajności za pomocą poziomów spójności](consistency-levels.md).

| Domyślny poziom spójności usługi Azure Cosmos DB |   Interfejs API Mongo (3.4) |
|---|---|
|Ostateczna| Ostateczna |
|Spójny prefiks| Ostateczna ze spójną kolejnością |
|Sesja| Ostateczna ze spójną kolejnością |
|Powiązana nieaktualność| Silna |
| Silna | Silna |

**Automatyczne indeksowanie:** domyślnie usługa Azure Cosmos DB automatycznie indeksuje wszystkie właściwości w dokumentach w bazie danych MongoDB i nie oczekuje ani nie wymaga żadnego schematu lub tworzenia indeksów pomocniczych. Ponadto funkcja indeksu unikatowego umożliwia ograniczenie unikatowości w dowolnych polach dokumentu, które zostały już automatycznie poindeksowane w usłudze Azure Cosmos DB.

**Klasa korporacyjna**: usługa Azure Cosmos DB obsługuje wiele lokalnych replik, aby zapewnić 99,99% dostępności oraz ochrony danych na wypadek awarii lokalnej lub regionalnej. Usługa Azure Cosmos DB ma [certyfikaty zgodności](https://www.microsoft.com/trustcenter) i funkcje zabezpieczeń klasy korporacyjnej. 

## <a name="how-to-get-started"></a>Jak zacząć

Wykonaj instrukcje z przewodników Szybki start usługi MongoDB, aby utworzyć konto usługi Azure Cosmos DB i przeprowadzić migrację istniejącej aplikacji MongoDB i używać usługi Azure Cosmos DB lub utworzyć nową:

* [Azure Cosmos DB: migracja istniejącej aplikacji internetowej MongoDB w środowisku Node.js](create-mongodb-nodejs.md).
* [Azure Cosmos DB: Tworzenie aplikacji sieci Web interfejsu API usługi MongoDB za pomocą programu .NET i witryny Azure Portal](create-mongodb-dotnet.md)
* [Azure Cosmos DB: Tworzenie aplikacji konsolowej interfejsu API usługi MongoDB przy użyciu języka Java i witryny Azure Portal](create-mongodb-java.md)

## <a name="next-steps"></a>Następne kroki

Informacje o interfejsie API usługi MongoDB usługi Azure Cosmos DB zostały zintegrowane z ogólną dokumentacją usługi Azure Cosmos DB, ale poniżej znajduje się kilka wskazówek, które ułatwią Ci rozpoczęcie pracy:

* Postępuj zgodnie z instrukcjami z samouczka [Connect to a MongoDB account](connect-mongodb-account.md) (Łączenie z kontem usługi MongoDB) i dowiedz się, jak uzyskać informacje dotyczące parametrów połączenia konta.
* Postępuj zgodnie z instrukcjami z samouczka [Use Studio 3T (MongoChef) with Azure Cosmos DB](mongodb-mongochef.md) [Korzystanie z programu Studio 3T (MongoChef) w usłudze Azure Cosmos DB] i dowiedz się, jak utworzyć połączenie między bazą danych usługi Azure Cosmos DB i aplikacją MongoDB w programie Studio 3T.
* Postępuj zgodnie z instrukcjami z samouczka [Migrate data to Azure Cosmos DB with protocol support for MongoDB](mongodb-migrate.md) (Migrowanie danych do usługi Azure Cosmos DB przy użyciu obsługi protokołów dla usługi MongoDB) i importuj dane do interfejsu API dla bazy danych usługi MongoDB.
* Połącz się z interfejsem API do konta usługi MongoDB przy użyciu programu [Robomongo](mongodb-robomongo.md).
* Dowiedz się, jak [konfigurować preferencje odczytu dla aplikacji rozproszonych globalnie](../cosmos-db/tutorial-global-distribution-mongodb.md).
