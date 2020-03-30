---
title: 'Wzorzec projektowania usługi Azure Cosmos DB: aplikacje mediów społecznościowych'
description: Dowiedz się więcej o wzorcu projektowania sieci społecznościowych, wykorzystując elastyczność magazynu usługi Azure Cosmos DB i innych usług platformy Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8428e417f5f86edca77edae6ca4b7ef84e5ff425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827296"
---
# <a name="going-social-with-azure-cosmos-db"></a>Społecznościowych dzięki usłudze Azure Cosmos DB

Życie w społeczeństwie masowo połączonych oznacza, że w pewnym momencie życia stajesz się częścią **sieci społecznościowej.** Korzystasz z sieci społecznościowych, aby pozostawać w kontakcie z przyjaciółmi, kolegami, rodziną, a czasem dzielić się swoją pasją z ludźmi o wspólnych zainteresowaniach.

Jako inżynierowie lub deweloperzy zastanawialiście się, w jaki sposób te sieci przechowują i łączą twoje dane. A może nawet mieć za zadanie stworzyć lub zaprojektować nową sieć społecznościową dla konkretnego rynku niszowego. Wtedy pojawia się istotne pytanie: W jaki sposób wszystkie te dane są przechowywane?

Załóżmy, że tworzysz nową i błyszczącą sieć społecznościową, w której użytkownicy mogą publikować artykuły z powiązanymi mediami, takimi jak zdjęcia, filmy, a nawet muzyka. Użytkownicy mogą komentować posty i dawać punkty za oceny. Na głównej stronie docelowej witryny pojawi się kanał z postami, z którymi użytkownicy będą mogli zobaczyć i z którymi będą współpracować. Ta metoda nie brzmi skomplikowane na początku, ale ze względu na prostotę, zatrzymajmy się na tym. (Możesz zagłębić się w niestandardowe źródła danych użytkowników, których dotyczą relacje, ale wykracza to poza cel tego artykułu).

Jak więc przechowywać te dane i gdzie?

