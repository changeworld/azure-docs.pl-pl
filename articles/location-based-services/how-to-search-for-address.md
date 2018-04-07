---
title: Sposób wyszukiwania adresu przy użyciu usługi wyszukiwanie Azure usługi na podstawie lokalizacji (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyszukiwanie adresu przy użyciu lokalizacji na podstawie usług Azure (wersja zapoznawcza) usługi wyszukiwania
services: location-based-services
author: kgremban
ms.author: kgremban
ms.date: 11/29/2017
ms.topic: article
ms.service: location-based-services
ms.openlocfilehash: f8b886607fe0915396a659593cd5910a271fca93
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-find-an-address-using-the-azure-location-based-services-preview-search-service"></a>Jak znaleźć adres przy użyciu usługi wyszukiwanie Azure usługi na podstawie lokalizacji (wersja zapoznawcza)
Usługa wyszukiwania jest RESTful zestaw interfejsów API przeznaczone dla deweloperów wyszukać adresy, miejsca, punkty zainteresowań, listy biznesowych i innych informacji geograficznych. Usługa wyszukiwania przypisuje szerokości geograficznej/długości określonego adresu, krzyżowego ulicy, geograficzne funkcji lub z interesujących (POI). Współrzędne geograficzne wartości zwracane przez usługę wyszukiwania interfejsów API mogą być używane jako parametry w innych Azure lokalizacji na podstawie usług takich jak API przepływ ruchu i trasy.

## <a name="prerequisites"></a>Wymagania wstępne
* Zainstaluj [aplikacji Postman](https://www.getpostman.com/apps).

* Mieć konto usług na podstawie lokalizacji platformy Azure i klucza. Aby uzyskać informacje o tworzeniu konta i pobierania klucza, zobacz [jak zarządzać Twoje konto usługi na podstawie lokalizacji platformy Azure i klucze](how-to-manage-account-keys.md). 

## <a name="using-fuzzy-search"></a>Przy użyciu Wyszukiwanie rozmyte

Domyślny interfejs API usługi wyszukiwania to wyszukiwanie rozmyte, która obsługuje dane wejściowe z dowolną kombinację adresu lub tokenów POI. To wyszukiwanie interfejsu API jest canonical "Wyszukiwanie jednowierszowego" i jest przydatne, jeśli nie wiesz, jakie z danych wprowadzonych przez użytkownika jako zapytania wyszukiwania. Rozmytego API wyszukiwania jest kombinacją POI wyszukiwania i kodowanie geograficzne. Można również ważone interfejsu API z pozycją kontekstowych (pomocą tabeli lat./długa. pair) pełni ograniczone przez współrzędną i usługi radius, lub mogą być wykonywane w ogólnie bez żadnych geograficznie odchylenia opóźnienia punktu zakotwiczenia.

Domyślnie większość zapytań wyszukiwania ' maxFuzzyLevel = 1' w celu uzyskania wydajności i zmniejszenia nieoczekiwane rezultaty. To ustawienie domyślne można przesłonić zgodnie z potrzebami na żądanie przez przekazywanie parametru zapytania "maxFuzzyLevel = 2" lub "3".

### <a name="search-for-an-address-using-fuzzy-search"></a>Wyszukiwanie adresu przy użyciu Wyszukiwanie rozmyte

1. Otwórz aplikację Postman, kliknij przycisk Nowy | Utwórz nowy, a następnie wybierz **żądania GET**. Wprowadź nazwę żądania **Wyszukiwanie rozmyte**, wybierz kolekcję lub folder, aby zapisać go do, a następnie kliknij przycisk **zapisać**.

    Aby uzyskać więcej informacji zobacz dokumentację żądania Postman.

2. Na karcie konstruktora wybierz **UZYSKAĆ** metody HTTP, a następnie wprowadź adres URL żądania dla punktu końcowego interfejsu API.

    ![Fuzzy Search ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | https://atlas.microsoft.com/search/fuzzy/json? |
    | Autoryzacja | Nie autoryzacji |

    **Json** atrybut ścieżki adresu URL określa format odpowiedzi. Łatwość użycia i czytelność używasz json w tym artykule. Możesz znaleźć odpowiedzi dostępnych formatów **uzyskać wyszukiwania rozmytego** definicji [odwołania do lokalizacji na podstawie funkcjonalności interfejsu API usług] (https://docs.microsoft.com/rest/api/location-based-services/search/getsearchfuzzy).

3. Kliknij przycisk **Params**i wprowadź następujący klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:

    ![Fuzzy Search ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klucz | Wartość |
    |------------------|-------------------------|
    | wersja interfejsu API | 1.0 |
    | klucz subskrypcji | \<Klucz usługi na podstawie lokalizacji platformy Azure\> |
    | query | pizza |

4. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi. 

    Ciąg zapytania niejednoznaczne "pizza" zwrócił 10 punktu wyników zainteresowań (POI) z kategorii w "pizza" i "restauracja". Każdy wynik zwraca ulicę szerokości geograficznej / wyświetlić wartości długości geograficznej portu, a punkty wejścia dla lokalizacji.
    
    Wyniki są różne dla tego zapytania nie jest związana z dowolnej lokalizacji danego odwołania. Można użyć **countrySet** parametr, aby określić krajów, dla których aplikacja wymaga pokrycia, zgodnie z domyślnym zachowaniem jest na całym świecie, zwracania potencjalnie niepotrzebnych wyników wyszukiwania.

5. Dodaj następujący klucz / wartość pary **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |------------------|-------------------------|
    | countrySet | USA |
    
    Wyniki są obecnie ograniczone przez kod kraju i zapytanie zwraca restauracji pizza w Stanach Zjednoczonych.
    
    Do udostępniania wyników ukierunkowane na określonej lokalizacji, możesz zapytania punkt odsetek i wybrać współrzędne zwracane wartości długości geograficznej w połączenia z usługą wyszukiwania rozmytego. W takim przypadku służy usługa wyszukiwania do zwracania lokalizację wskazówka miejsca Seattle i używać pomocą tabeli lat. / długa. wartości do zorientowania wyszukiwania.
    
4. W parametrów, wprowadź następujący klucz / wartość pary i kliknij przycisk **wysyłania**:

    ![Fuzzy Search ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Klucz | Wartość |
    |-----|------------|
    | lat | 47.62039 |
    | długa | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Wyszukiwanie właściwości adresu i współrzędnych 

Można przekazywać ulicę pełną lub częściową API adres wyszukiwania i odbierania odpowiedzi, która zawiera właściwości szczegółowe adresów, takich jak jednostki administracyjnej lub podziału, a także pozycyjnych wartości w współrzędne geograficzne.

1. W Postman, kliknij przycisk **nowe żądanie** | **żądania GET** i nadaj mu nazwę **wyszukiwanie adresu**.
2. Na karcie konstruktora wybierz **UZYSKAĆ** metodę HTTP, wprowadź adres URL żądania dla punktu końcowego interfejsu API, a następnie wybierz protokół autoryzacji, jeśli istnieje.

    ![Wyszukiwanie adresu ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | https://atlas.microsoft.com/search/address/json? |
    | Autoryzacja | Nie autoryzacji |

2. Kliknij przycisk **Params**i wprowadź następujący klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
    
    ![Wyszukiwanie adresu ](./media/how-to-search-for-address/address_search_params.png)
    
    | Klucz | Wartość |
    |------------------|-------------------------|
    | wersja interfejsu API | 1.0 |
    | klucz subskrypcji | \<Klucz usługi na podstawie lokalizacji platformy Azure\> |
    | query | 400 Broad St, Seattle, WA 98109 |
    
3. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi. 
    
    W takim przypadku określone zapytanie kompletny adres i odbierać jeden wynik w treści odpowiedzi. 
    
4. W Params należy edytować wartość ciągu kwerendy:
    ```
        400 Broad, Seattle
    ```

5. Dodaj następujący klucz / wartość pary **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | typeahead | true |

    **Typeahead** flagi informuje interfejsu API Search adres Traktuj zapytania jako częściowe dane wejściowe i zwraca tablicę wartości predykcyjnej.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Wyszukaj ulicę za pomocą wyszukiwania wstecznego adresów
1. W Postman, kliknij przycisk **nowe żądanie** | **żądania GET** i nadaj mu nazwę **wyszukiwania wstecznego adres**.

2. Na karcie konstruktora wybierz **UZYSKAĆ** metody HTTP, a następnie wprowadź adres URL żądania dla punktu końcowego interfejsu API.
    
    ![Adres URL wyszukiwania wstecznego ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | https://atlas.microsoft.com/search/address/reverse/json? |
    | Autoryzacja | Nie autoryzacji |
    
2. Kliknij przycisk **Params**i wprowadź następujący klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
    
    ![Adres parametry wyszukiwania wstecznego ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Klucz | Wartość |
    |------------------|-------------------------|
    | wersja interfejsu API | 1.0 |
    | klucz subskrypcji | \<Klucz usługi na podstawie lokalizacji platformy Azure\> |
    | query | 47.59093,-122.33263 |
    
3. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi. 
    
    Odpowiedź zawiera wpis POI Safeco pola z kategorii poi "stadium". 
    
4. Dodaj następujący klucz / wartość pary **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | numer | true |

    Jeśli [numer](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametru zapytania są wysyłane z żądania, odpowiedzi może zawierać części ulicy (prawej/lewej strony), a także przesunięcia pozycji dla tego numeru.
    
5. Dodaj następujący klucz / wartość pary **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | spatialKeys | true |

    Gdy [spatialKeys](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametru zapytania jest ustawiona, odpowiedź zawiera zastrzeżonych przestrzennych geograficznie najważniejsze informacje dla określonej lokalizacji.

6. Dodaj następujący klucz / wartość pary **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | returnSpeedLimit | true |
    
    Gdy [returnSpeedLimit](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametru zapytania jest ustawiona, zwracają oczekujących na opublikowanie limit szybkości odpowiedzi.

7. Dodaj następujący klucz / wartość pary **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | returnRoadUse | true |

    Gdy [returnRoadUse](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametru zapytania jest ustawiona, użyj tablicy array drogowej dla reversegeocodes na poziomie ulicy zwraca odpowiedź.

8. Dodaj następujący klucz / wartość pary **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | roadUse | true |

    Zapytanie wsteczne geocode można ograniczyć do określonego typu drogowej użycia za pomocą [roadUse](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametr zapytania.
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Wyszukaj krzyżowego ulicy przy użyciu wstecznego wyszukiwania ulicy między adresu

1. W Postman, kliknij przycisk **nowe żądanie** | **żądania GET** i nadaj mu nazwę **wstecznego Cross ulicy wyszukiwanie adresu**.

2. Na karcie konstruktora wybierz **UZYSKAĆ** metody HTTP, a następnie wprowadź adres URL żądania dla punktu końcowego interfejsu API.
    
    ![Adres ulicy między wyszukiwania wstecznego ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Autoryzacja | Nie autoryzacji |
    
3. Kliknij przycisk **Params**i wprowadź następujący klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
    
    | Klucz | Wartość |
    |------------------|-------------------------|
    | wersja interfejsu API | 1.0 |
    | klucz subskrypcji | \<Klucz usługi na podstawie lokalizacji platformy Azure\> |
    | query | 47.59093,-122.33263 |
    
4. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi. 

## <a name="next-steps"></a>Kolejne kroki
- Eksploruj [usługi wyszukiwania Serices na podstawie lokalizacji Azure](https://docs.microsoft.com/rest/api/location-based-services/search) dokumentacji interfejsu API 
