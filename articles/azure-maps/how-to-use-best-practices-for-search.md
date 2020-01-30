---
title: Efektywne wyszukiwanie przy użyciu Search Service Azure Maps | Mapy Microsoft Azure
description: Dowiedz się, jak stosować najlepsze rozwiązania dla usługi wyszukiwania przy użyciu map Microsoft Azure
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845750"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Najlepsze rozwiązania dotyczące korzystania z Azure Maps Search Service

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) obejmuje interfejsy API z różnymi możliwościami. Na przykład interfejs API wyszukiwania adresów służy do wyszukiwania punktów orientacyjnych (punkt POI) lub danych w określonej lokalizacji. W tym artykule przedstawiono wskazówki dotyczące dźwięku, które należy zastosować podczas wywoływania danych z usług wyszukiwania Azure Maps. Poznasz następujące czynności:

* Twórz zapytania, aby zwracać odpowiednie dopasowania
* Ogranicz wyniki wyszukiwania
* Poznaj różnice między różnymi typami wyników
* Odczytywanie struktury odpowiedzi wyszukiwania adresów


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać wywołania do interfejsów API usługi Maps, musisz mieć konto Azure Maps i klucz. Jeśli potrzebujesz, postępuj zgodnie z instrukcjami w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) i [Uzyskiwanie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account). Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

