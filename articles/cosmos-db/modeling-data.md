---
title: Modelowanie danych w Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Więcej informacji na temat modelowanie danych w bazach danych NoSQL, różnice między modelowania danych w relacyjnej bazie danych i bazy danych dokumentów.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: da119b2858c6b6c7bbc99b40d340f79964e0fae3
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467897"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelowanie danych w Azure Cosmos DB

Bazy danych bez schematu, takie jak Azure Cosmos DB, ułatwiają przechowywanie i wykonywanie zapytań dotyczących danych z nieprawidłową strukturą i z częściową strukturą, należy poświęcać trochę czasu na zaplanowanie modelu danych w celu uzyskania optymalnej wydajności i skalowalności oraz najniższych MPK.

Jak będzie dane mają być przechowywane Jak będzie aplikację do pobierania i wykonywania zapytań o dane Czy aplikacja jest odczytana i intensywnie zapisu?

Po przeczytaniu tego artykułu, możliwe będzie odpowiadać na następujące pytania:

* Co to jest modelowanie danych i dlaczego należy się?
* Jak dane modelowania w Azure Cosmos DB różnić się do relacyjnej bazy danych?
* Jak express relacji między danymi w bazie danych nierelacyjnych?
* Gdy osadzone dane i kiedy utworzyć połączenie danych?

## <a name="embedding-data"></a>Trwa osadzanie danych

Po rozpoczęciu modelowania danych w Azure Cosmos DB spróbuj traktować swoje jednostki jako **elementy** samodzielne reprezentowane jako dokumenty JSON.

Na potrzeby porównania najpierw zobaczymy, jak możemy modelować dane w relacyjnej bazie danych. Poniższy przykład pokazuje, jak osoby mogą być przechowywane w relacyjnej bazie danych.

![Relacyjna baza danych modelu](./media/sql-api-modeling-data/relational-data-model.png)

W przypadku pracy z relacyjnymi bazami danych strategia ma na celu normalizację wszystkich danych. Normalizacja danych zazwyczaj obejmuje przejęcie jednostki, takiej jak osoba, i przedzielenie jej na składniki dyskretne. W powyższym przykładzie osoba może mieć wiele rekordów szczegółów kontaktu, a także wiele rekordów adresów. Szczegóły kontaktu można podzielić na dalsze wyodrębnianie wspólnych pól, takich jak typ. To samo dotyczy adresu, każdy rekord może być typu *dom* lub *Business*.

Przeprowadzi lokalnego po normalizowanie danych do **uniknąć przechowywania nadmiarowych danych** na każdym rejestrowania i raczej odnosi się do danych. W tym przykładzie w celu odczytania osoby z uwzględnieniem wszystkich informacji kontaktowych i adresów należy użyć sprzężeń, aby efektywnie redagować (lub denormalizować) dane w czasie wykonywania.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Aktualizowanie jedna osoba wraz z ich szczegółowe dane kontaktowe i adresami wymaga operacje zapisu na wiele poszczególnych tabel.

Teraz przyjrzyjmy się sposobom, w jaki będziemy modelować te same dane jak samodzielna jednostka w Azure Cosmos DB.

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

Korzystając z powyższego podejścia  , odnosząc się do rekordu osoby  , osadzając wszystkie informacje dotyczące tej osoby, takie jak informacje kontaktowe i adresy, w *jednym dokumencie JSON* .
Ponadto ponieważ firma Microsoft nie są ograniczone do stałego schematu mamy elastyczność nadanie całkowicie konieczności skontaktowania się z różnych kształtów.

Pobranie kompletnego rekordu osoby z bazy danych jest teraz **jedną operacją odczytu** dla jednego kontenera i dla pojedynczego elementu. Aktualizacja rekordu osoby przy użyciu ich szczegółów i adresów kontaktowych jest również **jedną operacją zapisu** w odniesieniu do pojedynczego elementu.

Denormalizing danych, aplikacja może być konieczne wystawiać mniejszej liczby zapytań i aktualizacji do wykonania typowych operacji.

### <a name="when-to-embed"></a>Kiedy należy osadzić

Ogólnie rzecz biorąc, używane osadzone dane modeli, gdy:

* Istnieją relacje **między** jednostkami.
* Istnieją **jeden do kilka** relacje między jednostkami.
* Brak danych osadzonych, **zmieniają się rzadko**.
* Istnieją osadzone dane, które nie zostaną powiększone **bez powiązania**.
* Istnieje osadzona Data, do której często wykonywane są **zapytania**.

> [!NOTE]
> Zwykle nieznormalizowane danych modele oferują lepsze **odczytu** wydajności.

### <a name="when-not-to-embed"></a>Kiedy nie należy osadzać

Chociaż reguła kciuka w Azure Cosmos DB ma denormalizować wszystko i osadzić wszystkie dane w jednym elemencie, może to prowadzić do pewnych sytuacji, które należy unikać.

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

Może to być jak jednostka wpis z komentarzami osadzone będzie wyglądać Jeśli możemy zostały modelowania typowe blogu lub CMS system. Problem z tym przykładem jest tablica komentarze **niepowiązane**, co oznacza, że jest nieograniczona (praktyczne) liczba komentarzy, które mogą mieć dowolnego pojedynczego wpisu. Może to stać się problemem, ponieważ rozmiar elementu może wzrosnąć nieskończonie.

Ponieważ rozmiar elementu zwiększa możliwość przesyłania danych za pośrednictwem sieci, a także odczytywanie i aktualizowanie elementu, na dużą skalę.

W takim przypadku lepszym rozwiązaniem jest uwzględnienie poniższego modelu danych.

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

