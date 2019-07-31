---
title: Schemat konfiguracji Diagnostyka Azure 1,0
description: Dotyczy tylko sytuacji, gdy korzystasz z zestawu Azure SDK 2,4 i poniżej z platformą Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric lub Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237855"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schemat konfiguracji Diagnostyka Azure 1,0
> [!NOTE]
> Diagnostyka Azure jest składnikiem używanym do zbierania liczników wydajności i innych statystyk z platformy Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric i Cloud Services.  Ta strona ma zastosowanie tylko w przypadku korzystania z jednej z tych usług.
>

Diagnostyka Azure jest używany z innymi produktami diagnostyki firmy Microsoft, takimi jak Azure Monitor, które obejmują Application Insights i Log Analytics.

Plik konfiguracji Diagnostyka Azure definiuje wartości, które są używane do inicjowania monitora diagnostyki. Ten plik jest używany do inicjowania ustawień konfiguracji diagnostyki podczas uruchamiania monitora diagnostyki.  

 Domyślnie plik schematu konfiguracji Diagnostyka Azure jest instalowany w `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` katalogu. Zamień `<version>` na zainstalowaną wersję [zestawu Azure SDK](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Plik konfiguracji diagnostyki jest zazwyczaj używany z zadaniami uruchamiania, które wymagają zebrania danych diagnostycznych wcześniej w procesie uruchamiania. Aby uzyskać więcej informacji o korzystaniu z Diagnostyka Azure, zobacz [zbieranie danych rejestrowania przy użyciu Diagnostyka Azure](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Przykładowy plik konfiguracji diagnostyki  
 W poniższym przykładzie przedstawiono typowy plik konfiguracji diagnostyki:  

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

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration przestrzeń nazw  
 Przestrzeń nazw XML dla pliku konfiguracji diagnostyki:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementy schematu  
 Plik konfiguracji diagnostyki zawiera następujące elementy.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration, element  
Element najwyższego poziomu pliku konfiguracji diagnostyki.  

Atrybuty:

|Atrybut  |Type   |Wymagane| Domyślny | Opis|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Optional | PT1M| Określa interwał, w którym Monitor diagnostyczny sonduje zmiany konfiguracji diagnostyki.|  
|**overallQuotaInMB**|unsignedInt|Optional| 4000 MB. Jeśli podano wartość, nie może ona przekraczać tej kwoty |Całkowita ilość miejsca do magazynowania systemu plików przydzieloną dla wszystkich buforów rejestrowania.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs, element  
Definiuje konfigurację bufora dla dzienników generowanych przez podstawową infrastrukturę diagnostyki.

Element nadrzędny: DiagnosticMonitorConfiguration element.  

Atrybuty:

|Atrybut|Type|Opis|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalna. Określa maksymalną ilość magazynu systemu plików, który jest dostępny dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|ciąg|Opcjonalna. Określa minimalny poziom ważności wpisów dziennika, które są transferowane. Wartość domyślna to undefined. Inne możliwe wartości to **pełne**, **informacje**, **Ostrzeżenie**, **błąd**i **krytyczne**.|  
|**scheduledTransferPeriod**|duration|Opcjonalna. Określa interwał między planowanymi transferami danych zaokrągloną w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="logs-element"></a>Logs — element  
 Definiuje konfigurację bufora dla podstawowych dzienników platformy Azure.

 Element nadrzędny: DiagnosticMonitorConfiguration element.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalna. Określa maksymalną ilość magazynu systemu plików, który jest dostępny dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|ciąg|Opcjonalny. Określa minimalny poziom ważności wpisów dziennika, które są transferowane. Wartość domyślna to undefined. Inne możliwe wartości to **pełne**, **informacje**, **Ostrzeżenie**, **błąd**i **krytyczne**.|  
|**scheduledTransferPeriod**|duration|Opcjonalny. Określa interwał między planowanymi transferami danych zaokrągloną w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="directories-element"></a>Elementy katalogów  
Definiuje konfigurację bufora dla dzienników opartych na plikach, które można zdefiniować.

Element nadrzędny: DiagnosticMonitorConfiguration element.  


Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość magazynu systemu plików, który jest dostępny dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferPeriod**|duration|Opcjonalny. Określa interwał między planowanymi transferami danych zaokrągloną w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="crashdumps-element"></a>Zrzutów awaryjnych formacie, element  
 Definiuje katalog zrzutów awaryjnych.

 Element nadrzędny: Elementy katalogów.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**wbudowane**|ciąg|Nazwa kontenera, w którym ma zostać przetransferowana zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu (w megabajtach).<br /><br /> Wartość domyślna to 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs, element  
 Definiuje katalog dziennika żądań zakończonych niepowodzeniem.

 Element katalogów elementów nadrzędnych.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**wbudowane**|ciąg|Nazwa kontenera, w którym ma zostać przetransferowana zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu (w megabajtach).<br /><br /> Wartość domyślna to 0.|  

##  <a name="iislogs-element"></a>IISLogs, element  
 Definiuje katalog dzienników usług IIS.

 Element katalogów elementów nadrzędnych.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**wbudowane**|ciąg|Nazwa kontenera, w którym ma zostać przetransferowana zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalny rozmiar katalogu (w megabajtach).<br /><br /> Wartość domyślna to 0.|  

## <a name="datasources-element"></a>Element DataSources  
 Definiuje zero lub więcej dodatkowych katalogów dzienników.

 Element nadrzędny: Elementy katalogów.

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration, element  
 Definiuje katalog plików dziennika do monitorowania.

 Element nadrzędny: Element DataSources.

Atrybuty:

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**wbudowane**|ciąg|Nazwa kontenera, w którym ma zostać przetransferowana zawartość katalogu.|  
|**directoryQuotaInMB**|unsignedInt|Opcjonalna. Określa maksymalny rozmiar katalogu (w megabajtach).<br /><br /> Wartość domyślna to 0.|  

## <a name="absolute-element"></a>Element bezwzględny  
 Definiuje ścieżkę bezwzględną katalogu do monitorowania z opcjonalnym rozszerzeniem środowiska.

 Element nadrzędny: DirectoryConfiguration element.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**Ścieżka**|ciąg|Wymagane. Ścieżka bezwzględna do katalogu do monitorowania.|  
|**expandEnvironment**|boolean|Wymagana. W przypadku ustawienia **wartości true**zmienne środowiskowe w ścieżce są rozszerzane.|  

## <a name="localresource-element"></a>LocalResource, element  
 Definiuje ścieżkę względną do zasobu lokalnego zdefiniowanego w definicji usługi.

 Element nadrzędny: DirectoryConfiguration element.  

Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**name**|ciąg|Wymagana. Nazwa zasobu lokalnego, który zawiera katalog do monitorowania.|  
|**relativePath**|ciąg|Wymagane. Ścieżka względna do zasobu lokalnego do monitorowania.|  

## <a name="performancecounters-element"></a>Liczniki wydajności, element  
 Definiuje ścieżkę do licznika wydajności do zebrania.

 Element nadrzędny: DiagnosticMonitorConfiguration element.


 Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość magazynu systemu plików, który jest dostępny dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferPeriod**|duration|Opcjonalna. Określa interwał między planowanymi transferami danych zaokrągloną w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration, element  
 Definiuje licznik wydajności do zebrania.

 Element nadrzędny: Liczniki wydajności element.  

 Atrybuty:  

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**counterSpecifier**|ciąg|Wymagane. Ścieżka do licznika wydajności do zebrania.|  
|**sampleRate**|duration|Wymagana. Szybkość, z jaką ma być zbierany licznik wydajności.|  

## <a name="windowseventlog-element"></a>WindowsEventLog, element  
 Definiuje dzienniki zdarzeń do monitorowania.

 Element nadrzędny: DiagnosticMonitorConfiguration element.

  Atrybuty:

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcjonalny. Określa maksymalną ilość magazynu systemu plików, który jest dostępny dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|ciąg|Opcjonalny. Określa minimalny poziom ważności wpisów dziennika, które są transferowane. Wartość domyślna to undefined. Inne możliwe wartości to **pełne**, **informacje**, **Ostrzeżenie**, **błąd**i **krytyczne**.|  
|**scheduledTransferPeriod**|duration|Opcjonalna. Określa interwał między planowanymi transferami danych zaokrągloną w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  

## <a name="datasource-element"></a>DataSource — element  
 Definiuje dziennik zdarzeń do monitorowania.

 Element nadrzędny: WindowsEventLog element.  

 Atrybuty:

|Atrybut|Type|Opis|  
|---------------|----------|-----------------|  
|**name**|ciąg|Wymagany. Wyrażenie XPath określające dziennik do zebrania.|  

