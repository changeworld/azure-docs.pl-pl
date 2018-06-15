---
title: Śledzenia end-to-end usługi Azure Service Bus i informacji diagnostycznych | Dokumentacja firmy Microsoft
description: Omówienie diagnostyki klient usługi Service Bus i śledzenia end-to-end
services: service-bus-messaging
documentationcenter: ''
author: lmolkova
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 847056acd2d97391782dcac1874a2739b7f5825c
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2018
ms.locfileid: "27741335"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Śledzenie rozproszone i korelacji za pomocą komunikatów usługi Service Bus

Jednym z typowych problemów w rozwoju mikrousług jest możliwość operacji śledzenia z klienta za pośrednictwem usług, które są zaangażowane w przetwarzania. Jest przydatne w przypadku debugowania analizy wydajności, A / B, testowanie i innych scenariuszy typowe diagnostyki.
Logiczne fragmentów zadań służy do śledzenia jedną część tego problemu. Obejmuje on przetwarzania wyników i opóźnienia i wywołania zależności zewnętrznych komunikatów. Część innego jest korelacji tych zdarzeń diagnostycznych poza granice procesu.

Jeśli producent wysyła wiadomość do kolejki, zazwyczaj zdarza się w zakresie niektórych innych operacji logicznej, inicjowane przez niektóre klienta lub usługi. Tej samej operacji jest kontynuowany przez klienta po otrzymaniu komunikatu. Zarówno producenta i konsumenta (i innych usług, które przetwarzają operacji), prawdopodobnie Emituj zdarzenia telemetrii śledzenie przepływu operacji i wyników. W celu skorelowania takich zdarzeń i śledzenia operacji end-to-end, każda usługa, która raportuje telemetrii ma sygnatury każde zdarzenie z kontekstem śledzenia.

