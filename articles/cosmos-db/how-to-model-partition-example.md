---
title: Dane modelu i partycji w usłudze Azure Cosmos DB z przykładem ze świata rzeczywistego
description: Dowiedz się, jak modelować i partycjonować przykład rzeczywistych przy użyciu interfejsu API rdzenia usługi Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 10f8ffd90215a21ca03e112aea463d444c623d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445389"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Jak modelować i partycjonować dane w usłudze Azure Cosmos DB przy użyciu przykładu wziętego z życia

W tym artykule opiera się na kilku pojęciach usługi Azure Cosmos DB, takich jak [modelowanie danych,](modeling-data.md) [partycjonowanie](partitioning-overview.md)i [aprowizowana przepływność,](request-units.md) aby zademonstrować, jak rozwiązać problem projektowania danych w świecie rzeczywistym.

Jeśli zwykle pracujesz z relacyjnych baz danych, prawdopodobnie zostały utworzone nawyki i intuicje na temat sposobu projektowania modelu danych. Ze względu na określone ograniczenia, ale także unikatowe zalety usługi Azure Cosmos DB, większość z tych najlepszych rozwiązań nie przekłada się dobrze i może przeciągnąć cię do nieoptymalnych rozwiązań. Celem tego artykułu jest, aby poprowadzić Cię przez cały proces modelowania rzeczywistych przypadków użycia w usłudze Azure Cosmos DB, od modelowania elementów do kolokacji jednostek i partycjonowania kontenera.

## <a name="the-scenario"></a>Scenariusz

W tym ćwiczeniu rozważymy domenę platformy blogowej, na której *użytkownicy* mogą tworzyć *posty.* Użytkownicy mogą również *lubić* i dodawać *komentarze* do tych postów.

> [!TIP]
> Wyróżniliśmy kilka słów *kursywą*; te słowa identyfikują rodzaj "rzeczy" nasz model będzie musiał manipulować.

Dodanie kolejnych wymagań do naszej specyfikacji:

- Na pierwszej stronie jest wyświetlany kanał ostatnio utworzonych postów,
- Możemy pobrać wszystkie posty dla użytkownika, wszystkie komentarze do posta i wszystkie polubienia dla posta,
- Posty są zwracane z nazwą użytkownika ich autorów i liczyć, ile komentarzy i polubień mają,
- Komentarze i polubienia są również zwracane z nazwą użytkownika użytkowników, którzy je stworzyli,
- Gdy są wyświetlane jako listy, posty muszą jedynie przedstawiać skrócone podsumowanie ich zawartości.

## <a name="identify-the-main-access-patterns"></a>Identyfikowanie głównych wzorców dostępu

Na początek nadajemy pewną strukturę naszej wstępnej specyfikacji, identyfikując wzorce dostępu naszego rozwiązania. Podczas projektowania modelu danych dla usługi Azure Cosmos DB, ważne jest, aby zrozumieć, które żądania naszego modelu będą musiały służyć, aby upewnić się, że model będzie skutecznie obsługiwać te żądania.

Aby ułatwić ogólny proces do naśladowania, kategoryzujemy te różne żądania jako polecenia lub kwerendy, pożyczając niektóre słownictwo z [CQRS,](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) gdzie polecenia są żądania zapisu (czyli intencje aktualizacji systemu) i kwerendy są żądaniami tylko do odczytu.

Oto lista żądań, które nasza platforma będzie musiała ujawnić:

- **[C1]** Tworzenie/edytowanie użytkownika
- **[Q1]** Pobieranie użytkownika
- **[C2]** Tworzenie/edytowanie wpisu
- **[II kw.]** Pobieranie wpisu
- **[Q3]** Wyświetlanie wpisów użytkownika w krótkiej formie
- **[C3]** Tworzenie komentarza
- **[Q4]** Wyświetlanie komentarzy do posta
- **[C4]** Jak post
- **[Q5]** Wyświetlanie polubień posta
- **[Q6]** Lista *x* najnowszych postów utworzonych w formie krótkiej (kanał)

