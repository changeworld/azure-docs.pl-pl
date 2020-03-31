---
title: Wysyłanie zdarzeń do środowiska — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować centrum zdarzeń, uruchomić przykładową aplikację i wysłać zdarzenia do środowiska usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254250"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Wysyłanie zdarzeń do środowiska usługi Time Series Insights przy użyciu centrum zdarzeń

W tym artykule wyjaśniono, jak utworzyć i skonfigurować centrum zdarzeń w usłudze Azure Event Hubs. Opisano w nim również sposób uruchamiania przykładowej aplikacji do wypychania zdarzeń do usługi Azure Time Series Insights z centrum zdarzeń. Jeśli masz istniejące centrum zdarzeń ze zdarzeniami w formacie JSON, pomiń ten samouczek i wyświetl swoje środowisko w [usłudze Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurowanie centrum zdarzeń

1. Aby dowiedzieć się, jak utworzyć centrum zdarzeń, przeczytaj [dokumentację Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/).
1. W polu wyszukiwania wyszukaj **centrum zdarzeń**. Na liście zwracane wybierz pozycję **Centra zdarzeń**.
1. Wybierz centrum zdarzeń.
1. Podczas tworzenia centrum zdarzeń tworzysz obszar nazw centrum zdarzeń. Jeśli centrum zdarzeń nie zostało jeszcze utworzone w obszarze nazw, w menu w obszarze **Jednostki**utwórz centrum zdarzeń.  

    [![Lista centrów zdarzeń](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Po utworzeniu centrum zdarzeń wybierz go na liście centrów zdarzeń.
1. W menu w obszarze **Elementy**wybierz pozycję **Centra zdarzeń**.
1. Wybierz nazwę centrum zdarzeń, aby ją skonfigurować.
1. W obszarze **Przegląd**wybierz pozycję **Grupy odbiorców**, a następnie wybierz pozycję **Grupa odbiorców**.

    [![Tworzenie grupy odbiorców](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Upewnij się, że tworzysz grupę odbiorców, która jest używana wyłącznie przez źródło zdarzeń usługi Time Series Insights.

    > [!IMPORTANT]
    > Upewnij się, że ta grupa konsumentów nie jest używana przez żadną inną usługę, taką jak zadanie usługi Azure Stream Analytics lub inne środowisko usługi Time Series Insights. Jeśli grupa odbiorców jest używana przez inne usługi, operacje odczytu są negatywnie zagrożone zarówno dla tego środowiska, jak i dla innych usług. Jeśli używasz **$Default** jako grupy odbiorców, inni czytelnicy mogą potencjalnie ponownie użyć grupy odbiorców.

1. W menu w obszarze **Ustawienia**wybierz pozycję **Zasady dostępu współdzielonego**, a następnie wybierz polecenie **Dodaj**.

    [![Wybierz pozycję Zasady dostępu współdzielonego, a następnie wybierz przycisk Dodaj](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. W okienku **Dodawanie nowych zasad dostępu współdzielonego** utwórz dostęp udostępniony o nazwie **MySendPolicy**. Ta zasada dostępu udostępnionego służy do wysyłania zdarzeń w przykładach języka C# w dalszej części tego artykułu.

    [![W polu Nazwa zasad wprowadź mysendpolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. W obszarze **Oświadczenie**zaznacz pole wyboru **Wyślij.**

## <a name="add-a-time-series-insights-instance"></a>Dodawanie wystąpienia usługi Time Series Insights

Aktualizacja usługi Time Series Insights używa wystąpień do dodawania danych kontekstowych do przychodzących danych telemetrycznych. Dane są łączone w czasie kwerendy przy użyciu **identyfikatora szeregów czasowych**. **Identyfikator szeregów czasowych** dla przykładowego projektu wiatraków, `id`którego używamy w dalszej części tego artykułu, to . Aby dowiedzieć się więcej o wystąpieniach time series insight i **identyfikatorze szeregów czasowych,** przeczytaj [modely szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń usługi Time Series Insights

1. Jeśli źródło zdarzeń nie utworzono źródła zdarzeń, wykonaj czynności, aby [utworzyć źródło zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Ustaw wartość `timeSeriesId`dla . Aby dowiedzieć się więcej o **identyfikatorze szeregów czasowych,** przeczytaj [modely szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Wypychaj zdarzenia do próbek wiatraków

1. Na pasku wyszukiwania wyszukaj **centrum zdarzeń**. Na liście zwracane wybierz pozycję **Centra zdarzeń**.

1. Wybierz wystąpienie centrum zdarzeń.

1. Przejdź do >  **zasady dostępu współdzielonego****MySendPolicy**. Skopiuj wartość **dla klucza podstawowego ciągu połączenia**.

    [![Kopiowanie wartości ciągu połączenia klucza podstawowego](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Przejdź do pozycji https://tsiclientsample.azurewebsites.net/windFarmGen.html (Plik > Nowy > Inny). Adres URL tworzy i uruchamia symulowane urządzenia wiatraka.
1. W polu **Parametry połączenia centrum zdarzeń** na stronie sieci Web wklej parametry połączenia skopiowane w polu [wejściowym wiatraka](#push-events-to-windmills-sample).
  
    [![Wklejanie ciągu połączenia klucza podstawowego w polu Parametry połączenia usługi Event Hub](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Wybierz **opcję Kliknij, aby rozpocząć**. 

    > [!TIP]
    > Symulator wiatraka tworzy również JSON, którego można użyć jako ładunku za pomocą [interfejsów API zapytań GA usługi Time Series Insights.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)

    > [!NOTE]
    > Symulator będzie nadal wysyłać dane, dopóki karta przeglądarki nie zostanie zamknięta.

1. Wróć do centrum zdarzeń w witrynie Azure portal. Na **przegląd** strony zostaną wyświetlone nowe zdarzenia odebrane przez centrum zdarzeń.

    [![Strona Przegląd centrum zdarzeń z danymi dla Centrum zdarzeń](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Obsługiwane kształty JSON

### <a name="example-one"></a>Przykład jeden

* **Dane wejściowe:** Prosty obiekt JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Dane wyjściowe:** Jedno zdarzenie.

    |id|sygnatura czasowa|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Przykład dwa

* **Dane wejściowe:** tablica JSON z dwoma obiektami JSON. Każdy obiekt JSON jest konwertowany na zdarzenie.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Dane wyjściowe:** Dwa zdarzenia.

    |id|sygnatura czasowa|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Przykład trzeci

* **Dane wejściowe:** Obiekt JSON z zagnieżdżoną tablicą JSON zawierającą dwa obiekty JSON.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Dane wyjściowe:** Dwa zdarzenia. **Lokalizacja** właściwości jest kopiowana do każdego zdarzenia.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Przykład czwarty

* **Dane wejściowe:** Obiekt JSON z zagnieżdżoną tablicą JSON zawierającą dwa obiekty JSON. To dane wejściowe pokazuje, że właściwości globalne mogą być reprezentowane przez złożony obiekt JSON.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Dane wyjściowe:** Dwa zdarzenia.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie środowiska](https://insights.timeseries.azure.com) w Eksploratorze usługi Time Series Insights.

- Dowiedz się więcej o [komunikatach urządzeń usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
