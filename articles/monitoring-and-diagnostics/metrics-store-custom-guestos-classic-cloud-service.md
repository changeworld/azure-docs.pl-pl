---
title: Wysyłanie metryk systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywać klasycznej usługi w chmurze
description: Wysyłanie metryk systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywać klasycznej usługi w chmurze
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986918"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Wysyłanie metryk systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywać klasycznej usługi w chmurze

Usługi Azure Monitor [rozszerzenia diagnostyki Azure Windows](azure-diagnostics.md) (WAD) umożliwia zbieranie metryk i dzienników z systemu operacyjnego gościa (guestOS) uruchomiona w ramach klastra maszyny wirtualnej, usługa w chmurze lub usługi Service Fabric.  Rozszerzenie może wysyłać telemetrię do wielu różnych lokalizacjach, wymienione w artykule wcześniej połączona.  

W tym artykule opisano proces metryki wydajności systemu operacyjnego gościa wysyłania Azure classic usług w chmurze w magazynie metryk usługi Azure Monitor. Począwszy od wersji 1.11 WAD, można napisać metryki bezpośrednio do sklepu metryk usługi Azure Monitor, gdy już zbieranymi metrykami standardowa platforma. Przechowywania ich w tej lokalizacji umożliwia dostęp do tego samego Akcje dostępne dla platform metryk.  Akcje obejmują niemal w czasie rzeczywistym alertów, wykresy, routingu, dostęp z interfejsu API REST i nie tylko.  W przeszłości rozszerzenia WAD powstała z jednego do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.  

Z procedurą opisaną w tym artykule działa tylko dla liczników wydajności w usługach Azure Cloud Services. Nie działa dla innych metryki niestandardowe. 
   

## <a name="pre-requisites"></a>Wymagania wstępne

- Musi być [administratorów usługi lub administratorów współpracujących](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) w subskrypcji platformy Azure 

- Twoja subskrypcja musi być zarejestrowana w [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Musisz mieć [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) zainstalowany, możesz też [usługi Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="provision-cloud-service-and-storage-account"></a>Zainicjuj obsługę usługi w chmurze i konto magazynu 

1. Tworzenie i wdrażanie klasyczne usługi w chmurze (Przykładowa aplikacja usługi chmura klasyczna i wdrożenia można znaleźć [tutaj](../cloud-services/cloud-services-dotnet-get-started.md). 

2. Można użyć istniejącego konta magazynu można też wdrażać nowe konto magazynu. Najlepiej jest konto magazynu, w tym samym regionie co klasycznej usługi w chmurze, właśnie utworzony. W witrynie Azure portal przejdź do bloku zasobów konta magazynu, a następnie wybierz **klucze**. Zanotuj konta nazwy i przechowywania klucza konta magazynu, należy je w kolejnych krokach.

   ![Klucze kont magazynu](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Tworzenie jednostki usługi 

Tworzenie jednostki usługi w dzierżawie usługi Azure Active Directory, korzystając z instrukcji w... / azure/azure-resource-manager/resource-group-create-service-principal-portal. Należy pamiętać, że podczas przechodzenia przez ten proces: 
  - Możesz umieścić dowolny adres URL dla adresu URL logowania jednokrotnego.  
  - Utwórz nowy wpis tajny klienta dla tej aplikacji  
  - Zapisz klucz i identyfikator klienta do użycia w kolejnych krokach.  

Nadaj aplikacji utworzonej w poprzednim kroku *wydawcy metryki monitorowania* uprawnienia do zasobów, aby emitować metryki względem. Jeśli planujesz korzystanie z aplikacji do emitowania metryki niestandardowe w odniesieniu do wielu zasobów, można przyznać te uprawnienia na poziomie grupy lub subskrypcji zasobów.  

> [!NOTE]
> Rozszerzenie diagnostyki używa nazwy głównej usługi do uwierzytelniania względem usługi Azure Monitor i emitują metryki dla usługi w chmurze 

## <a name="author-diagnostics-extension-configuration"></a>Konfiguracji rozszerzenia diagnostyki autora 

Przygotuj plik konfiguracji rozszerzenia diagnostyki WAD. Ten plik decyduje o tym, które dzienniki i liczniki wydajności rozszerzenie diagnostyki należy zbierać dla usługi w chmurze. Poniżej przedstawiono przykładowy plik konfiguracji diagnostyki.  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

W sekcji "SinksConfig" w pliku diagnostyki Definiowanie nowych ujścia usługi Azure Monitor: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

W sekcji w pliku konfiguracji, w którym liczników listę wydajności mają być zbierane Dodaj ujścia usługi Azure Monitor. Ten wpis zapewnia liczników wydajności określonych są kierowane do usługi Azure Monitor jako metryki. Możesz dodawać i usuwać liczniki wydajności, zgodnie z potrzebami. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
Na koniec w konfiguracji prywatnej, należy dodać *konta usługi Azure Monitor* sekcji. Wprowadź identyfikator klienta jednostki usługi i klucz tajny, które zostały utworzone w poprzednim kroku. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
Zapisz ten plik diagnostyki lokalnie.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Wdrażanie rozszerzenia diagnostyki usługi w chmurze 

Uruchom program PowerShell i zaloguj się do platformy Azure 

```PowerShell
Login-AzureRmAccount 
```

Store szczegóły na temat szczegółów konta magazynu utworzone w poprzednim kroku w zmiennych przy użyciu następujących poleceń. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
Podobnie, Ustaw ścieżkę do pliku diagnostyki do zmiennej za pomocą poniższego polecenia. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Wdrażanie rozszerzenia diagnostyki do usługi w chmurze przy użyciu pliku diagnostyki za pomocą ujścia usługi Azure Monitor skonfigurowany przy użyciu poniższego polecenia. 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Jest nadal wymagane, aby podać konto magazynu w ramach instalacji rozszerzenia diagnostyki. Dzienniki i/lub liczniki wydajności w pliku konfiguracyjnym diagnostyki zostanie zapisany do podanego konta magazynu.  

## <a name="plot-metrics-in-the-azure-portal"></a>Wykres metryk w witrynie Azure portal 

Przejdź do witryny Azure portal 

 ![Metryki witryny Azure portal](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. W menu po lewej stronie kliknij Monitor 

1. W bloku Monitor kliknij kartę metryki (wersja zapoznawcza) 

1. Na rozwijanej zasobów wybierz klasycznej usługi w chmurze 

1. W przestrzeniach nazw listy rozwijanej wybierz **azure.vm.windows.guest** 

1. W metryki listę rozwijaną, wybierz *wartości pamięć\zadeklarowane bajty w użyciu* 

Można wyświetlić całkowita pamięć używana przez określoną rolę, a każde wystąpienie roli przy użyciu filtrowania wymiarów i dzielenia możliwości. 

 ![Metryki witryny Azure portal](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).



