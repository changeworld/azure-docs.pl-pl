---
title: Rozszerzenie diagnostyki Azure 1.3 i nowszym schemat konfiguracji
description: Wersja schematu 1.3 i nowszym diagnostyki Azure dostarczana jako część 2.4 zestawu SDK programu Microsoft Azure i później.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 501e28cf3d01385d65a2308db06702d2db0d91ee
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937917"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>1.3 diagnostyki Azure i nowszym schemat konfiguracji
> [!NOTE]
> Rozszerzenie Azure Diagnostics jest składnik służący do zbierania liczników wydajności i innych danych statystycznych z:
> - Azure Virtual Machines 
> - Zestawy skali maszyn wirtualnych
> - Service Fabric 
> - Cloud Services 
> - Grupy zabezpieczeń sieci
> 
> Ta strona jest tylko istotne, jeśli używasz tych usług.

Ta strona jest nieprawidłowa dla wersji 1.3 i nowsze (Azure SDK 2.4 i nowszych). Nowsze sekcji konfiguracji są oznaczone jako do wyświetlenia, w jakiej wersji zostały dodane.  

Plik konfiguracji opisanych tutaj służy do ustawienia konfiguracji diagnostyczne podczas uruchamiania Monitora diagnostyki.  

Rozszerzenie jest używany w połączeniu z innymi produktami firmy Microsoft diagnostyki, takich jak Azure monitora, usługi Application Insights i analizy dzienników.



