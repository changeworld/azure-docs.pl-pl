---
title: Żądanie danych tranzytowych | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak żądać danych transportu publicznego przy użyciu usługi mobilności Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f60b66790342874620971c8f15a1e8ace9a3c7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335472"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Żądanie danych transportu publicznego przy użyciu usługi mobilności usługi Azure Maps 

W tym artykule pokazano, jak używać usługi Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) do żądania danych transportu publicznego. Dane tranzytowe obejmują przystanki tranzytowe, informacje o trasie i szacowane czas podróży.

W tym artykule dowiesz się, jak:

* Uzyskaj identyfikator obszaru metra za pomocą [interfejsu API Get Metro Area](https://aka.ms/AzureMapsMobilityMetro)
* Zamów pobliskie przystanki tranzytowe korzystając z usługi [Get Nearby Transit.](https://aka.ms/AzureMapsMobilityNearbyTransit)
* Interfejs API Pobierz [trasy tranzytowe](https://aka.ms/AzureMapsMobilityTransitRoute) kwerendy w celu zaplanowania trasy przy użyciu transportu publicznego.
* Poproś o geometrię trasy tranzytowej i szczegółowy harmonogram trasy za pomocą [interfejsu API trasy get transit](https://aka.ms/https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz mieć konto usługi Azure Maps i klucz subskrypcji, aby wykonywać wszelkie wywołania interfejsów API transportu publicznego usługi Azure Maps. Aby uzyskać informacje, postępuj zgodnie z instrukcjami w [tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) w celu utworzenia konta usługi Azure Maps. Wykonaj kroki opisane w [celu uzyskania klucza podstawowego,](quick-demo-map-app.md#get-the-primary-key-for-your-account) aby uzyskać klucz podstawowy dla swojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).


W tym artykule używa [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Można użyć dowolnego środowiska programistycznego interfejsu API, które wolisz.


## <a name="get-a-metro-area-id"></a>Uzyskaj identyfikator obszaru metra

Aby uzyskać informacje o tranzycie dla określonego obszaru `metroId` metropolitalnego, musisz tego obszaru. [Interfejs API Get Metro Area](https://aka.ms/AzureMapsMobilityMetro) umożliwia żądanie obszarów metra, w których dostępna jest usługa azure maps mobilności. Odpowiedź zawiera `metroId`szczegóły, takie `metroName`jak , i reprezentacja geometrii obszaru metra w formacie GeoJSON.

Złóżmy wniosek, aby uzyskać Metro Area dla Seattle-Tacoma metro obszarze ID. Aby poprosić o identyfikator strefy metra, wykonaj następujące czynności:

1. Otwórz aplikację Postman i utwórzmy kolekcję do przechowywania żądań. U góry aplikacji Listonosz wybierz pozycję **Nowy**. W oknie **Utwórz nowy** wybierz pozycję **Kolekcja**.  Nazwij kolekcję i wybierz przycisk **Utwórz.**

2. Aby utworzyć żądanie, wybierz ponownie pozycję **Nowy.** W oknie **Utwórz nowy** wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, w której ma być zapisywane żądanie. Następnie wybierz pozycję **Zapisz**.
    
    ![Tworzenie żądania w listonoszu](./media/how-to-request-transit-data/postman-new.png)

3. Wybierz metodę **GET** HTTP na karcie konstruktora i wprowadź następujący adres URL, aby utworzyć żądanie GET. Zamień `{subscription-key}`klucz podstawowy usługi Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Po pomyślnym żądaniu otrzymasz następującą odpowiedź:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. Skopiuj `metroId`program , będziemy musieli użyć go później.

## <a name="request-nearby-transit-stops"></a>Poproś o pobliskie przystanki tranzytowe

Usługa Azure Maps [Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) umożliwia wyszukiwanie obiektów tranzytowych.  interfejs API zwraca szczegóły obiektu tranzytowego, takie jak przystanki transportu publicznego i udostępnione rowery wokół danej lokalizacji. Następnie zwrócimy się do służby z prośbą o wyszukanie pobliskich przystanków komunikacji miejskiej w promieniu 300 metrów wokół danej lokalizacji. W żądaniu musimy uwzględnić `metroId` pobrane wcześniej.

Aby złożyć wniosek do [get nearby transit,](https://aka.ms/AzureMapsMobilityNearbyTransit)wykonaj poniższe czynności:

1. W polu Postman kliknij pozycję **Żądanie nowego żądania** | **GET** i nazwij go **Poblistrzyj przystanki**.

2. Na karcie Konstruktor wybierz metodę **POBIERZ** HTTP, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Po pomyślnym żądaniu struktura odpowiedzi powinna wyglądać następująco poniżej:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

Jeśli uważnie obserwujesz strukturę odpowiedzi, zobaczysz, że zawiera ona parametry dla każdego obiektu tranzytowego. Każdy obiekt tranzytowy `id`ma `type` `stopName`parametry, `mainAgencyName`takie jak , , `mainTransitType`, i położenie, we współrzędnych, obiektu.

W celu nauki, użyjemy `id` przystanków autobusowych jako źródła, dla naszej trasy w następnym odcinku.  


## <a name="request-a-transit-route"></a>Poproś o trasę tranzytową

Interfejs API usługi Azure Maps [Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute) umożliwia planowanie podróży. Zwraca najlepsze możliwe opcje trasy z początku układu współrzędnych do miejsca docelowego. Usługa zapewnia różnego rodzaju środki transportu, w tym spacery, jazdę na rowerze i transport publiczny. Następnie przeszukujemy trasę z najbliższego przystanku autobusowego do wieży Space Needle w Seattle.

### <a name="get-location-coordinates-for-destination"></a>Pobierz współrzędne lokalizacji dla miejsca docelowego

Aby uzyskać współrzędne lokalizacji wieży Space Needle, umożliwia skorzystanie z usługi azure maps [fuzzy search service](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Aby złożyć wniosek do usługi wyszukiwania Rozmyte, wykonaj poniższe czynności:

1. W pozycji Listonosz kliknij pozycję **Żądanie nowego żądania** | **GET** i nazwij go **Pobierz współrzędne lokalizacji**.

2.  Na karcie Konstruktor wybierz metodę **POBIERZ** HTTP, wprowadź następujący adres URL żądania i kliknij przycisk **Wyślij**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Jeśli uważnie spojrzysz na odpowiedź, zawiera ona wiele lokalizacji w wynikach wyszukiwania igły kosmicznej. Każdy wynik zawiera współrzędne lokalizacji pod **pozycją**. Skopiuj `lat` i `lon` pod **pozycją** pierwszego wyniku.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>Zamów trasę

Aby złożyć żądanie trasy, wykonaj poniższe czynności:

1. W programie Postman kliknij pozycję **Żądanie nowe żądanie** | **GET** i nazwij go Pobierz informacje **o marszrutie**.

2. Na karcie Konstruktor wybierz metodę **POBIERZ** HTTP, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij**.

    Poprosimy o połączenia komunikacji miejskiej dla autobusu, określając `modeType` parametry i `transitType` parametry. Adres URL żądania zawiera lokalizacje pobrane w poprzednich sekcjach. Dla `originType`, mamy teraz **stopId**. A dla `destionationType`, mamy **stanowisko**.

    Zobacz [listę parametrów identyfikatora URI,](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) których można użyć w żądaniu, do [interfejsu API Pobierz trasy tranzytowe](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Po pomyślnym żądaniu struktura odpowiedzi powinna wyglądać następująco poniżej:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. Jeśli uważnie obserwować, istnieje wiele tras **autobusowych** w odpowiedzi. Każda trasa ma unikatowy identyfikator **trasy** i podsumowanie opisujące każdy odcinek trasy. Odcinek trasy jest częścią trasy między dwoma punktami postojowymi. Następnie poprosimy o szczegółowe informacje dotyczące najszybszej trasy przy użyciu `itineraryId` odpowiedzi.

## <a name="request-fastest-route-itinerary"></a>Poproś o najszybszą trasę trasy

Usługa [planu podróży](https://aka.ms/AzureMapsMobilityTransitItinerary) usługi Azure Maps Get Transit umożliwia żądanie danych dla określonej trasy przy użyciu identyfikatora trasy trasy **zwróconego** przez usługę interfejsu API Get Trasy [tranzytowe.](https://aka.ms/AzureMapsMobilityTransitRoute) Aby złożyć wniosek, wykonaj poniższe czynności:

1. W programie Postman kliknij pozycję **Żądanie nowego żądania** | **GET** i nazwij go Pobierz informacje **o tranzycie**.

2. Na karcie Konstruktor wybierz metodę **POBIERZ** HTTP. Wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **Wyślij**.

    Ustawimy parametr `detailType` na **geometrię,** tak aby odpowiedź zawierała informacje o zatrzymaniu dla transportu publicznego i nawigacji zakręt po kroku dla etapów spacerowych i rowerowych na trasie.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Po pomyślnym żądaniu struktura odpowiedzi powinna wyglądać następująco poniżej:

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak żądać danych w czasie rzeczywistym za pomocą usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi mobilności usługi Azure Maps

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)

