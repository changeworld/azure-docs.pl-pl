---
title: 'Wzorzec projektowy w usłudze Azure DB rozwiązania Cosmos: aplikacje mediów społecznościowych | Dokumentacja firmy Microsoft'
description: Informacje na temat wzorzec projektowania dla sieci społecznościowych dzięki wykorzystaniu elastyczność magazynu bazy danych Azure rozwiązania Cosmos i innymi usługami Azure.
keywords: Aplikacje mediów społecznościowych
services: cosmos-db
author: ealsur
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 2dbf83a7-512a-4993-bf1b-ea7d72e095d9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: mimig
ms.openlocfilehash: d223187ab1f8338c57e6aa32903ea9b762393ab5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="going-social-with-azure-cosmos-db"></a>Przechodzenie społecznościowych z bazy danych Azure rozwiązania Cosmos
Życia społeczeństwa ogromną skalę połączenia oznacza, że w pewnym momencie życia staje się częścią **sieci społecznościowych**. Korzystamy z sieciami społecznościowymi Utrzymuj znajomych, współpracowników, rodziny, lub czasami udostępniać naszym męczennicy osobom wspólnych zainteresowań.

Jako inżynierów lub deweloperów, firma Microsoft może mieć zastanawiasz się, jak te sieci magazynu i łączyć naszych danych lub może mieć zostały nawet zlecił do utworzenia lub yourselves projektowania nowej sieci społecznościowych na rynek niszowych określonych. Gdy to kwestia big: jak te dane są przechowywane?

Załóżmy, że tworzenia nowych i obiektowi błyszczący sieci społecznościowych, gdzie naszych użytkowników umożliwia opublikowanie artykułów z powiązanych nośniku, na przykład, obrazów, klipów wideo lub nawet utworów muzycznych. Użytkownicy mogą dodać komentarz dotyczący ogłoszeń i zapewniają punktów klasyfikacji. Będzie źródło wpisów, które użytkownicy zobaczą i mieć możliwość interakcji z na stronie docelowe głównej witryny sieci Web. To nie dźwiękowej naprawdę złożonych (na początku), ale dla uproszczenia, należy teraz (Firma Microsoft może delve do źródła danych użytkownika niestandardowego wpływ relacji, ale jego rozmiar przekracza celem tego artykułu).

Tak jak możemy zapisać tej i gdzie?

