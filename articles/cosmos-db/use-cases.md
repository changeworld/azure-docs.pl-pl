---
title: Typowe przypadki użycia i scenariusze dla bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: 'Dowiedz się więcej o górnej pięć zastosowań bazy danych Azure rozwiązania Cosmos: utworzona przez użytkownika zawartość, rejestrowanie zdarzeń, danych katalogu, danych preferencje użytkownika i Internetu rzeczy (IoT).'
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/7/2017
ms.author: sngun
ms.openlocfilehash: ab24dc98ef4c767f04dc083ac6208e2c9d831935
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34616808"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Typowe przypadki użycia bazy danych Azure rozwiązania Cosmos
Ten artykuł zawiera omówienie kilka typowe przypadki użycia dla bazy danych Azure rozwiązania Cosmos.  Zalecenia przedstawione w tym artykule służyć jako punktu wyjścia podczas opracowywania aplikacji DB rozwiązania Cosmos.   

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania: 

* Jakie są typowe przypadki użycia bazy danych rozwiązania Cosmos Azure?
* Jakie są korzyści wynikające ze stosowania bazy danych Azure rozwiązania Cosmos handlowej aplikacji?
* Jakie są korzyści wynikające ze stosowania bazy danych rozwiązania Cosmos Azure jako magazynu danych dla systemów Internetu rzeczy (IoT)?
* Jakie są korzyści wynikające ze stosowania bazy danych Azure rozwiązania Cosmos dla sieci web i aplikacji dla urządzeń przenośnych?

## <a name="introduction"></a>Wprowadzenie
[Azure DB rozwiązania Cosmos](../cosmos-db/introduction.md) jest usługą globalnie rozproszoną bazę danych firmy Microsoft. Usługa umożliwia klientom elastycznie (i niezależnie) Skala przepływność i magazyn na dowolną liczbę regionów geograficznych. Azure DB rozwiązania Cosmos jest pierwszej usługi globalnie rozproszoną bazę danych dostępnych na rynku dzisiaj oferuje kompleksowe [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/cosmos-db/) obejmujące przepływności, opóźnienia, dostępności i spójności. 

