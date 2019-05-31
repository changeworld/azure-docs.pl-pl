---
title: 'Wzorzec projektowy w usłudze Azure Cosmos DB: Aplikacje mediów społecznościowych'
description: Dowiedz się więcej o wzorcu projektowym w sieciach społecznościowych dzięki wykorzystaniu elastyczność magazynu usługi Azure Cosmos DB i innymi usługami platformy Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8206bff765b59ddc5d6be2388145bf51e1000241
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256876"
---
# <a name="going-social-with-azure-cosmos-db"></a>Azure Cosmos DB w zastosowaniach społecznościowych

Mieszkających w wysoce wzajemnie połączonych społeczeństwa oznacza, że w pewnym momencie w życiu staje się częścią **sieci społecznościowej**. Używasz sieci społecznościowych, Utrzymuj znajomych, współpracowników, rodziny lub czasami udostępniać swoją pasję osoby o wspólnych zainteresowaniach.

Jako inżynierów lub deweloperów możesz zastanawiać się, jak te sieci przechowywania i łączenia danych. Lub możesz może mieć nawet zostały nadzorowania do tworzenia lub projektowania nowej sieci społecznościowych na rynek niche określonych. Gdy pojawia się znaczne zapytania: Jak są przechowywane wszystkie te dane?

Załóżmy, że tworzysz nowe i shiny sieci społecznościowych których Twoje użytkownicy będą publikować artykuły z powiązanych nośników, takich jak obrazy, wideo lub nawet music. Użytkownicy mogą komentarze dotyczące wpisów i zapewniają punktów dla klasyfikacji. Będzie kanału informacyjnego wpisów, które użytkownicy będą widzieć i oddziałują na stronie docelowej głównej witryny sieci Web. Ta metoda nie brzmi złożone w najpierw, ale dla uproszczenia, Przyjrzyjmy się tam zatrzymać. (Można delve do źródła danych niestandardowych użytkownika dotyczy relacji, ale wykracza poza Celem tego artykułu).

Tak jak możesz przechowywać dane i gdzie?

