---
title: Lokalizacje wyszukiwania przy użyciu Search Service Azure Maps | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wyszukiwać lokalizację przy użyciu Search Service map Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 53856b4157afa5976947c451952fc26eefcdd0ea
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264190"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Znajdź adres przy użyciu usługi wyszukiwania Azure Maps

Usługa Maps Search jest zestawem interfejsów API RESTful przeznaczonych dla deweloperów. Usługa umożliwia wyszukiwanie adresów, miejsc, punktów orientacyjnych, list handlowych i innych informacji geograficznych. Każda z następujących elementów ma wartości szerokości geograficznej i długości geograficznej: określony adres, krzyżową funkcję geograficzną lub punkt zainteresowania (punkt POI). Możesz użyć zwracanych wartości szerokości i długości geograficznej z zapytania jako parametrów w innych usługach map. Na przykład zwracane wartości mogą stać się parametrami usługi Route Service lub usługi przepływu ruchu. 

Zapoznaj się z tematem:

* Wyszukiwanie adresu przy użyciu [interfejsu API wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Wyszukaj adres oraz właściwości i współrzędne
* Aby wyszukać adres ulicy, [Przeszukaj adres zwrotny](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* Wyszukiwanie krzyżowego przy użyciu [adresu wyszukiwania odwrotnej API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać wywołania do interfejsów API usługi Maps, musisz mieć konto usługi Maps i klucz. Aby utworzyć konto dla Azure Maps, postępuj zgodnie z instrukcjami podanymi w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps). Wykonaj kroki opisane w sekcji [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account), jeśli potrzebujesz pomocy przy uzyskiwaniu klucza podstawowego. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

W tym artykule jest wykorzystywana [aplikacja Poster](https://www.getpostman.com/apps) do kompilowania wywołań REST. Możesz użyć dowolnego preferowanego środowiska deweloperskiego interfejsu API.

## <a name="using-fuzzy-search"></a>Korzystanie z wyszukiwania rozmytego

Domyślnym interfejsem API usługi wyszukiwania jest [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Ta usługa jest przydatna, gdy nie masz pewności, czy format danych wejściowych użytkownika jest używany w zapytaniu wyszukiwania. Interfejs API łączy punkt POI wyszukiwanie i geokodowanie do kanonicznego wyszukiwania jednowierszowego. Interfejs API może na przykład obsłużyć dane wejściowe dowolnej kombinacji adresów lub tokenów punkt POI. Ponadto może być ważona z pozycją kontekstową (/Lon. para), w pełni ograniczone przez współrzędne i promień, lub wykonywane bardziej ogólnie bez żadnego punktu zakotwiczenia geograficznego.

Większość zapytań wyszukiwania jest domyślnie `maxFuzzyLevel=1`, aby uzyskać wydajność i zmniejszyć nietypowe wyniki. Ta wartość domyślna może zostać przesłonięta w razie konieczności na żądanie, przekazując parametr zapytania `maxFuzzyLevel=2` lub `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Wyszukaj adres przy użyciu wyszukiwania rozmytego

1. Otwórz aplikację Poster, a następnie kliknij pozycję Nowy | Utwórz nową, a następnie wybierz pozycję **Pobierz żądanie**. Wprowadź nazwę żądania dla **wyszukiwania rozmytego**, wybierz kolekcję lub folder, w którym chcesz zapisać, a następnie kliknij przycisk **Zapisz**.

2. Na karcie Konstruktor wybierz metodę **Get** http i wprowadź adres URL żądania dla punktu końcowego interfejsu API.

    ![Fuzzy Search](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autoryzacja | Brak autoryzacji |

    Atrybut **JSON** w ścieżce URL określa format odpowiedzi. W tym artykule opisano łatwość użycia i czytelność w formacie JSON. Dostępne formaty odpowiedzi można znaleźć w definicji **rozmytego wyszukiwania** w usłudze [Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Kliknij pozycję **params**i wprowadź następujące pary klucz/wartość, które mają być używane jako parametry zapytania lub ścieżki w adresie URL żądania:

    ![Fuzzy Search](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | klucz subskrypcji | \<klucz Azure Maps\> |
    | query | pizza |

4. Kliknij pozycję **Wyślij** i sprawdź treść odpowiedzi.

    Niejednoznaczny ciąg zapytania dla "Pizza" zwrócił 10 [punktów wyniku zainteresowania](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (punkt POI) zarówno w kategorii "Pizza", jak i "restauracji". Każdy wynik zwraca adres ulicy, wartości szerokości i długości geograficznej, port widoku i punkty wejścia dla tej lokalizacji.
  
    Wyniki są różne dla tego zapytania, nie są powiązane z żadną określoną lokalizacją odwołania. Za pomocą parametru **countrySet** można określić tylko kraje/regiony, dla których aplikacja wymaga pokrycia. Domyślnym zachowaniem jest przeszukanie całego świata i potencjalnie zwrócenie niepotrzebnych wyników.

5. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |------------------|-------------------------|
    | countrySet | Stany Zjednoczone |
  
    Wyniki są teraz ograniczone przez kod kraju, a zapytanie zwraca Pizza Restauracje w Stany Zjednoczone.
  
    Aby zapewnić wyniki dla lokalizacji, można wysłać zapytanie do punktu zainteresowania i użyć zwracanych wartości szerokości geograficznej i długości geograficznej w wywołaniu do usługi wyszukiwania rozmytego. W takim przypadku użyto usługi wyszukiwania do zwrócenia położenia wskazówki dotyczącej obszaru Seattle i użycia tabeli lat. Długość. wartości, które umożliwiają ukierunkowanie wyszukiwania.
  
6. W polu Parametry wprowadź następujące pary klucz/wartość, a następnie kliknij pozycję **Wyślij**:

    ![Fuzzy Search](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Klucz | Wartość |
    |-----|------------|
    | usługę | 47,620525 |
    | Długość | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Wyszukaj właściwości adresu i współrzędne

Do interfejsu API adresów wyszukiwania można przekazać pełny lub częściowo ulica adresu. Nadal otrzymujesz odpowiedź, która zawiera szczegółowe właściwości adresu. Szczegółowe właściwości adresu są wartościami, takimi jak wartości pozycyjne, w wysokości i długości geograficznej, gminie lub podziałie.

1. W programie Poster kliknij pozycję **nowe żądanie** , | **Pobierz żądanie** i nadaj nazwę **wyszukiwanemu adresowi**.
2. Na karcie Konstruktor wybierz metodę **Get** http, wprowadź adres URL żądania dla punktu końcowego interfejsu API i wybierz protokół autoryzacji (jeśli istnieje).

    ![Wyszukiwanie adresów](./media/how-to-search-for-address/address_search_url.png)
  
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autoryzacja | Brak autoryzacji |

3. Kliknij pozycję **params**i wprowadź następujące pary klucz/wartość, które mają być używane jako parametry zapytania lub ścieżki w adresie URL żądania:
  
    ![Wyszukiwanie adresów](./media/how-to-search-for-address/address_search_params.png)
  
    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | klucz subskrypcji | \<klucz Azure Maps\> |
    | query | 400 Broad St, Seattle, WA 98109 |
  
4. Kliknij pozycję **Wyślij** i sprawdź treść odpowiedzi.
  
    W takim przypadku należy określić kompletne zapytanie dotyczące adresu i otrzymać jeden wynik w treści odpowiedzi.
  
5. W obszarze Parametry Edytuj ciąg zapytania do następującej wartości:
    ```plaintext
        400 Broad, Seattle
    ```

6. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |-----|------------|
    | typeahead | true |

    Flaga **typeahead** INSTRUUJE interfejs API wyszukiwania adresów, aby traktować zapytanie jako częściowe dane wejściowe i zwracało tablicę wartości predykcyjnych.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Wyszukaj adres ulicy przy użyciu wyszukiwania odwrotnego adresu

1. W programie Poster kliknij pozycję **nowe żądanie** , | **Pobierz żądanie** i nadaj nazwę **Reverse Address Research**.

2. Na karcie Konstruktor wybierz metodę **Get** http i wprowadź adres URL żądania dla punktu końcowego interfejsu API.
  
    ![Adres URL wyszukiwania wstecznego adresu](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autoryzacja | Brak autoryzacji |
  
3. Kliknij pozycję **params**i wprowadź następujące pary klucz/wartość, które mają być używane jako parametry zapytania lub ścieżki w adresie URL żądania:
  
    ![Parametry wyszukiwania wstecznego adresu](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | klucz subskrypcji | \<klucz Azure Maps\> |
    | query | 47.591180,-122,332700 |
  
4. Kliknij pozycję **Wyślij** i sprawdź treść odpowiedzi.

    Odpowiedź zawiera informacje o adresie klucza dotyczące pola Safeco.
  
5. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |-----|------------|
    | numer | true |

    Jeśli parametr [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) jest wysyłany wraz z żądaniem, odpowiedź może zawierać stronę ulicy (lewą lub prawą), a także pozycję przesunięcia dla tego numeru.
  
6. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Gdy parametr zapytania [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) jest ustawiony, odpowiedź zwróci limit liczby opublikowanych prędkości.

7. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |-----|------------|
    | returnRoadUse | true |

    Gdy parametr zapytania [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) jest ustawiony, odpowiedź zwróci tablicę use do dróg w celu odwrócenia geokodowej na poziomie ulicy.

8. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |-----|------------|
    | roadUse | true |

    Można ograniczyć zapytania odwrotnego kodu do określonego typu drogi przy użyciu parametru zapytania [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Wyszukaj wartość Cross-ulica przy użyciu wyszukiwania odwrotnego adresu

1. W programie Poster kliknij pozycję **nowe żądanie** | **Pobierz żądanie** i nadaj jej nazwę **odwrotne wyszukiwanie w adresie**.

2. Na karcie Konstruktor wybierz metodę **Get** http i wprowadź adres URL żądania dla punktu końcowego interfejsu API.
  
    ![Odwrotne wyszukiwanie adresu zwrotnego](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autoryzacja | Brak autoryzacji |
  
3. Kliknij pozycję **params**i wprowadź następujące pary klucz/wartość, które mają być używane jako parametry zapytania lub ścieżki w adresie URL żądania:
  
    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | klucz subskrypcji | \<klucz Azure Maps\> |
    | query | 47.591180,-122,332700 |
  
4. Kliknij pozycję **Wyślij** i sprawdź treść odpowiedzi.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z dokumentacją interfejsu API [usługi Azure Maps Search](https://docs.microsoft.com/rest/api/maps/search) .
