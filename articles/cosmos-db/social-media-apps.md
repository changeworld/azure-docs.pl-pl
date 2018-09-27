---
title: 'Wzorzec projektowy w usłudze Azure Cosmos DB: aplikacje mediów społecznościowych | Dokumentacja firmy Microsoft'
description: Dowiedz się więcej o wzorcu projektowym w sieciach społecznościowych dzięki wykorzystaniu elastyczność magazynu usługi Azure Cosmos DB i innymi usługami platformy Azure.
keywords: aplikacje mediów społecznościowych
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: maquaran
ms.openlocfilehash: 5c916f847bf5098145c3ed14fad87c7669d916c8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222696"
---
# <a name="going-social-with-azure-cosmos-db"></a>Azure Cosmos DB w zastosowaniach społecznościowych
Mieszkających w wysoce wzajemnie połączonych społeczeństwa oznacza, że w pewnym momencie w życiu staje się częścią **sieci społecznościowej**. Używasz sieci społecznościowych, Utrzymuj znajomych, współpracowników, rodziny lub czasami udostępniać swoją pasję osoby o wspólnych zainteresowaniach.

Jako inżynierów lub deweloperów, możesz zastanawiać się, jak te sieci przechowywania i łączenia danych, lub może nawet mieć zostały nadzorowania do utworzenia lub yourselves architektury nowej sieci społecznościowych na rynek niche określonych. Gdy powstaje znaczące pytanie: jak te dane są przechowywane?

Załóżmy, że tworzysz nowe i shiny sieciach społecznościowych, gdzie użytkownicy może umieszczać artykuły z powiązanych nośniki, takie jak obrazy, wideo lub nawet music. Użytkownicy mogą komentarze dotyczące wpisów i zapewniają punktów dla klasyfikacji. Będzie kanału informacyjnego wpisów, które użytkownicy zobaczą i mieć możliwość interakcji z na stronie docelowej głównej witryny sieci Web. To nie brzmi złożone (na początku), ale dla uproszczenia, Przyjrzyjmy się tam zatrzymać (można delve do źródła danych niestandardowych użytkownika dotyczy relacji, ale przekracza on celem tego artykułu).

Tak jak można to przechowywać i gdzie?