Wiele osób może zawierać obsługi baz danych SQL lub co najmniej zawiera pojęcie [modelowania danych relacyjnych](https://en.wikipedia.org/wiki/Relational_model) i może się wydawać się rozpoczęcie pobierania podobny do następującego:

![Diagram pokazujący względną modelu relacyjnych](./media/social-media-apps/social-media-apps-sql.png) 

Struktura danych doskonale znormalizowane i łatwa... że nie skalowania. 

Nie pobieraj mnie niewłaściwy I pracował z bazy danych SQL wszystkie moje życia, są one bardzo, ale jak każdej platformy wzorzec, praktyki i oprogramowania nie jest idealny dla każdego scenariusza.

Dlaczego SQL najlepszym rozwiązaniem w tym scenariuszu? Oto struktury pojedynczego post, jeśli miała do wyświetlenia tego wpisu w witrynie sieci Web lub aplikacji, czy trzeba wykonywać zapytania... Pokazanie jednego pojedynczego post, teraz, może być wyświetlana strumień wpisów dynamicznie obciążenia i są wyświetlane na ekranie, gdzie użyjemy obraz po prostu sprzężeń tabel 8 (!).

Można oczywiście używamy agencji wystąpienie serwera SQL o wystarczającej ilości mocy rozwiązać tysiące kwerendy te wielu sprzężeń, aby obsługiwać zawartość, ale naprawdę, dlaczego czy możemy, jeśli istnieje rozwiązanie prostsze?

## <a name="the-nosql-road"></a>Drogowej NoSQL
Ten artykuł przeprowadzi Cię do modelowania platformy społecznościowych danych z bazy danych NoSQL platformy Azure [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) w sposób ekonomiczny, podczas korzystania z bazy danych innych Azure rozwiązania Cosmos funkcje, takie jak [interfejsu API programu Graph Gremlin](../cosmos-db/graph-introduction.md). Przy użyciu [NoSQL](https://en.wikipedia.org/wiki/NoSQL) podejście, przechowywanie danych w formacie JSON i stosowanie [denormalization](https://en.wikipedia.org/wiki/Denormalization), naszych wcześniej skomplikowane post może zostać zamieniony na pojedynczy [dokumentu](https://en.wikipedia.org/wiki/Document-oriented_database):


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

I można uzyskać z pojedynczego zapytania i nie sprzężenia. Jest to bardziej proste i bezpośrednie i budget-wise, wymaga mniej zasobów, aby uzyskać lepszą jakość.

Azure DB rozwiązania Cosmos upewnia się, że wszystkie właściwości są indeksowane z jego automatycznego indeksowania może nawet to [dostosowane](indexing-policies.md). Bez schematu podejście pozwala nam przechowywania dokumentów z różnych i dynamicznych struktur, może być jutro chcemy wpisów, aby wyświetlić listę kategorii lub hashtagów skojarzonych z nimi, DB rozwiązania Cosmos obsługi nowych dokumentów, dodane atrybuty z żadne dodatkowe czynności wymagane przez nas.

Komentarze na ogłoszenie (post) może być traktowana jako tylko innych wpisów z właściwością nadrzędnego (upraszcza naszych mapowania obiektu). 

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

I wszystkie interakcje społecznościowych mogą być przechowywane na oddzielnych obiektów jako liczników:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Tworzenie źródła danych jest wystarczy tworzenie dokumentów zawierających listy identyfikatorów post z kolejnością danego znaczenie:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Firma Microsoft może mieć "najnowszej" strumienia z wpisów uporządkowanych według daty utworzenia, strumień "najnowszych" tych wpisów z bardziej lubi w ostatnich 24 godzin, może nawet wprowadzania niestandardowych strumieni dla każdego użytkownika opartych na logice, takich jak adherentami i udziałów i będą nadal z wykazem. Jest to kwestia sposobu tworzenia tych list, ale wydajność odczytu pozostaje swobodnego. Po możemy uzyskać jeden z tych list, możemy wystawić pojedynczego zapytania przy użyciu rozwiązania Cosmos DB [w operatorze](sql-api-sql-query.md#WhereClause) uzyskać stron wpisów w czasie.

Mogą być zbudowane strumieni źródła przy użyciu [usługi aplikacji Azure](https://azure.microsoft.com/services/app-service/) procesów w tle: [Webjob](../app-service/web-sites-create-web-jobs.md). Utworzone stanowiska proces przetwarzania w tle może zostać zainicjowany przy użyciu [usługi Azure Storage](https://azure.microsoft.com/services/storage/) [kolejek](../storage/queues/storage-dotnet-how-to-use-queues.md) i wyzwalane za pomocą zadania Webjob [zestaw SDK zadań Webjob Azure](https://github.com/Azure/azure-webjobs-sdk/wiki), implementacja propagacji wpisu wewnątrz strumieni oparte na własnej logiki niestandardowej. 

Punkty i podobne za pośrednictwem ogłoszenie (post) mogą być przetwarzane w sposób odroczonego w ten sam sposób, aby utworzyć ostatecznie spójne środowisko.

Adherentami są trudniejszy. Rozwiązania cosmos bazy danych ma dokumentu maksymalny limit rozmiaru i odczytu/zapisu dużych dokumentów może mieć wpływ na skalowalność aplikacji. Dlatego sądzisz o zapisywaniu adherentami jako dokument z tej struktury:

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

To może działać dla użytkownika z kilku tysięcy adherentami, ale jeśli niektóre renomy sprzężenia naszych rangi, spowoduje to podejście prowadzić do rozmiaru dużych dokumentów i ostatecznie może osiągnęła limit rozmiaru dokumentu.

Aby rozwiązać ten problem, możemy użyć mieszanych podejście. Jako część dokumentu statystyki użytkownika firma Microsoft może przechowywać liczba adherentami:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

I rzeczywistego wykres adherentami mogą być przechowywane przy użyciu bazy danych Azure rozwiązania Cosmos [interfejsu API programu Graph Gremlin](../cosmos-db/graph-introduction.md), aby utworzyć [wierzchołków](http://mathworld.wolfram.com/GraphVertex.html) dla każdego użytkownika i [krawędzi](http://mathworld.wolfram.com/GraphEdge.html) który zachowania relacji "Następujące A-B". Interfejsu API programu Graph umożliwia można nie tylko uzyskać adherentami określonego użytkownika, ale tworzenie bardziej złożonych zapytań nawet wspólną sugerowanie osób. Jeśli dodamy do kategorii zawartości tej osoby, takich jak lub korzystać z wykresu, możemy rozpocząć tkania procesów, które obejmują inteligentne funkcję odnajdowania zawartości, sugerowanie zawartości tym te, które firma Microsoft wykonaj, takich jak lub znajdowania osoby, z którym firma Microsoft może być znacznie wspólnych.

Dokument statystyki użytkownika można nadal służyć do tworzenia kart interfejsu użytkownika lub podglądy szybkie profilu.

## <a name="the-ladder-pattern-and-data-duplication"></a>Duplikatów danych i wzorzec "Drabina"
Jak można zauważyć w dokumencie JSON, który odwołuje się do post, istnieje wiele wystąpień użytkownika. I można będzie mieć odgadnąć prawo, to oznacza, że informacje, które reprezentuje podana przez użytkownika, to denormalization może być obecny w więcej niż jednym miejscu.

Aby umożliwić szybsze zapytania, możemy pociągnąć za sobą deduplikacji danych. Problem z efektem ubocznym jest że jeśli przez niektóre akcje zmian danych użytkownika, należy znaleźć wszystkie działania kiedykolwiek została i zaktualizować je wszystkie. Nie dźwięk bardzo praktyczne, prawy?

Zamierzamy go rozwiązać, określając atrybutów klucza użytkownika, który zostanie przedstawiony w naszej aplikacji dla każdego działania. Jeśli firma Microsoft będzie Pokaż ogłoszenie (post) w naszej aplikacji i wyświetlić tylko twórcy nazwy i obrazu, dlaczego przechowywać wszystkie dane użytkownika w atrybucie "recenzowania utworzone przez"? Jeśli dla każdego komentarza zostanie tylko przedstawiony obrazu użytkownika, firma Microsoft nie wymagają z resztą jego informacje. To, gdzie wejścia coś można wywołać "wzorzec drabinę" play.

Spójrzmy informacje o użytkowniku, na przykład:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Analizując te informacje, firma Microsoft szybko wykrywać czyli kluczowych informacji i który nie jest tworząc "Drabina":

![Diagram przedstawiający wzorzec drabinę](./media/social-media-apps/social-media-apps-ladder.png)

Najmniejsza krok jest nazywany UserChunk, minimalnym część informacji umożliwiających identyfikację użytkownika i jest używany do duplikacji danych. Zmniejszenie rozmiaru zduplikowanych danych tylko informacje "w tekście", firma Microsoft ograniczenie możliwości ogromną aktualizacji.

Środkowy krok nosi nazwę użytkownika, jest pełne dane, które będą używane w większości kwerend zależne od wydajność dla rozwiązania Cosmos bazy danych, uzyskuje się dostęp i krytyczne. Zawiera informacje powiązane z UserChunk.

Największy jest rozszerzony użytkownika. Zawiera wszystkie informacje o użytkowniku krytycznych oraz inne dane, które nie naprawdę potrzebne do szybkiego odczytu lub jej użycie jest ostatecznego (np. proces logowania). Te dane mogą być przechowywane poza rozwiązania Cosmos bazy danych, w bazie danych SQL Azure lub tabel magazynu Azure.

Dlaczego czy możemy podzielić użytkownika i nawet przechowywać tych informacji w różnych miejscach? Ponieważ z punktu widzenia wydajności, tym większy dokumentów, costlier kwerendy. Przechowuje dokumenty cienki z właściwe informacje, czy wszystkie zapytania zależne od wydajności sieci społecznościowych i przechowywania dodatkowych informacji dla ostatecznego scenariusze, takie jak zmiany profilu pełne, logowania, nawet wyszukiwania danych analizy użycia i inicjatyw danych Big Data. Firma Microsoft naprawdę nieważne Jeśli zbierania danych do wyszukiwania danych jest mniejsza, ponieważ nie jest uruchomiona w bazie danych SQL Azure, firma Microsoft ma dotyczy jednak użytkowników że szybkie i obsługiwane środowiska. Użytkownik, przechowywane na DB rozwiązania Cosmos będzie wyglądać następująco:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

I Post powinien wyglądać tak:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

I podczas edycji występuje, gdy jeden z atrybutów fragmentów ma to wpływ na, jest łatwe do odnalezienia dotyczy dokumentów przy użyciu zapytań, które wskazują atrybuty indeksowane (Wybierz * FROM ogłoszeń p WHERE p.createdBy.id == "edited_user_id"), a następnie zaktualizowanie fragmentów.

## <a name="the-search-box"></a>Pole wyszukiwania
Użytkownicy wygeneruje szczęście dużo zawartości. Będziemy mogli oferują możliwość wyszukiwania i Znajdź zawartość, która może być bezpośrednio w ich strumieni zawartości, może być, ponieważ nie możemy wykonać twórców i może być tylko próbujemy znaleźć, że stary post robiliśmy 6 miesięcy temu.

Thankfully i ponieważ używamy bazy danych Azure rozwiązania Cosmos, firma Microsoft może łatwo zaimplementować aparat wyszukiwania przy użyciu [usługi Azure Search](https://azure.microsoft.com/services/search/) w kilka minut i bez wprowadzania pojedynczy wiersz kodu (inne niż oczywiście, proces wyszukiwania i interfejsu użytkownika).

Dlaczego jest to tak proste?

Usługa Azure Search implementuje one wywołać [indeksatory](https://msdn.microsoft.com/library/azure/dn946891.aspx)tła przetwarza tego punktu zaczepienia w repozytoriami danych i automagically dodać, zaktualizować lub usunąć obiektów w indeksach. Obsługują one [indeksatory bazy danych SQL Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indeksatory obiektów blob Azure](../search/search-howto-indexing-azure-blob-storage.md) i thankfully, [indeksatory bazy danych Azure rozwiązania Cosmos](../search/search-howto-index-documentdb.md). Przejście informacji z rozwiązania Cosmos bazy danych do usługi Azure Search jest proste, jak zarówno informacje magazynie w formacie JSON, musimy [tworzenia indeksu](../search/search-create-index-portal.md) i mapowanie atrybutów, które z naszych dokumentów chcemy indeksowane i, w ciągu kilku minut (zależy od rozmiaru danych), wszystkich naszych zawartość będzie dostępna do wyszukania po , przy użyciu najlepsze rozwiązania wyszukiwanie jako usługa w chmurze infrastruktury. 

Aby uzyskać więcej informacji na temat usługi Azure Search, możesz odwiedzić [Hitchhiker w przewodniku do wyszukiwania](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Podstawowej wiedzy
Po zapisaniu tej zawartości, który powiększa się i rozwoju codziennie, firma Microsoft może jesteśmy myśląc: co można zrobić z tego strumienia informacji od moich użytkowników?

Odpowiedź jest prosta: Zaprzęgnij do pracy i Dowiedz się od niego.

Ale co możemy informacje? Kilka łatwe przykładów [analizy wskaźniki nastrojów klientów](https://en.wikipedia.org/wiki/Sentiment_analysis), zawartości zalecenia na podstawie preferencji użytkownika lub nawet automatycznych zawartości moderatora, który zapewnia, że całej zawartości opublikowane przez naszych sieci społecznościowej są bezpieczne dla rodziny.

Teraz, możesz argumentów podłączono otrzymano, prawdopodobnie będzie traktować należy niektórych Praca nauki matematyczne, aby wyodrębnić te wzorce i informacje o proste baz danych i plików, ale może być nieprawidłowy.

[Usługa Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), część z [pakietu Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), jest usługą w chmurze pełni zarządzana, umożliwiający tworzenie przepływów pracy za pomocą prostego interfejsu przeciągania i upuszczania za pomocą algorytmów kodu algorytmy w [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) lub korzystać z niektórych już utworzone i gotowe do użycia interfejsów API, takich jak: [Analiza tekstu](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [zawartości moderatora](https://www.microsoft.com/moderator) lub [zalecenia](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Aby osiągnąć dowolnego z tych scenariuszy uczenia maszynowego, możemy użyć [usługi Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) pozyskiwania danych z różnych źródeł, a następnie użyć [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) do przetwarzania danych i generowanie danych wyjściowych, które mogą być przetwarzane przez usługi Azure Machine Learning.

Inny dostępną opcją jest użycie [kognitywnych usług firmy Microsoft](https://www.microsoft.com/cognitive-services) do analizowania zawartości; nie tylko może Rozumiemy im lepiej użytkowników (za pośrednictwem analizowanie zapisują z [interfejsu API z analizy tekstu](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), ale firma Microsoft może również wykrywania niechcianych ani dojrzałe zawartości i odpowiednio działają z [API przetwarzania obrazów komputera](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Kognitywnych usługi obejmują wiele rozwiązań poza pole, które nie wymagają dowolnego rodzaju wiedzy uczenia maszynowego do użycia.

## <a name="a-planet-scale-social-experience"></a>W środowisku społecznościowych planety skali
Jest ostatni, ale nie najmniej ważne tematu I muszą spełnić: **skalowalność**. Podczas projektowania architektury, które ważne jest, że każdego składnika można skalować na własną, albo ponieważ musimy przetwarzanie większej ilości danych lub ponieważ chcemy większy pokrycia geograficznego (lub obu!). Thankfully, jest złożonym zadaniem osiągnięcia **gotowe środowisko** DB rozwiązania Cosmos.

Obsługuje rozwiązania cosmos DB [dynamiczne partycjonowanie](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) out-of--box przez automatyczne tworzenie partycji na podstawie danego **klucza partycji** (zdefiniowany jako jeden z atrybutów w dokumentach). Definiowanie klucza partycji poprawne musi odbywać się w czasie projektowania i z uwzględnieniem [najlepsze rozwiązania](../cosmos-db/partition-data.md#designing-for-partitioning) dostępne; w przypadku obsługi społecznościowych, muszą być wyrównane strategii partycjonowania w sposobie wykonywania zapytań (odczyty w ramach tej samej partycji są pożądane) i zapisu (uniknąć "punkty aktywne" przez rozłożenie zapisy na wielu partycjach). Niektóre opcje są: partycje oparte na kluczu danych czasowych (dzień/miesiąc/tydzień), zawartości kategorii, regionu geograficznego, przez użytkownika. wszystko naprawdę zależy od sposobu będzie wysyłać zapytania o dane i pokazać środowiska społecznościowych. 

Jedną jest interesujące punktu warto zauważyć, które DB rozwiązania Cosmos wykona zapytania (w tym [agreguje](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) we wszystkich partycji przejrzysty, nie trzeba dodać wszelka logika wraz z rozwojem danych.

Z upływem czasu, należy po pewnym czasie wzrośnie w ruchu i użycia zasobów (mierzony w [RUs](request-units.md), albo w jednostkach żądań) wzrośnie. Zostanie odczytu i zapisu w częściej, Twoje userbase rozwoju lub rozpocznie tworzenie i odczytywanie więcej zawartości; zdolność **skalowanie sieci przepływności** jest ważna. Zwiększenie naszych RUs jest bardzo proste, możemy go za pomocą kilku kliknięć w portalu Azure lub przez [wydawania polecenia przy użyciu interfejsu API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Skalowanie w i definiowanie klucza partycji](./media/social-media-apps/social-media-apps-scaling.png)

Co się stanie, jeśli elementy pojawiają się lepiej użytkownicy z innego regionu, kraj lub kontynencie, zwróć uwagę, platformy i rozpocząć korzystanie z niej zaskoczeniem doskonałe!

Czekaj..., ale wkrótce realizować ich obsługi platformy nie są optymalne; są one wykonanej do tej pory od regionu operacyjne opóźnienie jest olbrzymich, czy oczywiście nie chcesz je zamknąć. Jeśli tylko było łatwo z **rozszerzanie zasięg globalny**..., ale istnieje!

Rozwiązania cosmos DB umożliwia [replikowany globalnie danych](../cosmos-db/tutorial-global-distribution-sql-api.md) i niewidocznie za pomocą paru kliknięć i automatycznie wybrać spośród dostępnych regionów, z Twojego [kodu klienta](../cosmos-db/tutorial-global-distribution-sql-api.md). Oznacza to również, że może mieć [kilka obszarów failover](regional-failover.md). 

Podczas danych jest replikowany globalnie, należy się upewnić, że klienci mogą wykorzystać go. Jeśli używasz frontonu sieci web lub w celu dostępu interfejsy API z klientów mobilnych, można wdrożyć [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) i klonowanie usługi aplikacji Azure we wszystkich obszarach żądaną przy użyciu konfiguracji wydajności do obsługi sieci rozszerzonej globalne pokrycie. Gdy klienci dostępu z frontonu lub interfejsów API, one będą kierowane do najbliższego usługi aplikacji, która z kolei będą łączyć się z lokalnej repliki bazy danych rozwiązania Cosmos.

![Dodawanie globalnego pokrycia do platformy społecznościowych](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Podsumowanie
W tym artykule próbuje pozostawia światło do alternatyw całkowicie tworzenie sieci społecznościowych na platformie Azure z ekonomicznych usług i zapewnienia doskonałe wyniki zachęcając do korzystania z dystrybucji rozwiązanie i danych wielowarstwowy magazynu o nazwie "Drabina".

![Diagram interakcji między usługami Azure, sieci społecznościowych](./media/social-media-apps/social-media-apps-azure-solution.png)

W rzeczywistości jest czy bez srebrny punktora dla tego rodzaju scenariusze nie istnieje, jest współdziałanie utworzone przez kombinację niezwykłych usług, które umożliwiają tworzyć wspaniałe środowiska: szybkości i swobody bazy danych Azure rozwiązania Cosmos zapewniają dużą społecznego aplikacji, Analiza za rozwiązania wyszukiwania najwyższej jakości, takie jak usługi Azure Search, elastyczność usługi aplikacji Azure do hostowania aplikacji nie nawet języka niezwiązane z żadnym, ale procesów w tle wydajne i rozwijania usługi Azure Storage i bazy danych SQL Azure do przechowywania olbrzymich ilości danych i możliwości analityczne uczenie maszynowe Azure do utworzenia wiedzy i analizy, które można wyrazić swoją opinię do naszej procesów i pomóc nam dostarczania odpowiedniej zawartości do odpowiednich użytkowników.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o przypadkach użycia rozwiązania Cosmos bazy danych, zobacz [DB rozwiązania Cosmos typowych zastosowań](use-cases.md).