Możesz mieć doświadczenie w bazach danych SQL lub mieć pojęcie [relacyjnego modelowania danych](https://en.wikipedia.org/wiki/Relational_model). Możesz zacząć rysować coś w następujący sposób:

![Diagram przedstawiający model względny relacyjny](./media/social-media-apps/social-media-apps-sql.png)

Doskonale znormalizowana i ładna struktura danych... który nie skaluje się.

Nie zrozumcie mnie źle, pracowałem z bazami danych SQL przez całe życie. Są świetne, ale jak każdy wzór, praktyka i platforma oprogramowania, nie jest idealny dla każdego scenariusza.

Dlaczego SQL nie jest najlepszym wyborem w tym scenariuszu? Spójrzmy na strukturę jednego postu. Gdybym chciał pokazać post na stronie internetowej lub w aplikacji, musiałbym zrobić zapytanie z ... dołączając do ośmiu tabel(!) tylko po to, aby pokazać jeden post. Teraz wyobraź sobie strumień postów, które dynamicznie ładują się i pojawiają się na ekranie, a zobaczysz, dokąd zmierzam.

Można użyć ogromnego wystąpienia SQL z wystarczającą mocą, aby rozwiązać tysiące zapytań z wieloma sprzężeniami do obsługi zawartości. Ale dlaczego miałbyś, skoro istnieje prostsze rozwiązanie?

## <a name="the-nosql-road"></a>Droga NoSQL

W tym artykule przewodnik po modelowaniu danych platformy społecznościowej za pomocą bazy danych NoSQL platformy Azure [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) w sposób ekonomiczny. Informuje również, jak korzystać z innych funkcji usługi Azure Cosmos DB, takich jak [interfejs API Gremlin.](../cosmos-db/graph-introduction.md) Przy użyciu podejścia [NoSQL,](https://en.wikipedia.org/wiki/NoSQL) przechowywania danych w formacie JSON i stosowania [denormalizacji,](https://en.wikipedia.org/wiki/Denormalization)wcześniej skomplikowany post można przekształcić w jeden [dokument:](https://en.wikipedia.org/wiki/Document-oriented_database)

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

I można go zdobyć za pomocą jednego zapytania i bez sprzężeń. Ta kwerenda jest znacznie proste i proste, a budżet mądry, wymaga mniej zasobów, aby osiągnąć lepszy wynik.

Usługa Azure Cosmos DB upewnia się, że wszystkie właściwości są indeksowane za pomocą automatycznego indeksowania. Automatyczne indeksowanie można nawet [dostosować](index-policy.md). Podejście bez schematu pozwala nam przechowywać dokumenty o różnych i dynamicznych strukturach. Może jutro chcesz, aby posty miały listę kategorii lub hashtagów z nimi powiązanych? Usługa Cosmos DB będzie obsługiwać nowe dokumenty z dodanymi atrybutami bez dodatkowej pracy wymaganej przez nas.

Komentarze do posta można traktować jak inne posty z właściwością nadrzędną. (Ta praktyka upraszcza mapowanie obiektów).

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

A wszystkie interakcje społeczne mogą być przechowywane na oddzielnym obiekcie jako liczniki:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Tworzenie kanałów informacyjnych to tylko kwestia tworzenia dokumentów, które mogą zawierać listę identyfikatorów postów z daną kolejnością trafności:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Możesz mieć "najnowszy" strumień z postami uporządkowanymi według daty utworzenia. Możesz też mieć "najgorętszy" strumień z tymi postami z większą liczą polubień w ciągu ostatnich 24 godzin. Można nawet zaimplementować niestandardowy strumień dla każdego użytkownika na podstawie logiki, takich jak naśladowcy i zainteresowania. Nadal byłaby to lista stanowisk. To kwestia tego, jak zbudować te listy, ale wydajność czytania pozostaje bez przeszkód. Po uzyskaniu jednej z tych list, wystawiasz pojedyncze zapytanie do usługi Cosmos DB przy użyciu [in słowa kluczowego,](sql-query-keywords.md#in) aby uzyskać strony wpisów naraz.

Strumienie danych można sbudować przy użyciu procesów w tle [usług Azure App Services:](https://azure.microsoft.com/services/app-service/) [Webjobs](../app-service/webjobs-create.md). Po utworzeniu wpisu przetwarzanie w tle można wyzwolić przy użyciu [kolejek](../storage/queues/storage-dotnet-how-to-use-queues.md) [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i funkcji Webjobs wyzwalanych przy użyciu narzędzia Azure [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), implementując propagację wpisów wewnątrz strumieni na podstawie własnej logiki niestandardowej.

Punkty i polubienia za pośrednictwem wpisu mogą być przetwarzane w sposób odroczony przy użyciu tej samej techniki, aby utworzyć ostatecznie spójne środowisko.

Obserwujący są trudniejsze. Usługa Cosmos DB ma limit rozmiaru dokumentu, a odczytywanie/zapisywanie dużych dokumentów może mieć wpływ na skalowalność aplikacji. Więc możesz pomyśleć o przechowywaniu obserwujących jako dokumentu o tej strukturze:

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

Ta struktura może działać dla użytkownika z kilkoma tysiącami obserwujących. Jeśli jednak niektóre gwiazdy dołączą do szeregów, takie podejście doprowadzi do dużego rozmiaru dokumentu i może ostatecznie trafić w limit rozmiaru dokumentu.

Aby rozwiązać ten problem, można użyć podejścia mieszanego. W ramach dokumentu Statystyka użytkownika można zapisać liczbę obserwujących:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Można przechowywać rzeczywisty wykres zwolenników przy użyciu [interfejsu API Gremlin](../cosmos-db/graph-introduction.md) usługi Azure Cosmos DB do tworzenia [wierzchołków](http://mathworld.wolfram.com/GraphVertex.html) dla każdego użytkownika i [krawędzi,](http://mathworld.wolfram.com/GraphEdge.html) które utrzymują relacje "A-follows-B". Dzięki interfejsowi API Gremlin możesz uzyskać zwolenników określonego użytkownika i tworzyć bardziej złożone zapytania, aby sugerować, że ludzie są wspólne. Jeśli dodasz do wykresu kategorie treści, które lubią lub lubią, możesz zacząć tkać środowiska, które obejmują inteligentne odkrywanie treści, sugerowanie treści, które lubisz, lub znajdowanie osób, które mogą mieć wiele wspólnego.

Dokument Statystyka użytkownika może nadal służyć do tworzenia kart w interfejsie użytkownika lub szybkich podglądach profilu.

## <a name="the-ladder-pattern-and-data-duplication"></a>Wzór "Drabina" i powielanie danych

Jak można zauważyć w dokumencie JSON, który odwołuje się do wpisu, istnieje wiele wystąpień użytkownika. I można by się domyślić, te duplikaty oznaczają, że informacje opisujące użytkownika, biorąc pod uwagę tę denormalizację, mogą być obecne w więcej niż jednym miejscu.

Aby umożliwić szybsze zapytania, należy ponieść duplikację danych. Problem z tym efektem ubocznym polega na tym, że jeśli przez jakąś akcję, dane użytkownika się zmieniają, musisz znaleźć wszystkie działania, które użytkownik kiedykolwiek zrobił i zaktualizować je wszystkie. Nie brzmi to praktyczne, prawda?

Rozwiążesz go, identyfikując kluczowe atrybuty użytkownika, które są wyświetlane w aplikacji dla każdego działania. Jeśli wizualnie pokazać post w aplikacji i pokazać tylko nazwę i obraz twórcy, dlaczego przechowywać wszystkie dane użytkownika w "createdBy" atrybut? Jeśli dla każdego komentarza po prostu pokazać zdjęcie użytkownika, tak naprawdę nie potrzebujesz reszty informacji użytkownika. To gdzie coś, co nazywam "wzór Drabina" staje się zaangażowany.

Weźmy informacje o użytkowniku jako przykład:

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

Patrząc na te informacje, można szybko wykryć, co jest krytyczną informacją, a która nie, tworząc w ten sposób "Ladder":

![Diagram wzoru drabiny](./media/social-media-apps/social-media-apps-ladder.png)

Najmniejszy krok nazywa się UserChunk, minimalna informacja, która identyfikuje użytkownika i jest używana do powielania danych. Zmniejszając zduplikowany rozmiar danych do tylko informacji, które "pokażesz", zmniejszasz możliwość masowych aktualizacji.

Środkowy krok jest nazywany użytkownikiem. Jest to pełne dane, które będą używane w większości zapytań zależnych od wydajności w usłudze Cosmos DB, najbardziej dostępne i krytyczne. Zawiera informacje reprezentowane przez UżytkownikaChunk.

Największym jest rozszerzony użytkownik. Zawiera krytyczne informacje o użytkowniku i inne dane, które nie muszą być szybko odczytywane lub ma ostateczne użycie, takie jak proces logowania. Te dane mogą być przechowywane poza usługą Cosmos DB, w usłudze Azure SQL Database lub tabelach usługi Azure Storage.

Dlaczego miałbyś podzielić użytkownika, a nawet przechowywać te informacje w różnych miejscach? Ponieważ z punktu widzenia wydajności, im większe dokumenty, tym droższe zapytania. Utrzymuj dokumenty w smukłej oswajać, a ich odpowiednie informacje można wykonywać we wszystkich zapytaniach zależnych od wydajności w sieci społecznościowej. Przechowuj inne dodatkowe informacje dla ewentualnych scenariuszy, takich jak pełne zmiany profilu, logowania i wyszukiwanie danych do analizy użycia i inicjatyw Big Data. Naprawdę nie obchodzi, czy zbieranie danych dla eksploracji danych jest wolniejszy, ponieważ jest uruchomiony w usłudze Azure SQL Database. Masz obawy, choć, że użytkownicy mają szybkie i szczupłe doświadczenie. Użytkownik przechowywany w usłudze Cosmos DB będzie wyglądać następująco:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

I post będzie wyglądać:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Gdy pojawi się edycja, w której dotyczy to atrybutu fragmentu, można łatwo znaleźć dokumenty, których dotyczy problem. Wystarczy użyć kwerend, które wskazują na atrybuty indeksowane, takie jak `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`, a następnie zaktualizować fragmenty.

## <a name="the-search-box"></a>Pole wyszukiwania

Użytkownicy wygenerują, na szczęście, dużo treści. I powinieneś być w stanie zapewnić możliwość wyszukiwania i znajdowania treści, które mogą nie być bezpośrednio w ich strumieniach treści, być może dlatego, że nie śledzisz twórców, a może po prostu próbujesz znaleźć ten stary post, który zrobiłeś sześć miesięcy temu.

Ponieważ używasz usługi Azure Cosmos DB, można łatwo zaimplementować wyszukiwarkę przy użyciu [usługi Azure Cognitive Search](https://azure.microsoft.com/services/search/) w ciągu kilku minut bez wpisywania kodu, innego niż proces wyszukiwania i interfejsu użytkownika.

Dlaczego ten proces jest tak prosty?

Usługa Azure Cognitive Search implementuje to, co nazywają indeksatory , [procesy](https://msdn.microsoft.com/library/azure/dn946891.aspx)w tle, które hak w repozytoriach danych i automatycznie dodać, zaktualizować lub usunąć obiekty w indeksach. Obsługują one [indeksatory usługi Azure SQL Database,](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/) [indeksatory obiektów blob platformy Azure](../search/search-howto-indexing-azure-blob-storage.md) i na szczęście [indeksatory usługi Azure Cosmos DB.](../search/search-howto-index-documentdb.md) Przejście informacji z usługi Cosmos DB do usługi Azure Cognitive Search jest proste. Obie technologie przechowują informacje w formacie JSON, więc wystarczy [utworzyć indeks](../search/search-create-index-portal.md) i zamapować atrybuty z dokumentów, które mają być indeksowane. Gotowe. W zależności od rozmiaru danych cała zawartość będzie dostępna do przeszukania w ciągu kilku minut za pomocą najlepszego rozwiązania search-as-a-service w infrastrukturze chmury.

Aby uzyskać więcej informacji na temat usługi Azure Cognitive Search, odwiedź [Przewodnik do wyszukiwania autostopowicza](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Podstawowa wiedza

Po zapisaniu całej tej zawartości, która rośnie i rośnie każdego dnia, możesz pomyśleć: Co mogę zrobić z tym strumieniem informacji od moich użytkowników?

Odpowiedź jest prosta: Umieść go do pracy i uczyć się od niego.

Ale czego możesz się nauczyć? Kilka prostych przykładów to [analiza opinii,](https://en.wikipedia.org/wiki/Sentiment_analysis)rekomendacje treści oparte na preferencjach użytkownika, a nawet zautomatyzowany moderator treści, który zapewnia, że treści publikowane przez sieć społecznościową są bezpieczne dla rodziny.

Teraz, gdy mam cię uzależniony, prawdopodobnie myślisz, że potrzebujesz doktora nauk matematycznych, aby wyodrębnić te wzorce i informacje z prostych baz danych i plików, ale mylisz się.

[Usługa Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), część [pakietu Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), to w pełni zarządzana usługa w chmurze, która umożliwia tworzenie przepływów pracy przy użyciu algorytmów w prostym interfejsie przeciągania i upuszczania, kodowanie własnych algorytmów w [językach R](https://en.wikipedia.org/wiki/R_\(programming_language\))lub korzystanie z niektórych już utworzonych i gotowych do użycia interfejsów API, takich jak: Analiza [tekstu](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Moderator zawartości lub [Zalecenia](https://gallery.azure.ai/Solution/Recommendations-Solution).

Aby osiągnąć dowolny z tych scenariuszy uczenia maszynowego, można użyć [usługi Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) do pozyskiwania informacji z różnych źródeł. Można również użyć [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) do przetwarzania informacji i generowania danych wyjściowych, które mogą być przetwarzane przez usługę Azure Machine Learning.

Inną dostępną opcją jest użycie [usługi Azure Cognitive Services](https://www.microsoft.com/cognitive-services) do analizowania zawartości użytkowników; nie tylko można je lepiej zrozumieć (poprzez analizę tego, co piszą z [Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale można również wykryć niechciane lub dojrzałe treści i działać odpowiednio z Computer Vision [API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Usługi Cognitive Services zawierają wiele gotowych rozwiązań, które nie wymagają żadnej wiedzy na temat uczenia maszynowego.

## <a name="a-planet-scale-social-experience"></a>Doświadczenie społeczne na skalę planety

Jest ostatni, ale nie najmniej ważny artykuł, który muszę zająć: **skalowalność**. Podczas projektowania architektury, każdy składnik powinien skalować na własną rękę. W końcu będziesz musiał przetworzyć więcej danych, lub będziesz chciał mieć większy zasięg geograficzny. Na szczęście, osiągnięcie obu zadań jest **"pod klucz" dzięki** Cosmos DB.

Usługa Cosmos DB obsługuje dynamiczne partycjonowanie gotowe do użycia. Automatycznie tworzy partycje na podstawie danego **klucza partycji,** który jest zdefiniowany jako atrybut w dokumentach. Definiowanie prawidłowego klucza partycji musi odbywać się w czasie projektowania. Aby uzyskać więcej informacji, zobacz [partycjonowanie w usłudze Azure Cosmos DB](partitioning-overview.md).

Aby uzyskać środowisko społecznościowe, należy wyrównać strategię partycjonowania ze sposobem, w jaki wysyłasz zapytania i piszesz. (Na przykład odczyty w ramach tej samej partycji są pożądane i uniknąć "hot spotów" przez rozprzestrzenianie zapisów na wielu partycjach.) Niektóre opcje to: partycje oparte na kluczu czasowym (dzień/miesiąc/tydzień), według kategorii zawartości, regionu geograficznego lub użytkownika. Wszystko zależy od tego, jak będziesz badać dane i wyświetlać dane w swoich środowiskach społecznościowych.

Usługa Cosmos DB uruchomi zapytania (w tym [agregacje)](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)we wszystkich partycjach w sposób przezroczysty, więc nie trzeba dodawać żadnej logiki w miarę wzrostu danych.

Z czasem nastąpi wzrost ruchu, a zużycie zasobów (mierzone w [jednostkach RR](request-units.md)lub jednostkach żądań) wzrośnie. Będziesz czytać i pisać częściej w miarę wzrostu bazy użytkowników. Baza użytkowników rozpocznie tworzenie i czytanie większej ilości zawartości. Tak więc zdolność **skalowania przepływności** jest niezbędna. Zwiększenie us jest łatwe. Można to zrobić za pomocą kilku kliknięć w witrynie Azure portal lub [wydając polecenia za pośrednictwem interfejsu API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Skalowanie i definiowanie klucza partycji](./media/social-media-apps/social-media-apps-scaling.png)

Co się stanie, jeśli sytuacja będzie coraz lepsza? Załóżmy, że użytkownicy z innego regionu, kraju lub kontynentu zauważą Twoją platformę i zaczną z niej korzystać. Co za wielka niespodzianka!

To nie wszystko! Wkrótce zdajesz sobie sprawę, że ich doświadczenie z platformą nie jest optymalne. Są tak daleko od regionu operacyjnego, że opóźnienie jest straszne. Oczywiście nie chcesz, aby rzucić. Jeśli tylko nie było łatwy sposób **na rozszerzenie globalnego zasięgu?** Jest!

Usługa Cosmos DB umożliwia [replikację danych globalnie](../cosmos-db/tutorial-global-distribution-sql-api.md) i w sposób przejrzysty za pomocą kilku kliknięć i automatyczne wybieranie spośród dostępnych regionów z [kodu klienta.](../cosmos-db/tutorial-global-distribution-sql-api.md) Ten proces oznacza również, że można mieć [wiele regionów trybu failover](high-availability.md).

Podczas replikowania danych globalnie, należy upewnić się, że klienci mogą korzystać z nich. Jeśli używasz frontonu sieci web lub uzyskujesz dostęp do interfejsów API z klientów mobilnych, możesz wdrożyć [usługę Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) i sklonować usługę Azure App Service we wszystkich żądanych regionach, używając konfiguracji wydajności do obsługi rozszerzonego globalnego zasięgu. Gdy klienci uzyskują dostęp do frontendu lub interfejsów API, będą one kierowane do najbliższej usługi app service, która z kolei połączy się z lokalną repliką usługi Cosmos DB.

![Dodawanie globalnego zasięgu do platformy społecznościowej](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Podsumowanie

W tym artykule rzuca trochę światła na alternatywy tworzenia sieci społecznościowych całkowicie na platformie Azure z usługami o niskich kosztach. zapewnia rezultaty, zachęcając do korzystania z wielowarstwowego rozwiązania pamięci masowej i dystrybucji danych o nazwie "Ladder".

![Diagram interakcji między usługami platformy Azure dla sieci społecznościowych](./media/social-media-apps/social-media-apps-azure-solution.png)

Prawda jest taka, że nie ma srebrnej kuli dla tego rodzaju scenariuszy. Jest to synergia stworzona przez połączenie wspaniałych usług, które pozwalają nam tworzyć wspaniałe doświadczenia: szybkość i wolność usługi Azure Cosmos DB w celu zapewnienia świetnej aplikacji społecznościowej, inteligencji stojącej za pierwszorzędnym rozwiązaniem wyszukiwania, takim jak Azure Cognitive Search, elastyczność usług Azure App Services do obsługi nawet aplikacji niezależnych od języka, ale zaawansowane procesy w tle oraz rozszerzalna usługa Azure Storage i usługa Azure SQL Database do przechowywania ogromnych ilości danych i możliwości analitycznych usługi Azure Machine Learning twórz wiedzę i inteligencję, które mogą przekazywać informacje zwrotne do twoich procesów i pomagać nam dostarczać odpowiednie treści odpowiednim użytkownikom.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o przypadkach użycia usługi Cosmos DB, zobacz [Typowe przypadki użycia usługi Cosmos DB](use-cases.md).