Wiele osób może masz doświadczenie w bazach danych SQL lub co najmniej ma pojęcie [relacyjnych, modelowanie danych](https://en.wikipedia.org/wiki/Relational_model) i może być kuszące rozpocznie się rysowanie podobny do poniższego:

![Diagram pokazujący względne model relacyjny](./media/social-media-apps/social-media-apps-sql.png) 

Struktura danych doskonale znormalizowane i łatwa... który skalowania. 

Nie uzyskasz mnie niewłaściwego Prócz tego pracowałam z bazami danych SQL swojego życia, są one wspaniałe, ale jak firmach wzorzec, praktyki i oprogramowanie nie jest idealny dla każdego scenariusza.

Dlaczego SQL w tym scenariuszu najlepszy wybór? Przyjrzyjmy się struktury pojedynczy wpis, jeśli chciałem pokazać tego wpisu w witrynie sieci Web lub aplikacji, będzie trzeba to zrobić zapytanie o... Aby wyświetlić jeden pojedynczy wpis, teraz obraz strumień wpisy, które dynamicznie obciążenia i są wyświetlane na ekranie i można napotkać, gdzie użyję po prostu osiem sprzężeń tabel (!).

Można oczywiście Użyj wystąpienia SQL, inwestują z wystarczająco dużo mocy rozwiązania tysiące zapytań za pomocą tych wielu sprzężeń, aby obsługiwać zawartość, ale naprawdę, dlaczego chcesz, gdy istnieje prostszy rozwiązania?

## <a name="the-nosql-road"></a>Drogowa NoSQL
Ten artykuł przeprowadzi Cię do modelowania danych platform społecznościowych dzięki bazie danych NoSQL Azure [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) w ekonomiczny sposób, korzystając z innych usługi Azure Cosmos DB funkcje, takie jak [interfejs API Gremlin](../cosmos-db/graph-introduction.md). Za pomocą [NoSQL](https://en.wikipedia.org/wiki/NoSQL) podejście, przechowywania danych w formacie JSON i stosowanie [denormalizacja](https://en.wikipedia.org/wiki/Denormalization), wcześniej skomplikowane wpis mogą zostać przekształcone w jednym [dokumentu](https://en.wikipedia.org/wiki/Document-oriented_database):


    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

I można uzyskać za pomocą jednego zapytania, a także nie połączeń. Jest to znacznie bardziej niezwykle proste, a budget-wise, wymaga mniej zasobów, aby osiągnąć lepszą jakość.

Usługa Azure Cosmos DB upewnia się, że wszystkie właściwości są indeksowane za pomocą jego automatycznego indeksowania, co jeszcze może być [dostosowane](indexing-policies.md). Podejście schematów umożliwiają przechowywanie dokumentów za pomocą różnych i dynamiczne struktur, może być jutro mają wpisy, aby wyświetlić listę kategorii lub hasztagi skojarzonych z nimi, Cosmos DB będzie obsługiwać nowe dokumenty za pomocą dodane atrybuty z żadnego dodatkowego nakładu pracy wymagane przez firmę Microsoft.

Komentarze na wpis może być traktowana jako inne wpisy z właściwością nadrzędnego (upraszcza mapowanie obiektu). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

I wszystkie interakcje społecznościowych mogą być przechowywane na oddzielnym obiektem jako liczniki:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Tworzenie źródła danych jest to kwestia tworzenie dokumentów, które mogą zawierać listy identyfikatorów wpis z kolejnością znaczenie dla danego:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Strumień "najnowsza" może mieć z wpisami, uporządkowane według daty utworzenia, "najbardziej aktywnych" przesyłanie strumieniowe przy użyciu tych wpisów z bardziej polubień w ostatnich 24 godzinach, można nawet zaimplementować niestandardowy strumienia dla każdego użytkownika, w oparciu o logikę, takich jak obserwatorów i zainteresowania i nadal będzie listy  wysyła żądanie POST. Jest kwestią dotyczącą tworzenia tych list, ale wydajność odczytu pozostaje swobodnego. Po nabyciu jedną z tych list wydać jedno zapytanie do usługi Cosmos DB przy użyciu [w operatorze](sql-api-sql-query.md#WhereClause) uzyskać stron wpisów w danym momencie.

Strumienie źródła danych mogą być zbudowane przy użyciu [usług aplikacji platformy Azure](https://azure.microsoft.com/services/app-service/) procesów w tle: [Webjobs](../app-service/web-sites-create-web-jobs.md). Po utworzeniu wpisu przetwarzania w tle mogą być wyzwalane za pomocą [usługi Azure Storage](https://azure.microsoft.com/services/storage/) [kolejek](../storage/queues/storage-dotnet-how-to-use-queues.md) i wyzwalane za pomocą zadań Webjob [zestawu Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), wdrożenia Opublikuj propagacji wewnątrz strumieni w oparciu o logikę niestandardowego. 

Punkty i polubień za pośrednictwem wpis mogą być przetwarzane w sposób odroczone tworzenie ostatecznie spójne środowisko przy użyciu tej samej techniki.

Trudniejszy są obserwatorów. Usługa cosmos DB ma ograniczenie maksymalnej dokumentu i odczytu/zapisu dużych dokumentów może mieć wpływ na skalowalność aplikacji. Tak sądzisz o przechowywaniu obserwatorów jako dokument z tą strukturą:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

To może działać dla użytkownika z tysiącami kilku obserwatorów, ale jeśli niektóre osobistości sprzężenia rangę spowoduje podejście prowadzić do rozmiaru dużych dokumentów i ostatecznie może napotkać limit rozmiaru dokumentu.

Aby rozwiązać ten problem, można użyć mieszanych podejście. Jako część dokumentu statystyki użytkownika może przechowywać liczba obserwatorów:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

A rzeczywista wykres obserwatorów mogą być przechowywane za pomocą usługi Azure Cosmos DB [interfejs API Gremlin](../cosmos-db/graph-introduction.md), aby utworzyć [wierzchołków](http://mathworld.wolfram.com/GraphVertex.html) dla każdego użytkownika i [krawędzie](http://mathworld.wolfram.com/GraphEdge.html) , obsługa "element-obserwowanych B" Relacje. Interfejs API Gremlin teraz można nie uzyskać obserwatorów określonego użytkownika tylko tworzyć bardziej złożone zapytania do nawet zasugerować osoby wspólnie. Jeśli dodasz do wykresu, takie jak kategorie zawartości tej osoby lub korzystaj z można uruchomić tkania procesy, które obejmują inteligentne odnajdywania zawartości, sugerowanie zawartości tego te, które należy wykonać, takie jak lub znajdowania osoby, z którymi może być wiele wspólnych.

Dokument statystyki użytkownika nadal może służyć do tworzenia kart interfejsu użytkownika lub wersji zapoznawczych szybkiego profilu.

## <a name="the-ladder-pattern-and-data-duplication"></a>Duplikowanie wzorca i dane "Drabinę"
Jak można zauważyć w dokumencie JSON, który odwołuje się do wpisu, istnieje wiele wystąpień użytkownika. A użytkownik będzie mieć złamać po prawej stronie to oznacza, że informacje, które reprezentuje użytkownika, biorąc pod uwagę ten denormalizacja może być obecny w więcej niż jednego miejsca.

Aby umożliwić szybszego wykonywania zapytań, wiąże się z duplikacji danych. Problem z efektem ubocznym to że jeśli przez niektóre działania, zmiany danych użytkownika, należy znaleźć wszystkie działania nigdy nie zostało i zaktualizować je wszystkie. Nie brzmi, praktyczne, bezpośrednio?

Zamierzasz rozwiązać problem, określając klucz atrybuty użytkownika, który możesz wyświetlać w aplikacji dla każdego działania. Jeśli wizualnie pokazują wpis w aplikacji i Pokaż tylko twórcy nazwy i obraz, dlaczego są przechowywane wszystkie dane użytkownika w atrybucie "createdBy"? Jeśli dla każdego komentarza tylko wyświetlić obrazu użytkownika, nie potrzebujesz pozostałej części jego informacje. To, skąd pochodzą coś czy mogę wywołać "wzorzec drabinę" do gry.

Spójrzmy na przykład informacje o użytkowniku:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Analizując te informacje, można szybko wykryć czyli kluczowych informacji i który nie jest co powoduje utworzenie "Drabinę":

![Diagram przedstawiający wzorzec drabiny](./media/social-media-apps/social-media-apps-ladder.png)

Najmniejsza krok jest nazywany UserChunk minimalny element informacje umożliwiające identyfikację użytkownika, i jest używany do duplikacji danych. Zmniejszenie rozmiaru zduplikowane dane można tylko te informacje, które można będzie "Pokaż", zmniejszyć możliwość dużych aktualizacji.

Środkowy krok jest nazywany użytkownik, jest pełnych danych, które będą używane w większości zapytań zależnych od wydajności na usługi Cosmos DB, uzyskuje się dostęp i krytyczne. Zawiera informacje, reprezentowane przez UserChunk.

Największy jest rozszerzony użytkownika. Zawiera wszystkie informacje o użytkowniku krytycznych oraz inne dane, które nie naprawdę potrzebne do szybkiego odczytu lub jego użycie jest ostateczną (np. proces logowania). Te dane mogą być przechowywane poza usługi Cosmos DB w usłudze Azure SQL Database lub tabele magazynu platformy Azure.

Dlaczego czy dzielenie użytkownika i nawet przechowywać tych informacji w różnych miejscach? Ponieważ z punktu widzenia wydajności, tym większe dokumenty, costlier zapytania. Przechowuje dokumenty kieszeń, za pomocą informacji potrzebnych do Twojej zapytań zależnych od wydajności sieci społecznościowych i przechowywania dodatkowych informacji do ostatecznej scenariuszy, takich jak zmiany pełny profil, logowania, nawet wyszukiwania danych do analizy użycia i danych Big Data inicjatyw. Tak naprawdę nie dba Jeśli zbierania danych do wyszukiwania danych jest mniejsza, ponieważ jest ona uruchomiona w usłudze Azure SQL Database, możesz mieć dotyczą jednak czy użytkownicy mają szybkiego i obsługiwane środowiska. Użytkownik w udziale usługi Cosmos DB będzie wyglądać następująco:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

I wpis powinien wyglądać tak:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

A podczas edycji powstaje, gdy jeden z atrybutów fragmentów naruszona, jest łatwe do znalezienia dotyczy dokumentów przy użyciu zapytań, które wskazują atrybuty indeksowane (Wybierz * FROM publikuje p p.createdBy.id gdzie == "edited_user_id"), a następnie zaktualizowanie fragmenty.

## <a name="the-search-box"></a>Pole wyszukiwania
Użytkownicy będą generować na szczęście dużo zawartości. I powinien mieć możliwość zapewnienia możliwość wyszukiwania i znajdowania zawartości, która może być bezpośrednio w swoich strumieni zawartości może być, ponieważ użytkownik nie są zgodne z twórcami lub może po prostu chcesz dowiedzieć się, że stary wpis nie sześć miesięcy temu.

Szczęście a ponieważ korzystają z usługi Azure Cosmos DB można łatwo zaimplementować aparatu wyszukiwania przy użyciu [usługi Azure Search](https://azure.microsoft.com/services/search/) w kilka minut i bez wpisywania jednego wiersza kodu (inne niż oczywiście, proces wyszukiwania i interfejsu użytkownika).

Dlaczego jest to tak proste?

Usługa Azure Search implementuje, zwany one [indeksatory](https://msdn.microsoft.com/library/azure/dn946891.aspx)tła przetwarza ten punkt zaczepienia w danych repozytoriów i automagically Dodawanie, aktualizowanie i usuwanie obiektów w indeksach. Obsługują one [indeksatorów usługi Azure SQL Database](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indeksatory obiektów blob platformy Azure](../search/search-howto-indexing-azure-blob-storage.md) i szczęście [indeksatorów usługi Azure Cosmos DB](../search/search-howto-index-documentdb.md). Przejście informacji z usługi Cosmos DB do usługi Azure Search jest proste, jak zarówno przechowuje informacje w formacie JSON, wystarczy [utworzyć indeks](../search/search-create-index-portal.md) mapy atrybuty ze swoich dokumentów, które mają być indeksowane i to wszystko w ciągu kilku minut (zależy od rozmiaru danych), wszystkie Twoje zawartość będzie dostępna do wyszukania na żądanie, najlepsze rozwiązania wyszukiwania jako usługi w kategoriach infrastruktura chmury. 

Aby uzyskać więcej informacji na temat usługi Azure Search, możesz odwiedzić stronę [firmy Hitchhiker przewodnik wyszukiwania](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Podstawowej wiedzy
Po zapisaniu tej zawartości, który wzrostu i rozwoju każdego dnia, może się okazać myśleć: co można zrobić za pomocą tego strumienia danych z moich użytkowników?

Odpowiedź na pytanie jest prosta: umieść je do pracy i wyciągnij z niego.

Jednak można omawiane? Kilka proste przykłady [analizę tonacji](https://en.wikipedia.org/wiki/Sentiment_analysis)zawartości, zalecenia na podstawie preferencji użytkownika lub nawet automatycznych pakietu content moderator, zapewnia bezpieczne dla rodziny całej zawartości opublikowane przez sieci społecznościowe.

Teraz, gdy mam możesz podłączone prawdopodobnie będzie traktować należy niektóre tytuł doktora matematyczne analizy, aby wyodrębnić te wzorce i informacji poza proste baz danych i plików, ale może być nieprawidłowy.

[Usługa Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)stanowiący część [pakietu Cortana Intelligence](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), to usługa w pełni zarządzana usługa w chmurze, która umożliwia tworzenie przepływów pracy za pomocą prostego interfejsu przeciągania i upuszczania za pomocą algorytmów, kod algorytmów w [ R](https://en.wikipedia.org/wiki/R_\(programming_language\)) lub korzystać z niektórych już utworzone i gotowe do użycia interfejsów API, takich jak: [analizy tekstu](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator lub [zalecenia](https://gallery.azure.ai/Solution/Recommendations-Solution).

Aby osiągnąć dowolnego z tych scenariuszy usługi Machine Learning, można użyć [usługi Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) pozyskiwać dane z różnych źródeł i użyj [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) do przetwarzania informacji i generuje danych wyjściowych, który może przetworzone przez usługi Azure Machine Learning.

Innym dostępną opcją jest użycie [usług Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) do analizowania zawartości; nie tylko można możesz im lepiej zrozumieć użytkowników (poprzez analizowanie zapisują z [interfejsu API analizy tekstu](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale można również wykrywanie niepożądanych lub dojrzała zawartości i podejmowanie odpowiednich działań dzięki [interfejs API przetwarzania obrazów](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Usługi cognitive Services zawiera wiele rozwiązań poza pole, które nie wymagają dowolnego rodzaju wiedzy uczenia maszynowego do użycia.

## <a name="a-planet-scale-social-experience"></a>W środowisku społecznościowych w skali globalnej
Jest ostatni, ale nie najmniej artykułu ważne I muszą spełnić: **skalowalność**. Podczas projektowania architektury, który ma zasadnicze znaczenie, czy poszczególne składniki można skalować samodzielnie, albo ponieważ trzeba przetworzyć większej ilości danych lub ponieważ większego zasięgu geograficznego (lub obu!). Szczęście osiągnięcie złożonym zadaniem jest **gotową** za pomocą usługi Cosmos DB.

Usługa cosmos DB obsługuje [dynamiczne partycjonowanie](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) out-of--box przez automatyczne tworzenie partycji na podstawie danego **klucza partycji** (zdefiniowany jako jeden z atrybutów w dokumentach). Definiowanie odpowiedniego klucza partycji muszą być wykonane w czasie projektowania i mając na względzie [najlepsze praktyki](../cosmos-db/partition-data.md#designing-for-partitioning) dostępne; społecznościowe środowisko, w przypadku strategii partycjonowania muszą być dopasowane do sposobu zapytania (odczyty w tej samej partycja jest pożądane) i zapis (uniknąć "aktywne" Dzięki rozdzieleniu zapisy na wielu partycjach). Niektóre opcje są: partycji na podstawie danych czasowych klucza (dzień/miesiąc/tydzień), zawartości kategorii, regionu geograficznego, przez użytkownika. to wszystko zależy sposób będzie wykonywania zapytań o dane i wyświetl ją w swoje środowisko społecznościowe. 

Jedną z interesujące punktu warto zauważyć, jest, że usługi Cosmos DB będzie uruchamiać zapytania (w tym [agregacje](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) we wszystkich partycji, nie trzeba dodawać wszelka logika wzroście rozmiaru danych.

Z upływem czasu możesz ostatecznie rośnie w ruchu sieciowego i użycia zasobów (mierzoną w [RUs](request-units.md), lub jednostek żądań) więc ceny wzrosną. Będzie odczytywanych i zapisywanych na częściej bazy użytkowników wraz ze wzrostem i rozpocznie tworzenie i odczytywanie większej ilości zawartości; zdolność **skalowanie przepływności** jest istotne. Zwiększania RUs usługi jest łatwe, możesz to zrobić za pomocą kilku kliknięć w witrynie Azure portal lub przez [wystawianie poleceń przez interfejs API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Skalowanie w górę i zdefiniowanie klucza partycji](./media/social-media-apps/social-media-apps-scaling.png)

Co się stanie, jeśli elementy stają się coraz lepsze i użytkowników z innego regionu kraju lub kontynent, zwróć uwagę, platformy i rozpocząć korzystanie z niej Zaskoczenie doskonały!

Ale Zaczekaj... wkrótce okazuje się ich doświadczeń z platformą nie są optymalne; są one do tej pory od Twojego regionu operacyjnej, opóźnienie jest panowanie i oczywiście nie chcesz je zamknąć. Jeśli tylko było to łatwy sposób **rozszerzanie zasięgu globalnego**..., ale istnieje!

Usługa cosmos DB umożliwia [Replikuj dane globalnie](../cosmos-db/tutorial-global-distribution-sql-api.md) i sposób niewidoczny dla użytkownika przy użyciu kilku kliknięć i automatycznie wybierz spośród dostępnych regionów z Twojej [kod klienta](../cosmos-db/tutorial-global-distribution-sql-api.md). Oznacza to również, że masz [wielu regionach w tryb failover](regional-failover.md). 

Podczas Replikuj dane globalnie, należy się upewnić, że klienci mogą z niej korzystać. Jeśli używasz frontonu sieci web lub dostęp do interfejsów API z klientów mobilnych, można wdrożyć [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) i klonowanie usługi Azure App Service na wszystkie żądane regionów, przy użyciu konfiguracji wydajności do obsługi usługi rozszerzonej globalne pokrycie. Gdy klientom dostęp do interfejsów API lub frontonu, będzie można kierować do najbliższego usługi App Service, co z kolei powoduje nawiązanie lokalna replika usługi Cosmos DB.

![Dodawanie zasięg globalny platformę społecznościowych](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Podsumowanie
W tym artykule podejmie próbę zmniejszenia światło do rozwiązań alternatywnych, tworzenie społecznościowych całkowicie na platformie Azure za pomocą niedrogiej usługi i zapewniając doskonałe wyniki przez zachęcanie do wykorzystania z wielowarstwowych magazynu danych i rozwiązań dystrybucji o nazwie "Drabinę".

![Diagram interakcji między usługami systemu Azure dla sieci społecznościowej](./media/social-media-apps/social-media-apps-azure-solution.png)

Uspójniaj jest czy nie ma żadnych silver punktora dla tego rodzaju scenariuszy, jest synergii utworzone przez kombinację wspaniałych usług, które pozwalają na tworzenie wspaniałych środowisk: szybkość i swobody usługi Azure Cosmos DB zapewnia doskonałą aplikację społecznościowych, Analiza za rozwiązania najwyższej jakości wyszukiwania, takiego jak usługi Azure Search elastyczność usługi Azure App Services do hostowania aplikacji nie nawet niezależny od języka, ale procesów w tle zaawansowane i rozwijania usługi Azure Storage i Azure SQL Database do przechowywania duże ilości danych i analizy możliwości usługi Azure Machine Learning do utworzenia wiedzy i analizy, które można przekazać opinię do procesów i Pomóż nam dostarczać odpowiednią zawartość do odpowiednich użytkowników.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o przypadkach użycia usługi Cosmos DB, zobacz [przypadków użycia wspólnego usługi Cosmos DB](use-cases.md).