Na tym etapie nie pomyśleliśmy o szczegółach tego, co będzie zawierać każda jednostka (użytkownik, post itp.). Ten krok jest zwykle jednym z pierwszych, które należy rozwiązać podczas projektowania przeciwko relacyjnego sklepu, ponieważ musimy dowiedzieć się, jak te podmioty będą tłumaczyć pod względem tabel, kolumn, kluczy obcych itp. Jest znacznie mniej obawy z bazy danych dokumentów, które nie wymusza żadnego schematu w zapisie.

Głównym powodem, dla którego ważne jest, aby zidentyfikować nasze wzorce dostępu od początku, jest to, że ta lista żądań będzie naszym zestawem testów. Za każdym razem, gdy iterujemy nasz model danych, przejdziemy przez każde z żądań i sprawdzimy jego wydajność i skalowalność.

## <a name="v1-a-first-version"></a>V1: Pierwsza wersja

Zaczynamy od dwóch `users` `posts`pojemników: i .

### <a name="users-container"></a>Kontener Użytkownicy

Ten kontener przechowuje tylko elementy użytkownika:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Dzielimy ten `id`kontener według , co oznacza, że każda partycja logiczna w tym kontenerze będzie zawierać tylko jeden element.

### <a name="posts-container"></a>Kontener słupków

Ten kontener obsługuje posty, komentarze i polubienia:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "title": "<post-title>",
      "content": "<post-content>",
      "creationDate": "<post-creation-date>"
    }

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "creationDate": "<like-creation-date>"
    }

Dzielimy ten `postId`kontener według , co oznacza, że każda partycja logiczna w tym kontenerze będzie zawierać jeden post, wszystkie komentarze do tego postu i wszystkie polubienia dla tego postu.

Należy zauważyć, że `type` wprowadziliśmy właściwość w elementach przechowywanych w tym kontenerze, aby odróżnić trzy typy jednostek, które hostuje ten kontener.

Ponadto wybraliśmy odwołanie się do powiązanych danych zamiast osadzać je (sprawdź [tę sekcję,](modeling-data.md) aby uzyskać szczegółowe informacje na temat tych pojęć), ponieważ:

- nie ma górnego limitu liczby postów, które użytkownik może utworzyć,
- stanowiska mogą być dowolnie długie,
- nie ma górnego limitu liczby komentarzy i polubień posta,
- chcemy mieć możliwość dodania komentarza lub polu znienajścia do posta bez konieczności aktualizowania samego posta.

## <a name="how-well-does-our-model-perform"></a>Jak dobrze radzi sobie nasz model?

Nadszedł czas, aby ocenić wydajność i skalowalność naszej pierwszej wersji. Dla każdego z wcześniej zidentyfikowanych żądań mierzymy jego opóźnienie i liczbę jednostek żądań, z których korzysta. Ten pomiar jest wykonywany na fikcyjnym zestawie danych zawierającym 100 000 użytkowników z 5 do 50 postów na użytkownika oraz do 25 komentarzy i 100 polubień na post.

### <a name="c1-createedit-a-user"></a>[C1] Tworzenie/edytowanie użytkownika

To żądanie jest proste do zaimplementowania, `users` jak po prostu utworzyć lub zaktualizować element w kontenerze. Żądania będą ładnie rozłożone na wszystkich `id` partycjach dzięki kluczowi partycji.

