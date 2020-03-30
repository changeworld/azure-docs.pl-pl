---
title: Typowe przypadki użycia i scenariusze usługi Azure Cosmos DB
description: 'Dowiedz się więcej o pięciu najważniejszych przypadkach użycia usługi Azure Cosmos DB: zawartość generowana przez użytkownika, rejestrowanie zdarzeń, dane katalogu, dane preferencji użytkownika i Internet rzeczy (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888933"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Typowe przypadki użycia usługi Azure Cosmos DB
Ten artykuł zawiera omówienie kilku typowych przypadków użycia usługi Azure Cosmos DB.  Zalecenia zawarte w tym artykule służą jako punkt wyjścia podczas tworzenia aplikacji za pomocą usługi Cosmos DB.   

Po przeczytaniu tego artykułu będziesz mógł odpowiedzieć na następujące pytania: 

* Jakie są typowe przypadki użycia usługi Azure Cosmos DB?
* Jakie są korzyści z używania usługi Azure Cosmos DB dla aplikacji detalicznych?
* Jakie są korzyści z używania usługi Azure Cosmos DB jako magazynu danych dla systemów Internetu rzeczy (IoT)?
* Jakie są korzyści z używania usługi Azure Cosmos DB dla aplikacji sieci web i aplikacji mobilnych?

