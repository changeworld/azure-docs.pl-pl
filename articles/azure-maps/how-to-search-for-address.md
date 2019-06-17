---
title: Jak wyszukiwać adres przy użyciu usługi Azure Maps Search | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyszukiwać adres przy użyciu usługi Azure Search mapy
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 497ffb5acf6262dfb42f490efe68e1ea26c777cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572376"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Znajdowanie adresu przy użyciu usługi Azure Maps usługi wyszukiwania

Usługa wyszukiwania map to zbiór interfejsów API RESTful umożliwiający deweloperom wyszukiwanie adresów, miejsc, punktów orientacyjnych, list biznesowych i innych informacji geograficznych. Usługa przypisuje szerokości/długości geograficznej do określonego adresu, między ulicy, funkcja geograficznej lub punktu orientacyjnego (punktów POI). Wartości szerokości i długości geograficznej zwróconego przez wyszukiwanie może służyć jako parametry w innych usługach map, takich jak trasach i ruchu.

W tym artykule dowiesz się jak:

* Wyszukaj przy użyciu adresu [interfejs API wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Wyszukiwanie adresu oraz współrzędne i właściwości
* Wprowadź [odwrotnego wyszukiwania adresu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) aby wyszukać adres ulicy
* Wyszukaj między ulicy przy użyciu [adres odwrotnego między ulicy interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wprowadzić wszelkie wywołania z usługą mapy interfejsów API, należy mapy konta i klucz. Aby uzyskać informacji na temat tworzenia konta usługi i pobierania klucza, zobacz [jak zarządzać swoim kontem usługi Azure Maps i klucze](how-to-manage-account-keys.md).

W tym artykule wykorzystano [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Możesz użyć dowolnego interfejsu API środowisko programistyczne, które użytkownik sobie tego życzy.

## <a name="using-fuzzy-search"></a>Za pomocą wyszukiwania rozmytego

Domyślny interfejs API usługi wyszukiwania jest [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) i jest przydatne, jeśli nie wiesz, jakie dane wejściowe użytkownika są zapytania wyszukiwania. Interfejs API łączy punktów POI wyszukiwania i geokodowania w canonical "jednowierszowego wyszukiwania". Na przykład interfejs API może obsługiwać dane wejściowe, adres lub kombinacji token punktu orientacyjnego. Można również ważone z pozycją kontekstowych (pomocą tabeli lat./długa. para), w pełni ograniczone przez współrzędnych i usługi radius lub wykonywane częściej bez żadnych geograficzną punktu kontrolnego odchylenia opóźnienia.

Domyślnie większość zapytań wyszukiwania `maxFuzzyLevel=1` wydajność i obniżyć nieoczekiwane rezultaty. To ustawienie domyślne można przesłonić, zgodnie z potrzebami każdego żądania, przekazując w parametrze zapytania `maxFuzzyLevel=2` lub `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Wyszukiwanie adresu za pomocą wyszukiwania rozmytego

1. Otwórz aplikację Postman, kliknij przycisk Nowa | Utwórz nowy, a następnie wybierz pozycję **żądanie GET**. Wprowadź nazwę żądania **Wyszukiwanie rozmyte**, wybierz kolekcję lub folder, aby zapisać go w celu, a następnie kliknij przycisk **Zapisz**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.

    ![Fuzzy Search](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autoryzacja | Nie uwierzytelniania |

    **Json** atrybut ścieżki adresu URL określa format odpowiedzi. Łatwość użycia i czytelności używasz formatu json, w tym artykule. Można znaleźć formaty dostępne odpowiedzi w **uzyskać wyszukiwania rozmytego** definicji [odwołania funkcjonalny interfejs API map](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Kliknij przycisk **Params**, a następnie wprowadź poniższy klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:

    ![Fuzzy Search](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Klucz usługi Azure Maps\> |
    | query | pizza |

4. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi.

    Ciąg zapytania niejednoznaczne "głosi" zwracana 10 [punktu wyników zainteresowania](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) wyniki (punktów POI) z kategorii w "głosi" i "restauracji". Każdy wynik zwraca adres ulicy, szerokość geograficzna / wyświetlić wartości długości geograficznej, portów i punktów wejścia do lokalizacji.
  
    Wyniki są zróżnicowane dla tego zapytania nie są powiązane z dowolnej lokalizacji szczególnym odniesieniem. Możesz użyć **countrySet** parametru do określenia tylko kraje/regiony dla których aplikacja wymaga pokrycia, zgodnie z domyślnym zachowaniem jest na całym świecie, potencjalnie zwracanie niepotrzebnego wyników wyszukiwania.

5. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |------------------|-------------------------|
    | countrySet | USA |
  
    Wyniki są obecnie ograniczone przez kod kraju, a zapytanie zwróci pizza restauracje w Stanach Zjednoczonych.
  
    Aby zapewnić wyniki dla lokalizacji, możesz można kwerendy punktu orientacyjnego i używać zwrócone szerokości i długości geograficznej wartości w wywołania do usługi wyszukiwania rozmytego. W tym przypadku używane usługi wyszukiwania w celu zwrócenia lokalizacja wskazówkę miejsca Seattle i używane pomocą tabeli lat. / długa. wartości do zorientowania wyszukiwania.
  
6. W Params, wprowadź następujący klucz / wartość pary i kliknij przycisk **wysyłania**:

    ![Fuzzy Search](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Klucz | Wartość |
    |-----|------------|
    | lat | 47.620525 |
    | długa | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Wyszukiwanie właściwości adresu i współrzędnych

Można przekazać adres pełną lub częściową adres wyszukiwania interfejsu API i odbierać odpowiedź, która zawiera adres szczegółowe właściwości, takie jak gmina lub części, a także wartości pozycyjnych w szerokości i długości geograficznej.

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **wyszukiwanie adresu**.
2. Na karcie Builder wybierz **UZYSKAĆ** metodę HTTP, wprowadź adres URL żądania dla punktu końcowego interfejsu API i wybierz protokół autoryzacji, jeśli istnieje.

    ![Wyszukiwanie adresu](./media/how-to-search-for-address/address_search_url.png)
  
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autoryzacja | Nie uwierzytelniania |

3. Kliknij przycisk **Params**, a następnie wprowadź poniższy klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
  
    ![Wyszukiwanie adresu](./media/how-to-search-for-address/address_search_params.png)
  
    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Klucz usługi Azure Maps\> |
    | query | 400 Broad St, Seattle, WA 98109 |
  
4. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi.
  
    W tym przypadku określone zapytanie kompletny adres i otrzymywać jeden wynik w treści odpowiedzi.
  
5. W Params Edytuj ciąg zapytania do następującej wartości:
    ```plaintext
        400 Broad, Seattle
    ```

6. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | typeahead | true |

    **Typeahead** flagi informuje interfejsu API wyszukiwania adresów do traktowania zapytania jako częściowe dane wejściowe i zwraca tablicę wartości predykcyjne.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Wyszukaj adres ulicy, używając odwrotnego wyszukiwania adresu

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **odwrotnego wyszukiwania adresu**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.
  
    ![Adres URL wyszukiwania wstecznego](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autoryzacja | Nie uwierzytelniania |
  
3. Kliknij przycisk **Params**, a następnie wprowadź poniższy klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
  
    ![Adres parametry wyszukiwania wstecznego](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Klucz usługi Azure Maps\> |
    | query | 47.591180,-122.332700 |
  
4. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi.

    Odpowiedź zawiera adres kluczowych informacji na temat Safeco Field baza domowa.
  
5. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | numer | true |

    Jeśli [numer](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parametr zapytania są wysyłane z tym żądaniem, odpowiedź może zawierać boku ulicy (lewo/w prawo), a także przesunięcie pozycji dla tego numeru.
  
6. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Gdy [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parametru zapytania jest ustawiona, zwraca odpowiedź przesłanych limitu szybkości.

7. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | returnRoadUse | true |

    Gdy [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parametru zapytania jest ustawiona, odpowiedzi zwraca tablicę Użyj drogi, do odwrotnego geocodes na poziomie ulicy.

8. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | roadUse | true |

    Zapytanie wsteczne geokodowania można ograniczyć do określonego typu drogowym za pomocą funkcji [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) parametr zapytania.
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Wyszukaj między ulica, przy użyciu odwrotnego wyszukiwania ulicy wielu adresów

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **odwrotnego Cross ulicy wyszukiwanie adresu**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.
  
    ![Odwrócone wyszukiwanie ulicy wielu adresów](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autoryzacja | Nie uwierzytelniania |
  
3. Kliknij przycisk **Params**, a następnie wprowadź poniższy klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
  
    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Klucz usługi Azure Maps\> |
    | query | 47.591180,-122.332700 |
  
4. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi.

## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się z [usługi Azure Maps, Usługa wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) dokumentacji interfejsu API.
