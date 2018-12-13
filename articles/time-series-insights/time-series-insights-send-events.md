---
title: Sposób wysyłania zdarzeń do środowiska usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym samouczku wyjaśniono, jak utworzyć i skonfigurować Centrum zdarzeń i uruchomić przykładową aplikację do wypychania zdarzeń, który ma być wyświetlane w usłudze Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: c583c2211297acd83f88d23b2b8cbd9f8207927f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867611"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Wysyłanie zdarzeń do środowiska usługi Time Series Insights za pomocą centrum zdarzeń

W tym artykule wyjaśniono, jak utworzyć i skonfigurować Centrum zdarzeń i uruchomić przykładową aplikację do wypychania zdarzeń. Jeśli masz już Centrum zdarzeń, które ze zdarzeniami w formacie JSON, pominąć ten samouczek i wyświetlić swoje środowisko w [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurowanie Centrum zdarzeń

1. Tworzenie Centrum zdarzeń, postępuj zgodnie z instrukcjami z Centrum zdarzeń [dokumentacji](https://docs.microsoft.com/azure/event-hubs/).
1. Wyszukaj `Event Hub` na pasku wyszukiwania. Kliknij przycisk **usługi Event Hubs** na zwracanej liście.
1. Wybierz Centrum zdarzeń, klikając jego nazwę.
1. Podczas tworzenia Centrum zdarzeń jest naprawdę utworzeniem Namespace Centrum zdarzeń.  Jeśli trzeba jeszcze utworzyć Centrum zdarzeń w ramach Namespace, zrób to w ramach jednostki.  

    ![zaktualizowano][1]

1. Po utworzeniu Centrum zdarzeń, kliknij jego nazwę.
1. W obszarze **jednostek** kliknij w oknie konfiguracji środkowej **usługi Event Hubs** ponownie.
1. Wybierz nazwę Centrum zdarzeń, aby go skonfigurować.

    ![Grupa odbiorców][2]

1. W obszarze **jednostek**, wybierz opcję **grupy konsumentów**.
1. Upewnij się, że utworzono grupę konsumentów, która jest używana wyłącznie przez źródło zdarzeń usługi TSI.

    > [!IMPORTANT]
    > Upewnij się, że ta grupa odbiorców nie jest używany przez żadną inną usługę (np. zadania usługi Stream Analytics lub innego środowiska TSI). Jeśli grupa odbiorców jest używana przez inne usługi, operacja odczytu ma negatywny wpływ na to środowisko i innych usług. Jeśli używasz `$Default` grupy odbiorców, może to spowodować potencjalne ponownego użycia przez inne czytniki.

1. W obszarze **ustawienia** nagłówka, wybierz **zasad dostępu udziału**.
1. W tym Centrum zdarzeń Utwórz **Moje_zasady_wysyłania** używany do wysyłania zdarzeń w C# próbki.

    ![udostępnione = co dostępu][3]

    ![udostępnione dostępu — dwa][4]

## <a name="add-time-series-insights-instances"></a>Dodawanie wystąpień usługi Time Series Insights

Aktualizacja TSI używa wystąpienia, aby dodać kontekstowego dane do przychodzących danych telemetrycznych. Dane sprzężony w zapytania przy użyciu **identyfikator serii czasu**. **Identyfikator serii czasu** windmills przykładowy projekt jest `Id`. Aby dowiedzieć się więcej o wystąpieniach serii czasu i **identyfikatory serii czasu**, przeczytaj artykuł [modeli szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="create-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń usługi Time Series Insights

1. Jeśli nie utworzono źródła zdarzeń, postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub), aby je utworzyć.
1. Określ `timeSeriesId` — dotyczą [modeli szeregów czasowych](./time-series-insights-update-tsm.md) Aby dowiedzieć się więcej na temat **identyfikatory serii czasu**.

### <a name="push-events-sample-windmills"></a>Wypychanie zdarzeń (przykładowy windmills)

1. Wyszukaj Centrum zdarzeń, na pasku wyszukiwania. Kliknij przycisk **usługi Event Hubs** na zwracanej liście.
1. Wybierz Centrum zdarzeń, klikając jego nazwę.
1. Przejdź do **współużytkowane zasady dostępu** i następnie **RootManageSharedAccessKey**. Kopiuj **połączenia stingu — klucz podstawowy**

   ![connection-string][5]

1. Przejdź do pozycji https://tsiclientsample.azurewebsites.net/windFarmGen.html (Plik > Nowy > Inny). Spowoduje to uruchomienie Wiatrak symulowanych urządzeń.
1. Wklej parametry połączenia skopiowane z kroku 3 w **parametry połączenia Centrum zdarzeń**.

    ![connection-string][6]

1. Kliknij pozycję **kliknij przycisk do ekranu startowego**. Symulator wygeneruje również JSON wystąpienie, którego można użyć bezpośrednio.
1. Wróć do Centrum zdarzeń. Powinny zostać wyświetlone nowe zdarzenia są odbierane przez Centrum:

   ![telemetria][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>Obsługiwane kształty JSON

### <a name="sample-1"></a>Przykład 1

#### <a name="input"></a>Dane wejściowe

Prosty obiekt JSON.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output---one-event"></a>Dane wyjściowe — jedno zdarzenie

|id|sygnatura czasowa|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Przykład 2

#### <a name="input"></a>Dane wejściowe

Tablica JSON z dwoma obiektami JSON. Każdy obiekt JSON zostanie przekształcony na zdarzenie.
```json
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

#### <a name="output---two-events"></a>Dane wyjściowe — dwa zdarzenia

|id|sygnatura czasowa|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Przykład 3

#### <a name="input"></a>Dane wejściowe

Obiekt JSON z zagnieżdżonych tablic JSON, który zawiera dwa obiekty JSON:
```json
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

#### <a name="output---two-events"></a>Dane wyjściowe — dwa zdarzenia

Zwróć uwagę, że właściwość "location" jest kopiowana do każdego zdarzenia.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Przykład 4

#### <a name="input"></a>Dane wejściowe

Obiekt JSON z zagnieżdżoną tablicą JSON zawierającą dwa obiekty JSON. Te dane wejściowe pokazują, że globalne właściwości mogą być reprezentowane przez złożony obiekt JSON.

```json
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

#### <a name="output---two-events"></a>Dane wyjściowe — dwa zdarzenia

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyświetlanie środowiska w Eksplorator usługi Time Series Insights](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png