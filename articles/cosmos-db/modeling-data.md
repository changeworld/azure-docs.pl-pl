---
title: Modelowanie dokumentów danych dla bazy danych NoSQL | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o modelowania danych dla bazy danych NoSQL
keywords: modelowanie danych
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 69521eb9-590b-403c-9b36-98253a4c88b5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2016
ms.author: sngun
ms.openlocfilehash: 8704c8b9278406b56475d30d149e33055278ef70
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="modeling-document-data-for-nosql-databases"></a>Modelowanie danych dokumentów NoSQL baz danych
Podczas schematu baz danych bez, takich jak Azure DB rozwiązania Cosmos, stał się bardzo łatwe obejmuje zmiany w modelu danych powinien nadal spędzonego niektórych planowania czasu dotyczące danych. 

Jak danych ma być przechowywany? Jak będzie aplikację do pobierania i zapytania na danych Jest duże aplikacji pogrubiona odczytu lub zapisu? 

Po przeczytaniu tego artykułu, będzie mógł odpowiedzieć na następujące pytania:

* Jak należy traktować dokumentu w bazie danych dokumentów?
* Co to jest modelowania danych i dlaczego należy zależy? 
* Czym różni się modelowania danych w bazie danych dokumentów relacyjnej bazy danych?
* Jak express relacji danych w bazie danych nierelacyjnych?
* Gdy osadzanie danych i gdy połączyć z danych?

## <a name="embedding-data"></a>Osadzanie danych
Po uruchomieniu modelowania danych w magazynie dokumentu, takie jak bazy danych Azure rozwiązania Cosmos, spróbuj traktowanie jednostek jako **niezależne dokumenty** reprezentowane w formacie JSON.

Przed zajrzyj mamy dostęp zbyt dużo więcej Daj nam Zabierz kilka kroków i przyjrzeć jak firma Microsoft może coś relacyjnej bazy danych, temat, który wiele osób zna już modelu. W poniższym przykładzie pokazano, jak osoby mogą być przechowywane w relacyjnej bazie danych. 

![Model relacyjnej bazy danych](./media/sql-api-modeling-data/relational-data-model.png)

Podczas pracy z relacyjnych baz danych, firma Microsoft już został nauczanych lat do normalizacji normalizacji, normalizacji.

Zwykle normalizacji danych obejmuje pobranie jednostki, takie jak osoby i podzielenie go celu odrębny fragmentów danych. W powyższym przykładzie osoba może mieć wiele rekordów szczegóły kontaktu, a także wiele rekordów adresów. Firma Microsoft nawet wykonaj krok dalej i rozbić szczegóły dotyczące kontaktu wyodrębniając dalsze typowe pola, takich jak typu. Tego samego adresu, w tym polu każdego rekordu ma typu like *Home* lub *biznesowa* 

Przeprowadzi lokalnych podczas normalizacji danych **nie należy przechowywać nadmiarowych danych** na każdym Rejestruj i zamiast odwoływania się do danych. W tym przykładzie można odczytać osoby, ich szczegóły dotyczące kontaktu i adresy, należy użyć SPRZĘŻEŃ skutecznie agregacji danych w czasie wykonywania.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizowanie jedna osoba ich szczegóły dotyczące kontaktu i adresy wymaga operacji zapisu w wielu poszczególnych tabel. 

Teraz Spójrzmy na jak możemy modelu tych samych danych jako autonomiczną jednostkę w bazie danych dokumentu.

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

Powyżej podejście mamy teraz **nieznormalizowany** osoby rejestrowania gdzie możemy **osadzonych** wszystkie informacje dotyczące tej osobie, takie jak ich szczegóły dotyczące kontaktu i adresów, w jednym JSON dokument.
Ponadto ponieważ firma Microsoft nie jest ograniczone do stałego schematu mamy może wykonywać następujące czynności mających całkowicie skontaktowania się z różnych kształtów. 

Pobieranie rekordu pełną osoby z bazy danych jest teraz jeden odczytu operacji względem jednej kolekcji, a dla pojedynczego dokumentu. Aktualizacja rekordu osoby, ich szczegóły dotyczące kontaktu i adresy, jest również operacji zapisu pojedynczego dla pojedynczego dokumentu.

Denormalizing danych, aplikacja może być konieczne wystawiać mniej zapytania i na zakończenie typowych operacji aktualizacji. 

### <a name="when-to-embed"></a>Aby można było osadzić
Ogólnie rzecz biorąc, użyj osadzonych danych modeli, gdy:

