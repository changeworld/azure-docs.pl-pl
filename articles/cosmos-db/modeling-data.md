---
title: Modelowanie danych w usłudze Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Więcej informacji na temat modelowanie danych w bazach danych NoSQL, różnice między modelowania danych w relacyjnej bazie danych i bazy danych dokumentów.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: e8581cb130c8b2d7ac044838f0ae922b9b5e86cd
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275695"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelowanie danych w usłudze Azure Cosmos DB

Gdy baz danych bez schematu, takich jak usługi Azure Cosmos DB ułatwiają super do przechowywania i wykonywania zapytań o dane bez określonej struktury i o połowicznej strukturze, powinni spędzać niektóre myśleć czasu o swój model danych, aby maksymalnie wykorzystać usługę pod względem wydajności i skalowalności i najniższego Koszt.

Jak będzie dane mają być przechowywane Jak będzie aplikację do pobierania i wykonywania zapytań o dane Jest aplikacja ciężkich odczytu lub zapisu obciążenie?

Po przeczytaniu tego artykułu, możliwe będzie odpowiadać na następujące pytania:

* Co to jest modelowanie danych i dlaczego należy się?
* Czym różni się modelowania danych w usłudze Azure Cosmos DB w relacyjnej bazie danych?
* Jak express relacji między danymi w bazie danych nierelacyjnych?
* Gdy osadzone dane i kiedy utworzyć połączenie danych?

## <a name="embedding-data"></a>Trwa osadzanie danych

Po uruchomieniu modelowania danych w usłudze Azure Cosmos DB, spróbuj traktowanie jednostek jako **niezależna elementów** reprezentowane jako dokumenty JSON.

Dla porównania najpierw Zobaczmy, jak firma Microsoft model danych relacyjnej bazy danych. Poniższy przykład pokazuje, jak osoby mogą być przechowywane w relacyjnej bazie danych.

![Relacyjna baza danych modelu](./media/sql-api-modeling-data/relational-data-model.png)

Podczas pracy z relacyjnych baz danych, strategia jest znormalizować wszystkich Twoich danych. Normalizowanie danych zwykle obejmuje pobranie jednostki, takie jak osoba i podzielenie go na osobne składniki. W powyższym przykładzie osoba może mieć wiele rekordów szczegółów kontaktu, a także wiele rekordów adresów. Szczegółowe dane kontaktowe można dodatkowo podzielić, wyodrębniając dalsze typowe pola, takie jak typ. To samo dotyczy adresu, każdy rekord może być typu *Home* lub *firm*.

Przeprowadzi lokalnego po normalizowanie danych do **uniknąć przechowywania nadmiarowych danych** na każdym rejestrowania i raczej odnosi się do danych. W tym przykładzie można odczytać osoby, wraz z ich szczegółowe dane kontaktowe i adresami należy użyć SPRZĘŻEŃ, aby efektywnie tworzą ponownie (lub denormalizowanie) danych w czasie wykonywania.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizowanie jedna osoba wraz z ich szczegółowe dane kontaktowe i adresami wymaga operacje zapisu na wiele poszczególnych tabel.

Teraz Przyjrzyjmy się w jaki sposób firma Microsoft modelu te same dane jako niezależna jednostki w usłudze Azure Cosmos DB.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

Przy użyciu podejścia powyżej firma Microsoft ma **nieznormalizowany** przez osobę rejestrowania **osadzania** wszystkie informacje dotyczące tej osoby, takich jak ich szczegółowe dane kontaktowe i adresy do *pojedynczego JSON* dokumentu.
Ponadto ponieważ firma Microsoft nie są ograniczone do stałego schematu mamy elastyczność nadanie całkowicie konieczności skontaktowania się z różnych kształtów.

Pobieranie rekordu pełną osoby z bazy danych jest teraz **pojedynczej operacji odczytu** pojedynczy kontener lub dla pojedynczego elementu. Aktualizacja rekordu osoby, wraz z ich szczegółowe dane kontaktowe i adresami, jest również **pojedyncza operacja zapisu** względem pojedynczy element.

Denormalizing danych, aplikacja może być konieczne wystawiać mniejszej liczby zapytań i aktualizacji do wykonania typowych operacji.

### <a name="when-to-embed"></a>Kiedy należy osadzić

Ogólnie rzecz biorąc, używane osadzone dane modeli, gdy:

