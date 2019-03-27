---
title: Wysyłanie metryk systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywać klasycznej usługi w chmurze
description: Wysyłanie metryk systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywać usług w chmurze
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 90e841628d989a16f504d2efd7a2c7b18335ff48
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482627"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Wysyłanie metryk systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywać klasycznej usługi w chmurze 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dzięki usłudze Azure Monitor [rozszerzenie diagnostyki](diagnostics-extension-overview.md), można zbierać metryki i dzienniki z systemu operacyjnego gościa (systemu operacyjnego gościa) uruchomiona w ramach maszyny wirtualnej, usługa w chmurze lub klaster usługi Service Fabric. Rozszerzenie może wysyłać telemetrię do [wielu różnych lokalizacjach.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryki wydajności systemu operacyjnego gościa dla usługi Azure Cloud Services klasycznego do usługi Azure Monitor metryki magazynu. Począwszy od wersji 1.11 diagnostyki, można napisać metryki bezpośrednio do usługi Azure Monitor przechowywać metryki, którym już zbieranymi metrykami standardowa platforma. 

Przechowywanie ich w tej lokalizacji umożliwia dostęp do tych samych czynności, które mogą uzyskać większej liczby metryk platformy. Akcje obejmują niemal w czasie rzeczywistym alertów, wykresów i routing, dostęp z interfejsu API REST i nie tylko.  W przeszłości rozszerzenie diagnostyki zapisano do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.  

Proces, który jest opisany w tym artykule dotyczy tylko liczniki wydajności w usługach Azure Cloud Services. To nie zadziała, aby uzyskać inne metryki niestandardowe. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musi być [administratorów usługi lub administratorów współpracujących](~/articles/billing/billing-add-change-azure-subscription-administrator.md) w subskrypcji platformy Azure. 

- Twoja subskrypcja musi być zarejestrowana w [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musisz mieć [programu Azure PowerShell](/powershell/azure) lub [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) zainstalowane.

## <a name="provision-a-cloud-service-and-storage-account"></a>Aprowizacja konta magazynu i usługi chmury 

1. Tworzenie i wdrażanie klasyczną usługę w chmurze. Przykładowej aplikacji klasycznej w usługi w chmurze i wdrażania znajduje się w temacie [rozpoczęcie korzystania z usług Azure Cloud Services i platformy ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Można użyć istniejącego konta magazynu można też wdrażać nowe konto magazynu. Najlepiej jest konto magazynu, w tym samym regionie co usługa w chmurze klasyczne, który został utworzony. W witrynie Azure portal przejdź do **kont magazynu** bloku zasobów, a następnie wybierz **klucze**. Zanotuj nazwę konta magazynu i klucza konta magazynu. Należy te informacje w kolejnych krokach.

   ![Klucze kont magazynu](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi 

Tworzenie jednostki usługi w dzierżawie usługi Azure Active Directory zgodnie z instrukcjami podanymi w [w obsłudze portalu do tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Należy pamiętać o następujących, gdy zamierzasz przez ten proces: 

- Możesz umieścić dowolny adres URL dla adresu URL logowania.  
- Utwórz nowy wpis tajny klienta dla tej aplikacji.  
- Zapisz klucz i identyfikator klienta do użycia w kolejnych krokach.  

Nadaj aplikacji utworzonej w poprzednim kroku *wydawcy metryki monitorowania* uprawnienia do zasobów, aby emitować metryk. Jeśli planujesz korzystanie z aplikacji do emitowania metryki niestandardowe w odniesieniu do wielu zasobów, można przyznać te uprawnienia na poziomie grupy lub subskrypcji zasobów.  

> [!NOTE]
> Rozszerzenie diagnostyki używa nazwy głównej usługi do uwierzytelniania względem usługi Azure Monitor i emitują metryki dla usługi w chmurze.

## <a name="author-diagnostics-extension-configuration"></a>Tworzenie konfiguracji rozszerzenia diagnostyki 

Przygotuj plik konfiguracji rozszerzenia diagnostyki. Ten plik decyduje o tym, które dzienniki i liczniki wydajności rozszerzenie diagnostyki należy zbierać dla usługi w chmurze. Poniżej przedstawiono przykładowy plik konfiguracji diagnostyki:  

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

W sekcji pliku konfiguracji, w którym listę liczników wydajności do zbierania należy dodać ujścia usługi Azure Monitor. Ten wpis gwarantuje, że wszystkie liczniki wydajności, które można określić są kierowane do usługi Azure Monitor jako metryki. Można dodać lub usunąć liczniki wydajności, zgodnie z potrzebami. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Na koniec w konfiguracji prywatnej, należy dodać *konta usługi Azure Monitor* sekcji. Wprowadź identyfikator klienta jednostki usługi i klucz tajny, który został utworzony wcześniej. 

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

Uruchom program PowerShell i zaloguj się do platformy Azure. 

```powershell
Login-AzAccount 
```

Poniższe polecenia umożliwiają przechowywanie szczegółów konta magazynu, który został utworzony wcześniej. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Podobnie Ustaw ścieżkę pliku diagnostyki do zmiennej za pomocą następującego polecenia:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Wdróż rozszerzenie diagnostyki usługi w chmurze przy użyciu pliku diagnostyki za pomocą ujścia usługi Azure Monitor skonfigurowany przy użyciu następującego polecenia:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Jest nadal wymagane, aby podać konto magazynu w ramach instalacji rozszerzenia diagnostyki. Wszystkie dzienniki lub liczniki wydajności, które są określone w pliku konfiguracji diagnostyki są zapisywane do podanego konta magazynu.  

## <a name="plot-metrics-in-the-azure-portal"></a>Wykres metryk w witrynie Azure portal 

1. Przejdź do witryny Azure Portal. 

   ![Metryki witryny Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. W menu po lewej stronie wybierz **monitora.**

3. Na **Monitor** bloku wybierz **metryki (wersja zapoznawcza)** kartę.

4. W menu rozwijanym zasobów wybierz usługi w chmurze w klasycznym.

5. Wybierz z menu rozwijanego przestrzenie nazw **azure.vm.windows.guest**. 

6. Wybierz z menu rozwijanego metryki **wartości pamięć\zadeklarowane bajty w użyciu**. 

Wymiar, filtrowania i dzielenia możliwości umożliwia wyświetlanie całkowitej ilości pamięci używanej przez określoną rolę lub wystąpieniem roli. 

 ![Metryki witryny Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).

