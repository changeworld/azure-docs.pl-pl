---
title: Typowe przypadki użycia i scenariusze dla usługi Azure Cosmos DB
description: 'Dowiedz się więcej o pierwszych pięciu przypadki użycia usługi Azure Cosmos DB: wygenerowaną przez użytkowników zawartość, rejestrowanie zdarzeń, danych wykazu, dane preferencje użytkownika i Internetu rzeczy (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: e22b426b2172c169f9343569fffac57f370afbee
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219888"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Typowe przypadki użycia usługi Azure Cosmos DB
Ten artykuł zawiera omówienie kilku typowych przypadków użycia usługi Azure Cosmos DB.  Zalecenia przedstawione w tym artykule służyć jako punkt początkowy, podczas opracowywania aplikacji za pomocą usługi Cosmos DB.   

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania: 

* Jakie są typowe przypadki użycia usługi Azure Cosmos DB?
* Jakie są korzyści z używania usługi Azure Cosmos DB dla aplikacji handlu detalicznego?
* Jakie są korzyści z używania usługi Azure Cosmos DB do przechowywania danych dla systemów Internetu rzeczy (IoT)?
* Jakie są korzyści z używania usługi Azure Cosmos DB dla aplikacji internetowych i mobilnych?

## <a name="introduction"></a>Wprowadzenie
[Usługa Azure Cosmos DB](../cosmos-db/introduction.md) to usługa globalnie rozproszona baza danych firmy Microsoft. Service została zaprojektowana, aby umożliwić klientom elastyczne (i niezależne) Skaluj przepływność oraz Magazyn w dowolnej liczbie regionów geograficznych. Azure Cosmos DB to pierwsza usługa globalnie rozproszonej bazy danych na rynku już dziś do oferty kompleksowe [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/cosmos-db/) obejmujący przepływności, opóźnienia, dostępności i spójności. 

