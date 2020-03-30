---
title: Najważniejsze wskazówki dotyczące usługi Azure Maps Search Service | Mapy platformy Microsoft Azure
description: Dowiedz się, jak zastosować najlepsze rozwiązania podczas korzystania z usługi wyszukiwania w usługach Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335309"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Najważniejsze wskazówki dotyczące usługi Azure Maps Search

Usługa [Azure](https://docs.microsoft.com/rest/api/maps/search) Maps Search Service zawiera interfejsy API, które oferują różne możliwości. Na przykład interfejs API adresu wyszukiwania może znajdować punkty szczególne (POI) lub dane wokół określonej lokalizacji. 

W tym artykule wyjaśniono, jak stosować rozsądne praktyki podczas wywoływania danych z usługi Azure Maps Search Service. Omawiane tematy:

* Twórz zapytania, aby zwrócić odpowiednie dopasowania.
* Ogranicz wyniki wyszukiwania.
* Poznaj różnice między typami wyników.
* Przeczytaj strukturę wyszukiwania adresów i odpowiedzi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby nawiązywać połączenia z interfejsami API usługi Azure Maps, potrzebujesz konta usługi Azure Maps i klucza. Aby uzyskać więcej informacji, zobacz [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) i Uzyskaj [klucz podstawowy](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Aby uzyskać informacje dotyczące uwierzytelniania w usłudze Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).

> [!TIP]
> Aby zbadać usługę wyszukiwania, można użyć [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Możesz też użyć dowolnego środowiska programistycznego interfejsu API, które wolisz.

## <a name="best-practices-to-geocode-addresses"></a>Najważniejsze wskazówki dotyczące adresów geokodowych

Podczas wyszukiwania pełnego lub częściowego adresu przy użyciu usługi Azure Maps Search Service interfejsu API odczytuje słowa kluczowe z zapytania wyszukiwania. Następnie zwraca współrzędne długości i szerokości geograficznej adresu. Ten proces jest nazywany *geokodowaniem*. 

Możliwość geokodowania w danym kraju zależy od dostępności danych drogowych i dokładności usługi geokodowania. Aby uzyskać więcej informacji na temat możliwości geokodowania usługi Azure Maps według kraju lub regionu, zobacz [Pokrycie geokodowania](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Ogranicz wyniki wyszukiwania

 Interfejs API usługi Azure Maps Search może pomóc w odpowiednim ograniczeniu wyników wyszukiwania. Ogranicz wyniki, aby można było wyświetlać odpowiednie dane użytkownikom.

> [!NOTE]
> Interfejsy API wyszukiwania obsługują więcej parametrów niż tylko te, które omówiono w tym artykule.

#### <a name="geobiased-search-results"></a>Geobiased wyniki wyszukiwania

Aby geobias wyniki do odpowiedniego obszaru dla użytkownika, zawsze dodać jak najwięcej szczegółów lokalizacji, jak to możliwe. Można ograniczyć wyniki wyszukiwania, określając niektóre typy danych wejściowych:

* Ustaw `countrySet` parametr. Można go ustawić `US,FR`na przykład. Domyślnie interfejs API przeszukuje cały świat, dzięki czemu może zwracać niepotrzebne wyniki. Jeśli zapytanie `countrySet` nie ma parametru, wyszukiwanie może zwrócić niedokładne wyniki. Na przykład wyszukiwanie miasta o nazwie *Bellevue* zwraca wyniki z USA i Francji, ponieważ oba kraje zawierają miasto o nazwie *Bellevue*.

* Można użyć `btmRight` i `topleft` parametry, aby ustawić obwiednię. Parametry te ograniczają wyszukiwanie do określonego obszaru na mapie.

* Aby wpłynąć na obszar trafności `lat` wyników, należy zdefiniować parametry i `lon` współrzędne. Za `radius` pomocą parametru można ustawić promień obszaru wyszukiwania.


#### <a name="fuzzy-search-parameters"></a>Parametry wyszukiwania rozmytego

Zaleca się użycie interfejsu [API rozmytego usługi](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) Azure Maps Search, gdy nie znasz danych wejściowych użytkownika dla zapytania wyszukiwania. Interfejs API łączy wyszukiwanie i geokodowanie UM w kanoniczne *wyszukiwanie jednowierszowe:* 

* Parametry `minFuzzyLevel` `maxFuzzyLevel` i parametry pomagają zwracać odpowiednie dopasowania, nawet jeśli parametry kwerendy nie są dokładnie zgodne z informacjami, które użytkownik chce. Aby zmaksymalizować wydajność i zmniejszyć nietypowe wyniki, `minFuzzyLevel=1` `maxFuzzyLevel=2`ustaw zapytania wyszukiwania na domyślne i . 

    Na przykład, `maxFuzzyLevel` gdy parametr jest ustawiony na 2, wyszukiwany termin *restrant* jest dopasowywał się do *restauracji*. Domyślne poziomy rozmyte można zastąpić, gdy zajdzie taka potrzeba. 

* Parametr `idxSet` służy do określania priorytetów dokładnego zestawu typów wyników. Aby ustalić priorytety dokładnego zestawu wyników, można przesłać listę indeksów oddzielonych przecinkami. Na liście kolejność elementów nie ma znaczenia. Usługa Azure Maps obsługuje następujące indeksy:

* `Addr` - **Zakresy adresów:** Punkty adresowe interpolowane od początku i końca ulicy. Punkty te są reprezentowane jako zakresy adresów.
* `Geo` - **Obszary geograficzne**: Administracyjne podziały gruntów. Geografia może być na przykład krajem, stanem lub miastem.
* `PAD` - **Adresy punktów:** adresy, które zawierają nazwę ulicy i numer. Adresy punktów można znaleźć w indeksie. Przykładem jest *Soquel Dr 2501*. Adres punktu zapewnia najwyższy poziom dokładności dostępnych adresów.  
* `POI` - **Punkty szczególne**: Punkty na mapie, które są uważane za warte uwagi lub które mogą być interesujące. [Interfejs API adresu wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) nie zwraca usu.  
* `Str` - **Ulice**: Ulice na mapie.
* `XStr` - **Skrzyżowania ulic lub skrzyżowań**: Skrzyżowania lub miejsca, w których przecinają się dwie ulice.


#### <a name="usage-examples"></a>Przykłady użycia

* `idxSet=POI`- Szukaj tylko EDU. 

* `idxSet=PAD,Addr`- Tylko adresy wyszukiwania. `PAD`wskazuje adres punktu i `Addr` wskazuje zakres adresów.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Odwrotny geokodowanie i filtr dla typu jednostki geografii

Po wykonaniu wyszukiwania odwrotnego geokodu w [interfejsie API odwrotnego adresu wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)usługa może zwracać wielokąty dla obszarów administracyjnych.Aby zawęzić wyszukiwanie do określonych `entityType` typów jednostek geografii, należy uwzględnić parametr w żądaniach. 

Wynikowa odpowiedź zawiera identyfikator geografii i typ jednostki, który został dopasowany. Jeśli podasz więcej niż jedną jednostkę, punkt końcowy zwróci *najmniejszą dostępną encję*. Przywrócono identyfikator geometrii, aby uzyskać geometrię geografii za pośrednictwem [usługi Szukaj wielokąta.](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)

#### <a name="sample-request"></a>Przykładowe żądanie

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Odpowiedź

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

### <a name="set-the-results-language"></a>Ustawianie języka wyników

Użyj `language` parametru, aby ustawić język zwróconych wyników wyszukiwania. Jeśli żądanie nie ustawi języka, domyślnie usługa wyszukiwania używa najbardziej najpopularniejszego języka w kraju lub regionie. Jeśli żadne dane nie są dostępne w określonym języku, używany jest język domyślny. 

Aby uzyskać więcej informacji, zobacz [Usługi Azure Maps obsługiwane języki](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="use-predictive-mode-automatic-suggestions"></a>Użyj trybu predykcyjnego (sugestie automatyczne)

Aby znaleźć więcej dopasowań dla `typeahead` kwerend `true`częściowych, ustaw parametr na . Ta kwerenda jest interpretowana jako częściowe dane wejściowe, a wyszukiwanie przechodzi w tryb predykcyjny. Jeśli parametr nie zostanie `typeahead` ustawiony `true`na wartość , usługa zakłada, że wszystkie istotne informacje zostały przekazane.

W poniższej przykładowej kwerendzie usługa Adres wyszukiwania jest wyszukiwana w przypadku *programu Microso*. W tym `typeahead` miejscu `true`parametr ustawiony na . Odpowiedź pokazuje, że usługa wyszukiwania zinterpretowała kwerendę jako kwerendę częściową. Odpowiedź zawiera wyniki dla automatycznie sugerowanej kwerendy.

#### <a name="sample-query"></a>Przykładowe zapytanie

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Odpowiedź

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


### <a name="encode-a-uri-to-handle-special-characters"></a>Kodowanie identyfikatora URI do obsługi znaków specjalnych 

Aby znaleźć adresy ulic krzyżowych, należy zakodować identyfikator URI do obsługi znaków specjalnych w adresie. Rozważmy przykład adresu: *1st Avenue & Union Street, Seattle*. W tym miejscu zakoduj`&`znak ampersand ( ) przed wysłaniem żądania. 

Zaleca się kodowanie danych znaków w identyfikatorze URI. W identyfikatorze URI wszystkie znaki są kodowane`%`przy użyciu znaku procentowego ( ) i dwuznakowej wartości szesnastkowej odpowiadającej kodowi UTF-8 znaków.

#### <a name="usage-examples"></a>Przykłady użycia

Zacznij od tego adresu:

```
query=1st Avenue & E 111th St, New York
```

Zakoduj adres:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Można użyć następujących metod.

JavaScript lub TypeScript:
```Javascript
encodeURIComponent(query)
```

C# lub Visual Basic:
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

Php:
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

Przejdź:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Najważniejsze wskazówki dotyczące wyszukiwania usu

W wyszukiwaniu YM można poprosić o wyniki US POI według nazwy. Na przykład można wyszukiwać firmę według nazwy. 

Zdecydowanie zaleca się użycie `countrySet` tego parametru do określenia krajów, w których aplikacja potrzebuje zasięgu. Domyślnym zachowaniem jest wyszukiwanie całego świata. To szerokie wyszukiwanie może zwrócić niepotrzebne wyniki, a wyszukiwanie może zająć dużo czasu.

### <a name="brand-search"></a>Wyszukiwanie marki

Aby poprawić trafność wyników i informacji w odpowiedzi, odpowiedź na wyszukiwanie YM zawiera informacje o marce. Można użyć tych informacji, aby dodatkowo przeanalizować odpowiedź.

W zgłoszeniu można przesłać listę marek oddzielonych przecinkami. Użyj listy, aby ograniczyć wyniki do `brandSet` określonych marek, ustawiając parametr. Na liście kolejność elementów nie ma znaczenia. Po podaniu wielu list marek zwracane wyniki muszą należeć do co najmniej jednej z twoich list.

Aby zapoznać się z wyszukiwaniem marki, zróbmy żądanie [wyszukiwania kategorii YM.](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) W poniższym przykładzie szukamy stacji benzynowych w pobliżu kampusu firmy Microsoft w Redmond w stanie Waszyngton. Odpowiedź pokazuje informacje o marce dla każdego CHU, który został zwrócony.

#### <a name="sample-query"></a>Przykładowe zapytanie

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Odpowiedź

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


### <a name="airport-search"></a>Wyszukiwanie na lotnisku

Korzystając z interfejsu API usyrkuj poucie, możesz szukać lotnisk, używając ich oficjalnego kodu. Na przykład można użyć *SEA,* aby znaleźć międzynarodowe lotnisko Seattle-Tacoma: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Wyszukiwanie w pobliżu

Aby pobrać wyniki US WOKÓŁ określonej lokalizacji, możesz spróbować użyć [interfejsu API wyszukiwania w pobliżu](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). Punkt końcowy zwraca tylko wyniki YM. Nie przyjmuje parametru zapytania wyszukiwania. 

Aby ograniczyć wyniki, zaleca się ustawienie promienia.

## <a name="understanding-the-responses"></a>Zrozumienie odpowiedzi

Znajdźmy adres w Seattle, składając żądanie wyszukiwania adresów w usłudze wyszukiwania w usługach Azure Maps Search. W poniższym adresie URL `countrySet` żądania `US` ustawiamy parametr, aby wyszukać adres w USA.

### <a name="sample-query"></a>Przykładowe zapytanie

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Obsługiwane typy wyników

* **Adres punktu:** Punkty na mapie, które mają określony adres z nazwą i numerem ulicy. Adres punktowy zapewnia najwyższy poziom dokładności adresów. 

* **Zakres adresów:** Zakres punktów adresowych interpolowanych od początku i końca ulicy.  

* **Geografia:** Obszary na mapie reprezentujące administracyjne podziały gruntu, na przykład kraju, stanu lub miasta. 

* **US**: Punkty na mapie, które są warte uwagi i które mogą być interesujące.

* **Ulica**: Ulice na mapie. Adresy są rozpoznawane na współrzędne szerokości i długości geograficznej ulicy, która zawiera adres. Numer domu może nie zostać przetworzony. 

* **Ulica Krzyżowa**: Skrzyżowania. Ulice krzyżowe reprezentują skrzyżowania, na których przecinają się dwie ulice.

### <a name="response"></a>Odpowiedź

Przyjrzyjmy się strukturze odpowiedzi. W odpowiedzi, która następuje, typy obiektów wynik są różne. Jeśli przyjrzysz się uważnie, zobaczysz trzy typy obiektów wynikowych:

* Adres punktu
* Ulica
* Ulica Krzyżowa

Należy zauważyć, że wyszukiwanie adresów nie zwraca US.  

Parametr `Score` dla każdego obiektu odpowiedzi wskazuje, jak pasujący wynik odnosi się do wyników innych obiektów w tej samej odpowiedzi. Aby uzyskać więcej informacji o parametrach obiektu odpowiedzi, zobacz [Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

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

Typ odpowiedzi *Geometria* może zawierać identyfikator geometrii, który `dataSources` jest `geometry` zwracany w obiekcie w obszarze i `id`. Na przykład można użyć [usługi Szukaj wielokąta,](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) aby zażądać danych geometrii w formacie GeoJSON. Za pomocą tego formatu można uzyskać zarys miasta lub lotniska dla zestawu encji. Następnie można użyć tych danych granicy, aby [skonfigurować geofence](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) lub [wyszukaj CHO wewnątrz geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Odpowiedzi na interfejs API [adresu wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) lub interfejsu API [rozmytego wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) mogą `dataSources` zawierać `geometry` identyfikator `id`geometrii zwrócony w obiekcie w obszarze i:


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz:

> [!div class="nextstepaction"]
> [Jak tworzyć żądania usługi Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi wyszukiwania](https://docs.microsoft.com/rest/api/maps/search)