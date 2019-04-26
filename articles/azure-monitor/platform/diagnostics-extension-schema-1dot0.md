---
title: Schemat konfiguracji usługi Diagnostyka Azure 1.0
description: Jest to istotne tylko jeśli używasz usługi Azure SDK 2.4 i poniżej za pomocą usługi Azure Virtual Machines, Virtual Machine Scale Sets, usługi Service Fabric lub usług w chmurze.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60237855"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schemat konfiguracji usługi Diagnostyka Azure 1.0
> [!NOTE]
> Narzędzie diagnostyczne systemu Azure to składnik używany do zbierania liczników wydajności i innych danych statystycznych z usługi Azure Virtual Machines, Virtual Machine Scale Sets, usługi Service Fabric i Cloud Services.  Ta strona ma zastosowanie tylko jeśli używasz jednej z tych usług.
>

Narzędzie diagnostyczne systemu Azure jest używany z innymi produktami firmy Microsoft diagnostyki, takich jak Azure Monitor, który zawiera usługi Application Insights i Log Analytics.

Plik konfiguracji usługi Azure Diagnostics definiuje wartości, które są stosowane do inicjalizacji Monitor diagnostyki. Ten plik jest używany do zainicjowania ustawień diagnostycznych konfiguracji podczas uruchamiania Monitora diagnostyki.  

 Domyślnie plik schematu konfiguracji diagnostyki platformy Azure jest zainstalowany na `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` katalogu. Zastąp `<version>` z zainstalowaną wersją programu [zestawu Azure SDK](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Plik konfiguracji diagnostyki jest zwykle używany z zadania uruchamiania, które wymagają danych diagnostycznych, które mają być zbierane we wcześniejszej części procesu uruchamiania. Aby uzyskać więcej informacji o korzystaniu z usługi Azure Diagnostics, zobacz [zbierania danych rejestrowania przez diagnostykę platformy Azure przy użyciu](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Przykładowy plik konfiguracji diagnostyki  
 Poniższy przykład przedstawia plik konfiguracji typowych diagnostyki:  

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
 Przestrzeń nazw XML w pliku konfiguracji diagnostyki jest:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementy schematu  
 Plik konfiguracji diagnostyki obejmuje następujące elementy.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element  
Element najwyższego poziomu w pliku konfiguracji diagnostyki.  

Atrybuty:

|Atrybut  |Type   |Wymagane| Domyślne | Opis|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|czas trwania|Optional (Opcjonalność) | PT1M| Określa interwał, jaką monitor diagnostyczny sonduje zmiany konfiguracji diagnostycznych.|  
|**overallQuotaInMB**|unsignedInt|Optional (Opcjonalność)| 4000 MB. Jeśli podano wartość nie może przekraczać tę kwotę |Łączna kwota magazyn systemu plików przydzielony dla wszystkich buforów rejestrowania.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs Element  
Definiuje konfigurację buforu dla dzienników, które są generowane przez podstawowej infrastruktury diagnostyki.

Element nadrzędny: DiagnosticMonitorConfiguration Element.  

Atrybuty:

|Atrybut|Type|Opis|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość magazyn systemu plików, które są dostępne dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|string|Opcjonalny. Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**. Inne możliwe wartości to **pełne**, **informacji**, **ostrzeżenie**, **błąd**, i **krytyczny**.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowanego transferu danych, zaokrąglane w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="logs-element"></a>Dzienniki elementu  
 Definiuje konfigurację buforu dla podstawowych dzienniki platformy Azure.

 Element nadrzędny: DiagnosticMonitorConfiguration Element.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość magazyn systemu plików, które są dostępne dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|string|Opcjonalny. Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**. Inne możliwe wartości to **pełne**, **informacji**, **ostrzeżenie**, **błąd**, i **krytyczny**.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowanego transferu danych, zaokrąglane w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="directories-element"></a>Element katalogów  
Definiuje konfigurację buforu dla dzienników opartych na plikach, które można zdefiniować.

Element nadrzędny: DiagnosticMonitorConfiguration Element.  


Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość magazyn systemu plików, które są dostępne dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowanego transferu danych, zaokrąglane w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="crashdumps-element"></a>Element zrzutów awaryjnych  
 Określa katalog zrzuty awarii.

 Element nadrzędny: Element katalogów.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**Kontener**|string|Nazwa kontenera, w którym ma zostać przeniesiony zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu w megabajtach.<br /><br /> Wartość domyślna to 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs Element  
 Określa katalog dziennika nie powiodło się żądanie.

 Element katalogi Element nadrzędny.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**Kontener**|string|Nazwa kontenera, w którym ma zostać przeniesiony zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu w megabajtach.<br /><br /> Wartość domyślna to 0.|  

##  <a name="iislogs-element"></a>IISLogs Element  
 Określa katalog dziennika usług IIS.

 Element katalogi Element nadrzędny.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**Kontener**|string|Nazwa kontenera, w którym ma zostać przeniesiony zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu w megabajtach.<br /><br /> Wartość domyślna to 0.|  

## <a name="datasources-element"></a>Element źródła danych  
 Określa zero lub więcej katalogów dzienniku dodatkowe.

 Element nadrzędny: Element katalogów.

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 Określa katalog plików dziennika do monitorowania.

 Element nadrzędny: Element źródła danych.

Atrybuty:

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**Kontener**|string|Nazwa kontenera, w którym ma zostać przeniesiony zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu w megabajtach.<br /><br /> Wartość domyślna to 0.|  

## <a name="absolute-element"></a>Element bezwzględne  
 Definiuje ścieżkę bezwzględną katalogu do monitorowania za pomocą środowiska opcjonalne rozszerzenia.

 Element nadrzędny: DirectoryConfiguration Element.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**path**|string|Wymagany. Ścieżka bezwzględna do katalogu, do monitorowania.|  
|**expandEnvironment**|wartość logiczna|Wymagany. Jeśli ustawiono **true**, zostaną rozwinięte zmiennych środowiskowych w ścieżce.|  

## <a name="localresource-element"></a>LocalResource Element  
 Określa ścieżkę względną zasób lokalny, określone w definicji usługi.

 Element nadrzędny: DirectoryConfiguration Element.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**Nazwa**|string|Wymagany. Nazwa zasobu lokalnego, która zawiera katalog do monitorowania.|  
|**relativePath**|string|Wymagany. Ścieżka względna zasobu lokalnego do monitorowania.|  

## <a name="performancecounters-element"></a>Performancecounters — Element  
 Określa ścieżkę licznika wydajności do zbierania.

 Element nadrzędny: DiagnosticMonitorConfiguration Element.


 Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość magazyn systemu plików, które są dostępne dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowanego transferu danych, zaokrąglane w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 Określa licznik wydajności do zbierania.

 Element nadrzędny: Element liczniki wydajności.  

 Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**counterSpecifier**|string|Wymagany. Ścieżka do liczników wydajności do zbierania.|  
|**sampleRate**|czas trwania|Wymagany. Szybkość, licznik wydajności powinny być zbierane.|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 Definiuje dzienniki zdarzeń, aby monitorować.

 Element nadrzędny: DiagnosticMonitorConfiguration Element.

  Atrybuty:

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość magazyn systemu plików, które są dostępne dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|string|Opcjonalny. Określa minimalny poziom ważności wpisy dziennika, które są przenoszone. Wartość domyślna to **niezdefiniowane**. Inne możliwe wartości to **pełne**, **informacji**, **ostrzeżenie**, **błąd**, i **krytyczny**.|  
|**scheduledTransferPeriod**|czas trwania|Opcjonalny. Określa interwał między zaplanowanego transferu danych, zaokrąglane w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="datasource-element"></a>Element źródła danych  
 Definiuje dziennika zdarzeń do monitorowania.

 Element nadrzędny: WindowsEventLog Element.  

 Atrybuty:

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**Nazwa**|string|Wymagany. Wyrażenie XPath, określając dziennika do zbierania.|  