![Zapisywanie pojedynczego elementu w kontenerze użytkowników](./media/how-to-model-partition-example/V1-C1.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 7 ms | 5.71 ru | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Pobieranie użytkownika

Pobieranie użytkownika odbywa się przez odczytanie `users` odpowiedniego elementu z kontenera.

![Pobieranie pojedynczego elementu z kontenera użytkowników](./media/how-to-model-partition-example/V1-Q1.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 2 ms | 1 ru | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Tworzenie/edytowanie wpisu

Podobnie jak **[C1]**, musimy tylko `posts` napisać do kontenera.

![Zapisywanie pojedynczego elementu w kontenerze wpisów](./media/how-to-model-partition-example/V1-C2.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 9 ms. | 8,76 ru | ✅ |

### <a name="q2-retrieve-a-post"></a>[II kw.] Pobieranie wpisu

Zaczynamy od pobrania odpowiedniego dokumentu `posts` z kontenera. Ale to nie wystarczy, zgodnie z naszą specyfikacją musimy również agregować nazwę użytkownika autora posta i liczbę komentarzy i liczby polubień tego posta, co wymaga wydania 3 dodatkowych zapytań SQL.

![Pobieranie wpisu i agregowanie dodatkowych danych](./media/how-to-model-partition-example/V1-Q2.png)

Każdy z dodatkowych filtrów zapytań na klucz partycji odpowiedniego kontenera, który jest dokładnie to, co chcemy zmaksymalizować wydajność i skalowalność. Ale w końcu musimy wykonać cztery operacje, aby zwrócić pojedynczy post, więc poprawimy to w następnej iteracji.

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 9 ms. | 19.54 ru. | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Wyświetlanie wpisów użytkownika w krótkiej formie

Najpierw musimy pobrać żądane wpisy z zapytaniem SQL, które pobiera wpisy odpowiadające określonemu użytkownikowi. Ale musimy również wydać dodatkowe zapytania, aby agregować nazwę użytkownika autora i liczbę komentarzy i polubień.

![Pobieranie wszystkich wpisów dla użytkownika i agregowanie jego dodatkowych danych](./media/how-to-model-partition-example/V1-Q3.png)

Ta implementacja przedstawia wiele wad:

- zapytania agregujące liczbę komentarzy i polubień muszą być wystawione dla każdego posta zwróconego przez pierwsze zapytanie,
- kwerenda główna nie filtruje klucza `posts` partycji kontenera, co prowadzi do przeskanowania wentylatora i partycji w kontenerze.

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 130 ms | 619.41 ru | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Tworzenie komentarza

Komentarz jest tworzony przez zapisanie `posts` odpowiedniego elementu w kontenerze.

![Zapisywanie pojedynczego elementu w kontenerze wpisów](./media/how-to-model-partition-example/V1-C2.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Wyświetlanie komentarzy do posta

Zaczynamy od zapytania, które pobiera wszystkie komentarze do tego postu i po raz kolejny musimy również zbiorczo nazwy użytkowników oddzielnie dla każdego komentarza.

![Pobieranie wszystkich komentarzy do posta i agregowanie ich dodatkowych danych](./media/how-to-model-partition-example/V1-Q4.png)

Mimo że kwerenda główna nie filtruje klucz partycji kontenera, agregowanie nazw użytkowników oddzielnie penalizuje ogólną wydajność. Poprawimy to później.

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 23 ms. | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Jak post

Podobnie jak **[C3]**, tworzymy `posts` odpowiedni element w kontenerze.

![Zapisywanie pojedynczego elementu w kontenerze wpisów](./media/how-to-model-partition-example/V1-C2.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Wyświetlanie polubień posta

Podobnie jak **[Q4]**, zapytania lubi dla tego postu, a następnie agregować ich nazwy użytkowników.

![Pobieranie wszystkich polubień posta i agregowanie ich dodatkowych danych](./media/how-to-model-partition-example/V1-Q5.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 59 ms. | 58,92 ru | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista x najnowszych postów utworzonych w formie krótkiej (kanał)

Pobieramy najnowsze posty, odwołując się do `posts` kontenera posortowanego według malejącej daty utworzenia, a następnie agregujemy nazwy użytkowników i liczby komentarzy i polubień dla każdego z postów.

![Pobieranie najnowszych wpisów i agregowanie ich dodatkowych danych](./media/how-to-model-partition-example/V1-Q6.png)

Po raz kolejny nasze początkowe zapytanie nie `posts` filtruje klucza partycji kontenera, co powoduje kosztowne wyjście z wentylatora. Ten jest jeszcze gorszy, ponieważ kierujemy się na znacznie `ORDER BY` większy zestaw wyników i sortujemy wyniki za pomocą klauzuli, co czyni go droższym pod względem jednostek żądań.

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 306 ms. | 2063,54 ru. | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Refleksja nad wydajnością V1

Patrząc na problemy z wydajnością, z którymi mieliśmy do czynienia w poprzedniej sekcji, możemy zidentyfikować dwie główne klasy problemów:

- niektóre żądania wymagają wydania wielu zapytań w celu zebrania wszystkich danych, które musimy zwrócić,
- Niektóre zapytania nie filtrują klucza partycji kontenerów, które są kierowane, co prowadzi do fan-out, który utrudnia naszą skalowalność.

Rozwiążmy każdy z tych problemów, zaczynając od pierwszego.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Wprowadzenie denormalizacji w celu optymalizacji zapytań odczytu

Powodem, dla którego musimy wystawiać dodatkowe żądania w niektórych przypadkach, jest to, że wyniki początkowego żądania nie zawierają wszystkich danych, które musimy zwrócić. Podczas pracy z nierelacyjnego magazynu danych, takich jak Usługi Azure Cosmos DB, tego rodzaju problem jest często rozwiązywane przez denormalizing danych w naszym zestawie danych.

W naszym przykładzie modyfikujemy elementy postu, aby dodać nazwę użytkownika autora postu, liczbę komentarzy i liczbę polubień:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Modyfikujemy również komentarz i polu jak elementy, aby dodać nazwę użytkownika, który je utworzył:

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "userUsername": "<comment-author-username>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "userUsername": "<liker-username>",
      "creationDate": "<like-creation-date>"
    }

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizing komentarz i jak liczy

Co chcemy osiągnąć jest to, że za każdym razem, gdy `commentCount` dodajemy `likeCount` komentarz lub podobne, możemy również zwiększyć lub w odpowiednim poście. Ponieważ `posts` nasz kontener jest `postId`podzielony na partycje przez , nowy element (komentarz lub jak) i jego odpowiedni post siedzieć w tej samej partycji logicznej. W rezultacie możemy użyć [procedury składowanej](stored-procedures-triggers-udfs.md) do wykonania tej operacji.

Teraz podczas tworzenia komentarza (**[C3]**), zamiast po prostu `posts` dodając nowy element w kontenerze, wywołujemy następującą procedurę składowaną w tym kontenerze:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Ta procedura składowana przyjmuje identyfikator postu i treść nowego komentarza jako parametry, a następnie:

- pobiera post
- zwiększa`commentCount`
- zastępuje post
- dodaje nowy komentarz

Ponieważ procedury przechowywane są wykonywane jako transakcje niepodzielne, jest gwarantowane, że wartość `commentCount` i rzeczywista liczba komentarzy zawsze pozostanie zsynchronizowana.

Oczywiście nazywamy podobną procedurę składowaną podczas dodawania `likeCount`nowych lubi zwiększać .

### <a name="denormalizing-usernames"></a>Denormalizacja nazw użytkowników

Nazwy użytkowników wymagają innego podejścia, ponieważ użytkownicy nie tylko siedzą w różnych partycjach, ale w innym kontenerze. Gdy musimy zdenormalować dane między partycjami i kontenerami, możemy użyć źródła kanału informacyjnego zmiany kontenera [źródłowego.](change-feed.md)

W naszym przykładzie używamy pliku `users` danych o zmianie kontenera, aby reagować za każdym razem, gdy użytkownicy aktualizują swoje nazwy użytkowników. W takim przypadku możemy propagować zmiany, wywołując `posts` inną procedurę składowaną w kontenerze:

![Denormalizing nazwy użytkowników w kontenerze postów](./media/how-to-model-partition-example/denormalization-1.png)

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Ta procedura składowana przyjmuje identyfikator użytkownika i nową nazwę użytkownika użytkownika jako parametry, a następnie:

- pobiera wszystkie elementy pasujące `userId` do (które mogą być posty, komentarze, lub lubi)
- dla każdego z tych elementów
  - zastępuje`userUsername`
  - zastępuje przedmiot

> [!IMPORTANT]
> Ta operacja jest kosztowne, ponieważ wymaga tej procedury `posts` składowanej, które mają być wykonywane na każdej partycji kontenera. Zakładamy, że większość użytkowników wybiera odpowiednią nazwę użytkownika podczas rejestracji i nigdy jej nie zmieni, więc ta aktualizacja będzie działać bardzo rzadko.

## <a name="what-are-the-performance-gains-of-v2"></a>Jakie są przyrosty wydajności V2?

### <a name="q2-retrieve-a-post"></a>[II kw.] Pobieranie wpisu

Teraz, gdy nasza denormalizacja jest na miejscu, musimy tylko pobrać jeden element do obsługi tego żądania.

![Pobieranie pojedynczego elementu z kontenera słupków](./media/how-to-model-partition-example/V2-Q2.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 2 ms | 1 ru | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Wyświetlanie komentarzy do posta

W tym miejscu ponownie możemy oszczędzić dodatkowe żądania, które pobrane nazwy użytkowników i kończy się z jednej kwerendy, która filtruje na klucz partycji.

![Pobieranie wszystkich komentarzy do posta](./media/how-to-model-partition-example/V2-Q4.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 4 ms | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Wyświetlanie polubień posta

Dokładnie taka sama sytuacja podczas wystawiania polubień.

![Pobieranie wszystkich polubień posta](./media/how-to-model-partition-example/V2-Q5.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 4 ms | 8,92 ru | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Upewnianie się, że wszystkie żądania są skalowalne

Patrząc na naszą ogólną poprawę wydajności, nadal istnieją dwa żądania, które nie zostały w pełni zoptymalizowane: **[Q3]** i **[Q6]**. Są to żądania obejmujące kwerendy, które nie filtrują klucza partycji kontenerów, które są kierowane.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Wyświetlanie wpisów użytkownika w krótkiej formie

To żądanie już korzysta z ulepszeń wprowadzonych w wersji 2, co oszczędza dodatkowe zapytania.

![Pobieranie wszystkich wpisów dla użytkownika](./media/how-to-model-partition-example/V2-Q3.png)

Ale pozostałe zapytanie nadal nie filtruje klucza partycji kontenera. `posts`

Sposób myślenia o tej sytuacji jest rzeczywiście prosty:

1. To żądanie *musi* filtrować na `userId` ponieważ chcemy pobrać wszystkie posty dla konkretnego użytkownika
1. Nie działa dobrze, ponieważ jest wykonywany `posts` względem kontenera, który nie jest podzielony na partycje przez`userId`
1. Stwierdzając oczywiste, że rozwiązalibyśmy nasz problem z wydajnością, wykonując to żądanie względem kontenera, który *jest*`userId`
1. Okazuje się, że mamy już taki `users` pojemnik: pojemnik!

Więc wprowadzamy drugi poziom denormalizacji, powielając `users` całe słupki do kontenera. W ten sposób skutecznie otrzymujemy kopię naszych postów, tylko podzielonych na partycje wzdłuż `userId`różnych wymiarów, dzięki czemu są bardziej wydajne, aby odzyskać przez ich .

`users` Kontener zawiera teraz 2 rodzaje elementów:

    {
      "id": "<user-id>",
      "type": "user",
      "userId": "<user-id>",
      "username": "<username>"
    }

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Należy pamiętać, że:

- wprowadziliśmy pole `type` w elemencie użytkownika, aby odróżnić użytkowników od postów,
- dodaliśmy również `userId` pole w elemencie użytkownika, `id` które jest nadmiarowe `users` z polem, `userId` ale `id` jest wymagane, ponieważ kontener jest teraz podzielony na partycje przez (a nie tak jak poprzednio)

Aby osiągnąć tę denormalizację, ponownie używamy kanału zmian. Tym razem reagujemy na źródło `posts` danych kontenera, aby wysłać `users` dowolny nowy lub zaktualizowany wpis do kontenera. A ponieważ lista postów nie wymaga zwrotu pełnej zawartości, możemy je obciąć w procesie.

![Denormalizacja wpisów w kontenerze użytkowników](./media/how-to-model-partition-example/denormalization-2.png)

Możemy teraz kierować nasze `users` zapytanie do kontenera, filtrowanie klucza partycji kontenera.

![Pobieranie wszystkich wpisów dla użytkownika](./media/how-to-model-partition-example/V3-Q3.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 4 ms | 6.46 Ru | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista x najnowszych postów utworzonych w formie krótkiej (kanał)

Mamy do czynienia z podobną sytuacją tutaj: nawet po oszczędzaniu dodatkowych zapytań pozostawionych niepotrzebne przez denormalization wprowadzone w wersji 2, pozostałe zapytanie nie filtruje na klucz partycji kontenera:

![Pobieranie najnowszych wpisów](./media/how-to-model-partition-example/V2-Q6.png)

Zgodnie z tym samym podejściem maksymalizacji wydajności i skalowalności tego żądania wymaga, aby uderzył tylko w jedną partycję. Jest to możliwe, ponieważ musimy zwrócić tylko ograniczoną liczbę przedmiotów; w celu zapełnienia naszej platformy blogów stronie głównej, musimy tylko uzyskać 100 najnowszych postów, bez konieczności paginate przez cały zestaw danych.

Aby zoptymalizować to ostatnie żądanie, wprowadzamy do naszego projektu trzeci kontener, w całości przeznaczony do obsługi tego żądania. Denormalize nasze posty `feed` do tego nowego pojemnika:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Ten kontener jest `type`podzielony na partycje przez , który zawsze będzie `post` w naszych elementach. Dzięki temu wszystkie elementy w tym kontenerze będą siedzieć w tej samej partycji.

Aby osiągnąć denormalizacji, musimy po prostu podłączyć do rurociągu źródła zmian, które wcześniej wprowadziliśmy, aby wysłać słupki do tego nowego kontenera. Jedną z ważnych rzeczy, o których należy pamiętać, jest to, że musimy upewnić się, że przechowujemy tylko 100 najnowszych postów; w przeciwnym razie zawartość kontenera może rosnąć poza maksymalny rozmiar partycji. Odbywa się to przez wywołanie [post-trigger](stored-procedures-triggers-udfs.md#triggers) za każdym razem, gdy dokument jest dodawany w kontenerze:

![Denormalizacja słupków do pojemnika na paszę](./media/how-to-model-partition-example/denormalization-3.png)

Oto treść post-trigger, który obcina kolekcję:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Ostatnim krokiem jest przekierowanie naszego `feed` zapytania do naszego nowego kontenera:

![Pobieranie najnowszych wpisów](./media/how-to-model-partition-example/V3-Q6.png)

| **Opóźnienie** | **Opłata RU** | **Wydajność** |
| --- | --- | --- |
| 9 ms. | 16.97 RU | ✅ |

## <a name="conclusion"></a>Podsumowanie

Przyjrzyjmy się ogólnym ulepszeniom wydajności i skalowalności, które wprowadziliśmy w różnych wersjach naszego projektu.

| | Wersja 1 | Wersja 2 | Wersja V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5,71 RU | 7 ms / 5,71 RU | 7 ms / 5,71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]** | 9 ms / 8,76 RU | 9 ms / 8,76 RU | 9 ms / 8,76 RU |
| **[II kw.]** | 9 ms / 19,54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619,41 RU | 28 ms / 201.54 RU | 4 ms / 6,46 RU |
| **[C3]** | 7 ms / 8,57 RU | 7 ms / 15,27 RU | 7 ms / 15,27 RU |
| **[Q4]** | 23 ms / 27,72 RU | 4 ms / 7,72 RU | 4 ms / 7,72 RU |
| **[C4]** | 6 ms / 7,05 RU | 7 ms / 14,67 RU | 7 ms / 14,67 RU |
| **[Q5]** | 59 ms / 58,92 RU | 4 ms / 8,92 RU | 4 ms / 8,92 RU |
| **[Q6]** | 306 ms / 2063.54 RU | 83 ms / 532,33 RU | 9 ms / 16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Zoptymalizowaliśmy scenariusz

Być może zauważyliście, że skoncentrowaliśmy nasze wysiłki na poprawie wydajności żądań odczytu (zapytań) kosztem żądań zapisu (poleceń). W wielu przypadkach operacje zapisu wyzwalają teraz kolejne denormalizacji za pomocą źródeł danych zmian, co sprawia, że są one bardziej kosztowne pod względem obliczeniowym i dłużej się zmaterializują.

Jest to uzasadnione faktem, że platforma blogowa (podobnie jak większość aplikacji społecznościowych) jest intensywna odczytu, co oznacza, że ilość żądań odczytu, które musi obsługiwać, jest zwykle rzędu wielkości wyższe niż ilość żądań zapisu. Warto więc, aby żądania zapisu były droższe do wykonania, aby umożliwić tańsze i lepszą wydajność żądań odczytu.

Jeśli spojrzymy na najbardziej ekstremalną optymalizację, jaką zrobiliśmy, **[Q6]** przeszedł z ponad 2000 r., do zaledwie 17 procesorów; osiągnęliśmy to poprzez denormalizację słupków kosztem około 10 zł za sztukę. Ponieważ służylibyśmy o wiele więcej żądań paszy niż tworzenie lub aktualizowanie postów, koszt tej denormalizacji jest znikomy, biorąc pod uwagę ogólne oszczędności.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalizacja może być stosowana stopniowo

Ulepszenia skalowalności, które zostały zbadane w tym artykule obejmują denormalization i powielania danych w zestawie danych. Należy zauważyć, że optymalizacje te nie muszą być wprowadzone w dniu 1. Kwerendy, które filtrują klucze partycji działają lepiej na dużą skalę, ale zapytania między partycjami mogą być całkowicie dopuszczalne, jeśli są wywoływane rzadko lub względem ograniczonego zestawu danych. Jeśli dopiero budujesz prototyp lub wprowadzasz na rynek produkt z niewielką i kontrolowaną bazą użytkowników, prawdopodobnie możesz oszczędzić te ulepszenia na później; Ważne jest zatem [monitorowanie](use-metrics.md) wydajności modelu, aby można było zdecydować, czy i kiedy nadszedł czas, aby je wprowadzić.

Źródło danych ze zmian, których używamy do dystrybucji aktualizacji do innych kontenerów, przechowuje wszystkie te aktualizacje. Dzięki temu można zażądać wszystkich aktualizacji od czasu utworzenia kontenera i widoków zdenormalizowanej jako jednorazowej operacji nadrabiania zaległości, nawet jeśli system ma już dużo danych.

## <a name="next-steps"></a>Następne kroki

Po tym wprowadzeniu do praktycznego modelowania danych i partycjonowania warto zapoznać się z następującymi artykułami, aby przejrzeć pojęcia, które omówiliśmy:

- [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md)
- [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
- [Zmiana kanału informacyjnego w usłudze Azure Cosmos DB](change-feed.md)
