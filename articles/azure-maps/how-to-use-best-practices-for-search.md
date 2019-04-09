---
title: Jak wyszukiwać wydajnie za pomocą usługi Azure Maps wyszukiwania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak stosować najlepsze rozwiązania dla wyszukiwania przy użyciu usługi Azure Search mapy
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3a9c5ad92494dd82500c4faee82c119e99346c7a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288159"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Najlepsze rozwiązania usługi Azure Maps Search

Usługi Azure Maps [usługi wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) obejmuje funkcje interfejsu API przy użyciu różnych funkcji, na przykład z wyszukiwanie adresu do wyszukiwania punktu zainteresowania (GROT) dane dotyczące konkretnej lokalizacji. W tym artykule udostępnimy najlepsze rozwiązania dla wywołań dane za pomocą usługi Azure Maps wyszukiwania usług. Poznasz następujące czynności:

* Tworzyć zapytania do zwrócenia odpowiednie dopasowania
* Limit wyników wyszukiwania
* Dowiedz się, różnica między różne typy wyników
* Przeczytaj struktura odpowiedzi wyszukiwania adresów


## <a name="prerequisites"></a>Wymagania wstępne

Aby wprowadzić wszelkie wywołania z usługą mapy interfejsów API, należy mapy konta i klucz. Aby uzyskać informacji na temat tworzenia konta usługi i pobierania klucza, zobacz [jak zarządzać swoim kontem usługi Azure Maps i klucze](how-to-manage-account-keys.md).

