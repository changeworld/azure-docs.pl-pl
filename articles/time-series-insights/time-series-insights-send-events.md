---
title: Sposób wysyłania zdarzeń do środowiska Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym samouczku wyjaśniono, jak utworzyć i skonfigurować Centrum zdarzeń i uruchomić przykładową aplikację do wypychania zdarzeń ma być wyświetlany w usłudze Azure czas serii Insights.
ms.service: time-series-insights
services: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: db528f5a02d90e7e1e2e2cd3da30f04755575777
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657792"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Wysyłanie zdarzeń do środowiska usługi Time Series Insights za pomocą centrum zdarzeń
W tym artykule wyjaśniono, jak utworzyć i skonfigurować Centrum zdarzeń i uruchom przykładową aplikację do wypychania zdarzeń. Jeśli masz istniejące Centrum zdarzeń z zdarzenia w formacie JSON, Pomiń tego samouczka i wyświetlić środowiska w [Insights serii czas](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Konfigurowanie centrum zdarzeń
1. Aby utworzyć centrum zdarzeń, wykonaj instrukcje zawarte w [dokumentacji](../event-hubs/event-hubs-create.md) centrum zdarzeń.

2. Wyszukaj **Centrum zdarzeń** na pasku wyszukiwania. Kliknij przycisk **usługi Event Hubs** umieszczone na zwracanej liście.

3. Wybierz Centrum zdarzeń, klikając jej nazwę.

4. W obszarze **jednostek** kliknij w oknie Konfiguracja środkowej **usługi Event Hubs** ponownie.

5. Wybierz nazwę Centrum zdarzeń, aby go skonfigurować.

  ![Wybieranie grupy odbiorców centrum zdarzeń](media/send-events/consumer-group.png)

6. W obszarze **jednostek**, wybierz pozycję **grupy konsumentów**.
 
7. Upewnij się, że utworzona grupa odbiorców jest używana wyłącznie przez źródło zdarzeń usługi Time Series Insights.

   > [!IMPORTANT]
   > Sprawdź, czy ta grupa odbiorców nie jest używana przez żadną inną usługę (np. zadanie usługi Stream Analytics lub drugie środowisko usługi Time Series Insights). Grupa użytkowników jest używana przez inne usługi, operacja odczytu jest negatywny wpływ na tego środowiska i innych usług. Użycie grupy odbiorców „$Default” może potencjalnie spowodować jej ponowne użycie przez inne czytniki.

8. W obszarze **ustawienia** nagłówek, wybierz **zasady dostępu do udziału**.

9. W Centrum zdarzeń, Utwórz **MySendPolicy** używany do wysyłania zdarzeń w przykładowym csharp.

  ![Wybieranie zasad dostępu współdzielonego i klikanie przycisku Dodaj](media/send-events/shared-access-policy.png)  

  ![Dodawanie nowej zasady dostępu współdzielonego](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Dodaj zestaw danych odwołania Insights serii czasu 
Przy użyciu danych referencyjnych w TSI contextualizes danych telemetrii.  Tego kontekstu dodaje znaczenie danych i ułatwia filtru i agregacji.  Sprzężenia TSI odwoływał się do danych w czasie wejściowych i Wstecz nie można połączyć te dane.  W związku z tym jest krytyczny można dodać danych referencyjnych przed dodaniem źródła zdarzenia z danymi.  Dane, takie jak typ lokalizacji lub czujnik są przydatne wymiarów, które można dołączyć do urządzenia/tagu/czujnika ID, aby ułatwić wycinka i filtru.  

> [!IMPORTANT]
> Bardzo ważne jest posiadanie zestawem danych odwołania skonfigurowane, podczas przekazywania danych historycznych.

Sprawdź, czy mają danych referencyjnych w miejscu przypadku zbiorczego przekazywania danych historycznych w celu TSI.  Należy pamiętać, TSI zostanie od razu rozpocząć odczytywanie od źródła zdarzeń przyłączone do Jeśli źródła zdarzenia zawiera dane.  Warto czekać do dołączania źródła zdarzenia do TSI aż do uzyskania danych odwołania w miejscu, zwłaszcza, jeśli źródła zdarzenia zawiera dane w nim. Alternatywnie można poczekać do dystrybuowania danych do źródła zdarzeń, dopóki nie jest odwołanie do zestawu danych w miejscu.

Aby zarządzać danych referencyjnych, w Eksploratorze TSI jest interfejs użytkownika sieci web, a istnieje programowe API języka C#. TSI Eksploratora ma visual interfejs do przekazywania plików lub wklejania w istniejącej relacji zestawów danych w formacie JSON lub CSV użytkownika. Przy użyciu interfejsu API można tworzyć niestandardowych aplikacji w razie potrzeby.

Aby uzyskać więcej informacji na temat zarządzania danych referencyjnych w czasie serii Insights, zobacz [danych artykule](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Tworzenie źródła zdarzeń usługi Time Series Insights
1. Jeśli nie utworzono źródła zdarzeń, postępuj zgodnie z [tymi instrukcjami](time-series-insights-how-to-add-an-event-source-eventhub.md), aby je utworzyć.

2. Określ **deviceTimestamp** jako nazwy właściwości sygnatury czasowej — ta właściwość jest używana jako rzeczywista sygnatury czasowej w przykładowym C#. W nazwie właściwości sygnatury czasowej jest uwzględniana wielkość liter. Wartości wysyłane do centrum zdarzeń jako dane JSON muszą mieć format __rrrr-MM-ddTGG:mm:ss.FFFFFFFK__. Jeśli w zdarzeniu nie ma tej właściwości, używany jest czas umieszczenia zdarzenia w kolejce w centrum zdarzeń.

  ![Tworzenie źródła zdarzeń](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Przykładowy kod umożliwiający wypychanie zdarzeń
1. Przejdź do Centrum zdarzeń zasady o nazwie **MySendPolicy**. Kopiuj **ciąg połączenia** kluczem zasad.

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

Obiekt JSON z zagnieżdżonych tablicy JSON, który zawiera dwa obiekty JSON:
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
Należy zauważyć, że właściwość "Lokalizacja" są kopiowane do każdego zdarzenia.

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
> [Wyświetl środowiska w Eksploratorze Insights serii czas](https://insights.timeseries.azure.com).
