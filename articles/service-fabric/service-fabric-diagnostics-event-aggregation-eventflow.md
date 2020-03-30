---
title: Agregacja zdarzeń sieci szkieletowej usługi Azure z przepływem zdarzeń
description: Dowiedz się więcej o agregowaniu i zbieraniu zdarzeń przy użyciu usługi EventFlow do monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde24657cc8ed78b91e72df16d51df4077a6e030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463095"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregacja i zbieranie zdarzeń przy użyciu usługi EventFlow

[Usługa Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) może kierować zdarzenia z węzła do jednego lub kilku miejsc docelowych monitorowania. Ponieważ jest on uwzględniony jako pakiet NuGet w projekcie usługi, kod EventFlow i podróży konfiguracji z usługą, eliminując problem konfiguracji na węzeł wspomniano wcześniej o diagnostyki platformy Azure. EventFlow działa w ramach procesu usługi i łączy się bezpośrednio ze skonfigurowanych wyjść. Ze względu na bezpośrednie połączenie EventFlow działa na platformie Azure, kontenerów i lokalnych wdrożeń usługi. Należy zachować ostrożność po uruchomieniu EventFlow w scenariuszach o wysokiej gęstości, takich jak w kontenerze, ponieważ każdy potok EventFlow sprawia, że połączenie zewnętrzne. Tak więc, jeśli hostujesz kilka procesów, otrzymasz kilka połączeń wychodzących! Nie jest to tak duże zmartwienie dla aplikacji sieci `ServiceType` szkieletowej usług, ponieważ wszystkie repliki uruchamiania w tym samym procesie, a to ogranicza liczbę połączeń wychodzących. EventFlow oferuje również filtrowanie zdarzeń, dzięki czemu wysyłane są tylko zdarzenia, które pasują do określonego filtru.

## <a name="set-up-eventflow"></a>Konfigurowanie przepływu zdarzeń

Pliki binarne EventFlow są dostępne jako zestaw pakietów NuGet. Aby dodać EventFlow do projektu usługi sieci szkieletowej usług, kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz opcję "Zarządzaj pakietami NuGet". Przełącz się na zakładkę "Przeglądaj" i wyszukaj "`Diagnostics.EventFlow`":

