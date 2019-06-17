---
title: Jak utworzyć żądanie danych w czasie rzeczywistym w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Żądanie danych w czasie rzeczywistym przy użyciu usługi Azure Maps mobilności.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: bb99a3c063f69aa5aeb00efdb51319a53d05b2d1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067602"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Żądanie danych w czasie rzeczywistym przy użyciu usługi mobilności Azure Maps

W tym artykule pokazano, jak używać usługi Azure Maps [usługi mobilności](https://aka.ms/AzureMapsMobilityService) do danych w czasie rzeczywistym przesyłania żądania.

W tym artykule dowiesz się jak:


 * Żądanie dalej Przyjazdy w czasie rzeczywistym dla wszystkich wierszy otrzymywanych z danym stop
 * Żądania w czasie rzeczywistym informacje o stacji dokującej danego roweru.


## <a name="prerequisites"></a>Wymagania wstępne

Aby wprowadzić wszelkie wywołania do usługi Azure Maps przesyłania publicznych interfejsów API, należy mapy konta i klucz. Aby uzyskać informacji na temat tworzenia konta usługi i pobierania klucza, zobacz [jak zarządzać swoim kontem usługi Azure Maps i klucze](how-to-manage-account-keys.md).

W tym artykule wykorzystano [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Możesz użyć dowolnego interfejsu API środowisko programistyczne, które użytkownik sobie tego życzy.


## <a name="request-real-time-arrivals-for-a-stop"></a>Żądanie Przyjazdy w czasie rzeczywistym dla stop

Aby można było dane w czasie rzeczywistym Przyjazdy na zatrzymanie przesyłania publicznego danego żądania, musisz zgłosić wniosek o [API Przyjazdy w czasie rzeczywistym](https://aka.ms/AzureMapsMobilityRealTimeArrivals) usługi Azure Maps [usługi mobilności](https://aka.ms/AzureMapsMobilityService). Konieczne będzie **metroID** i **stopID** można wykonać żądania. Aby dowiedzieć się więcej na temat tych parametrów żądania, zobacz instrukcje, nasz przewodnik dotyczący [żądania tras tranzytowych publicznych](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Użyjmy "522" jako naszych metro identyfikator, czyli metro zatrzyma, identyfikator obszaru "Bellevue — Tacoma — Seattle, WA", oraz identyfikator stop "2060603", który jest magistrali "Ne 24th St z & apisz 162nd Ne, Bellevue WA". Aby zażądać kolejnych pięciu dane w czasie rzeczywistym Przyjazdy dalej na żywo każdorazowym w tym stop, wykonaj następujące czynności:

1. Utwórz kolekcję, w którym będzie przechowywany żądania. W aplikacji Postman wybierz **New**. W **Utwórz nowy** wybierz **kolekcji**. Nazwij kolekcję, a następnie wybierz pozycję **Utwórz** przycisku.

2. Aby utworzyć żądanie, wybierz **New** ponownie. W **Utwórz nowy** wybierz **żądania**. Wprowadź **nazwy żądania** dla żądania, zaznacz kolekcję utworzoną w poprzednim kroku jako lokalizacja, w której chcesz zapisać żądanie, a następnie wybierz **Zapisz**.

    ![Utwórz żądanie w narzędziu Postman](./media/how-to-request-transit-data/postman-new.png)

3. Wybierz metodę GET HTTP, na karcie konstruktora i wprowadź następujący adres URL, aby utworzyć żądanie GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Po pomyślnym żądania otrzymasz następującą odpowiedź.  Zwróć uwagę, że ten parametr "scheduleType" definiuje, czy Godzina nadejścia szacowany jest oparta na danych w czasie rzeczywistym lub statyczną.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }


## Real-time data for bike docking station

The [Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) of the Azure Maps Mobility Service, allows to request static and real-time information such as availability and vacancy information for a given bike or scooter docking station. We will make a request to get real-time data for a docking station for bikes.

In order to make a request to the Get Transit Dock Info API, you will need the **dockId** for that station. You can get the dock ID by making a search request to the [Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) and setting the **objectType** parameter to "bikeDock". Follow the steps below to get real-time data of a docking station for bikes.


### Get dock ID

To get **dockID**, follow the steps below to make a request to the Get Nearby Transit API:

1. In Postman, click **New Request** | **GET request** and name it **Get dock ID**.

2.  On the Builder tab, select the **GET** HTTP method, enter the following request URL, and click **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Po pomyślnym żądania otrzymasz następującą odpowiedź. Należy zauważyć, że mamy **identyfikator** w odpowiedzi, której można później jako parametr zapytania w żądaniu Rozpoczynanie przesyłania Dock informacje o funkcji API.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
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


### <a name="get-real-time-bike-dock-status"></a>Pobierz stan dokowania rowerów w czasie rzeczywistym

Wykonaj poniższe kroki, aby zgłosić wniosek Rozpoczynanie przesyłania zadokować informacje interfejsu API można pobrać danych w czasie rzeczywistym dla wybranych dokowania.

1. W narzędziu Postman, kliknij przycisk **nowe żądanie** | **żądanie GET** i nadaj mu nazwę **pobieranie danych w czasie rzeczywistym dock**.

2.  Na karcie Builder wybierz **UZYSKAĆ** metodę HTTP, wprowadź następujący adres URL żądania, a następnie kliknij przycisk **wysyłania**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Po pomyślnym żądania zostanie wyświetlony odpowiedzi o następującej strukturze:

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć żądanie przesyłane dane przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak utworzyć żądanie przesyłane dane](how-to-request-transit-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi Azure Maps Mobility Service:

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)
