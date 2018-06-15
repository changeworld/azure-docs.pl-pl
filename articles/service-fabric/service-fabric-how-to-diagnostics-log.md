---
title: Generuj zdarzenia z dziennika z aplikacji sieci szkieletowej usług .NET w Azure lub w klastrze autonomiczny
description: Dowiedz się więcej o sposobie dodawania logowania do aplikacji sieci szkieletowej usług .NET hostowanych w klastrze platformy Azure lub w klastrze autonomicznych.
services: service-fabric
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: adegeo
ms.openlocfilehash: ed9aaf67b4f6749ea6d505a51fbc76e3d1cf0870
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204876"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Dodaj logowanie do aplikacji sieci szkieletowej usług

Aplikacja musi zawierać informacje wystarczające do całkowitego debugowania, jeśli wystąpią problemy. Rejestrowanie jest jednym z najważniejszych czynności, które można dodać do aplikacji sieci szkieletowej usług. Gdy wystąpi błąd, dobrym rejestrowania pozwala sposobem zbadaj błędy. Analizując wzorce dziennika, może się okazać sposobów poprawy wydajności lub struktury aplikacji. W tym dokumencie przedstawiono kilka opcji rejestrowania różnych.

## <a name="eventflow"></a>EventFlow

[Biblioteki EventFlow](https://github.com/Azure/diagnostics-eventflow) suite umożliwia aplikacji określenie, jakie dane diagnostyczne do zbierania i którym powinny one wyjściowych. Dane diagnostyczne można niczego z liczników wydajności do śladów aplikacji. Uruchomieniu na platformie te same czynności co aplikacji, więc obciążenie komunikacji jest zminimalizowany. Aby uzyskać więcej informacji na temat EventFlow i sieci szkieletowej usług, zobacz [Azure Usługa sieci szkieletowej zdarzeń agregacji o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>W przypadku używania strukturalnych zdarzeń źródła zdarzeń

Definiowanie komunikat zdarzenia według przypadków użycia umożliwia pakietu danych dotyczących zdarzeń, w kontekście zdarzenia. Można łatwiej wyszukiwać i filtr na podstawie nazw lub wartości właściwości określonego zdarzenia. Struktura sprawia, że dane wyjściowe Instrumentacji czytelności, ale wymaga więcej myśl i czasu, aby zdefiniować zdarzenie dla każdego przypadku użycia. 

Definicje zdarzeń mogą być współużytkowane przez całej aplikacji. Na przykład metoda uruchamiania lub zatrzymywania zdarzeń będzie można używać ponownie w wielu usług w aplikacji. Usługa specyficznego dla domeny, takich jak system zamówień może mieć **CreateOrder** zdarzenie, które ma własną unikatową zdarzeń. Takie podejście może wygenerować wiele zdarzeń i mogą wymagać koordynacji identyfikatorów zespołów projektu. 

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

Definiowanie określonych zdarzeń mogą być trudne, wiele osób zdefiniować kilka zdarzenia ze wspólnego zestawu parametrów, które zwykle wysyłają informacji o ich jako ciąg. Większość strukturalnych aspekt zostaną utracone i jest trudne do wyszukiwania i filtrowania wyników. W takie podejście są zdefiniowane kilka zdarzeń, które zwykle odpowiadają poziomy rejestrowania. Poniższy fragment kodu definiuje debugowania i komunikatu o błędzie:

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

Przy użyciu hybrydowego Instrumentacji strukturalnych i rodzajowy również może działać również. Strukturalne Instrumentacji jest używana do raportowania błędów i metryki. Zdarzenia ogólne może służyć do szczegółowe rejestrowanie, używanej przez inżynierów do rozwiązywania problemów.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

Rejestrowanie w programie ASP.NET Core ([pakietu Microsoft.Extensions.Logging NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) to platforma rejestrowania, która zapewnia interfejs API rejestrowania standardowych aplikacji. Obsługę innych zapleczy rejestrowania można podłączyć do platformy ASP.NET Core rejestrowania. Zapewnia szeroką gamę obsługę rejestrowania w aplikacji jest przetwarzana, bez konieczności zmiany większej ilości kodu.

1. Dodaj **Microsoft.Extensions.Logging** NuGet pakietu się do projektu chcesz dokumentu. Ponadto Dodaj wszelkie pakiety dostawcy. Aby uzyskać więcej informacji, zobacz [logowanie do platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Dodaj **przy użyciu** dyrektywy dla **Microsoft.Extensions.Logging** do pliku usługi.
3. Zdefiniuj zmienną prywatny w klasie usługi.

   ```csharp
   private ILogger _logger = null;
   ```

4. W konstruktorze klasy usługi Dodaj ten kod:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Uruchom kod w metody instrumentacji. Oto kilka przykładów:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Przy użyciu innych dostawców logowania

Niektóre użycia innych dostawców podejścia opisanego w poprzedniej sekcji, w tym [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/), i [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Możesz podłączyć każdego z nich do platformy ASP.NET Core rejestrowania lub można ich używać oddzielnie. Serilog jest funkcją, która wzbogaca wszystkich wiadomości wysłanych z rejestrator. Ta funkcja może być przydatne do wyjściowego nazwę usługi, typ i informacji o partycji. Aby używać tej funkcji w infrastrukturze podstawowej platformy ASP.NET, wykonaj następujące kroki:

1. Dodaj **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, i **Serilog.Sinks.Observable** pakietów NuGet do projektu. 
2. Utwórz `LoggerConfiguration` i wystąpienia rejestratora.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Dodaj `Serilog.ILogger` argument do konstruktora usługi i Przekaż nowo utworzony rejestratora.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. W Konstruktorze usługi tworzy enrichers właściwości dla **ServiceTypeName**, **ServiceName**, **PartitionId**, i **InstanceId** .

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

5. Instrumentacja kod na takie same, jak przy użyciu platformy ASP.NET Core bez Serilog.

   >[!NOTE]
   >Zalecamy, aby użytkownik *nie* Użyj statycznych `Log.Logger` z poprzedniego przykładu. Sieć szkieletowa usług może obsługiwać wiele wystąpień tego samego typu usługi w ramach jednego procesu. Jeśli używasz statycznych `Log.Logger`, twórcę ostatniego elementu enrichers właściwości wyświetli wartości dla wszystkich wystąpień, które są uruchomione. Jest jednym z powodów dlaczego zmiennej _logger jest zmienną prywatnego elementu członkowskiego klasy usługi. Ponadto należy `_logger` dostępne dla wspólnego kod, który może być używane w usługach.

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej informacji na temat [aplikacji monitorowania w sieci szkieletowej usług](service-fabric-diagnostics-event-generation-app.md).
- Przeczytaj informacje o rejestracji z [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) i [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










