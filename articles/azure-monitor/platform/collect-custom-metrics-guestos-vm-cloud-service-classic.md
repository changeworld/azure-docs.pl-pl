---
title: Wysyłanie klasycznych metryk usług w chmurze do bazy danych metryk usługi Azure Monitor
description: W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla klasycznych usług w chmurze platformy Azure do magazynu metryk Usługi Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3b390ffa20cf3cf79b8fb6311ad05b2978bd5d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655802"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Wysyłanie metryk systemu operacyjnego gościa do klasycznego magazynu metrycznego usługi w chmurze usługi Azure Monitor 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą [rozszerzenia Diagnostyka](diagnostics-extension-overview.md)usługi Azure Monitor można zbierać metryki i dzienniki z systemu operacyjnego gościa (gos) działającego jako część maszyny wirtualnej, usługi w chmurze lub klastra sieci szkieletowej usług. Rozszerzenie może wysyłać dane telemetryczne do [wielu różnych lokalizacji.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla klasycznych usług w chmurze platformy Azure do magazynu metryk Usługi Azure Monitor. Począwszy od diagnostyki w wersji 1.11, można zapisywać metryki bezpośrednio w magazynie metryk usługi Azure Monitor, gdzie standardowe metryki platformy są już zbierane. 

Przechowywanie ich w tej lokalizacji umożliwia dostęp do tych samych akcji, które można dla metryk platformy. Akcje obejmują alerty w czasie zbliżonym do rzeczywistego, wykresy, routing, dostęp z interfejsu API REST i inne.  W przeszłości rozszerzenie diagnostyki napisał do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.  

Proces opisany w tym artykule działa tylko dla liczników wydajności w usługach Azure Cloud Services. To nie działa dla innych metryk niestandardowych. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być [administratorem usługi lub współadministratorem](../../cost-management-billing/manage/add-change-subscription-administrator.md) w ramach subskrypcji platformy Azure. 

- Subskrypcja musi być zarejestrowana w [witrynie Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musisz mieć zainstalowany [program Azure PowerShell](/powershell/azure) lub [usługa Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- Usługa w chmurze musi znajdować się w [regionie obsługującym metryki niestandardowe.](metrics-custom-overview.md#supported-regions)

## <a name="provision-a-cloud-service-and-storage-account"></a>Aprowizuj konto usługi i magazynu w chmurze 

1. Tworzenie i wdrażanie klasycznej usługi w chmurze. Przykładowa klasyczna aplikacja i wdrożenie usług w chmurze można znaleźć na stronie [Wprowadzenie do usług w chmurze Azure i ASP.NET.](../../cloud-services/cloud-services-dotnet-get-started.md) 

2. Można użyć istniejącego konta magazynu lub wdrożyć nowe konto magazynu. Najlepiej, jeśli konto magazynu znajduje się w tym samym regionie co klasyczna usługa w chmurze, która została utworzona. W witrynie Azure portal przejdź do bloku zasobów **kont magazynu,** a następnie wybierz pozycję **Klucze**. Zanotuj nazwę konta magazynu i klucz konta magazynu. Te informacje będą potrzebne w późniejszych krokach.

   ![Klucze kont magazynu](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi 

Utwórz zasadę usługi w dzierżawie usługi Azure Active Directory przy użyciu instrukcji w [portalu użycia, aby utworzyć aplikację i jednostkę usługi Azure Active Directory, która może uzyskiwać dostęp do zasobów.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Podczas przechodzenia przez ten proces należy zwrócić uwagę na następujące kwestie: 

- Możesz umieścić dowolny adres URL adresu URL logowania.  
- Utwórz nowy klucz tajny klienta dla tej aplikacji.  
- Zapisz klucz i identyfikator klienta do użycia w późniejszych krokach.  

Nadaj aplikacji utworzonej w poprzednim kroku *Monitorowanie metryk wydawcy* uprawnień do zasobu, dla którego chcesz emitować metryki. Jeśli zamierzasz użyć aplikacji do emitowania niestandardowych metryk względem wielu zasobów, możesz udzielić tych uprawnień na poziomie grupy zasobów lub subskrypcji.  

> [!NOTE]
> Rozszerzenie diagnostyki używa jednostki usługi do uwierzytelniania za pomocą usługi Azure Monitor i emitować metryki dla usługi w chmurze.

## <a name="author-diagnostics-extension-configuration"></a>Konfiguracja rozszerzenia diagnostyki autora 

Przygotuj plik konfiguracji rozszerzenia diagnostyki. Ten plik określa, które dzienniki i liczniki wydajności rozszerzenie diagnostyki powinny zbierać dla usługi w chmurze. Poniżej znajduje się przykładowy plik konfiguracyjny diagnostyki:  

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

W sekcji "SinksConfig" pliku diagnostycznego zdefiniuj nowy ujście usługi Azure Monitor: 

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

W sekcji pliku konfiguracji, w której można wyświetlić listę liczników wydajności do zbierania, dodaj ujście usługi Azure Monitor. Ten wpis gwarantuje, że wszystkie liczniki wydajności, które zostały określone są kierowane do usługi Azure Monitor jako metryki. Liczniki wydajności można dodawać lub usuwać zgodnie z potrzebami. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Na koniec w konfiguracji prywatnej dodaj sekcję *Konta usługi Azure Monitor.* Wprowadź identyfikator klienta jednostki usługi i klucz tajny, który został utworzony wcześniej. 

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

Zapisz ten plik diagnostyczny lokalnie.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Wdrażanie rozszerzenia diagnostyki w usłudze w chmurze 

Uruchom program PowerShell i zaloguj się na platformie Azure. 

```powershell
Login-AzAccount 
```

Poniższe polecenia umożliwiają przechowywanie szczegółów konta magazynu utworzonego wcześniej. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Podobnie ustaw ścieżkę pliku diagnostyki na zmienną za pomocą następującego polecenia:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Wdrażanie rozszerzenia diagnostyki w usłudze w chmurze za pomocą pliku diagnostycznego z ujściem usługi Azure Monitor skonfigurowanym przy użyciu następującego polecenia:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Nadal jest obowiązkowe, aby zapewnić konto magazynu w ramach instalacji rozszerzenia diagnostyki. Wszystkie dzienniki lub liczniki wydajności, które są określone w pliku konfiguracji diagnostyki są zapisywane na określonym koncie magazynu.  

## <a name="plot-metrics-in-the-azure-portal"></a>Drukowanie danych w witrynie Azure portal 

1. Przejdź do witryny Azure Portal. 

   ![Metryki witryny Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. W menu po lewej stronie wybierz pozycję **Monitor.**

3. Na bloku **Monitor** wybierz kartę **Podgląd metryk.**

4. W menu rozwijanym zasoby wybierz klasyczną usługę w chmurze.

5. W menu rozwijanym obszary nazw wybierz pozycję **azure.vm.windows.guest**. 

6. W menu rozwijanym metryki wybierz polecenie **Pamięć\Zatwierdzone bajty w użyciu**. 

Możliwości filtrowania i dzielenia wymiarów służą do wyświetlania całkowitej pamięci używanej przez określone wystąpienie roli lub roli. 

 ![Metryki witryny Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [metrykach niestandardowych](metrics-custom-overview.md).

