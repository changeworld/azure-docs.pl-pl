---
title: Kompleksowe śledzenie i diagnostyka usługi Azure Service Bus | Dokumenty firmy Microsoft
description: Omówienie diagnostyki klienta usługi Service Bus i śledzenia end-to-end (klient za pośrednictwem wszystkich usług, które są zaangażowane w przetwarzanie).)
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7c2efc9c736097873201505f280af5d47bed4847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294175"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Rozproszone śledzenie i korelacja za pośrednictwem obsługi wiadomości usługi Service Bus

Jednym z typowych problemów w rozwoju mikrousług jest możliwość śledzenia operacji od klienta za pośrednictwem wszystkich usług, które są zaangażowane w przetwarzanie. Jest to przydatne do debugowania, analizy wydajności, testowania A/B i innych typowych scenariuszy diagnostycznych.
Jedną z części tego problemu jest śledzenie logicznych elementów pracy. Zawiera wynik przetwarzania wiadomości i opóźnienia i wywołania zależności zewnętrznych. Inną częścią jest korelacja tych zdarzeń diagnostycznych poza granicami procesu.

Gdy producent wysyła wiadomość za pośrednictwem kolejki, zazwyczaj dzieje się to w zakresie innej operacji logicznej, zainicjowanej przez innego klienta lub usługę. Ta sama operacja jest kontynuowana przez konsumenta po otrzymaniu wiadomości. Zarówno producent, jak i konsument (i inne usługi, które przetwarzają operację), prawdopodobnie emitują zdarzenia telemetryczne w celu śledzenia przepływu operacji i wyniku. Aby skorelować takie zdarzenia i śledzenia operacji end-to-end, każda usługa, która raportuje dane telemetryczne musi stemplować każde zdarzenie z kontekstu śledzenia.

