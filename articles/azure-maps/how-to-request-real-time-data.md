---
title: Żądaj danych tranzytowych w czasie rzeczywistym | Mapy Microsoft Azure
description: Zażądaj danych w czasie rzeczywistym przy użyciu usługi mobilności Maps Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 053e6c84f69e8b3d3fed0a90a8b632aa4eb311cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198160"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Żądanie danych w czasie rzeczywistym przy użyciu usługi mobilności Azure Maps

W tym artykule pokazano, jak za pomocą [usługi mobilności](https://aka.ms/AzureMapsMobilityService) Azure Maps żądania danych tranzytowych w czasie rzeczywistym.

W tym artykule dowiesz się, jak:


 * Żądaj następnych przyjęć w czasie rzeczywistym dla wszystkich wierszy, które docierają do danego zatrzymania
 * Zażądaj informacji w czasie rzeczywistym dla danej stacji dokowania roweru.


## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz mieć konto Azure Maps i klucz subskrypcji, aby wykonać dowolne wywołania interfejsów API Azure Maps publicznego przesyłania. Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami podanymi w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) w celu utworzenia konta Azure Maps. Postępuj zgodnie z instrukcjami w temacie [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) , aby uzyskać klucz podstawowy dla Twojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).


W tym artykule jest wykorzystywana [aplikacja Poster](https://www.getpostman.com/apps) do kompilowania wywołań REST. Możesz użyć dowolnego preferowanego środowiska deweloperskiego interfejsu API.


## <a name="request-real-time-arrivals-for-a-stop"></a>Żądaj przyjęć w czasie rzeczywistym na potrzeby zatrzymania

Aby można było zażądać danych o nadejściu w czasie rzeczywistym określonego tranzytu publicznego, należy wysłać żądanie do [interfejsu API przybycia w czasie rzeczywistym](https://aka.ms/AzureMapsMobilityRealTimeArrivals) [usługi mobilności](https://aka.ms/AzureMapsMobilityService)Azure Maps. Do ukończenia żądania potrzebne są **metroID** i **stopID** . Aby dowiedzieć się więcej o tym, jak zażądać tych parametrów, zobacz nasz przewodnik dotyczący [żądania publicznych tras tranzytowych](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Użyjmy "522" jako identyfikatora Metro, który jest IDENTYFIKATORem Metro dla obszaru "Seattle – Tacoma – Bellevue, WA". Użyj "522---2060603" jako identyfikatora zatrzymania, ta magistrala zatrzymuje się o godzinie "ne 24 St & 162nd Ave ne, Bellevue WA". Aby poprosić o następne pięć danych o nadejściu w czasie rzeczywistym dla wszystkich następnych na żywo przyjęć, wykonaj następujące czynności:

1. Otwórz aplikację Poster i Utwórz kolekcję do przechowywania żądań. W górnej części aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**.  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** .

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, w której ma zostać zapisane żądanie. Następnie wybierz pozycję **Zapisz**.

    ![Utwórz żądanie w programie Poster](./media/how-to-request-transit-data/postman-new.png)

3. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL, aby utworzyć żądanie Get. Zastąp `{subscription-key}`Azure Maps kluczem podstawowym.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Po pomyślnym żądaniu otrzymasz poniższą odpowiedź.  Należy zauważyć, że parametr "ScheduleType" określa, czy szacowany czas przybycia jest oparty na danych w czasie rzeczywistym, czy statycznym.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Dane czasu rzeczywistego dla stacji dokującej roweru

[Interfejs API uzyskiwania informacji o dockce zadokowanych](https://aka.ms/AzureMapsMobilityTransitDock) umożliwia użytkownikom żądanie informacji statycznych i w czasie rzeczywistym. Na przykład użytkownicy mogą żądać informacji dotyczących dostępności i wakatów dla roweru lub stacji dokującej Scooter. [Interfejs API uzyskiwania informacji o Docku przejazdu](https://aka.ms/AzureMapsMobilityTransitDock) jest również częścią [usługi mobilności](https://aka.ms/AzureMapsMobilityService)Azure Maps.

Aby można było wysłać żądanie do [interfejsu API uzyskiwania informacji o Docku przejścia](https://aka.ms/AzureMapsMobilityTransitDock), konieczne będzie **dockId** dla tej stacji. Identyfikator dokowania można uzyskać, wysyłając żądanie wyszukiwania do [interfejsu API pobierania w pobliżu](https://aka.ms/AzureMapsMobilityNearbyTransit) z parametrem **ObjectType** przypisanym do elementu "bikeDock". Wykonaj poniższe kroki, aby uzyskać dane czasu rzeczywistego stacji dokującej dla rowerów.


### <a name="get-dock-id"></a>Pobierz identyfikator dokowania

Aby uzyskać **dockID**, wykonaj poniższe kroki, aby wysłać żądanie do interfejsu API uzyskiwania bliskiej tranzytu:

1. W programie Poster kliknij pozycję **nowe żądanie** , | **Pobierz żądanie** i nadaj mu nazwę **Identyfikator dokowania**.

2.  Na karcie Konstruktor wybierz metodę **Get** http, wprowadź następujący adres URL żądania, a następnie kliknij przycisk **Wyślij**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Po pomyślnym żądaniu otrzymasz poniższą odpowiedź. Zwróć uwagę, że mamy teraz **Identyfikator** w odpowiedzi, który może być później używany jako parametr zapytania w żądaniu do interfejsu API uzyskiwania informacji o Docku przejścia.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Pobieranie stanu dokowania roweru w czasie rzeczywistym

Wykonaj poniższe kroki, aby wysłać żądanie do interfejsu API uzyskiwania informacji o Docku tranzytowym w celu uzyskania danych w czasie rzeczywistym dla wybranego doku.

1. W programie Poster kliknij pozycję **nowe żądanie** | **Pobierz żądanie** i nadaj jej nazwę **Pobierz dane w czasie rzeczywistym**.

2.  Na karcie Konstruktor wybierz metodę **Get** http, wprowadź następujący adres URL żądania, a następnie kliknij przycisk **Wyślij**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Po pomyślnym żądaniu otrzymasz odpowiedź o następującej strukturze:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak żądać danych tranzytowych przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych tranzytowych](how-to-request-transit-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi Azure Maps Mobility:

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)
