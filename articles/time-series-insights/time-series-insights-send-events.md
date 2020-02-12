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
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152520"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Wysyłanie zdarzeń do środowiska usługi Time Series Insights za pomocą Centrum zdarzeń

W tym artykule opisano sposób tworzenia i konfigurowania centrum zdarzeń w usłudze Azure Event Hubs. Opisano w nim również sposób uruchamiania przykładowej aplikacji w celu wypchnięcia zdarzeń do Azure Time Series Insights z Event Hubs. Jeśli masz istniejące centrum zdarzeń ze zdarzeniami w formacie JSON, Pomiń ten samouczek i Sprawdź środowisko w [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurowanie centrum zdarzeń

1. Aby dowiedzieć się, jak utworzyć centrum zdarzeń, Przeczytaj [dokumentację dotyczącą Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. W polu wyszukiwania Wyszukaj **Event Hubs**. Na liście zwracanej wybierz pozycję **Event Hubs**.
1. Wybierz Centrum zdarzeń.
1. Podczas tworzenia centrum zdarzeń tworzona jest przestrzeń nazw centrum zdarzeń. Jeśli nie utworzono jeszcze centrum zdarzeń w przestrzeni nazw, w menu w obszarze **jednostki**Utwórz centrum zdarzeń.  

    [![listę centrów zdarzeń](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Po utworzeniu Centrum zdarzeń, wybierz go na liście usługi event hubs.
1. W menu w obszarze **jednostki**wybierz pozycję **Event Hubs**.
1. Wybierz nazwę Centrum zdarzeń, aby go skonfigurować.
1. W obszarze **Przegląd**wybierz pozycję **grupy użytkowników**, a następnie wybierz pozycję **Grupa odbiorców**.

    [![utworzyć grupę odbiorców](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Upewnij się, że utworzono grupę odbiorców, która jest używana wyłącznie przez źródło zdarzeń Time Series Insights.

    > [!IMPORTANT]
    > Upewnij się, że ta grupa odbiorców nie jest używana przez żadną inną usługę, na przykład zadanie Azure Stream Analytics lub inne środowisko Time Series Insights. Używanie grupy odbiorców przez inne usługi, operacje odczytu są negatywny wpływ dla tego środowiska oraz innych usług. Jeśli używasz **$default** jako grupy konsumentów, inni czytelnicy mogą potencjalnie ponownie wykorzystać grupę odbiorców.

1. W menu w obszarze **Ustawienia**wybierz pozycję **zasady dostępu współdzielonego**, a następnie wybierz pozycję **Dodaj**.

    [![wybierz pozycję Zasady dostępu współdzielonego, a następnie wybierz przycisk Dodaj.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. W okienku **Dodaj nowe zasady dostępu współdzielonego** Utwórz współużytkowany dostęp o nazwie **MySendPolicy**. Te zasady dostępu współdzielonego służą do wysyłania zdarzeń C# w przykładach w dalszej części tego artykułu.

    [![w polu Nazwa zasad wpisz MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. W **obszarze**Zażądaj zaznacz pole wyboru **Wyślij** .

## <a name="add-a-time-series-insights-instance"></a>Dodaj wystąpienie usługi Time Series Insights

Aktualizacja usługi Time Series Insights używa wystąpienia, aby dodać kontekstowego dane do przychodzących danych telemetrycznych. Dane są przyłączone w czasie zapytania przy użyciu **identyfikatora szeregów czasowych**. **Identyfikator szeregów czasowych** dla projektu przykładowej Windmills, który jest używany w dalszej części tego artykułu, jest `id`. Aby dowiedzieć się więcej o wystąpieniach usługi Time Series Insights i **identyfikatorze szeregów czasowych**, zobacz [modele szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń usługi Time Series Insights

1. Jeśli nie utworzono źródła zdarzeń, wykonaj kroki, aby [utworzyć źródło zdarzenia](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Ustaw wartość `timeSeriesId`. Aby dowiedzieć się więcej na temat **identyfikatora szeregów czasowych**, Przeczytaj [modele szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Wypchnij zdarzenia do przykładu Windmills

1. Na pasku wyszukiwania Wyszukaj **Event Hubs**. Na liście zwracanej wybierz pozycję **Event Hubs**.

1. Wybierz wystąpienie centrum zdarzeń.

1. Przejdź do **zasad dostępu Współdzielonego** > **MySendPolicy**. Skopiuj wartość **parametrów połączenia — klucz podstawowy**.

    [![skopiować wartości parametrów połączenia klucza podstawowego](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Przejdź do pozycji https://tsiclientsample.azurewebsites.net/windFarmGen.html (Plik > Nowy > Inny). Adres URL tworzy i uruchamia symulowane urządzenia Windmill.
1. W polu **Parametry połączenia centrum zdarzeń** na stronie sieci Web wklej parametry połączenia, które zostały skopiowane do [pola wejściowego Windmill](#push-events-to-windmills-sample).
  
    [![wkleić parametry połączenia klucza podstawowego w polu parametrów połączenia centrum zdarzeń](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Wybierz **pozycję kliknij, aby rozpocząć**. 

    > [!TIP]
    > Symulator Windmill tworzy również kod JSON, którego można użyć jako ładunku z [interfejsami API zapytań Time Series INSIGHTS ga](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).

    > [!NOTE]
    > Symulator będzie kontynuował wysyłanie danych, dopóki karta przeglądarki nie zostanie zamknięta.

1. Wróć do Centrum zdarzeń w witrynie Azure portal. Na stronie **Przegląd** zostaną wyświetlone nowe zdarzenia odebrane przez centrum zdarzeń.

    [![stronie Przegląd centrum zdarzeń, która zawiera metryki centrum zdarzeń](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

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

* **Dane wyjściowe**: dwa zdarzenia. **Lokalizacja** właściwości jest kopiowana do każdego zdarzenia.

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