> [!Tip]
> Aby wysłać zapytanie do usługi wyszukiwania, możesz użyć [aplikacji Poster](https://www.getpostman.com/apps) do kompilowania wywołań REST lub można użyć dowolnego preferowanego środowiska DEWELOPERSKIEGO interfejsu API.


## <a name="best-practices-for-geocoding-address-search"></a>Najlepsze rozwiązania dotyczące geokodowania (wyszukiwanie adresów)

Podczas wyszukiwania pełnego lub częściowego adresu przy użyciu Azure Maps Search Service, interfejs API odczytuje słowa kluczowe z zapytania wyszukiwania i zwraca współrzędne długości geograficznej i szerokości geograficznej adresu. Ten proces jest nazywany geokodowaniem. Możliwość geokodowania w kraju zależy od pokrycia danych drogowych oraz dokładności geokodowanej usługi geokodowania.

Zobacz [pokrycie geokodowania](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) , aby dowiedzieć się więcej o możliwościach geokodowania Azure Maps według kraju/regionu.

### <a name="limit-search-results"></a>Ogranicz wyniki wyszukiwania

 Interfejs API wyszukiwania Azure Maps może pomóc w odpowiednim ograniczeniu wyników wyszukiwania, aby można było wyświetlać odpowiednie dane dla użytkowników.

   > [!Note]
   > Nie wszystkie parametry obsługiwane przez interfejsy API wyszukiwania są wymienione poniżej

   **Wyniki wyszukiwania z odchyleniami geograficznymi**

   Aby można było rozprowadzić swoje wyniki do odpowiednich obszarów dla użytkownika, należy zawsze dodać maksymalną możliwą liczbę danych wejściowych lokalizacji. Aby ograniczyć wyniki wyszukiwania, należy rozważyć dodanie następujących typów danych wejściowych:

   1. Ustaw parametr `countrySet`, na przykład "US, FR". Domyślnym zachowaniem wyszukiwania jest przeszukanie całego świata, co może spowodować zwrócenie niepotrzebnych wyników. Jeśli zapytanie zawiera parametr `countrySet`, wyszukiwanie może zwracać niedokładne wyniki. Na przykład wyszukaj miasto o nazwie **Bellevue** zwróci wyniki z USA i Francja, ponieważ istnieją miasta o nazwie **Bellevue** w bulionie Francja i USA.

   2. Możesz użyć parametrów `btmRight` i `topleft`, aby ustawić pole ograniczenia, aby ograniczyć wyszukiwanie do określonego obszaru na mapie.

   3. Aby mieć wpływ na obszar istotności dla wyników, można zdefiniować parametry koordynacji `lat`i `lon` i ustawić promień obszaru wyszukiwania przy użyciu parametru `radius`.


   **Parametry wyszukiwania rozmytego**
   
  Azure Maps [interfejs API wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) jest zalecaną usługą, która jest używana, gdy nie wiesz, jak dane wejściowe użytkownika są przeznaczone dla zapytania wyszukiwania. Interfejs API łączy funkcję wyszukiwania (punkt POI) z wyszukiwaniem i geokodowaniem do kanonicznego *wyszukiwania jednowierszowego*.

   1. `minFuzzyLevel` i `maxFuzzyLevel` pomoc zwracają odpowiednie dopasowania nawet wtedy, gdy parametry zapytania nie pasują dokładnie do pożądanych informacji. Aby uzyskać wydajność i zmniejszyć nietypowe wyniki, domyślne zapytania wyszukiwania do `minFuzzyLevel=1` i `maxFuzzyLevel=2`. Zapoznaj się z przykładem terminu wyszukiwania "restrant", który jest dopasowywany do "restauracji", gdy `maxFuzzyLevel` ma wartość 2. Domyślne poziomy rozmyte można przesłonić w razie konieczności.  

   2. Można również określić priorytety dokładnego zestawu typów wyników do zwrócenia przy użyciu parametru `idxSet`. W tym celu można przesłać listę indeksów rozdzieloną przecinkami. kolejność elementów nie ma znaczenia. Obsługiwane są następujące indeksy:

       * `Addr`**zakresy adresów** - : w przypadku niektórych ulic istnieją punkty adresów, które są interpolowane od początku i końca ulicy. Te punkty są reprezentowane jako zakresy adresów.
       * `Geo` - **lokalizacje geograficzne**: obszary na mapie, które reprezentują Wydział administracyjny terenu, czyli kraj, Województwo, miasto.
       * **adres punktu** - `PAD`: wskazuje na mapę, w której określony adres o nazwie ulicy i numerze można znaleźć w indeksie, na przykład Soquel Dr 2501. Ta wartość idxSet jest najwyższym poziomem dokładności dostępnym dla adresów.  
       * `POI` - **zainteresowania**: wskazuje na mapę, które są cenną uwagę i mogą być interesujące.  [Pobieranie adresu wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nie zwróci punktów POI.  
       * `Str` - **Streets**: reprezentacja ulic na mapie.
       * `XStr` - **krzyżówek/przecięć**: reprezentacja skrzyżowań; miejsce, w którym dwa ulice przecinają się.


       **Przykłady użycia**:

       * idxSet = punkt POI (tylko interesujące punkty wyszukiwania) 

       * idxSet = PAD, addr (tylko adresy wyszukiwania, PAD = adres punktu, addr = zakres adresów)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Filtr typu Odwróć kod i typ jednostki geograficznej

Podczas przeszukiwania odwrotnego kodu geokodowanego za pomocą [odwrotnego interfejsu API wyszukiwania adresów](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)usługa ma możliwość zwracania wielokątów dla obszarów administracyjnych. Podając parametr `entityType` w żądaniu, można zawęzić wyszukiwanie dla określonych typów jednostek geograficznych. Odpowiedź z wynikiem będzie zawierać identyfikator geografii i pasujący typ jednostki. Jeśli podano więcej niż jedną jednostkę, punkt końcowy zwróci **najmniejszą dostępną jednostkę**. Zwrócony identyfikator geometrii może służyć do uzyskania geometrii tej lokalizacji geograficznej za pośrednictwem [usługi Get Wielokąt](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Przykładowe żądanie:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Odpowiedź:**

```JSON
{
    "summary": {
        "queryTime": 14,
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

Parametr `language` umożliwia wybranie języka wyników zwracanych przez interfejs API. Jeśli język nie jest ustawiony w żądaniu, usługa wyszukiwania automatycznie domyślnie będzie najczęściej używanym językiem w kraju/regionie. Ponadto, jeśli dane w określonym języku są niedostępne, używany jest język domyślny. Aby uzyskać listę obsługiwanych języków z usług Azure Maps Services według kraju/regionu, zobacz [obsługiwane języki](https://docs.microsoft.com/azure/azure-maps/supported-languages) .


### <a name="predictive-mode-autosuggest"></a>Tryb predykcyjny (automatyczne sugerowanie)

Aby znaleźć więcej dopasowań dla zapytań częściowych, parametr `typeahead` powinien mieć ustawioną wartość "true". Zapytanie będzie interpretowane jako częściowe dane wejściowe, a wyszukiwanie spowoduje przetworzenie trybu predykcyjnego. W przeciwnym razie usługa przyjmie wszystkie odpowiednie informacje, które zostały przesłane.

W zapytaniu przykładowym poniżej można zobaczyć, że dla usługi adres wyszukiwania jest wykonywana kwerenda "Microso" z parametrem `typeahead` ustawionym na **wartość true**. Jeśli zobaczysz odpowiedź, możesz zobaczyć, że usługa wyszukiwania interpretuje zapytanie jako częściowe zapytanie. Odpowiedź zawiera wyniki dla zapytania z sugestią sugerowaną.

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
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Kodowanie identyfikatora URI w celu obsługi znaków specjalnych 

Aby znaleźć adresy krzyżowe, należy zakodować identyfikator URI w celu obsługi znaków specjalnych w adresie. Rozważmy ten przykład adresu: "& 1 Przed wysłaniem żądania należy zakodować znak specjalny "&". Zalecamy kodowanie danych znakowych w identyfikatorze URI, gdzie wszystkie znaki są kodowane przy użyciu znaku "%" i dwuznakowej wartości szesnastkowej odpowiadającej jej znakowi UTF-8.

**Przykłady użycia**:

Pobierz adres wyszukiwania:

```
query=1st Avenue & E 111th St, New York
```

 są kodowane jako:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
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

Wyszukiwanie punktów orientacyjnych (punkt POI) umożliwia żądanie punkt POI wyników według nazwy, na przykład wyszukiwanie w firmie według nazwy. Zdecydowanie zalecamy użycie parametru `countrySet`, aby określić kraje, w których aplikacja wymaga pokrycia, ponieważ domyślne zachowanie będzie przeszukiwać cały świat, potencjalnie zwracając niepotrzebne wyniki i/lub spowodować dłuższe czasy wyszukiwania.

### <a name="brand-search"></a>Wyszukiwanie marki

Aby poprawić znaczenie wyników i informacje zawarte w odpowiedzi, punkt zainteresowania (punkt POI) odpowiedzi na odpowiedź zawiera informacje o marce, których można użyć do przeanalizowania odpowiedzi.

W żądaniu można również przesłać listę nazw marek rozdzielonych przecinkami. Możesz użyć listy, aby ograniczyć wyniki do określonych marek przy użyciu parametru `brandSet`. Kolejność elementów nie ma znaczenia. Jeśli podano wiele marek, zwracane są tylko wyniki należące do (co najmniej jednej z podanych list).

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
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
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

Przekażmy żądanie przeszukiwania adresu do [usługi wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) Azure Maps w adresie w Seattle. Jeśli zobaczysz uważnie pod adresem URL żądania poniżej, ustawimy parametr `countrySet` **, aby** wyszukać adres w Stany Zjednoczone w Ameryce Północnej.

**Przykładowe zapytanie:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Dalsze Przyjrzyjmy się strukturze odpowiedzi poniżej. Typy wyników obiektów wynikowych w odpowiedzi są różne. Jeśli zobaczysz uważnie, zobaczymy, że mamy trzy różne typy obiektów wynikowych, czyli "adres punktowy", "ulica" i "skrzyżowanie". Zwróć uwagę, że wyszukiwanie adresów nie zwraca punktów POI. Parametr `Score` dla każdego obiektu odpowiedzi wskazuje względny wynik dopasowania do wyników innych obiektów w tej samej odpowiedzi. Zobacz [Uzyskaj adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) , aby dowiedzieć się więcej na temat parametrów obiektu odpowiedzi.

**Obsługiwane typy wyników:**

* **Adres punktu:** Wskazuje na mapę z określonym adresem z nazwą ulicy i numerem. Najwyższy poziom dokładności dostępny dla adresów. 

* **Zakres adresów:**  W przypadku niektórych ulic istnieją punkty adresów, które są interpolowane od początku i końca ulicy; te punkty są reprezentowane jako zakresy adresów. 

* **Lokalizacja geograficzna:** Obszary na mapie, które reprezentują Wydział administracyjny terenu, czyli kraj, Województwo, miasto. 

* **Punkt POI — (punkty orientacyjne):** Wskazuje na mapę, które są cenną uwagę i mogą być interesujące.

* **Ulica:** Reprezentacja ulic na mapie. Adresy są rozpoznawane jako Współrzędna szerokości/długości geograficznej ulicy, która zawiera adres. Nie można przetworzyć numeru domu. 

* **Skrzyżowanie:** Przecięcia. Reprezentacje połączeń; miejsce, w którym dwa ulice przecinają się.

**Odpowiedź:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrii

Gdy typ odpowiedzi jest **geometryczny**, może zawierać identyfikator geometrii, który jest zwracany w obiekcie **DataSources** w obszarze "geometria" i "ID". Na przykład [Usługa Get Wielokąt](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) umożliwia zażądanie danych geometrii w formacie GEOJSON. Takie jak miasto lub konspekt lotniska dla zestawu jednostek. Możesz użyć tych danych granicznych dla [geofencingu](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) lub [punktów POI wyszukiwania wewnątrz geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) lub [Wyszukiwanie rozmytej](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) odpowiedzi interfejsu API może zawierać **Identyfikator geometrii** , który jest zwracany w obiekcie DataSources w obszarze "geometria" i "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak kompilować żądania usługi Search Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Zapoznaj się z [dokumentacją interfejsu API usługi Azure Maps Search](https://docs.microsoft.com/rest/api/maps/search). 
