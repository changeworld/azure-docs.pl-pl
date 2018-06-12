---
title: Strumień danych diagnostycznych Azure Event Hubs
description: Konfigurowanie diagnostyki Azure za pomocą usługi Event Hubs kompleksowe, w tym wskazówki dotyczące typowych scenariuszy.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 98e788d87b0ce03eece35868391aadd5233217b0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267717"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Strumieniowe przesyłanie danych diagnostycznych platformy Azure w ścieżce aktywnej za pomocą usługi Event Hubs
Diagnostyka Azure oferuje elastyczne metod zbierać metryki i dzienniki z maszyn wirtualnych usługi w chmurze (VM) i przenieść wyniki do magazynu Azure. Uruchamianie w ramach czasowych marca 2016 (zestaw SDK 2.9), można wysyłanie danych diagnostycznych do źródeł danych niestandardowych i transferu danych ścieżkę aktywną w ciągu sekund za pomocą [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Obsługiwane typy danych obejmują:

* Zdarzenia funkcji Śledzenie zdarzeń systemu Windows (ETW)
* Liczniki wydajności
* Dzienniki zdarzeń systemu Windows
* Dzienniki aplikacji
* Dzienniki infrastruktury diagnostyki Azure

W tym artykule przedstawiono sposób konfigurowania diagnostyki Azure z usługą Event Hubs od końca do końca. Także wskazówki dotyczące następujących scenariuszy:

* Dostosowywanie dzienniki i metryki, która jest wysyłana do usługi Event Hubs
* Jak zmienić konfiguracje w każdym środowisku.
* Jak wyświetlać dane strumienia centra zdarzeń
* Jak rozwiązywać problemy z połączenia  

## <a name="prerequisites"></a>Wymagania wstępne
Dane receieving centra zdarzeń z diagnostyki Azure jest obsługiwana w usługi w chmurze, maszyn wirtualnych, zestawy skalowania maszyny wirtualnej i sieci szkieletowej usług, począwszy od programu Azure SDK 2.9 i odpowiednie narzędzia Azure dla programu Visual Studio.

* Rozszerzenie diagnostyki Azure 1.6 ([zestawu Azure SDK dla programu .NET 2.9 lub nowszego](https://azure.microsoft.com/downloads/) dotyczy to domyślnie)
* [Visual Studio 2013 lub nowszy](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Istniejące konfiguracje diagnostyki Azure w aplikacji przy użyciu *.wadcfgx* plików i jedną z następujących metod:
  * Visual Studio: [Konfigurowanie diagnostyki dla usług w chmurze Azure i maszyny wirtualne](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Program Windows PowerShell: [Włącz diagnostykę w usług Azure Cloud Services przy użyciu programu PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
* Udostępniane na artykuł, centra zdarzeń w przestrzeni nazw [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Połącz diagnostyki Azure do ujścia centra zdarzeń
Domyślnie diagnostyki Azure zawsze wysyła dzienniki i metryk do konta usługi Azure Storage. Aplikacja może również wysyłać dane do usługi Event Hubs, dodając nową **wychwytywanie** w obszarze **PublicConfig** / **WadCfg** elementu *.wadcfgx* pliku. W programie Visual Studio *.wadcfgx* plik znajduje się w następującej ścieżce: **projekt usługi w chmurze** > **ról** > **(RoleName)** > **diagnostics.wadcfgx** pliku.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

W tym przykładzie adres URL Centrum zdarzeń jest równa nazw FQDN Centrum zdarzeń: przestrzeń nazw usługi Event Hubs + "/" + Nazwa Centrum zdarzeń.  

Adres URL jest wyświetlany w Centrum zdarzeń [portalu Azure](http://go.microsoft.com/fwlink/?LinkID=213885) na pulpicie nawigacyjnym usługi Event Hubs.  

**Sink** może mieć ustawionej nazwy dowolny prawidłowy ciąg tak długo, jak taką samą wartość jest używana przez całą pliku konfiguracji.

> [!NOTE]
> Mogą istnieć dodatkowe wychwytywanie, takich jak *applicationInsights* skonfigurowane w tej sekcji. Diagnostyka Azure umożliwia wychwytywanie co najmniej jeden do zdefiniowania, jeśli każdy obiekt sink jest również zadeklarowany w **PrivateConfig** sekcji.  
>
>

Zbiornik usługi Event Hubs również musi być zadeklarowana i zdefiniowane w **PrivateConfig** sekcji *.wadcfgx* pliku konfiguracji.

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

`SharedAccessKeyName` Wartości muszą być zgodne, klucz dostępu sygnatury dostępu Współdzielonego i zasad, który został zdefiniowany w **usługi Event Hubs** przestrzeni nazw. Przejdź do pulpitu nawigacyjnego usługi Event Hubs w [portalu Azure](https://portal.azure.com), kliknij przycisk **Konfiguruj** , a następnie skonfigurować zasadę nazwanych (na przykład "SendRule"), która ma *wysyłania* uprawnienia. **StorageAccount** jest również zadeklarowany w **PrivateConfig**. Nie istnieje potrzeba umożliwia zmianę wartości w tym miejscu pracy. W tym przykładzie firma Microsoft może pozostać wartości puste, która jest znak, że zasób podrzędne spowoduje ustawienie wartości. Na przykład *ServiceConfiguration.Cloud.cscfg* pliku konfiguracji środowiska ustawia odpowiednie środowisko nazwy i kluczy.  

> [!WARNING]
> Klucz sygnatury dostępu Współdzielonego Event Hubs jest przechowywany w postaci zwykłego tekstu w *.wadcfgx* pliku. Często ten klucz jest zaewidencjonowany do kontroli kodu źródłowego lub jest dostępna jako zasób w serwerze kompilacji, więc należy je chronić, zależnie od potrzeb. Zalecane jest użycie klucza sygnatury dostępu Współdzielonego tutaj z *wysłać tylko* uprawnienia tak, aby złośliwy użytkownik może zapisać do Centrum zdarzeń, ale nie nasłuchiwania do niego lub możesz nim zarządzać.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Skonfiguruj diagnostyki Azure, aby wysłać dzienniki i metryk do usługi Event Hubs
Zgodnie z opisem wcześniej, wszystkie domyślne i dane diagnostyki niestandardowej, oznacza to, metryki i dzienników, jest automatycznie przesyłany do usługi Azure Storage w skonfigurowanych interwałów. Centra zdarzeń i wszelkie dodatkowe zbiornika można określić dowolnego węzła głównego lub liścia w hierarchii, które mają być wysyłane do Centrum zdarzeń. W tym zdarzenia ETW, liczniki wydajności, dzienniki zdarzeń systemu Windows i dzienników aplikacji.   

Należy wziąć pod uwagę liczbę punktów danych faktycznie mają zostać przeniesione do usługi Event Hubs. Zwykle deweloperzy transferu danych hot ścieżki małe opóźnienia, który musi być używane i szybko interpretowane. Przykłady są systemy monitorowania, alertów lub reguły automatycznego skalowania. Deweloper może również skonfigurować Magazyn alternatywnego analizy lub Wyszukaj magazynu — na przykład usługi Azure Stream Analytics, Elasticsearch, system monitorowania niestandardowe lub Ulubione system monitorowania od innych użytkowników.

Poniżej przedstawiono niektóre przykładowe konfiguracje.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

W powyższym przykładzie sink są stosowane do nadrzędnego **liczniki wydajności** węzeł w hierarchii, co oznacza, że wszystkie podrzędne **liczniki wydajności** będą wysyłane do usługi Event Hubs.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

W poprzednim przykładzie obiekt sink zostanie zastosowana tylko trzy liczniki: **żądania w kolejce**, **odrzucenia żądania**, i **% czasu procesora**.  

W poniższym przykładzie pokazano, jak deweloper może ograniczyć ilość danych wysłanych krytyczne metryki, które są używane dla tej usługi kondycji.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

W tym przykładzie obiekt sink jest stosowany do dzienników i jest filtrowana tylko dla błędów poziomu śledzenia.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Wdrażanie i aktualizacji aplikacji i informacji diagnostycznych konfiguracji usługi w chmurze
Program Visual Studio udostępnia najłatwiejszą do wdrażania aplikacji i usługi Event Hubs zbiornika konfiguracji. Aby wyświetlić i edytować plik, otwórz *.wadcfgx* plików w programie Visual Studio, go edytować i zapisać go. Ścieżka jest **projekt usługi w chmurze** > **ról** > **(RoleName)** > **diagnostics.wadcfgx**.  

W tym momencie wszystkie wdrożenia i wdrożenia aktualizacji w Visual Studio, Visual Studio Team System i wszystkie polecenia lub skryptów, które są oparte na MSBuild i użyj akcje **/t: publikowanie** docelowy obejmują *.wadcfgx* w procesie tworzenia pakietów. Ponadto wdrożenia i aktualizacje wdrażanie pliku na platformie Azure przy użyciu odpowiedniego rozszerzenia agenta diagnostyki Azure na maszyny wirtualne.

Po wdrożeniu aplikacji i konfiguracji diagnostyki Azure, będzie wyświetlany natychmiast działania na pulpicie nawigacyjnym Centrum zdarzeń. Oznacza to, że możesz przejść do wyświetlania danych hot ścieżki w narzędziu klienta lub analizy odbiornika wybranych przez użytkownika.  

Na poniższej ilustracji na pulpicie nawigacyjnym usługi Event Hubs zawiera dobrej kondycji wysyłanie danych diagnostycznych do Centrum zdarzeń uruchamianie pewnego czasu po 23: 00. Gdy to aplikacja została wdrożona z zaktualizowaną *.wadcfgx* pliku, a obiekt sink zostało skonfigurowane prawidłowo.

![][0]  

> [!NOTE]
> Po wprowadzeniu aktualizacji do pliku konfiguracji diagnostyki Azure (.wadcfgx), zaleca się wypychania aktualizacji do całej aplikacji, a także konfiguracji za pomocą programu Visual Studio publikowania lub skrypt programu Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Dane widoku dynamicznego ścieżki
Jak wcześniej wspomniano, istnieje wiele zastosowań do nasłuchiwania i przetwarzania danych usługi Event Hubs.

Jest jednym z podejść proste do tworzenia aplikacji konsoli teście małych do nasłuchiwania Centrum zdarzeń i drukowanie w strumieniu wyjściowym. Możesz umieścić następujący kod, który jest co omówiono bardziej szczegółowo w [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)), w aplikacji konsoli.  

Należy pamiętać, że aplikacja konsoli musi zawierać [pakietu NuGet hosta procesora zdarzeń](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Pamiętaj, aby zastąpić wartości w nawiasy w **Main** funkcji z wartościami dla zasobów.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>Rozwiązywanie problemów z wychwytywanie centra zdarzeń
* Centrum zdarzeń nie są wyświetlane zdarzenia przychodzącego lub wychodzącego działania zgodnie z oczekiwaniami.

    Sprawdź, czy Centrum zdarzeń jest pomyślnie zainicjowano obsługę administracyjną. Wszystkie informacje o połączeniu w **PrivateConfig** sekcji *.wadcfgx* musi odpowiadać wartości zasobu, jak pokazano w portalu. Upewnij się, że masz SAS zasady zdefiniowane ("SendRule" w przykładzie) w portalu, który *wysyłania* uprawnienia.  
* Po zaktualizowaniu Centrum zdarzeń nie jest już wyświetlana działania zdarzenia przychodzącego lub wychodzącego.

    Najpierw upewnij się, czy informacje o Centrum i konfiguracji zdarzeń jest poprawna, jak opisano wcześniej. Czasami **PrivateConfig** jest resetowany w aktualizacji wdrożenia. Zalecane rozwiązanie polega na wszystkie zmiany do *.wadcfgx* w projekcie, a następnie wypychania aktualizacji kompletna aplikacja. Jeśli nie jest to możliwe, upewnij się, że aktualizacji diagnostyki wypchnięcia pełnego **PrivateConfig** zawierającej klucz sygnatury dostępu Współdzielonego.  
* Próbuję sugestie i Centrum zdarzeń nadal nie działa.

    Należy przejrzeć tabeli magazynu Azure, która zawiera dzienniki i błędy diagnostyki Azure, sama: **WADDiagnosticInfrastructureLogsTable**. Jedną z opcji się za pomocą narzędzia, takie jak [Eksploratora usługi Storage Azure](http://www.storageexplorer.com) nawiązać tego konta magazynu, wyświetlić tej tabeli i Dodaj zapytanie dla sygnatury czasowej w ostatnich 24 godzin. Można użyć narzędzia, aby wyeksportować plik CSV i otwórz go w aplikacji, takich jak program Microsoft Excel. Excel ułatwia wyszukiwanie ciągów karty telefonicznej, takich jak **EventHubs**, aby zobaczyć, jakie błąd jest zgłaszany.  

## <a name="next-steps"></a>Kolejne kroki
• [Dowiedz się więcej o usłudze Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Dodatek: Ukończyć przykład pliku (.wadcfgx) konfiguracji diagnostyki Azure
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Uzupełniające *ServiceConfiguration.Cloud.cscfg* dla tego przykładu wygląda podobnie do następującej.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

Odpowiednik Json na podstawie ustawień maszyny wirtualnej jest następujący:
```JSON
"settings": {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}


"protectedSettings": {
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](../event-hubs/event-hubs-create.md)
* [Event Hubs — często zadawane pytania](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
