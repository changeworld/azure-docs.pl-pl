---
title: Stream dane diagnostyczne platformy Azure do usługi Event Hubs
description: Konfigurowanie diagnostyki platformy Azure za pomocą usługi Event Hubs typu end to end, w tym wskazówki dotyczące typowych scenariuszy.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: c5fc2199de8623dd3a9f2bc5faf23c7c40d67d75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922829"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Strumieniowe przesyłanie danych diagnostycznych platformy Azure w warstwie gorąca ścieżce za pomocą usługi Event Hubs
Narzędzie diagnostyczne systemu Azure oferuje elastyczne sposoby zbierać metryki i dzienniki z maszyn wirtualnych usługi w chmurze (VM) i przenieść wyniki do usługi Azure Storage. Począwszy od marca 2016 r. (zestaw SDK 2.9) przedział czasu możesz wysyłanie danych diagnostycznych niestandardowymi źródłami danych i transferu danych ścieżkę aktywną w ciągu kilku sekund przy użyciu [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Obsługiwane typy danych obejmują:

* Zdarzenia funkcji Śledzenie zdarzeń systemu Windows (ETW)
* Liczniki wydajności
* Dzienniki zdarzeń systemu Windows
* Dzienniki aplikacji
* Dzienniki infrastruktury diagnostyki Azure

W tym artykule przedstawiono sposób konfigurowania usługi Azure Diagnostics przy użyciu usługi Event Hubs od końca do końca. Wskazówki dotyczące również znajduje się w następujących typowych scenariuszach:

* Jak dostosować dzienników i metryk, które są wysyłane do usługi Event Hubs
* Jak zmienić konfiguracje w każdym środowisku
* Jak wyświetlać dane strumienia usługi Event Hubs
* Jak rozwiązywać problemy z połączeniem  

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Event Hubs odbierania danych z usługi Azure Diagnostics jest obsługiwana w usługi w chmurze, maszyny wirtualne, zestawy skalowania maszyn wirtualnych i usługi Service Fabric, począwszy od Azure SDK 2.9 i odpowiednie narzędzia systemu Azure dla programu Visual Studio.

* Rozszerzenie diagnostyki platformy Azure w wersji 1.6 ([zestawu Azure SDK dla platformy .NET 2.9 lub nowszej](https://azure.microsoft.com/downloads/) jest przeznaczony dla to domyślnie)
* [Visual Studio 2013 lub nowszy](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Istniejących konfiguracji diagnostyki platformy Azure w aplikacji przy użyciu *.wadcfgx* pliku i jeden z następujących metod:
  * Visual Studio: [Konfigurowanie diagnostyki dla usług Azure Cloud Services i Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [Włączanie diagnostyki w usługach Azure Cloud Services przy użyciu programu PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Aprowizowane na artykułu, przestrzeń nazw usługi Event Hubs [Rozpoczynanie pracy z usługą Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Łączenie usługi Azure Diagnostics do ujścia usługi Event Hubs
Domyślnie Diagnostyka Azure zawsze wysyła dzienniki i metryki na konto usługi Azure Storage. Aplikacja może również wysyłać dane do usługi Event Hubs, dodając nowe **wychwytywanie** sekcji **PublicConfig** / **WadCfg** elementu *. wadcfgx* pliku. W programie Visual Studio *.wadcfgx* plik jest przechowywany w następującej ścieżce: **Projekt usługi w chmurze** > **role** >  **(RoleName)**  > **diagnostics.wadcfgx** pliku.

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

W tym przykładzie adres URL z Centrum zdarzeń jest równa w pełni kwalifikowaną przestrzeń nazw Centrum zdarzeń: Przestrzeń nazw usługi Event Hubs + "/" + Nazwa Centrum zdarzeń.  

Adres URL jest wyświetlany w Centrum zdarzeń [witryny Azure portal](https://go.microsoft.com/fwlink/?LinkID=213885) na pulpicie nawigacyjnym usługi Event Hubs.  

**Ujścia** nazwy, można ustawić dowolny prawidłowy ciąg, tak długo, jak taką samą wartość, jest stosowane konsekwentnie w całym pliku konfiguracji.

> [!NOTE]
> Mogą istnieć dodatkowe ujścia, takich jak *applicationInsights* skonfigurowane w tej sekcji. Narzędzie diagnostyczne systemu Azure umożliwia ujść co najmniej jeden zdefiniowany, jeśli każdy obiekt sink także jest zadeklarowany w **PrivateConfig** sekcji.  
>
>

Obiekt sink usługi Event Hubs również musi być zadeklarowana i zdefiniowane w **PrivateConfig** części *.wadcfgx* pliku konfiguracji.

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

`SharedAccessKeyName` Wartość musi być zgodna klucza sygnatury dostępu współdzielonego (SAS) i zasad, które zostały zdefiniowane w **usługi Event Hubs** przestrzeni nazw. Przejdź do pulpitu nawigacyjnego usługi Event Hubs w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **Konfiguruj** karcie i skonfiguruj zasady o nazwie (na przykład "SendRule"), który ma *wysyłania* uprawnienia. **StorageAccount** zadeklarowano również w **PrivateConfig**. Nie ma potrzeby można zmienić wartości w tym miejscu, jeśli pracują. W tym przykładzie pozostawimy wartości puste, który jest znakiem, że zasobu podrzędnego spowoduje ustawienie wartości. Na przykład *ServiceConfiguration.Cloud.cscfg* pliku konfiguracji środowiska ustawia odpowiednie środowisko nazwy i klucze.  

> [!WARNING]
> Klucz sygnatury dostępu Współdzielonego Event Hubs jest przechowywany w postaci zwykłego tekstu w *.wadcfgx* pliku. Często ten klucz jest zaewidencjonowane do kontroli kodu źródłowego lub jest dostępna jako zasobu na serwerze kompilacji, więc należy je chronić zgodnie z potrzebami. Firma Microsoft zaleca używanie klucza sygnatury dostępu Współdzielonego w tym miejscu przy użyciu *wysłać tylko* uprawnienia tak, aby złośliwy użytkownik zapisu do Centrum zdarzeń, ale nie może nasłuchiwać go ani zarządzać nim.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Konfigurowanie diagnostyki Azure do wysyłania dzienników i metryk do usługi Event Hubs
Jak omówiono wcześniej, wszystkie domyślne i Diagnostyka niestandardowa dane, oznacza to, metryk i dzienników, jest automatycznie przesyłany do usługi Azure Storage w skonfigurowanych interwałów. Za pomocą usługi Event Hubs i wszelkie dodatkowe ujścia w hierarchii do wysłania do Centrum zdarzeń można określić dowolny węzeł główny lub typu liść. Obejmuje to zdarzenia ETW, liczniki wydajności, Windows, dzienniki zdarzeń i dzienników aplikacji.   

Należy wziąć pod uwagę, jak wiele punktów danych, faktycznie mają zostać przeniesione do usługi Event Hubs. Zazwyczaj deweloperów transferu danych hot ścieżki o małych opóźnieniach, który musi być używane i szybko interpretowane. Przykłady są systemy monitorowania, alertów i reguł skalowania automatycznego. Deweloper może także skonfigurować Magazyn alternatywnego analizy lub Wyszukaj magazynu — na przykład usługi Azure Stream Analytics, usługi Elasticsearch, niestandardowego systemu monitorowania lub ulubionego systemu monitorowania przez inne osoby.

Poniżej przedstawiono kilka przykładowych konfiguracji.

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

W powyższym przykładzie obiekt sink jest stosowany do nadrzędnego **liczniki wydajności** węzeł w hierarchii, co oznacza, że wszystkie podrzędne **liczniki wydajności** będą wysyłane do usługi Event Hubs.  

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

W poprzednim przykładzie obiekt sink jest stosowany do tylko trzy liczniki: **Żądania w kolejce**, **żądań odrzuconych**, i **czas procesora (%)** .  

Poniższy przykład pokazuje, jak deweloper może ograniczyć ilość danych wysłanych do krytyczne metryki, które są używane na potrzeby tej usługi kondycji.  

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

W tym przykładzie obiekt sink jest stosowany do dzienników i jest filtrowana tylko do śledzenia poziomu błędu.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Wdrażanie i aktualizowanie konfiguracji aplikacji i Diagnostyka usług w chmurze
Program Visual Studio udostępnia najłatwiejszą drogą do wdrażania aplikacji i konfiguracji ujścia usługi Event Hubs. Aby wyświetlić i edytować plik, otwórz *.wadcfgx* pliku w programie Visual Studio, edytować go i zapisz go. Ścieżka jest **projekt usługi w chmurze** > **role** >  **(RoleName)**  > **diagnostics.wadcfgx**.  

W tym momencie wszystkie wdrożeniem i aktualizowanie działań w Visual Studio, Visual Studio Team System i wszystkich poleceń lub skryptów, które są oparte na MSBuild i użyj **/t: publikowanie** docelowy obejmują *.wadcfgx* w procesie tworzenia pakietów. Ponadto wdrożenia i aktualizacje wdrażanie pliku na platformie Azure przy użyciu odpowiedniego rozszerzenia agenta usługi Azure Diagnostics na maszynach wirtualnych.

Po wdrożeniu aplikacji i konfiguracji usługi Azure Diagnostics, natychmiast zobaczysz aktywność na pulpicie nawigacyjnym Centrum zdarzeń. Oznacza to, że możesz przejść do przeglądania danych ścieżkę aktywną w narzędziu klienta lub analiza odbiornika wybranego.  

Na poniższej ilustracji na pulpicie nawigacyjnym usługi Event Hubs zawiera dobrej kondycji wysyłania danych diagnostycznych do Centrum zdarzeń, od pewnego czasu po 23: 00. Gdy aplikacja została wdrożona przy użyciu zaktualizowanych *.wadcfgx* plików i obiekt sink został prawidłowo skonfigurowany.

![][0]  

> [!NOTE]
> Po wprowadzeniu aktualizacji do pliku konfiguracji usługi Azure Diagnostics (.wadcfgx), zaleca się wypchnięciu aktualizacji do całej aplikacji, a także konfigurację przy użyciu publikowania w programie Visual Studio lub skryptu środowiska Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Wyświetl dane ścieżkę aktywną
Jak wcześniej wspomniano, istnieje wiele zastosowań do nasłuchiwania i przetwarzanie danych usługi Event Hubs.

Jest jednym proste podejście do tworzenia aplikacji konsolowej mały test, do nasłuchiwania Centrum zdarzeń i drukowanie w strumieniu wyjściowym. Możesz umieścić następujący kod, który zostało wyjaśnione bardziej szczegółowo w [Rozpoczynanie pracy z usługą Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)), w aplikacji konsoli.  

Należy zauważyć, że aplikacja konsoli musi zawierać [pakietu NuGet hosta procesora zdarzeń](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Pamiętaj, aby zastąpić wartości w nawiasy ostre w **Main** funkcji z wartościami dla zasobów.   

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
            string eventHubConnectionString = "Endpoint= <your connection string>";
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>";
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

## <a name="troubleshoot-event-hubs-sinks"></a>Rozwiązywanie problemów z ujścia usługi Event Hubs
* Centrum zdarzeń nie są wyświetlane zdarzenia przychodzące lub wychodzące działania zgodnie z oczekiwaniami.

    Sprawdź, czy Centrum zdarzeń nie zostanie pomyślnie aprowizowane. Wszystkie informacje o połączeniu w **PrivateConfig** części *.wadcfgx* musi odpowiadać wartości zasobu, jak pokazano w portalu. Upewnij się, że zasady sygnatury dostępu Współdzielonego zdefiniowanych ("SendRule" w przykładzie) w portalu, który *wysyłania* uprawnienia.  
* Po zaktualizowaniu Centrum zdarzeń nie jest już aktywnością zdarzeń przychodzących lub wychodzących.

    Najpierw upewnij się, że informacje o Centrum i konfiguracji zdarzenia jest poprawna, jak wyjaśniono wcześniej. Czasami **PrivateConfig** jest resetowany do aktualizacji wdrożenia. Zalecane poprawki, jest zapewnienie wszystkie zmiany *.wadcfgx* w projekcie, a następnie wypychania aktualizacji kompletnej aplikacji. Jeśli nie jest to możliwe, upewnij się, że aktualizacji diagnostyki wypycha kompletna **PrivateConfig** zawierającej klucz sygnatury dostępu Współdzielonego.  
* Podjęto sugestii i Centrum zdarzeń nadal nie działa.

    Spróbuj wykonać wyszukiwanie w tabeli usługi Azure Storage, która zawiera dzienniki i błędów dla usługi Azure Diagnostics, sama: **WADDiagnosticInfrastructureLogsTable**. Jedną z opcji jest, aby użyć narzędzia, takie jak [Eksploratora usługi Azure Storage](https://www.storageexplorer.com) połączyć się z tego konta magazynu, wyświetlić tę tabelę, a następnie dodaj zapytanie dla sygnatury czasowej w ostatnich 24 godzinach. To narzędzie umożliwia eksportowanie pliku CSV, a następnie otwórz go w aplikacji, takich jak program Microsoft Excel. Excel można łatwo wyszukiwać ciągi karty telefonicznej, takich jak **EventHubs**, aby zobaczyć, jaki dokładnie błąd jest zgłaszany.  

## <a name="next-steps"></a>Kolejne kroki
• [Dowiedz się więcej o usłudze Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Dodatek: Kompletny przykład pliku (.wadcfgx) konfiguracji diagnostyki platformy Azure
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

Odpowiedniki ustawień JSON dla maszyn wirtualnych jest następująca:

Ustawienia publicznego:
```JSON
{
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

```

Chronione ustawienia:
```JSON
{
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

* [Omówienie usługi Event Hubs](../../event-hubs/event-hubs-about.md)
* [Tworzenie centrum zdarzeń](../../event-hubs/event-hubs-create.md)
* [Event Hubs — często zadawane pytania](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

