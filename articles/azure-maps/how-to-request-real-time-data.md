---
title: Żądanie danych tranzytowych w czasie rzeczywistym | Mapy platformy Microsoft Azure
description: Żądaj danych w czasie rzeczywistym za pomocą usługi mobilności Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335482"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Żądanie danych w czasie rzeczywistym przy użyciu usługi mobilności usługi Azure Maps

W tym artykule pokazano, jak używać usługi Azure Maps [Mobility Service](https://aka.ms/AzureMapsMobilityService) do żądania przesyłania danych w czasie rzeczywistym.

W tym artykule dowiesz się, jak:


 * Zamów następne przyjazdy w czasie rzeczywistym dla wszystkich linii przybywających na danym przystanku
 * Poproś o informacje w czasie rzeczywistym dla danej stacji dokującej dla rowerów.


## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz mieć konto usługi Azure Maps i klucz subskrypcji, aby wykonywać wszelkie wywołania interfejsów API transportu publicznego usługi Azure Maps. Aby uzyskać informacje, postępuj zgodnie z instrukcjami w [tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) w celu utworzenia konta usługi Azure Maps. Wykonaj kroki opisane w [celu uzyskania klucza podstawowego,](quick-demo-map-app.md#get-the-primary-key-for-your-account) aby uzyskać klucz podstawowy dla swojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).


W tym artykule używa [aplikacji Postman](https://www.getpostman.com/apps) do tworzenia wywołań REST. Można użyć dowolnego środowiska programistycznego interfejsu API, które wolisz.


## <a name="request-real-time-arrivals-for-a-stop"></a>Poproś o przyjazdy w czasie rzeczywistym, aby zatrzymać

Aby zażądać danych o przyjazdach w czasie rzeczywistym określonego przystanku transportu publicznego, należy złożyć wniosek do [interfejsu API przylotów w czasie rzeczywistym](https://aka.ms/AzureMapsMobilityRealTimeArrivals) usługi azure maps [mobilności.](https://aka.ms/AzureMapsMobilityService) Aby wypełnić żądanie, musisz **metroID** i **stopID.** Aby dowiedzieć się więcej o tym, jak zażądać tych parametrów, zapoznaj się z naszym przewodnikiem, jak [żądać tras transportu publicznego.](https://aka.ms/AMapsHowToGuidePublicTransitRouting) 

Użyjmy "522" jako naszego identyfikatora metra, który jest identyfikatorem metra dla obszaru "Seattle-Tacoma-Bellevue, WA". Użyj "522---2060603" jako identyfikator przystanku, ten przystanek autobusowy znajduje się pod adresem "Ne 24th St & 162nd Ave Ne, Bellevue WA". Aby uzyskać kolejne pięć danych o przyjazdach w czasie rzeczywistym, dla wszystkich następnych przylotów na żywo na tym przystanku, wykonaj następujące kroki:

1. Otwórz aplikację Postman i utwórzmy kolekcję do przechowywania żądań. U góry aplikacji Listonosz wybierz pozycję **Nowy**. W oknie **Utwórz nowy** wybierz pozycję **Kolekcja**.  Nazwij kolekcję i wybierz przycisk **Utwórz.**

2. Aby utworzyć żądanie, wybierz ponownie pozycję **Nowy.** W oknie **Utwórz nowy** wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, w której ma być zapisywane żądanie. Następnie wybierz pozycję **Zapisz**.

    ![Tworzenie żądania w listonoszu](./media/how-to-request-transit-data/postman-new.png)

3. Wybierz metodę **GET** HTTP na karcie konstruktora i wprowadź następujący adres URL, aby utworzyć żądanie GET. Zamień `{subscription-key}`klucz podstawowy usługi Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Po pomyślnym żądaniu otrzymasz następującą odpowiedź.  Należy zauważyć, że parametr "scheduleType" określa, czy szacowany czas przybycia jest oparty na danych w czasie rzeczywistym lub statycznych.

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


## <a name="real-time-data-for-bike-docking-station"></a>Dane w czasie rzeczywistym dla stacji dokującej dla rowerów

[Interfejs API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) umożliwia użytkownikom żądanie informacji statycznych i w czasie rzeczywistym. Użytkownicy mogą na przykład poprosić o informacje o dostępności i pustostanach dla roweru lub stacji dokującej do hulajnogi. [Interfejs API Informacji o doku tranzytowym](https://aka.ms/AzureMapsMobilityTransitDock) jest również częścią usługi [mobilności](https://aka.ms/AzureMapsMobilityService)usługi Azure Maps .

Aby złożyć żądanie do [interfejsu API Pobierz informacje o doku tranzytowym,](https://aka.ms/AzureMapsMobilityTransitDock)potrzebny jest adres **dokującej** dla tej stacji. Identyfikator stacji dokującej można uzyskać, wysyłając żądanie wyszukiwania do [interfejsu API Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) z parametrem **objectType** przypisanym do "bikeDock". Wykonaj poniższe czynności, aby uzyskać w czasie rzeczywistym dane stacji dokującej dla rowerów.


### <a name="get-dock-id"></a>Uzyskaj identyfikator stacji dokującej

Aby uzyskać **dockID,** wykonaj poniższe czynności, aby poprosić o interfejs API Pobierz dostęp do tranzytu w pobliżu:

1. W poście kliknij pozycję **Żądanie nowego żądania** | **GET** i nazwij go Pobierz identyfikator **stacji dokującej**.

2.  Na karcie Konstruktor wybierz metodę **POBIERZ** HTTP, wprowadź następujący adres URL żądania i kliknij przycisk **Wyślij**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Po pomyślnym żądaniu otrzymasz następującą odpowiedź. Należy zauważyć, że mamy teraz **identyfikator** w odpowiedzi, który może być później używany jako parametr kwerendy w żądaniu do interfejsu API Get Transit Dock Info.

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


### <a name="get-real-time-bike-dock-status"></a>Uzyskaj status stacji dokującej rowerowej w czasie rzeczywistym

Wykonaj poniższe czynności, aby poprosić o interfejs API Pobierz informacje o doku tranzytowym, aby uzyskać dane w czasie rzeczywistym dla wybranej stacji dokującej.

1. W poście kliknij pozycję **Żądanie nowego żądania** | **GET** i nazwij go Pobierz **dane dokowania w czasie rzeczywistym**.

2.  Na karcie Konstruktor wybierz metodę **POBIERZ** HTTP, wprowadź następujący adres URL żądania i kliknij przycisk **Wyślij**.
 
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

Dowiedz się, jak żądać danych tranzytowych za pomocą usługi mobilności:

> [!div class="nextstepaction"]
> [Jak zażądać danych tranzytowych](how-to-request-transit-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi mobilności usługi Azure Maps:

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)
