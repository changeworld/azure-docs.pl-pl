---
title: Znajdź adres przy użyciu usługi wyszukiwania Azure Maps | Microsoft Docs
description: Dowiedz się, jak wyszukiwać adresy przy użyciu usługi wyszukiwania Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 56194bcfb9531def87a9918ad442a2927413c964
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432956"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Znajdź adres przy użyciu usługi wyszukiwania Azure Maps

Usługa Maps Search jest zestawem interfejsów API RESTful przeznaczonych dla deweloperów, którzy mogą szukać adresów, miejsc, punktów orientacyjnych i innych informacji geograficznych. Usługa przypisuje szerokość/długość geograficzną do określonego adresu, skrzyżowania, funkcji geograficznej lub punktu orientacyjnego (punkt POI). Wartości szerokości i długości geograficznej zwrócone przez wyszukiwanie mogą służyć jako parametry w innych usługach Maps, takich jak trasa i przepływ ruchu.

W tym artykule dowiesz się, jak:

* Wyszukiwanie adresu przy użyciu [interfejsu API wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Wyszukaj adres oraz właściwości i współrzędne
* Aby wyszukać adres ulicy, [Przeszukaj adres zwrotny](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* Wyszukiwanie krzyżowego przy użyciu [adresu wyszukiwania odwrotnej API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać wywołania do interfejsów API usługi Maps, musisz mieć konto i klucz mapy. Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) , aby utworzyć subskrypcję konta Azure Maps, i wykonaj kroki opisane w sekcji [Uzyskiwanie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) , aby uzyskać klucz podstawowy dla Twojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

W tym artykule jest wykorzystywana [aplikacja Poster](https://www.getpostman.com/apps) do kompilowania wywołań REST. Możesz użyć dowolnego preferowanego środowiska deweloperskiego interfejsu API.

## <a name="using-fuzzy-search"></a>Korzystanie z wyszukiwania rozmytego

Domyślnym interfejsem API usługi wyszukiwania jest [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) i przydatne, gdy nie wiesz, jakie dane wejściowe użytkownika dotyczą zapytania wyszukiwania. Interfejs API łączy punkt POI wyszukiwanie i geokodowanie do kanonicznego wyszukiwania jednowierszowego. Interfejs API może na przykład obsłużyć dane wejściowe dowolnej kombinacji adresów lub tokenów punkt POI. Może być również ważona z pozycją kontekstową (/Lon. para), w pełni ograniczone przez współrzędne i promień, lub wykonywane bardziej ogólnie bez żadnego punktu zakotwiczenia geograficznego.

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

    Atrybut **JSON** w ścieżce URL określa format odpowiedzi. Używasz formatu JSON w tym artykule, aby ułatwić ich użycie i czytelność. Dostępne formaty odpowiedzi można znaleźć w definicji **rozmytego wyszukiwania** w usłudze [Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Kliknij pozycję **params**i wprowadź następujące pary klucz/wartość, które mają być używane jako parametry zapytania lub ścieżki w adresie URL żądania:

    ![Fuzzy Search](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | klucz subskrypcji | \<klucz Azure Maps\> |
    | query | pizza |

4. Kliknij pozycję **Wyślij** i sprawdź treść odpowiedzi.

    Niejednoznaczny ciąg zapytania "Pizza" zwrócił 10 [punktów wyniku zainteresowania](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (punkt POI) wyniki z kategoriami wchodzącymi w skład "Pizza" i "restauracji". Każdy wynik zwraca adres ulicy, wartości szerokości/długości geograficznej, port widoku i punkty wejścia dla tej lokalizacji.
  
    Wyniki są różne dla tego zapytania, nie są powiązane z żadną określoną lokalizacją odwołania. Można użyć parametru **countrySet** , aby określić tylko kraje/regiony, dla których aplikacja wymaga pokrycia, jako domyślne zachowanie, aby przeszukać cały świat, potencjalnie zwracając niepotrzebne wyniki.

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

Można przekazać pełny lub częściowo ulica adresu do interfejsu API adresu wyszukiwania i odebrać odpowiedź, która zawiera szczegółowe właściwości adresu, takie jak gmina lub podział, a także wartości pozycyjne w postaci szerokości i długości geograficznej.

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

    Jeśli parametr [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) jest wysyłany wraz z żądaniem, odpowiedź może zawierać stronę ulicy (lewą/prawą), a także pozycję przesunięcia dla tego numeru.
  
6. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Gdy parametr zapytania [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) jest ustawiony, zwracana jest odpowiedź z zarejestrowanego limitu prędkości.

7. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |-----|------------|
    | returnRoadUse | true |

    Gdy parametr zapytania [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) jest ustawiony, odpowiedź zwróci tablicę use do dróg w celu odwrócenia geokodowej na poziomie ulicy.

8. Dodaj następującą parę klucz/wartość do sekcji **params** i kliknij pozycję **Wyślij**:

    | Klucz | Wartość |
    |-----|------------|
    | roadUse | true |

    Można ograniczyć zapytanie odwrotnego kodu do określonego typu ruchu drogowego za pomocą parametru zapytania [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
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