> [!Tip]
> Aby wysłać zapytanie usługi wyszukiwania, można użyć [aplikacji Postman](https://www.getpostman.com/apps) tworzenie REST wywołania lub możesz użyć dowolnego interfejsu API środowisko programistyczne, którego chcesz używać.


## <a name="best-practices-for-geocoding"></a>Najlepsze rozwiązania w celu Geokodowania

Podczas wyszukiwania dla pełnej lub częściowej adresu, przy użyciu usługi Azure Maps Search przyjmuje wyszukiwanego terminu i zwraca współrzędne długości i szerokości geograficznej adresu. Ten proces jest nazywany geokodowania. Możliwość geokodowania w kraju, zależy od zakresu danych podróży i dokładności geokodowania usługi geokodowania.

Zobacz [pokrycie geokodowaniem](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) Aby dowiedzieć się więcej o możliwościach geokodowania usługi Azure Maps według kraju/regionu.

### <a name="limit-search-results"></a>Limit wyników wyszukiwania

   W tej sekcji dowiesz się, jak użyć usługi Azure Maps wyszukiwania interfejsów API, aby ograniczyć wyniki wyszukiwania. 

   > [!Note]
   > Wyszukiwanie nie wszystkie interfejsy API obsługują w pełni parametrami wymienionymi poniżej

   **Wyniki wyszukiwania odchylenie geograficznie**

   W celu geograficznego odchylenie wyniki do odpowiedniego obszaru dla użytkownika, należy zawsze dodać maksymalną liczbę wierzy szczegółowe dane wejściowe lokalizacji. Aby ograniczyć wyniki wyszukiwania, należy rozważyć dodanie następujących typów danych wejściowych:

   1. Ustaw `countrySet` parametru, na przykład "US, FR". Domyślne zachowanie wyszukiwania jest na całym świecie, potencjalnie zwracanie niepotrzebnego wyników wyszukiwania. Jeśli zapytanie nie obejmuje `countrySet` parametru wyszukiwania mogą zwracać nieprecyzyjne wyniki. Na przykład wyszukaj miasta o nazwie **Bellevue** zwróci wyniki z USA i Francji, ponieważ istnieją miasta o nazwie **Bellevue** we Francji, jak i w USA.

   2. Możesz użyć `btmRight` i `topleft` parametrów umożliwiających ustawianie blokujących pole, aby ograniczyć wyszukiwanie do określonego obszaru na mapie.

   3. Aby wpłynąć na obszarze istotności wyników, można zdefiniować `lat`i `lon` koordynować parametrów i ustaw radius przy użyciu obszaru search `radius` parametru.


   **Parametry wyszukiwania rozmytego**

   1. `minFuzzyLevel` i `maxFuzzyLevel`, pomocy zwracają odpowiednie dopasowania, nawet wtedy, gdy parametry zapytania nie dokładnie odpowiadają żądane informacje. Domyślnie większość zapytań wyszukiwania `minFuzzyLevel=1` i `maxFuzzyLevel=2` wydajność i obniżyć nieoczekiwane rezultaty. Pobranie przykładem wyszukiwany termin "restrant", jest dopasowywany do "restauracji" podczas `maxFuzzyLevel` jest równa 2. Zgodnie z potrzebami żądania można przesłonić domyślne poziomy rozmytego. 

   2. Można również określić dokładny zestaw typy wyników do zwrócenia przy użyciu `idxSet` parametru. W tym celu możesz przesłać rozdzielana przecinkami lista indeksów, kolejność elementów nie ma znaczenia. Indeksy obsługiwane są następujące:

       * `Addr` - **Zakresy adresów**: W przypadku niektórych streets istnieją punkty adresów, które są interpolowane z początku i końcu ulicy; te punkty są reprezentowane jako zakres adresów.
       * `Geo` - **Lokalizacje geograficzne**: Obszary na mapę, która reprezentuje podział administracyjny gruntów, to znaczy, kraj, stan, miasta.
       * `PAD` - **Adres punktu**:  Wskazuje na mapie, gdzie określonego adresu przy użyciu nazwy ulica i numer można znaleźć w indeksie, na przykład 2501 odzyskiwania po awarii Soquel. Jest najwyższy poziom dokładności dostępnych adresów.  
       * `POI` - **Punktów orientacyjnych**: Punktów na mapie, które są warte uwagi i mogą być interesujące.  [Uzyskaj adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nie zwraca POIs.  
       * `Str` - **Mapa Streets**: Reprezentacja Streets (ulice) na mapie.
       * `XStr` - **Mapa Streets/przecięcia granic**:  Reprezentacja punktach transferu; miejsca przecięcia się dwie Streets (ulice).


       **Przykłady użycia**:

       * idxSet = punktów POI (tylko wyszukiwania ważne punkty) 

       * idxSet = konsoli, Addr (wyszukiwania dotyczy tylko KONSOLĘ = adres punktu Addr = zakres adresów)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Odwróć geocode i położenia geograficznego filtr typu jednostki

Podczas przeprowadzania wyszukiwania wstecznego geocode z [adres zwrotny interfejsu API wyszukiwania w](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), usługa ma możliwość zwracają wielokątów dla obszarów administracyjnych. Podając parametr `entityType` w żądaniu, można ograniczyć wyniki wyszukiwania dla typów jednostek w określonej lokalizacji geograficznej. Wynikowe odpowiedzi będzie zawierać identyfikator lokalizacji geograficznej, a także dopasować typ jednostki. Jeśli musisz podać co najmniej jedna jednostka, punkt końcowy będzie zwracać **najmniejszy jednostki, dostępna**. Zwrócony identyfikator geometrii może służyć do Pobierz geometrii tej lokalizacji geograficznej, za pośrednictwem [usługi Pobierz wielokąta](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Przykładowe żądanie:**

```HTTP
https://atlas.microsoft.com/search/address/json?api-version=1.0&subscription-key={subscription-key}&query=MicrosoftWay&entityType=Municipality
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

`language` Parametr można ustawić w polu wyszukiwania języka, który ma zostać zwrócone wyniki. Jeśli w żądaniu nie ustawiono język, usługa search domyślnie automatycznie najbardziej typowe liście wybierz język kraj/region. Ponadto gdy brak dostępnych danych w wybranym języku, używany jest język domyślny. Zobacz [obsługiwane języki](https://docs.microsoft.com/azure/azure-maps/supported-languages) Aby uzyskać listę obsługiwanych języków w odniesieniu do usługi Azure Maps według kraju/regionu.


### <a name="predictive-mode-auto-suggest"></a>Tryb predykcyjne (Zaproponuj automatycznie)

Aby znaleźć więcej dopasowań dla zapytania częściowego `typeHead` parametru powinna być równa się "true". Zapytanie, które będą interpretowane jako częściowe dane wejściowe i wyszukiwania przejdzie w tryb predykcyjne. W przeciwnym razie usługa zakłada, że wszystkie istotne informacje został przekazany w.

W przykładzie poniżej możesz zapytanie znajduje czy wyszukiwania usługi adresu zostaje przesłane zapytanie "Microso" z `typehead` parametr **true**. Jeśli zauważysz odpowiedzi, widać, Usługa wyszukiwania interpretowane zapytanie jako zapytanie częściowe, i odpowiedź zawiera najlepszych wyników zapytania sugerowany automatycznie.

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


### <a name="uri-encoding-to-handle-special-characters"></a>Identyfikator URI kodowanie do obsługi znaków specjalnych 

Aby dowiedzieć się, ulicy wielu adresów oznacza to, że 1 dnia ścieżek & ulicy Unii, Seattle, znaki specjalne ' i ' musi być zakodowany przed wysłaniem żądania. Firma Microsoft zaleca, kodowania danych znak w identyfikatorze URI, w przypadku, gdy wszystkie znaki są zakodowane przy użyciu znaku "%" i wartości szesnastkowej znaku dwóch odpowiadający ich znaków UTF-8.

**Przykłady użycia**:

Uzyskaj adres wyszukiwania:

```
query=1st Avenue & E 111th St, New York shall be encoded as query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York 
```


Oto różnych metod do użycia w różnych językach: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```C#
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

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Odwiedź:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Najlepsze rozwiązania dotyczące wyszukiwania punktu orientacyjnego

Punkty wyszukiwania zainteresowania (punktów POI) umożliwia zażądanie punktów POI wyniki według nazwy, na przykład wyszukiwanie biznesowych według nazwy. Zdecydowanie zalecamy, aby przy użyciu `countrySet` parametru, aby określić kraje, w którym Twoja aplikacja potrzebuje pokrycia, zgodnie z domyślnym zachowaniem będzie cały świat, potencjalnie zwracanie niepotrzebnego wyników wyszukiwania i/lub spowodować wydłużenie czasu wyszukiwania.

### <a name="brand-search"></a>Wyszukaj na marki

Aby poprawić istotności wyników, a informacje w odpowiedzi, odpowiedź wyszukiwania punktu zainteresowania (GROT) zawiera informacje marki można dodatkowo przeanalizować odpowiedzi.

Upewnijmy się [wyszukiwanie według kategorii punktów POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) żądanie benzynowym niemal kampusu firmy Microsoft (Redmond, WA). Jeśli zauważysz odpowiedzi, widać marki informacje dotyczące poszczególnych punktów POI zwracane.

**Przykładowe zapytanie:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas station&limit=3&lat=47.6413362&lon=-122.1327968
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
        {
            "type": "POI",
            "id": "US/POI/p0/7728133",
            "score": 5.663,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "url": "www.76.com/",
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
                "streetNumber": "2421",
                "streetName": "148th Ave NE",
                "municipalitySubdivision": "Redmond, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148th Ave NE, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313
                    }
                }
            ]
        },
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


