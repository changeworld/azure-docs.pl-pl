---
title: Jak utworzyć żądanie przesyłane dane usługi Azure Maps | Dokumentacja firmy Microsoft
description: Żądanie publicznego przesyłania danych za pomocą usługi Azure Maps mobilności.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e8250763153f7c5b71f3906a560365dadfd55694
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735574"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Żądanie danych publicznych przesyłania przy użyciu usługi mobilności Azure Maps 

W tym artykule pokazano, jak używać usługi Azure Maps [usługi mobilności](https://aka.ms/AzureMapsMobilityService) publicznych przesyłania żądania danych, w tym zostanie ono zatrzymane, informacje dotyczące tras i szacunki czas podróży.

W tym artykule dowiesz się jak:

* Pobierz identyfikator Miasto za pomocą [uzyskać API obszaru Metro](https://aka.ms/AzureMapsMobilityMetro)
* Żądanie przesyłane pobliskich ta wstrzymuje korzystanie z [Rozpoczynanie przesyłania pobliskich](https://aka.ms/AzureMapsMobilityNearbyTransit) usługi.
* Zapytanie [uzyskać API trasy przesyłania](https://aka.ms/AzureMapsMobilityTransitRoute) zaplanować trasę za pomocą publicznego przesyłania.
* Żądanie przesyłane trasy geometrii i szczegółowy harmonogram dla tras za pomocą [Rozpoczynanie przesyłania trasa API](https://aka.ms/ https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Wymagania wstępne

Aby wprowadzić wszelkie wywołania do usługi Azure Maps przesyłania publicznych interfejsów API, należy mapy konta i klucz. Aby uzyskać informacji na temat tworzenia konta usługi i pobierania klucza, zobacz [jak zarządzać swoim kontem usługi Azure Maps i klucze](how-to-manage-account-keys.md).

W tym artykule wykorzystano [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Możesz użyć dowolnego interfejsu API środowisko programistyczne, które użytkownik sobie tego życzy.


## <a name="get-a-metro-area-id"></a>Pobierz identyfikator Miasto

Aby żądanie przesyłane informacje o określonym obszarze metropolitarnym, konieczne będzie `metroId` dla obszaru, aby dane przesyłane do żądania. [Pobierz interfejsu API obszaru Metro](https://aka.ms/AzureMapsMobilityMetro) umożliwia zażądanie metro obszary, w których jest dostępna usługa mobilności Azure Maps. Odpowiedź zawierać szczegóły `metroId`, `metroName` a reprezentacją geometrii Miasto w formacie GeoJSON.

Upewnijmy się, żądanie, aby otrzymać promień Metro identyfikator Seattle Tacoma metro obszaru. Aby zażądać identyfikator dla obszaru metro, wykonaj następujące czynności:

1. Utwórz kolekcję, w którym będzie przechowywany żądania. W aplikacji Postman wybierz **New**. W **Utwórz nowy** wybierz **kolekcji**. Nazwij kolekcję, a następnie wybierz pozycję **Utwórz** przycisku.

2. Aby utworzyć żądanie, wybierz **New** ponownie. W **Utwórz nowy** wybierz **żądania**. Wprowadź **nazwy żądania** dla żądania, zaznacz kolekcję utworzoną w poprzednim kroku jako lokalizacja, w której chcesz zapisać żądanie, a następnie wybierz **Zapisz**.
    
    ![Utwórz żądanie w narzędziu Postman](./media/how-to-request-transit-data/postman-new.png)

3. Wybierz metodę GET HTTP, na karcie konstruktora i wprowadź następujący adres URL, aby utworzyć żądanie GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Po pomyślnym żądania zostanie wyświetlony następującą odpowiedź:

    ```JSON
    {
        "results": [
            {
                "metroId": "522",
                "metroName": "Seattle–Tacoma–Bellevue, WA",
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
                            [
                                -121.95725,
                                47.18314
                            ],
                            ...,
                            ...,
                            ...,
                            ...,
                            [
                                -122.18711,
                                47.15571
                            ],
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
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                }
            }
        ]
    }
    ```

5. Kopiuj `metroId`, do użycia w przyszłości.

## <a name="request-nearby-transit-stops"></a>Żądania w pobliżu tranzytowego

Usługi Azure Maps [Rozpoczynanie przesyłania pobliskich](https://aka.ms/AzureMapsMobilityNearbyTransit) service umożliwia wyszukiwanie obiektów przesyłania, na przykład, publiczne przesyłania zatrzymuje i udostępnione rowery wokół danej lokalizacji, zwracając tranzytowych Szczegóły obiektu. Następnie możemy wysłać żądanie do usługi, które umożliwia wyszukiwanie pobliskiej przesyłania publicznego zatrzymuje się w promieniu 300 liczniki wokół podanej lokalizacji. W żądaniu, musimy uwzględnić `metroId` pobranym w wcześniej.

Aby zgłosić wniosek o [Rozpoczynanie przesyłania pobliskich](https://aka.ms/AzureMapsMobilityNearbyTransit), wykonaj następujące czynności:

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **uzyskać pobliskich zatrzymuje**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **wysyłania**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Po żądania zakończonego powodzeniem struktury odpowiedzi powinien wyglądać podobnie do poniższego:

    ```JSON
    {
        "results": [
            {
                "id": "2060603",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "Ne 24th St & 162nd Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.63241381296315,
                        "longitude": -122.12659096560266
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594172088166,
                        "longitude": -122.12395908007201
                    }
                }
            },
            {
                "id": "2061020",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318791818726,
                        "longitude": -122.12845199584025
                    },
                    "btmRightPoint": {
                        "latitude": 47.63049919323126,
                        "longitude": -122.12582004983427
                    }
                }
            },
            {
                "id": "2060604",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474784183636,
                        "longitude": -122.12912401149087
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655200865796,
                        "longitude": -122.12649203418405
                    }
                }
            }
        ]
    }    
    ```

Jeśli zauważysz dokładnie struktury odpowiedzi, widać, że zawiera on parametry każdego obiektu, przesyłania, takich jak `id`, `type`, `stopName`, `mainTransitType`, `mainAgencyName` i położenie (współrzędne) obiektu.

W celu zrozumienia, użyjemy `id` jeden z etapów magistrali jako punkt początkowy dla naszych tras w następnej sekcji.  


## <a name="request-a-transit-route"></a>Trasy przesyłania żądania

Usługi Azure Maps [uzyskać API trasy przesyłania](https://aka.ms/AzureMapsMobilityTransitRoute) umożliwia planowanie podróży zwracające opcje najlepsze możliwe trasy z zakresu od początkowego i docelowego. Usługa zapewnia różnych trybach podróży, m.in. zalet jazdę i przesyłania publicznych. Następnie wyszukamy trasę z najbliższego stop magistrali do miejsca wskazówkę w Seattle.

### <a name="get-location-coordinates-for-destination"></a>Pobieranie współrzędnych lokalizacji dla miejsca docelowego

Na potrzeby Pobieranie lokalizacji współrzędne miejsca wskazówkę, umożliwia używanie usługi Azure Maps [usługi wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Aby wysyłać żądania do usługi wyszukiwania rozmytego, wykonaj następujące czynności:

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **uzyskać współrzędne lokalizacji**.

2.  Na karcie Builder wybierz **UZYSKAĆ** metodę HTTP, wprowadź następujący adres URL żądania, a następnie kliknij przycisk **wysyłania**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Jeśli spojrzysz na odpowiedź dokładnie, zawiera wiele lokalizacji w wynikach dla wskazówkę miejsca i zawiera także informacje o współrzędnych lokalizacji dla każdego z nich w ramach **pozycji**. Kopiuj `lat` i `lon` od pozycji dla pierwszego wyniku.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 61,
            "numResults": 8,
            "offset": 0,
            "totalResults": 24,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/8309323",
                "score": 4.674,
                "info": "search:ta:840539000511573-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "monument"
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
                                    "name": "monument"
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
                    "country": "United States Of America",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
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
    

### <a name="request-route"></a>Trasy żądania

Aby przesłać wniosek trasy, wykonaj poniższe kroki:

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **informacje o trasie Get**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **wysyłania**.

    Firma Microsoft będzie żądać publicznych przesyłania trasy dla magistrali, określając `modeType` i `transitType` parametrów. Adres URL żądania zawiera lokalizacje pobranego w poprzedniej sekcji. Jako `originType` mamy **stopId** i `destionationType` mamy **pozycji**.

    Zobacz [listę parametrów identyfikatora URI](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) można używać w Twoje żądanie [uzyskać API trasy przesyłania](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Na żądanie zakończy się powodzeniem struktury odpowiedzi powinien wyglądać podobnie do poniższego:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:0---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:15:53-07:00",
                "travelTimeInSeconds": 3259,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T14:59:47-07:00",
                        "caption": "545",
                        "lengthInMeters": 16441
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:59:48-07:00",
                        "legEndTime": "2019-04-03T15:03:53-07:00",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:03:53-07:00",
                        "legEndTime": "2019-04-03T15:07:26-07:00",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:07:26-07:00",
                        "legEndTime": "2019-04-03T15:12:12-07:00",
                        "caption": "8",
                        "lengthInMeters": 1057
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:13-07:00",
                        "legEndTime": "2019-04-03T15:15:53-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 268
                    }
                ]
            },
            ...,
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:2---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:19:18-07:00",
                "travelTimeInSeconds": 3464,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T15:01:00-07:00",
                        "caption": "545",
                        "lengthInMeters": 17400
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:01:01-07:00",
                        "legEndTime": "2019-04-03T15:04:59-07:00",
                        "caption": "3rd Avenue",
                        "lengthInMeters": 269
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:04:59-07:00",
                        "legEndTime": "2019-04-03T15:09:14-07:00",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:09:14-07:00",
                        "legEndTime": "2019-04-03T15:12:52-07:00",
                        "caption": "33,24",
                        "lengthInMeters": 947
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:53-07:00",
                        "legEndTime": "2019-04-03T15:19:18-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 474
                    }
                ]
            }
        ]
    }
    ```

4. Jeśli zauważysz dokładnie, dostępnych jest wiele **magistrali** trasy w odpowiedzi. Każda marszruta ma unikatowy **trasa identyfikator** i podsumowanie, opisujący każdej nogi trasy. Następnie będzie żądać szczegóły najszybszy przy użyciu trasy `itineraryId` w odpowiedzi.

## <a name="request-fastest-route-itinerary"></a>Najszybszy trasa trasy żądania

Usługi Azure Maps [Pobierz trasę przesyłania](https://aka.ms/AzureMapsMobilityTransitItinerary) service pozwala na żądanie danych dla określonej trasy, korzystając z tras **trasa identyfikator** zwrócone przez [uzyskać API trasy przesyłania](https://aka.ms/AzureMapsMobilityTransitRoute) Usługa. Aby przesłać wniosek, wykonaj poniższe kroki:

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **Rozpoczynanie przesyłania informacji**.

2. Na karcie Builder wybierz **UZYSKAĆ** metody HTTP, wprowadź następujący adres URL żądania dla punktu końcowego interfejsu API i kliknij przycisk **wysyłania**.

    Firma Microsoft ustawi `detailType` parametr **geometrii** tak, aby odpowiedź zawiera informacje zatrzymania dla publicznych przesyłania i Włącz, wyłącz nawigację dla zapoznaj się z i roweru nogi trasy.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. Na żądanie zakończy się powodzeniem struktury odpowiedzi powinien wyglądać podobnie do poniższego:

    ```JSON
    {
    "departureTime": "2019-05-01T11:16:56-07:00",
    "arrivalTime": "2019-05-01T12:23:45-07:00",
    "legs": [
                {
                    "legType": "Walk",
                    "legStartTime": "2019-05-01T11:16:56-07:00",
                    "legEndTime": "2019-05-01T11:24:06-07:00",
                    "walkingSteps": [
                        {
                            "direction": {
                                "relativeDirection": "left"
                            },
                            "streetName": "Northeast 24th Street"
                        },
                        {
                            "direction": {
                                "relativeDirection": "right"
                            },
                            "streetName": "156th Avenue Northeast"
                        }
                    ],
                    "walkingOrigin": {
                        "latitude": 47.63096,
                        "longitude": -122.126
                    },
                    "walkingDestination": {
                        "latitude": 47.631843,
                        "longitude": -122.132294
                    },
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.126,
                                47.63096
                            ],
                            [
                                -122.12645,
                                47.63099
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.63184
                            ]
                        ]
                    }
                },
                {
                    "legType": "Wait",
                    "legStartTime": "2019-05-01T11:24:06-07:00",
                    "legEndTime": "2019-05-01T11:25:07-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "waitOnVehicle": "false"
                },
                {
                    "legType": "Bus",
                    "legStartTime": "2019-05-01T11:25:07-07:00",
                    "legEndTime": "2019-05-01T11:30:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        ...,
                        ...,
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.13235,
                                47.63184
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.64398
                            ]
                        ]
                    }
                },
                ...,
                ...,
                ...,
                {
                    "legType": "Tram",
                    "legStartTime": "2019-05-01T12:20:00-07:00",
                    "legEndTime": "2019-05-01T12:22:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 4083239,
                        "agencyId": 1360766,
                        "agencyName": "Seattle Monorail",
                        "lineNumber": "Monorail",
                        "caption1": "Seattle Center - Westlake Center",
                        "caption2": "MONORAIL Seattle Center - Westlake Center",
                        "color": "00AEEF",
                        "transitType": "Tram"
                    },
                    "line": {
                        "lineId": 3769726,
                        "lineGroupId": 4083239,
                        "direction": "backward",
                        "agencyId": 1360766,
                        "lineNumber": "Monorail",
                        "destination": "Seattle Center"
                    },
                    "stops": [
                        {
                            "stopId": 32962125,
                            "stopName": "Westlake Station",
                            "position": {
                                "latitude": 47.611417,
                                "longitude": -122.337089
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        },
                        {
                            "stopId": 32962134,
                            "stopName": "Seattle Center",
                            "position": {
                                "latitude": 47.62123,
                                "longitude": -122.349746
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.3369,
                                47.61201
                            ],
                            ...,
                            ...,
                            [
                                -122.34973,
                                47.6212
                            ]
                        ]
                    }
                },
                {
                    "legType": "PathWayWalk",
                    "legStartTime": "2019-05-01T12:22:00-07:00",
                    "legEndTime": "2019-05-01T12:23:45-07:00"
                }
          ]
    }
    ```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak żądania danych w czasie rzeczywistym przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak utworzyć żądanie danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi Azure Maps Mobility Service

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)

