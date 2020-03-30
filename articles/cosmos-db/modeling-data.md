---
title: Modelowanie danych w usłudze Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Dowiedz się więcej o modelowaniu danych w bazach danych NoSQL, różnicach między modelowania danych w relacyjnej bazie danych i bazie danych dokumentów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755013"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelowanie danych w usłudze Azure Cosmos DB

Podczas gdy bazy danych bez schematu, takie jak Azure Cosmos DB, sprawiają, że bardzo łatwo przechowywać i wysyłać zapytania do danych nieustrukturyzowanych i częściowo ustrukturyzowanych, należy poświęcić trochę czasu na myślenie o modelu danych, aby w pełni wykorzystać wydajność i skalowalność oraz najniższą skalowalność i najniższą Koszt.

W jaki sposób będą przechowywane dane? W jaki sposób aplikacja będzie pobierać i wysyłać zapytania do danych? Czy aplikacja jest ciężka do odczytu lub zapisu ciężki?

Po przeczytaniu tego artykułu, będziesz w stanie odpowiedzieć na następujące pytania:

* Co to jest modelowanie danych i dlaczego warto się tym przejmować?
* Czym różni się modelowanie danych w usłudze Azure Cosmos DB od relacyjnej bazy danych?
* Jak wyrazić relacje danych w nierelacyjnej bazie danych?
* Kiedy osadzam dane i kiedy łącze do danych?

## <a name="embedding-data"></a>Osadzanie danych

Po uruchomieniu modelowania danych w usłudze Azure Cosmos DB spróbuj traktować jednostki jako **samodzielne elementy** reprezentowane jako dokumenty JSON.

Dla porównania najpierw zobaczmy, jak możemy modelować dane w relacyjnej bazie danych. W poniższym przykładzie pokazano, jak osoba może być przechowywana w relacyjnej bazie danych.

![Relacyjny model bazy danych](./media/sql-api-modeling-data/relational-data-model.png)

Podczas pracy z relacyjnych baz danych, strategia jest normalizacja wszystkich danych. Normalizacja danych zazwyczaj polega na pobraniu jednostki, takiej jak osoba, i podzieleniu jej na oddzielne składniki. W powyższym przykładzie osoba może mieć wiele rekordów szczegółów kontaktu, a także wiele rekordów adresów. Dane kontaktowe można dalej rozkładać, wyodrębniając wspólne pola, takie jak typ. To samo dotyczy adresu, każdy rekord może być typu *Dom* lub *Firma*.

Założeniem przewodnim podczas normalizacji danych jest **uniknięcie przechowywania nadmiarowych danych** w każdym rekordzie i raczej odwoływanie się do danych. W tym przykładzie, aby odczytać osobę, ze wszystkimi jej danymi kontaktowymi i adresami, należy użyć JOINS skutecznie komponować dane (lub denormalize) danych w czasie wykonywania.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizowanie jednej osoby z jej danymi kontaktowymi i adresami wymaga operacji zapisu w wielu poszczególnych tabelach.

Teraz przyjrzyjmy się, jak będziemy modelować te same dane jako samodzielnej jednostki w usłudze Azure Cosmos DB.

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

Stosując powyższe **podejście, zdenormalowaliśmy** rekord osoby, **osadząc** wszystkie informacje związane z tą osobą, takie jak ich dane kontaktowe i adresy, w jednym dokumencie *JSON.*
Ponadto, ponieważ nie ograniczamy się do schematu stałego, mamy elastyczność, aby wykonywać takie czynności, jak posiadanie szczegółów kontaktu o różnych kształtach.

Pobieranie pełnego rekordu osoby z bazy danych jest teraz **pojedynczą operacją odczytu** względem pojedynczego kontenera i dla pojedynczego elementu. Aktualizowanie rekordu osoby, z ich danymi kontaktowymi i adresami, jest również **pojedynczą operacją zapisu** względem pojedynczego elementu.

Przez denormalizing danych, aplikacja może być konieczne wydanie mniejszej liczby zapytań i aktualizacji, aby zakończyć typowe operacje.

### <a name="when-to-embed"></a>Kiedy osadzić

Ogólnie rzecz biorąc, należy używać wbudowanych modeli danych, gdy:

* Istnieją **zawarte** relacje między jednostkami.
* Istnieją **relacje jeden do niewielu** między jednostkami.
* Istnieją osadzone dane, które **zmieniają się rzadko**.
* Istnieją osadzone dane, które nie będą rosnąć **bez powiązanych**.
* Istnieją osadzone dane, które są **często używane razem.**

