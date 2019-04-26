---
title: Rozszerzenie diagnostyki Azure 1.2 schemat konfiguracji
description: Jest to istotne tylko jeśli używasz zestawu SDK Azure 2.5 za pomocą usługi Azure Virtual Machines, Virtual Machine Scale Sets, usługi Service Fabric lub usług w chmurze.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60237835"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schemat konfiguracji usługi platformy Azure Diagnostyka 1.2
> [!NOTE]
> Narzędzie diagnostyczne systemu Azure to składnik używany do zbierania liczników wydajności i innych danych statystycznych z usługi Azure Virtual Machines, Virtual Machine Scale Sets, usługi Service Fabric i Cloud Services.  Ta strona ma zastosowanie tylko jeśli używasz jednej z tych usług.
>

Narzędzie diagnostyczne systemu Azure jest używany z innymi produktami firmy Microsoft diagnostyki, takich jak Azure Monitor, który zawiera usługi Application Insights i Log Analytics.

Ten schemat definiuje możliwych wartości, które można użyć do zainicjowania ustawień diagnostycznych konfiguracji podczas uruchamiania Monitora diagnostyki.  


 Pobierz definicję schematu pliku konfiguracji publicznej, wykonując następujące polecenie programu PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Aby uzyskać więcej informacji o korzystaniu z usługi Azure Diagnostics, zobacz [Włączanie diagnostyki w usługach Azure Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Przykładowy plik konfiguracji diagnostyki  
 Poniższy przykład przedstawia plik konfiguracji typowych diagnostyki:  

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
 Przestrzeń nazw XML w pliku konfiguracji diagnostyki jest:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig Element  
 Element najwyższego poziomu w pliku konfiguracji diagnostyki. W poniższej tabeli opisano elementy pliku konfiguracji.  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**WadCfg**|Wymagany. Ustawienia konfiguracji dla dane telemetryczne, mają być zbierane.|  
|**StorageAccount**|Nazwa konta usługi Azure Storage do przechowywania danych. To może zostać określony jako parametr podczas wykonywania polecenia cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Katalog na maszynie wirtualnej używany przez agenta monitorowania do przechowywania danych o zdarzeniach. Jeśli nie jest używany zestaw, katalog domyślny:<br /><br /> Dla roli procesu roboczego/sieci web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Dla maszyny wirtualnej: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Dostępne są następujące wymagane atrybuty:<br /><br /> -                      **ścieżka** -directory w systemie, który będzie używany przez usługi Azure Diagnostics.<br /><br /> -                      **expandEnvironment** -Określa, czy zmienne środowiskowe są rozwinięte w nazwie ścieżki.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Definiuje ustawienia konfiguracji z dane telemetryczne, które mają być zbierane. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Wymagany. Dostępne są następujące atrybuty opcjonalne:<br /><br /> -                     **overallQuotaInMB** -maksymalną ilość miejsca na dysku lokalnym, które mogą być używane przez różne rodzaje danych diagnostycznych zebranych przez diagnostykę platformy Azure. Ustawienie domyślne to 5120MB.<br /><br /> -                     **useProxyServer** — Konfigurowanie diagnostyki platformy Azure, użyj ustawień serwera proxy, zgodnie z ustawień programu Internet Explorer.|  
|**Zrzutów awaryjnych**|Włączanie zbierania zrzutów awaryjnych. Dostępne są następujące atrybuty opcjonalne:<br /><br /> -                     **containerName** — nazwa kontenera obiektów blob na koncie usługi Azure Storage ma być używany do przechowywania zrzuty awaryjne.<br /><br /> -                     **crashDumpType** -zrzuty konfiguruje diagnostyki Azure do zbierania Mini lub pełnej awarii.<br /><br /> -                     **directoryQuotaPercentage**— określa wartość procentową **overallQuotaInMB** mają zostać zarezerwowane dla zrzuty awaryjne na maszynie Wirtualnej.|  
|**DiagnosticInfrastructureLogs**|Włącz zbieranie dzienników generowanych przez usługi Azure Diagnostics. Dzienniki infrastruktury diagnostycznej są przydatne podczas rozwiązywania problemów sam system diagnostyki. Dostępne są następujące atrybuty opcjonalne:<br /><br /> -                     **scheduledTransferLogLevelFilter** — Określa minimalny poziom ważności zebranych danych dzienników.<br /><br /> -                     **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Wpisz dane czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Katalogi**|Umożliwia zbieranie zawartości katalogu, dzienniki żądania dostępu do usług IIS nie powiodło się i/lub dzienniki programu IIS. Opcjonalny atrybut:<br /><br /> **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Wpisz dane czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfiguruje zbieranie zdarzeń funkcji ETW z źródła zdarzeń i/lub manifestu ETW na podstawie dostawcy.|  
|**Metryki**|Ten element umożliwia generowanie tabeli liczników wydajności, która jest zoptymalizowana pod kątem zapytań. Każdego licznika wydajności, która jest zdefiniowana w **liczniki wydajności** elementu są przechowywane w tabeli metryk, oprócz tabeli licznika wydajności. Wymagany atrybut:<br /><br /> **resourceId** — jest to identyfikator zasobu maszyny wirtualnej diagnostyki Azure do wdrażania. Pobierz **resourceID** z [witryny Azure portal](https://portal.azure.com). Wybierz **Przeglądaj** -> **grup zasobów** -> **< nazwa\>**. Kliknij przycisk **właściwości** kafelka, a następnie skopiuj wartość z **identyfikator** pola.|  
|**PerformanceCounters**|Umożliwia zbieranie liczników wydajności. Opcjonalny atrybut:<br /><br /> **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Czas trwania Data Type".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Umożliwia zbieranie dzienników zdarzeń systemu Windows. Opcjonalny atrybut:<br /><br /> **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [XML "Czas trwania Data Type".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Element zrzutów awaryjnych  
 Umożliwia zbieranie zrzutów awaryjnych. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Wymagany. Wymagany atrybut:<br /><br /> **processName** — nazwa procesu ma diagnostyki Azure do zbierania zrzutu awaryjnego dla.|  
|**crashDumpType**|Umożliwia skonfigurowanie usługi Azure Diagnostics do zbierania zrzutów mini lub pełnej awarii.|  
|**directoryQuotaPercentage**|Określa wartość procentową **overallQuotaInMB** mają zostać zarezerwowane dla zrzuty awaryjne na maszynie Wirtualnej.|  

## <a name="directories-element"></a>Element katalogów  
 Umożliwia zbieranie zawartości katalogu, dzienniki żądania dostępu do usług IIS nie powiodło się i/lub dzienniki programu IIS. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DataSources**|Lista katalogów, do monitorowania.|  
|**FailedRequestLogs**|W konfiguracji w tym ten element umożliwia zbieranie dzienników dotyczących żądań zakończonych niepowodzeniem do witryny usług IIS lub aplikacji. Należy również włączyć śledzenie opcji w obszarze **systemu. Serwer sieci Web** w **Web.config**.|  
|**IISLogs**|W konfiguracji w tym ten element umożliwia zbieranie dzienników usług IIS:<br /><br /> **containerName** — nazwa kontenera obiektów blob na koncie usługi Azure Storage ma być używany do przechowywania dzienników usług IIS.|  

## <a name="datasources-element"></a>Element źródła danych  
 Lista katalogów, do monitorowania. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Wymagany. Wymagany atrybut:<br /><br /> **containerName** — nazwa kontenera obiektów blob na koncie usługi Azure Storage ma być używany do przechowywania plików dziennika.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 **DirectoryConfiguration** może zawierać albo **bezwzględne** lub **LocalResource** elementu, ale nie oba. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Bezwzględna**|Ścieżka bezwzględna do katalogu, do monitorowania. Wymagane są następujące atrybuty:<br /><br /> -                     **Ścieżka** — ścieżka bezwzględna do katalogu, do monitorowania.<br /><br /> -                      **expandEnvironment** -Określa, czy zostaną rozwinięte zmiennych środowiskowych w ścieżce.|  
|**LocalResource**|Ścieżka względna do monitorowania zasobów lokalnych. Dostępne są następujące wymagane atrybuty:<br /><br /> -                     **Nazwa** — zasób lokalny, która zawiera katalog do monitorowania<br /><br /> -                     **relativePath** — ścieżka względna nazwa, która zawiera katalog do monitorowania|  

## <a name="etwproviders-element"></a>EtwProviders Element  
 Konfiguruje zbieranie zdarzeń funkcji ETW z źródła zdarzeń i/lub manifestu ETW na podstawie dostawcy. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Umożliwia skonfigurowanie kolekcji zdarzeń generowanych przez [EventSource — klasa](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Wymagany atrybut:<br /><br /> **Dostawca** — Nazwa klasy zdarzeń EventSource.<br /><br /> Dostępne są następujące atrybuty opcjonalne:<br /><br /> -                     **scheduledTransferLogLevelFilter** — minimalny poziom ważności do przesyłania do swojego konta magazynu.<br /><br /> -                     **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [typu danych czasu trwania XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Wymagany atrybut:<br /><br /> **Dostawca** — identyfikator GUID dostawcy zdarzeń<br /><br /> Dostępne są następujące atrybuty opcjonalne:<br /><br /> - **scheduledTransferLogLevelFilter** — minimalny poziom ważności do przesyłania do swojego konta magazynu.<br /><br /> -                     **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [typu danych czasu trwania XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Umożliwia skonfigurowanie kolekcji zdarzeń generowanych przez [EventSource — klasa](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DefaultEvents**|Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwę tabeli do przechowywania zdarzeń|  
|**Zdarzenie**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwę tabeli do przechowywania zdarzeń|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DefaultEvents**|Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwę tabeli do przechowywania zdarzeń|  
|**Zdarzenie**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwę tabeli do przechowywania zdarzeń|  

## <a name="metrics-element"></a>Element metryki  
 Umożliwia generowanie tabeli liczników wydajności, która jest zoptymalizowana pod kątem zapytań. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**MetricAggregation**|Wymagany atrybut:<br /><br /> **okres zaplanowanego transferu** — interwał między zaplanowanego transferu do magazynu zaokrąglane w górę do najbliższej minuty. Wartość jest [typu danych czasu trwania XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Performancecounters — Element  
 Umożliwia zbieranie liczników wydajności. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Wymagane są następujące atrybuty:<br /><br /> -                     **counterSpecifier** — nazwę licznika wydajności. Na przykład `\Processor(_Total)\% Processor Time`. Aby uzyskać listę wydajności liczniki na hoście Uruchom polecenie `typeperf`.<br /><br /> -                     **sampleRate** — częstotliwość próbkowania licznika.<br /><br /> Opcjonalny atrybut:<br /><br /> **Jednostka** — jednostki miary licznika.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Adnotacja**|Wymagany atrybut:<br /><br /> **displayName** — Nazwa wyświetlana dla licznika<br /><br /> Opcjonalny atrybut:<br /><br /> **Ustawienia regionalne** — ustawienia regionalne do użycia przy wyświetlaniu Nazwa licznika|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DataSource**|Aby zebrać dzienniki zdarzeń Windows. Wymagany atrybut:<br /><br /> **Nazwa** — zapytanie XPath opisujące zdarzeń systemu windows, które mają być zbierane. Na przykład:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Aby zebrać wszystkie zdarzenia, należy określić "*".|