Pobierz definicję schematu pliku konfiguracji publicznego, wykonując następujące polecenie programu PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Aby uzyskać więcej informacji o korzystaniu z diagnostyki Azure, zobacz [rozszerzenia diagnostyki Azure](azure-diagnostics.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Przykładowy plik konfiguracji diagnostyki  
 W poniższym przykładzie przedstawiono plik konfiguracji typowych diagnostyki:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 --> 
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
   
    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
   
    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

Wartość równoważna JSON poprzedniego pliku konfiguracyjnego XML. 

PublicConfig i PrivateConfig są rozdzielone, ponieważ w większości przypadków użycia json, są przekazywane jako różne zmienne. Tych przypadkach obejmują szablony Menedżera zasobów, zestawu skalowania maszyn wirtualnych programu PowerShell i programu Visual Studio. 

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

## <a name="reading-this-page"></a>Odczytywanie tej strony  
 Tagi po około znajdują się w kolejności przedstawionej w poprzednim przykładzie.  Jeśli widzisz pełny opis, których można oczekiwać, wyszukaj strony elementu lub atrybutu.  

## <a name="common-attribute-types"></a>Popularne typy atrybutów  
 **scheduledTransferPeriod** atrybutu pojawia się w kilku elementów. Jest odstęp między zaplanowanego transferu do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Typ danych Duration."](http://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration Element  
 *Drzewa: Główny - DiagnosticsConfiguration*

Dodany w wersji 1.3.  

Element najwyższego poziomu w pliku konfiguracji diagnostyki.  

**Atrybut** xmlns — przestrzeń nazw XML dla pliku konfiguracji diagnostyki jest:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**PublicConfig**|Wymagany. Zobacz opis w innym miejscu na tej stronie.|  
|**PrivateConfig**|Opcjonalny. Zobacz opis w innym miejscu na tej stronie.|  
|**IsEnabled**|Wartość logiczna. Zobacz opis w innym miejscu na tej stronie.|  

## <a name="publicconfig-element"></a>PublicConfig Element  
 *Drzewa: PublicConfig - DiagnosticsConfiguration - katalogu głównego*

 Zawiera opis konfiguracji diagnostyki publicznego.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**WadCfg**|Wymagany. Zobacz opis w innym miejscu na tej stronie.|  
|**StorageAccount**|Nazwa konta magazynu Azure do przechowywania danych. Można także określić jako parametr podczas wykonywania polecenia cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Może być *tabeli*, *obiektu Blob*, lub *TableAndBlob*. Tabela jest domyślny. Po wybraniu TableAndBlob danych diagnostycznych są zapisywane dwukrotnie — raz dla każdego typu.|  
|**LocalResourceDirectory**|Katalog na maszynie wirtualnej, na którym Agent monitorowania przechowuje dane zdarzenia. Jeśli nie, ustawić, jest używany domyślny katalog:<br /><br /> Dla roli proces roboczy/sieci web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Dla maszyny wirtualnej: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Atrybuty wymagane są:<br /><br /> - **ścieżka** -katalogu w systemie mają być używane przez diagnostyki Azure.<br /><br /> - **expandEnvironment** — Określa, czy zmienne środowiskowe są rozwijane w nazwie ścieżki.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG katalogu głównego*
 
 Identyfikuje i konfiguruje dane telemetryczne, które mają być zbierane.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element 
 *Drzewa: DiagnosticMonitorConfiguration PublicConfig - WadCFG - głównego - DiagnosticsConfiguration-*

 Wymagane 

|Atrybuty|Opis|  
|----------------|-----------------|  
| **overallQuotaInMB** | Maksymalna ilość miejsca na dysku lokalnym, które mogą być używane przez różne rodzaje danych diagnostycznych zebranych przez diagnostyki Azure. Ustawienie domyślne to 4096 MB.<br />
|**useProxyServer** | Skonfiguruj diagnostyki Azure, aby użyć ustawienia serwera proxy zgodnie z ustawieniami w ustawieniach programu Internet Explorer.|  

<br /> <br />

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Zrzutów awaryjnych**|Zobacz opis w innym miejscu na tej stronie.|  
|**DiagnosticInfrastructureLogs**|Włącz zbieranie dzienników generowanych przez diagnostyki Azure. Dzienniki diagnostyczne infrastruktury są przydatne podczas rozwiązywania problemów Diagnostyka system. Opcjonalne atrybuty:<br /><br /> - **scheduledTransferLogLevelFilter** — konfiguruje poziom ważności minimalna dzienników zbierane.<br /><br /> - **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Typ danych Duration."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Katalogi**|Zobacz opis w innym miejscu na tej stronie.|  
|**EtwProviders**|Zobacz opis w innym miejscu na tej stronie.|  
|**Metryki**|Zobacz opis w innym miejscu na tej stronie.|  
|**PerformanceCounters**|Zobacz opis w innym miejscu na tej stronie.|  
|**WindowsEventLog**|Zobacz opis w innym miejscu na tej stronie.| 
|**DockerSources**|Zobacz opis w innym miejscu na tej stronie. | 



## <a name="crashdumps-element"></a>Element zrzutów awaryjnych  
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - zrzutów awaryjnych w katalogu głównego*
 
 Włącz zbieranie zrzutów awaryjnych.  

|Atrybuty|Opis|  
|----------------|-----------------|  
|**containerName**|Opcjonalny. Nazwa kontenera obiektów blob na koncie magazynu Azure używanego do przechowywania zrzuty awaryjne.|  
|**crashDumpType**|Opcjonalny.  Konfiguruje diagnostyki Azure do zbieranie zrzutów mini lub pełnej awarii.|  
|**directoryQuotaPercentage**|Opcjonalny.  Określa procent **overallQuotaInMB** mają zostać zarezerwowane dla zrzuty awaryjne na maszynie Wirtualnej.|  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Wymagany. Definiuje wartości konfiguracji dla każdego procesu.<br /><br /> Następujący atrybut jest również wymagany:<br /><br /> **Parametr** — nazwa procesu ma diagnostyki Azure, aby zbierać zrzutu awaryjnego dla.|  

## <a name="directories-element"></a>Element katalogów 
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - katalogów katalogu głównego*

 Umożliwia zbieranie zawartości katalogu, dzienniki żądań dostępu do usług IIS nie powiodło się i/lub dzienniki programu IIS.  

 Opcjonalne **scheduledTransferPeriod** atrybutu. Zawiera wyjaśnienie wcześniej.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**IISLogs**|W tym ten element w konfiguracji umożliwia zbieranie dzienników usług IIS:<br /><br /> **Właściwość containerName** — nazwa kontenera obiektów blob na koncie magazynu Azure używanego do przechowywania dzienników usług IIS.|   
|**FailedRequestLogs**|W tym ten element w konfiguracji umożliwia zbieranie dzienników dotyczące żądań zakończonych niepowodzeniem do witryny usług IIS lub aplikacji. Należy też włączyć śledzenie opcje w obszarze **systemu. Serwer sieci Web** w **Web.config**.|  
|**Źródła danych**|Lista katalogi do monitorowania.| 




## <a name="datasources-element"></a>Element źródeł danych  
 *Drzewa: Źródła danych PublicConfig - WadCFG - DiagnosticMonitorConfiguration - katalogów - głównego - DiagnosticsConfiguration-*

 Lista katalogi do monitorowania.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Wymagany. Wymagany atrybut:<br /><br /> **Właściwość containerName** — nazwa kontenera obiektów blob w magazynie Azure konta, które ma być używany do przechowywania plików dziennika.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - katalogów - DataSources — DirectoryConfiguration katalogu głównego*

 Może zawierać **bezwzględną** lub **LocalResource** elementu, ale nie oba.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Bezwzględne**|Ścieżka bezwzględna do katalogu, do monitorowania. Wymagane są następujące atrybuty:<br /><br /> - **Ścieżka** -ścieżka bezwzględna do katalogu, do monitorowania.<br /><br /> - **expandEnvironment** — Określa, czy są rozwinięte zmiennych środowiskowych w ścieżce.|  
|**LocalResource**|Ścieżka względna zasobu lokalnego do monitorowania. Atrybuty wymagane są:<br /><br /> - **Nazwa** -zasób lokalny, zawierająca katalogi do monitorowania<br /><br /> - **relativePath** -ścieżka względna nazwa zawierająca katalogi do monitorowania|  



## <a name="etwproviders-element"></a>EtwProviders Element  
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders katalogu głównego*

 Zbieranie zdarzeń ETW z EventSource konfiguruje i/lub ETW manifestu na podstawie dostawców.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfiguruje zbierania zdarzeń generowanych przez [EventSource — klasa](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Wymagany atrybut:<br /><br /> **Dostawca** — Nazwa klasy zdarzenia EventSource.<br /><br /> Opcjonalne atrybuty:<br /><br /> - **scheduledTransferLogLevelFilter** -minimalny poziom ważności na transfer do konta magazynu.<br /><br /> - **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Typ danych Duration."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Wymagany atrybut:<br /><br /> **Dostawca** — identyfikator GUID dostawcy zdarzeń<br /><br /> Opcjonalne atrybuty:<br /><br /> - **scheduledTransferLogLevelFilter** -minimalny poziom ważności na transfer do konta magazynu.<br /><br /> - **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Typ danych Duration."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwEventSourceProviderConfiguration katalogu głównego*

 Konfiguruje zbierania zdarzeń generowanych przez [EventSource — klasa](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DefaultEvents**|Atrybut opcjonalny:<br/><br/> **eventDestination** — Nazwa tabeli do przechowywania zdarzeń|  
|**Zdarzenie**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Atrybut opcjonalny:<br /><br /> **eventDestination** — Nazwa tabeli do przechowywania zdarzeń|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Drzewa: EtwManifestProviderConfiguration PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - głównego - DiagnosticsConfiguration-*

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DefaultEvents**|Atrybut opcjonalny:<br /><br /> **eventDestination** — Nazwa tabeli do przechowywania zdarzeń|  
|**Zdarzenie**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Atrybut opcjonalny:<br /><br /> **eventDestination** — Nazwa tabeli do przechowywania zdarzeń|  



## <a name="metrics-element"></a>Element metryk  
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - metryki katalogu głównego*

 Umożliwia generowanie tabeli licznika wydajności, która jest zoptymalizowana pod kątem szybkiego zapytania. Każdego licznika wydajności, który jest zdefiniowany w **liczniki wydajności** elementu są przechowywane w tabeli metryki oprócz tabeli licznika wydajności.  

 **ResourceId** atrybut jest wymagany.  Identyfikator zasobu maszyny wirtualnej lub zestawu skalowania maszyny wirtualnej diagnostyki Azure do wdrożenia. Pobierz **resourceID** z [portalu Azure](https://portal.azure.com). Wybierz **Przeglądaj** -> **grup zasobów** -> **< nazwa\>**. Kliknij przycisk **właściwości** Kafelek i skopiuj wartości z **identyfikator** pola.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**MetricAggregation**|Wymagany atrybut:<br /><br /> **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Typ danych Duration."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters — Element  
 *Drzewa: DiagnosticMonitorConfiguration PublicConfig - WadCFG - głównego - DiagnosticsConfiguration - - liczniki wydajności*

 Umożliwia zbieranie liczników wydajności.  

 Atrybut opcjonalny:  

 Opcjonalne **scheduledTransferPeriod** atrybutu. Zawiera wyjaśnienie wcześniej.

|Element podrzędny|Opis|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Wymagane są następujące atrybuty:<br /><br /> - **counterSpecifier** — Nazwa licznika wydajności. Na przykład `\Processor(_Total)\% Processor Time`. Aby uzyskać listę liczników wydajności na hoście, uruchom polecenie `typeperf`.<br /><br /> - **sampleRate** -częstotliwość próbkowania licznika.<br /><br /> Atrybut opcjonalny:<br /><br /> **Jednostka** — jednostka miary licznika.|  




## <a name="windowseventlog-element"></a>WindowsEventLog Element
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog katalogu głównego*
 
 Umożliwia zbieranie dzienników zdarzeń systemu Windows.  

 Opcjonalne **scheduledTransferPeriod** atrybutu. Zawiera wyjaśnienie wcześniej.  

|Element podrzędny|Opis|  
|-------------------|-----------------|  
|**DataSource**|Dzienniki zdarzeń systemu Windows do zbierania. Wymagany atrybut:<br /><br /> **Nazwa** — Kwerenda XPath opisujące zdarzeń systemu windows, które mają być zbierane. Na przykład:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> W celu gromadzenia wszystkich zdarzeń, określ "*"|  




## <a name="logs-element"></a>Element dzienników  
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - dzienniki katalogu głównego*

 Przedstawia w wersji 1.0, 1.1. Brak w 1.2. Dodane ponownie w 1.3.  

 Definiuje konfigurację buforu podstawowe dzienników Azure.  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Opcjonalny. Określa maksymalną ilość pamięci systemu plików, która jest dostępna dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilterr**|**Ciąg**|Opcjonalny. Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**, który przesyła wszystkie dzienniki. Inne możliwe wartości (w kolejności od najbardziej do najmniej informacji) to **pełne**, **informacji**, **ostrzeżenie**, **błąd**i **Krytyczne**.|  
|**scheduledTransferPeriod**|**Czas trwania**|Opcjonalny. Określa interwał między zaplanowane transferów danych, zaokrąglona w górę do najbliższej minutę.<br /><br /> Wartość domyślna to PT0S.|  
|**wychwytywanie** dodane w wersji 1.5|**Ciąg**|Opcjonalny. Wskazuje lokalizację odbioru można również wysyłać dane diagnostyczne. Na przykład usługi Application Insights.|  

## <a name="dockersources"></a>DockerSources
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources katalogu głównego*

 Dodane w 1.9.

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Statystyka**|Informuje system, aby zebrać statystykę dla kontenerów Docker|  

## <a name="sinksconfig-element"></a>SinksConfig Element  
 *Drzewa: SinksConfig PublicConfig - WadCFG - głównego - DiagnosticsConfiguration-*

 Lista lokalizacji wysyłanie danych diagnostycznych i konfiguracji skojarzone z tych lokalizacjach.  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Obiekt sink**|Zobacz opis w innym miejscu na tej stronie.|  

## <a name="sink-element"></a>Sink — Element
 *Drzewa: - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - zbiornika katalogu głównego*

 Dodane w wersji 1.5.  

 Określa lokalizacje do wysyłania danych diagnostycznych. Na przykład usługa Application Insights.  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Nazwa**|ciąg|Ciąg identyfikujący sinkname.|  

|Element|Typ|Opis|  
|-------------|----------|-----------------|  
|**Application Insights**|ciąg|Używana tylko wtedy, gdy wysyłanie danych do usługi Application Insights. Zawiera klucz instrumentacji dla aktywnego konta usługi Application Insights, czy masz dostęp do.|  
|**Kanały**|ciąg|Po jednej dla każdego dodatkowego filtrowania strumienia, który|  

## <a name="channels-element"></a>Element kanałów  
 *Drzewa: Kanały SinksConfig - zbiornika - głównego - DiagnosticsConfiguration - PublicConfig - WadCFG-*

 Dodane w wersji 1.5.  

 Definiuje filtry dla strumieni danych dziennika przechodzącej przez zbiorniku.  

|Element|Typ|Opis|  
|-------------|----------|-----------------|  
|**Channel**|ciąg|Zobacz opis w innym miejscu na tej stronie.|  

## <a name="channel-element"></a>Element kanału
 *Drzewa: Kanały SinksConfig - zbiornika - głównego - DiagnosticsConfiguration - PublicConfig - WadCFG - — kanału*

 Dodane w wersji 1.5.  

 Określa lokalizacje do wysyłania danych diagnostycznych. Na przykład usługa Application Insights.  

|Atrybuty|Typ|Opis|  
|----------------|----------|-----------------|  
|**logLevel**|**Ciąg**|Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**, który przesyła wszystkie dzienniki. Inne możliwe wartości (w kolejności od najbardziej do najmniej informacji) to **pełne**, **informacji**, **ostrzeżenie**, **błąd**i **Krytyczne**.|  
|**Nazwa**|**Ciąg**|Unikatowa nazwa kanału do odwoływania się do|  


## <a name="privateconfig-element"></a>PrivateConfig Element 
 *Drzewa: PrivateConfig - DiagnosticsConfiguration - katalogu głównego*

 Dodany w wersji 1.3.  

 Optional (Opcjonalność)  

 Przechowuje prywatne informacje szczegółowe konta magazynu (nazwa, klucz i końcowy). Te informacje są wysyłane do maszyny wirtualnej, ale nie można pobrać z niego.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**StorageAccount**|Konta magazynu do użycia. Następujące atrybuty są wymagane<br /><br /> - **Nazwa** — nazwa konta magazynu.<br /><br /> - **klucz** — klucz do konta magazynu.<br /><br /> - **punkt końcowy** — punkt końcowy do uzyskania dostępu do konta magazynu. <br /><br /> -**sasToken** (dodany 1.8.1)-tokenu sygnatury dostępu Współdzielonego, zamiast klucz konta magazynu można określić w prywatnej konfiguracji. Podany klucz konta magazynu jest ignorowana. <br />Wymagania dotyczące tokenu sygnatury dostępu Współdzielonego: <br />— Obsługuje tylko tokenu sygnatury dostępu Współdzielonego konta <br />- *b*, *t* typów usług są wymagane. <br /> - *a*, *c*, *u*, *w* uprawnienia są wymagane. <br /> - *c*, *o* typów zasobów są wymagane. <br /> — Obsługuje tylko protokół HTTPS <br /> -Start i czas wygaśnięcia musi być prawidłowy.|  


## <a name="isenabled-element"></a>IsEnabled Element  
 *Drzewa: IsEnabled - DiagnosticsConfiguration - katalogu głównego*

 Wartość logiczna. Użyj `true` umożliwiające diagnostyki lub `false` wyłączyć diagnostyki.