> [!NOTE]
> Zazwyczaj zdenormalizowane modele danych zapewniają lepszą wydajność **odczytu.**

### <a name="when-not-to-embed"></a>Kiedy nie osadzać

Podczas gdy regułą w usłudze Azure Cosmos DB jest denormalize wszystko i osadzać wszystkie dane w jednym elemencie, może to prowadzić do niektórych sytuacji, które należy unikać.

Weź ten fragment JSON.

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

Może to być, co jednostki post z osadzonych komentarzy będzie wyglądać, gdybyśmy modelowania typowy blog lub CMS, system. Problem z tym przykładem jest to, że tablica komentarzy jest **nieograniczona,** co oznacza, że nie ma (praktycznego) limitu liczby komentarzy, które może mieć pojedynczy post. Może to stać się problemem, ponieważ rozmiar przedmiotu może rosnąć nieskończenie duży.

Wraz ze wzrostem rozmiaru elementu będzie miało wpływ na możliwość przesyłania danych przez sieć, a także odczytywania i aktualizowania elementu na dużą skalę.

W takim przypadku lepiej byłoby wziąć pod uwagę następujący model danych.

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

Ten model ma trzy najnowsze komentarze osadzone w kontenerze post, który jest tablicą ze stałym zestawem atrybutów. Inne komentarze są pogrupowane w partiach 100 komentarzy i przechowywane jako oddzielne elementy. Rozmiar partii został wybrany jako 100, ponieważ nasza fikcyjna aplikacja pozwala użytkownikowi załadować 100 komentarzy naraz.  

Innym przypadkiem, w którym osadzanie danych nie jest dobrym pomysłem, jest to, że osadzone dane są często używane w elementach i często się zmieniają.

Weź ten fragment JSON.

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

Może to stanowić portfel akcji danej osoby. Postanowiliśmy osadzić informacje o akcjach w każdym dokumencie portfela. W środowisku, w którym powiązane dane często się zmieniają, na przykład aplikacja do handlu akcjami, osadzanie danych, które często się zmieniają, oznacza, że stale aktualizujesz każdy dokument portfela za każdym razem, gdy akcja jest handlowana.

Akcje *zaza* mogą być przedmiotem obrotu setki razy w ciągu jednego dnia, a tysiące użytkowników może mieć *zaza* na swoim portfelu. W modelu danych, takim jak powyższy, musielibyśmy aktualizować wiele tysięcy dokumentów portfela wiele razy dziennie, co prowadzi do systemu, który nie będzie dobrze skalowany.

## <a name="referencing-data"></a>Odwoływanie się do danych

Osadzanie danych działa ładnie w wielu przypadkach, ale istnieją scenariusze, gdy denormalizing danych spowoduje więcej problemów, niż jest to warte. Więc co teraz robimy?

Relacyjne bazy danych nie są jedynym miejscem, w którym można tworzyć relacje między encjami. W bazie danych dokumentów można mieć informacje w jednym dokumencie, które odnoszą się do danych w innych dokumentach. Nie zaleca się tworzenia systemów, które byłyby lepiej dostosowane do relacyjnej bazy danych w usłudze Azure Cosmos DB lub innej bazy danych dokumentów, ale proste relacje są w porządku i mogą być przydatne.

W JSON poniżej zdecydowaliśmy się na przykład portfela akcji z wcześniej, ale tym razem odnosimy się do pozycji akcji w portfelu zamiast osadzać go. W ten sposób, gdy towar akcji zmienia się często w ciągu dnia, jedynym dokumentem, który wymaga aktualizacji, jest pojedynczy dokument magazynowy.

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

Bezpośrednim minusem tego podejścia jest jednak, jeśli aplikacja jest wymagana do wyświetlania informacji o każdym magazynie, który jest przechowywany podczas wyświetlania portfela osoby; w takim przypadku należy wykonać wiele podróży do bazy danych, aby załadować informacje dla każdego dokumentu giełdowego. Tutaj podjęliśmy decyzję o poprawie wydajności operacji zapisu, które zdarzają się często w ciągu dnia, ale z kolei zagrożone na operacje odczytu, które potencjalnie mają mniejszy wpływ na wydajność tego konkretnego systemu.

> [!NOTE]
> Znormalizowane modele danych **mogą wymagać większej liczby rund** na serwer.

### <a name="what-about-foreign-keys"></a>A co z kluczami obcymi?

