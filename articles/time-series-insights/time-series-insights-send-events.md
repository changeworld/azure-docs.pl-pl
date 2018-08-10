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
ms.date: 04/09/2018
ms.openlocfilehash: 30b83c54d314934f1de170955eec22e7b2a264b8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629756"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Wysyłanie zdarzeń do środowiska usługi Time Series Insights za pomocą centrum zdarzeń
W tym artykule wyjaśniono, jak utworzyć i skonfigurować Centrum zdarzeń i uruchomić przykładową aplikację do wypychania zdarzeń. Jeśli masz już Centrum zdarzeń, które ze zdarzeniami w formacie JSON, pominąć ten samouczek i wyświetlić swoje środowisko w [usługi Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Konfigurowanie centrum zdarzeń
1. Aby utworzyć centrum zdarzeń, wykonaj instrukcje zawarte w [dokumentacji](../event-hubs/event-hubs-create.md) centrum zdarzeń.

2. Wyszukaj **Centrum zdarzeń** na pasku wyszukiwania. Kliknij przycisk **usługi Event Hubs** na zwracanej liście.

3. Wybierz Centrum zdarzeń, klikając jego nazwę.

4. W obszarze **jednostek** kliknij w oknie konfiguracji środkowej **usługi Event Hubs** ponownie.

5. Wybierz nazwę Centrum zdarzeń, aby go skonfigurować.

  ![Wybieranie grupy odbiorców centrum zdarzeń](media/send-events/consumer-group.png)

6. W obszarze **jednostek**, wybierz opcję **grupy konsumentów**.
 
7. Upewnij się, że utworzona grupa odbiorców jest używana wyłącznie przez źródło zdarzeń usługi Time Series Insights.

   > [!IMPORTANT]
   > Sprawdź, czy ta grupa odbiorców nie jest używana przez żadną inną usługę (np. zadanie usługi Stream Analytics lub drugie środowisko usługi Time Series Insights). Jeśli grupa odbiorców jest używana przez inne usługi, operacja odczytu ma negatywny wpływ na to środowisko i innych usług. Użycie grupy odbiorców „$Default” może potencjalnie spowodować jej ponowne użycie przez inne czytniki.

8. W obszarze **ustawienia** nagłówka, wybierz **zasad dostępu udziału**.

9. W tym Centrum zdarzeń Utwórz **Moje_zasady_wysyłania** używany do wysyłania zdarzeń w przykładzie w języku csharp.

  ![Wybieranie zasad dostępu współdzielonego i klikanie przycisku Dodaj](media/send-events/shared-access-policy.png)  

  ![Dodawanie nowej zasady dostępu współdzielonego](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Dodawanie zestawu danych referencyjnych usługi Time Series Insights 
Przy użyciu danych referencyjnych w usłudze TSI będąca dane telemetryczne.  Ten kontekst dodaje znaczenia z danymi i ułatwia do filtrowania i agregacji.  Usługa TSI Przesyła sprzężeń odwołują się do danych podczas transferu danych przychodzących i wstecznie nie można dołączyć do tych danych.  Dlatego ważne jest do dodawania danych referencyjnych przed dodaniem źródła zdarzeń z danymi.  Dane, takie jak typ lokalizacji lub czujnika są przydatne wymiarów, które możesz chcieć dołączyć do urządzenia/tagu/czujnika identyfikator, aby ułatwić wycinka i filtru.  

> [!IMPORTANT]
> Zestawu danych referencyjnych, skonfigurować posiadające bezwzględnie podczas przekazywania danych historycznych.

Upewnij się, że dane referencyjne w miejscu przypadku zbiorczego przekazania danych historycznych w celu TSI.  Należy pamiętać, TSI zostanie natychmiast rozpocząć odczytywanie od źródła zdarzeń dołączonym do Jeśli tego źródła zdarzeń zawiera dane.  Jest to przydatne oczekiwania do dołączenia do źródła zdarzeń do TSI aż do uzyskania danych odwołania w miejscu, zwłaszcza, jeśli tego źródła zdarzeń zawiera dane. Alternatywnie możesz poczekać, wypychać dane do tego źródła zdarzeń, aż do zestawu danych referencyjnych w miejscu.

Aby zarządzać danymi referencyjnymi, w Eksploratorze TSI jest interfejs użytkownika oparty na sieci web i ma programowego interfejsu API języka C#. Eksplorator usługi TSI ma visual użytkowników do przekazywania plików lub Wklej odwołanie istniejących zestawów danych w formacie JSON lub CSV. Za pomocą interfejsu API można utworzyć niestandardową aplikację w razie.

Aby uzyskać więcej informacji na temat zarządzania danymi referencyjnymi w usługi Time Series Insights, zobacz [artykule informacyjnym na temat danych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń usługi Time Series Insights
1. Jeśli nie utworzono źródła zdarzeń, postępuj zgodnie z [tymi instrukcjami](time-series-insights-how-to-add-an-event-source-eventhub.md), aby je utworzyć.

2. Określ **deviceTimestamp** jako nazwę właściwości sygnatury czasowej — właściwość ta jest używana jako rzeczywista sygnatura czasowa w przykładzie języka C#. W nazwie właściwości sygnatury czasowej jest uwzględniana wielkość liter. Wartości wysyłane do centrum zdarzeń jako dane JSON muszą mieć format __rrrr-MM-ddTGG:mm:ss.FFFFFFFK__. Jeśli w zdarzeniu nie ma tej właściwości, używany jest czas umieszczenia zdarzenia w kolejce w centrum zdarzeń.

  ![Tworzenie źródła zdarzeń](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Przykładowy kod umożliwiający wypychanie zdarzeń
1. Przejdź do zasad Centrum zdarzeń, o nazwie **Moje_zasady_wysyłania**. Kopiuj **parametry połączenia** z kluczem zasad.

  ![Kopiowanie parametrów połączenia Moja_zasada_wysyłania](media/send-events/sample-code-connection-string.png)

2. Uruchom poniższy kod, który umożliwia wysyłanie 600 zdarzeń do każdego z trzech urządzeń. Zaktualizuj zmienną `eventHubConnectionString` przy użyciu parametrów połączenia.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
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
> [Wyświetlanie środowiska w Eksploratorze usługi Time Series Insights](https://insights.timeseries.azure.com).
