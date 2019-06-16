---
title: Generowanie dziennika zdarzeń z aplikacji .NET usługi Service Fabric na platformie Azure lub klastra autonomicznego
description: Dowiedz się więcej o tym, jak dodać logowanie do aplikacji .NET usługi Service Fabric hostowany na klastrze platformy Azure lub klastra autonomicznego.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: d1b3dc25dd9bda9d7f9d9152c2a94cea8321f5cf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482611"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Dodawanie rejestrowania aplikacji usługi Service Fabric

Aplikacja musi zawierać informacje wystarczające do całkowitego debugowania, gdy wystąpią problemy. Rejestrowanie jest jednym z najważniejszych czynników, które można dodać do aplikacji usługi Service Fabric. Gdy wystąpi błąd, dobrym rejestrowania umożliwiają sposób zbadaj błędy. Analizując wzorców dziennika, może się okazać sposobów na zwiększenie wydajności lub projektu danej aplikacji. W tym dokumencie przedstawiono kilka rejestrowania różnych opcji.

## <a name="eventflow"></a>Użyciu struktury EventFlow

[Biblioteki użyciu struktury EventFlow](https://github.com/Azure/diagnostics-eventflow) pakiet umożliwia aplikacji określenie, jakie dane diagnostyczne do zbierania i której powinny zostać zwrócone do. Dane diagnostyczne mogą być cokolwiek — od liczników wydajności do śladów aplikacji. Działa w tym samym procesie co aplikacja, dzięki czemu jest zminimalizowany obciążenie komunikatu. Aby uzyskać więcej informacji o użyciu struktury EventFlow i Service Fabric, zobacz [Azure usługi Service Fabric zdarzenie agregacji, przy użyciu struktury EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Przy użyciu structured zdarzeń EventSource

Definiowanie wiadomości zdarzenia według przypadków użycia umożliwia pakiet danych dotyczących zdarzeń, w kontekście zdarzenia. Możesz łatwiej wyszukiwać i filtrowania na podstawie nazwy lub wartości właściwości określonego zdarzenia. Tworzenie struktury sprawia, że dane wyjściowe Instrumentacji łatwiejsza do odczytania, ale wymaga więcej myślenia i czasu, aby określić zdarzenia dla każdego przypadku użycia. 

Definicje zdarzeń mogą być udostępniane w całej aplikacji. Na przykład metoda uruchamiania lub zatrzymania zdarzeń będzie można użyć ponownie w wielu usługach w ramach aplikacji. Usługi specyficzne dla domeny, takich jak system zamówień, może być **CreateOrder** zdarzenie, które ma swój własny unikatowy zdarzeń. To podejście może wygenerować wiele zdarzeń i mogą wymagać koordynacji identyfikatorów w projektach zespołowych. 

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

### <a name="using-eventsource-generically"></a>Ogólnie przy użyciu źródła zdarzeń

Definiowanie określone zdarzenia mogą być trudne, wiele osób zdefiniować kilka zdarzeń za pomocą wspólnego zestawu parametrów, które zazwyczaj udostępniają swoich informacji jako ciąg. Większość ze strukturą aspekt zostaną utracone i jest trudniejsze do wyszukiwania i filtrowania wyników. W tym podejściu zdefiniowano kilka zdarzeń, które zwykle odpowiadają poziomów rejestrowania. Poniższy fragment kodu definiuje debugowania i komunikatu o błędzie:

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

Za pomocą hybrydowego rozwiązania łączącego program instrumentacji ze strukturą i ogólny również będzie działać prawidłowo. Strukturalne Instrumentacji jest używana do raportowania błędów oraz metryki. Zdarzenia ogólne może służyć do szczegółowe rejestrowanie, który jest używany przez inżynierów do rozwiązywania problemów.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

Rejestrowanie w programie ASP.NET Core ([pakietu Microsoft.Extensions.Logging NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) to struktura rejestrowania, która udostępnia interfejs API standardowa rejestrowania dla swojej aplikacji. Obsługa innych zaplecza rejestrowania można podłączyć do platformy ASP.NET Core rejestrowania. Dzięki temu jest przetwarzana szerokiej gamy obsługę rejestrowania w aplikacji, bez konieczności zmiany większej ilości kodu.

1. Dodaj **Microsoft.Extensions.Logging** pakietu NuGet do projektu zostanie chcesz dokumentu. Ponadto należy dodać wszystkie pakiety dostawcy. Aby uzyskać więcej informacji, zobacz [rejestrowania w programie ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Dodaj **przy użyciu** dyrektywy dla **Microsoft.Extensions.Logging** do pliku usługi.
3. Zdefiniuj zmienną prywatną w klasie usługi.

   ```csharp
   private ILogger _logger = null;
   ```

4. W konstruktorze klasy usługi Dodaj następujący kod:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Uruchom kod w metody instrumentacji. Poniżej przedstawiono kilka przykładów:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Przy użyciu innych dostawców logowania

Niektórych dostawców innych firm, użyj podejścia opisanego w poprzedniej sekcji, w tym [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/), i [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Możesz podłączyć każdy z nich do platformy ASP.NET Core rejestrowania lub korzystania z nich osobno. Serilog ma funkcję, która uzupełnia wszystkie wiadomości wysyłane z rejestratora. Ta funkcja może być przydatne w danych wyjściowych, nazwę usługi, typ i informacje o partycji. Aby używać tej funkcji w ramach infrastruktury platformy ASP.NET Core, wykonaj następujące kroki:

1. Dodaj **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, i **Serilog.Sinks.Observable** pakietów NuGet do projektu. 
2. Utwórz `LoggerConfiguration` i wystąpienia rejestratora.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Dodaj `Serilog.ILogger` argument konstruktora usługi i Przekaż nowo utworzony rejestratora.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. W Konstruktorze service tworzy enrichers właściwości dla **ServiceTypeName**, **ServiceName**, **PartitionId**, i **InstanceId** .

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

5. Przygotuj Instrumentację kod taki sam, jakby były używane platformy ASP.NET Core bez Serilog.

   >[!NOTE]
   >Zaleca się, że możesz *nie* używa się statycznej `Log.Logger` w poprzednim przykładzie. Usługa Service Fabric umożliwia hostowanie wielu wystąpień typu usługi w ramach jednego procesu. Jeśli używasz statycznych `Log.Logger`, ostatni składnik zapisywania programu enrichers właściwość wyświetli wartości dla wszystkich wystąpień, które są uruchomione. To jest jednym z powodów dlaczego zmienna _logger jest zmienną prywatnego elementu członkowskiego klasy usługi. Ponadto należy `_logger` dostępne dla wspólnego kodu, który może być używane w usługach.

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej informacji na temat [aplikacji, monitorowanie w usłudze Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Przeczytaj o rejestrowanie za pomocą [użyciu struktury EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) i [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










