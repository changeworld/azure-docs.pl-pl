---
title: Generowanie zdarzeń dziennika z aplikacji .NET
description: Dowiedz się, jak dodać rejestrowanie do aplikacji sieci szkieletowej usług .NET hostowanych w klastrze platformy Azure lub w klastrze autonomicznym.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: 8c4721584e74bd7f7111c516f2d16bd190392bb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614370"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Add logging to your Service Fabric application (Dodawanie rejestrowania do aplikacji usługi Service Fabric)

Aplikacja musi dostarczyć wystarczających informacji, aby forensically debugować go, gdy pojawią się problemy. Rejestrowanie jest jedną z najważniejszych rzeczy, które można dodać do aplikacji sieci szkieletowej usług. W przypadku wystąpienia błędu dobre rejestrowanie może dać sposób na zbadanie błędów. Analizując wzorce dziennika, można znaleźć sposoby poprawy wydajności lub projektowania aplikacji. W tym dokumencie przedstawiono kilka różnych opcji rejestrowania.

## <a name="eventflow"></a>Przepływ zdarzeń

Pakiet [biblioteki EventFlow](https://github.com/Azure/diagnostics-eventflow) umożliwia aplikacjom definiowanie, jakie dane diagnostyczne mają być zbierane i do których powinny być wyprowadzane. Dane diagnostyczne mogą być wszystko od liczników wydajności do śledzenia aplikacji. Działa w tym samym procesie co aplikacja, więc obciążenie komunikacji jest zminimalizowane. Aby uzyskać więcej informacji na temat usługi EventFlow i sieci szkieletowej usług, zobacz [Agregacja zdarzeń sieci szkieletowej usługi Azure za pomocą usługi EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Korzystanie ze zdarzeń ustrukturyzowane zdarzenie EventSource

Definiowanie zdarzeń wiadomości według przypadku użycia umożliwia spakowanie danych o zdarzeniu w kontekście zdarzenia. Można łatwiej wyszukiwać i filtrować na podstawie nazw lub wartości określonych właściwości zdarzenia. Strukturyzacja danych wyjściowych instrumentacji ułatwia odczytywanie, ale wymaga więcej czasu na zdefiniowanie zdarzenia dla każdego przypadku użycia. 

Niektóre definicje zdarzeń mogą być współużytkowane w całej aplikacji. Na przykład zdarzenie rozpoczęcia lub zatrzymania metody będzie ponownie w wielu usługach w aplikacji. Usługa specyficzne dla domeny, takich jak system zamówień, może mieć **CreateOrder** zdarzenia, który ma własne zdarzenie unikatowe. Takie podejście może generować wiele zdarzeń i potencjalnie wymagać koordynacji identyfikatorów między zespołami projektowymi. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Ogólne korzystanie z usługi EventSource

Ponieważ definiowanie określonych zdarzeń może być trudne, wiele osób definiuje kilka zdarzeń ze wspólnym zestawem parametrów, które zazwyczaj wyprowadzają informacje jako ciąg. Wiele z aspektu strukturalnego jest tracona i trudniej jest wyszukiwać i filtrować wyniki. W tym podejściu zdefiniowano kilka zdarzeń, które zwykle odpowiadają poziomom rejestrowania. Poniższy urywek definiuje komunikat debugowania i błędu:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Korzystanie z hybrydy ustrukturyzowanych i ogólnych oprzyrządowania również może działać dobrze. Instrumentacja strukturalna służy do raportowania błędów i metryk. Zdarzenia ogólne mogą służyć do szczegółowego rejestrowania, który jest używany przez inżynierów do rozwiązywania problemów.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

Rejestrowanie ASP.NET Core[(Microsoft.Extensions.Logging NuGet package)](https://www.nuget.org/packages/Microsoft.Extensions.Logging)jest platformą rejestrowania, która zapewnia standardowy interfejs API rejestrowania dla aplikacji. Obsługa innych zaplecza rejestrowania można podłączyć do rejestrowania ASP.NET Core. Daje to szeroką gamę obsługi logowania w aplikacji jest przetwarzany, bez konieczności zmiany kodu.

1. Dodaj pakiet **Microsoft.Extensions.Logging** NuGet do projektu, który chcesz zaa instrumentować. Ponadto dodaj pakiety dostawców. Aby uzyskać więcej informacji, zobacz [Logowanie do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Dodaj **używaną** dyrektywę dla **microsoft.extensions.Logging** do pliku usługi.
3. Zdefiniuj zmienną prywatną w klasie usługi.

   ```csharp
   private ILogger _logger = null;
   ```

4. W konstruktorze klasy usługi dodaj ten kod:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Rozpocznij instrumentowanie kodu w swoich metodach. Oto kilka przykładów:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Korzystanie z innych dostawców rejestrowania

Niektórzy dostawcy zewnętrzni używają podejścia opisanego w poprzedniej sekcji, w tym [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/)i [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Każdy z nich można podłączyć do rejestrowania ASP.NET Core lub używać ich oddzielnie. Serilog ma funkcję, która wzbogaca wszystkie wiadomości wysyłane z rejestratora. Ta funkcja może być przydatna do wyprowadzania informacji o nazwie usługi, typie i partycji. Aby użyć tej funkcji w infrastrukturze ASP.NET Core, wykonaj następujące kroki:

1. Dodaj **serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**i **Serilog.Sinks.Observable** NuGet pakietów do projektu. 
2. Tworzenie `LoggerConfiguration` wystąpienia rejestratora i rejestratora.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Dodaj `Serilog.ILogger` argument do konstruktora usługi i przekaż nowo utworzony rejestrator.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. W konstruktorze usług tworzy wzbogacacze właściwości dla **ServiceTypeName**, **ServiceName**, **PartitionId**i **InstanceId**.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrument kod taki sam, jak w przypadku korzystania z ASP.NET Core bez Serilog.

   >[!NOTE]
   >Zaleca *się,* aby nie używać `Log.Logger` statyczne z poprzednim przykładem. Sieci szkieletowej usług może obsługiwać wiele wystąpień tego samego typu usługi w ramach jednego procesu. Jeśli używasz statyczne `Log.Logger`, ostatni moduł zapisujący wzbogacacze właściwości pokaże wartości dla wszystkich wystąpień, które są uruchomione. Jest to jeden z powodów, dla których zmienna _logger jest prywatną zmienną członkowną klasy usługi. Ponadto należy udostępnić `_logger` wspólny kod, który może być używany w różnych usługach.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej informacji na temat [monitorowania aplikacji w sieci szkieletowej usług](service-fabric-diagnostics-event-generation-app.md).
- Przeczytaj o rejestrowaniu za pomocą [usługi EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) i [diagnostyki platformy Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md).










