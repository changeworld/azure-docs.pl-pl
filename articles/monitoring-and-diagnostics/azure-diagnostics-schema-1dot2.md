---
title: Rozszerzenie diagnostyki Azure 1.2 schemat konfiguracji
description: Znaczenie tylko wtedy, gdy 2.5 zestawu SDK platformy Azure za pomocą usługi Azure Virtual Machines, zestawy skalowania maszyny wirtualnej, sieci szkieletowej usług lub usługi w chmurze.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 8c3980231404e5c8068dbd011d20759f207d7fff
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937958"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schemat konfiguracji 1.2 Diagnostyka Azure
> [!NOTE]
> Diagnostyka Azure jest używane do zbierania liczników wydajności i innych danych statystycznych z maszyn wirtualnych platformy Azure, zestawy skalowania maszyn wirtualnych, sieci szkieletowej usług i usługi w chmurze.  Ta strona jest tylko istotne, jeśli używasz tych usług.
>

Diagnostyka Azure jest używana z innymi produktami firmy Microsoft diagnostyki, takich jak Azure monitora, usługi Application Insights i analizy dzienników.

Ten schemat definiuje możliwych wartości, które służy do inicjowania ustawień diagnostycznych konfiguracji podczas uruchamiania Monitora diagnostyki.  


 Pobierz definicję schematu pliku konfiguracji publicznego, wykonując następujące polecenie programu PowerShell:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Aby uzyskać więcej informacji o korzystaniu z diagnostyki Azure, zobacz [Włączanie diagnostyki w usług Azure Cloud Services](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Przykładowy plik konfiguracji diagnostyki  
 W poniższym przykładzie przedstawiono plik konfiguracji typowych diagnostyki:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
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
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
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
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Namespace konfiguracji diagnostyki  
 Przestrzeń nazw XML dla pliku konfiguracji diagnostyki jest:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig Element  
 Element najwyższego poziomu w pliku konfiguracji diagnostyki. W poniższej tabeli opisano elementy pliku konfiguracji.  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**WadCfg**|Wymagany. Ustawienia konfiguracji dla danych telemetrycznych do zebrania.|  
|**StorageAccount**|Nazwa konta magazynu Azure do przechowywania danych. To może również być określone jako parametr podczas wykonywania polecenia cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Katalog na maszynie wirtualnej do użycia przez agenta monitorowania do przechowywania danych o zdarzeniach. Jeśli nie jest używany zestaw, domyślny katalog:<br /><br /> Dla roli proces roboczy/sieci web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Dla maszyny wirtualnej: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Atrybuty wymagane są:<br /><br /> -                      **ścieżka** -katalogu w systemie mają być używane przez diagnostyki Azure.<br /><br /> -                      **expandEnvironment** — Określa, czy zmienne środowiskowe są rozwijane w nazwie ścieżki.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Definiuje ustawienia konfiguracji dla danych telemetrycznych do zebrania. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Wymagany. Opcjonalne atrybuty:<br /><br /> -                     **overallQuotaInMB** -maksymalną ilość miejsca na dysku lokalnym, które mogą być używane przez różne rodzaje danych diagnostycznych zebranych przez diagnostyki Azure. Ustawienie domyślne to 5120MB.<br /><br /> -                     **useProxyServer** -skonfigurować diagnostyki Azure, aby użyć ustawienia serwera proxy zgodnie z ustawieniami w ustawieniach programu Internet Explorer.|  
|**Zrzutów awaryjnych**|Włącz zbieranie zrzutów awarii. Opcjonalne atrybuty:<br /><br /> -                     **Właściwość containerName** — nazwa kontenera obiektów blob na koncie magazynu Azure używanego do przechowywania zrzuty awaryjne.<br /><br /> -                     **crashDumpType** -zrzuty konfiguruje diagnostyki Azure do zbierania Mini lub pełnego awarii.<br /><br /> -                     **directoryQuotaPercentage**— konfiguruje procent **overallQuotaInMB** mają zostać zarezerwowane dla zrzuty awaryjne na maszynie Wirtualnej.|  
|**DiagnosticInfrastructureLogs**|Włącz zbieranie dzienników generowanych przez diagnostyki Azure. Dzienniki diagnostyczne infrastruktury są przydatne podczas rozwiązywania problemów Diagnostyka system. Opcjonalne atrybuty:<br /><br /> -                     **scheduledTransferLogLevelFilter** — konfiguruje poziom ważności minimalna dzienników zbierane.<br /><br /> -                     **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Typ danych Duration."](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Katalogi**|Umożliwia zbieranie zawartości katalogu, dzienniki żądań dostępu do usług IIS nie powiodło się i/lub dzienniki programu IIS. Atrybut opcjonalny:<br /><br /> **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Typ danych Duration."](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Zbieranie zdarzeń ETW z EventSource konfiguruje i/lub ETW manifestu na podstawie dostawców.|  
|**Metryki**|Ten element umożliwia generowanie tabeli licznika wydajności, która jest zoptymalizowana pod kątem szybkiego zapytania. Każdego licznika wydajności, który jest zdefiniowany w **liczniki wydajności** elementu są przechowywane w tabeli metryki oprócz tabeli licznika wydajności. Wymagany atrybut:<br /><br /> **resourceId** -to jest identyfikator zasobu wdrażasz diagnostyki Azure do maszyny wirtualnej. Pobierz **resourceID** z [portalu Azure](https://portal.azure.com). Wybierz **Przeglądaj** -> **grup zasobów** -> **< nazwa\>**. Kliknij przycisk **właściwości** Kafelek i skopiuj wartości z **identyfikator** pola.|  
|**PerformanceCounters**|Umożliwia zbieranie liczników wydajności. Atrybut opcjonalny:<br /><br /> **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Czas trwania typu danych".](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Umożliwia zbieranie dzienników zdarzeń systemu Windows. Atrybut opcjonalny:<br /><br /> **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [XML "Czas trwania typu danych".](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Element zrzutów awaryjnych  
 Umożliwia zbieranie zrzutów awaryjnych. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Wymagany. Wymagany atrybut:<br /><br /> **Parametr** — nazwa procesu ma diagnostyki Azure, aby zbierać zrzutu awaryjnego dla.|  
|**crashDumpType**|Konfiguruje diagnostyki Azure do zbieranie zrzutów mini lub pełnej awarii.|  
|**directoryQuotaPercentage**|Określa procent **overallQuotaInMB** mają zostać zarezerwowane dla zrzuty awaryjne na maszynie Wirtualnej.|  

## <a name="directories-element"></a>Element katalogów  
 Umożliwia zbieranie zawartości katalogu, dzienniki żądań dostępu do usług IIS nie powiodło się i/lub dzienniki programu IIS. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Źródła danych**|Lista katalogi do monitorowania.|  
|**FailedRequestLogs**|W tym ten element w konfiguracji umożliwia zbieranie dzienników dotyczące żądań zakończonych niepowodzeniem do witryny usług IIS lub aplikacji. Należy też włączyć śledzenie opcje w obszarze **systemu. Serwer sieci Web** w **Web.config**.|  
|**IISLogs**|W tym ten element w konfiguracji umożliwia zbieranie dzienników usług IIS:<br /><br /> **Właściwość containerName** — nazwa kontenera obiektów blob na koncie magazynu Azure używanego do przechowywania dzienników usług IIS.|  

## <a name="datasources-element"></a>Element źródeł danych  
 Lista katalogi do monitorowania. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Wymagany. Wymagany atrybut:<br /><br /> **Właściwość containerName** — nazwa kontenera obiektów blob na koncie magazynu Azure używanego do przechowywania plików dziennika.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 **DirectoryConfiguration** może zawierać **bezwzględną** lub **LocalResource** elementu, ale nie oba. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Bezwzględne**|Ścieżka bezwzględna do katalogu, do monitorowania. Wymagane są następujące atrybuty:<br /><br /> -                     **Ścieżka** -ścieżka bezwzględna do katalogu, do monitorowania.<br /><br /> -                      **expandEnvironment** — Określa, czy są rozwinięte zmiennych środowiskowych w ścieżce.|  
|**LocalResource**|Ścieżka względna zasobu lokalnego do monitorowania. Atrybuty wymagane są:<br /><br /> -                     **Nazwa** -zasób lokalny, zawierająca katalogi do monitorowania<br /><br /> -                     **relativePath** -ścieżka względna nazwa zawierająca katalogi do monitorowania|  

## <a name="etwproviders-element"></a>EtwProviders Element  
 Zbieranie zdarzeń ETW z EventSource konfiguruje i/lub ETW manifestu na podstawie dostawców. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfiguruje zbierania zdarzeń generowanych przez [EventSource — klasa](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Wymagany atrybut:<br /><br /> **Dostawca** — Nazwa klasy zdarzenia EventSource.<br /><br /> Opcjonalne atrybuty:<br /><br /> -                     **scheduledTransferLogLevelFilter** -minimalny poziom ważności na transfer do konta magazynu.<br /><br /> -                     **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [typu danych XML czas trwania](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Wymagany atrybut:<br /><br /> **Dostawca** — identyfikator GUID dostawcy zdarzeń<br /><br /> Opcjonalne atrybuty:<br /><br /> - **scheduledTransferLogLevelFilter** -minimalny poziom ważności na transfer do konta magazynu.<br /><br /> -                     **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [typu danych XML czas trwania](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Konfiguruje zbierania zdarzeń generowanych przez [EventSource — klasa](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DefaultEvents**|Atrybut opcjonalny:<br /><br /> **eventDestination** — Nazwa tabeli do przechowywania zdarzeń|  
|**Zdarzenie**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Atrybut opcjonalny:<br /><br /> **eventDestination** — Nazwa tabeli do przechowywania zdarzeń|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DefaultEvents**|Atrybut opcjonalny:<br /><br /> **eventDestination** — Nazwa tabeli do przechowywania zdarzeń|  
|**Zdarzenie**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Atrybut opcjonalny:<br /><br /> **eventDestination** — Nazwa tabeli do przechowywania zdarzeń|  

## <a name="metrics-element"></a>Element metryk  
 Umożliwia generowanie tabeli licznika wydajności, która jest zoptymalizowana pod kątem szybkiego zapytania. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**MetricAggregation**|Wymagany atrybut:<br /><br /> **scheduledTransferPeriod** — interwał transferu zaplanowane do magazynu zaokrąglona w górę do najbliższej minutę. Wartość jest [typu danych XML czas trwania](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters — Element  
 Umożliwia zbieranie liczników wydajności. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Wymagane są następujące atrybuty:<br /><br /> -                     **counterSpecifier** — Nazwa licznika wydajności. Na przykład `\Processor(_Total)\% Processor Time`. Aby uzyskać listę wydajności liczniki na hoście Uruchom polecenie `typeperf`.<br /><br /> -                     **sampleRate** -częstotliwość próbkowania licznika.<br /><br /> Atrybut opcjonalny:<br /><br /> **Jednostka** — jednostka miary licznika.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Adnotacja**|Wymagany atrybut:<br /><br /> **Nazwa wyświetlana** — Nazwa wyświetlana dla licznika<br /><br /> Atrybut opcjonalny:<br /><br /> **Ustawienia regionalne** -ustawień regionalnych używany podczas wyświetlania Nazwa licznika|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DataSource**|Dzienniki zdarzeń systemu Windows do zbierania. Wymagany atrybut:<br /><br /> **Nazwa** — Kwerenda XPath opisujące zdarzeń systemu windows, które mają być zbierane. Na przykład:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> W celu gromadzenia wszystkich zdarzeń, określ "*".|
