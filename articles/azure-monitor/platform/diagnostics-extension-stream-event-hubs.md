---
title: Przesyłanie strumieniowe danych Diagnostyka Azure do Event Hubs
description: Konfigurowanie Diagnostyka Azure na zakończenie Event Hubs, w tym wskazówki dotyczące typowych scenariuszy.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: c5fc2199de8623dd3a9f2bc5faf23c7c40d67d75
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "64922829"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Przesyłanie strumieniowe danych Diagnostyka Azure w ścieżce gorąca przy użyciu Event Hubs
Diagnostyka Azure zapewnia elastyczne sposoby zbierania metryk i dzienników z maszyn wirtualnych usług Cloud Services i przesyłania wyników do usługi Azure Storage. Począwszy od okresu 2016 marca (SDK 2,9), można wysyłać diagnostykę do niestandardowych źródeł danych i transferować dane w postaci gorącej ścieżki w ciągu kilku sekund przy użyciu [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Obsługiwane typy danych obejmują:

* Zdarzenia funkcji Śledzenie zdarzeń systemu Windows (ETW)
* Liczniki wydajności
* Dzienniki zdarzeń systemu Windows
* Dzienniki aplikacji
* Dzienniki infrastruktury diagnostyki Azure

W tym artykule opisano sposób konfigurowania Diagnostyka Azure z Event Hubs od końca do końca. Wskazówki są również udostępniane w następujących typowych scenariuszach:

* Jak dostosować dzienniki i metryki, które są wysyłane do Event Hubs
* Jak zmienić konfiguracje w każdym środowisku
* Jak wyświetlać dane strumienia Event Hubs
* Jak rozwiązywać problemy z połączeniem  

## <a name="prerequisites"></a>Wymagania wstępne
Event Hubs otrzymywanie danych z Diagnostyka Azure jest obsługiwane w Cloud Services, maszynach wirtualnych, Virtual Machine Scale Sets i Service Fabric, począwszy od zestawu Azure SDK 2,9 i odpowiednich narzędzi platformy Azure dla programu Visual Studio.

* Diagnostyka Azure rozszerzenia 1,6 ([zestaw Azure SDK dla programu .net 2,9 lub nowszego jest domyślnie przeznaczony dla tego elementu](https://azure.microsoft.com/downloads/) )
* [Visual Studio 2013 lub nowszy](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Istniejące konfiguracje Diagnostyka Azure w aplikacji przy użyciu pliku *. wadcfgx* oraz jednej z następujących metod:
  * Visual Studio: [Konfigurowanie diagnostyki dla Cloud Services platformy Azure i Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Program Windows PowerShell: [Włączanie diagnostyki na platformie Azure Cloud Services przy użyciu programu PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Event Hubs przestrzeń nazw, która została zainicjowana zgodnie z artykułem, [wprowadzenie do Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Łączenie Diagnostyka Azure z Event Hubs ujścia
Domyślnie program Diagnostyka Azure zawsze wysyła dzienniki i metryki do konta usługi Azure Storage. Aplikacja może również wysyłać dane do Event Hubs przez dodanie nowej sekcji **ujścia** w elemencie **PublicConfig** / **WadCfg** pliku *. wadcfgx* . W programie Visual Studio plik *. wadcfgx* jest przechowywany w następującej ścieżce: > Plik diagnostyczny >  **** role > projektu usługi w chmurze (rolename **)** .**wadcfgx** .

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

W tym przykładzie adres URL centrum zdarzeń jest ustawiony na w pełni kwalifikowaną przestrzeń nazw centrum zdarzeń: Event Hubs przestrzeń nazw + "/" + nazwa centrum zdarzeń.  

W [Azure Portal](https://go.microsoft.com/fwlink/?LinkID=213885) na pulpicie nawigacyjnym Event Hubs zostanie wyświetlony adres URL centrum zdarzeń.  

Nazwę **ujścia** można ustawić na dowolny prawidłowy ciąg, o ile ta sama wartość jest używana spójnie w całym pliku konfiguracyjnym.

> [!NOTE]
> Mogą istnieć dodatkowe ujścia, takie jak *applicationInsights* skonfigurowany w tej sekcji. Diagnostyka Azure umożliwia zdefiniowanie co najmniej jednego ujścia, jeśli każdy obiekt ujścia jest również zadeklarowany w sekcji **PrivateConfig** .  
>
>

Obiekt sink Event Hubs musi być również zadeklarowany i zdefiniowany w sekcji **PrivateConfig** w pliku konfiguracyjnym *. wadcfgx* .

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

Wartość musi być zgodna z kluczem sygnatury dostępu współdzielonego (SAS) i zasadami zdefiniowanymi w przestrzeni nazw **Event Hubs.** `SharedAccessKeyName` Przejdź do pulpitu nawigacyjnego Event Hubs w [Azure Portal](https://portal.azure.com), kliknij kartę **Konfiguracja** i skonfiguruj nazwane zasady (na przykład "SendRule"), które mają uprawnienia do *wysyłania* . **StorageAccount** jest również zadeklarowany w **PrivateConfig**. Nie ma potrzeby zmiany wartości w tym miejscu, jeśli działają. W tym przykładzie pozostawiamy puste wartości, co oznacza, że zasób podrzędny ustawi wartości. Na przykład plik konfiguracyjny środowiska *ServiceConfiguration. Cloud. cscfg* ustawia nazwy i klucze odpowiednie dla środowiska.  

> [!WARNING]
> Klucz SAS Event Hubs jest przechowywany w postaci zwykłego tekstu w pliku *. wadcfgx* . Często ten klucz jest zaewidencjonowany do kontroli kodu źródłowego lub jest dostępny jako element zawartości na serwerze kompilacji, dlatego należy chronić go zgodnie z potrzebami. Zalecamy używanie klucza SAS tutaj z uprawnieniami *tylko do wysyłania* , aby złośliwy użytkownik mógł zapisywać w centrum zdarzeń, ale nie mógł się z nim odsłuchiwać ani zarządzać nim.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Konfigurowanie Diagnostyka Azure do wysyłania dzienników i metryk do Event Hubs
Jak wspomniano wcześniej, wszystkie domyślne i niestandardowe dane diagnostyczne, czyli metryki i dzienniki, są automatycznie wysyłane do usługi Azure Storage w skonfigurowanych odstępach czasu. Za pomocą Event Hubs i dowolnego dodatkowego ujścia można określić dowolny węzeł główny lub liścia w hierarchii, który ma być wysyłany do centrum zdarzeń. Dotyczy to zdarzeń ETW, liczników wydajności, dzienników zdarzeń systemu Windows i dzienników aplikacji.   

Ważne jest, aby uwzględnić liczbę punktów danych, które powinny być transferowane do Event Hubs. Zazwyczaj deweloperzy przesyłają dane gorącej ścieżki o małym opóźnieniu, które muszą być używane i interpretowane szybko. Przykładami są systemy, które monitorują alerty lub reguły automatycznego skalowania. Deweloper może również skonfigurować alternatywny magazyn analizy lub magazyn wyszukiwania — na przykład Azure Stream Analytics, Elasticsearch, niestandardowy system monitorowania lub ulubiony system monitorowania od innych.

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

W powyższym przykładzie obiekt ujścia jest stosowany do węzła nadrzędnego **liczniki wydajności** w hierarchii, co oznacza, że wszystkie podrzędne **liczniki wydajności** będą wysyłane do Event Hubs.  

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

W poprzednim przykładzie ujścia jest stosowane tylko do trzech liczników: **Żądania dodane do kolejki**, **żądania odrzucone**i **czas procesora (%)** .  

Poniższy przykład pokazuje, jak Projektant może ograniczyć ilość wysłanych danych jako metryki krytyczne, które są używane dla kondycji tej usługi.  

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

W tym przykładzie obiekt sink jest stosowany do dzienników i jest filtrowany tylko w ślad poziomu błędu.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Wdrażanie i aktualizowanie Cloud Services aplikacji i konfiguracji diagnostyki
Program Visual Studio zapewnia najłatwą ścieżkę do wdrożenia aplikacji i Event Hubs konfiguracji ujścia. Aby wyświetlić i edytować plik, Otwórz plik *. wadcfgx* w programie Visual Studio, edytuj go i Zapisz. Ścieżka jest diagnostyczna**role** >  **projektu** > usługi w chmurze (rolename **)**  >  **. wadcfgx**.  

W tym momencie wszystkie akcje aktualizacji wdrożenia i wdrożenia w programie Visual Studio, Visual Studio Team System i wszystkie polecenia lub skrypty, które są oparte na programie MSBuild i używają elementu docelowego **/t: Publish** , obejmują *. wadcfgx* w procesie pakowania. Ponadto wdrożenia i aktualizacje wdrażają plik na platformie Azure przy użyciu odpowiedniego rozszerzenia agenta Diagnostyka Azure na maszynach wirtualnych.

Po wdrożeniu aplikacji i konfiguracji Diagnostyka Azure natychmiast będzie ona widoczna na pulpicie nawigacyjnym centrum zdarzeń. Oznacza to, że wszystko jest gotowe do przejścia do wyświetlania danych w ścieżce gorącej w wybranym przez klienta odbiornika lub narzędziu do analizy.  

Na poniższej ilustracji na pulpicie nawigacyjnym Event Hubs są wyświetlane prawidłowe wysyłanie danych diagnostycznych do centrum zdarzeń, rozpoczynając od 11 PM. Oznacza to, że aplikacja została wdrożona przy użyciu zaktualizowanego pliku *. wadcfgx* , a ujścia zostało prawidłowo skonfigurowane.

![][0]  

> [!NOTE]
> Po wprowadzeniu aktualizacji Diagnostyka Azure pliku konfiguracji (. wadcfgx) zaleca się wypchnięcie aktualizacji do całej aplikacji oraz konfigurację przy użyciu programu Visual Studio Publishing lub skryptu programu Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Wyświetlanie danych ścieżki gorącej
Jak wspomniano wcześniej, istnieje wiele przypadków użycia do nasłuchiwania i przetwarzania danych Event Hubs.

Jednym z prostych metod jest utworzenie niewielkiej aplikacji konsolowej testowej w celu nasłuchiwania centrum zdarzeń i wydrukowania strumienia wyjściowego. Można umieścić Poniższy kod, który jest bardziej szczegółowy w temacie [wprowadzenie do Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)) w aplikacji konsolowej.  

Należy pamiętać, że Aplikacja konsolowa musi zawierać [pakiet NuGet hosta procesora zdarzeń](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Pamiętaj, aby zastąpić wartości w nawiasach kątowych w funkcji **Main** wartością dla zasobów.   

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

## <a name="troubleshoot-event-hubs-sinks"></a>Rozwiązywanie problemów z Event Hubs ujściami
* Centrum zdarzeń nie wyświetla aktywności zdarzenia przychodzącego lub wychodzącego zgodnie z oczekiwaniami.

    Sprawdź, czy centrum zdarzeń zostało pomyślnie zainicjowane. Wszystkie informacje o połączeniu w sekcji **PrivateConfig** *. wadcfgx* muszą być zgodne z wartościami Twojego zasobu wyświetlanymi w portalu. Upewnij się, że masz zdefiniowane zasady sygnatury dostępu współdzielonego ("SendRule" w przykładzie) w portalu oraz że udzielono uprawnienia do *wysyłania* .  
* Po aktualizacji centrum zdarzeń nie pokazuje już aktywności zdarzenia przychodzącego lub wychodzącego.

    Najpierw upewnij się, że informacje o centrum zdarzeń i konfiguracji są poprawne, jak wyjaśniono wcześniej. Czasami **PrivateConfig** jest resetowana w aktualizacji wdrożenia. Zalecanym rozwiązaniem jest wprowadzenie wszystkich zmian do *. wadcfgx* w projekcie, a następnie wypchnięcie pełnej aktualizacji aplikacji. Jeśli nie jest to możliwe, należy się upewnić, że aktualizacja diagnostyki wypycha pełną **PrivateConfig** , która zawiera klucz sygnatury dostępu współdzielonego.  
* Podjęto próbę dokonania sugestii, a centrum zdarzeń nadal nie działa.

    Spróbuj zajrzeć do tabeli usługi Azure Storage zawierającej dzienniki i błędy dla Diagnostyka Azure samego siebie: **WADDiagnosticInfrastructureLogsTable**. Jedną z opcji jest użycie narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://www.storageexplorer.com) , aby nawiązać połączenie z tym kontem magazynu, wyświetlić tę tabelę i dodać zapytanie dla sygnatury czasowej w ciągu ostatnich 24 godzin. Możesz użyć narzędzia, aby wyeksportować plik CSV i otworzyć go w aplikacji, takiej jak program Microsoft Excel. Program Excel ułatwia wyszukiwanie ciągów kart wywołujących, takich jak **EventHubs**, aby zobaczyć, jaki błąd jest zgłaszany.  

## <a name="next-steps"></a>Następne kroki
• [Dowiedz się więcej o Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Dodatek Zakończono przykład pliku konfiguracji Diagnostyka Azure (wadcfgx)
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

Uzupełniająca *Konfiguracja ServiceConfiguration. Cloud. cscfg* tego przykładu wygląda następująco.

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

Równoważne ustawienia JSON dla maszyn wirtualnych są następujące:

Ustawienia publiczne:
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

Ustawienia chronione:
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

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](../../event-hubs/event-hubs-about.md)
* [Tworzenie centrum zdarzeń](../../event-hubs/event-hubs-create.md)
* [Event Hubs — często zadawane pytania](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

