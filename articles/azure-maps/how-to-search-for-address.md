---
title: Jak wyszukiwać adres przy użyciu usługi Azure Maps Search | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyszukiwać adres przy użyciu usługi Azure Search mapy
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 8b7d2119e1eef8532c30b0a45ae2684493462277
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990017"
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Jak znaleźć adresu przy użyciu usługi Azure Maps usługi wyszukiwania

Usługa wyszukiwania mapy jest RESTful zestaw interfejsów API, umożliwiający deweloperom wyszukiwanie adresów, miejsc, punktów orientacyjnych, list biznesowych i innych informacji geograficznych. Usługa przypisuje szerokości/długości geograficznej do określonego adresu, między ulicy, funkcja geograficznej lub punktu orientacyjnego (punktów POI). Wartości szerokości i długości geograficznej zwróconego przez wyszukiwanie może służyć jako parametry w innych usługach map, takich jak trasach i ruchu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wprowadzić wszelkie wywołania z usługą mapy interfejsów API, należy mapy konta i klucz. Aby uzyskać informacji na temat tworzenia konta usługi i pobierania klucza, zobacz [jak zarządzać swoim kontem usługi Azure Maps i klucze](how-to-manage-account-keys.md).

W tym artykule wykorzystano [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Możesz użyć dowolnego interfejsu API środowisko programistyczne, które użytkownik sobie tego życzy. 


## <a name="using-fuzzy-search"></a>Za pomocą wyszukiwania rozmytego

Domyślny interfejs API usługi wyszukiwania jest [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy), która obsługuje dane wejściowe z dowolnej kombinacji adresu lub punktów POI tokenów. Ten interfejs API wyszukiwania jest canonical "Wyszukaj jeden wiersz" i jest przydatne, gdy nie wiadomo, jakie swoje dane wprowadzane przez użytkownika jako zapytania wyszukiwania. Interfejs API wyszukiwania rozmytego jest kombinacją punktów POI wyszukiwania i geokodowania. Interfejs API może również ważone z pozycją kontekstowych (pomocą tabeli lat./długa. Sparuj), w pełni ograniczone przez współrzędnych i usługi radius, lub może być wykonywana w bardziej ogólnie bez żadnych geograficzną punktu kontrolnego odchylenia opóźnienia.

Domyślnie większość zapytań wyszukiwania "maxFuzzyLevel = 1' na wydajność i zmniejszyć nieoczekiwane rezultaty. To ustawienie domyślne można przesłonić, zgodnie z potrzebami każdego żądania, przekazując w parametrze zapytania "maxFuzzyLevel = 2" lub "3".

### <a name="search-for-an-address-using-fuzzy-search"></a>Wyszukiwanie adresu za pomocą wyszukiwania rozmytego

1. Otwórz aplikację Postman, kliknij przycisk Nowa | Utwórz nowy, a następnie wybierz pozycję **żądanie GET**. Wprowadź nazwę żądania **Wyszukiwanie rozmyte**, wybierz kolekcję lub folder, aby zapisać go w celu, a następnie kliknij przycisk **Zapisz**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.

    ![Fuzzy Search ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | https://atlas.microsoft.com/search/fuzzy/json? |
    | Autoryzacja | Nie uwierzytelniania |

    **Json** atrybut ścieżki adresu URL określa format odpowiedzi. Łatwość użycia i czytelności używasz formatu json, w tym artykule. Możesz znaleźć formaty dostępne odpowiedzi w **uzyskać wyszukiwania rozmytego** definicji [dokumentacja funkcjonalny interfejs API map] (https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Kliknij przycisk **Params**, a następnie wprowadź poniższy klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:

    ![Fuzzy Search ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klucz | Wartość |
    |------------------|-------------------------|
    | wersja interfejsu API | 1.0 |
    | klucz subskrypcji | \<Klucz usługi Azure Maps\> |
    | query | pizza |

4. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi. 

    Ciąg zapytania niejednoznaczne "głosi" zwracane 10 punktów wyniki zainteresowania (punktów POI) z kategorii w "głosi" i "restauracji". Każdy wynik zwraca adres ulicy, szerokość geograficzna / wyświetlić wartości długości geograficznej, portów i punktów wejścia do lokalizacji.
    
    Wyniki są zróżnicowane dla tego zapytania nie są powiązane z dowolnej lokalizacji szczególnym odniesieniem. Możesz użyć **countrySet** parametru, aby określić kraje, dla których aplikacja wymaga pokrycia, zgodnie z domyślnym zachowaniem jest na całym świecie, potencjalnie zwracanie niepotrzebnego wyników wyszukiwania.

5. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |------------------|-------------------------|
    | countrySet | USA |
    
    Wyniki są obecnie ograniczone przez kod kraju, a zapytanie zwróci pizza restauracje w Stanach Zjednoczonych.
    
    Aby zapewnić wyniki ukierunkowane na danej lokalizacji, możesz można kwerendy punktu orientacyjnego i używać zwrócone szerokości i długości geograficznej wartości w wywołania do usługi wyszukiwania rozmytego. W tym przypadku używane usługi wyszukiwania w celu zwrócenia lokalizacja wskazówkę miejsca Seattle i używane pomocą tabeli lat. / długa. wartości do zorientowania wyszukiwania.
    
4. W Params, wprowadź następujący klucz / wartość pary i kliknij przycisk **wysyłania**:

    ![Fuzzy Search ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Klucz | Wartość |
    |-----|------------|
    | lat | 47.62039 |
    | długa | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Wyszukiwanie właściwości adresu i współrzędnych 

Można przekazać adres pełną lub częściową adres wyszukiwania interfejsu API i odbierać odpowiedź, która zawiera adres szczegółowe właściwości, takie jak gmina lub części, a także wartości pozycyjnych w szerokości i długości geograficznej.

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **wyszukiwanie adresu**.
2. Na karcie Builder wybierz **UZYSKAĆ** metodę HTTP, wprowadź adres URL żądania dla punktu końcowego interfejsu API i wybierz protokół autoryzacji, jeśli istnieje.

    ![Wyszukiwanie adresu ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | https://atlas.microsoft.com/search/address/json? |
    | Autoryzacja | Nie uwierzytelniania |

2. Kliknij przycisk **Params**, a następnie wprowadź poniższy klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
    
    ![Wyszukiwanie adresu ](./media/how-to-search-for-address/address_search_params.png)
    
    | Klucz | Wartość |
    |------------------|-------------------------|
    | wersja interfejsu API | 1.0 |
    | klucz subskrypcji | \<Klucz usługi Azure Maps\> |
    | query | 400 Broad St, Seattle, WA 98109 |
    
3. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi. 
    
    W tym przypadku określone zapytanie kompletny adres i otrzymywać jeden wynik w treści odpowiedzi. 
    
4. W Params Edytuj ciąg zapytania do następującej wartości:
    ```
        400 Broad, Seattle
    ```

5. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | typeahead | true |

    **Typeahead** flagi informuje interfejsu API wyszukiwania adresów do traktowania zapytania jako częściowe dane wejściowe i zwraca tablicę wartości predykcyjne.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Wyszukaj adres ulicy, używając odwrotnego wyszukiwania adresu
1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **odwrotnego wyszukiwania adresu**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.
    
    ![Adres URL wyszukiwania wstecznego ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | https://atlas.microsoft.com/search/address/reverse/json? |
    | Autoryzacja | Nie uwierzytelniania |
    
2. Kliknij przycisk **Params**, a następnie wprowadź poniższy klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
    
    ![Adres parametry wyszukiwania wstecznego ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Klucz | Wartość |
    |------------------|-------------------------|
    | wersja interfejsu API | 1.0 |
    | klucz subskrypcji | \<Klucz usługi Azure Maps\> |
    | query | 47.59093,-122.33263 |
    
3. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi. 
    
    Odpowiedź zawiera wpis punktów POI dla Safeco Field z kategorii punktów poi "stadion". 
    
4. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | numer | true |

    Jeśli [numer](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametr zapytania są wysyłane z tym żądaniem, odpowiedź może zawierać boku ulicy (lewo/w prawo), a także przesunięcie pozycji dla tego numeru.
    
5. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | spatialKeys | true |

    Gdy [spatialKeys](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametru zapytania jest ustawiona, odpowiedź zawiera własności geoprzestrzenne informacje o kluczu dla określonej lokalizacji.

6. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | returnSpeedLimit | true |
    
    Gdy [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametru zapytania jest ustawiona, zwraca odpowiedź przesłanych limitu szybkości.

7. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | returnRoadUse | true |

    Gdy [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) ustawiono parametr zapytania, odpowiedzi zwraca macierzy Użyj reversegeocodes na poziomie ulicy w drodze.

8. Dodaj następujący klucz / wartość pary do **Params** sekcji, a następnie kliknij przycisk **wysyłania**:

    | Klucz | Wartość |
    |-----|------------|
    | roadUse | true |

    Zapytanie wsteczne geokodowania można ograniczyć do określonego typu drogowym za pomocą funkcji [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parametr zapytania.
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Wyszukaj między ulica, przy użyciu odwrotnego wyszukiwania ulicy wielu adresów

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **odwrotnego Cross ulicy wyszukiwanie adresu**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.
    
    ![Odwrócone wyszukiwanie ulicy wielu adresów ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Autoryzacja | Nie uwierzytelniania |
    
3. Kliknij przycisk **Params**, a następnie wprowadź poniższy klucz / wartość pary do użycia jako parametry zapytania lub ścieżki w adresie URL żądania:
    
    | Klucz | Wartość |
    |------------------|-------------------------|
    | wersja interfejsu API | 1.0 |
    | klucz subskrypcji | \<Klucz usługi Azure Maps\> |
    | query | 47.59093,-122.33263 |
    
4. Kliknij przycisk **wysyłania** i przejrzeć treść odpowiedzi. 

## <a name="next-steps"></a>Kolejne kroki
- Zapoznaj się z [usługi Azure Maps, Usługa wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) dokumentacji interfejsu API 