* Brak **zawiera** relacje między obiektami.
* Brak **jeden do kilka** relacje między obiektami.
* Brak dostępnych danych osadzonych który **zmienia się rzadko**.
* Jest osadzony danych nie będzie rosnąć **bez powiązanych z**.
* Brak osadzonych danych, która jest **integralną** z danymi w dokumencie.

> [!NOTE]
> Zwykle nieznormalizowane danych modele umożliwiają lepsze **odczytu** wydajności.
> 
> 

### <a name="when-not-to-embed"></a>Kiedy nie można osadzić
Chociaż zasadą w bazie danych dokumentów do denormalize wszystko i osadzone wszystkie dane do pojedynczego dokumentu, może to prowadzić do niektórych sytuacjach, w których należy unikać.

Zająć ta Wstawka kodu JSON.

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

Może to być co podmiot post z komentarzami osadzone będzie wyglądała jeśli zostały możemy modelowania typowe blogu lub CMS, system. Problem z tym przykładzie jest to tablica komentarze **niepowiązany**, co oznacza, że nie ma żadnego limitu (praktyczne), liczba komentarze może mieć żadnych pojedynczego post. Problem będzie wzrostem rozmiaru dokumentu może znacznie.

Ucierpi w miarę zwiększania się możliwość przesyłania danych za pośrednictwem danych przesyłanych w sieci, a także odczytywanie i aktualizowanie dokumentu, na dużą skalę, rozmiar dokumentu.

W takim przypadku byłoby lepiej wziąć pod uwagę następujące modelu.

    Post document:
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

    Comment documents:
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

Ten model ma trzy najnowsze komentarze osadzone w post, który jest tablicą o stałym powiązany teraz. Inne komentarze są grupowane w partiach 100 komentarze i przechowywane w oddzielnych dokumentów. Rozmiar partii została wybrana jako 100, ponieważ fikcyjne aplikacji zezwala użytkownikowi na załadować 100 komentarze naraz.  

Innym przypadku, gdy osadzanie danych nie jest dobrym rozwiązaniem jest, gdy dane osadzone jest często używane w różnych dokumentach i będzie często zmieniana. 

Zająć ta Wstawka kodu JSON.

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

To może reprezentować portfolio standardowych osoby. Wybraliśmy osadzanie standardowych informacji w do każdego dokumentu portfolio. W środowisku, gdzie dane dotyczące zmienia się często takich jak giełdowych handlowymi aplikacji, osadzanie danych, które zmieniają się często będzie oznaczać, że stale aktualizowany każdy dokument portfolio za każdym razem, gdy jest przedmiotem handlu zasobu.

Stock *zaza* mogą być przedmiotem handlu setki wiele razy w jednym dniu i tysięcy użytkowników może mieć *zaza* na ich portfolio. Z modelem danych, takich jak powyżej czy konieczne jest uaktualnienie wielu tysięcy dokumentów portfolio wielokrotnie codziennie, co może prowadzić do systemu który nie będzie bardzo dobrego skalowania. 

## <a id="Refer"></a>Odwołanie do danych
Tak osadzanie danych działa dobrze w wielu przypadkach, ale jest jasne, czy istnieją scenariusze, gdy problemy więcej niż warto denormalizing danych spowoduje przerwanie. Dlatego co możemy zrobić teraz? 

Relacyjnych baz danych nie są jedynym miejscem, w którym można utworzyć relacji między obiektami. W bazie danych dokumentu może mieć informacji w jednym dokumencie, który faktycznie odnosi się do danych w innych dokumentów. Teraz I am nie postulować przez jedną minutę, nawet budujemy systemów, które mogłyby być lepiej dostosowane do relacyjnej bazy danych w usłudze Azure DB rozwiązania Cosmos lub wszelkie inne bazy danych dokumentów, że relacje proste jest uszkodzona i może być bardzo przydatne. 

W poniższych JSON wybraliśmy do użycia w przykładzie standardowych portfolio z wcześniej, ale tym razem możemy odnosi się do standardowych elementów na wchodzących w skład zestawu zamiast osadzania. W ten sposób element standardowych zmienia się często w ciągu dnia tylko dokument, który musi zostać zaktualizowany jest pojedynczego dokumentu standardowych. 

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