* Istnieją **zawarte** relacje między jednostkami.
* Istnieją **jeden do kilka** relacje między jednostkami.
* Brak danych osadzonych, **zmieniają się rzadko**.
* Brak osadzonych danych, który nie będzie się zwiększać **bez powiązanych z**.
* Brak danych osadzonych, która jest **badane często razem**.

> [!NOTE]
> Zwykle nieznormalizowane danych modele oferują lepsze **odczytu** wydajności.

### <a name="when-not-to-embed"></a>Kiedy nie należy osadzać

Gdy zasada mówi w usłudze Azure Cosmos DB jest denormalizowanie wszystko i osadzanie wszystkie dane w jeden element, może to prowadzić do sytuacje, w których należy unikać.

Wykonaj ten fragment kodu JSON.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Może to być jak jednostka wpis z komentarzami osadzone będzie wyglądać Jeśli możemy zostały modelowania typowe blogu lub CMS system. Problem z tym przykładem jest tablica komentarze **niepowiązane**, co oznacza, że jest nieograniczona (praktyczne) liczba komentarzy, które mogą mieć dowolnego pojedynczego wpisu. Ponieważ rozmiar elementu, można powiększać nieskończenie dużych może stać się problemem.

Rozmiar elementu wzrostem możliwość przesyłania danych za pośrednictwem sieci, a także odczytywanie i aktualizowanie elementu na dużą skalę, będzie mieć wpływ.

W takim wypadku byłoby lepiej wziąć pod uwagę następujące modelu danych.

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Ten model ma trzy najnowszych komentarzy osadzone w kontenerze wpis, który jest tablicą ustalony zestawu atrybutów. Inne uwagi są grupowane w partii 100 komentarze i przechowywane jako oddzielne elementy. Rozmiar partii została wybrana jako 100, ponieważ naszej fikcyjnej aplikacji umożliwia użytkownikowi załadować 100 komentarzy naraz.  

Innym przypadku, gdy osadzania danych nie jest dobrym pomysłem jest, gdy osadzonych danych jest często używany przez elementy i zmieni się często.

Wykonaj ten fragment kodu JSON.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

To może reprezentować portfel akcji osoby. Wybraliśmy osadzić podstawowe informacje w każdym dokumencie portfolio. W środowisku, w których powiązane dane zmieniają się często takich jak stock handlowymi aplikacji, osadzanie danych, która zmienia się często będzie oznaczać, że stale aktualizujesz każdy dokument portfolio za każdym razem, gdy zasobu jest przedmiotem handlu.

Stock *zaza* może być przedmiotem obrotu wiele setki razy w jednym dnia i tysiące użytkowników może mieć *zaza* ich portfolio. W przypadku modelu danych, takich jak powyżej firma Microsoft musi zaktualizować wielu tysięcy dokumentów portfolio wiele razy codziennie, co prowadzi do systemu, nie będzie skalują się dobrze.

## <a name="referencing-data"></a>Odwołanie do danych

Trwa osadzanie danych działa dobrze w wielu sytuacjach, ale tak, to oczywiste, że istnieją scenariusze denormalizing danych spowoduje, że więcej problemów niż warto. Dlatego co możemy zrobić teraz?

Relacyjne bazy danych nie są jedynym miejscem, w którym można utworzyć relacji między jednostkami. W bazie danych dokumentów może mieć informacji w jednym dokumencie, który faktycznie odnosi się do danych w innych dokumentach. Teraz mogę am nie działaniem na rzecz nawet minutę wbudowujemy systemów, które mogłyby być lepiej dostosowane do relacyjnej bazy danych w usłudze Azure Cosmos DB lub dowolnej innej bazy danych dokumentów, że proste relacje są wystarczające i może być przydatny.

Za pomocą pliku JSON poniżej wybraliśmy do użycia w przykładzie portfel akcji z wcześniej, ale tym razem nazywamy do podstawowego elementu w portfolio, zamiast go osadzić. Dzięki temu podstawowego elementu zmienia się często w ciągu dnia tylko dokument, który musi zostać zaktualizowany jest pojedynczego dokumentu podstawowego.

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

Natychmiastowe wadą tego podejścia jest jednak, jeśli aplikacja jest wymagana, aby pokazać informacje o każdej akcji, przechowywane przy wyświetlaniu portfolio osoby; w takim przypadku należy wprowadzić wiele rund do bazy danych można załadować informacji dla poszczególnych dokumentów standardowych. W tym miejscu wprowadziliśmy decyzję, aby zwiększyć wydajność operacji zapisu, które się zdarzyć, często w ciągu dnia, ale z kolei naruszenia zabezpieczeń na operacje odczytu, które potencjalnie mieć mniej wpływ na wydajność tego określonego systemu.