Być może masz doświadczenie w bazach danych SQL lub masz pojęcie [relacyjnych, modelowanie danych](https://en.wikipedia.org/wiki/Relational_model). Można rozpocząć rysowania coś w następujący sposób:

![Diagram pokazujący względne model relacyjny](./media/social-media-apps/social-media-apps-sql.png)

Doskonale znormalizowane i łatwa struktura danych..., skalowania.

Nie uzyskasz mnie niewłaściwego Prócz tego pracowałam z bazami danych SQL swojego życia. Są one wspaniałe, ale jak firmach wzorzec, praktyki i oprogramowanie nie jest idealny dla każdego scenariusza.

Dlaczego SQL w tym scenariuszu najlepszy wybór? Przyjrzyjmy się struktura pojedynczego wpisu. Chciałem pokazać wpis w witrynie sieci Web lub aplikacji, czy muszę wykonaj zapytanie o..., dołączając do ośmiu tables(!) tak, aby wyświetlić jeden pojedynczy wpis. Teraz obraz strumień wpisy, które dynamicznie obciążenia i są wyświetlane na ekranie i może zostać wyświetlony, gdy kliknę teraz.

Można użyć inwestują wystąpienia SQL z wystarczająco dużo mocy rozwiązania tysiące zapytań w wielu połączeń do obsługi zawartości. Ale Dlaczego chcesz, gdy istnieje prostszy rozwiązania?

## <a name="the-nosql-road"></a>Drogowa NoSQL

Ten artykuł przeprowadzi Cię do modelowania danych platform społecznościowych dzięki bazie danych NoSQL Azure [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ekonomiczne. On również informacje o sposobie korzystanie z innych funkcji usługi Azure Cosmos DB, takie jak [interfejs API Gremlin](../cosmos-db/graph-introduction.md). Za pomocą [NoSQL](https://en.wikipedia.org/wiki/NoSQL) podejście, przechowywania danych w formacie JSON i stosowanie [denormalizacja](https://en.wikipedia.org/wiki/Denormalization), wcześniej skomplikowane wpis mogą zostać przekształcone w jednym [dokumentu](https://en.wikipedia.org/wiki/Document-oriented_database):

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["https://myfirstimage.png","https://mysecondimage.png"],
        "videos":[
            {"url":"https://myfirstvideo.mp4", "title":"The first video"},
            {"url":"https://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

I mogą być uzyskane za pomocą jednego zapytania, a także nie połączeń. To zapytanie jest znacznie niezwykle proste, a budget-wise, wymaga mniej zasobów, aby osiągnąć lepszą jakość.

Usługa Azure Cosmos DB gwarantuje, że wszystkie właściwości są indeksowane, za pomocą automatycznego indeksowania. Automatyczne indeksowanie można nawet [dostosowane](index-policy.md). Podejście schematów umożliwiają przechowywanie dokumentów ze strukturami różnych i dynamicznych. Może być jutro mają wpisy, aby wyświetlić listę kategorii lub hasztagi skojarzonych z nimi? Usługa cosmos DB będzie obsługiwać nowe dokumenty za pomocą dodane atrybuty bez dodatkowej pracy wymagane przez firmę Microsoft.

Komentarze na wpis może być traktowana jako inne wpisy z właściwością nadrzędnej. (To rozwiązanie upraszcza mapowanie obiektu).

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

Tworzenie źródła danych jest to kwestia tworzenie dokumentów, które mogą zawierać listę wpis identyfikatory kolejności znaczenie dla danego:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Strumień "najnowsza" może mieć z wpisami, uporządkowane według daty utworzenia. Lub masz "najbardziej aktywnych" przesyłanie strumieniowe przy użyciu tych wpisów z polubienia więcej w ciągu ostatnich 24 godzin. Można nawet implementować niestandardowe strumienia dla każdego użytkownika, w oparciu o logikę, takich jak obserwatorów i zainteresowania. Nadal będzie lista wpisów. Jest kwestią dotyczącą tworzenia tych list, ale wydajność odczytu pozostaje swobodnego. Po nabyciu jedną z tych list wydać jedno zapytanie do usługi Cosmos DB przy użyciu [w operatorze](how-to-sql-query.md#WhereClause) można pobrać strony wpisów w danym momencie.

Strumienie źródła danych mogą być zbudowane przy użyciu [usług aplikacji platformy Azure](https://azure.microsoft.com/services/app-service/) procesów w tle: [Zadania Webjob](../app-service/webjobs-create.md). Po utworzeniu wpisu przetwarzania w tle mogą być wyzwalane za pomocą [usługi Azure Storage](https://azure.microsoft.com/services/storage/) [kolejek](../storage/queues/storage-dotnet-how-to-use-queues.md) i wyzwalane za pomocą zadań Webjob [zestawu Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), wdrożenia Opublikuj propagacji wewnątrz strumieni w oparciu o logikę niestandardowego.

Punkty i polubień za pośrednictwem wpis mogą być przetwarzane w sposób odroczone tworzenie ostatecznie spójne środowisko przy użyciu tej samej techniki.

Trudniejszy są obserwatorów. Usługa cosmos DB ma limit rozmiaru dokumentu, a odczytu/zapisu dużych dokumentów może mieć wpływ na skalowalność aplikacji. Tak sądzisz o przechowywaniu obserwatorów jako dokument z tą strukturą:

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

Ta struktura może działać dla użytkownika z tysiącami kilku obserwatorów. Jeśli niektóre osobistości sprzężenia rangę, jednak takie podejście prowadzi do rozmiaru dużych dokumentów i go po pewnym czasie może napotkać limit rozmiaru dokumentu.

Aby rozwiązać ten problem, można użyć mieszanych podejście. Jako część dokumentu statystyki użytkownika może przechowywać liczba obserwatorów:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Rzeczywiste wykres obserwatorów przy użyciu usługi Azure Cosmos DB można przechowywać [interfejs API Gremlin](../cosmos-db/graph-introduction.md) utworzyć [wierzchołków](http://mathworld.wolfram.com/GraphVertex.html) dla każdego użytkownika i [krawędzie](http://mathworld.wolfram.com/GraphEdge.html) , obsługa "element-obserwowanych B" Relacje. Za pomocą interfejsu API języka Gremlin możesz pobrać obserwatorów określonego użytkownika i tworzyć bardziej złożone zapytania proponować osoby wspólnie. Jeśli dodasz do wykresu, takie jak kategorie zawartości tej osoby lub korzystaj z uruchomieniem tkania procesy, które obejmują inteligentne odnajdywania zawartości, co sugeruje, że zawartość, te osoby, należy wykonać takie jak lub wyszukiwanie osób, które może mieć wiele wspólnego z.

Dokument statystyki użytkownika nadal może służyć do tworzenia kart interfejsu użytkownika lub wersji zapoznawczych szybkiego profilu.

## <a name="the-ladder-pattern-and-data-duplication"></a>Duplikowanie wzorca i dane "Drabinę"

Jak można zauważyć w dokumencie JSON, który odwołuje się do wpisu, istnieje wiele wystąpień tego użytkownika. A użytkownik będzie mieć złamać po prawej stronie tych duplikaty oznacza informacje opisujące podana przez użytkownika, to denormalizacja może istnieć w więcej niż jednym miejscu.

Aby umożliwić szybszego wykonywania zapytań, wiąże się z duplikacji danych. Problem z tym efektem jest, jeśli przez niektóre działania, zmiany danych użytkownika, musisz znaleźć wszystkich działań użytkownika nigdy nie zostało i zaktualizuj je wszystkie. Nie brzmi, praktyczne, bezpośrednio?

Zamierzasz rozwiązać problem, określając kluczowych atrybutów użytkownika, który możesz wyświetlać w aplikacji dla każdego działania. Jeśli wizualnie pokazują wpis w aplikacji i Pokaż tylko twórcy nazwy i obraz, dlaczego są przechowywane wszystkie dane użytkownika w atrybucie "createdBy"? Jeśli dla każdego komentarza możesz tylko wyświetlać obrazu użytkownika, nie potrzebujesz pozostałe informacje o użytkowniku. To, gdzie staje się coś czy mogę wywołać "wzorzec drabinę" zaangażowane.

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

Najmniejsza krok jest nazywany UserChunk minimalny element informacje umożliwiające identyfikację użytkownika, i jest używany do duplikacji danych. Dzięki zmniejszeniu rozmiaru zduplikowane dane można tylko te informacje, które są "wyświetlisz", można zmniejszyć możliwość dużych aktualizacji.

Krok środkowej nosi nazwę użytkownika. Jest pełna dane, które będą używane w większości zapytań zależnych od wydajności na usługi Cosmos DB, uzyskuje się dostęp i krytyczne. Zawiera informacje, reprezentowane przez UserChunk.

Największy jest rozszerzony użytkownika. Zawiera informacje o użytkowniku krytyczne i inne dane, które nie muszą być odczytywane szybko lub ma użycie ostatecznej, takich jak procesu logowania. Te dane mogą być przechowywane poza usługi Cosmos DB w usłudze Azure SQL Database lub tabele magazynu platformy Azure.

Dlaczego czy dzielenie użytkownika i nawet przechowywać tych informacji w różnych miejscach? Ponieważ z punktu widzenia wydajności, tym większe dokumenty, costlier zapytania. Zachowaj dokumentów kieszeń, za pomocą odpowiednie informacje do swojej zapytań zależnych od wydajności sieci społecznościowych. Store dodatkowe informacje dla ostatecznej scenariuszy, takich jak pełny profil edycje, logowania i analizą danych, analizy użycia i inicjatyw danych Big Data. Gdy zależy nam, nie w przypadku zbierania danych do wyszukiwania danych wolniej, ponieważ jest ona uruchomiona w usłudze Azure SQL Database. Możesz mieć dotyczą jednak czy użytkownicy mają szybkiego i obsługiwane środowiska. Użytkownik usługi Cosmos DB w udziale powinien wyglądać podobnie do tego kodu:

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

Edytuj podatkowego, których dotyczy atrybut fragmentów, można łatwo wyszukiwać dokumentów, których to dotyczy. Wystarczy użyć zapytania, które wskazują indeksowanych atrybutów, takich jak `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`, a następnie zaktualizuj fragmenty.

## <a name="the-search-box"></a>Pole wyszukiwania

Użytkownicy będą generować na szczęście dużo zawartości. I powinien mieć możliwość zapewnienia możliwość wyszukiwania i znajdowania zawartości, która może być bezpośrednio w swoich strumieni zawartości może być, ponieważ użytkownik nie są zgodne z twórcami lub może po prostu próbujesz znaleźć, że stary wpis nie sześć miesięcy temu.

Ponieważ używasz usługi Azure Cosmos DB można łatwo zaimplementować aparatu wyszukiwania przy użyciu [usługi Azure Search](https://azure.microsoft.com/services/search/) w ciągu kilku minut bez pisania żadnego kodu, inne niż proces wyszukiwania i interfejsu użytkownika.

Ten proces jest tak proste

Usługa Azure Search implementuje, zwany one [indeksatory](https://msdn.microsoft.com/library/azure/dn946891.aspx)tła przetwarza ten punkt zaczepienia w danych repozytoriów i automagically Dodawanie, aktualizowanie i usuwanie obiektów w indeksach. Obsługują one [indeksatorów usługi Azure SQL Database](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indeksatory obiektów blob platformy Azure](../search/search-howto-indexing-azure-blob-storage.md) i szczęście [indeksatorów usługi Azure Cosmos DB](../search/search-howto-index-documentdb.md). Przejście informacji z usługi Cosmos DB do usługi Azure Search jest bardzo proste. Obie technologie przechowywania informacji w formacie JSON, więc wystarczy [utworzyć indeks](../search/search-create-index-portal.md) i mapowanie atrybutów ze swoich dokumentów, które mają być indeksowane. Gotowe. W zależności od rozmiaru danych wszystkie zawartości będą dostępne dla wyszukiwanych w ciągu kilku minut, najlepsze rozwiązania wyszukiwania jako usługi w kategoriach infrastruktura chmury.

Aby uzyskać więcej informacji na temat usługi Azure Search, możesz odwiedzić stronę [firmy Hitchhiker przewodnik wyszukiwania](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Podstawowej wiedzy

Po zapisaniu tej zawartości, który wzrostu i rozwoju każdego dnia, może się okazać myśleć: Co można zrobić za pomocą tego strumienia danych z moich użytkowników?

Odpowiedź na pytanie jest prosta: Umieść go do pracy i wyciągnij z niego.

Jednak można omawiane? Kilka proste przykłady [analizę tonacji](https://en.wikipedia.org/wiki/Sentiment_analysis)zawartości, zalecenia dotyczące oparte na preferencjach użytkownika, lub nawet automatycznych pakietu content moderator, który zapewnia, że z zawartością opublikowaną przez sieci społecznościowe, jest bezpieczny dla rodziny.

Teraz, gdy mam możesz podłączone prawdopodobnie będzie traktować należy niektóre tytuł doktora matematyczne analizy, aby wyodrębnić te wzorce i informacji poza proste baz danych i plików, ale może być nieprawidłowy.

[Usługa Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)stanowiący część [pakietu Cortana Intelligence](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), to usługa w pełni zarządzana usługa w chmurze, która umożliwia tworzenie przepływów pracy za pomocą prostego interfejsu przeciągania i upuszczania za pomocą algorytmów, kod algorytmów w [ R](https://en.wikipedia.org/wiki/R_\(programming_language\)), lub używać niektórych już utworzone i gotowe do użycia interfejsów API, takich jak: [Analiza tekstu](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [usługi Content Moderator, lub [zalecenia](https://gallery.azure.ai/Solution/Recommendations-Solution).

Aby osiągnąć dowolnego z tych scenariuszy usługi Machine Learning, można użyć [usługi Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) pozyskiwać dane z różnych źródeł. Można również użyć [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) do przetwarzania informacji i generuje danych wyjściowych, które mogą być przetwarzane przez uczenie maszynowe Azure.

Innym dostępną opcją jest użycie [usług Azure Cognitive Services](https://www.microsoft.com/cognitive-services) do analizowania zawartości; nie tylko można możesz im lepiej zrozumieć użytkowników (za pośrednictwem analizowanie zapisują za pomocą [interfejsu API analizy tekstu](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale można również wykrywanie niepożądanych lub dojrzała zawartości i podejmowanie odpowiednich działań dzięki [interfejs API przetwarzania obrazów](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Usługi cognitive Services zawiera wiele rozwiązań poza pole, które nie wymagają dowolnego rodzaju wiedzy uczenia maszynowego do użycia.

## <a name="a-planet-scale-social-experience"></a>W środowisku społecznościowych w skali globalnej

Jest ostatni, ale nie najmniej artykułu ważne I muszą spełnić: **skalowalność**. Podczas projektowania architektury każdego składnika powinny być skalowane samodzielnie. Po pewnym czasie będzie konieczne przetwarzanie większej ilości danych lub należy z większym zasięgiem geograficznym. Szczęście osiągnięcie zarówno do zadań jest **gotową** za pomocą usługi Cosmos DB.

Usługa cosmos DB obsługuje dynamiczne partycjonowania out-of--box. Automatycznie utworzy partycje na podstawie danego **klucza partycji**, który jest zdefiniowany jako atrybut w dokumentach. Definiowanie odpowiedniego klucza partycji musi odbywać się w czasie projektowania. Aby uzyskać więcej informacji, zobacz [partycjonowanie w usłudze Azure Cosmos DB](partitioning-overview.md).

Społecznościowe środowisko pracy należy wyrównać strategii partycjonowania ze sposobem zapytań i zapisu. (Na przykład, operacje odczytu w ramach tej samej partycji są pożądane i uniknąć "aktywne" Dzięki rozdzieleniu zapisy na wielu partycjach.) Niektóre opcje są: partycji na podstawie danych czasowych klucza (dzień/miesiąc/tydzień), według kategorii zawartości, regionu geograficznego lub użytkownika. To wszystko zależy sposób będzie wysłać zapytanie dotyczące danych i wyświetlanie danych w środowisko społecznościowe.

Usługa cosmos DB będzie uruchamiać zapytania (w tym [agregacje](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) we wszystkich partycji, więc nie trzeba dodać wszelka logika wzroście rozmiaru danych.

Z upływem czasu będzie ostatecznie rozwijasz w ruchu sieciowego i użycia zasobów (mierzoną w [RUs](request-units.md), lub jednostek żądań) więc ceny wzrosną. Będzie odczytu i zapisu więcej często wzrostem bazy użytkowników. Podstawowy użytkownik rozpocznie się tworzenie i odczytywanie większej ilości zawartości. Dlatego możliwość **skalowanie przepływności** jest istotne. Zwiększania RUs usługi jest bardzo proste. Możesz to zrobić za pomocą kilku kliknięć w witrynie Azure portal lub przez [wystawianie poleceń przez interfejs API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Skalowanie w górę i zdefiniowanie klucza partycji](./media/social-media-apps/social-media-apps-scaling.png)

Co się stanie, jeśli elementy stają się coraz lepsze? Załóżmy, że użytkownicy z innego regionu kraju lub kontynent Zwróć uwagę, platformy i rozpocząć korzystanie z niej. Co to doskonały niespodzianek!

Ale czeka! Możesz szybko zorientujesz się, że ich środowisko pracy z platformą nie są optymalne. Są one do tej pory od Twojego regionu operacyjnej panowanie to opóźnienie. Oczywiście nie chcesz je zamknąć. Jeśli tylko było to łatwy sposób **rozszerzanie zasięgu globalnego**? Istnieje!

Usługa cosmos DB umożliwia [Replikuj dane globalnie](../cosmos-db/tutorial-global-distribution-sql-api.md) i sposób niewidoczny dla użytkownika przy użyciu kilku kliknięć i automatycznie wybierz spośród dostępnych regionów z Twojej [kod klienta](../cosmos-db/tutorial-global-distribution-sql-api.md). Ten proces oznacza, że masz [wielu regionach w tryb failover](high-availability.md).

Podczas Replikuj dane globalnie, należy się upewnić, że klienci mogą z niej korzystać. Jeśli używasz frontonu sieci web lub dostęp do interfejsów API z klientów mobilnych, można wdrożyć [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) i klonowanie usługi Azure App Service na wszystkie żądane regionów, przy użyciu konfiguracji wydajności do obsługi usługi rozszerzonej globalne pokrycie. Gdy klientom dostęp do interfejsów API lub frontonu, będzie można kierować do najbliższego usługi App Service, co z kolei powoduje nawiązanie lokalna replika usługi Cosmos DB.

![Dodawanie zasięg globalny platformę społecznościowych](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Podsumowanie

W tym artykule rzuca światło na alternatywy tworzenia sieci społecznościowych całkowicie na platformie Azure za pomocą niedrogiej usługi. System ten zapewnia wyniki przez zachęcanie do wykorzystania z wielowarstwowych magazynu danych i rozwiązań dystrybucji o nazwie "Drabinę".

![Diagram interakcji między usługami systemu Azure dla sieci społecznościowej](./media/social-media-apps/social-media-apps-azure-solution.png)

Uspójniaj jest nie silver punktora dla tego rodzaju scenariuszy. Jest synergii utworzone przez kombinację wspaniałych usług, które pozwalają na tworzenie wspaniałych środowisk: szybkość i swobody usługi Azure Cosmos DB zapewnia doskonałe aplikacji społecznościowych analizy za rozwiązania najwyższej jakości wyszukiwania, takiego jak usługi Azure Search elastyczność usługi Azure App Services do hostowania aplikacji nie nawet niezależny od języka, ale procesów w tle zaawansowane i rozwijania usługi Azure Storage i Azure SQL Database do przechowywania dużych ilości danych i analizy możliwości usługi Azure Machine Learning do Utwórz wiedzy i analizy, które można przekazać opinię do procesów i Pomóż nam dostarczać odpowiednią treść do odpowiednich użytkowników.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o przypadkach użycia usługi Cosmos DB, zobacz [przypadków użycia wspólnego usługi Cosmos DB](use-cases.md).
