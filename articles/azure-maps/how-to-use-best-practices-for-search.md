---
title: Jak wyszukiwać efektywnie przy użyciu usługi Azure Maps Search | Microsoft Docs
description: Dowiedz się, jak używać najlepszych rozwiązań do wyszukiwania za pomocą usługi Azure Maps Search
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 996a084fd653b2100d94313e8801d915b4bf2cf3
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348174"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Najlepsze rozwiązania dotyczące korzystania z Azure Maps Search Service

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) obejmuje interfejsy API z różnymi możliwościami, na przykład od wyszukiwania adresów do wyszukiwania danych punktów orientacyjnych (punkt POI) wokół określonej lokalizacji. W tym artykule opisano najlepsze rozwiązania dotyczące wywoływania danych za pośrednictwem usług wyszukiwania Azure Maps. Poznasz następujące czynności:

* Twórz zapytania, aby zwracać odpowiednie dopasowania
* Ogranicz wyniki wyszukiwania
* Poznaj różnice między różnymi typami wyników
* Odczytywanie struktury odpowiedzi wyszukiwania adresów


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać wywołania do interfejsów API usługi Maps, musisz mieć konto i klucz mapy. Aby uzyskać informacje na temat tworzenia konta i pobierania klucza, zobacz [jak zarządzać kontem i kluczami Azure Maps](how-to-manage-account-keys.md).

