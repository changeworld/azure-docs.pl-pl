---
title: Wyszukiwanie lokalizacji przy użyciu usług Azure Maps Search | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak wyszukiwać lokalizację przy użyciu usługi Wyszukiwania Map Platformy Microsoft Azure w celu geokodowania i odwrotnego geokodowania.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335428"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Wyszukiwanie lokalizacji przy użyciu usług Azure Maps Search

Usługa Azure Maps [Search to](https://docs.microsoft.com/rest/api/maps/search) zestaw interfejsów API RESTful zaprojektowanych, aby ułatwić deweloperom wyszukiwanie adresów, miejsc, wizyt firm według nazwy lub kategorii oraz innych informacji geograficznych. Oprócz obsługi tradycyjnego geokodowania, usługi mogą również odwracać adresy geokodów i ulice na podstawie szerokości i długości geograficznej. Wartości szerokości i długości geograficznej zwracane przez wyszukiwanie mogą służyć jako parametry w innych usługach usługi Azure Maps, takich jak [usługi Routs i](https://docs.microsoft.com/rest/api/maps/route) [Pogoda.](https://docs.microsoft.com/rest/api/maps/weather)

W tym artykule dowiesz się, jak:

* Żądania współrzędnych szerokości i długości geograficznej dla adresu (lokalizacji adresu geokodowania) przy użyciu [interfejsu API adresu wyszukiwania]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Wyszukiwanie adresu lub punktu zainteresowania (POI) za pomocą [interfejsu API wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Wyszukiwanie adresu wraz z właściwościami i współrzędnymi
* Wyszukiwanie [odwrotnego adresu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) w celu tłumaczenia lokalizacji współrzędnych na adres ulicy
* Wyszukiwanie ulicy krzyżowej za pomocą [interfejsu API Odwróć ulicę adresu wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, należy najpierw utworzyć konto usługi Azure Maps i uzyskać klucz subskrypcji konta map. Postępuj zgodnie z instrukcjami w [tworzenie konta,](quick-demo-map-app.md#create-an-account-with-azure-maps) aby utworzyć subskrypcję konta usługi Azure Maps i wykonaj kroki w [celu uzyskania klucza podstawowego,](quick-demo-map-app.md#get-the-primary-key-for-your-account) aby uzyskać klucz podstawowy dla swojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).

W tym artykule używa [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Można użyć dowolnego środowiska programistycznego interfejsu API, które wolisz.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Żądanie szerokości i długości geograficznej dla adresu (geokodowanie)

W tym przykładzie używamy interfejsu API adresu wyszukiwania usługi Azure Maps [Pobierz adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) do konwersji adresu ulicy na współrzędne szerokości i długości geograficznej. Można przekazać pełny lub częściowy adres ulicy do interfejsu API i otrzymać odpowiedź, która zawiera szczegółowe właściwości adresu, takie jak ulica, kod pocztowy i kraj/region, a także wartości pozycyjne w szerokości i długości geograficznej.

Jeśli masz zestaw adresów do geokodowania, możesz użyć [interfejsu API partii post search address batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) do wysyłania partii zapytań w jednym wywołaniu interfejsu API.

1. W polu Postman kliknij pozycję **Żądanie NOWE ŻĄDANIE** | **GET** i nazwij go **Wyszukiwanie adresowe**.

2. Na karcie Konstruktor wybierz metodę **POBIERZ** HTTP, wprowadź adres URL żądania dla punktu końcowego interfejsu API i wybierz protokół autoryzacji, jeśli istnieje.

![Wyszukiwanie adresów](./media/how-to-search-for-address/address_search_url.png)

| Parametr | Sugerowana wartość |
|---------------|------------------------------------------------| 
| Metoda HTTP | GET |
| Adres URL żądania | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autoryzacja | Nie Ów ów |

3. Kliknij **params**i wprowadź następujące pary klucz / wartość, które mają być używane jako parametry kwerendy lub ścieżki w adresie URL żądania: 

![Wyszukiwanie adresów](./media/how-to-search-for-address/address_search_params.png) 

| Klucz | Wartość | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| klucz subskrypcji | \<klucz usługi Azure Maps\> | 
| query | 400 Broad St, Seattle, WA 98109 | 

4. Kliknij **przycisk Wyślij** i przejrzyj treść odpowiedzi. 

W takim przypadku określono pełną kwerendę adresową i otrzymano pojedynczy wynik w treści odpowiedzi. 

5. W params, edytuj ciąg zapytania do następującej wartości: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Dodaj następującą parę Klucz / Wartość do sekcji **Params** i kliknij przycisk **Wyślij:** 

| Klucz | Wartość | 
|-----|------------| 
| Typeahead | true | 

Flaga **typeahead** informuje interfejs API wyszukiwania adresów, aby traktować kwerendę jako częściowe dane wejściowe i zwracać tablicę wartości predykcyjnych.

## <a name="using-fuzzy-search-api"></a>Korzystanie z interfejsu API wyszukiwania rozmytego

Interfejs API usługi Azure Maps[ Fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) jest zalecaną usługą do użycia, gdy nie wiesz, jakie są dane wejściowe użytkownika dla zapytania wyszukiwania. Interfejs API łączy wyszukiwanie i geokodowanie punktu zainteresowania (POI) w kanoniczne "wyszukiwanie jednowierszowe". Na przykład interfejs API może obsługiwać dane wejściowe dowolnej kombinacji tokenu adresu lub CZOŁdu. Może być również ważony pozycją kontekstową (lat./lon. para), w pełni ograniczona współrzędną i promieniem lub wykonywana bardziej ogólnie bez żadnego punktu kontrolnego odchylenia geograficznego.

Większość zapytań wyszukiwania `maxFuzzyLevel=1` domyślnie, aby uzyskać wydajność i zmniejszyć nietypowe wyniki. Tę wartość domyślną można zastąpić zgodnie z potrzebami `maxFuzzyLevel=2` `3`na żądanie, przekazując parametr kwerendy lub .

### <a name="search-for-an-address-using-fuzzy-search"></a>Wyszukiwanie adresu przy użyciu wyszukiwania rozmytego

1. Otwórz aplikację Listonosz, kliknij pozycję Nowy | Utwórz nowy i wybierz **żądanie GET**. Wprowadź nazwę żądania **wyszukiwania rozmytego,** wybierz kolekcję lub folder, w który chcesz ją zapisać, a następnie kliknij przycisk **Zapisz**.

2. Na karcie Konstruktor wybierz metodę **GET** HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.

    ![Rozmyte wyszukiwanie](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autoryzacja | Nie Ów ów |

    Atrybut **json** w ścieżce adresu URL określa format odpowiedzi. W tym artykule używa json dla ułatwienia użytkowania i czytelności. Dostępne formaty odpowiedzi można znaleźć w definicji **Get Search Fuzzy** [odwołania do funkcjonalnego interfejsu API map](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Kliknij **params**i wprowadź następujące pary klucz / wartość, które mają być używane jako parametry kwerendy lub ścieżki w adresie URL żądania:

    ![Rozmyte wyszukiwanie](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | klucz subskrypcji | \<klucz usługi Azure Maps\> |
    | query | Pizza |

4. Kliknij **przycisk Wyślij** i przejrzyj treść odpowiedzi.

    Niejednoznaczny ciąg zapytania dla "pizza" zwrócił [wynik](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) 10 punktów zainteresowania (POI) w kategoriach "pizza" i "restaurant". Każdy wynik zwraca adres ulicy, wartości szerokości i długości geograficznej, port widoku i punkty wejścia dla lokalizacji.
  
    Wyniki są zróżnicowane dla tej kwerendy, nie są powiązane z żadną konkretną lokalizacją referencyjną. Za pomocą parametru **countrySet** można określić tylko kraje/regiony, dla których aplikacja wymaga ochrony. Domyślnym zachowaniem jest wyszukiwanie całego świata, potencjalnie zwracając niepotrzebne wyniki.

5. Dodaj następującą parę Klucz / Wartość do sekcji **Params** i kliknij przycisk **Wyślij:**

    | Klucz | Wartość |
    |------------------|-------------------------|
    | krajStaw | USA |
  
    Wyniki są teraz ograniczone przez kod kraju i kwerenda zwraca pizzerii w Stanach Zjednoczonych.
  
    Aby zapewnić wyniki dla lokalizacji, można zbadać punkt zainteresowania i użyć zwracanych wartości szerokości i długości geograficznej w wywołaniu usługi wyszukiwania rozmytego. W takim przypadku użyto usługi wyszukiwania, aby zwrócić lokalizację Seattle Space Needle i używane łat. / lon. wartości, aby zorientować wyszukiwanie.
  
6. W parach parajowych wprowadź następujące pary kluczy/ wartości i kliknij przycisk **Wyślij**:

    ![Rozmyte wyszukiwanie](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Klucz | Wartość |
    |-----|------------|
    | lat | 47.620525 |
    | Lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Wyszukiwanie adresu ulicy przy użyciu wyszukiwania odwrotnego adresu

Interfejs [API odwrotnego adresu wyszukiwania]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) usługi Azure Maps umożliwia przetłumaczenie współrzędnych (np. 37.786505, -122.3862) na adres ulicy zrozumiałej dla człowieka. Najczęściej jest to potrzebne w aplikacjach śledzących, w których otrzymujesz kanał GPS z urządzenia lub zasobu i chcesz wiedzieć, jaki adres znajduje się współrzędna.
Jeśli masz zestaw lokalizacji współrzędnych do odwrócenia geokodu, możesz użyć [interfejsu API odwrotnej partii adresu wyszukiwania,](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) aby wysłać partię zapytań w jednym wywołaniu interfejsu API.


1. W polu Postman kliknij pozycję **Żądanie NOWE ŻĄDANIE** | **GET** i nazwij go Reverse **Address Search**.

2. Na karcie Konstruktor wybierz metodę **GET** HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.
  
    ![Odwrotny adres URL wyszukiwania adresów](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autoryzacja | Nie Ów ów |
  
3. Kliknij **params**i wprowadź następujące pary klucz / wartość, które mają być używane jako parametry kwerendy lub ścieżki w adresie URL żądania:
  
    ![Parametry wyszukiwania adresu zwrotnego](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | klucz subskrypcji | \<klucz usługi Azure Maps\> |
    | query | 47.591180,-122.332700 |
  
4. Kliknij **przycisk Wyślij** i przejrzyj treść odpowiedzi.

    Odpowiedź zawiera kluczowe informacje adresowe dotyczące Safeco Field.
  
5. Dodaj następującą parę Klucz / Wartość do sekcji **Params** i kliknij przycisk **Wyślij:**

    | Klucz | Wartość |
    |-----|------------|
    | numer | true |

    Jeśli parametr kwerendy [numerycznej](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) jest wysyłany z żądaniem, odpowiedź może zawierać stronę ulicy (lewą lub prawą), a także pozycję odsunięcia dla tego numeru.
  
6. Dodaj następującą parę Klucz / Wartość do sekcji **Params** i kliknij przycisk **Wyślij:**

    | Klucz | Wartość |
    |-----|------------|
    | zwrotSpeedLimit | true |
  
    Po ustawieniu parametru kwerendy [returnspeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odpowiedź zwraca zaksięgowaną prędkość.

7. Dodaj następującą parę Klucz / Wartość do sekcji **Params** i kliknij przycisk **Wyślij:**

    | Klucz | Wartość |
    |-----|------------|
    | returnRoadUse | true |

    Po ustawieniu parametru kwerendy [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odpowiedź zwraca tablicę użycia drogi dla wstecznych geokodów na poziomie ulicy.

8. Dodaj następującą parę Klucz / Wartość do sekcji **Params** i kliknij przycisk **Wyślij:**

    | Klucz | Wartość |
    |-----|------------|
    | drogaUżyj | true |

    Kwerendę geokodowania odwrotnej można ograniczyć do określonego typu drogi przy użyciu parametru zapytania [roadUse.](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Wyszukaj ulicę krzyżową za pomocą wyszukiwania odwrotnego adresu cross street

1. W polu Listonosz kliknij pozycję **Żądanie nowego żądania** | **GET** i nazwij go Reverse Address Cross **Street Search**.

2. Na karcie Konstruktor wybierz metodę **GET** HTTP i wprowadź adres URL żądania dla punktu końcowego interfejsu API.
  
    ![Odwrotny adres Cross Street Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Sugerowana wartość |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adres URL żądania | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autoryzacja | Nie Ów ów |
  
3. Kliknij **params**i wprowadź następujące pary klucz / wartość, które mają być używane jako parametry kwerendy lub ścieżki w adresie URL żądania:
  
    | Klucz | Wartość |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | klucz subskrypcji | \<klucz usługi Azure Maps\> |
    | query | 47.591180,-122.332700 |
  
4. Kliknij **przycisk Wyślij** i przejrzyj treść odpowiedzi.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [dokumentacją interfejsu API REST usługi wyszukiwania usług Azure Maps.](https://docs.microsoft.com/rest/api/maps/search)
- Dowiedz się więcej o [najlepszych rozwiązaniach usługi Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) i o tym, jak zoptymalizować zapytania.
