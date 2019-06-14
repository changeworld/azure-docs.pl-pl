---
title: Rozszerzenie diagnostyki Azure 1.3 i nowsze schemat konfiguracji
description: Schemat w wersji 1.3 i nowsze diagnostyki platformy Azure są dostarczane w ramach programu Microsoft Azure SDK 2.4 lub nowszy.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: fa03017c35c76d986139eeee00eea8a9b4a00e62
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60238051"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Azure Diagnostyka 1.3 i nowsze schemat konfiguracji
> [!NOTE]
> Rozszerzenie diagnostyki platformy Azure to składnik używany do zbierania liczników wydajności i innych danych statystycznych od:
> - Usługa Azure Virtual Machines
> - Zestawy skali maszyn wirtualnych
> - Service Fabric
> - Usługi w chmurze
> - Grupy zabezpieczeń sieci
>
> Ta strona ma zastosowanie tylko jeśli używasz jednej z tych usług.

Ta strona jest prawidłowy dla wersji 1.3 i nowsze (zestaw Azure SDK 2.4 i nowszych). Aby pokazać, w jakiej wersji zostały dodane są ujęte w nowszych sekcji konfiguracji.  

Plik konfiguracji opisane w tym miejscu jest używany do określenia ustawień konfiguracyjnych diagnostyczne, podczas uruchamiania Monitora diagnostyki.  

Rozszerzenie jest używany w połączeniu z innymi produktami firmy Microsoft diagnostyki, takich jak Azure Monitor, który zawiera usługi Application Insights i Log Analytics.