Microsoft Azure Service Bus wiadomości została zdefiniowana ładunku właściwości, które producenci i konsumenci mają zostać użyte do przekazywania takich kontekst śledzenia.
Protokół jest oparta na [protokołu HTTP korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Nazwa właściwości        | Opis                                                 |
|----------------------|-------------------------------------------------------------|
|  Identyfikator diagnostyki       | Unikatowy identyfikator zewnętrznego połączenia od producenta do kolejki. Zapoznaj się [Request-Id w protokole HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) uzasadnienie, zagadnień i formatu |
|  Kontekst korelacji | Kontekst operacji są propagowane we wszystkich usługach objętego operacji przetwarzania. Aby uzyskać więcej informacji, zobacz [kontekstu korelacji w protokole HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Klient .NET magistrali usługi automatycznego śledzenia

Począwszy od wersji 3.0.0 [klienta magistrali usług Microsoft Azure dla platformy .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) udostępniają punkty Instrumentacji śledzenia, które mogą być dołączane przez systemy śledzenia lub fragment kodu klienta.
Instrumentacja umożliwia śledzenie wszystkich wywołań do usługi Service Bus wiadomości usługi z po stronie klienta. Jeśli przetwarzanie komunikatów wykonuje się za pomocą [obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), przetwarzanie komunikatu jest również instrumentowany.

### <a name="tracking-with-azure-application-insights"></a>Śledzenie z usługą Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) zapewnia zaawansowane możliwości takie żądanie automagical i śledzenia zależności do monitorowania wydajności.

W zależności od danego typu projektu Zainstaluj zestaw SDK usługi Application Insights:
- [ASP.NET](../application-insights/app-insights-asp-net.md) wersji 2.5-beta2 lub nowszej
- [Platformy ASP.NET Core](../application-insights/app-insights-asp-net-core.md) wersji 2.2.0-beta2 lub nowszej.
Łącza te zawierają szczegółowe informacje dotyczące instalowania zestawu SDK, tworzenia zasobów i konfigurowania zestawu SDK (w razie potrzeby). Aplikacje z systemem innym niż ASP.NET można znaleźć w temacie [Azure Application Insights dla aplikacji konsoli](../application-insights/application-insights-console.md) artykułu.

Jeśli używasz [obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) do przetwarzania komunikatów, wszystko będzie gotowe: wszystkie wywołania usługi Service Bus wykonywane przez usługę automatycznie są śledzone i powiązane z innych elementów telemetrii. W przeciwnym razie odwoływać się do przetwarzania komunikatów ręcznego śledzenia w poniższym przykładzie.

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

W tym przykładzie `RequestTelemetry` został zgłoszony dla każdego komunikatu przetworzonej, posiadające sygnatury czasowej, czas trwania i wynik (Powodzenie). Dane telemetryczne ma również zbiór właściwości korelacji.
Zagnieżdżone śladów i wyjątków zgłoszonych podczas przetwarzania komunikatu są również oznaczony właściwości korelacji reprezentujący je jako "elementy podrzędne" `RequestTelemetry`.

W przypadku wywołania obsługiwanych składników zewnętrznych upewnij się podczas przetwarzania komunikatu, są one również automagically śledzone i skorelowane. Zapoznaj się [śledzenie działań niestandardowych z zestawu SDK .NET usługi Application Insights](../application-insights/application-insights-custom-operations-tracking.md) do ręcznego śledzenia i korelacji.

### <a name="tracking-without-tracing-system"></a>Śledzenie bez śledzenia systemu
W przypadku systemu śledzenia nie obsługuje wywołań usługi Service Bus automagical śledzenia może być szukasz do dodawania takich obsługi w systemie śledzenia lub w aplikacji. W tej sekcji opisano zdarzenia diagnostyczne wysyłany przez klienta .NET magistrali usługi.  

Klient .NET magistrali usługi został zinstrumentowany przy użyciu podstawowych śledzenie .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity`Służy jako kontekst śledzenia podczas `DiagnosticSource` mechanizm powiadomień. 

Jeśli nie ma żadnych odbiornika zdarzeń DiagnosticSource, Instrumentacji jest wyłączone, utrzymania zero kosztów instrumentacji. DiagnosticSource powoduje sterowania wszystkie odbiornika:
- odbiornik kontrolki które źródła i zdarzeń w celu nasłuchiwania
- prędkość zdarzeń formantów odbiornika i pobierania próbek
- zdarzenia są wysyłane z ładunku, które dostarcza kontekst pełne, aby można było uzyskać dostęp i zmodyfikować obiekt komunikatu podczas zdarzenia

Zapoznaj się z [Podręcznik użytkownika DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) przed kontynuowaniem implementacji.

Umożliwia tworzenie odbiornika zdarzeń usługi Service Bus w aplikacji platformy ASP.NET Core, który zapisuje dzienniki z Microsoft.Extension.Logger.
Używa [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) biblioteki do subskrybowania DiagnosticSource (także są łatwe do subskrybowania DiagnosticSource bez niego)

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

W tym przykładzie odbiornika rejestruje czas trwania, wynik Unikatowy identyfikator i czas rozpoczęcia dla każdej operacji usługi Service Bus.

#### <a name="events"></a>Zdarzenia

Dla każdej operacji dwa zdarzenia są wysyłane: "Start" i "Stop". Prawdopodobnie interesuje Cię tylko zdarzenia "Stop". One Podaj wynik operacji, a także początek oraz czas trwania jako właściwości działania.

Ładunek zdarzenia zawiera odbiornik z kontekstem operacji, replikuje dane przychodzące parametrów interfejsu API i zwrócić wartość. "Stop" ładunek zdarzenia ma wszystkie właściwości ładunku zdarzenia "Start", dlatego całkowicie zignorować zdarzeń "Start".

Wszystkie zdarzenia również mieć właściwości "Entity" i "Punktu końcowego", są one pominięte w poniższej tabeli
  * `string Entity`— Nazwa obiektu (kolejki, tematu, itp.)
  * `Uri Endpoint`-Adres URL punktu końcowego Service Bus

Każde zdarzenie "Stop" ma `Status` właściwości o `TaskStatus` operacja asynchroniczna została ukończona, który jest również zostały pominięte w poniższej tabeli, dla uproszczenia.

Poniżej przedstawiono pełną listę operacji instrumentowanych:

| Nazwa operacji | Śledzonych interfejsu API | Właściwości określonym ładunku|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | IList<Message> komunikaty — lista komunikatów są wysyłane |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | Wiadomość - przetwarzanego komunikatu<br/>DateTimeOffset ScheduleEnqueueTimeUtc - przesunięcie zaplanowane wiadomości<br/>długie SequenceNumber — numer sekwencyjny komunikatu zaplanowane ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | długie SequenceNumber — numer sekwencyjny komunikatu te do anulowania | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) |int RequestedMessageCount — maksymalna liczba wiadomości, które mogą być odebrane.<br/>IList<Message> wiadomości - listy odebranej wiadomości ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | int FromSequenceNumber — punkt początkowy z którego ma zostać Przeglądaj partia komunikatów.<br/>int RequestedMessageCount — liczba komunikatów do pobrania.<br/>IList<Message> komunikaty — lista odebrane wiadomości ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | Interfejs IEnumerable<long> SequenceNumbers — lista zawierająca numerów sekwencji. do odbierania.<br/>IList<Message> komunikaty — lista odebrane wiadomości ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | IList<string> LockTokens — lista zawierająca tokenów blokady odpowiednich komunikatów, aby zakończyć.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | ciąg LockToken - token blokady odpowiedni komunikat został porzucony. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | ciąg LockToken - token blokady odpowiedniego komunikatu, które mają być odroczone. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | ciąg LockToken - token blokady odpowiednie wiadomości utraconych wiadomości. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | ciąg LockToken - token blokady odpowiednia wiadomość odnowiona blokady.<br/>Data i godzina LockedUntilUtc — nowy blokady tokenu datę i godzinę wygaśnięcia w formacie UTC. ("Stop" ładunek zdarzenia)|
| Microsoft.Azure.ServiceBus.Process | Funkcja lambda obsługi wiadomości w [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | Wiadomość - przetwarzanego komunikatu. |
| Microsoft.Azure.ServiceBus.ProcessSession | Funkcja lambda sesji programu obsługi wiadomości w [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | Wiadomość - przetwarzanego komunikatu.<br/>Sesja IMessageSession - przetwarzanych sesji |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | Reguła RuleDescription — opis reguły, który zawiera reguły, aby dodać. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | ciąg RuleName — Nazwa reguły do usunięcia. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | Interfejs IEnumerable<RuleDescription> reguły wszystkie reguły skojarzone z subskrypcją. (Tylko "Stop" ładunku) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | ciąg SessionId - sessionId w wiadomości. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | ciąg SessionId - sessionId w wiadomości.<br/>byte [] Stan — Stan sesji ("Stop" ładunek zdarzenia) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | ciąg SessionId - sessionId w wiadomości.<br/>byte [] Stan — Stan sesji |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | ciąg SessionId - sessionId w wiadomości. |
| Microsoft.Azure.ServiceBus.Exception | wszelkie instrumentowanych interfejsu API| Wyjątek wyjątek - wystąpienie wyjątku |

W każdym przypadku można uzyskać dostępu do `Activity.Current` przechowuje bieżącego kontekstu operacji.

#### <a name="logging-additional-properties"></a>Dodatkowe właściwości rejestrowania

`Activty.Current`zawiera szczegółowe kontekście bieżącej operacji i jego obiektów nadrzędnych. Aby uzyskać więcej informacji, zobacz [dokumentacji działania](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) więcej szczegółów.
Instrumentacja usługi Service Bus udostępnia dodatkowe informacje w `Activity.Current.Tags` -posiadają `MessageId` i `SessionId` zawsze, gdy są one dostępne.

Działania, które śledzą "Odbierania", "Peek" i "ReceiveDeferred" zdarzenia również może mieć `RelatedTo` tagu. Przechowuje listę różnych `Diagnostic-Id`(s) wiadomości, które zostały uzyskane w wyniku.
Takie działanie może spowodować niepowiązanych komunikaty do odebrania. Ponadto `Diagnostic-Id` nie jest znany rozpoczęcia operacji, więc operacji "Receive" może zostać skorelowane z operacji "Proces" za pomocą tylko ten Tag. Jest przydatne podczas analizowania problemów z wydajnością można sprawdzić, jak długo trwało komunikatu.

Wydajny sposób logowania tagów jest iteracja, więc Dodawanie tagów do poprzedniego przykładu wygląda następująco: 

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

W niektórych przypadkach pożądane jest logowania tylko część zdarzenia, które należy zmniejszyć zużycie wydajności obciążenie lub magazynu. Logowanie powiodło "Stop" zdarzeń tylko (co w poprzednim przykładzie) lub procent przykładowe zdarzenia. 
`DiagnosticSource`umożliwiają uzyskanie go przy użyciu `IsEnabled` predykatu. Aby uzyskać więcej informacji, zobacz [filtrowanie na podstawie kontekstu w DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled`może zostać wywołana wiele razy dla jednej operacji zminimalizować wpływ na wydajność.

`IsEnabled`jest wywoływana w następującej kolejności:

1. `IsEnabled(<OperationName>, string entity, null)`na przykład `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Należy pamiętać, że nie istnieje "Start" lub "Stop" na końcu. Użyj, aby odfiltrować określonych operacji lub kolejek. Jeśli wywołanie zwrotne zwraca `false`, zdarzenia dla tej operacji nie są wysyłane.

  * Dla operacji "Proces" i "Encjami ProcessSession" pojawia się także `IsEnabled(<OperationName>, string entity, Activity activity)` wywołania zwrotnego. Umożliwia filtrowanie na podstawie zdarzeń `activity.Id` lub tagów właściwości.
  
2. `IsEnabled(<OperationName>.Start)`na przykład `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Sprawdza, czy zdarzenie "Start" powinny być uruchamiane. Wynik ma wpływ tylko na zdarzenie "Start", ale dalsze Instrumentacja nie zależą od niej.

Brak nie `IsEnabled` dla zdarzenia "Stop".

Jeśli niektóre wynik operacji jest wyjątek, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` jest wywoływana. Można tylko subskrybowanie zdarzeń "Wyjątek" i zapobiec rest instrumentacji. W takim przypadku nadal należy obsługiwać takie wyjątki. Ponieważ innych Instrumentacji jest wyłączony, nie spodziewać kontekst śledzenia przepływu komunikatów z klienta do producenta.

Można użyć `IsEnabled` również wdrożenie strategii próbkowania. Na podstawie próbkowania `Activity.Id` lub `Activity.RootId` zapewnia spójne próbkowania we wszystkich testowany (o ile jest on propagowane systemu śledzenia lub własnego kodu).

W obecności wielu `DiagnosticSource` odbiorników dla tego samego źródła, wystarczy, aby tylko jeden odbiornik do akceptowania zdarzeń, więc `IsEnabled` nie musi zostać wywołana,

## <a name="next-steps"></a>Kolejne kroki

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Application Insights korelacji](../application-insights/application-insights-correlation.md)
* [Application Insights monitora zależności](../application-insights/app-insights-asp-net-dependencies.md) aby zobaczyć, jeśli REST, SQL i innych zasobów zewnętrznych są spowolnieniem użytkownik.
* [Śledzenie działań niestandardowych z zestawu SDK .NET usługi Application Insights](../application-insights/application-insights-custom-operations-tracking.md)
