---
title: Jak model i partycji danych w usłudze Azure Cosmos DB przy użyciu przykładu rzeczywistych
description: Dowiedz się, jak model i partycji stanowi przykład rzeczywistych za pomocą usługi Azure Cosmos DB podstawowy interfejs API
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 3/27/2019
ms.author: thweiss
ms.openlocfilehash: ac1b94de4b439aab202d53b23b0d0da616a9f851
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58919616"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Jak model i partycji danych w usłudze Azure Cosmos DB przy użyciu przykładu rzeczywistych

W tym artykule opiera się na kilka koncepcji usługi Azure Cosmos DB, takie jak [modelowania danych](modeling-data.md), [partycjonowanie](partitioning-overview.md), i [aprowizowanej przepływności](request-units.md) aby zademonstrować czoła w rzeczywistych warunkach dane projektu wykonywania.

Jeśli zazwyczaj korzystasz z relacyjnych baz danych, dlatego prawdopodobnie utworzone nawyki i intuitions dotyczące projektowania modelu danych. Ze względu na szczególnych ograniczeń, ale również unikatowe zalety usługi Azure Cosmos DB większość z tych najlepszych rozwiązań nie wykonuje również i może przeciągać nieoptymalne rozwiązaniami. Celem tego artykułu jest przeprowadzenie Cię przez proces kompletnego procesu modelowania przypadek użycia rzeczywistych w usłudze Azure Cosmos DB, z poziomu elementu modelowania do wspólnej lokalizacji jednostki i partycjonowania kontenera.

## <a name="the-scenario"></a>Scenariusz

Na potrzeby tego ćwiczenia zamierzamy należy wziąć pod uwagę domeny platforma do obsługi blogów gdzie *użytkowników* można utworzyć *publikuje*. Użytkownicy mogą również *takich jak* i Dodaj *komentarze* do tych wpisów.

> [!TIP]
> Firma Microsoft ma wyróżnione niektórych słów w *italic*; te wyrazy zidentyfikowaniu rodzaju "rzeczy" nasz model będzie musiał manipulować.

Dodawanie dodatkowych wymagań naszej specyfikacji:

- Pierwszej strony wyświetli Kanał informacyjny ostatnio utworzone wpisy
- Firma Microsoft może pobrać wszystkie wpisy dla użytkownika, wszystkie komentarze na wpis i wszystkie polubienia dla wpisu,
- Wpisy są zwracane z nazwą użytkownika, ich autorów i liczba liczbę komentarzy i polubień mają one
- Komentarze i polubienia również są zwracane z nazwą użytkownika użytkowników, którzy je utworzyli
- Po wyświetleniu jako listy wpisów muszą tylko przedstawia skróconą streszczenie ich zawartości.

## <a name="identify-the-main-access-patterns"></a>Identyfikuj wzorce dostępu głównego

Aby rozpocząć, udostępniamy niektórych elementów struktury naszej początkowej specyfikacji identyfikując Nasze rozwiązanie wzorce dostępu. Projektowanie modelu danych usługi Azure Cosmos DB, jest ważne, aby zrozumieć, które żądania nasz model będzie musiał obsługiwać, aby upewnić się, że modelu będzie służyć efektywnie te żądania.

Aby ułatwić postępuj zgodnie z całego procesu, firma Microsoft automatycznej klasyfikacji tych innych żądań jako poleceń lub zapytania, finansowania zewnętrznego słownika, niektóre z [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) której polecenia są zapisu żądania (czyli intencje do aktualizowania systemu) i zapytania są tylko do odczytu żądań.

Poniżej przedstawiono listę żądań, które będą musieli udostępnienia naszej platformy:

- **[C1]**  Utwórz/Edytuj użytkownika
- **[1].**  Pobrać użytkownika
- **[C2]**  Utwórz/Edytuj wpis
- **[Q2]**  Pobieranie wpisu
- **[K3]**  Listy wpisów użytkownika w krótkich fragmentów
- **[C3]**  Tworzenia komentarza
- **KWARTAŁ [4]**  Listy publikowanie komentarzy
- **[C4]**  Wpis, takich jak
- **[Q5]**  Listy polubienia post
- **[Q6]**  Listy *x* najnowsze wpisy w skrócie utworzone formularza (źródło)

Jako ten etap nie jeszcze Pomyśleliśmy o szczegółach co będzie zawierać każdej jednostki (użytkownika, post itp.). Ten krok jest zazwyczaj między pierwszym tymi, które należy być rozwiązane podczas projektowania względem magazynu relacyjnego, ponieważ mamy zorientować się, jak te jednostki przekształci się pod względem tabel, kolumn, kluczy obcych itp. Jest znacznie mniejszym problemem z bazy danych dokumentów, które nie wymuszają żadnego schematu w zapisu.

Głównym powodem, dlaczego jest ważne, aby zidentyfikować naszych wzorce dostępu i od początku, jest, ponieważ ta lista żądań będzie to nasz zestaw testów. Za każdym razem, gdy firma Microsoft przejść przez nasz model danych, firma Microsoft każdemu wniosków i sprawdź jej wydajność i skalowalność.

## <a name="v1-a-first-version"></a>V1: Pierwsza wersja

Rozpoczniemy pracę dwóch kontenerów: `users` i `posts`.

### <a name="users-container"></a>Kontener Użytkownicy

Ten kontener przechowuje tylko elementy użytkownika:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Możemy podzielić ten kontener przez `id`, co oznacza, że każda partycja logiczna, w tym kontenerze będzie zawierać tylko jeden element.

### <a name="posts-container"></a>Kontener wpisów

Ten kontener obsługuje wpisów, komentarzy i polubień:

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

Możemy podzielić ten kontener przez `postId`, co oznacza, że każda partycja logiczna, w tym kontenerze będzie zawierać jeden wpis, wszystkie komentarze dotyczące tego wpisu i wszystkie polubienia dla tego wpisu.

Należy zauważyć, że firma Microsoft wprowadza `type` właściwości w elementach przechowywanych w tym kontenerze rozróżnienie między trzy rodzaje jednostek, że hosty kontenerów.

Ponadto Wybraliśmy odwołują się do powiązanych danych, zamiast go osadzić (Sprawdź [w tej sekcji](modeling-data.md) szczegółowe informacje na temat tych pojęć) ponieważ:

- nie ma żadnego limitu górnego, do ilu wpisów, które użytkownik może utworzyć,
- wpisy mogą być długi,
- nie ma żadnego limitu górnego, ile komentarzy i polubień wpis może być,
- chcemy można było dodać polubienie lub komentarz na stanowisko, bez konieczności aktualizowania urzędu.

## <a name="how-well-does-our-model-perform"></a>Jak dobrze wykonać nasz model

Teraz nadszedł czas, aby ocenić wydajność i skalowalność naszej pierwszej wersji. Dla każdego żądania wcześniej zidentyfikowane mierzymy jej opóźnienie i liczbę żądań zużywa jednostki. Ten pomiar odbywa się przed fikcyjnego zestaw danych zawierający 100 000 użytkowników w 5 do 50 wpisy na użytkownika i maksymalnie 25 komentarzy i polubień 100 na wpis.

### <a name="c1-createedit-a-user"></a>[C1] Utwórz/Edytuj użytkownika

To żądanie jest bardzo proste Implementowanie ponieważ możemy po prostu Utwórz lub zaktualizuj element `users` kontenera. Żądania będą dobrze rozkłada się na wszystkie partycje dzięki `id` klucza partycji.