Ponieważ obecnie nie istnieje pojęcie ograniczenia, klucz obcy lub w inny sposób, wszelkie relacje między dokumentami, które masz w dokumentach są skutecznie "słabe łącza" i nie zostaną zweryfikowane przez samą bazę danych. Jeśli chcesz upewnić się, że dane, do których odnosi się dokument, faktycznie istnieje, należy to zrobić w aplikacji lub za pomocą wyzwalaczy po stronie serwera lub procedur przechowywanych w usłudze Azure Cosmos DB.

### <a name="when-to-reference"></a>Kiedy się odwoływać

Ogólnie rzecz biorąc, należy użyć znormalizowanych modeli danych, gdy:

* Reprezentujący relacje **jeden do wielu.**
* Reprezentujący relacje **wiele do wielu.**
* Powiązane **dane często się zmieniają**.
* Dane, do których istnieje odwołanie, mogą być **niezwiązane.**

> [!NOTE]
> Zazwyczaj normalizacji zapewnia lepszą wydajność **zapisu.**

### <a name="where-do-i-put-the-relationship"></a>Gdzie mogę umieścić związek?

Rozwój relacji pomoże określić, w którym dokumencie do przechowywania odwołania.

Jeśli spojrzymy na JSON poniżej, że modele wydawców i książek.

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

Jeśli liczba książek na wydawcę jest niewielka przy ograniczonym wzroście, przechowywanie odwołania do książki w dokumencie wydawcy może być przydatne. Jeśli jednak liczba książek na wydawcę jest nieograniczona, ten model danych doprowadzi do modyfikowalnych, rosnących tablic, jak w powyższym dokumencie wydawcy.

Przełączanie rzeczy wokół nieco spowodowałoby model, który nadal reprezentuje te same dane, ale teraz unika tych dużych kolekcji modyfikowalne.

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

W powyższym przykładzie upuściliśmy niezwiązane kolekcji w dokumencie wydawcy. Zamiast tego mamy tylko odniesienie do wydawcy w każdym dokumencie książki.

### <a name="how-do-i-model-manymany-relationships"></a>Jak modelować wiele:wiele relacji?

W relacyjnej bazie danych *wiele:wiele* relacji są często modelowane z tabel sprzężenia, które po prostu łączą rekordy z innych tabel razem.

![Dołączanie do tabel](./media/sql-api-modeling-data/join-table.png)

Możesz być kuszony, aby replikować to samo przy użyciu dokumentów i utworzyć model danych, który wygląda podobnie do następującego.

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

To by zadziałało. Jednak załadowanie autora z ich książek lub załadowanie książki z jego autorem, zawsze wymaga co najmniej dwóch dodatkowych zapytań do bazy danych. Jedno zapytanie do dokumentu łączącego, a następnie inne zapytanie, aby pobrać rzeczywisty dokument jest przyłączany.

Jeśli wszystko to tabela sprzężenia robi jest klejenie razem dwa kawałki danych, to dlaczego nie upuścić go całkowicie?
Należy wziąć pod uwagę następujące kwestie.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Teraz, gdybym miał autora, od razu wiem, które książki napisali, i odwrotnie, gdybym miał załadowany dokument książki, znałbym identyfikatory autora(-ów). Spowoduje to zapisanie kwerendy pośredniczącej względem tabeli sprzężenia, zmniejszając liczbę rund serwera, które aplikacja musi wykonać.

## <a name="hybrid-data-models"></a>Hybrydowe modele danych

Teraz przyjrzeliśmy się osadzaniu (lub denormalizacji) i odwoływaniu się (lub normalizacji) danych, każdy ma swoje plusy i każdy ma kompromisy, jak widzieliśmy.

Nie zawsze musi być albo albo, nie bój się mieszać rzeczy się trochę.

Na podstawie wzorców użycia określonych aplikacji i obciążeń mogą wystąpić przypadki, w których mieszanie danych osadzonych i przywoływanych ma sens i może prowadzić do prostszej logiki aplikacji przy mniejszej liczbie rund serwera przy zachowaniu dobrego poziomu wydajności.

Należy wziąć pod uwagę następujące JSON.

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

W tym miejscu mamy (głównie) po modelu osadzonego, gdzie dane z innych jednostek są osadzone w dokumencie najwyższego poziomu, ale inne dane są odwoływane.