Wadą tego podejścia natychmiastowego interfejsu jest jednak, jeśli aplikacja jest wymagane, aby wyświetlić informacje o każdej akcji przechowywana podczas wyświetlania portfolio osoby; w takim przypadku należy wprowadzić wiele rund do bazy danych można załadować danych dla każdego dokumentu standardowych. W tym miejscu wprowadziliśmy decyzji, aby zwiększyć wydajność operacji zapisu, które się zdarzyć, często w ciągu dnia, ale z kolei naruszenia zabezpieczeń na operacje odczytu, potencjalnie mające mniej wpływ na wydajność tej konkretnej systemu.

> [!NOTE]
> Znormalizowany modeli danych **może wymagać więcej rund** do serwera.
> 
> 

### <a name="what-about-foreign-keys"></a>Jakie klucze obce?
Ponieważ nie ma żadnych koncepcji ograniczenia, klucz obcy lub w inny sposób relacje między dokumentów zawierających w dokumentach skutecznej "powiązania" i nie są weryfikowane przez sama baza danych. Jeśli chcesz upewnij się, że dane, które dokument odwołuje się do faktycznie istnieje, należy to zrobić w aplikacji lub przy użyciu wyzwalaczy po stronie serwera lub procedur składowanych dla bazy danych Azure rozwiązania Cosmos.

### <a name="when-to-reference"></a>Kiedy do odwołania
Ogólnie rzecz biorąc, użyj danych znormalizowane modele, gdy:

* Reprezentujący **jeden do wielu** relacji.
* Reprezentujący **wiele do wielu** relacji.
* Dane dotyczące **zmienia się często**.
* Może być danych występujące w odwołaniu **niepowiązany**.

> [!NOTE]
> Zwykle normalizacji zapewnia lepsze **zapisu** wydajności.
> 
> 

### <a name="where-do-i-put-the-relationship"></a>Gdzie umieścić relacji?
Rozwój relacji pomoże określić, w których dokumentu do przechowywania odwołanie.

Jeśli przyjrzymy się poniżej JSON modelujący wydawcy i książek.

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
    {"id": "1000", "name": "Deep Dive in to Azure Cosmos DB" }

W przypadku małych wzrost ograniczona liczba książek na wydawcy mogą być przydatne następnie przechowywanie odwołanie książki w dokumencie wydawcy. Jednak w przypadku niepowiązanego liczba książek na wydawcy tego modelu danych może spowodować modyfikowalna, rosnącym tablic, tak jak na przykład dokument wydawcy powyżej. 

Przełączanie rzeczy wokół bit spowoduje modelu, który nadal reprezentuje tych samych danych, ale teraz pozwala uniknąć dużych kolekcjach modyfikowalne.

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
    {"id": "1000","name": "Deep Dive in to Azure Cosmos DB", "pub-id": "mspress"}

W powyższym przykładzie mamy porzucona niepowiązany kolekcji w dokumencie wydawcy. Zamiast tego po prostu mamy odwołanie do wydawcy na każdy dokument książki.

### <a name="how-do-i-model-manymany-relationships"></a>Jak model relacje wiele: wiele?
Relacyjnej bazy danych *wiele: wiele* relacje są często modelowane sprzężenie tabel, które właśnie ze sobą połączone rekordy z innych tabel. 

![Dołącz do tabel](./media/sql-api-modeling-data/join-table.png)

Może się wydawać do replikowania samo używanie dokumentów oraz tworzenia modelu danych, która wygląda podobnie do następującego.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive in to Azure Cosmos DB" }

    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Czy to pomoże. Jednak podczas ładowania albo autor z ich książki lub ładowania książkę z jego autorem zawsze wymagają co najmniej dwóch dodatkowych zapytań w bazie danych. Jedno zapytanie do łącząca dokumentu, a następnie inne zapytanie, aby pobrać rzeczywiste dokumentu jest dołączony. 

Jeśli jest wszystkich zadań w tej tabeli sprzężenia przyklejanie ze sobą dwie części danych, dlaczego nie upuszczenie go całkowicie?
Rozważ następujące opcje.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents: 
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to Azure Cosmos DB", "authors": ["a2"]}

Teraz gdyby Autor, niezwłocznie sprawdzić książki zostały one zapisane i odwrotnie gdyby załadować dokument książki I wiedział, identyfikatory autora lub autorów. Spowoduje to zapisanie tej pośredniczące zapytanie sprzężenia zmniejszenie tabeli numer serwera przekazywanych aplikacja ma być. 

## <a id="WrapUp"></a>Modele hybrydowe danych
Teraz analizujemy zostały osadzanie (lub denormalizing) i danych odwołującego się (lub normalizacji), o ich upsides, a każdy mieć dokonywania jako zaobserwowano. 

