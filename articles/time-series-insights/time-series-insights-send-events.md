---
title: Wysyłanie zdarzeń do środowiska — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak skonfigurować centrum zdarzeń, uruchamiać przykładową aplikację i wysyłać zdarzenia do środowiska Azure Time Series Insightsowego.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: cdcd64b5281ce16002720072db3b5f29f1978cac
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014831"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Wysyłanie zdarzeń do środowiska usługi Time Series Insights za pomocą Centrum zdarzeń

W tym artykule opisano sposób tworzenia i konfigurowania centrum zdarzeń w usłudze Azure Event Hubs. Opisano w nim również sposób uruchamiania przykładowej aplikacji w celu wypchnięcia zdarzeń do Azure Time Series Insights z Event Hubs. Jeśli masz istniejące centrum zdarzeń ze zdarzeniami w formacie JSON, Pomiń ten samouczek i Sprawdź środowisko w [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurowanie centrum zdarzeń

1. Aby dowiedzieć się, jak utworzyć Centrum zdarzeń, zobacz [dokumentacja usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. W polu wyszukiwania, wyszukaj **usługi Event Hubs**. W liście zwracanych wybierz **usługi Event Hubs**.
1. Wybierz Centrum zdarzeń.
1. Podczas tworzenia centrum zdarzeń tworzona jest przestrzeń nazw centrum zdarzeń. Jeśli nie utworzono jeszcze centrum zdarzeń w przestrzeni nazw, w menu w obszarze **jednostki**Utwórz centrum zdarzeń.  

    [![listę centrów zdarzeń](media/send-events/1-event-hub-namespace.png)](media/send-events/1-event-hub-namespace.png#lightbox)

1. Po utworzeniu Centrum zdarzeń, wybierz go na liście usługi event hubs.
1. W menu w obszarze **jednostki**wybierz pozycję **Event Hubs**.
1. Wybierz nazwę Centrum zdarzeń, aby go skonfigurować.
1. W obszarze **Przegląd**wybierz pozycję **grupy użytkowników**, a następnie wybierz pozycję **Grupa odbiorców**.

    [![utworzyć grupę odbiorców](media/send-events/2-consumer-group.png)](media/send-events/2-consumer-group.png#lightbox)

1. Upewnij się, że utworzono grupę odbiorców, która jest używana wyłącznie przez źródło zdarzeń Time Series Insights.

    > [!IMPORTANT]
    > Upewnij się, że ta grupa odbiorców nie jest używana przez żadną inną usługę, na przykład zadanie Azure Stream Analytics lub inne środowisko Time Series Insights. Używanie grupy odbiorców przez inne usługi, operacje odczytu są negatywny wpływ dla tego środowiska oraz innych usług. Jeśli używasz **$Default** grupy odbiorców inne czytniki potencjalnie mogą ponownie używać grupy odbiorców.

1. W menu w obszarze **Ustawienia**wybierz pozycję **zasady dostępu współdzielonego**, a następnie wybierz pozycję **Dodaj**.

    [![wybierz pozycję Zasady dostępu współdzielonego, a następnie wybierz przycisk Dodaj.](media/send-events/3-shared-access-policy.png)](media/send-events/3-shared-access-policy.png#lightbox)

1. W **Dodaj nowe zasady dostępu współdzielonego** okienku tworzenie dostępu współdzielonego, o nazwie **Moje_zasady_wysyłania**. Te zasady dostępu współdzielonego służą do wysyłania zdarzeń C# w przykładach w dalszej części tego artykułu.

    [![w polu Nazwa zasad wpisz MySendPolicy](media/send-events/4-shared-access-policy-confirm.png)](media/send-events/4-shared-access-policy-confirm.png#lightbox)

1. W **obszarze**Zażądaj zaznacz pole wyboru **Wyślij** .

## <a name="add-a-time-series-insights-instance"></a>Dodaj wystąpienie usługi Time Series Insights

Aktualizacja usługi Time Series Insights używa wystąpienia, aby dodać kontekstowego dane do przychodzących danych telemetrycznych. Dane sprzężony w czasie wykonywania zapytań przy użyciu **identyfikator serii czasu**. **Identyfikator szeregów czasowych** dla projektu przykładowej Windmills, który jest używany w dalszej części tego artykułu, jest `id`. Aby dowiedzieć się więcej o usłudze Time Series Insights instances i **identyfikator serii czasu**, zobacz [modeli szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń usługi Time Series Insights

1. Jeśli jeszcze nie utworzono źródła zdarzeń, wykonaj kroki, aby [tworzenie źródła zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Ustaw wartość `timeSeriesId`. Aby dowiedzieć się więcej na temat **identyfikator serii czasu**, zobacz [modeli szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Wypchnij zdarzenia do przykładu Windmills

1. Na pasku wyszukiwania, wyszukaj **usługi Event Hubs**. W liście zwracanych wybierz **usługi Event Hubs**.

1. Wybierz wystąpienie centrum zdarzeń.

1. Przejdź do **zasad dostępu Współdzielonego** > **MySendPolicy**. Skopiuj wartość **parametrów połączenia — klucz podstawowy**.

    [![skopiować wartości parametrów połączenia klucza podstawowego](media/send-events/5-sample-code-connection-string.png)](media/send-events/5-sample-code-connection-string.png#lightbox)

1. Przejdź do pozycji https://tsiclientsample.azurewebsites.net/windFarmGen.html (Plik > Nowy > Inny). Adres URL jest uruchamiany Wiatrak symulowanych urządzeń.
1. W polu **Parametry połączenia centrum zdarzeń** na stronie sieci Web wklej parametry połączenia, które zostały skopiowane do [pola wejściowego Windmill](#push-events-to-windmills-sample).
  
    [![wkleić parametry połączenia klucza podstawowego w polu parametrów połączenia centrum zdarzeń](media/send-events/6-wind-mill-sim.png)](media/send-events/6-wind-mill-sim.png#lightbox)

1. Wybierz **kliknij, aby rozpocząć**. Symulator generuje wystąpienia JSON, który może bezpośrednio korzystać.

1. Wróć do Centrum zdarzeń w witrynie Azure portal. Na stronie **Przegląd** zostaną wyświetlone nowe zdarzenia, które są odbierane przez centrum zdarzeń.

    [![stronie Przegląd centrum zdarzeń, która zawiera metryki centrum zdarzeń](media/send-events/7-telemetry.png)](media/send-events/7-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Obsługiwane kształty JSON

### <a name="example-one"></a>Przykład jeden

* **Dane wejściowe**: prosty obiekt JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Dane wyjściowe**: jedno zdarzenie.

    |id|sygnatura czasowa|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Przykład dwa

* **Dane wejściowe**: tablica JSON z dwoma obiektami JSON. Każdy obiekt JSON jest konwertowana na zdarzenie.

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

* **Dane wyjściowe**: dwa zdarzenia.

    |id|sygnatura czasowa|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Przykład trzy

* **Wejście**: obiekt JSON z zagnieżdżoną tablicą JSON, która zawiera dwa obiekty JSON.

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

* **Dane wyjściowe**: dwa zdarzenia. Właściwość **lokalizacji** jest kopiowana do każdego zdarzenia.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Przykład cztery

* **Wejście**: obiekt JSON z zagnieżdżoną tablicą JSON, która zawiera dwa obiekty JSON. Te dane wejściowe pokazują, że globalne właściwości mogą być reprezentowane przez złożony obiekt JSON.

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

* **Dane wyjściowe**: dwa zdarzenia.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Następne kroki

- [Wyświetl swoje środowisko](https://insights.timeseries.azure.com) w Eksploratorze Time Series Insights.

- Przeczytaj więcej na temat [IoT Hub komunikatów urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