Usługa Microsoft Azure Service Bus wiadomości zdefiniowała właściwości ładunku, które producenci i konsumenci powinni używać do przekazywania takiego kontekstu śledzenia.
Protokół jest oparty na [protokole korelacji HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Nazwa właściwości        | Opis                                                 |
|----------------------|-------------------------------------------------------------|
|  Identyfikator diagnostyczny       | Unikatowy identyfikator wywołania zewnętrznego od producenta do kolejki. Aby uzyskać informacje o uzasadnieniu, uwagach i formacie, zapoznaj się z [identyfikatorem żądania w protokole HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) |
|  Korelacja-Kontekst | Kontekst operacji, który jest propagowany we wszystkich usługach zaangażowanych w przetwarzanie operacji. Aby uzyskać więcej informacji, zobacz [Correlation-Context w protokole HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Automatyczne śledzenie klienta usługi Service Bus .NET

Począwszy od wersji 3.0.0 [Microsoft Azure ServiceBus Client dla platformy .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) zapewnia punkty instrumentacji śledzenia, które mogą być podłączone przez systemy śledzenia lub fragment kodu klienta.
Instrumentacja umożliwia śledzenie wszystkich wywołań usługi obsługi wiadomości usługi Service Bus od strony klienta. Jeśli przetwarzanie wiadomości odbywa się za pomocą [wzorca obsługi wiadomości,](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)przetwarzanie wiadomości jest również instrumentowane

### <a name="tracking-with-azure-application-insights"></a>Śledzenie za pomocą usługi Azure Application Insights

[Usługa Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) zapewnia zaawansowane funkcje monitorowania wydajności, w tym automatyczne śledzenie żądań i zależności.

W zależności od typu projektu zainstaluj pakiet SDK usługi Application Insights:
- [ASP.NET](../azure-monitor/app/asp-net.md) - instalacja w wersji 2.5-beta2 lub nowszej
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - zainstaluj wersję 2.2.0-beta2 lub wyższą.
Te łącza zawierają szczegółowe informacje na temat instalowania zestawu SDK, tworzenia zasobów i konfigurowania zestawu SDK (w razie potrzeby). Aby uzyskać non-ASP.NET aplikacje, zapoznaj się z artykułem [usługi Azure Application Insights for Console Applications.](../azure-monitor/app/console.md)

Jeśli używasz [wzorzec obsługi wiadomości](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) do przetwarzania wiadomości, gotowe: wszystkie wywołania usługi Service Bus wykonywane przez usługę są automatycznie śledzone i skorelowane z innymi elementami telemetrii. W przeciwnym razie odnoszą się do następującego przykładu ręcznego śledzenia przetwarzania wiadomości.

#### <a name="trace-message-processing"></a>Śledzenie przetwarzania wiadomości

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

W tym `RequestTelemetry` przykładzie jest zgłaszane dla każdej przetworzonej wiadomości, o sygnatury czasowej, czas trwania i wynik (sukces). Dane telemetryczne ma również zestaw właściwości korelacji.
Zagnieżdżone ślady i wyjątki zgłaszane podczas przetwarzania wiadomości są również stemplowane `RequestTelemetry`właściwościami korelacji reprezentującymi je jako "dzieci" .

W przypadku, gdy nawiązywane są połączenia z obsługiwanymi składnikami zewnętrznymi podczas przetwarzania wiadomości, są one również automatycznie śledzone i skorelowane. Aby uzyskać ręczne śledzenie i korelację, zapoznaj się z instrukcjami operacji niestandardowych aplikacji za [pomocą narzędzia Application Insights .NET SDK.](../azure-monitor/app/custom-operations-tracking.md)

Jeśli używasz dowolnego kodu zewnętrznego oprócz SDK usługi Application Insights, spodziewaj się **dłuższego czasu trwania** podczas wyświetlania dzienników usługi Application Insights. 

![Dłuższy czas trwania w dzienniku usługi Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

Nie oznacza to, że wystąpiło opóźnienie w otrzymaniu wiadomości. W tym scenariuszu wiadomość została już odebrana, ponieważ wiadomość jest przekazywana jako parametr do kodu SDK. I tag **nazwy** w dziennikach usługi App Insights **(Proces)** wskazuje, że wiadomość jest obecnie przetwarzane przez kod przetwarzania zdarzeń zewnętrznych. Ten problem nie jest związany z platformą Azure. Zamiast tego te metryki odnoszą się do wydajności kodu zewnętrznego, biorąc pod uwagę, że komunikat został już odebrany z usługi Service Bus. Zobacz [ten plik w usłudze GitHub,](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) aby zobaczyć, gdzie jest generowany i przypisywany tag **Process** po odebraniu wiadomości z usługi Service Bus. 

### <a name="tracking-without-tracing-system"></a>Śledzenie bez systemu śledzenia
W przypadku, gdy system śledzenia nie obsługuje automatycznego śledzenia połączeń usługi Service Bus, możesz chcieć dodać taką pomoc techniczną do systemu śledzenia lub do aplikacji. W tej sekcji opisano zdarzenia diagnostyczne wysyłane przez klienta usługi Service Bus .NET.  

Klient usługi Service Bus .NET jest instrumentowany przy użyciu pliku .NET tracing primitives [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) and [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity`służy jako kontekst `DiagnosticSource` śledzenia, podczas gdy jest mechanizmem powiadamiania. 

Jeśli nie ma odbiornika dla DiagnosticSource zdarzeń, instrumentacji jest wyłączony, zachowując zero kosztów instrumentacji. DiagnosticSource daje wszystkie kontroli do odbiornika:
- odbiornik kontroluje, które źródła i zdarzenia, aby słuchać
- odbiornik kontroluje szybkość zdarzeń i próbkowanie
- zdarzenia są wysyłane z ładunkiem, który zapewnia pełny kontekst, dzięki czemu można uzyskać dostęp i modyfikować Obiekt Message podczas zdarzenia

Zapoznaj się z [DiagnosticSource Podręcznik użytkownika](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) przed kontynuowaniem implementacji.

Utwórzmy odbiornik zdarzeń usługi Service Bus w aplikacji ASP.NET Core, która zapisuje dzienniki za pomocą witryny Microsoft.Extension.Logger.
Używa [biblioteki System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) do subskrybowania DiagnosticSource (jest również łatwo subskrybować DiagnosticSource bez niego)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

W tym przykładzie odbiornik rejestruje czas trwania, wynik, unikatowy identyfikator i czas rozpoczęcia dla każdej operacji usługi Service Bus.

#### <a name="events"></a>Zdarzenia

Dla każdej operacji są wysyłane dwa zdarzenia: "Start" i "Stop". Najprawdopodobniej interesuje Cię tylko wydarzenia "Stop". Zapewniają one wynik operacji, a także czas rozpoczęcia i czas trwania jako właściwości działania.

Ładunek zdarzeń zapewnia detektorowi kontekst operacji, replikuje parametry przychodzące interfejsu API i zwraca wartość. Ładunek zdarzenia "Stop" ma wszystkie właściwości ładunku zdarzenia "Start", dzięki czemu możesz całkowicie zignorować zdarzenie "Start".

Wszystkie zdarzenia mają również właściwości "Jednostka" i "Punkt końcowy", są one pominięte w poniższej tabeli
  * `string Entity`- - Nazwa jednostki (kolejka, temat itp.)
  * `Uri Endpoint`- Adres URL punktu końcowego usługi Service Bus

Każde zdarzenie "Stop" `Status` `TaskStatus` ma właściwość z operacją asynchronizowania została ukończona, która jest również pominięta w poniższej tabeli dla uproszczenia.

Oto pełna lista operacji instrumentowanych:

| Nazwa operacji | Śledzony interfejs API | Określone właściwości ładunku|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`- Lista wysyłanych wiadomości |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageSync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`- Wiadomość jest przetwarzana<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`- Zaplanowane przesunięcie wiadomości<br/>`long SequenceNumber`- Numer sekwencyjny zaplanowanej wiadomości (ładunek zdarzenia "Stop") |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber`- Numer sekwencyjny te wiadomości do anulowania | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`- Maksymalna liczba wiadomości, które mogą być odbierane.<br/>`IList<Message> Messages`- Lista odebranych wiadomości (ładowność zdarzenia "Stop") |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`- Punkt wyjścia, od którego można przeglądać partię wiadomości.<br/>`int RequestedMessageCount`- Liczba wiadomości do pobrania.<br/>`IList<Message> Messages`- Lista odebranych wiadomości (ładowność zdarzenia "Stop") |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`- Lista zawierająca numery sekwencowe do odebrania.<br/>`IList<Message> Messages`- Lista odebranych wiadomości (ładowność zdarzenia "Stop") |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`- Lista zawierająca tokeny blokady odpowiednich komunikatów do wypełnienia.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`- Token blokady odpowiedniego komunikatu do porzucenia. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`- Token blokady odpowiedniej wiadomości do odroczenia. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`- Token blokady odpowiedniej wiadomości do martwej litery. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`- Token blokady odpowiedniego komunikatu, aby odnowić blokadę.<br/>`DateTime LockedUntilUtc`- Nowa data wygaśnięcia tokenu blokady i godzina w formacie UTC. (Ładowność zdarzenia "Stop")|
| Microsoft.Azure.ServiceBus.Process | Funkcja lambda obsługi wiadomości podana w [pliku IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message`- Wiadomość jest przetwarzana. |
| Microsoft.Azure.ServiceBus.ProcessSession | Funkcja lambda programu Message Session Handler dostępna w [pliku IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message`- Wiadomość jest przetwarzana.<br/>`IMessageSession Session`- Sesja jest przetwarzana |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`- Opis reguły, która zawiera regułę do dodania. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`- Nazwa reguły do usunięcia. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`- Wszystkie reguły związane z subskrypcją. (tylko ładowność "Stop") |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`- SessionId obecny w wiadomościach. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`- SessionId obecny w wiadomościach.<br/>`byte [] State`- Stan sesji (ładowność zdarzenia "Zatrzymaj") |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`- SessionId obecny w wiadomościach.<br/>`byte [] State`- Stan sesji |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`- SessionId obecny w wiadomościach. |
| Microsoft.Azure.ServiceBus.Exception | dowolny instrumentowany API| `Exception Exception`- Wystąpienie wyjątku |

W każdym przypadku można `Activity.Current` uzyskać dostęp, który zawiera bieżący kontekst operacji.

#### <a name="logging-additional-properties"></a>Rejestrowanie dodatkowych właściwości

`Activity.Current`zapewnia szczegółowy kontekst bieżącej działalności i jego rodziców. Aby uzyskać więcej informacji, zobacz [dokumentację działania, aby](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) uzyskać więcej informacji.
Oprzyrządowanie usługi Service `Activity.Current.Tags` Bus zawiera `MessageId` `SessionId` dodatkowe informacje w - posiadają i zawsze, gdy są one dostępne.

Działania, które śledzą zdarzenie "Receive", "Peek" i "ReceiveDeferred", również mogą mieć `RelatedTo` tag. Posiada odrębną `Diagnostic-Id`listę (s) wiadomości, które zostały odebrane w wyniku.
Takie działanie może spowodować odebranie kilku niepowiązanych komunikatów. Ponadto nie `Diagnostic-Id` jest znany po uruchomieniu operacji, więc operacje "Odbierz" mogą być skorelowane z operacjami "Proces" przy użyciu tylko tego tagu. Jest to przydatne podczas analizowania problemów z wydajnością, aby sprawdzić, jak długo trwało odebrawanie wiadomości.

Skutecznym sposobem rejestrowania tagów jest iterowanie nad nimi, więc dodanie tagów do poprzedniego przykładu wygląda tak, jak 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtrowanie i pobieranie próbek

W niektórych przypadkach jest pożądane, aby rejestrować tylko część zdarzeń, aby zmniejszyć obciążenie wydajności lub zużycie magazynu. Można rejestrować zdarzenia "Stop" tylko (jak w poprzednim przykładzie) lub przykładowy procent zdarzeń. 
`DiagnosticSource`sposób na osiągnięcie `IsEnabled` tego celu za pomocą predykatu. Aby uzyskać więcej informacji, zobacz [Filtrowanie kontekstowe w diagnosticsource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled`może być wywoływana wiele razy dla pojedynczej operacji, aby zminimalizować wpływ na wydajność.

`IsEnabled`jest wywoływana w następującej kolejności:

1. `IsEnabled(<OperationName>, string entity, null)`na przykład `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Uwaga, na końcu nie ma "Start" ani "Stop". Użyj go, aby odfiltrować określone operacje lub kolejki. Jeśli wywołanie `false`zwrotne zwraca, zdarzenia dla operacji nie są wysyłane

   * W przypadku operacji "Proces" i "ProcessSession" odbierasz `IsEnabled(<OperationName>, string entity, Activity activity)` również wywołanie zwrotne. Służy do filtrowania `activity.Id` zdarzeń na podstawie właściwości lub Tags.
  
2. `IsEnabled(<OperationName>.Start)`na przykład `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Sprawdza, czy zdarzenie "Start" powinno zostać podpalone. Wynik wpływa tylko na zdarzenie "Start", ale dalsze instrumentacji nie zależy od niego.

Nie ma `IsEnabled` dla zdarzenia "Stop".

Jeśli jakiś wynik operacji `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` jest wyjątkiem, jest wywoływana. Można tylko subskrybować zdarzenia "Wyjątek" i zapobiec pozostałej części instrumentacji. W takim przypadku nadal trzeba obsługiwać takie wyjątki. Ponieważ inne instrumentacji jest wyłączona, nie należy oczekiwać kontekstu śledzenia przepływu z komunikatów od konsumenta do producenta.

Można również `IsEnabled` użyć strategii pobierania próbek. Pobieranie próbek `Activity.Id` na `Activity.RootId` podstawie lub zapewnia spójne pobieranie próbek na wszystkich oponach (o ile jest propagowane przez system śledzenia lub przez własny kod).

W obecności `DiagnosticSource` wielu słuchaczy dla tego samego źródła, wystarczy tylko jeden słuchacz `IsEnabled` zaakceptować wydarzenie, więc nie jest gwarantowane, aby być wywoływane,

## <a name="next-steps"></a>Następne kroki

* [Korelacja usługi Application Insights](../azure-monitor/app/correlation.md)
* [Zależności monitora usługi Application Insights monitorują,](../azure-monitor/app/asp-net-dependencies.md) czy REST, SQL lub inne zasoby zewnętrzne spowalniają.
* [Śledzenie operacji niestandardowych za pomocą sdk usługi Application Insights .NET](../azure-monitor/app/custom-operations-tracking.md)