Nie zawsze musi być lub nie można Przerażony do nieco pomylone rzeczy. 

Na podstawie wzorców użycia określonych aplikacji i obciążeń, które może się zdarzyć, gdy mieszanie osadzonych i danych występujące w odwołaniu ma sens, i może prowadzić do prostsze logiki aplikacji z serwerem mniej przekazywanych zachowując dobrej poziomu wydajności.

Należy wziąć pod uwagę następujące JSON. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",        
        "countOfBooks": 3,
         "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

W tym miejscu zostały (przeważnie) była osadzonego modelu, gdy dane z innych jednostek są osadzone w dokumencie najwyższego poziomu, ale odwołuje się do innych danych. 

W dokumencie Podręcznik, firma Microsoft może zobaczyć kilka interesujące pola, jeśli przyjrzymy się tablicy autorów. Brak *identyfikator* pola, które jest polem używamy odwołują się do dokumentu autora standardową praktyką w modelu znormalizowane, ale następnie mamy także *nazwa* i *thumbnailUrl*. Firma Microsoft może po prostu wciąż z *identyfikator* i aplikacji w celu uzyskania ono potrzebne informacje dodatkowe z odpowiednich autora dokumentu za pomocą "link", ale ponieważ naszej aplikacji jest wyświetlana nazwa autora i obraz z każdym książką wyświetlane można zapisać podróż serwer na książki na liście przez denormalizing **niektórych** danych od autora.

Na pewno zmienić imię i nazwisko autora lub chcieli zaktualizować ich fotografii, firma Microsoft będzie musiał przejść aktualizacji co książki one kiedykolwiek opublikowany, ale dla aplikacji, na podstawie założenia, że autorów nie zmieniaj nazwy bardzo często jest to dopuszczalne decyzję.  

W tym przykładzie są **wstępnie obliczone wartości zagregowanych** wartości do zapisania kosztowne przetwarzania na operację odczytu. W tym przykładzie dane osadzony w dokumencie autora jest dane, które jest obliczane w czasie wykonywania. Nowej książki zostanie opublikowana, tworzony jest dokument książki **i** pola countOfBooks ma ustawioną wartość obliczana na podstawie liczby dokumentów książki, które istnieją dla konkretnego autora. Tego rodzaju optymalizacji byłoby dobrej w systemach duże odczytu gdzie możemy akceptowalny wykonać obliczenia na zapisy w celu zoptymalizowania odczytów.

Możliwość modelu z pól obliczeniowych wstępnie jest możliwe ponieważ obsługuje bazy danych Azure rozwiązania Cosmos **transakcji wielu dokumentów**. Wiele magazynów NoSQL nie nie transakcji w dokumentach i w związku z tym wspierają decyzje dotyczące projektu, takie jak "zawsze osadzić wszystko", z powodu tego ograniczenia. Z bazy danych rozwiązania Cosmos platformy Azure można użyć wyzwalacze po stronie serwera lub procedur składowanych, które Wstaw książki i zaktualizuj autorów wszystko w ramach transakcji ACID. Teraz nie zostanie **ma** do osadzenia wszystkie elementy z jednym dokumentem tak, aby mieć pewność, spójność danych.

## <a name="NextSteps"></a>Następne kroki
Największych takeaways z tego artykułu jest zrozumienie, że modelowania w świecie bez schematu danych jest równie ważne co kiedykolwiek. 

Podobnie jak nie istnieje jeden sposób reprezentujący element danych na ekranie, nie istnieje jeden sposób do modelu danych. Możesz muszą zrozumieć i aplikacji, jak powoduje wygenerowanie, korzystać z, a przetwarzania danych. Następnie przez zastosowanie niektórych wytycznych przedstawionych w tym miejscu można ustawić o tworzeniu modelu, który dotyczy natychmiastowego wymagań aplikacji. Jeśli aplikacje potrzebne zmiany, można wykorzystać elastyczność bez schematu bazy danych obejmuje zmiany i łatwo rozwijać modelu danych. 

Aby dowiedzieć się więcej o usłudze Azure DB rozwiązania Cosmos, należy zapoznać się z usługi [dokumentacji](https://azure.microsoft.com/documentation/services/cosmos-db/) strony. 

Aby zrozumieć, jak do niezależnego fragmentu danych między wieloma partycjami się [partycjonowanie danych w usłudze Azure DB rozwiązania Cosmos](sql-api-partition-data.md). 
