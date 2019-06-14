---
title: Śledzenia end-to-end w usłudze Azure Service Bus i Diagnostyka | Dokumentacja firmy Microsoft
description: Omówienie diagnostyki klient usługi Service Bus i śledzenia end-to-end
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 6e5895392db1d75a985674bf2f878a84bc8dd926
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311016"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Rozproszonego śledzenia i korelacji, za pomocą komunikatów usługi Service Bus

Jedną z typowych problemów podczas tworzenia mikrousług jest możliwość śledzenia operacji klienta za pośrednictwem usług, które są zaangażowane w przetwarzaniu. Jest to przydatne do debugowania i analizy wydajności, A / B, testowanie i innych scenariuszy typowe diagnostyki.
Logiczne fragmentów zadań służy do śledzenia jedną część tego problemu. Obejmuje ona wiadomości, takich jak przetwarzanie wynik i czas oczekiwania i wywołania zależności zewnętrznych. Innej części jest korelacji te zdarzenia diagnostyczne poza granice procesu.

Gdy producent wysyła komunikat do kolejki, zazwyczaj miejsce w zakresie niektórych innych operacji logicznej, inicjowane przez inne klienta lub usługę. Tej samej operacji jest kontynuowane przez konsumenta, po odebraniu komunikatu. Zarówno producentów i konsumentów (i innych usług, które przetwarzają operacji), prawdopodobnie emitować zdarzenia telemetrii, aby prześledzić przepływ operacji i wynik. Aby skorelować takich zdarzeń i śledzenia operacji end-to-end, wszystkich usług, które raporty telemetrii ma sygnatury każdego zdarzenia z kontekstem śledzenia.