Ten model ma trzy ostatnie komentarze osadzone w kontenerze post, który jest tablicą ze stałym zestawem atrybutów. Inne Komentarze są pogrupowane w do partii 100 komentarzy i przechowywane jako oddzielne elementy. Rozmiar partii została wybrana jako 100, ponieważ naszej fikcyjnej aplikacji umożliwia użytkownikowi załadować 100 komentarzy naraz.  

Innym przypadkiem, gdy osadzenie danych nie jest dobrym pomysłem, jest to, że osadzone dane są często używane między elementami i zmieniają się często.

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

To może reprezentować portfel akcji osoby. Wybrano osadzenie informacji o zapasach w każdym dokumencie portfolio. W środowisku, w których powiązane dane zmieniają się często takich jak stock handlowymi aplikacji, osadzanie danych, która zmienia się często będzie oznaczać, że stale aktualizujesz każdy dokument portfolio za każdym razem, gdy zasobu jest przedmiotem handlu.

Stock *zaza* może być przedmiotem obrotu wiele setki razy w jednym dnia i tysiące użytkowników może mieć *zaza* ich portfolio. W przypadku modelu danych, takich jak powyżej firma Microsoft musi zaktualizować wielu tysięcy dokumentów portfolio wiele razy codziennie, co prowadzi do systemu, nie będzie skalują się dobrze.

## <a name="referencing-data"></a>Odwołujące się do danych

Osadzanie danych działa dobrze w wielu przypadkach, ale istnieją scenariusze, w których denormalizacja danych spowoduje więcej problemów niż jest to możliwe. Dlatego co możemy zrobić teraz?

Relacyjne bazy danych nie są jedynym miejscem, w którym można utworzyć relacji między jednostkami. W bazie danych dokumentów można zawierać informacje w jednym dokumencie, które odnoszą się do danych w innych dokumentach. Nie zaleca się kompilowania systemów, które lepiej nadają się do relacyjnej bazy danych w Azure Cosmos DB lub innej bazy danych dokumentów, ale proste relacje są bardziej przydatne i mogą być użyteczne.

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

Teraz, jeśli mam autora, natychmiast wiemy, które książki zostały napisane, i odwrotnie, jeśli mam załadowana dokument książki, znamy identyfikatory autorów. Spowoduje to zapisanie tej pośrednie zapytania względem zmniejszenie sprzężenie tabeli, liczba serwerów rund, którą aplikacja ma się.

## <a name="hybrid-data-models"></a>Hybrydowe modele danych

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

Jeśli przyjrzymy się dokument książki, widać kilka interesujących pola, gdy spojrzymy na tablicy autorów. Istnieje pole, które jest używane do odwoływania się z powrotem do dokumentu autora, standardowej zwyczajowej w znormalizowanym modelu, ale `name` również mamy i `thumbnailUrl`. `id` Firma Microsoft mogła zostać `id` zablokowana i pozostawiona aplikacji, aby uzyskać dodatkowe informacje potrzebne z odpowiedniego autora dokumentu przy użyciu linku, ale ponieważ nasza aplikacja wyświetla nazwę autora i miniaturę obrazu z każdą książką wyświetlone dane można zapisać w postaci okrężnej na serwerze dla każdej książki na liście przez denormalizację **niektórych** danych od autora.

Upewnij się, że jeśli nazwa autora została zmieniona lub chcesz zaktualizować swoje zdjęcie, musimy przeszedł i zaktualizować każdą opublikowaną przez siebie książkę, ale dla naszej aplikacji, w oparciu o założenie, że autorzy nie zmieniają nazw często, jest to akceptowalna decyzja w zakresie projektu.  

W tym przykładzie istnieją **wstępnie obliczone wartości zagregowanych** wartości można zapisać przetwarzania kosztownych operacji odczytu. W tym przykładzie dane osadzone w dokumencie Autor to dane, które jest obliczane w czasie wykonywania. Za każdym razem, gdy zostanie opublikowany nowy książki, tworzony jest dokument książki **i** pole countOfBooks jest ustawione na wartość obliczona na podstawie liczby dokumentów książki, które istnieją dla danego autora. Tego rodzaju optymalizacji może być dobrym odczytu dużych systemów, gdzie możemy pozwolić sobie w obliczeniach na operacje zapisu w celu zoptymalizowania Odczyt.

Możliwość dysponowania model przy użyciu wstępnie obliczonych pól jest możliwe, że usługa Azure Cosmos DB obsługuje **transakcji obejmujących wiele dokumentów**. Wiele magazynów NoSQL nie można wykonać transakcje w dokumentach i w związku z tym Ambasador decyzje dotyczące projektu, takich jak "zawsze osadzić wszystko", ze względu na to ograniczenie. Za pomocą usługi Azure Cosmos DB można użyć po stronie serwera wyzwalaczy ani procedur składowanych, które wstawiania książki i aktualizowania autorzy wszystko to w ramach transakcji ACID. Teraz nie **trzeba** osadzać wszystkiego w jednym dokumencie, aby upewnić się, że dane pozostają spójne.

## <a name="distinguishing-between-different-document-types"></a>Rozróżnianie między różnymi typami dokumentów

W niektórych scenariuszach warto mieszać różne typy dokumentów w tej samej kolekcji. zwykle jest to przypadek, gdy wiele powiązanych dokumentów ma być w tej samej [partycji](partitioning-overview.md). Można na przykład umieścić zarówno książki, jak i książki, w tej samej kolekcji, a następnie podzielić `bookId`je na partycje. W takiej sytuacji zazwyczaj warto dodać do dokumentów za pomocą pola, które identyfikuje ich typ w celu odróżnienia ich.

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

Aby dowiedzieć się, jak modelować i dzielić dane na Azure Cosmos DB przy użyciu rzeczywistego przykładu, zobacz [modelowanie i partycjonowanie danych — rzeczywisty przykład](how-to-model-partition-example.md).