> [!NOTE]
> Znormalizowane modeli danych **może wymagać więcej wystąpień komunikacji dwustronnej** do serwera.

### <a name="what-about-foreign-keys"></a>Jak wygląda klucze obce?

Ponieważ aktualnie nie obowiązuje koncepcja ograniczenia, klucz obcy lub w inny sposób, relacje między dokumentu, które mają w dokumentach są faktycznymi "słabe łącza" i nie można zweryfikować przez sama baza danych. Jeśli chcesz potwierdzić, że dane, które dokument odwołuje się do faktycznie istnieje, należy to zrobić w aplikacji lub przy użyciu wyzwalaczy po stronie serwera lub procedur składowanych w usłudze Azure Cosmos DB.

### <a name="when-to-reference"></a>Kiedy należy odwoływać się do

Ogólnie rzecz biorąc, użyć znormalizowanych danych modeli, gdy:

* Reprezentuje **jeden do wielu** relacji.
* Reprezentuje **wiele do wielu** relacji.
* Dane dotyczące **zmienia się często**.
* Może być danych występujące w odwołaniu **niepowiązane**.

> [!NOTE]
> Normalizowanie zwykle zapewnia lepsze **zapisu** wydajności.

### <a name="where-do-i-put-the-relationship"></a>Gdzie umieścić relację?

Rozwój relacji pomoże określić, w których dokumentu do przechowywania odwołania.

Jeśli przyjrzymy się poniżej JSON, który modeluje wydawcy i książek.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

W przypadku małych wzrost ograniczona liczba książek na wydawcy, następnie przechowywanie odwołania książki w dokumencie wydawcy może być przydatne. Jednak w przypadku nieograniczona liczba książek na wydawcy tego modelu danych może prowadzić do modyfikowalnych tablic rosnący, tak jak w powyższym dokumencie wydawcy przykład.

Przełączanie rzeczy wokół nieco mogłoby spowodować modelu, który nadal reprezentuje te same dane, ale teraz pozwala uniknąć dużych kolekcjach modyfikowalna.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

W powyższym przykładzie Porzucono niepowiązane kolekcji w dokumencie wydawcy. Zamiast tego mamy po prostu odwołanie z wydawcą zostało nawiązane na każdy dokument książki.

### <a name="how-do-i-model-manymany-relationships"></a>Jak modelowanie relacji wiele: wiele?

W relacyjnej bazie danych *wiele: wielu* relacje często są modelowane sprzężenie tabel, które po prostu Dołącz razem rekordy z innych tabel.

![Dołączanie tabel](./media/sql-api-modeling-data/join-table.png)

Być może uznasz, że replikować ten sam efekt przy użyciu dokumentów i utworzyć model danych, który wygląda podobnie do poniższego.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

To będzie działać. Jednak podczas ładowania albo autor z ich książki lub ładowania książki z jego autorem zawsze wymaga co najmniej dwóch dodatkowych kwerend w bazie danych. Jedno zapytanie do przyłączany dokumentu, a następnie inne zapytanie, aby pobrać rzeczywiste dokumentu jest dołączony.

Jeśli wszystko, co dzieje się w tej tabeli sprzężenia jest łączenia ze ze sobą dwie części danych, dlaczego nie upuszczenie go całkowicie?
Należy wziąć pod uwagę następujące czynności.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Teraz gdyby Autor, od razu sprawdzić książek, które mogą być napisane tak, i odwrotnie gdyby załadować dokument książki będzie wiem identyfikatory autorów. Spowoduje to zapisanie tej pośrednie zapytania względem zmniejszenie sprzężenie tabeli, liczba serwerów rund, którą aplikacja ma się.

## <a name="hybrid-data-models"></a>Hybrydowych modelów danych

Obecnie analizujemy już osadzanie (lub denormalizing) i danych odwołujący się (lub Zakończono normalizowanie), o ich upsides, a każda z nich ma naruszeń jako Zaobserwowaliśmy.

Nie zawsze musi to być lub nie być Przerażony do nieco pomylone rzeczy.

Na podstawie wzorców użycia określonych aplikacji i obciążeń, które mogą wystąpić przypadki, gdzie osadzone mieszanie i danych występujące w odwołaniu ma sens, i może doprowadzić do prostszych logiki aplikacji za pomocą serwera mniej rund przy jednoczesnym zachowaniu dobry poziom wydajności.

Należy wziąć pod uwagę następujące dane JSON.

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