Microsoft Azure Service Bus messaging został zdefiniowany właściwości ładunek, które producentów i konsumentów powinna być używana do przekazania takich kontekst śledzenia.
Protokół opiera się na [protokołu HTTP korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Nazwa właściwości        | Opis                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Unikatowy identyfikator wywołania zewnętrznego od producenta do kolejki. Zapoznaj się [w protokole HTTP Request-Id](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) uzasadnienie, uwagi i format |
|  Kontekst korelacji | Kontekst operacji, który jest propagowany dla wszystkich usług zaangażowanych w operację przetwarzania. Aby uzyskać więcej informacji, zobacz [kontekstowe korelacji w protokole HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Klient modelu .NET magistrali usługi automatycznego śledzenia

Począwszy od wersji 3.0.0 [klienta magistrali usług Microsoft Azure dla platformy .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) zapewnia punktów Instrumentacji śledzenia, które mogą być dołączane przez systemy śledzenia lub fragment kodu klienta.
Instrumentacja umożliwia śledzenie wszystkich wywołań do usługi Service Bus messaging po stronie klienta w ramach usługi. Jeśli przetwarzanie komunikatów odbywa się za pomocą [wzorzec procedury obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), przetwarzanie wiadomości również ma Instrumentacji

### <a name="tracking-with-azure-application-insights"></a>Śledzenie za pomocą usługi Azure Application Insights

[Usługa Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) zapewnia rozbudowane możliwości, takie jak żądania automagical i śledzenia zależności do monitorowania wydajności.

W zależności od typu projektu należy zainstalować zestaw SDK usługi Application Insights:
- [ASP.NET](../azure-monitor/app/asp-net.md) — instalowanie wersji 2.5-beta2 lub nowszej
- [Platforma ASP.NET Core](../azure-monitor/app/asp-net-core.md) — instalowanie wersji 2.2.0-beta2 lub nowszej.
Te linki zawierają szczegółowe informacje na temat instalowania zestawu SDK, tworzenia zasobów i konfigurowanie zestawu SDK (jeśli jest to konieczne). Aplikacje niedotyczący środowiska ASP.NET można znaleźć [usługi Azure Application Insights dla aplikacji konsoli](../azure-monitor/app/console.md) artykułu.

Jeśli używasz [wzorzec procedury obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) do przetwarzania komunikatów, wszystko będzie gotowe: wszystkie wywołania usługi Service Bus, wykonywane przez usługę są automatycznie śledzone i skorelowane z innych elementów telemetrii. W przeciwnym razie odnoszą się do przetwarzania ręcznie komunikat śledzenia w poniższym przykładzie.

#### <a name="trace-message-processing"></a>Przetwarzanie komunikatów śledzenia

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

W tym przykładzie `RequestTelemetry` jest zgłaszana dla każdego komunikatu przetworzone, timestamp, czas trwania i wynik (Powodzenie). Dane telemetryczne ma również zbiór właściwości korelacji.
Zagnieżdżone ślady i wyjątki zgłoszone podczas przetwarzania komunikatu również sygnaturę z właściwościami korelacji reprezentujący je jako "podrzędne" `RequestTelemetry`.

W przypadku, gdy utworzysz wywołania obsługiwanych składników zewnętrznych podczas przetwarzania komunikatu, są automatycznie śledzone i skorelowane. Zapoznaj się [śledzenie operacji niestandardowych za pomocą zestawu SDK .NET usługi Application Insights](../azure-monitor/app/custom-operations-tracking.md) do ręcznego śledzenia i korelacji.

### <a name="tracking-without-tracing-system"></a>Śledzenie bez śledzenia systemu
W przypadku, gdy system śledzenia nie obsługuje automatycznego wywołań usługi Service Bus, śledzenie zostanie może przyjrzeć Dodawanie takich obsługi w systemie śledzenia lub w aplikacji. W tej sekcji opisano zdarzenia diagnostyczne wysłane przez klienta usługi Service Bus .NET.  

Klient modelu .NET magistrali usługi został zinstrumentowany przy użyciu platformy .NET śledzenia podstawowych [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` Służy jako kontekst śledzenia podczas `DiagnosticSource` mechanizm powiadomień. 

W przypadku brak odbiornika zdarzeń DiagnosticSource Instrumentacji jest wyłączona, utrzymania zero kosztów instrumentacji. DiagnosticSource zapewnia kontrolę wszystkie połączenia z odbiornikiem:
- odbiornik kontroluje które pozyskuje i zdarzenia do nasłuchiwania
- szybkość zdarzeń formantów odbiornik i próbkowania
- zdarzenia są wysyłane z ładunkiem, który zapewnia pełny kontekst, dzięki czemu mogą uzyskać dostęp i zmodyfikować obiekt komunikatu podczas zdarzenia

Zapoznaj się z [Podręcznik użytkownika DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) przed przejściem do implementacji.

W aplikacji ASP.NET Core, która zapisuje dzienników za pomocą Microsoft.Extension.Logger Utwórzmy odbiornika dla zdarzeń usługi Service Bus.
Używa ona [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) biblioteki do subskrybowania DiagnosticSource (jest to również łatwe subskrybowanie DiagnosticSource bez niego)

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

#### <a name="events"></a>Events

Dla każdej operacji dwa zdarzenia są wysyłane: "Start" i "Stop". Najbardziej prawdopodobnie interesuje Cię tylko zdarzenia "Stop". One zwraca wynik operacji, a także godzina rozpoczęcia, czas trwania jako właściwości działania.

Ładunek zdarzenia zawiera odbiornika z kontekstem operacji, replikacji przychodzącej parametry interfejsu API i zwraca wartość. "Stop" ładunek zdarzenia ma właściwości "Start" ładunek zdarzenia, dzięki czemu możesz zignorować całkowicie zdarzeń "Start".

Wszystkie zdarzenia mają również właściwości "Entity" i "Endpoint", zostały pominięte w poniższej tabeli
  * `string Entity` — Nazwa jednostki (kolejki, tematu, itp.)
  * `Uri Endpoint` — Adres URL punktu końcowego Usługa Service Bus

Każde zdarzenie "Stop" ma `Status` właściwość o `TaskStatus` wykonał asynchroniczną operację, która również jest pomijane w poniższej tabeli dla uproszczenia.

Poniżej przedstawiono pełną listę instrumentowanych operacje:

| Nazwa operacji | Śledzone interfejsu API | Ładunek określonej właściwości|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` -Lista komunikaty przesyłane |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` -Przetwarzanego komunikatu<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` -Przesunięcie zaplanowanych wiadomości<br/>`long SequenceNumber` -Numer sekwencyjny zaplanowanych wiadomości ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` -Numer sekwencyjny komunikatu te zostaną anulowane | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` Maksymalna liczba wiadomości, które mogą być odbierane.<br/>`IList<Message> Messages` -Lista Odebrane komunikaty ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` — Punkt początkowy z którego ma zostać Przeglądaj partię komunikatów.<br/>`int RequestedMessageCount` — Liczba komunikatów do pobrania.<br/>`IList<Message> Messages` -Lista Odebrane komunikaty ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` -Lista zawierająca numery sekwencyjne do odbierania.<br/>`IList<Message> Messages` -Lista Odebrane komunikaty ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` -Lista zawierająca tokeny blokady odpowiednich komunikatów, aby zakończyć.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` -Token blokady odpowiednie wiadomości do porzucenia. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` -Token blokady odpowiedniego komunikatu, które mają być odroczone. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` -Token blokady odpowiedniego komunikatu do utraconych wiadomości. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` -Token blokady odpowiedniego komunikatu do odnowienia blokady na.<br/>`DateTime LockedUntilUtc` -New blokady na datę i godzinę w formacie UTC wygaśnięcia tokenu. ("Stop" ładunek zdarzenia)|
| Microsoft.Azure.ServiceBus.Process | Funkcja lambda obsługi wiadomości w [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` -Przetwarzanego komunikatu. |
| Microsoft.Azure.ServiceBus.ProcessSession | Funkcji lambda sesji obsługi wiadomości podano w [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` -Przetwarzanego komunikatu.<br/>`IMessageSession Session` -Sesji przetwarzane |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` Opis reguły, który zawiera reguły, aby dodać. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` — Nazwa reguły do usunięcia. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` — Wszystkie reguły skojarzone z tą subskrypcją. (Tylko "Stop" ładunku) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` SessionId jest obecny w komunikatach. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` SessionId jest obecny w komunikatach.<br/>`byte [] State` — Stan sesji ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` SessionId jest obecny w komunikatach.<br/>`byte [] State` -Stan sesji |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` SessionId jest obecny w komunikatach. |
| Microsoft.Azure.ServiceBus.Exception | dowolnego instrumentowanych interfejsu API| `Exception Exception` — Wystąpienie wyjątek |

W każdym przypadku możesz uzyskać dostęp `Activity.Current` przechowuje bieżącego kontekstu operacji.

#### <a name="logging-additional-properties"></a>Dodatkowe właściwości rejestrowania

`Activity.Current` zapewnia szczegółowe kontekście bieżącej operacji i jego elementów nadrzędnych. Aby uzyskać więcej informacji, zobacz [dokumentacja działania](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) Aby uzyskać więcej informacji.
Instrumentacja usługi Service Bus udostępnia dodatkowe informacje w `Activity.Current.Tags` -przechowują `MessageId` i `SessionId` zawsze, gdy są one dostępne.

Działania, które śledzą "Odbierz", "Odbierz" i "ReceiveDeferred" zdarzenia również może mieć `RelatedTo` tagu. Przechowuje listę odrębnych `Diagnostic-Id`(s) wiadomości, które zostały odebrane w wyniku.
Działanie takie może spowodować kilka niezależnych komunikatów do odbioru. Ponadto `Diagnostic-Id` nie jest znany po uruchomieniu operacji, dzięki czemu operacje "Odbierz" może zostać skorelowane z operacji "Proces" przy użyciu tylko ten Tag. Może to być przydatne, gdy analizowania problemów z wydajnością, aby sprawdzić, jak długo otrzymać wiadomość.

Wydajny sposób logowania tagów jest iteracja je, aby dodać znaczniki do poprzedniego przykładu wygląda 

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

#### <a name="filtering-and-sampling"></a>Filtrowanie i pobierania próbek

W niektórych przypadkach jest pożądane logowania tylko część zdarzeń, aby zmniejszyć zużycie obciążenie lub magazynu wydajności. Można zalogować "Stop" zdarzeń tylko (tak jak w poprzednim przykładzie) lub procent przykładowe zdarzenia. 
`DiagnosticSource` zapewnia sposób osiągnięcia ją za pomocą `IsEnabled` predykat. Aby uzyskać więcej informacji, zobacz [oparte na kontekście filtrowanie DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` może zostać wywołana wiele razy dla jednej operacji zminimalizować wpływ na wydajność.

`IsEnabled` jest wywoływana w następującej sekwencji:

1. `IsEnabled(<OperationName>, string entity, null)` na przykład `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Należy pamiętać, że nie ma "Start" lub "Stop" na końcu. Umożliwia filtrowanie określonych operacji lub kolejek. Jeśli funkcja wywołania zwrotnego zwraca `false`, zdarzenia dla tej operacji nie są wysyłane.

   * Dla operacji "Process" i "Encjami ProcessSession", otrzymasz także `IsEnabled(<OperationName>, string entity, Activity activity)` wywołania zwrotnego. Umożliwia filtrowanie na podstawie zdarzeń `activity.Id` lub tagi właściwości.
  
2. `IsEnabled(<OperationName>.Start)` na przykład `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Sprawdza, czy zdarzenie "Start" powinny być uruchamiane. Wynik ma wpływ tylko na zdarzenie "Start", ale dalsze Instrumentacji nie od niej zależnych.

Istnieje nie `IsEnabled` dla zdarzenia "Stop".

Jeśli niektóre wynik operacji jest wyjątek, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` jest wywoływana. Można tylko subskrybowanie zdarzeń "Exception" i zapobiec pozostałą część Instrumentację. W takim przypadku nadal masz do obsługi wyjątków. Ponieważ inną Instrumentację jest wyłączona, nie należy się spodziewać kontekst śledzenia przepływu za pomocą wiadomości od konsumenta do producenta.

Możesz użyć `IsEnabled` również wdrożenie strategii próbkowania. Na podstawie próbkowania `Activity.Id` lub `Activity.RootId` zapewnia spójne próbkowanie we wszystkich tires (tak długo, jak go jest propagowany przez system śledzenia lub własnego kodu).

W obecności wielu `DiagnosticSource` odbiorników tego samego źródła, wystarczy, aby tylko jeden odbiornik do akceptowania zdarzenia, więc `IsEnabled` nie musi być wywoływana,

## <a name="next-steps"></a>Kolejne kroki

* [Application Insights korelacji](../azure-monitor/app/correlation.md)
* [Application Insights monitora zależności](../azure-monitor/app/asp-net-dependencies.md) aby zobaczyć, jeśli REST, programu SQL lub inne zasoby zewnętrzne nie spowalniają.
* [Śledzenie operacji niestandardowych za pomocą zestawu SDK .NET usługi Application Insights](../azure-monitor/app/custom-operations-tracking.md)