![Zapisywanie pojedynczego elementu w kontenerze Użytkownicy](./media/how-to-model-partition-example/V1-C1.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[1]. Pobieranie użytkownika

Podczas pobierania użytkownika jest wykonywane, czytając odpowiedni element z `users` kontenera.

![Trwa pobieranie pojedynczego elementu z kontener użytkownicy](./media/how-to-model-partition-example/V1-Q1.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 2 ms | 1 JEDNOSTKA ŻĄDANIA | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Utwórz/Edytuj wpis

Podobnie jak **[C1]**, mamy po prostu zapisać `posts` kontenera.

![Zapisywanie pojedynczy element do kontenera wpisów](./media/how-to-model-partition-example/V1-C2.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] Pobieranie wpisu

Zaczniemy od pobierania odpowiedni dokument z `posts` kontenera. Ale nie jest wystarczająco dużo, zgodnie z naszym specyfikacji mamy także agregacji nazwa autora wpisu, i liczby liczbę komentarzy i liczbę polubień ten wpis ma, co wymaga 3 dodatkowe zapytania SQL zostanie wysłane.

![Pobieranie wpisu i agregowanie danych dodatkowych](./media/how-to-model-partition-example/V1-Q2.png)

Każda kwerenda dodatkowe filtry na klucz partycji odpowiednich kontenerów, to znaczy dokładnie, co chcemy zmaksymalizować wydajność i skalowalność. Ogranicza NAS jednak kwestia ostatecznie do wykonania czterech operacji, aby zwrócić pojedynczy wpis, dzięki czemu będzie można ulepszyć, w następnej iteracji.

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[K3] Listy wpisów użytkownika w krótkich fragmentów

Najpierw musimy pobrać żądaną wpisów z zapytania SQL, które pobiera wpisy odpowiadające dla tego konkretnego użytkownika. Ale możemy również mają do wysyłania zapytań dodatkowych agregacji użytkownika autora i liczbę komentarzy i polubień.

![Pobieranie wszystkie wpisy dla użytkownika i agregowanie ich dodatkowe dane](./media/how-to-model-partition-example/V1-Q3.png)

Ta implementacja przedstawia wiele wady:

- zapytania agregowanie liczby komentarzy i polubień musi być wystawiony dla każdego wpisu zwracane przez pierwsze zapytanie
- główne zapytanie nie można filtrować według klucza partycji z `posts` kontenera, co prowadzi do wielokierunkowego i skanowania partycji w kontenerze.

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 130 ms | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Utwórz komentarz

Komentarz jest tworzony przez napisanie odpowiadający mu element `posts` kontenera.

![Zapisywanie pojedynczy element do kontenera wpisów](./media/how-to-model-partition-example/V1-C2.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>KWARTAŁ [4] Lista publikowanie komentarzy

Rozpoczniemy pracę zapytania, które pobiera wszystkie komentarze dotyczące tego wpisu i jeszcze raz, musimy również agregacji nazw użytkowników osobno dla każdego komentarza.

![Pobieranie wszystkich komentarzy na wpis i agregowanie ich dodatkowe dane](./media/how-to-model-partition-example/V1-Q4.png)

Mimo że główne zapytanie filtrować kontenera klucza partycji, agregowania nazw użytkowników osobno penalizes ogólną wydajność. Firma Microsoft będzie poprawić, później.

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Podobnie jak wpis

Podobnie jak w przypadku **[C3]**, utworzymy odpowiadający mu element w `posts` kontenera.

![Zapisywanie pojedynczy element do kontenera wpisów](./media/how-to-model-partition-example/V1-C2.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Lista polubienia post

Podobnie jak w przypadku **[kwartale 4]**, firma Microsoft zapytania polubienia dla tego wpisu, a następnie agregowanie ich nazw użytkowników.

![Pobieranie wszystkich polubień wpis i agregowanie ich dodatkowe dane](./media/how-to-model-partition-example/V1-Q5.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 59 ms | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista x najnowsze wpisy utworzone w skrócie formularza (źródło)

Możemy pobrać najnowsze wpisy, badając `posts` kontenera, posortowane według malejących Data utworzenia, a następnie agregacji nazwy użytkowników i liczba komentarzy i polubień dla każdego z wpisy.

![Pobieranie najnowsze wpisy i agregowanie ich dodatkowe dane](./media/how-to-model-partition-example/V1-Q6.png)

Jeszcze raz naszych początkowego zapytania nie są filtrowane na klucz partycji `posts` kontenera, które wyzwala wielokierunkowego kosztowne. To zadanie jest jeszcze gorzej możemy docelową znacznie większy zestaw wyników i sortować wyniki z `ORDER BY` klauzuli, co pozwala na bardziej kosztowne pod względem jednostki żądania.

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 306 ms | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Rozważania na temat wydajności w wersji 1

Patrząc problemy z wydajnością, które firma Microsoft jest skierowany w poprzedniej sekcji, umożliwi nam poznanie dwóch klas głównych problemów:

- niektóre żądania wymagają wielu zapytań wydaje się, aby zebrać wszystkie dane potrzebne do zwrócenia,
- Niektóre zapytania nie filtru klucza partycji, kontenerów, docelowych, co prowadzi do wielokierunkowe, która utrudnia naszych skalowalność.

Spróbujmy rozwiązania każdego z tych problemów, rozpoczynając od pierwszego.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Wprowadzenie do denormalizacja zoptymalizować zapytania odczytu

Powód, dlaczego mamy do wysyłania dodatkowych żądań w niektórych przypadkach jest, ponieważ wyniki żądania wstępnego nie zawiera wszystkie dane potrzebne do zwrócenia. Podczas pracy z magazynem danych nierelacyjnych, takich jak usługi Azure Cosmos DB, rozwiązania tego problemu jest zwykle rozwiązać przez denormalizing danych w naszym zestawie danych.

W naszym przykładzie zmodyfikujemy wpis elementy do dodania nazwy użytkownika autora wpisu, liczba komentarzy i liczbę polubień:

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

Możemy również zmodyfikować komentarz i elementy do dodania nazwa użytkownika, który je utworzył, takich jak:

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

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizing komentarz i liczby, takich jak

Co chcemy osiągnąć to, że za każdym razem, gdy możemy dodać polubienie lub komentarz, możemy też zwiększyć `commentCount` lub `likeCount` z odpowiedniego wpisu. Jako naszych `posts` kontenera jest podzielona na partycje według `postId`, nowy element (komentarze lub, takich jak) i jego odpowiedniego wpisu znajdują się w jednej partycji logicznej. W rezultacie, możemy użyć [procedury składowanej](stored-procedures-triggers-udfs.md) do wykonania tej operacji.

Teraz podczas tworzenia komentarza (**[C3]**), zamiast po prostu dodać nowy element w `posts` kontenera nazywamy następującą procedurę składowaną w danym kontenerze:

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

Tę procedurę składowaną, następnie przyjmuje identyfikator wpisu i treści nowy komentarz jako parametry:

- pobiera wpisu
- zwiększa `commentCount`
- zastępuje wpisu
- dodaje nowy komentarz

Procedury składowane są wykonywane jako transakcji niepodzielnej, ma żadnej gwarancji, że wartość `commentCount` a rzeczywista liczba komentarzy zawsze pozostają zsynchronizowane.

Oczywiście nazywamy podobne procedury składowanej podczas dodawania nowych polubień się zwiększać `likeCount`.

### <a name="denormalizing-usernames"></a>Denormalizing nazw użytkowników

Nazwy użytkowników wymagają innego podejścia, ponieważ użytkownicy nie tylko znajdują się w różnych partycjach, ale innego kontenera. Gdy mamy zdenormalizowanie danych między partycjami i kontenerów, możemy użyć kontenera źródłowego [zestawienia zmian](change-feed.md).

W naszym przykładzie używamy zestawienia zmian `users` kontenera reagować w każdym przypadku, gdy użytkownicy zaktualizować ich nazw użytkowników. Jeśli tak się stanie, firma Microsoft propagować zmian za pomocą wywołania innej procedury przechowywanej na `posts` kontenera:

![Denormalizing nazw użytkowników w kontenerze wpisów](./media/how-to-model-partition-example/denormalization-1.png)

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

Tę procedurę składowaną, następnie przyjmuje identyfikator użytkownika i nowego użytkownika jako parametry:

- pobiera wszystkie elementy pasujące `userId` (które mogą być wpisy, komentarze lub polubień)
- dla każdego z tych elementów
  - zastępuje `userUsername`
  - zastępuje element

> [!IMPORTANT]
> Ta operacja jest kosztowne, ponieważ wymaga tej procedury składowanej do wykonania na każdej partycji o `posts` kontenera. Przyjęto założenie, że większość użytkowników wybierz odpowiednie nazwy użytkownika podczas rejestracji i nigdy nie zmienią, więc ta aktualizacja zostanie uruchomiony bardzo rzadko.

## <a name="what-are-the-performance-gains-of-v2"></a>Co to jest zwiększenie wydajności w wersji 2?

### <a name="q2-retrieve-a-post"></a>[Q2] Pobieranie wpisu

Teraz, że nasze denormalizacja znajduje się w miejscu, mamy tylko jeden element, aby obsłużyć tego żądania pobierania.

![Trwa pobieranie pojedynczego elementu z kontenera wpisów](./media/how-to-model-partition-example/V2-Q2.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 2 ms | 1 JEDNOSTKA ŻĄDANIA | ✅ |

### <a name="q4-list-a-posts-comments"></a>KWARTAŁ [4] Lista publikowanie komentarzy

W tym miejscu ponownie, firma Microsoft może zapasowe dodatkowych żądań, które pobrać nazwy użytkownika i na końcu za pomocą jednego zapytania, który filtruje klucza partycji.

![Pobieranie wszystkich komentarzy na wpis](./media/how-to-model-partition-example/V2-Q4.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 4 ms | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Lista polubienia post

Dokładnie tej samej sytuacji podczas wyświetlania polubień.

![Pobieranie wszystkich polubień na stanowisko](./media/how-to-model-partition-example/V2-Q5.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Upewniając się, wszystkie żądania są skalowalne

Patrząc naszych poprawy wydajności, nadal istnieją dwa żądania, które firma Microsoft nie zostały w pełni zoptymalizowana pod: **[K3]** i **[Q6]**. Są to żądania dotyczące zapytania, które nie filtrowanie według klucza partycji, kontenerów, docelowych.

### <a name="q3-list-a-users-posts-in-short-form"></a>[K3] Listy wpisów użytkownika w krótkich fragmentów

To żądanie jest już korzysta z ulepszeń wprowadzonych w wersji 2 dysków zapasowych dodatkowych kwerend.

![Trwa pobieranie wszystkie wpisy dla użytkownika](./media/how-to-model-partition-example/V2-Q3.png)

Pozostałe zapytania nie jest nadal filtrowania na klucz partycji, ale `posts` kontenera.

Sposób myśleć o tej sytuacji jest naprawdę proste:

1. To żądanie *ma* do filtrowania `userId` ponieważ chcemy, aby pobrać wszystkie wpisy dla określonego użytkownika
1. Nie działa, ponieważ jest wykonywane względem `posts` kontenera, w którym nie jest podzielona na partycje według `userId`
1. Podając oczywiste, firma Microsoft może rozwiązać problem z wydajnością przez wykonanie tego żądania względem kontenera, *jest* podzielonym na partycje według `userId`
1. Okazuje się, że mamy już takiego kontenera: `users` kontenera!

Dzięki wprowadzeniu drugiego poziomu denormalizacja duplikując całego wpisów `users` kontenera. Wykonując skutecznie uzyskujemy kopię nasze wpisy tylko partycje wzdłuż różnych wymiarach, dzięki czemu w sposób bardziej wydajne, można pobrać przez ich `userId`.

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

- Firma Microsoft wprowadza `type` pole w elemencie użytkownika do rozróżniania użytkowników z wpisów,
- dodaliśmy również `userId` pole w elemencie "użytkownik" jest nadmiarowy za pomocą `id` ale pole jest wymagane jako `users` kontenera jest teraz podzielona na partycje według `userId` (a nie `id` wcześniej)

Uzyskanie tego denormalizacja, możemy ponownie użyć zestawienia zmian. Tym razem, możemy reagować na zestawienia zmian `posts` kontenera do wysyłania nowych lub zaktualizowanych wpis, aby `users` kontenera. I ponieważ lista wpisów nie wymaga do zwrócenia ich pełnej zawartości, firma Microsoft może Obetnij je w procesie.

![Denormalizing wpisów w kontenerze Użytkownicy](./media/how-to-model-partition-example/denormalization-2.png)

Firma Microsoft może teraz kierować naszego zapytania do `users` kontenera, filtrowanie według klucza partycji kontenera.

![Trwa pobieranie wszystkie wpisy dla użytkownika](./media/how-to-model-partition-example/V3-Q3.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista x najnowsze wpisy utworzone w skrócie formularza (źródło)

Mamy do czynienia z podobna sytuacja: mimo spare dodatkowych kwerend w lewo niepotrzebne przez denormalizacja wprowadzona w wersji 2, pozostałe zapytania nie można filtrować według klucza partycji kontenera:

![Trwa pobieranie najnowsze wpisy](./media/how-to-model-partition-example/V2-Q6.png)

Po tej samej metody, maksymalizowanie wydajności i skalowalności to żądanie wymaga, że trafienia tylko jedną partycję. Jest to wymyślonego przez siebie, ponieważ trzeba zwrócić ograniczoną liczbę elementów. Aby wypełnić stronę główną naszą platformę do obsługi blogów, musimy pobrać 100 najnowsze wpisy, bez konieczności stronicowanie za pośrednictwem całego zestawu danych.

Dlatego w celu zoptymalizowania tego ostatniego żądania, wprowadzeniu trzeci kontener do naszego projektu, całkowicie dedykowany obsługująca tego żądania. Firma Microsoft denormalizowanie nasze wpisy w tym nowych `feed` kontenera:

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

Ten kontener jest podzielona na partycje według `type`, który będzie zawsze `post` w naszych elementów. Sposób, który zapewnia, że wszystkie elementy w tym kontenerze będzie znajdują się w tej samej partycji.

Aby osiągnąć denormalizacja, mamy po prostu utworzenie punktu zaczepienia w przypadku zmiany źródła danych potoku wprowadziliśmy wcześniej wysyłania wpisy do tego nowego kontenera. Istotnym aspektem bez pamiętać jest potrzebujemy upewnić się, że przechowujemy tylko 100 najnowsze wpisy; w przeciwnym razie zawartość kontenerów może rosnąć poza maksymalny rozmiar partycji. Jest to realizowane przez wywołanie [po wyzwalacza](stored-procedures-triggers-udfs.md#triggers) za każdym razem, gdy dokument zostanie dodany do kontenera:

![Denormalizing wpisy do źródła danych kontenera.](./media/how-to-model-partition-example/denormalization-3.png)

Oto treści po wyzwalacz, który obcina kolekcji:

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

Ostatnim krokiem jest przekierowywania naszego zapytania do naszej nowej `feed` kontenera:

![Trwa pobieranie najnowsze wpisy](./media/how-to-model-partition-example/V3-Q6.png)

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Podsumowanie

Przyjrzyjmy się jej przyjrzeć ogólne ulepszenia wydajności i skalowalności, które wprowadziliśmy przez różne wersje naszych projektu.

| | Wersja 1 | Wersja 2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5.71 RU | 7 ms / 5.71 RU | 7 ms / 5.71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]** | 9 ms / 8.76 RU | 9 ms / 8.76 RU | 9 ms / 8.76 RU |
| **[Q2]** | 9 ms / 19.54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619.41 RU | 28 ms / 201.54 RU | 4 ms / 6.46 RU |
| **[C3]** | 7 ms / 8.57 RU | 7 ms / 15.27 RU | 7 ms / 15.27 RU |
| **[Q4]** | 23 ms / 27.72 RU | 4 ms / 7.72 RU | 4 ms / 7.72 RU |
| **[C4]** | 6 ms / 7.05 RU | 7 ms / 14.67 RU | 7 ms / 14.67 RU |
| **[Q5]** | 59 ms / 58.92 RU | 4 ms / 8.92 RU | 4 ms / 8.92 RU |
| **[Q6]** | 306 ms / 2063.54 RU | 83 ms / 532.33 RU | 9 ms / 16.97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Zoptymalizowaliśmy scenariusza odczycie

Być może zauważono, że firma Microsoft ma skoncentrowane dążymy do poprawy wydajność żądań odczytu (zapytania) kosztem żądań zapisu (poleceń). W wielu przypadkach operacje zapisu teraz wyzwolić kolejnych denormalizacja za pośrednictwem ze źródeł zmian, które czynią je bardziej praktyce kosztowne, a dłużej zmaterializowania.

Jest to uzasadnione faktem, że platforma do obsługi blogów, (na przykład aplikacje najbardziej społecznościowe) jest odczyt dużą ilością zawartości, co oznacza ilość żądaniami odczytu, które musi obsługiwać jest zazwyczaj rzędy wyższe niż ilość żądań zapisu. Dlatego warto utworzyć bardziej kosztowne, można wykonać, aby zezwolić na żądania zapisu żądaniami odczytu się tańsze i lepsze wykonywanie.

Jeśli spojrzymy na najbardziej ekstremalnych optymalizacji wykonaliśmy, **[Q6]** poszło z 2000 + (RUS) po prostu 17 (RUS); firma Microsoft, zostały osiągnięte przez denormalizing wpisów w cenie około 10 jednostek zarezerwowanych na element. Jak firma Microsoft będzie obsługiwać żądania źródła znacznie więcej danych niż Tworzenie lub aktualizowanie wpisów, koszt tego denormalizacja jest nieistotny, biorąc pod uwagę ogólną oszczędności.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalizacja, które mogą być stosowane przyrostowo

Ulepszenia skalowalności, który rozważyliśmy w tym artykule dotyczą denormalizacja i duplikacji danych w zestawie danych. Należy zauważyć, że Optymalizacje te nie mają do umieszczenia w miejscu w dniu 1. Zapytania, które filtrowanie według kluczy partycji działać lepiej w dużej skali, ale zapytań między partycjami może być całkowicie dopuszczalne tylko, gdy są one nazywane lub rzadko względem ograniczony zestaw danych. Jeśli po prostu zbudować prototyp lub uruchamianie produktu za pomocą małych i kontrolowany użytkownika podstawowego, prawdopodobnie można zapas te ulepszenia do późniejszego; Co to jest ważne następnie jest [monitor](use-metrics.md) wydajności modelu podejmiesz decyzję, czy i kiedy nadszedł czas, aby wyświetlić je.

Zmiana źródła danych, użyjemy, aby rozpowszechniać aktualizacje do innego magazynu kontenery wszystkich tych aktualizacji trwałe. Pozwala na request wszystkich aktualizacji od czasu utworzenia kontenera i ładowania początkowego widoki nieznormalizowany to jednorazowa operacja zapoznać się ze zmianami nawet wtedy, gdy system jest już w dużej ilości danych.

## <a name="next-steps"></a>Kolejne kroki

Po wprowadzeniu do praktycznych modelowania i partycjonowanie danych którego chcesz sprawdzić następujące artykuły, aby zapoznać się z pojęciami, które, gdy Omówiliśmy już:

- [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md)
- [Partycjonowanie w usłudze Azure Cosmos DB](partitioning-overview.md)
- [Zmiana źródła danych w usłudze Azure Cosmos DB](change-feed.md)