W tym miejscu możemy (najczęściej) wykonano osadzonym modelem, gdy dane z innymi jednostkami są osadzone w dokumencie najwyższego poziomu, ale odwołuje się do innych danych.

Jeśli przyjrzymy się dokument książki, widać kilka interesujących pola, gdy spojrzymy na tablicy autorów. Brak `id` pola, oznacza to, pola są używane do odnoszą się do dokumentu Autor standardową praktyką w znormalizowanych modelu, ale następnie mamy też `name` i `thumbnailUrl`. Firma Microsoft może być zatrzymany `id` i pozostałych aplikacji w celu uzyskania wszelkie dodatkowe informacje i potrzebny z dokumentu odpowiednich autora, za pomocą "Połącz", ale ponieważ nasza aplikacja wyświetla imię i nazwisko autora i obraz z każdej książki wyświetlane, można zapisać komunikacji dwustronnej z serwerem na książki na liście przez denormalizing **niektóre** danych od autora.

Upewnij się czy zmienić imię i nazwisko autora użytkownik chce zaktualizować swoje zdjęcie Przejdź i zaktualizuj każdej książki, ich kiedykolwiek opublikowany, ale dla naszej aplikacji na podstawie założenia, że autorzy zmienianie ich nazw często konieczne, to decyzja projektowa dopuszczalne.  

W tym przykładzie istnieją **wstępnie obliczone wartości zagregowanych** wartości można zapisać przetwarzania kosztownych operacji odczytu. W tym przykładzie dane osadzone w dokumencie Autor to dane, które jest obliczane w czasie wykonywania. Za każdym razem, gdy zostanie opublikowany nowy książki, tworzony jest dokument książki **i** pole countOfBooks jest ustawione na wartość obliczona na podstawie liczby dokumentów książki, które istnieją dla danego autora. Tego rodzaju optymalizacji może być dobrym odczytu dużych systemów, gdzie możemy pozwolić sobie w obliczeniach na operacje zapisu w celu zoptymalizowania Odczyt.

Możliwość dysponowania model przy użyciu wstępnie obliczonych pól jest możliwe, że usługa Azure Cosmos DB obsługuje **transakcji obejmujących wiele dokumentów**. Wiele magazynów NoSQL nie można wykonać transakcje w dokumentach i w związku z tym Ambasador decyzje dotyczące projektu, takich jak "zawsze osadzić wszystko", ze względu na to ograniczenie. Za pomocą usługi Azure Cosmos DB można użyć po stronie serwera wyzwalaczy ani procedur składowanych, które wstawiania książki i aktualizowania autorzy wszystko to w ramach transakcji ACID. Teraz zrobisz **mają** osadzać wszystko w jednym dokumencie, po prostu, aby mieć pewność, że dane pozostają spójne.

## <a name="distinguishing-between-different-document-types"></a>Rozróżniania typów dokumentów

W niektórych scenariuszach możesz chcieć łączyć różnych typów dokumentów w tej samej kolekcji; to zwykle tak jest gdy chcesz, aby wiele powiązanych dokumentów znajdują się w tym samym [partycji](partitioning-overview.md). Na przykład można umieścić zarówno książki i łącza do recenzji w tej samej kolekcji i jego partycji `bookId`. W takiej sytuacji zazwyczaj chcesz dodać do dokumentów za pomocą pola, które identyfikuje ich typu, aby odróżnić je.

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>Kolejne kroki

Największe wnioski z tego artykułu są Aby dowiedzieć się, że modelowania danych w świecie bez schematu jest ważniejsza niż kiedykolwiek wcześniej.

Po prostu, ponieważ nie istnieje sposób pojedynczej do reprezentowania elementu danych na ekranie, nie ma jednego możliwości do modelowania danych. Możesz potrzebne, aby zrozumieć aplikacji i jak powoduje wygenerowanie, używanie i przetwarzania danych. Następnie stosując niektóre wytycznych przedstawionych tutaj można ustawić dotyczących tworzenia modelu, odnoszący się do natychmiastowego wymagań aplikacji. Jeśli aplikacje muszą zmienić, możesz korzystać z elastyczności bez schematu bazy danych obejmuje zmiany i łatwego rozwijania modelu danych.

Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zapoznaj się z usługą firmy [dokumentacji](https://azure.microsoft.com/documentation/services/cosmos-db/) strony.

Aby dowiedzieć się, jak do dzielenia danych na wielu partycjach dotyczą [partycjonowanie danych w usłudze Azure Cosmos DB](sql-api-partition-data.md).