## <a name="introduction"></a>Wprowadzenie
[Usługa Azure Cosmos DB](../cosmos-db/introduction.md) to globalnie rozproszona usługa bazy danych firmy Microsoft. Usługa została zaprojektowana, aby umożliwić klientom elastyczne (i niezależnie) skalowanie przepływności i magazynu w dowolnej liczbie regionów geograficznych. Usługa Azure Cosmos DB jest pierwszą globalnie rozproszoną usługą baz danych na rynku, która oferuje [kompleksowe umowy dotyczące poziomu usług obejmujące](https://azure.microsoft.com/support/legal/sla/cosmos-db/) przepływność, opóźnienie, dostępność i spójność. 

Usługa Azure Cosmos DB to globalna rozproszona, wielomodelowa baza danych, która jest używana w szerokim zakresie aplikacji i przypadków użycia. Jest to dobry wybór dla każdej aplikacji [bezserwerowej,](https://azure.com/serverless) która potrzebuje czasu odpowiedzi o niskim poziomie kolejności milisekund i musi być skalowana szybko i globalnie. Obsługuje wiele modeli danych (klucz wartość, dokumenty, wykresy i kolumnowe) i wiele interfejsów API dostępu do danych, w tym [interfejsu API usługi Azure Cosmos DB dla MongoDB,](mongodb-introduction.md)SQL [API](documentdb-introduction.md), [Gremlin API](graph-introduction.md)i tabel [interfejsu API](table-introduction.md) natywnie i w sposób rozszerzalny. 

Poniżej przedstawiono niektóre atrybuty usługi Azure Cosmos DB, które sprawiają, że doskonale nadaje się do aplikacji o wysokiej wydajności o globalnych ambicjach.

* Usługa Azure Cosmos DB natywnie dzieli dane w celu uzyskania wysokiej dostępności i skalowalności. Usługa Azure Cosmos DB oferuje 99,99% gwarancji dostępności, przepływności, małych opóźnień i spójności na wszystkich kontach jednoregionalnych i wszystkich kontach wieloregionalnych z luźną spójnością i dostępnością odczytu 99,999% na wszystkich kontach bazy danych w wielu regionach.
* Usługa Azure Cosmos DB ma magazyn z kopii SSD z czasem odpowiedzi o małym opóźnieniu.
* Obsługa usługi Azure Cosmos DB dla poziomów spójności, takich jak ostateczny, spójny prefiks, sesja i ograniczona nieaktualność pozwala na pełną elastyczność i niski stosunek kosztów do wydajności. Żadna usługa bazy danych oferuje tyle elastyczności, co usługa Azure Cosmos DB w spójności poziomów. 
* Usługa Azure Cosmos DB ma elastyczny model cenowy przyjazny dla danych, który mierzy magazyn i przepływność niezależnie.
* Model zarezerwowanej przepływności usługi Azure Cosmos DB umożliwia myślenie w kategoriach liczby odczytów/zapisów zamiast cpu/memory/IOPs sprzętu źródłowego.
* Projekt usługi Azure Cosmos DB umożliwia skalowanie do ogromnych woluminów żądań w kolejności bilionów żądań dziennie.

Te atrybuty są korzystne w aplikacjach internetowych, mobilnych, gier i IoT, które wymagają niskiego czasu reakcji i muszą obsługiwać ogromne ilości odczytów i zapisów.

## <a name="iot-and-telematics"></a>IoT i telematyka
Przypadki użycia IoT często współużytkują niektóre wzorce w sposobie pozyskiwania, przetwarzania i przechowywania danych.  Po pierwsze, systemy te muszą połykać wybuchy danych z czujników urządzeń różnych ustawień regionalnych. Następnie te systemy przetwarzają i analizują dane przesyłania strumieniowego w celu uzyskania szczegółowych informacji w czasie rzeczywistym. Dane są następnie archiwizowane do chłodni do analizy partii. Platforma Microsoft Azure oferuje zaawansowane usługi, które można zastosować w przypadkach użycia IoT, w tym usługi Azure Cosmos DB, Usługi Azure Event Hubs, usługi Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight i Power BI. 

![Architektura referencyjna IoT usługi Azure Cosmos DB](./media/use-cases/iot.png)

Seria danych może być pozyskana przez usługi Azure Event Hubs, ponieważ oferuje wysokie pozyskiwania danych przepływności z małym opóźnieniem. Dane pozyskane, które muszą zostać przetworzone w celu uzyskania wglądu w czasie rzeczywistym, mogą być kierowane do usługi Azure Stream Analytics w celu analizy w czasie rzeczywistym. Dane mogą być ładowane do usługi Azure Cosmos DB do doraźnych zapytań. Po załadowaniu danych do usługi Azure Cosmos DB, dane są gotowe do kwerendy. Ponadto nowe dane i zmiany istniejących danych można odczytywać w pliku danych zmian. Źródło danych change jest trwałym dziennikiem, który przechowuje zmiany w kontenerach usługi Cosmos w kolejności sekwencyjnej. Wszystkie dane lub po prostu zmiany danych w usłudze Azure Cosmos DB mogą być używane jako dane referencyjne w ramach analizy w czasie rzeczywistym. Ponadto dane można dodatkowo udoskonalić i przetworzyć, łącząc dane usługi Azure Cosmos DB z usługą HDInsight dla zadań Pig, Hive lub Map/Reduce.  Udoskonalone dane są następnie ładowane z powrotem do usługi Azure Cosmos DB do raportowania.   

Przykładowe rozwiązanie IoT korzystające z usługi Azure Cosmos DB, EventHubs i Storm można znaleźć [w repozytorium hdinsight-storm-examples w usłudze GitHub.](https://github.com/hdinsight/hdinsight-storm-examples/)

Aby uzyskać więcej informacji na temat ofert platformy Azure dla IoT, zobacz [Tworzenie Internetu twoich rzeczy](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Handel detaliczny i marketing
Usługa Azure Cosmos DB jest szeroko używana na własnych platformach e-commerce firmy Microsoft, które uruchamiają Sklep Windows i XBox Live. Jest również używany w branży detalicznej do przechowywania danych katalogu i do pozyskiwania zdarzeń w potokach przetwarzania zamówień.

Scenariusze użycia danych wykazu obejmują przechowywanie i wykonywanie zapytań zestaw atrybutów dla jednostek, takich jak osoby, miejsca i produkty. Niektóre przykłady danych katalogu to konta użytkowników, katalogi produktów, rejestry urządzeń IoT i systemy zestawienia materiałów. Atrybuty dla tych danych mogą się różnić i mogą się zmieniać wraz z czasem, aby dopasować wymagania aplikacji.

Rozważmy przykład katalogu produktów dla dostawcy części samochodowych. Każda część może mieć swoje własne atrybuty oprócz wspólnych atrybutów, które wszystkie części współużytkują. Ponadto atrybuty dla określonej części można zmienić w następnym roku, gdy nowy model jest zwolniony. Usługa Azure Cosmos DB obsługuje elastyczne schematy i dane hierarchiczne, dzięki czemu doskonale nadaje się do przechowywania danych katalogu produktów.

![Architektura referencyjna wykazu sieci sprzedaży usługi Azure Cosmos DB](./media/use-cases/product-catalog.png)

Usługa Azure Cosmos DB jest często używana do pozyskiwania zdarzeń do zasilania architektury oparte na zdarzeniach przy użyciu funkcji [źródła danych zmian.](change-feed.md) Źródło danych o zmianach zapewnia mikrousług niższego rzędu możliwość niezawodnego i przyrostowego odczytu wstawia i aktualizacji (na przykład zdarzeń zamówienia) wprowadzonych do usługi Azure Cosmos DB. Tę funkcję można wykorzystać, aby zapewnić trwały magazyn zdarzeń jako broker komunikatów dla zdarzeń zmieniających stan i przepływu pracy przetwarzania zamówień dysku między wieloma mikrousługami (które można zaimplementować jako [usługi Azure bezserwerowe).](https://azure.com/serverless)

![Architektura referencyjna potoku zamawiania usługi Azure Cosmos DB](./media/use-cases/event-sourcing.png)

Ponadto dane przechowywane w usłudze Azure Cosmos DB można zintegrować z usługą HDInsight do analizy dużych zbiorów danych za pośrednictwem zadań Apache Spark. Aby uzyskać szczegółowe informacje na temat łącznika platformy Spark dla usługi Azure Cosmos DB, zobacz [Uruchamianie zadania platformy Spark z usługą Cosmos DB i HDInsight.](spark-connector.md)

## <a name="gaming"></a>Gry
Warstwa bazy danych jest kluczowym elementem aplikacji do gier. Nowoczesne gry przetwarzają graficzne rozwiązania na klientach mobilnych/konsolowych, ale polegają na chmurze, aby dostarczać spersonalizowane i spersonalizowane treści, takie jak statystyki w grze, integracja z mediami społecznościowymi i wysokie wyniki w rankingach. Gry często wymagają jednomiliturowych opóźnień dla odczytów i zapisów, aby zapewnić angażujące doświadczenie w grze. Baza danych gier musi być szybka i być w stanie obsłużyć ogromne skoki liczby żądań podczas premier nowych gier i aktualizacji funkcji.

Usługa Azure Cosmos DB jest używana przez gry takie jak [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) by Next [Games](https://www.nextgames.com/)i [Halo 5: Guardians.](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) Usługa Azure Cosmos DB zapewnia następujące korzyści dla twórców gier:

* Usługa Azure Cosmos DB umożliwia skalowanie wydajności w górę lub w dół elastycznie. Dzięki temu gry mogą obsługiwać aktualizowanie profilu i statystyk od kilkudziesięciu do milionów jednoczesnych graczy, wykonując pojedyncze wywołanie interfejsu API.
* Usługa Azure Cosmos DB obsługuje odczyty i zapisy w milisekundach, aby uniknąć opóźnień podczas gry.
* Automatyczne indeksowanie usługi Azure Cosmos DB umożliwia filtrowanie wielu różnych właściwości w czasie rzeczywistym, na przykład lokalizowanie graczy według ich wewnętrznych identyfikatorów graczy lub ich gamecenter, Facebook, Identyfikatory Google lub zapytania na podstawie członkostwa gracza w gildii. Jest to możliwe bez tworzenia złożonej infrastruktury indeksowania lub dzielenia na fragmenty.
* Funkcje społecznościowe, w tym wiadomości czatu w grze, członkostwo w gildii graczy, ukończone wyzwania, wysokie wyniki rankingów i wykresy społecznościowe są łatwiejsze do wdrożenia za pomocą elastycznego schematu.
* Usługa Azure Cosmos DB jako zarządzana platforma jako usługa (PaaS) wymagała minimalnej pracy konfiguracji i zarządzania, aby umożliwić szybką iterację i skrócić czas na rynku.

![Architektura referencyjna usługi Azure Cosmos DB w grach](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplikacje internetowe i mobilne
Usługa Azure Cosmos DB jest powszechnie używana w aplikacjach sieci Web i aplikacji mobilnych i doskonale nadaje się do modelowania interakcji społecznościowych, integracji z usługami innych firm oraz do tworzenia bogatych spersonalizowanych środowisk. Cosmos DB SDK mogą być używane budować bogate aplikacje dla systemów iOS i Android przy użyciu popularnej [platformy Xamarin](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplikacje społecznościowe
Typowym przypadkiem użycia usługi Azure Cosmos DB jest przechowywanie i wykonywanie zapytań o zawartość generowaną przez użytkowników (UGC) dla aplikacji sieci web, urządzeń mobilnych i aplikacji mediów społecznościowych. Niektóre przykłady UGC to sesje czatu, tweety, wpisy na blogu, oceny i komentarze. Często UGC w aplikacjach mediów społecznościowych jest mieszanką tekstu w postaci swobodnej, właściwości, tagów i relacji, które nie są ograniczone sztywną strukturą. Zawartość, taka jak czaty, komentarze i posty, może być przechowywana w usłudze Cosmos DB bez konieczności przekształcania lub złożonych obiektów do relacyjnych warstw mapowania.  Właściwości danych można łatwo dodawać lub modyfikować, aby dopasować wymagania, ponieważ deweloperzy iterują kod aplikacji, promując w ten sposób szybki rozwój.  

Aplikacje integrujące się z sieciami społecznościowymi innych firm muszą reagować na zmieniające się schematy z tych sieci. Ponieważ dane są domyślnie indeksowane automatycznie w usłudze Cosmos DB, dane są gotowe do użycia w dowolnym momencie. W związku z tym aplikacje te mają elastyczność, aby pobrać prognozy zgodnie z ich potrzebami.

Wiele aplikacji społecznościowych działa w skali globalnej i może wykazywać nieprzewidywalne wzorce użycia. Elastyczność skalowania magazynu danych jest niezbędna, ponieważ warstwa aplikacji jest skalowana w celu dopasowania do zapotrzebowania na użycie.  Można skalować w poziomie, dodając dodatkowe partycje danych w ramach konta usługi Cosmos DB.  Ponadto można również utworzyć dodatkowe konta usługi Cosmos DB w wielu regionach. Aby zapoznać się z dostępnością regionu usługi Usługi Cosmos DB, zobacz [Regiony platformy Azure](https://azure.microsoft.com/regions/#services).

![Architektura referencyjna aplikacji sieci Web usługi Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalizacja
W dzisiejszych czasach nowoczesne aplikacje mają złożone widoki i doświadczenia. Są to zazwyczaj dynamiczne, zaspokajające preferencje użytkownika lub nastroje i potrzeby brandingowe. W związku z tym aplikacje muszą być w stanie skutecznie pobierać spersonalizowane ustawienia, aby szybko renderować elementy interfejsu użytkownika i środowiska. 

JSON, format obsługiwany przez Cosmos DB, jest skutecznym formatem do reprezentowania danych układu interfejsu użytkownika, ponieważ jest nie tylko lekki, ale także może być łatwo interpretowany przez JavaScript. Usługa Cosmos DB oferuje przestrajalne poziomy spójności, które umożliwiają szybkie odczyty z zapisami o małym opóźnieniu. W związku z tym przechowywanie danych układu interfejsu użytkownika, w tym spersonalizowanych ustawień jako dokumentów JSON w usłudze Cosmos DB jest skutecznym sposobem, aby uzyskać te dane w sieci.

![Architektura referencyjna aplikacji sieci Web usługi Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć korzystanie z usługi Azure Cosmos DB, postępuj zgodnie z naszymi [szybkimi uruchomieniami,](create-sql-api-dotnet.md)które umożliwiają tworzenie konta i rozpoczynanie pracy z usługą Cosmos DB.

* Jeśli chcesz dowiedzieć się więcej o klientach korzystających z usługi Azure Cosmos DB, zobacz stronę [studiów przypadków klientów.](https://azure.microsoft.com/case-studies/?service=cosmos-db)
