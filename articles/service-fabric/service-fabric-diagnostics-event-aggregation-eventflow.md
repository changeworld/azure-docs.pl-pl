---
title: Usługa Azure sieci szkieletowej zdarzeń agregacji o EventFlow | Dokumentacja firmy Microsoft
description: Więcej informacji na temat agregowania i zbierania zdarzeń przy użyciu EventFlow monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 37cae133dda2f274a83db1df37e798c5da58c539
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregacja zdarzeń i kolekcji przy użyciu EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) może kierować zdarzenia z węzła do jednego lub więcej monitorowania miejsc docelowych. Ponieważ jest uwzględniona jako pakietu NuGet w projekcie usługi EventFlow kod i konfiguracja są przesyłane z usługą, eliminując problem konfiguracji każdego węzła wymienione wcześniej o diagnostyki Azure. EventFlow jest uruchamiany w ramach procesu usługi, a następnie łączy się bezpośrednio skonfigurowanych danych wyjściowych. Ze względu na bezpośrednie połączenie EventFlow działa platformy Azure, kontenera i lokalnego wdrożenia usługi. Należy zachować ostrożność, jeśli uruchomisz EventFlow w scenariuszach wysokiej gęstości, takich jak w kontenerze, ponieważ każdy potoku EventFlow sprawia, że połączenie zewnętrzne. Tak Jeśli host kilka procesów, możesz uzyskać kilka połączeń wychodzących! Nie jest to tyle problemem w przypadku aplikacji usługi Service Fabric, ponieważ wszystkie repliki `ServiceType` działają w ten sam proces, i ogranicza liczbę połączeń wychodzących. EventFlow oferuje również filtrowanie zdarzeń, tak aby były wysyłane tylko zdarzenia, które są zgodne z filtrem określonym.

## <a name="set-up-eventflow"></a>Konfigurowanie EventFlow

Pliki binarne EventFlow są dostępne jako zestaw pakietów NuGet. Aby dodać EventFlow do projektu usługi Service Fabric, kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz polecenie "Manage NuGet packages". Przejdź do karty "Przeglądaj" i wyszukaj "`Diagnostics.EventFlow`":

