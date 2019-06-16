---
title: Usługa Azure Service Fabric zdarzenie agregacji przy użyciu struktury EventFlow | Dokumentacja firmy Microsoft
description: Więcej informacji na temat agregowania i zbierania zdarzeń przy użyciu struktury EventFlow monitorowania i diagnostyki klastrów usługi Azure Service Fabric.
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
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: bdc6c9476529b986f425d56544fd4b1afd8a864e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393240"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Zdarzenie agregacji i zbieranie przy użyciu struktury EventFlow

[Microsoft Diagnostics użyciu struktury EventFlow](https://github.com/Azure/diagnostics-eventflow) może kierować zdarzenia z węzła do co najmniej jeden monitorowania miejsc docelowych. Ponieważ jest dołączony jako pakiet NuGet w projekcie usługi, użyciu struktury EventFlow kodu i konfiguracji są przesyłane przy użyciu usługi, eliminując wspomniano wcześniej, dotyczące usługi Azure Diagnostics problem z konfiguracją na węzeł. Użyciu struktury EventFlow jest uruchamiany w ramach procesu usługi, a następnie łączy się bezpośrednio ze skonfigurowanym danych wyjściowych. Ze względu na bezpośrednie połączenie użyciu struktury EventFlow działa w przypadku platformy Azure, kontenera i lokalnych wdrożeń usługi. Należy zachować ostrożność, jeśli uruchomisz użyciu struktury EventFlow w zaawansowanych scenariuszach, takich jak w kontenerze, ponieważ poszczególne potoku użyciu struktury EventFlow sprawia, że połączenia zewnętrznego. Dlatego w przypadku obsługi wielu procesów, uzyskasz kilka połączeń wychodzących! Jest tak dużej ilości problemem w przypadku aplikacji usługi Service Fabric, ponieważ wszystkie repliki `ServiceType` działają w ten sam proces i ogranicza liczbę połączeń wychodzących. Użyciu struktury EventFlow oferuje także filtrowanie zdarzeń, tak aby były wysyłane tylko zdarzenia, które pasują do określonego filtru.

## <a name="set-up-eventflow"></a>Konfigurowanie użyciu struktury EventFlow

Użyciu struktury EventFlow pliki binarne są dostępne jako zestaw pakietów NuGet. Aby dodać użyciu struktury EventFlow projekt usługi Service Fabric, kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję "Manage NuGet packages". Przejdź do karty "Przeglądaj" i wyszukaj frazę "`Diagnostics.EventFlow`":

![Pakiety NuGet użyciu struktury EventFlow w interfejs użytkownika Menedżera pakietów NuGet usługi Visual Studio](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Zostanie wyświetlona lista pakietów różnych wyświetlane, etykietą "Inputs" i "Generuje". Użyciu struktury EventFlow obsługuje różne analizatory i rejestrowania różnych dostawców. Usługa hostingu użyciu struktury EventFlow powinna zawierać odpowiednie pakiety, w zależności od źródła i miejsca docelowego dzienników aplikacji. Oprócz pakiet ServiceFabric podstawowego potrzebujesz co najmniej jedne dane wejściowe i skonfigurować dane wyjściowe. Na przykład można dodać następujących pakietów do wysyłania zdarzeń źródła zdarzeń do usługi Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` do takich jak przechwytywanie danych z klasy źródła zdarzeń usługi i standardowych EventSources *usług firmy Microsoft — ServiceFabric* i *aktorów-Microsoft-ServiceFabric*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (użyjemy wysyłania dzienników do zasobu usługi Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(umożliwia inicjowanie potoku użyciu struktury EventFlow z konfiguracji usługi Service Fabric i zgłasza wszelkie problemy z wysyłaniem danych diagnostycznych jako raportów o kondycji usługi Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` pakiet wymaga usługi projekt przeznaczony dla .NET Framework 4.6 lub nowszej. Upewnij się, że w ramach odpowiedniego obiektu docelowego jest ustawiona we właściwościach projektu przed instalacją tego pakietu.

Po zainstalowaniu wszystkich pakietów, następnym krokiem jest, aby skonfigurować i włączyć użyciu struktury EventFlow w usłudze.

## <a name="configure-and-enable-log-collection"></a>Skonfiguruj i Włącz zbieranie danych dziennika
Potok użyciu struktury EventFlow odpowiedzialny za wysyłanie dzienników jest tworzony na podstawie specyfikacji, przechowywane w pliku konfiguracji. `Microsoft.Diagnostics.EventFlow.ServiceFabric` Pakiet instaluje początkowy plik konfiguracji użyciu struktury EventFlow w obszarze `PackageRoot\Config` folder rozwiązania o nazwie `eventFlowConfig.json`. Ten plik konfiguracji musi zostać zmodyfikowane w celu przechwycenia danych z domyślnej usługi `EventSource` klasy i inne dane wejściowe chcesz skonfigurować, a następnie wysyłać dane do odpowiedniego miejsca.

>[!NOTE]
>Jeśli plik projektu zawiera formatu programu Visual Studio 2017 `eventFlowConfig.json` pliku nie zostaną automatycznie dodane. Aby rozwiązać problem, to utworzenie pliku w `Config` folder i Ustaw akcję kompilacji na `Copy if newer`. 

Poniżej znajduje się przykładowy *eventFlowConfig.json* oparte na pakiety NuGet wymienione powyżej:
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

Nazwa ServiceEventSource usługi to wartością właściwości Name obiektu `EventSourceAttribute` stosowane do klasy ServiceEventSource. Jest ono wszystkie określone w `ServiceEventSource.cs` pliku, który jest częścią kodu usługi. Na przykład w poniższym fragmencie kodu nazwa ServiceEventSource jest *Moja firma-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Należy pamiętać, że `eventFlowConfig.json` plik jest częścią pakietu konfiguracji usługi. Zmiany w tym pliku mogą objęte uaktualnienia pełnej — lub tylko do konfiguracji usługi, z zastrzeżeniem mechanizmy kontroli kondycji uaktualnienia usługi Service Fabric i automatycznego wycofania w przypadku niepowodzenia uaktualnienia. Aby uzyskać więcej informacji, zobacz [uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md).

*Filtry* sekcja konfiguracji umożliwia dalsze dostosowywanie informacji, która ma wejść przechodzą przez potok użyciu struktury EventFlow do danych wyjściowych, umożliwiając porzucić lub zawierać pewne informacje lub zmienić strukturę Dane zdarzenia. Aby uzyskać więcej informacji na temat filtrowania, zobacz [filtry użyciu struktury EventFlow](https://github.com/Azure/diagnostics-eventflow#filters).

Ostatnim krokiem jest utworzenie wystąpienia potoku użyciu struktury EventFlow w kodzie uruchamiania usługi na terenie `Program.cs` pliku:

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

Nazwa przekazana jako parametr `CreatePipeline` metody `ServiceFabricDiagnosticsPipelineFactory` nazywa się *kondycji jednostki* reprezentujący użyciu struktury EventFlow potoku zbierania dzienników. Ta nazwa jest używana, jeśli użyciu struktury EventFlow napotka błąd i zgłasza go za pomocą podsystemu kondycji usługi Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Korzystanie z usługi Service Fabric, ustawienia i parametry aplikacji w eventFlowConfig

Użyciu struktury EventFlow obsługuje przy użyciu usługi Service Fabric, ustawienia i parametry aplikacji, aby skonfigurować ustawienia użyciu struktury EventFlow. Mogą odwoływać się do parametrów ustawień usługi Service Fabric przy użyciu tej składni specjalnych wartości:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` to nazwa sekcji konfiguracji usługi Service Fabric i `<setting-name>` jest ustawienie konfiguracji, zapewniającego wartość, która będzie służyć do konfigurowania ustawień użyciu struktury EventFlow. Aby przeczytać więcej na temat sposobu, w tym celu przejdź do [pomocy technicznej dla usługi Service Fabric, ustawienia i parametry aplikacji](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Weryfikacja

Uruchom usługę i sprawdź debugowania okno danych wyjściowych w programie Visual Studio. Po uruchomieniu usługi, możesz rozpocząć oglądanie dowody, że usługa wysyła rekordów danych wyjściowych, który został skonfigurowany. Przejdź do zdarzenia platformy analiz i wizualizacji, a następnie upewnij się, dzienniki popularność zaczęła Pokaż w górę (może to potrwać kilka minut).

## <a name="next-steps"></a>Kolejne kroki

* [Analiza zdarzeń i wizualizacji przy użyciu usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacji przy użyciu dzienników usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentacja użyciu struktury EventFlow](https://github.com/Azure/diagnostics-eventflow)
