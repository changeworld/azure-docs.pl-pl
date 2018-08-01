---
title: Przykłady prostego zapytania usługi Azure Search | Dokumentacja firmy Microsoft
description: Przykłady prostego zapytania wyszukiwania pełnotekstowego, filtr wyszukiwania, wyszukiwanie geograficzne, wyszukiwanie aspektowe i inne ciągi zapytań, które umożliwia tworzenie zapytań względem indeksu usługi Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369121"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Prosta składnia zapytań przykłady do tworzenia zapytań w usłudze Azure Search

[Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) wywołuje analizator składni zapytań domyślne do wykonywania zapytań wyszukiwania pełnotekstowego względem indeksu usługi Azure Search. Analizator prostego zapytania jest szybkie i obsługuje typowych scenariuszy w usłudze Azure Search, takich jak wyszukiwanie pełnotekstowe, wyszukiwanie filtrowane i aspektowa i wyszukiwanie geograficzne. W tym artykule należy przejrzeć przykłady pokazujące dostępne operacje zapytań, korzystając z prostą składnię.

Składnia zapytania alternatywne jest [pełne Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), takie jak obsługa bardziej złożonych struktur, zapytania, rozmyte i wyszukiwania symboli wieloznacznych, co może zająć dodatkowy czas przetwarzania. Aby uzyskać więcej informacji i przykłady pokazujące pełną składnię, zobacz [przykłady zapytań składni Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Sformułowania żądań w narzędziu Postman

Poniższe przykłady korzystać z indeksu wyszukiwania Pokazowa składający się z dostępnych zadań na podstawie zestawu danych, dostarczone przez [miasta w Nowym Jorku OpenData](https://nycopendata.socrata.com/) inicjatywy. Te dane nie uważa się bieżących lub ukończone. Indeks znajduje się w piaskownicy usługi obsługiwane przez firmę Microsoft, co oznacza, że nie ma potrzeby subskrypcji platformy Azure lub usługi Azure Search, aby wypróbować te zapytania.

Konieczne jest Postman lub równoważne narzędzie do wystawiania żądań HTTP na GET. Aby uzyskać więcej informacji, zobacz [testu za pomocą klientów REST](search-fiddler.md).

### <a name="set-the-request-header"></a>Ustaw nagłówek żądania

1. W nagłówku żądania ustaw **Content-Type** do `application/json`.

2. Dodaj **klucz interfejsu api**i ustaw go na następujący ciąg: `252044BE3886FE4A8E3BAA4F595114BB`. Jest to klucz zapytania usługi wyszukiwania piaskownicy hostingu indeksu Pokazowa.

Po określeniu nagłówek żądania, można użyć ponownie go dla wszystkich zapytań w tym artykule wymienić tylko **wyszukiwania =** ciągu. 

  ![Nagłówek żądania narzędzia Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Ustaw adres URL żądania

Żądanie jest sparowana z adres URL usługi Azure Search punktu końcowego i wyszukaj ciąg zawierający polecenie GET.

  ![Nagłówek żądania narzędzia Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Kompozycja adresu URL zawiera następujące elementy:

+ **`https://azs-playground.search.windows.net/`** jest to usługa wyszukiwania w piaskownicy jest obsługiwana przez zespół usługi Azure Search. 
+ **`indexes/nycjobs/`** jest indeksem Pokazowa w kolekcji indeksów tej usługi. Nazwa usługi i indeksu są wymagane dla żądania.
+ **`docs`** to kolekcji documents zawierający całą zawartość można wyszukiwać. Klucz interfejsu api zapytań podany w nagłówku żądania działa tylko na operacje odczytu, wybieranie kolekcji dokumentów.
+ **`api-version=2017-11-11`** Ustawia wartość api-version, czyli wymaganego parametru na każde żądanie.
+ **`search=*`** jest ciągiem zapytania, które początkowego zapytania ma wartość null, zwraca 50 pierwszych wyników (domyślnie).

## <a name="send-your-first-query"></a>Wyślij pierwszego zapytania

Jako kroku weryfikacji, wklej następujące żądanie GET, a następnie kliknij przycisk **wysyłania**. Wyniki są zwracane jako pełne dokumenty JSON. Użytkownik może kopiowanie i wklejanie tego adresu URL w pierwszym przykładzie poniżej.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

Ciąg zapytania **`search=*`**, odpowiada nieokreślonego wyszukiwania wyszukiwania o wartości null ani być pusta. Nie jest to szczególnie przydatne, ale jest najprostszym wyszukiwania, które można wykonać.

Opcjonalnie można dodać **`$count=true`** do adresu URL, aby zwrócić liczbę dokumentów spełniających kryteria wyszukiwania. W ciągu wyszukiwania puste to wszystkie dokumenty w indeksie (2802 w przypadku Pokazowa).

## <a name="how-to-invoke-simple-query-parsing"></a>Jak wywołać proste zapytanie analizy

Dla zapytań interakcyjnych, nie trzeba niczego określać: proste jest ustawieniem domyślnym. W kodzie, jeśli wcześniej wywoływany **queryType = full** składni pełnej kwerendy, można zresetować Wstecz, aby domyślne **queryType = proste**.

## <a name="example-1-field-scoped-query"></a>Przykład 1: Zakres pola zapytania

Pierwsze zapytanie nie jest specyficzne składni (zapytanie działa zarówno proste, jak i pełnej składni), ale możemy prowadzić z tego przykładu wprowadzenie pojęcia zapytania linii bazowej, tworzącego uzasadniony sposób czytelny odpowiedź w formacie JSON. Celu skrócenia programu, zapytanie jest przeznaczona tylko *business_title* pola, a następnie określa tylko tytuły biznesowe są zwracane. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

**SearchFields** parametr ogranicza wyszukiwanie do tylko pola tytułu biznesowych. **Wybierz** parametr określa pola, które znajdują się w zestawie wyników.

Odpowiedź dla tego zapytania powinien wyglądać podobnie do poniższej zrzut ekranu.

  ![Postman przykładowa odpowiedź](media/search-query-lucene-examples/postman-sample-results.png)

Być może Zauważyłeś, że wynik wyszukiwania jest także zwracany dla każdego dokumentu, nawet jeśli nie określono wyniku wyszukiwania. Jest to spowodowane wynik wyszukiwania jest metadanych z wartością wskazujący rangi kolejność wyników. Jednolite wyniki 1 wystąpić, gdy jest nie rangę, albo ponieważ wyszukiwanie pełnotekstowe nie jest wyszukiwanie lub ponieważ nie kryteria w celu zastosowania. Dla wyszukiwania o wartości null jest kryteriów i wiersze powracające znajdują się w dowolnej kolejności. Zgodnie z kryteriami wyszukiwania przyjmuje więcej definicji, zostaną wyświetlone wyniki ewoluować w istotne wartości wyszukiwania.

## <a name="example-2-look-up-by-id"></a>Przykład 2: Wyszukiwania według Identyfikatora

W tym przykładzie jest to nieco nietypowe, ale podczas obliczania wyszukiwań, możesz chcieć sprawdzić całą zawartość dokumentu, aby zrozumieć, dlaczego zostało dołączone lub wykluczone z wyników. Aby przywrócić cały dokument, należy użyć [operacji wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) podawać identyfikatora dokumentu.

Wszystkie dokumenty ma unikatowy identyfikator. Aby wypróbować składnia zapytania wyszukiwania, najpierw zwrócić listę dokumentu identyfikatory tak, aby można było znaleźć należy użyć. Pokazowa, identyfikatory są przechowywane w `id` pola.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

Następny przykład jest zapytaniem wyszukiwania zwraca określony dokument na podstawie `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", która znajdowała się pierwszy w poprzedniej odpowiedzi. Następujące zapytanie zwraca całego dokumentu, nie tylko wybranych pól. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>Przykład 3: Wyszukiwanie dokładności

Termin zapytania są pojedyncze terminy, może być wiele z nich, które zostaną ocenione niezależnie. Wyrażenie zapytania są ujęte w znaki cudzysłowu i jego ocenie jako ciąg verbatim. Dokładność dopasowania jest kontrolowana przez operatorów i searchMode.

Przykład 1: **`&search=fire`** zwraca wyniki 150, gdzie wszystkie dopasowania zawierać fire programu word, zawarty w dokumencie.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Przykład 2: **`&search=fire department`** zwraca wyniki, 2002. Dopasowania są zwracane dla dokumentów zawierających pożar lub działów.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Przykład 3: **`&search="fire department"`** zwraca wyniki 82. Otaczający ciąg w cudzysłowie to wyszukiwanie verbatim na oba warianty pojęć i dopasowania znajdują się na warunki tokenami w indeksie, składający się z warunków połączone. To wyjaśnia, dlaczego wyszukiwania, takie jak **`search=+fire +department`** nie odpowiada. Oba warunki są wymagane, ale są skanowane pod kątem niezależnie. 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>Przykład 4: Wartości logiczne, z searchMode

Prosta składnia obsługuje operatory logiczne w formie znaków (`+, -, |`). Parametr searchMode informuje kompromis między dokładności i odwołania, za pomocą `searchMode=any` favoring odwołania (dopasowywania dla dowolnego kryterium kwalifikuje się dokument w zestawie wyników), i `searchMode=all` favoring precyzji (wszystkie kryteria muszą się zgadzać). Wartość domyślna to `searchMode=any`, który mylące, jeśli są układania zapytania przy użyciu wielu operatorów i wprowadzenie szersze zamiast mniejszą niż wyniki. Jest to szczególnie istotne, z użyciem NOT, w którym wyniki obejmują wszystkie dokumenty "nie zawiera" konkretny termin.

Przy użyciu domyślnego searchMode (wszystkie), są zwracane dokumenty 2800: termin zawierającymi wiele części "fire dział", a także wszystkie dokumenty, które nie mają termin "Metrotech Centrum".

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
Zmiana searchMode do `all` wymusza skumulowany efekt kryteriami i zwraca mniejszy zestaw wyników — dokumenty 21 - składający się z dokumentów zawierających cała fraza "fire dział" minus te zadania pod adresem Metrotech Centrum.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>Przykład 5: Tworzenie struktury wyników

Kilka kontroli parametry, które pola są w wyszukiwaniu powoduje liczby zwracanych w każdej partii i porządek sortowania dokumentów. W tym przykładzie resurfaces kilka poprzednich przykładach ograniczania wyników określonych pól za pomocą **$select** instrukcji i kryteria wyszukiwania verbatim, zwracając 82 dopasowań 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Dołączany na poprzednim przykładzie, można sortować według tytułu. Tego rodzaju działania, ponieważ jest civil_service_title *sortowanie* w indeksie.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Stronicowanie wyników jest implementowany przy użyciu **$top** parametru, w tym przypadku zwracając dokumentów pierwsze 5:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Aby uzyskać dalej 5, Pomiń pierwszej partii:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Kolejne kroki
Spróbuj określić zapytania w kodzie. Poniższe linki wyjaśniono, jak skonfigurować zapytania wyszukiwania dla środowiska .NET i interfejsu API REST przy użyciu domyślnego prostą składnię.

* [Zapytanie indeksu usługi Azure Search przy użyciu zestawu .NET SDK](search-query-dotnet.md)
* [Zapytanie indeksu usługi Azure Search przy użyciu interfejsu API REST](search-query-rest-api.md)

Odwołanie do dodatkowej składni, architektura zapytania i przykłady można znaleźć w następujących łączy:

+ [Przykłady zapytań składni Lucene do tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Pełne zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
