---
title: Schemat konfiguracji diagnostyki Azure 1.0
description: TYLKO odpowiednie Jeśli używasz 2.4 zestawu SDK platformy Azure i poniżej z maszyn wirtualnych platformy Azure, zestawy skalowania maszyn wirtualnych, sieci szkieletowej usług lub usługi w chmurze.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 916e2123262402e23f35778e66683ecce2cec4b7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262589"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schemat konfiguracji diagnostyki Azure 1.0
> [!NOTE]
> Diagnostyka Azure jest używane do zbierania liczników wydajności i innych danych statystycznych z maszyn wirtualnych platformy Azure, zestawy skalowania maszyn wirtualnych, sieci szkieletowej usług i usługi w chmurze.  Ta strona jest tylko istotne, jeśli używasz tych usług.
>

Diagnostyka Azure jest używana z innymi produktami firmy Microsoft diagnostyki, takich jak Azure monitora, usługi Application Insights i analizy dzienników.

Plik konfiguracji diagnostyki Azure definiuje wartości, które są używane do zainicjowania monitora diagnostyki. Ten plik służy do inicjowania ustawień diagnostycznych konfiguracji podczas uruchamiania Monitora diagnostyki.  

 Domyślnie plik schematu konfiguracji diagnostyki Azure jest instalowany na `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` katalogu. Zastąp `<version>` z zainstalowaną wersją [zestawu Azure SDK](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Plik konfiguracji diagnostyki zazwyczaj jest używany z uruchomienia zadania, które wymagają danych diagnostycznych, które mają być zbierane wcześniej w procesie uruchamiania. Aby uzyskać więcej informacji o korzystaniu z diagnostyki Azure, zobacz [zbierania danych rejestrowania za pomocą diagnostyki Azure](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Przykładowy plik konfiguracji diagnostyki  
 W poniższym przykładzie przedstawiono plik konfiguracji typowych diagnostyki:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>Namespace DiagnosticsConfiguration  
 Przestrzeń nazw XML dla pliku konfiguracji diagnostyki jest:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementy schematu  
 Plik konfiguracji diagnostyki obejmuje następujące elementy.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element  
Element najwyższego poziomu w pliku konfiguracji diagnostyki.  

Atrybuty:

|Atrybut  |Typ   |Wymagane| Domyślne | Opis|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|czas trwania|Optional (Opcjonalność) | PT1M| Określa interwał, jaką monitor diagnostyczny sonduje zmiany konfiguracji diagnostyki.|  
|**overallQuotaInMB**|unsignedInt|Optional (Opcjonalność)| 4000 MB. Jeżeli określona wartość nie może przekraczać tę wartość |Łączną ilość przydzielonego na wszystkie bufory rejestrowania magazyn systemu plików.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs Element  
Definiuje konfigurację buforu dzienników, które są generowane przez podstawowej infrastruktury diagnostyki.

Element nadrzędny: [DiagnosticMonitorConfiguration elementu](#DiagnosticMonitorConfiguration).  

Atrybuty:

|Atrybut|Typ|Opis|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość pamięci systemu plików, która jest dostępna dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|ciąg|Opcjonalny. Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**. Inne możliwe wartości to **pełne**, **informacji**, **ostrzeżenie**, **błąd**, i **krytyczny**.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowane transferów danych, zaokrąglona w górę do najbliższej minutę.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="logs-element"></a>Element dzienników  
 Definiuje konfigurację buforu podstawowe dzienników Azure.

 Element nadrzędny: [DiagnosticMonitorConfiguration elementu](#DiagnosticMonitorConfiguration).  

Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość pamięci systemu plików, która jest dostępna dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|ciąg|Opcjonalny. Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**. Inne możliwe wartości to **pełne**, **informacji**, **ostrzeżenie**, **błąd**, i **krytyczny**.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowane transferów danych, zaokrąglona w górę do najbliższej minutę.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="directories-element"></a>Element katalogów  
Definiuje konfigurację buforu dla opartych na plikach dzienników zdefiniowanych przez użytkownika.

Element nadrzędny: [DiagnosticMonitorConfiguration elementu](#DiagnosticMonitorConfiguration).  


Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość pamięci systemu plików, która jest dostępna dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowane transferów danych, zaokrąglona w górę do najbliższej minutę.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="crashdumps-element"></a>Element zrzutów awaryjnych  
 Określa katalog zrzuty awaryjne.

 Element nadrzędny: [Element katalogów](#Directories).  

Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Kontener**|ciąg|Nazwa kontenera, w którym ma zostać przeniesiony zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu w megabajtach.<br /><br /> Wartość domyślna to 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs Element  
 Określa katalog dziennika nie powiodło się żądanie.

 Element nadrzędny elementu [Element katalogów](#Directories).  

Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Kontener**|ciąg|Nazwa kontenera, w którym ma zostać przeniesiony zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu w megabajtach.<br /><br /> Wartość domyślna to 0.|  

##  <a name="iislogs-element"></a>IISLogs Element  
 Określa katalog dziennika usług IIS.

 Element nadrzędny elementu [Element katalogów](#Directories).  

Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Kontener**|ciąg|Nazwa kontenera, w którym ma zostać przeniesiony zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu w megabajtach.<br /><br /> Wartość domyślna to 0.|  

## <a name="datasources-element"></a>Element źródeł danych  
 Definiuje zero lub więcej katalogów dodatkowe dziennika.

 Element nadrzędny: [Element katalogów](#Directories).

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 Określa katalog plików dziennika do monitorowania.

 Element nadrzędny: [źródeł danych elementu](#DataSources).

Atrybuty:

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Kontener**|ciąg|Nazwa kontenera, w którym ma zostać przeniesiony zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu w megabajtach.<br /><br /> Wartość domyślna to 0.|  

## <a name="absolute-element"></a>Element bezwzględne  
 Określa ścieżkę bezwzględną katalogu można monitorować za pomocą środowiska opcjonalne rozszerzenia.

 Element nadrzędny: [DirectoryConfiguration elementu](#DirectoryConfiguration).  

Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Ścieżka**|ciąg|Wymagany. Ścieżka bezwzględna do katalogu, do monitorowania.|  
|**expandEnvironment**|wartość logiczna|Wymagany. Jeśli ustawiono **true**, zostaną rozwinięte zmiennych środowiskowych w ścieżce.|  

## <a name="localresource-element"></a>LocalResource Element  
 Określa ścieżkę względną wobec zasobu lokalnego określona w definicji usługi.

 Element nadrzędny: [DirectoryConfiguration elementu](#DirectoryConfiguration).  

Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Nazwa**|ciąg|Wymagany. Nazwa zasobu lokalnego zawierająca katalogi do monitorowania.|  
|**RelativePath**|ciąg|Wymagany. Ścieżka względna zasobu lokalnego do monitorowania.|  

## <a name="performancecounters-element"></a>PerformanceCounters — Element  
 Określa ścieżkę do wartości licznika wydajności do zbierania.

 Element nadrzędny: [DiagnosticMonitorConfiguration elementu](#DiagnosticMonitorConfiguration).


 Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość pamięci systemu plików, która jest dostępna dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowane transferów danych, zaokrąglona w górę do najbliższej minutę.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 Określa licznik wydajności do zbierania.

 Element nadrzędny: [PerformanceCounters — Element](#PerformanceCounters).  

 Atrybuty:  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**counterSpecifier**|ciąg|Wymagany. Ścieżka do liczników wydajności do zbierania.|  
|**sampleRate**|czas trwania|Wymagany. Częstotliwość licznika wydajności powinny być zbierane.|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 Definiuje dzienniki zdarzeń, aby monitorować.

 Element nadrzędny: [DiagnosticMonitorConfiguration elementu](#DiagnosticMonitorConfiguration).

  Atrybuty:

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość pamięci systemu plików, która jest dostępna dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|ciąg|Opcjonalny. Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**. Inne możliwe wartości to **pełne**, **informacji**, **ostrzeżenie**, **błąd**, i **krytyczny**.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowane transferów danych, zaokrąglona w górę do najbliższej minutę.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="datasource-element"></a>DataSource Element  
 Definiuje dziennik zdarzeń w celu monitorowania.

 Element nadrzędny: [WindowsEventLog elementu](#windowsEventLog).  

 Atrybuty:

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Nazwa**|ciąg|Wymagany. Wyrażenie XPath, określając dziennika do zbierania.|  