### <a name="airport-search"></a>Port lotniczy wyszukiwania

Wyszukiwanie punktu orientacyjnego obsługuje wyszukiwanie lotniskach za pomocą oficjalnego kodów lotniczego. Na przykład **Morza** (Seattle Tacoma międzynarodowy port lotniczy). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Wyszukiwanie pobliskiej

Można pobrać tylko punktów POI wyników w okolicy określonej lokalizacji [interfejs API wyszukiwania w pobliżu](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) może być właściwym wyborem. Ten punkt końcowy będzie zwracać tylko wyniki punktów POI, a nie przyjmuje parametr zapytania wyszukiwania. Aby ograniczyć wyniki, zaleca się Ustaw promienia.

## <a name="understanding-the-responses"></a>Informacje o odpowiedzi

Upewnijmy się, żądanie wyszukiwania adresów do usługi Azure Maps [usługi wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) adresu w Seattle. Jeśli przyjrzymy się dokładnie poniższy adres URL żądania, możemy ustawić `countrySet` parametr **USA** wyszukiwanie adresu w Stanach Zjednoczonych.

**Przykładowe zapytanie:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400BroadSt,Seattle,WA&countrySet=US
```

Dodatkowo załóżmy zapoznaj się z odpowiedzi struktura poniższego schematu. Typy wyników obiektów wynikowych w odpowiedzi są różne. Jeśli zauważysz dokładnie czy widać, że mamy trzy różne typy obiektów wynikowych, które są adres punktu, ulicy i wielu ulicy. Zwróć uwagę, że wyszukiwanie tego adresu nie może zwracać POIs. `Score` Parametr dla każdego obiektu odpowiedzi wskazuje względną pasującego oceny oceny innych obiektów w tej samej odpowiedzi. Zobacz [Uzyskaj adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) Aby dowiedzieć się więcej na temat parametrów obiektu odpowiedzi.

**Obsługiwane typy wyników:**

**Adres punktu:** Wskazuje na mapie z określonego adresu przy użyciu nazwy ulica i numer. Najwyższy poziom dokładności dostępnych adresów. 

**Zakres adresów:**  W przypadku niektórych streets istnieją punkty adresów, które są interpolowane z początku i końcu ulicy; te punkty są reprezentowane jako zakres adresów. 

**Lokalizacja geograficzna:** Obszary na mapę, która reprezentuje podział administracyjny gruntów, to znaczy, kraj, stan, miasta. 

**GROT - (punktów orientacyjnych):** Punktów na mapie, które są warte uwagi i mogą być interesujące.

**Ulica:** Reprezentacja Streets (ulice) na mapie. Adresy są rozwiązywane do Współrzędna szerokości/długości geograficznej ulicy, która zawiera adres. Numer domu nie mogą być przetwarzane. 

**Ulica wielu:** Przecięcia. Reprezentacje punktach transferu; miejsca przecięcia się dwie Streets (ulice).

**Odpowiedź:**

```JSON
{
    "summary": {
        "query": "400 broad st seattle wa",
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

Gdy typ odpowiedzi jest **geometrii**, może zawierać identyfikator geometry, który jest zwracany w **źródeł danych** obiekt w obszarze "geometrii" i "id". Na przykład [usługi Pobierz wielokąta](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) umożliwia zażądanie danych geometria, w formacie GeoJSON, na przykład miasta lub port lotniczy konspekt dla zestawu jednostek. Możesz użyć tych danych granic dla [Geofencing](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) lub [POIs wyszukiwania wewnątrz geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Wyszukaj adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) lub [wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) odpowiedzi interfejsu API może obejmować **identyfikator geometrii** zwracanym obiektu źródła danych w obszarze "geometrii" i "id".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [sposób tworzenia żądań obsługi usługi Azure Search mapy](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Zapoznaj się z usługi Azure Maps [dokumentacja interfejsu API usługi wyszukiwania](https://docs.microsoft.com/rest/api/maps/search). 