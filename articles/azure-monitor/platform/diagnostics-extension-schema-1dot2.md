---
title: Schemat konfiguracji rozszerzenia Diagnostyka Azure 1,2
description: Dotyczy tylko sytuacji, gdy korzystasz z zestawu Azure SDK 2,5 z platformą Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric lub Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237835"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schemat konfiguracji Diagnostyka Azure 1,2
> [!NOTE]
> Diagnostyka Azure jest składnikiem używanym do zbierania liczników wydajności i innych statystyk z platformy Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric i Cloud Services.  Ta strona ma zastosowanie tylko w przypadku korzystania z jednej z tych usług.
>

Diagnostyka Azure jest używany z innymi produktami diagnostyki firmy Microsoft, takimi jak Azure Monitor, które obejmują Application Insights i Log Analytics.

Ten schemat definiuje możliwe wartości, których można użyć do zainicjowania ustawień konfiguracji diagnostyki podczas uruchamiania monitora diagnostyki.  


 Pobierz definicję schematu pliku konfiguracji publicznej, wykonując następujące polecenie programu PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Aby uzyskać więcej informacji o korzystaniu z Diagnostyka Azure, zobacz [Włączanie diagnostyki na platformie Azure Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Przykładowy plik konfiguracji diagnostyki  
 W poniższym przykładzie przedstawiono typowy plik konfiguracji diagnostyki:  

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

## <a name="diagnostics-configuration-namespace"></a>Przestrzeń nazw konfiguracji diagnostyki  
 Przestrzeń nazw XML dla pliku konfiguracji diagnostyki:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig Element  
 Element najwyższego poziomu pliku konfiguracji diagnostyki. W poniższej tabeli opisano elementy pliku konfiguracji.  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**WadCfg**|Wymagane. Ustawienia konfiguracji dla danych telemetrycznych, które mają być zbierane.|  
|**StorageAccount**|Nazwa konta usługi Azure Storage do przechowywania danych. Można to również określić jako parametr podczas wykonywania polecenia cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Katalog na maszynie wirtualnej, który ma być używany przez agenta monitorowania do przechowywania danych zdarzenia. Jeśli nie zostanie ustawiona, zostanie użyty katalog domyślny:<br /><br /> Dla roli proces roboczy/Sieć Web:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Dla maszyny wirtualnej:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Wymagane atrybuty:<br /><br /> -                      **ścieżka** — katalog w systemie, który ma być używany przez Diagnostyka Azure.<br /><br /> -                      **expandEnvironment** — określa, czy zmienne środowiskowe są rozwinięte w nazwie ścieżki.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Definiuje ustawienia konfiguracji dla danych telemetrycznych, które mają być zbierane. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Wymagane. Atrybuty opcjonalne:<br /><br /> -                     **overallQuotaInMB** — Maksymalna ilość miejsca na dysku lokalnym, która może być używana przez różne typy danych diagnostycznych zbieranych przez Diagnostyka Azure. Ustawieniem domyślnym jest 5120MB.<br /><br /> -                     **useProxyServer** — Skonfiguruj Diagnostyka Azure tak, aby korzystał z ustawień serwera proxy zgodnie z ustawieniem w ustawieniach programu IE.|  
|**Zrzutów awaryjnych formacie**|Włącz zbieranie zrzutów awaryjnych. Atrybuty opcjonalne:<br /><br /> -                     **ContainerName** — nazwa kontenera obiektów BLOB na koncie usługi Azure Storage, która ma być używana do przechowywania zrzutów awaryjnych.<br /><br /> -                     **crashDumpType** — konfiguruje Diagnostyka Azure w celu zbierania danych o minimalnych lub pełnych zrzutach awaryjnych.<br /><br /> -                     **directoryQuotaPercentage**— określa wartość procentową **overallQuotaInMB** do zarezerwowania na Zrzuty awaryjne na maszynie wirtualnej.|  
|**DiagnosticInfrastructureLogs**|Włącz zbieranie dzienników generowanych przez Diagnostyka Azure. Dzienniki infrastruktury diagnostycznej są przydatne do rozwiązywania problemów z systemem diagnostyki. Atrybuty opcjonalne:<br /><br /> -                     **scheduledTransferLogLevelFilter** — konfiguruje minimalny poziom ważności zbieranych dzienników.<br /><br /> -                     **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych "Duration" typu XML ".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Katalogi**|Włącza zbieranie zawartości katalogu, Dzienniki żądań dostępu i/lub dzienniki usług IIS. Opcjonalny atrybut:<br /><br /> **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych "Duration" typu XML ".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfiguruje kolekcję zdarzeń ETW z dostawców EventSource i/lub opartych na manifestach ETW.|  
|**Metryki**|Ten element umożliwia generowanie tabeli liczników wydajności zoptymalizowanej pod kątem szybkich zapytań. Każdy licznik wydajności, który jest zdefiniowany w elemencie **liczniki wydajności** , jest przechowywany w tabeli metryk oprócz tabeli liczników wydajności. Wymagany atrybut:<br /><br /> **ResourceID** — identyfikator zasobu maszyny wirtualnej, do której jest wdrażany Diagnostyka Azure. Pobierz identyfikator **zasobu** z [Azure Portal](https://portal.azure.com). Wybierz kolejno pozycje **Przeglądaj** -> **grupy** -> zasobów **< nazwa\>** . Kliknij kafelek **Właściwości** i skopiuj wartość z pola **Identyfikator** .|  
|**PerformanceCounters**|Włącza Zbieranie liczników wydajności. Opcjonalny atrybut:<br /><br /> **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość to [XML "typ danych czasu trwania".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Włącza zbieranie dzienników zdarzeń systemu Windows. Opcjonalny atrybut:<br /><br /> **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość to [XML "typ danych czasu trwania".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Zrzutów awaryjnych formacie, element  
 Włącza Zbieranie zrzutów awaryjnych. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Wymagana. Wymagany atrybut:<br /><br /> **ProcessName** — nazwa procesu, który ma Diagnostyka Azure zbierać zrzut awaryjny dla.|  
|**crashDumpType**|Konfiguruje Diagnostyka Azure w celu zbierania danych o minimalnych lub pełnych zrzutach awaryjnych.|  
|**directoryQuotaPercentage**|Określa wartość procentową **overallQuotaInMB** do zarezerwowania na Zrzuty awaryjne na maszynie wirtualnej.|  

## <a name="directories-element"></a>Elementy katalogów  
 Włącza zbieranie zawartości katalogu, Dzienniki żądań dostępu i/lub dzienniki usług IIS. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Źródła danych**|Lista katalogów do monitorowania.|  
|**FailedRequestLogs**|Uwzględnienie tego elementu w konfiguracji umożliwia zbieranie dzienników dotyczących żądań zakończonych niepowodzeniem do witryny lub aplikacji usług IIS. Należy również włączyć opcje śledzenia w obszarze **System. Serwer WebServer** w **pliku Web. config**.|  
|**IISLogs**|Uwzględnienie tego elementu w konfiguracji umożliwia zbieranie dzienników usług IIS:<br /><br /> **ContainerName** — nazwa kontenera obiektów BLOB na koncie usługi Azure Storage, która ma być używana do przechowywania dzienników usług IIS.|  

## <a name="datasources-element"></a>Element DataSources  
 Lista katalogów do monitorowania. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Wymagane. Wymagany atrybut:<br /><br /> **ContainerName** — nazwa kontenera obiektów BLOB na koncie usługi Azure Storage, która będzie używana do przechowywania plików dziennika.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration, element  
 **DirectoryConfiguration** może zawierać element bezwzględny lub **LocalResource** , ale nie oba jednocześnie. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Pozycjonowa**|Ścieżka bezwzględna do katalogu do monitorowania. Wymagane są następujące atrybuty:<br /><br /> -                     **Ścieżka** — bezwzględna ścieżka do katalogu do monitorowania.<br /><br /> -                      **expandEnvironment** — określa, czy zmienne środowiskowe w ścieżce są rozwinięte.|  
|**LocalResource**|Ścieżka względna do zasobu lokalnego do monitorowania. Wymagane atrybuty:<br /><br /> -                     **Nazwa** — zasób lokalny zawierający katalog do monitorowania<br /><br /> -                     **RelativePath** — ścieżka względna do nazwy zawierającej katalog do monitorowania|  

## <a name="etwproviders-element"></a>EtwProviders, element  
 Konfiguruje kolekcję zdarzeń ETW z dostawców EventSource i/lub opartych na manifestach ETW. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfiguruje kolekcję zdarzeń wygenerowanych z [klasy EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Wymagany atrybut:<br /><br /> **dostawca** — nazwa klasy zdarzenia EventSource.<br /><br /> Atrybuty opcjonalne:<br /><br /> -                     **scheduledTransferLogLevelFilter** — minimalny poziom ważności, który ma zostać przesłany na konto magazynu.<br /><br /> -                     **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych czas trwania XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Wymagany atrybut:<br /><br /> **dostawca** — identyfikator GUID dostawcy zdarzeń<br /><br /> Atrybuty opcjonalne:<br /><br /> - **scheduledTransferLogLevelFilter** — minimalny poziom ważności, który ma zostać przesłany na konto magazynu.<br /><br /> -                     **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych czas trwania XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Konfiguruje kolekcję zdarzeń wygenerowanych z [klasy EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DefaultEvents**|Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli, w której mają być przechowywane zdarzenia|  
|**Event**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli, w której mają być przechowywane zdarzenia|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DefaultEvents**|Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli, w której mają być przechowywane zdarzenia|  
|**Event**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli, w której mają być przechowywane zdarzenia|  

## <a name="metrics-element"></a>Element metryk  
 Umożliwia generowanie tabeli liczników wydajności zoptymalizowanej pod kątem szybkich zapytań. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**MetricAggregation**|Wymagany atrybut:<br /><br /> **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych czas trwania XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Liczniki wydajności, element  
 Włącza Zbieranie liczników wydajności. W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Wymagane są następujące atrybuty:<br /><br /> -                     **counterSpecifier** — Nazwa licznika wydajności. Na przykład `\Processor(_Total)\% Processor Time`. Aby uzyskać listę liczników wydajności na hoście, uruchom polecenie `typeperf`.<br /><br /> -                     **SampleRate** — częstotliwość próbkowania licznika.<br /><br /> Opcjonalny atrybut:<br /><br /> **Unit** — jednostka miary licznika.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration, element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**wskazani**|Wymagany atrybut:<br /><br /> **DisplayName** — nazwa wyświetlana licznika<br /><br /> Opcjonalny atrybut:<br /><br /> **locale** — ustawienia regionalne, które mają być używane podczas wyświetlania nazwy licznika|  

## <a name="windowseventlog-element"></a>WindowsEventLog, element  
 W poniższej tabeli opisano elementy podrzędne:  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**DataSource**|Dzienniki zdarzeń systemu Windows do zebrania. Wymagany atrybut:<br /><br /> **Nazwa** — zapytanie XPath opisujące zdarzenia systemu Windows, które mają być zbierane. Na przykład:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Aby zebrać wszystkie zdarzenia, określ wartość "*".|