![Pakiety EventFlow NuGet w interfejs użytkownika Menedżera pakietów NuGet usługi Visual Studio](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Zostanie wyświetlona lista pakietów różnych wyświetlani, etykietą "Danych wejściowych" i "Generuje". EventFlow obsługuje różne analizatory i rejestrowania różnych dostawców. Usługę hostingu EventFlow powinna zawierać odpowiednie pakiety, w zależności od źródłowego i docelowego dla dzienników aplikacji. Oprócz pakietu ServiceFabric core należy również co najmniej jedno wejście i skonfigurować dane wyjściowe. Na przykład można dodać następujących pakietów do wysyłania zdarzeń źródła zdarzeń do usługi Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` do przechwytywania danych z usługi EventSource — klasa i standardowe EventSources takich jak *usługi ServiceFabric* i *Microsoft-ServiceFabric-podmiotów*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (zamierzamy wysłać dzienniki do zasobu usługi Application Insights dla platformy Azure)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(umożliwia inicjowanie potoku EventFlow z konfiguracji usługi sieć szkieletowa usług i raportowanie jakiekolwiek problemy z wysyłania danych diagnostycznych jako raportów o kondycji sieci szkieletowej usług)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` pakiet wymaga projekt usługi pod kątem .NET Framework 4.6 lub nowszą. Upewnij się, że należy ustawić odpowiednie platformę docelową we właściwościach projektu przed instalacją tego pakietu.

Po zainstalowaniu wszystkich pakietów, następnym krokiem jest skonfigurować i włączyć EventFlow w usłudze.

## <a name="configure-and-enable-log-collection"></a>Skonfiguruj i Włącz zbierania dzienników
Potok EventFlow odpowiedzialny za wysyłanie dzienników jest tworzona na podstawie specyfikacji przechowywane w pliku konfiguracji. `Microsoft.Diagnostics.EventFlow.ServiceFabric` Pakiet instaluje plik początkowej konfiguracji EventFlow pod `PackageRoot\Config` folder rozwiązania o nazwie `eventFlowConfig.json`. Ten plik konfiguracyjny musi zostać zmodyfikowane w celu przechwycenia danych z usługi domyślne `EventSource` klasy i inne dane wejściowe chcesz skonfigurować, a następnie wysyłać dane w odpowiednie miejsce.

>[!NOTE]
>Jeśli plik projektu ma format Visual Studio 2017 `eventFlowConfig.json` pliku nie zostanie automatycznie dodany. Aby rozwiązać, to utworzyć plik w `Config` folderu i ustawić akcji kompilacji `Copy if newer`. 

Poniżej przedstawiono przykładowe *eventFlowConfig.json* oparte na pakietów NuGet wymienionych powyżej:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

Nazwa usługi ServiceEventSource jest wartość właściwości Name `EventSourceAttribute` stosowanym do klasy ServiceEventSource. Wszystkie określono w `ServiceEventSource.cs` pliku, który jest częścią kodu usługi. Na przykład w poniższy fragment kodu nazwa ServiceEventSource jest *Moja firma-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Należy pamiętać, że `eventFlowConfig.json` plik jest częścią pakietu konfiguracji usługi. Zmiany w tym pliku mogą zostać włączone w pełnej lub Konfiguracja — tylko do uaktualnienia usługi kontroli uaktualnienia kondycji sieci szkieletowej usług i automatycznego wycofania w przypadku niepowodzenia uaktualnienia. Aby uzyskać więcej informacji, zobacz [uaktualniania aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md).

*Filtry* sekcja konfiguracji umożliwia dalsze dostosować informacje, które ma przejść przez potok EventFlow do danych wyjściowych, umożliwiając drop lub uwzględnienia niektórych informacji lub zmiana struktury danych zdarzeń. Aby uzyskać więcej informacji dotyczących filtrowania, zobacz [filtry EventFlow](https://github.com/Azure/diagnostics-eventflow#filters).

Ostatnim krokiem jest utworzenie wystąpienia potoku EventFlow w kodzie uruchamiania usługi, znajduje się w `Program.cs` pliku:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Nazwa przekazywana jako parametr `CreatePipeline` metody `ServiceFabricDiagnosticsPipelineFactory` jest nazwą *jednostki kondycji* reprezentujący potoku EventFlow dziennika kolekcji. Ta nazwa jest używana, jeśli EventFlow napotkał błąd i zgłasza przez podsystem kondycji sieci szkieletowej usług.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Użyj ustawień sieci szkieletowej usług i aplikacji parametrów w eventFlowConfig

EventFlow obsługuje przy użyciu ustawień sieci szkieletowej usług oraz parametry aplikacji, aby skonfigurować ustawienia EventFlow. Może się odwoływać do parametrów ustawień sieci szkieletowej usług przy użyciu tej składni specjalne wartości:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` Nazwa sekcji konfiguracji sieci szkieletowej usług i `<setting-name>` jest ustawienie konfiguracji podawania wartości, która będzie służyć do konfigurowania ustawień EventFlow. Aby przeczytać więcej informacji na temat w tym celu przejdź do [obsługę parametry aplikacji i ustawień sieci szkieletowej usług](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Weryfikacja

Uruchomić usługę i sprawdź debugowania okno dane wyjściowe w programie Visual Studio. Po uruchomieniu usługi powinny zacząć się wyświetlać dowód, że usługa wysyła rekordów danych wyjściowych, który został skonfigurowany. Przejdź do zdarzeń platformy analizy i wizualizacji i upewnij się, że dzienniki zaczęły Pokaż up (może to potrwać kilka minut).

## <a name="next-steps"></a>Kolejne kroki

* [Analiza zdarzeń i wizualizacji z usługą Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacji z OMS](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentacja EventFlow](https://github.com/Azure/diagnostics-eventflow)