Jeśli spojrzymy na dokument książki, możemy zobaczyć kilka interesujących pól, gdy spojrzymy na tablicę autorów. Istnieje `id` pole, którego używamy do odwoływania się do dokumentu autora, standardowa praktyka w `name` znormalizowanym modelu, ale wtedy mamy też i `thumbnailUrl`. Mogliśmy utknąć `id` i zostawić aplikację, aby uzyskać wszelkie dodatkowe informacje potrzebne z odpowiedniego dokumentu autora za pomocą "link", ale ponieważ nasza aplikacja wyświetla nazwisko autora i obraz miniatury z każdej książki wyświetlane możemy zapisać podróż do serwera na książkę na liście przez deormalowanie **niektórych** danych od autora.

Oczywiście, gdyby imię i nazwisko autora zmieniło się lub chcieli zaktualizować swoje zdjęcie, musielibyśmy zaktualizować każdą książkę, którą kiedykolwiek opublikowali, ale dla naszej aplikacji, przy założeniu, że autorzy nie zmieniają często swoich nazw, jest to dopuszczalna decyzja projektowa.  

W tym przykładzie istnieją wstępnie obliczone wartości **agregacji,** aby zaoszczędzić kosztowne przetwarzanie w operacji odczytu. W tym przykładzie niektóre dane osadzone w dokumencie autora są danymi, które są obliczane w czasie wykonywania. Za każdym razem, gdy nowa książka jest publikowana, tworzony jest dokument książki, **a** pole CountOfBooks jest ustawiane na wartość obliczeniową na podstawie liczby dokumentów książki, które istnieją dla określonego autora. Ta optymalizacja byłaby dobra w systemach odczytu ciężkich, gdzie możemy sobie pozwolić na obliczenia na zapisy w celu optymalizacji odczytów.

Możliwość modelowania z wstępnie obliczonymi polami jest możliwa, ponieważ usługa Azure Cosmos DB obsługuje **transakcje wieloznadowe.** Wiele sklepów NoSQL nie może wykonywać transakcji między dokumentami i dlatego zaleca decyzje projektowe, takie jak "zawsze osadzić wszystko", ze względu na to ograniczenie. Za pomocą usługi Azure Cosmos DB można używać wyzwalaczy po stronie serwera lub procedur przechowywanych, które wstawiają książki i aktualizują autorów w ramach transakcji ACID. Teraz nie **musisz** osadzić wszystkiego w jednym dokumencie, aby upewnić się, że dane pozostają spójne.

## <a name="distinguishing-between-different-document-types"></a>Rozróżnianie różnych typów dokumentów

W niektórych scenariuszach można mieszać różne typy dokumentów w tej samej kolekcji; zwykle jest to przypadek, gdy chcesz wiele, powiązanych dokumentów, aby usiąść w tej samej [partycji](partitioning-overview.md). Na przykład można umieścić zarówno książki i recenzje książek `bookId`w tej samej kolekcji i podzielić go przez . W takiej sytuacji zwykle chcesz dodać do dokumentów pole, które identyfikuje ich typ w celu ich rozróżnienia.

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

## <a name="next-steps"></a>Następne kroki

Największe wynos z tego artykułu są do zrozumienia, że modelowanie danych w świecie bez schematu jest tak samo ważne, jak zawsze.

Tak jak nie ma jednego sposobu do reprezentowania fragmentu danych na ekranie, nie ma jednego sposobu modelowania danych. Należy zrozumieć aplikacji i jak będzie produkować, zużywać i przetwarzać dane. Następnie, stosując niektóre z wytycznych przedstawionych w tym miejscu można ustawić tworzenie modelu, który odpowiada na bezpośrednie potrzeby aplikacji. Gdy aplikacje trzeba zmienić, można wykorzystać elastyczność bazy danych bez schematu, aby objąć tę zmianę i łatwo rozwijać model danych.

Aby dowiedzieć się więcej o usłudze Azure Cosmos DB, zapoznaj się ze stroną [dokumentacji](https://azure.microsoft.com/documentation/services/cosmos-db/) usługi.

Aby dowiedzieć się, jak fragmentowanie danych na wielu partycjach, zapoznaj się z [partycjonowanie danych w usłudze Azure Cosmos DB](sql-api-partition-data.md).

Aby dowiedzieć się, jak modelować i partycjonować dane w usłudze Azure Cosmos DB przy użyciu przykładu rzeczywistego świata, zapoznaj się [z modelowania danych i partycjonowania — przykład świata rzeczywistego.](how-to-model-partition-example.md)