![Pakiety EventFlow NuGet w interfejsie użytkownika menedżera pakietów Programu Visual Studio NuGet](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Pojawi się lista różnych pakietów, oznaczonych "Inputs" i "Outputs". EventFlow obsługuje różnych dostawców rejestrowania i analizatorów. Usługa hosting EventFlow powinny zawierać odpowiednie pakiety w zależności od źródła i miejsca docelowego dla dzienników aplikacji. Oprócz podstawowego pakietu ServiceFabric, potrzebujesz również co najmniej jednego skonfigurowane dane wejściowe i wyjściowe. Na przykład można dodać następujące pakiety, aby wysłać zdarzenia EventSource do usługi Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource`do przechwytywania danych z usługi EventSource klasy i standardowych EventSources takich jak *Microsoft-ServiceFabric-Services* i *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`(będziemy wysyłać dzienniki do zasobu usługi Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(umożliwia inicjowanie potoku EventFlow z konfiguracji usługi Sieci szkieletowej usług i zgłasza wszelkie problemy z wysyłaniem danych diagnostycznych jako raporty kondycji sieci szkieletowej usług)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource`pakiet wymaga projektu usługi do docelowego .NET Framework 4.6 lub nowsze. Przed zainstalowaniem tego pakietu upewnij się, że ustawiono odpowiednią platformę docelową we właściwościach projektu.

Po zainstalowaniu wszystkich pakietów następnym krokiem jest skonfigurowanie i włączenie EventFlow w usłudze.

## <a name="configure-and-enable-log-collection"></a>Konfigurowanie i włączanie zbierania dzienników
Potok EventFlow odpowiedzialny za wysyłanie dzienników jest tworzony na podstawie specyfikacji przechowywanej w pliku konfiguracyjnym. Pakiet `Microsoft.Diagnostics.EventFlow.ServiceFabric` instaluje początkowy plik konfiguracyjny EventFlow w folderze `PackageRoot\Config` rozwiązania o nazwie `eventFlowConfig.json`. Ten plik konfiguracyjny musi zostać zmodyfikowany `EventSource` w celu przechwycenia danych z domyślnej klasy usługi oraz innych danych wejściowych, które chcesz skonfigurować, i wysyłania danych do odpowiedniego miejsca.

>[!NOTE]
>Jeśli plik projektu ma format VisualStudio 2017, `eventFlowConfig.json` plik nie zostanie automatycznie dodany. Aby rozwiązać ten problem, `Config` utwórz plik w `Copy if newer`folderze i ustaw akcję kompilacji na . 

Oto przykładowe *zdarzenieFlowConfig.json* na podstawie pakietów NuGet wymienionych powyżej:
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

Nazwa service's ServiceEventSource jest wartością Name właściwości `EventSourceAttribute` stosowane do ServiceEventSource klasy. Wszystko jest określone `ServiceEventSource.cs` w pliku, który jest częścią kodu usługi. Na przykład w poniższym fragmentie kodu nazwa ServiceEventSource jest *MyCompany-Application1-Stateless1:*

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Należy `eventFlowConfig.json` zauważyć, że plik jest częścią pakietu konfiguracji usługi. Zmiany w tym pliku mogą być zawarte w pełnych lub tylko konfiguracji uaktualnień usługi, z zastrzeżeniem kontroli kondycji uaktualnienia sieci szkieletowej usług i automatyczne wycofywanie, jeśli występuje niepowodzenie uaktualnienia. Aby uzyskać więcej informacji, zobacz [Uaktualnianie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md).

Sekcja *filtrów* konfiguracji umożliwia dalsze dostosowanie informacji, które mają przejść przez potok EventFlow do wyjść, co pozwala na upuszczenie lub dołączenie pewnych informacji lub zmianę struktury danych zdarzeń. Aby uzyskać więcej informacji na temat filtrowania, zobacz [Filtry EventFlow](https://github.com/Azure/diagnostics-eventflow#filters).

Ostatnim krokiem jest wystąpienie potoku EventFlow w kodzie `Program.cs` startowym usługi, znajdującym się w pliku:

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

Nazwa przekazana jako parametr `CreatePipeline` metody `ServiceFabricDiagnosticsPipelineFactory` jest nazwą *jednostki kondycji* reprezentującej potoku kolekcji dziennika EventFlow. Ta nazwa jest używana, jeśli EventFlow napotka i błąd i zgłasza go za pośrednictwem podsystemu kondycji sieci szkieletowej usług.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Użyj ustawień sieci szkieletowej usług i parametrów aplikacji w eventFlowConfig

EventFlow obsługuje przy użyciu ustawień sieci szkieletowej usług i parametrów aplikacji do konfigurowania ustawień EventFlow. Wartości można znaleźć w parametrach ustawień sieci szkieletowej sieci szkieletowej, używając tej specjalnej składni:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>`jest nazwą sekcji konfiguracji sieci szkieletowej usług i `<setting-name>` jest ustawieniem konfiguracji zapewniającym wartość, która będzie używana do konfigurowania ustawienia EventFlow. Aby dowiedzieć się więcej o tym, jak to zrobić, przejdź do [pomocy technicznej dla ustawień sieci szkieletowej usług i parametrów aplikacji](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Weryfikacja

Uruchom usługę i obserwować okno danych wyjściowych debugowania w programie Visual Studio. Po uruchomieniu usługi należy rozpocząć wyświetlanie dowodów, że usługa wysyła rekordy do danych wyjściowych, które zostały skonfigurowane. Przejdź do platformy analizy zdarzeń i wizualizacji i upewnij się, że dzienniki zaczęły się pojawiać (może potrwać kilka minut).

## <a name="next-steps"></a>Następne kroki

* [Analiza i wizualizacja zdarzeń za pomocą usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacja za pomocą dzienników usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentacja EventFlow](https://github.com/Azure/diagnostics-eventflow)