Usługa Azure Cosmos DB jest globalne dystrybuowanej, wielomodelowej bazy danych, która jest używana w szerokiej gamy aplikacji i przypadkami użycia. To dobry wybór dla każdego [bezserwerowe](https://azure.com/serverless) aplikację, która wymaga krótkim kolejności z milisekund czasem odpowiedzi oraz potrzebuje skalowania szybkiego i globalnie. Obsługuje ona wiele modeli danych (klucz-wartość, dokumenty, wykresy i kolumnowy) i wiele interfejsów API na potrzeby dostępu do danych, w [Azure Cosmos DB tym interfejs API usługi MongoDB](mongodb-introduction.md), [interfejs API SQL](documentdb-introduction.md), [interfejs API Gremlin](graph-introduction.md), interfejs [API tabel](table-introduction.md) natywnie i rozszerzalny skoordynowany. 

Poniżej przedstawiono niektóre atrybuty usługi Azure Cosmos DB, które ułatwiają one odpowiednie dla aplikacji o wysokiej wydajności przy użyciu globalnych ambicji.

* Usługa Azure Cosmos DB natywnie partycje dane dotyczące wysokiej dostępności i skalowalności. Usługa Azure Cosmos DB oferuje gwarantuje dostępność przez 99,99% dla dostępności, przepływności, małego opóźnienia i spójności na wszystkich kont w jednym regionie i wszystkich kont w wielu regionach za pomocą rozluźnionej spójności i dostępności na wszystkich multiregionalne konta baz danych do odczytu przez 99,999% czasu.
* Usługa Azure Cosmos DB ma magazynu SSD kopie z czasem odpowiedzi kolejności z milisekund o małych opóźnieniach.
* Pomoc techniczna platformy Azure Cosmos DB dla poziomów spójności ostatecznej, spójny prefiks, sesja i powiązana nieaktualność umożliwia pełną elastyczność i niski współczynnik wydajności kosztów. Żadna usługa bazy danych oferuje tylu elastyczności w usłudze Azure Cosmos DB w poziomach spójności. 
* Usługa Azure Cosmos DB ma o elastyczny model cenowy przyjaznego dla danych, który niezależnie liczniki usług magazynu i przepływności.
* Model zarezerwowaną przepływność danych usługi Azure Cosmos DB można traktować pod względem liczby operacji odczytu/zapisu, zamiast Procesora/pamięci/operacji We/Wy bazowego sprzętu.
* Danych usługi Azure Cosmos DB projektu pozwala na skalowanie do żądania dużych woluminów zgodnie z kolejnością biliony żądań dziennie.

Te atrybuty są korzystne w sieci web, mobilnych, gier i aplikacji IoT, które muszą krótkim czasem odpowiedzi i wymagają obsługi ogromnych ilości operacji odczytu i zapisu.

## <a name="iot-and-telematics"></a>IoT i telematyka
Przypadki użycia IoT często mają pewne wzorców w sposób pozyskiwanie ich, proces i przechowywania danych.  Najpierw te systemy musisz pozyskiwać wzrosty danych z czujników urządzenia z różnych ustawień regionalnych. Następnie te systemy przetwarzać i analizować dane przesyłane strumieniowo w czasie rzeczywistym analiz. Następnie archiwizowania danych do zimnego magazynu w przypadku analizy wsadowej. Microsoft Azure oferuje bogate usługi, które mogą być stosowane do przypadków użycia IoT, w tym Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, centrum powiadomień platformy Azure, Azure Machine Learning, Azure HDInsight i Power BI. 

![Architektura odwołań usługi Azure Cosmos DB IoT](./media/use-cases/iot.png)

Przez usługi Azure Event Hubs można pozyskać wzrosty danych, ponieważ oferuje on wysokiej przepływności, pozyskiwania danych z małymi opóźnieniami. Dane pozyskane, który ma zostać przetworzony, aby uzyskać wgląd w czasie rzeczywistym można funneled do usługi Azure Stream Analytics dla analizy w czasie rzeczywistym. Dane można załadować do usługi Azure Cosmos DB do wykonywania zapytań ad hoc. Po załadowaniu danych do usługi Azure Cosmos DB dane są gotowe do zbadania. Ponadto nowe dane i zmian do istniejących danych mogą być odczytywane na kanał informacyjny zmian. Kanał informacyjny zmiany jest trwały, Dołącz tylko dziennik, który przechowuje zmiany w kontenerach Cosmos w kolejności sekwencyjnej. Wszystkie dane lub po prostu zmian danych w usłudze Azure Cosmos DB może służyć jako dane referencyjne w ramach analizy w czasie rzeczywistym. Ponadto dane dalsze można Elegancja i przetwarzane przez nawiązywania połączenia z danymi usługi Azure Cosmos DB HDInsight do zadań Pig, Hive i Map/Reduce.  Dostosowany dane są następnie ładowane do usługi Azure Cosmos DB do raportowania.   

Aby uzyskać przykładowe rozwiązanie IoT przy użyciu usługi Azure Cosmos DB, EventHubs i Storm, zobacz [repozytorium usługi hdinsight — — przykłady dotyczące systemu storm w usłudze GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Aby uzyskać więcej informacji na temat ofert na platformie Azure dla IoT, zobacz [Stwórz Internet swoich rzeczy —](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Sprzedaży i marketingu
Usługa Azure Cosmos DB jest szeroko stosowany w platformach e handlu firmy Microsoft z systemem Windows Store i XBox Live. Służy również w branży handlu detalicznego do przechowywania danych wykazu i określania źródła w kolejności potoków przetwarzania zdarzeń.

Scenariusze użycia usługi wykaz danych obejmują przechowywania i wykonywania zapytań względem zestaw atrybutów jednostki, takie jak osoby, miejsca i produktów. Przykładowe dane wykazu są kontami użytkowników, z wykazami różnych produktów, rejestrów urządzeń IoT i zestawienie materiałów systemów. Atrybuty dla tych danych mogą się różnić i może ulegać zmianie w celu dopasowania do wymagań aplikacji.

Rozważ przykład katalog produktów dla dostawcy samochodowych. Każdej części mogą mieć własne atrybuty, oprócz wspólne atrybuty, które współużytkują wszystkie elementy. Ponadto atrybuty dla określonej części można zmienić następny rok po udostępnieniu nowego modelu. Usługa Azure Cosmos DB obsługuje schematach elastycznych i dane hierarchiczne, i dlatego dobrze nadają się do przechowywania danych wykazu produktów.

![Architektura referencyjna katalogu handlu detalicznego Azure Cosmos DB](./media/use-cases/product-catalog.png)

Usługa Azure Cosmos DB jest często używana do określania źródła zdarzeń do zasilania oparte na zdarzeniach architektury przy użyciu jego [zestawienia zmian](change-feed.md) funkcji. Kanał informacyjny zmian zapewnia podrzędnego mikrousług, niezawodne i stopniowo odczytywanie INSERT i Update (na przykład kolejność zdarzeń), wprowadzone do usługi Azure Cosmos DB. Ta funkcja może zostać użyta do zapewnienia magazynu trwałego zdarzeń jako broker komunikatów zdarzeń zmiany stanu i przepływu pracy przetwarzania zamówień dysku między wiele mikrousług (które można zaimplementować jako [bezserwerowej usługi Azure Functions](https://azure.com/serverless)).

![Usługa Azure Cosmos DB w kolejności architektury referencyjnej potoku](./media/use-cases/event-sourcing.png)

Ponadto dane przechowywane w usłudze Azure Cosmos DB można zintegrować z HDInsight do analizy danych big data przy użyciu zadań platformy Apache Spark. Aby uzyskać szczegółowe informacje dotyczące łącznika Spark dla usługi Azure Cosmos DB, zobacz [uruchamianie zadania Spark za pomocą usługi Cosmos DB i HDInsight](spark-connector.md).

## <a name="gaming"></a>Gry
Warstwa bazy danych jest kluczowym składnikiem aplikacji gier. Nowoczesne gry wykonywania przetwarzania graficznych na klientach mobilnych/konsoli, ale polegać na chmurze i dostarczać zawartość dostosowane i spersonalizowaną, taką jak statystyki gry, integracja z mediów społecznościowych i rankingi. Gier często wymagają opóźnień o wielkości pojedynczych milisekund dla operacji odczytu i zapisu zapewnienie interesujące w grze środowisko. Gry baza danych musi być szybkie i mieć możliwość obsługi również ogromne wzrosty liczby żądań podczas nowych uruchomień gry i aktualizacje funkcji.

Azure Cosmos DB jest używany przez gry, [takie jak idące: Brak terenów](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) firmy człowiek w [następnych grach](https://www.nextgames.com/)i [Halo 5: Opiekunowie](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Usługa Azure Cosmos DB zapewnia następujące korzyści dla deweloperów gier:

* Usługa Azure Cosmos DB umożliwia wydajności można skalować w górę lub dół elastycznie. Dzięki temu gry do obsługi aktualizacji profilu i statystyk z dziesiątek, jak do milionów równoczesnych graczy, wprowadzając jednego wywołania interfejsu API.
* Usługa Azure Cosmos DB obsługuje milisekund odczytuje i zapisuje w celu uniknięcia spowolnienia wszelkie podczas gry.
* Automatyczne indeksowanie danych usługi Azure Cosmos DB umożliwia filtrowanie względem wielu różnych właściwości w czasie rzeczywistym, na przykład zlokalizuj graczy przez ich wewnętrzne player identyfikatorów lub GameCenter, Facebook, Google identyfikatorów lub zapytań na podstawie player członkostwa w guild. Jest to możliwe bez konieczności tworzenia złożonych indeksowania lub infrastruktury dzielenia na fragmenty.
* Funkcje społecznościowe, w tym wiadomości rozmów w grze, odtwarzacza guild członkostwa, wyzwań ukończone, rankingi i grafów społecznościowych są łatwiejsze do wdrożenia przy użyciu elastycznego schematu.
* Usługa Azure Cosmos DB jako zarządzanego platform-as-a-service (PaaS) wymagane minimalnej liczby zarządzania współpracują w celu umożliwienia szybkiego iteracji i skróceniu czasu wprowadzania produktów na rynek.

![Architektura referencyjna gier w usłudze Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplikacji sieci Web i aplikacje mobilne
Usługa Azure Cosmos DB jest najczęściej używany w sieci web i aplikacje mobilne i jest dobrze nadaje się do modelowania społecznościowych interakcji, integracji z usługami innych firm oraz do tworzenia rozbudowanych spersonalizowanych środowisk. Zestawy SDK usługi DB Cosmos może być używane kompilacji sformatowanego z systemem iOS i aplikacji dla systemu Android za pomocą popularnej [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Społecznościowe aplikacje
Typowy przypadek użycia usługi Azure Cosmos DB jest do przechowywania i wykonywania zapytań względem zawartości wygenerowane przez użytkowników (UGC) dla sieci web, aplikacji mobilnych i społecznościowych. Niektóre przykłady UGC są, sesje rozmów, tweety, wpisy w blogu, oceny i komentarze. Często UGC w mediach społecznościowych, aplikacji jest blend tekst w dowolnej postaci, właściwości, tagi i relacje, które nie są ograniczone przez sztywne struktury. Zawartości, takiej jak rozmowy, komentarze i wpisy mogą być przechowywane w usługi Cosmos DB bez konieczności przekształcenia lub obiektu złożonego do relacyjnego mapowania warstw.  Właściwości danych można dodać lub zmodyfikować łatwe do potrzeb deweloperów przejść przez kod aplikacji, więc podwyższania poziomu szybkiego tworzenia zawartości.  

Aplikacje, które integrują się z sieciami społecznościowymi firm reagować na zmieniające się schematów z tych sieci. Ponieważ dane są automatycznie indeksowane domyślnie w usłudze Cosmos DB, dane są gotowe do można wykonywać zapytania w dowolnym momencie. Dzięki temu te aplikacje elastycznie do pobrania projekcji zgodnie ze swoimi potrzebami odpowiednich.

Wiele aplikacji społecznościowych w skali globalnej i może cechować się nieprzewidywalnych wzorcach użycia. Elastyczność skalowania w magazynie danych jest niezbędne, zgodnie z warstwy aplikacji, skalowanie uwzględniające użycie żądanie.  Można skalować w poziomie przez dodanie dodatkowych danych partycji w ramach konta usługi Cosmos DB.  Ponadto można również utworzyć dodatkowych kont usługi Cosmos DB w wielu regionach. Aby uzyskać dostępność regionów usługi Cosmos DB, zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/#services).

![Architektura referencyjna aplikacji sieci web usługi Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalizacja
Dzisiaj nowoczesnych aplikacji pochodzą z widokami złożone i środowisk. Są to zazwyczaj dynamiczny, świadcząc usługi dla preferencji użytkownika lub nastrój i znakowanie potrzeb. W związku z tym aplikacje muszą mieć możliwość pobrania spersonalizowane ustawienia efektywnie, aby szybko renderowania elementów interfejsu użytkownika i środowiska. 

JSON, w formacie obsługiwanym przez usługi Cosmos DB to skuteczne format do reprezentowania danych układ interfejsu użytkownika, ponieważ nie jest tylko uproszczone, ale także mogą być łatwo interpretowane przez język JavaScript. Usługa cosmos DB oferuje poziomy możliwości dostosowania spójności, które umożliwiają szybkie odczyty z zapisu małych opóźnień. W związku z tym przechowywanie danych układ interfejsu użytkownika, w tym spersonalizowane ustawienia jako dokumenty JSON w usłudze Cosmos DB jest skutecznym sposobem do pobrania tych danych w sieci.

![Architektura referencyjna aplikacji sieci web usługi Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z usługą Azure Cosmos DB, postępuj zgodnie z naszym [przewodniki szybkiego startu](create-sql-api-dotnet.md), które przeprowadzą Cię przez tworzenie konta i rozpoczynania pracy z usługą Cosmos DB.

* Jeśli chcesz dowiedzieć się więcej o klientach przy użyciu Azure Cosmos DB, zobacz stronę [analizy przypadków klientów](https://azure.microsoft.com/en-us/case-studies/?service=cosmos-db) .