Azure DB rozwiązania Cosmos jest globalne rozproszone i wiele modeli bazy danych, która jest używana w wielu aplikacji oraz przypadki użycia. Jest dobrym rozwiązaniem dla każdego [niekorzystającą](http://azure.com/serverless) aplikacji, która wymaga czasy odpowiedzi kolejności z milisekund niski i musi szybko i globalnie. Obsługuje wiele modeli danych (klucz wartość, dokumentów, wykresów i kolumnowy) i uzyskiwać dostęp do wielu interfejsów API dla danych, w tym [API bazy danych MongoDB](mongodb-introduction.md), [interfejsu API SQL](documentdb-introduction.md), [interfejsu API programu Graph (Gremlin)](graph-introduction.md), i [API tabel](table-introduction.md) natywnie i w sposób rozszerzonego. 

Poniżej przedstawiono niektóre atrybuty bazy danych Azure rozwiązania Cosmos, wchodzące nadają się do wysokiej wydajności aplikacji za pomocą ambicji globalnego.

* Azure DB rozwiązania Cosmos natywnie partycje danych wysokiej dostępności i skalowalności. Azure DB rozwiązania Cosmos oferuje 99,99% gwarancje dostępności, przepływności, małych opóźnień i spójności na wszystkich kontach jednym regionie i wszystkich kont w przypadku swobodna spójności i 99,999% odczytu dostępności na wszystkich kontach w przypadku bazy danych.
* Azure DB rozwiązania Cosmos ma magazynu SSD kopii wraz z czasem odpowiedzi kolejności z milisekund małe opóźnienia.
* Azure Obsługa rozwiązania Cosmos w DB poziomy spójności, takie jak prefiks ostateczna, spójne, sesji i nieaktualność umożliwia pełną elastyczność i niski współczynnik wydajności kosztów. Żadna usługa bazy danych oferuje tylu elastyczność jako bazy danych Azure rozwiązania Cosmos w poziomy spójności. 
* Azure DB rozwiązania Cosmos ma elastyczne przyjaznych dla danych modelu cen, który gazomierzy pamięci masowej i przepływność niezależnie.
* Model zarezerwowaną przepływnością Azure DB rozwiązania Cosmos można wziąć pod uwagę pod względem liczby odczyt/zapis, zamiast Procesora/pamięci/IOPs używanego sprzętu.
* Umożliwia projektowania Azure DB rozwiązania Cosmos skalować do żądania duże woluminy kolejności bilionów żądań dziennie.

Te atrybuty są przydatne w sieci web, mobilnych, gier i aplikacji IoT, które wymagają czasy odpowiedzi niski i wymagana jest obsługa olbrzymich ilości odczyty i zapisy.

## <a name="iot-and-telematics"></a>IoT i telematyka
Przypadki użycia IoT często Udostępnij niektóre wzorce w sposób pozyskiwania one, proces i przechowywania danych.  Najpierw trzeba pozyskiwania seria dane z czujników urządzenia różnych ustawień regionalnych tych systemów. Następnie te systemy przetwarzać i analizować dane przesyłane strumieniowo do uzyskania wgląd w czasie rzeczywistym. Następnie archiwizowania danych do magazynu chłodni dla analizach wsadowych. Microsoft Azure oferuje rozbudowane usług, które mogą być stosowane dla IoT zastosowań, łącznie z bazy danych Azure rozwiązania Cosmos, Azure Event Hubs Azure Stream Analytics, Centrum powiadomień Azure, Azure Machine Learning, Azure HDInsight i usługi Power BI. 

![Architektura referencyjna IoT DB rozwiązania Cosmos platformy Azure](./media/use-cases/iot.png)

Seria danych można pozyskanych przez usługi Azure Event Hubs, ponieważ zapewnia ona wysokiej przepływności wprowadzanie danych z niskim opóźnieniem. Pozyskanych danych, który ma zostać przetworzony uzyskać wgląd w czasie rzeczywistym można funneled Azure Stream Analytics analiz w czasie rzeczywistym. Dane mogą zostać załadowane do bazy danych rozwiązania Cosmos Azure do wykonywania zapytań ad hoc. Po załadowaniu danych do bazy danych Azure rozwiązania Cosmos danych jest gotowy do można wykonać zapytania. Ponadto nowe dane i zmian do istniejących danych mogą być odczytywane na zmiany źródła danych. Źródło zmiana jest trwała, Dołącz tylko dziennika, który zapisuje zmiany w kolekcjach DB rozwiązania Cosmos w kolejności sekwencyjnej. Wszystkie dane i tylko zmiany danych w usłudze Azure DB rozwiązania Cosmos może służyć jako odwołanie do danych w ramach analiz w czasie rzeczywistym. Ponadto danych pozwala dodatkowo można wprowadzono ulepszenia i przetwarzane przez nawiązanie połączenia danych DB rozwiązania Cosmos Azure HDInsight dla zadań Pig, Hive i mapy/Zmniejsz.  Precyzyjnych dane są następnie ładowane do bazy danych Azure rozwiązania Cosmos raportowania.   

Przykładowe rozwiązanie IoT przy użyciu bazy danych Azure rozwiązania Cosmos, EventHubs i Storm, zobacz [hdinsight-storm przykłady repozytorium w usłudze GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Aby uzyskać więcej informacji na oferty Azure IoT, zobacz [utworzyć Internetu rzeczy Your](http://www.microsoft.com/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Sieci sprzedaży i marketingu
Azure DB rozwiązania Cosmos jest bardzo często używane na platformach firmy Microsoft handlu elektronicznego, systemem Sklep Windows i usługi XBox Live. Również służy w sprzedaży detalicznej do przechowywania danych katalogu i zdarzenia sourcing w kolejności przetwarzania potoków.

Scenariusze użycia wykazu danych obejmują przechowywania i badania zestaw atrybutów dla jednostek, takich jak osoby, miejsca i produktów. Przykładowe dane wykazu są konta użytkowników, katalogów produktów rejestry urządzenia IoT i rachunku materiałów systemów. Atrybuty dla tych danych może się różnić i można zmieniać do wymagań aplikacji.

Rozważ przykład katalog produktów dla dostawcy samochodowych. Każdej części może mieć własne atrybuty, oprócz typowych atrybutów, które mają wszystkie części. Ponadto atrybuty dla określonej części można zmienić rok po zwolnieniu nowego modelu. Azure DB rozwiązania Cosmos obsługuje elastyczne schematów i danymi hierarchicznymi i dlatego jest dobrze nadają się do przechowywania danych katalogu produktów.

![Architektura referencyjna katalogu Azure DB rozwiązania Cosmos handlowe](./media/use-cases/product-catalog.png)

Azure DB rozwiązania Cosmos jest często używany do zdarzeń sourcing do zasilania architektury zdarzeniami przy użyciu jego [zmienić źródła strumieniowego](change-feed.md) funkcji. Zmiana źródła strumieniowego umożliwia mikrousług podrzędne niezawodnie i stopniowo odczytu INSERT i Update (na przykład kolejność zdarzeń), wprowadzone do bazy danych Azure rozwiązania Cosmos. Tej funkcji można użyć do zapewnienia magazynu trwałego zdarzeń jako brokera komunikatów zdarzeń zmiany stanu i dysku kolejność przetwarzania w przepływie pracy między wiele mikrousług (które można zaimplementować jako [niekorzystającą usługi Azure Functions](http://azure.com/serverless)).

![Azure DB rozwiązania Cosmos porządkowanie architektura referencyjna potoku](./media/use-cases/event-sourcing.png)

Ponadto dane przechowywane w usłudze Azure DB rozwiązania Cosmos można zintegrować z usługą HDInsight do analizy danych big data za pomocą zadania Apache Spark. Aby uzyskać szczegółowe informacje w łączniku Spark dla bazy danych rozwiązania Cosmos platformy Azure, zobacz [uruchamiać zadanie Spark z rozwiązania Cosmos bazę danych i HDInsight](spark-connector.md).

## <a name="gaming"></a>Gry
Warstwa bazy danych jest kluczowym składnikiem aplikacji gier. Nowoczesne gry przetwarzania graficzny na klientach mobile/konsoli, ale polegać na chmurze do dostarczania zawartości dostosowane i spersonalizowaną, takich jak statystyka w grę, integracja mediów społecznościowych i tablice wyników oceny wysokiej. Gry często wymagają pojedynczego milisekundy opóźnienia dla odczytów i zapisów zapewnienie zaangażowana w grze środowisko. Gry bazy danych musi być szybkiego oraz mieć możliwość obsługi ogromną gwałtowny wzrost liczby żądań podczas nowych gier powoduje uruchomienie i funkcji aktualizacji.

Azure DB rozwiązania Cosmos jest używany przez gry, takich jak [przejście martwy: ziemi Man nie](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) przez [dalej gry](http://www.nextgames.com/), i [Halo 5: opiekunów kontroli](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure DB rozwiązania Cosmos zapewnia następujące korzyści deweloperom gier:

* Azure DB rozwiązania Cosmos umożliwia wydajności skalowania w górę lub w dół elastycznie. Dzięki temu gry do obsługi aktualizacji profilu i statystyka z wielu do milionów jednoczesnych gracze przez jednego wywołania interfejsu API.
* Azure DB rozwiązania Cosmos obsługuje milisekund odczytuje i zapisuje pozwala na uniknięcie wszelkich odchyłki podczas gry.
* Azure DB rozwiązania Cosmos automatycznego indeksowania umożliwia filtrowanie dla wielu różnych właściwości w czasie rzeczywistym, na przykład zlokalizuj odtwarzacze ich wewnętrzny Player identyfikatorów lub GameCenter, Facebook, Google identyfikatorów lub kwerendy na podstawie player członkostwa w podręcznik. Jest to możliwe bez tworzenia złożonych indeksowania lub infrastruktury dzielenia na fragmenty.
* Funkcji społecznościowych, w tym wiadomości rozmów w grę, player Podręcznik członkostwa wyzwania ukończone, tablice wyników oceny wysokiej i wykresy społecznościowych są łatwiejsze do wdrożenia ze schematem elastyczne.
* Azure DB rozwiązania Cosmos jako zarządzanych platformy jako — usługa (PaaS) wymagane minimalne Konfiguracja i zarządzanie współpracują, aby umożliwić szybkie iteracji i skrócić czas na rynek.

![Architektura referencyjna gier w usłudze Azure DB rozwiązania Cosmos](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplikacji sieci Web i aplikacje mobilne
Azure DB rozwiązania Cosmos jest najczęściej używany w sieci web i aplikacji dla urządzeń przenośnych i jest odpowiednia dla modelowania społecznościowych interakcje integracji z usługami innych firm, a także do tworzenia zaawansowanych środowisk spersonalizowane. Zestawy SDK DB rozwiązania Cosmos mogą być używane kompilacji sformatowanego z systemem iOS i aplikacji systemu Android przy użyciu popularnych [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplikacje społecznościowych
Przypadek użycia wspólnych dla bazy danych Azure rozwiązania Cosmos jest do przechowywania i badać zawartości użytkownika wygenerowany (UGC) dla sieci web, aplikacji mobilnych i społecznościowych. Przykłady UGC są sesji rozmowy, tweetów wpisach w blogu, oceny i komentarze. Często UGC w aplikacjach mediów społecznościowych jest kombinacją dowolnej postaci tekstu, właściwości, znaczników i relacje, które nie są ograniczone przez sztywne struktury. Zawartości, takich jak rozmowy, komentarze i wpisy mogą być przechowywane w bazie danych rozwiązania Cosmos bez konieczności przekształcenia lub obiekt złożony do mapowania relacyjnego warstw.  Właściwości danych można dodać lub modyfikowane łatwo wymagania jak deweloperzy przejść przez kod aplikacji, w związku z tym wspieranie szybkie opracowywanie.  

Aplikacje, które integrują się z sieciami społecznościowymi innych firm reagować na zmieniające się schematów z tych sieci. Jak danych jest automatycznie indeksowana domyślnie do rozwiązania Cosmos bazy danych, dane są gotowe do można wykonać zapytania w dowolnym momencie. W związku z tym te aplikacje mają możliwość pobrania projekcje zgodnie z potrzebami odpowiednich.

Wiele aplikacji społecznościowych Uruchom w skali globalnej i może zawierać nieprzewidywalnych wzorców. Elastyczność skalowania magazynu danych jest niezbędne, zgodnie z warstwy aplikacji skaluje odpowiadające żądanie użycia.  Możesz skalować w poziomie przez dodanie dodatkowych danych partycji w ramach konta bazy danych rozwiązania Cosmos.  Ponadto możesz również utworzyć dodatkowych kont DB rozwiązania Cosmos w różnych regionach. Dostępność region usługi rozwiązania Cosmos bazy danych, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

![Architektura referencyjna aplikacji sieci web Azure DB rozwiązania Cosmos](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalizacja
Dzisiaj nowoczesne aplikacje są dostarczane z widokami złożonych i środowiska. Są to zazwyczaj dynamiczny, gastronomiczne do preferencji użytkownika lub skojarzone nastroje i znakowanie potrzeb. W związku z tym aplikacje muszą być w stanie pobrać spersonalizowanych ustawień skutecznie do renderowania elementów interfejsu użytkownika i środowiska szybko. 

JSON, format jest obsługiwany przez DB rozwiązania Cosmos jest skuteczne format reprezentuje dane układ interfejsu użytkownika nie jest tylko lekkie, ale również może łatwo odczytać JavaScript. Rozwiązania cosmos DB udostępnia dostosowywalne poziomy spójności umożliwiających szybkie odczyty o małych opóźnieniach zapisów. W związku z tym przechowywania danych układ interfejsu użytkownika w tym ustawienia spersonalizowane jako dokumenty JSON w bazie danych rozwiązania Cosmos jest skuteczne środki do pobrania tych danych przez sieć.

![Architektura referencyjna aplikacji sieci web Azure DB rozwiązania Cosmos](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć pracę z bazy danych rozwiązania Cosmos platformy Azure, wykonaj naszych [Szybki Start](create-sql-api-dotnet.md), który przeprowadzi użytkownika przez proces tworzenia konta i wprowadzenie do korzystania z bazy danych rozwiązania Cosmos. 

Lub, jeśli chcesz dowiedzieć się więcej o klienci korzystający z rozwiązania Cosmos DB, dostępne są następujące scenariusze klienta:

* [Jet.com](https://jet.com). Dynamicznego uzgadniania handlu elektronicznego eyes miejscu top, działa w chmurze firmy Microsoft, wykorzystuje DB rozwiązania Cosmos w skali globalnej.
* [Asos.com](http://www.asos.com/). Asos.com jest British sposób i zaletą sklepu online. Przede wszystkim mające na celu małych osoby dorosłe, Asos sprzedaje ponad 850 marki, a także zasięgu i akcesoria.
* [Toyota](https://www.toyota.com/). Corporation motocykla Toyota jest japońskiego producenta motoryzacyjnych. Toyota wykorzystywana DB rozwiązania Cosmos globalnego aplikacji IoT.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix rozwija single-sign-on rozwiązania przy użyciu usługi Azure Service Fabric i bazy danych Azure rozwiązania Cosmos
* [TEXA](https://customers.microsoft.com/story/texaspa) w TEXA Rewolucyjny IoT rozwiązanie dla właścicieli pojazdów pozwala zaoszczędzić czas, pieniądze, gazu — i prawdopodobnie znajduje się.
* [W Domino Pizza](https://www.dominos.com). Inc. Pizza Domino firmy jest American pizza restauracji łańcucha.
* [Formanty Johnson](http://www.johnsoncontrols.com). Formanty Johnson jest technologii zróżnicowanymi globalnych i wielu przemysłowych wiodące obsługująca wielu klientów w więcej niż 150 krajach.
* [Microsoft Windows, sklep uniwersalnego, Centrum IoT Azure, Xbox Live i innych usług Internet skali](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Jak Microsoft tworzy skalowalna na ogromną skalę usług przy użyciu bazy danych Azure rozwiązania Cosmos.
* [Zespół Microsoft Data i analiza](https://customers.microsoft.com/story/microsoftdataandanalytics). Firmy Microsoft danych i ich analiza zespół osiąga planety skali danych big data kolekcji z bazy danych Azure rozwiązania Cosmos
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha używa bazy danych Azure rozwiązania Cosmos nawiązywania połączenia między Indie klientów i firm.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit przyjmuje lot Azure DB rozwiązania Cosmos.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio przełączników z usług AWS do bazy danych rozwiązania Cosmos Azure o dane społecznościowe na dużą skalę.
* [Następnie gier](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Wiadomości Walking: nr Man w ziemi gier soars # 1 obsługiwanych przez bazy danych Azure rozwiązania Cosmos.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Halo 5 implementowania społecznościowych gry przy użyciu bazy danych Azure rozwiązania Cosmos.
* [Galeria Analytics Cortana](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galeria Analytics Cortana — lokacji społeczności skalowalne, oparta na usłudze Azure DB rozwiązania Cosmos.
* [Błyskawicznie](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Początkowe Integrator zwraca międzynarodowej przedsiębiorstwa globalnego szczegółowe informacje o minut z technologii chmury elastyczne.
* [Republika wiadomości](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Dodawanie analizy do wiadomości, aby podać informacje z celem zaangażowani obywateli. 
* [Międzynarodowe służą](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Spójne koloru na całym świecie głównych marek Przejdź służą. I włącza służą do platformy Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Wiodącymi globalne Telenor używa chmury, aby przenieść o szybkości uruchomienia. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Magazyn w przyszłości działa na wyszukiwania szybkiego i łatwego przepływu danych.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Platforma Azure oprogramowania dzieli bariery między w firmach i klientów
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Inteligentne lodówko weka usprawnia zarządzanie szczepionki, więc większej liczbie użytkowników mogą być chronione przed chorobami
* [Pomarańczowy Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Istnieje więcej do tej aplikacji żywności nie spełnia oka lub ujścia.
* [Rzeczywiste Madrycie](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Rzeczywiste Madrycie wprowadzono stadium bliżej do 450 milionów wentylatory na całym świecie, z Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU sprawia, że samochodu kupowanie fun pomocy z usług Azure