> [!Tip]
> Aby wysłać zapytanie do usługi wyszukiwania, możesz użyć [aplikacji Poster](https://www.getpostman.com/apps) do kompilowania wywołań REST lub można użyć dowolnego preferowanego środowiska DEWELOPERSKIEGO interfejsu API.


## <a name="best-practices-for-geocoding"></a>Najlepsze rozwiązania dotyczące geokodowania

Podczas wyszukiwania pełnych lub częściowych adresów przy użyciu Azure Maps Search Service, pobiera termin wyszukiwania i zwraca współrzędne długości geograficznej adresu. Ten proces jest nazywany geokodowaniem. Możliwość geokodowania w kraju zależy od pokrycia danych drogowych oraz dokładności geokodowanej usługi geokodowania.

Zobacz [pokrycie geokodowania](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) , aby dowiedzieć się więcej o możliwościach geokodowania Azure Maps według kraju/regionu.

### <a name="limit-search-results"></a>Ogranicz wyniki wyszukiwania

   W tej sekcji dowiesz się, jak ograniczyć wyniki wyszukiwania za pomocą interfejsów API wyszukiwania Azure Maps. 

   > [!Note]
   > Nie wszystkie interfejsy API wyszukiwania w pełni obsługują parametry wymienione poniżej

   **Wyniki wyszukiwania z odchyleniami geograficznymi**

   Aby można było rozprowadzić swoje wyniki do odpowiednich obszarów dla użytkownika, należy zawsze dodać maksymalną możliwą liczbę danych wejściowych lokalizacji. Aby ograniczyć wyniki wyszukiwania, należy rozważyć dodanie następujących typów danych wejściowych:

   1. `countrySet` Ustaw parametr, na przykład "US, fr". Domyślnym zachowaniem wyszukiwania jest przeszukanie całego świata, co może spowodować zwrócenie niepotrzebnych wyników. Jeśli zapytanie nie zawiera `countrySet` parametru, wyszukiwanie może zwracać niedokładne wyniki. Na przykład wyszukaj miasto o nazwie **Bellevue** zwróci wyniki z USA i Francja, ponieważ istnieją miasta o nazwie **Bellevue** we Francji i w USA.

   2. Możesz użyć `btmRight` parametrów i `topleft` , aby ustawić pole ograniczenia, aby ograniczyć wyszukiwanie do określonego obszaru na mapie.

   3. Aby mieć wpływ na obszar istotności dla wyników, można zdefiniować `lat`parametry i `lon` współrzędne i ustawić promień obszaru wyszukiwania przy użyciu `radius` parametru.


   **Parametry wyszukiwania rozmytego**

   1. `minFuzzyLevel` I`maxFuzzyLevel`, pomoc zwracają odpowiednie dopasowania nawet wtedy, gdy parametry zapytania nie są dokładnie zgodne z wymaganymi informacjami. Większość zapytań wyszukiwania jest domyślnie `minFuzzyLevel=1` do `maxFuzzyLevel=2` i aby uzyskać wydajność i zmniejszyć nietypowe wyniki. Zapoznaj się z przykładem terminu wyszukiwania "restrant", który jest dopasowywany do "restauracji" `maxFuzzyLevel` , gdy wartość jest równa 2. Domyślne poziomy rozmyte mogą być zastępowane zgodnie z potrzebami żądania. 

   2. Można również określić dokładny zestaw typów wyników do zwrócenia przy użyciu `idxSet` parametru. W tym celu można przesłać listę indeksów rozdzieloną przecinkami, kolejność elementów nie ma znaczenia. Obsługiwane są następujące indeksy:

       * `Addr` - **Zakresy adresów**: W przypadku niektórych ulic istnieją punkty adresów, które są interpolowane od początku i końca ulicy; te punkty są reprezentowane jako zakresy adresów.
       * `Geo` - **Lokalizacje geograficzne**: Obszary na mapie, które reprezentują Wydział administracyjny terenu, czyli kraj, Województwo, miasto.
       * `PAD` - **Adres punktu**:  Wskazuje na mapę, w której określony adres o nazwie ulicy i numerze można znaleźć w indeksie, na przykład Soquel Dr 2501. Jest to najwyższy poziom dokładności dostępny dla adresów.  
       * `POI` - **Ważne punkty**: Wskazuje na mapę, które są cenną uwagę i mogą być interesujące.  [Pobieranie adresu wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nie zwróci punktów POI.  
       * `Str` - **Ulice**: Reprezentacja ulic na mapie.
       * `XStr` - **Cross Streets/** skrzyżowania:  Reprezentacja skrzyżowań; miejsce, w którym dwa ulice przecinają się.


       **Przykłady użycia**:

       * idxSet = punkt POI (tylko interesujące punkty wyszukiwania) 

       * idxSet = PAD, addr (tylko adresy wyszukiwania, PAD = adres punktu, addr = zakres adresów)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Filtr typu Odwróć kod i typ jednostki geograficznej

Podczas przeszukiwania odwrotnego kodu geokodowanego za pomocą [odwrotnego interfejsu API wyszukiwania adresów](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)usługa ma możliwość zwracania wielokątów dla obszarów administracyjnych. Podając parametr `entityType` w żądaniu, można zawęzić wyszukiwanie określonych typów jednostek geograficznych. Uzyskana odpowiedź będzie zawierać identyfikator geografii, a także typ jednostki dopasowanej. Jeśli podano więcej niż jedną jednostkę, punkt końcowy zwróci **najmniejszą dostępną jednostkę**. Zwrócony identyfikator geometrii może służyć do uzyskania geometrii tej lokalizacji geograficznej za pośrednictwem [usługi Get Wielokąt](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Przykładowe żądanie:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Odpowiedź:**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Język wyników wyszukiwania

`language` Parametr umożliwia określenie, w jaki sposób mają być zwracane wyniki wyszukiwania języka. Jeśli w żądaniu nie ustawiono języka, usługa Search automatycznie domyślnie przyjmowana jest do najczęściej używanego języka w kraju/regionie. Ponadto, jeśli dane w określonym języku są niedostępne, używany jest język domyślny. Zobacz [obsługiwane języki](https://docs.microsoft.com/azure/azure-maps/supported-languages) , aby uzyskać listę obsługiwanych języków w odniesieniu do usług Azure Maps według kraju/regionu.


### <a name="predictive-mode-auto-suggest"></a>Tryb predykcyjny (automatyczne sugerowanie)

Aby znaleźć więcej dopasowań dla zapytań częściowych, `typeahead` parametr powinien mieć ustawioną wartość "true". Zapytanie będzie interpretowane jako częściowe dane wejściowe, a wyszukiwanie spowoduje przetworzenie trybu predykcyjnego. W przeciwnym razie usługa przyjmie wszystkie odpowiednie informacje, które zostały przesłane.

W zapytaniu przykładowym poniżej można zobaczyć, że w usłudze adresów wyszukiwania jest wykonywana kwerenda dla "Microso" z `typeahead` parametrem ustawionym na **wartość true**. Jeśli zobaczysz odpowiedź, zobaczysz, że usługa wyszukiwania interpretuje zapytanie jako częściowe zapytanie i odpowiedź zawiera wyniki dla zapytania z sugestią sugerowaną.

**Przykładowe zapytanie:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Odpowiedź:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Kodowanie identyfikatora URI w celu obsługi znaków specjalnych 

Aby znaleźć adresy krzyżowe, to oznacza, że przed wysłaniem żądania należy zakodować "1. drogi & złożenia ulicy, Seattle", znak specjalny "&". Zalecamy kodowanie danych znakowych w identyfikatorze URI, gdzie wszystkie znaki są kodowane przy użyciu znaku "%" i dwuznakowej wartości szesnastkowej odpowiadającej jej znakowi UTF-8.

**Przykłady użycia**:

Pobierz adres wyszukiwania:

```
query=1st Avenue & E 111th St, New York
```

 są kodowane jako:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Oto różne metody używane w różnych językach: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Dopisek
```Ruby
CGI::escape(query) 
```

Adres
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Udać
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Najlepsze rozwiązania dotyczące wyszukiwania punkt POI

Wyszukiwanie punktów orientacyjnych (punkt POI) umożliwia żądanie punkt POI wyników według nazwy, na przykład wyszukiwanie w firmie według nazwy. Zdecydowanie zalecamy użycie `countrySet` parametru, aby określić kraje, w których aplikacja wymaga pokrycia, ponieważ domyślne zachowanie będzie przeszukiwać cały świat, potencjalnie zwracając niepotrzebne wyniki i/lub spowodować dłuższe czasy wyszukiwania.

### <a name="brand-search"></a>Wyszukiwanie marki

Aby poprawić znaczenie wyników i informacje zawarte w odpowiedzi, punkt zainteresowania (punkt POI) odpowiedzi na odpowiedź zawiera informacje o marce, których można użyć do przeanalizowania odpowiedzi.

Przyjrzyjmy się żądaniu [wyszukiwania kategorii punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) dla stacji gazowych blisko Microsoft kampusów (REDMOND, WA). Jeśli zobaczysz odpowiedź, zobaczysz informacje o marce dla każdej zwróconej punkt POI.

**Przykładowe zapytanie:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Odpowiedź:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Wyszukiwanie portów lotniczych

Wyszukiwanie punkt POI obsługuje wyszukiwanie portów lotniczych przy użyciu oficjalnych kodów lotniska. Na przykład **morze** (Seattle-Tacoma International lotniska). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Wyszukiwanie w pobliżu

Aby pobrać tylko wyniki punkt POI wokół określonej lokalizacji, [interfejs API wyszukiwania w pobliżu](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) może być właściwym wyborem. Ten punkt końcowy zwróci tylko wyniki punkt POI i nie przyjmuje parametru zapytania wyszukiwania. Aby ograniczyć wyniki, zaleca się ustawienie promienia.

## <a name="understanding-the-responses"></a>Zrozumienie odpowiedzi

Przekażmy żądanie przeszukiwania adresu do [usługi wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) Azure Maps w adresie w Seattle. Jeśli zobaczysz uważnie pod adresem URL żądania poniżej, ustawimy `countrySet` parametr na wartość **My** , aby wyszukać adres w Stany Zjednoczone.

**Przykładowe zapytanie:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Dalsze Przyjrzyjmy się strukturze odpowiedzi poniżej. Typy wyników obiektów wynikowych w odpowiedzi są różne. Jeśli zobaczysz uważnie, zobaczymy, że mamy trzy różne typy obiektów wynikowych, czyli "adres punktowy", "ulica" i "skrzyżowanie". Zwróć uwagę, że wyszukiwanie adresów nie zwraca punktów POI. `Score` Parametr dla każdego obiektu odpowiedzi wskazuje względny wynik dopasowania do wyników innych obiektów w tej samej odpowiedzi. Zobacz [Uzyskaj adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) , aby dowiedzieć się więcej na temat parametrów obiektu odpowiedzi.

**Obsługiwane typy wyników:**

* **Adres punktu:** Wskazuje na mapę z określonym adresem z nazwą ulicy i numerem. Najwyższy poziom dokładności dostępny dla adresów. 

* **Zakres adresów:**  W przypadku niektórych ulic istnieją punkty adresów, które są interpolowane od początku i końca ulicy; te punkty są reprezentowane jako zakresy adresów. 

* **Geograficzne** Obszary na mapie, które reprezentują Wydział administracyjny terenu, czyli kraj, Województwo, miasto. 

* **PUNKT POI — (punkty orientacyjne):** Wskazuje na mapę, które są cenną uwagę i mogą być interesujące.

* **Ulicy** Reprezentacja ulic na mapie. Adresy są rozpoznawane jako Współrzędna szerokości/długości geograficznej ulicy, która zawiera adres. Nie można przetworzyć numeru domu. 

* **Skrzyżowanie:** Przecięcia. Reprezentacje połączeń; miejsce, w którym dwa ulice przecinają się.

**Odpowiedź:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrii

Gdy typ odpowiedzi jest **geometryczny**, może zawierać identyfikator geometrii, który jest zwracany w obiekcie DataSources w obszarze "geometria" i "ID". Na przykład [Usługa Get Wielokąt](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) umożliwia zażądanie danych geometrii w formacie GEOJSON, takim jak miasto lub wzór lotniska dla zestawu jednostek. Możesz użyć tych danych granicznych dla [geofencingu](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) lub [punktów POI wyszukiwania wewnątrz geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) lub [Wyszukiwanie rozmytej](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) odpowiedzi interfejsu API może zawierać **Identyfikator geometrii** , który jest zwracany w obiekcie DataSources w obszarze "geometria" i "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się [, jak kompilować żądania usługi Search Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Zapoznaj się z [dokumentacją interfejsu API usługi Azure Maps Search](https://docs.microsoft.com/rest/api/maps/search). 
