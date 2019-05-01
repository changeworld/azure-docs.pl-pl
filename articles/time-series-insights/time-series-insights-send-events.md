---
title: Wysyłanie zdarzeń do środowiska usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować Centrum zdarzeń i uruchomić przykładową aplikację do wypychania zdarzeń, który można wyświetlić w usłudze Azure Time Series Insights.
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
ms.custom: seodec18
ms.openlocfilehash: 55b19a6cf71730858fcf42880f71a2c9c07a3b31
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683978"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Wysyłanie zdarzeń do środowiska usługi Time Series Insights za pomocą Centrum zdarzeń

W tym artykule wyjaśniono, jak utworzyć i skonfigurować Centrum zdarzeń w usłudze Azure Event Hubs, a następnie uruchom przykładową aplikację do wypychania zdarzeń. Jeśli masz już Centrum zdarzeń, które zawiera zdarzenia w formacie JSON, pominąć ten samouczek i wyświetlić swoje środowisko w [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurowanie centrum zdarzeń

1. Aby dowiedzieć się, jak utworzyć Centrum zdarzeń, zobacz [dokumentacja usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. W polu wyszukiwania, wyszukaj **usługi Event Hubs**. W liście zwracanych wybierz **usługi Event Hubs**.
1. Wybierz Centrum zdarzeń.
1. Podczas tworzenia Centrum zdarzeń są naprawdę tworzenie przestrzeni nazw Centrum zdarzeń. Jeśli nie został jeszcze utworzony Centrum zdarzeń w przestrzeni nazw, w menu, w obszarze **jednostek**, utworzyć Centrum zdarzeń.  

    ![Lista usługi event hubs][1]

1. Po utworzeniu Centrum zdarzeń, wybierz go na liście usługi event hubs.
1. W menu w obszarze **jednostek**, wybierz opcję **usługi Event Hubs**.
1. Wybierz nazwę Centrum zdarzeń, aby go skonfigurować.
1. W obszarze **jednostek**, wybierz opcję **grupy konsumentów**, a następnie wybierz pozycję **grupy odbiorców**.

    ![Utwórz grupę odbiorców][2]

1. Upewnij się, że utworzona grupa odbiorców jest używana wyłącznie przez źródło zdarzeń usługi Time Series Insights.

    > [!IMPORTANT]
    > Upewnij się, że ta grupa odbiorców nie jest używany przez żadną inną usługę (np. zadania usługi Azure Stream Analytics lub innego środowiska usługi Time Series Insights). Używanie grupy odbiorców przez inne usługi, operacje odczytu są negatywny wpływ dla tego środowiska oraz innych usług. Jeśli używasz **$Default** grupy odbiorców inne czytniki potencjalnie mogą ponownie używać grupy odbiorców.

1. W menu w obszarze **ustawienia**, wybierz opcję **zasady dostępu współdzielonego**, a następnie wybierz pozycję **Dodaj**.

    ![Wybierz zasady dostępu współdzielonego, a następnie wybierz przycisk Dodaj][3]

1. W **Dodaj nowe zasady dostępu współdzielonego** okienku tworzenie dostępu współdzielonego, o nazwie **Moje_zasady_wysyłania**. Te zasady dostępu współdzielonego będzie używać do wysyłania zdarzeń w C# przykłady w dalszej części tego artykułu.

    ![W polu Nazwa zasad wprowadź Moje_zasady_wysyłania][4]

1. W obszarze **oświadczenia**, wybierz opcję **wysyłania** pola wyboru.

## <a name="add-a-time-series-insights-instance"></a>Dodaj wystąpienie usługi Time Series Insights

Aktualizacja usługi Time Series Insights używa wystąpienia, aby dodać kontekstowego dane do przychodzących danych telemetrycznych. Dane sprzężony w czasie wykonywania zapytań przy użyciu **identyfikator serii czasu**. **Identyfikator serii czasu** windmills przykładowy projekt, której używamy w dalszej części tego artykułu jest **identyfikator**. Aby dowiedzieć się więcej o usłudze Time Series Insights instances i **identyfikator serii czasu**, zobacz [modeli szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń usługi Time Series Insights

1. Jeśli jeszcze nie utworzono źródła zdarzeń, wykonaj kroki, aby [tworzenie źródła zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Ustaw wartość `timeSeriesId`. Aby dowiedzieć się więcej na temat **identyfikator serii czasu**, zobacz [modeli szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Wypychanie zdarzeń (przykładowy windmills)

1. Na pasku wyszukiwania, wyszukaj **usługi Event Hubs**. W liście zwracanych wybierz **usługi Event Hubs**.

1. Wybierz Centrum zdarzeń.

1. Przejdź do **współużytkowane zasady dostępu** > **RootManageSharedAccessKey**. Skopiuj wartość dla **połączenia klucz podstawowy stingu**.

    ![Skopiuj wartość dla parametry połączenia klucza podstawowego][5]

1. Przejdź do pozycji https://tsiclientsample.azurewebsites.net/windFarmGen.html (Plik > Nowy > Inny). Adres URL jest uruchamiany Wiatrak symulowanych urządzeń.
1. W **parametry połączenia Centrum zdarzeń** pole na stronie internetowej, Wklej parametry połączenia, który został skopiowany w [wypychania zdarzeń](#push-events).
  
    ![Wklej parametry połączenia klucza podstawowego w polu Parametry połączenia Centrum zdarzeń][6]

1. Wybierz **kliknij, aby rozpocząć**. Symulator generuje wystąpienia JSON, który może bezpośrednio korzystać.

1. Wróć do Centrum zdarzeń w witrynie Azure portal. Na **Przegląd** strony, powinny zostać wyświetlone nowe zdarzenia, które są odbierane przez Centrum zdarzeń:

    ![Strony Przegląd Centrum zdarzeń, który zawiera metryki dla Centrum zdarzeń][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>Obsługiwane kształty JSON

### <a name="sample-1"></a>Przykład 1

#### <a name="input"></a>Dane wejściowe

Prosty obiekt JSON:

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>Dane wyjściowe: Jedno zdarzenie

|id|sygnatura czasowa|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Przykład 2

#### <a name="input"></a>Dane wejściowe

Tablica JSON z dwoma obiektami JSON. Każdy obiekt JSON jest konwertowana na zdarzenie.

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

#### <a name="output-two-events"></a>Dane wyjściowe: Dwa zdarzenia

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

#### <a name="output-two-events"></a>Dane wyjściowe: Dwa zdarzenia

Właściwość **lokalizacji** jest kopiowana do każdego zdarzenia.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Przykład 4

#### <a name="input"></a>Dane wejściowe

Obiekt JSON z zagnieżdżonych tablic JSON, który zawiera dwa obiekty JSON. Te dane wejściowe pokazują, że globalne właściwości mogą być reprezentowane przez złożony obiekt JSON.

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

#### <a name="output-two-events"></a>Dane wyjściowe: Dwa zdarzenia

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Kolejne kroki

- [Wyświetlić swoje środowisko](https://insights.timeseries.azure.com) w Eksploratorze usługi Time Series Insights.

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