Pobierz definicję schematu pliku konfiguracji publicznej, wykonując następujące polecenie programu PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Aby uzyskać więcej informacji o korzystaniu z usługi Azure Diagnostics, zobacz [rozszerzenie Diagnostyka Azure](diagnostics-extension-overview.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Przykładowy plik konfiguracji diagnostyki  
 Poniższy przykład przedstawia plik konfiguracji typowych diagnostyki:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
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
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
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

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

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
> [!NOTE]
> Publiczne config definicji ujścia usługi Azure Monitor ma dwie właściwości, resourceId i region. Są to tylko wymagane dla klasycznych maszyn wirtualnych i klasycznych usług w chmurze. Te właściwości nie powinny być używane dla maszyn wirtualnych usługi Resource Manager lub zestawów skalowania maszyn wirtualnych.
> Istnieje również dodatkowy element konfiguracji prywatnej ujścia usługi Azure Monitor, który przekazuje jednostki identyfikatora i klucza tajnego. Jest to tylko wymagane dla klasycznych maszyn wirtualnych i klasycznej usługi w chmurze. Dla maszyn wirtualnych usługi Resource Manager i zestawu skalowania maszyn wirtualnych usługi Azure Monitor definicji w elemencie konfiguracji prywatnej można wykluczyć.
>

Poprzednie plik konfiguracyjny XML w przeliczeniu na formacie JSON.

PublicConfig i PrivateConfig są rozdzielone, ponieważ w większości przypadków użycia formatu json, są przekazywane jako różne zmienne. Te przypadki obejmują szablony usługi Resource Manager, zestawu skalowania maszyn wirtualnych programu PowerShell i programu Visual Studio.

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
                "sinks": "AzureMonitorSink",
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
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
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

> [!NOTE]
> Publiczne config definicji ujścia usługi Azure Monitor ma dwie właściwości, resourceId i region. Są to tylko wymagane dla klasycznych maszyn wirtualnych i klasycznych usług w chmurze.
> Te właściwości nie powinny być używane dla maszyn wirtualnych usługi Resource Manager lub zestawów skalowania maszyn wirtualnych.
>

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
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
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

> [!NOTE]
> Istnieje dodatkowy element konfiguracji prywatnej ujścia usługi Azure Monitor, który przekazuje jednostki identyfikatora i klucza tajnego. Jest to tylko wymagane dla klasycznych maszyn wirtualnych i klasycznej usługi w chmurze. Dla maszyn wirtualnych usługi Resource Manager i zestawu skalowania maszyn wirtualnych usługi Azure Monitor definicji w elemencie konfiguracji prywatnej można wykluczyć.
>


## <a name="reading-this-page"></a>Czytanie tej strony  
 Znaczniki po około znajdują się w kolejności przedstawionej w poprzednim przykładzie.  Jeśli nie widzisz pełny opis, których oczekujesz, wyszukaj strony elementu lub atrybutu.  

## <a name="common-attribute-types"></a>Popularne typy atrybutów  
 **okres zaplanowanego transferu** atrybutu jest wyświetlana w kilku elementów. Jest interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Wpisz dane czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration Element  
 *Drzewo: Root - DiagnosticsConfiguration*

Dodane w wersji 1.3.  

Element najwyższego poziomu w pliku konfiguracji diagnostyki.  

**Atrybut** xmlns — przestrzeń nazw XML w pliku konfiguracji diagnostyki jest:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**PublicConfig**|Wymagany. Zobacz opis w innym miejscu na tej stronie.|  
|**PrivateConfig**|Opcjonalny. Zobacz opis w innym miejscu na tej stronie.|  
|**isEnabled**|Wartość logiczna. Zobacz opis w innym miejscu na tej stronie.|  

## <a name="publicconfig-element"></a>PublicConfig Element  
 *Drzewo: Główny - DiagnosticsConfiguration - PublicConfig*

 W tym artykule opisano konfiguracji publicznego diagnostyki.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**WadCfg**|Wymagany. Zobacz opis w innym miejscu na tej stronie.|  
|**StorageAccount**|Nazwa konta usługi Azure Storage do przechowywania danych. Może zostać określony jako parametr podczas wykonywania polecenia cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Może być *tabeli*, *Blob*, lub *TableAndBlob*. Tabela jest domyślna. Po wybraniu TableAndBlob dane diagnostyczne są zapisywane dwa razy — raz dla każdego typu.|  
|**LocalResourceDirectory**|Katalog na maszynie wirtualnej, w którym Agent monitorowania zapisuje dane zdarzeń. Jeśli nie, ustawić, jest używany domyślny katalog:<br /><br /> Dla roli procesu roboczego/sieci web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Dla maszyny wirtualnej: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Dostępne są następujące wymagane atrybuty:<br /><br /> - **ścieżka** -directory w systemie, który będzie używany przez usługi Azure Diagnostics.<br /><br /> - **expandEnvironment** -Określa, czy zmienne środowiskowe są rozwinięte w nazwie ścieżki.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG*

 Identyfikuje i konfiguruje dane telemetryczne, które mają być zbierane.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element
 *Drzewo: DiagnosticMonitorConfiguration PublicConfig - WadCFG - głównego - DiagnosticsConfiguration-*

 Wymagane

|Atrybuty|Opis|  
|----------------|-----------------|  
| **overallQuotaInMB** | Maksymalna ilość miejsca na dysku lokalnym, które mogą być używane przez różne rodzaje danych diagnostycznych zebranych przez diagnostykę platformy Azure. Ustawienie domyślne to 4096 MB.<br />
|**useProxyServer** | Konfigurowanie diagnostyki Azure do użycia ustawienia serwera proxy jako zestaw ustawień programu Internet Explorer.|
|**Wychwytywanie** | Dodane w wersji 1.5. Opcjonalny. Wskazuje lokalizację obiektu sink, można również wysyłać dane diagnostyczne dla wszystkich elementów podrzędnych, które obsługują ujścia. Przykład obiektu sink to usługi Application Insights lub centrów zdarzeń.|  


<br /> <br />

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Zrzutów awaryjnych**|Zobacz opis w innym miejscu na tej stronie.|  
|**DiagnosticInfrastructureLogs**|Włącz zbieranie dzienników generowanych przez usługi Azure Diagnostics. Dzienniki infrastruktury diagnostycznej są przydatne podczas rozwiązywania problemów sam system diagnostyki. Dostępne są następujące atrybuty opcjonalne:<br /><br /> - **scheduledTransferLogLevelFilter** — Określa minimalny poziom ważności zebranych danych dzienników.<br /><br /> - **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Wpisz dane czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Katalogi**|Zobacz opis w innym miejscu na tej stronie.|  
|**EtwProviders**|Zobacz opis w innym miejscu na tej stronie.|  
|**Metryki**|Zobacz opis w innym miejscu na tej stronie.|  
|**PerformanceCounters**|Zobacz opis w innym miejscu na tej stronie.|  
|**WindowsEventLog**|Zobacz opis w innym miejscu na tej stronie.|
|**DockerSources**|Zobacz opis w innym miejscu na tej stronie. |



## <a name="crashdumps-element"></a>Element zrzutów awaryjnych  
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - zrzutów awaryjnych*

 Włączanie zbierania zrzutów awaryjnych.  

|Atrybuty|Opis|  
|----------------|-----------------|  
|**containerName**|Opcjonalny. Nazwa kontenera obiektów blob na koncie usługi Azure Storage ma być używany do przechowywania zrzuty awaryjne.|  
|**crashDumpType**|Opcjonalny.  Umożliwia skonfigurowanie usługi Azure Diagnostics do zbierania zrzutów mini lub pełnej awarii.|  
|**directoryQuotaPercentage**|Opcjonalny.  Określa wartość procentową **overallQuotaInMB** mają zostać zarezerwowane dla zrzuty awaryjne na maszynie Wirtualnej.|  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Wymagany. Definiuje wartości konfiguracji dla każdego procesu.<br /><br /> Następujący atrybut jest również wymagane:<br /><br /> **processName** — nazwa procesu ma diagnostyki Azure do zbierania zrzutu awaryjnego dla.|  

## <a name="directories-element"></a>Element katalogów
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - katalogów*

 Umożliwia zbieranie zawartości katalogu, dzienniki żądania dostępu do usług IIS nie powiodło się i/lub dzienniki programu IIS.  

 Opcjonalnie **okres zaplanowanego transferu** atrybutu. Zobacz opis wcześniej.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**IISLogs**|W konfiguracji w tym ten element umożliwia zbieranie dzienników usług IIS:<br /><br /> **containerName** — nazwa kontenera obiektów blob na koncie usługi Azure Storage ma być używany do przechowywania dzienników usług IIS.|   
|**FailedRequestLogs**|W konfiguracji w tym ten element umożliwia zbieranie dzienników dotyczących żądań zakończonych niepowodzeniem do witryny usług IIS lub aplikacji. Należy również włączyć śledzenie opcji w obszarze **systemu. Serwer sieci Web** w **Web.config**.|  
|**DataSources**|Lista katalogów, do monitorowania.|




## <a name="datasources-element"></a>Element źródła danych  
 *Drzewo: Źródła danych PublicConfig - WadCFG - DiagnosticMonitorConfiguration - katalogów - głównego - DiagnosticsConfiguration-*

 Lista katalogów, do monitorowania.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Wymagany. Wymagany atrybut:<br /><br /> **containerName** — nazwa kontenera obiektów blob w usłudze Azure Storage konta, które ma być używane do przechowywania plików dziennika.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 *Drzewo: Źródła danych z PublicConfig - WadCFG - DiagnosticMonitorConfiguration - katalogów - głównego - DiagnosticsConfiguration - - DirectoryConfiguration*

 Może zawierać albo **bezwzględne** lub **LocalResource** elementu, ale nie oba.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Bezwzględna**|Ścieżka bezwzględna do katalogu, do monitorowania. Wymagane są następujące atrybuty:<br /><br /> - **Ścieżka** — ścieżka bezwzględna do katalogu, do monitorowania.<br /><br /> - **expandEnvironment** -Określa, czy zostaną rozwinięte zmiennych środowiskowych w ścieżce.|  
|**LocalResource**|Ścieżka względna do monitorowania zasobów lokalnych. Dostępne są następujące wymagane atrybuty:<br /><br /> - **Nazwa** — zasób lokalny, która zawiera katalog do monitorowania<br /><br /> - **relativePath** — ścieżka względna nazwa, która zawiera katalog do monitorowania|  



## <a name="etwproviders-element"></a>EtwProviders Element  
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Konfiguruje zbieranie zdarzeń funkcji ETW z źródła zdarzeń i/lub manifestu ETW na podstawie dostawcy.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Umożliwia skonfigurowanie kolekcji zdarzeń generowanych przez [EventSource — klasa](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Wymagany atrybut:<br /><br /> **Dostawca** — Nazwa klasy zdarzeń EventSource.<br /><br /> Dostępne są następujące atrybuty opcjonalne:<br /><br /> - **scheduledTransferLogLevelFilter** — minimalny poziom ważności do przesyłania do swojego konta magazynu.<br /><br /> - **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Wpisz dane czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Wymagany atrybut:<br /><br /> **Dostawca** — identyfikator GUID dostawcy zdarzeń<br /><br /> Dostępne są następujące atrybuty opcjonalne:<br /><br /> - **scheduledTransferLogLevelFilter** — minimalny poziom ważności do przesyłania do swojego konta magazynu.<br /><br /> - **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Wpisz dane czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Umożliwia skonfigurowanie kolekcji zdarzeń generowanych przez [EventSource — klasa](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DefaultEvents**|Opcjonalny atrybut:<br/><br/> **eventDestination** — nazwę tabeli do przechowywania zdarzeń|  
|**Event**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwę tabeli do przechowywania zdarzeń|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DefaultEvents**|Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwę tabeli do przechowywania zdarzeń|  
|**Event**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwę tabeli do przechowywania zdarzeń|  



## <a name="metrics-element"></a>Element metryki  
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration — metryki*

 Umożliwia generowanie tabeli liczników wydajności, która jest zoptymalizowana pod kątem zapytań. Każdego licznika wydajności, która jest zdefiniowana w **liczniki wydajności** elementu są przechowywane w tabeli metryk, oprócz tabeli licznika wydajności.  

 **ResourceId** atrybut jest wymagany.  Identyfikator zasobu maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych są wdrażane diagnostyki platformy Azure. Pobierz **resourceID** z [witryny Azure portal](https://portal.azure.com). Wybierz **Przeglądaj** -> **grup zasobów** ->  **< nazwa\>** . Kliknij przycisk **właściwości** kafelka, a następnie skopiuj wartość z **identyfikator** pola.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**MetricAggregation**|Wymagany atrybut:<br /><br /> **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Wpisz dane czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Performancecounters — Element  
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - liczniki wydajności*

 Umożliwia zbieranie liczników wydajności.  

 Opcjonalny atrybut:  

 Opcjonalnie **okres zaplanowanego transferu** atrybutu. Zobacz opis wcześniej.

|Element podrzędny|Opis|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Wymagane są następujące atrybuty:<br /><br /> - **counterSpecifier** — nazwę licznika wydajności. Na przykład `\Processor(_Total)\% Processor Time`. Aby uzyskać listę liczników wydajności na hoście, uruchom polecenie `typeperf`.<br /><br /> - **sampleRate** — częstotliwość próbkowania licznika.<br /><br /> Opcjonalny atrybut:<br /><br /> **Jednostka** — jednostki miary licznika.|
|**Wychwytywanie** | Dodane w wersji 1.5. Opcjonalny. Wskazuje lokalizację obiektu sink, można również wysyłać dane diagnostyczne. Na przykład usługa Azure Monitor lub centrów zdarzeń.|    




## <a name="windowseventlog-element"></a>WindowsEventLog Element
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Umożliwia zbieranie dzienników zdarzeń systemu Windows.  

 Opcjonalnie **okres zaplanowanego transferu** atrybutu. Zobacz opis wcześniej.  

|Element podrzędny|Opis|  
|-------------------|-----------------|  
|**DataSource**|Aby zebrać dzienniki zdarzeń Windows. Wymagany atrybut:<br /><br /> **Nazwa** — zapytanie XPath opisujące zdarzeń systemu windows, które mają być zbierane. Na przykład:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Aby zebrać wszystkie zdarzenia, należy określić "*"|  




## <a name="logs-element"></a>Dzienniki elementu  
 *Drzewo: Rejestruje root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration-*

 Obecne w wersji 1.0 i 1.1. Brak w 1.2. Dodane w 1.3.  

 Definiuje konfigurację buforu dla podstawowych dzienniki platformy Azure.  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Opcjonalny. Określa maksymalną ilość magazyn systemu plików, które są dostępne dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|**ciąg**|Opcjonalny. Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**, który przesyła wszystkie dzienniki. Inne możliwe wartości (w kolejności od najbardziej do najmniej informacji) to **pełne**, **informacji**, **ostrzeżenie**, **błąd**i **Krytyczne**.|  
|**scheduledTransferPeriod**|**Czas trwania**|Opcjonalny. Określa interwał między zaplanowanego transferu danych, zaokrąglane w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  
|**Wychwytywanie** |**ciąg**| Dodane w wersji 1.5. Opcjonalny. Wskazuje lokalizację obiektu sink, można również wysyłać dane diagnostyczne. Na przykład usługi Application Insights lub centrów zdarzeń.|  

## <a name="dockersources"></a>DockerSources
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Dodane w 1.9.

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Statystyki**|Informuje system w celu zbierania statystyk dla kontenerów Docker|  

## <a name="sinksconfig-element"></a>SinksConfig Element  
 *Drzewo: SinksConfig PublicConfig - WadCFG - głównego - DiagnosticsConfiguration-*

 Lista lokalizacji do przesyłania danych diagnostycznych i konfiguracji skojarzone z tych lokalizacji.  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**obiekt sink**|Zobacz opis w innym miejscu na tej stronie.|  

## <a name="sink-element"></a>Będący ujściem — Element
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig — ujście*

 Dodane w wersji 1.5.  

 Definiuje lokalizacjach w celu wysyłania danych diagnostycznych. Na przykład usługi Application Insights.  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**name**|string|Ciąg identyfikujący sinkname.|  

|Element|Typ|Opis|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Używany tylko w przypadku wysyłania danych do usługi Application Insights. Zawiera klucz instrumentacji dla aktywnego konta usługi Application Insights, który ma dostęp do.|  
|**Channels**|string|Jeden dla każdego dodatkowego filtrowanie strumienia|  

## <a name="channels-element"></a>Element kanałów  
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 Dodane w wersji 1.5.  

 Definiuje filtry dla strumieni danych dziennika przechodzi przez obiekt sink.  

|Element|Typ|Opis|  
|-------------|----------|-----------------|  
|**Channel**|string|Zobacz opis w innym miejscu na tej stronie.|  

## <a name="channel-element"></a>Element kanału
 *Drzewo: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 Dodane w wersji 1.5.  

 Definiuje lokalizacjach w celu wysyłania danych diagnostycznych. Na przykład usługi Application Insights.  

|Atrybuty|Typ|Opis|  
|----------------|----------|-----------------|  
|**logLevel**|**ciąg**|Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**, który przesyła wszystkie dzienniki. Inne możliwe wartości (w kolejności od najbardziej do najmniej informacji) to **pełne**, **informacji**, **ostrzeżenie**, **błąd**i **Krytyczne**.|  
|**name**|**ciąg**|Unikatowa nazwa kanału do odwoływania się do|  


## <a name="privateconfig-element"></a>PrivateConfig Element
 *Drzewo: Główny - DiagnosticsConfiguration - PrivateConfig*

 Dodane w wersji 1.3.  

 Optional (Opcjonalność)  

 Przechowuje prywatne szczegółów konta magazynu (nazwa, klucz i punkt końcowy). Te informacje są wysyłane do maszyny wirtualnej, ale nie można pobrać z niego.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**StorageAccount**|Konto magazynu do użycia. Wymagane są następujące atrybuty<br /><br /> - **Nazwa** -nazwa konta magazynu.<br /><br /> - **klucz** -klucza konta magazynu.<br /><br /> - **punkt końcowy** — punkt końcowy, aby uzyskać dostęp do konta magazynu. <br /><br /> -**sasToken** (dodane 1.8.1)-tokenu sygnatury dostępu Współdzielonego zamiast klucza konta magazynu można określić w konfiguracji prywatnej. Podany klucz konta magazynu jest ignorowana. <br />Wymagania dotyczące tokenu sygnatury dostępu Współdzielonego: <br />— Obsługuje tylko token sygnatury dostępu Współdzielonego konta <br />- *b*, *t* wymaganych typów usług. <br /> - *a*, *c*, *u*, *w* wymagane są uprawnienia. <br /> - *c*, *o* wymaganych typów zasobów. <br /> — Obsługuje tylko protokół HTTPS <br /> — Rozpocznij i czas wygaśnięcia musi być prawidłowy.|  


## <a name="isenabled-element"></a>IsEnabled Element  
 *Drzewo: Główny - DiagnosticsConfiguration - IsEnabled*

 Wartość logiczna. Użyj `true` włączyć diagnostyki lub `false` wyłączenie diagnostyki.

