---
title: Wysyłanie metryk systemu operacyjnego gościa do magazynu danych usługi Azure Monitor na maszynie wirtualnej Windows (wersja klasyczna)
description: Wysyłanie metryk systemu operacyjnego gościa do magazynu danych usługi Azure Monitor na maszynie wirtualnej Windows (wersja klasyczna)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: cb803450f7765ae62292ff3afb7f32209b437f78
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978933"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Wysyłanie metryk systemu operacyjnego gościa do magazynu danych usługi Azure Monitor na maszynie wirtualnej Windows (wersja klasyczna)

Usługi Azure Monitor [rozszerzenia diagnostyki Azure Windows](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (WAD) umożliwia zbieranie metryk i dzienników uruchamiania systemu operacyjnego gościa (systemu operacyjnego gościa) jako część klastra maszyny wirtualnej, usługa w chmurze lub usługi Service Fabric. Rozszerzenie może wysyłać telemetrię do wielu różnych lokalizacjach, wymienione w artykule wcześniej połączona.

W tym artykule opisano proces metryki wydajności systemu operacyjnego gościa wysyłania dla Windows maszyna wirtualna (klasyczna) w magazynie metryk usługi Azure Monitor. Począwszy od wersji 1.11 WAD, można napisać metryki bezpośrednio do sklepu metryk usługi Azure Monitor, gdy już zbieranymi metrykami standardowa platforma. Przechowywania ich w tej lokalizacji umożliwia dostęp do tego samego Akcje dostępne dla platform metryk.  Akcje obejmują niemal w czasie rzeczywistym alertów, wykresy, routingu, dostęp z interfejsu API REST i nie tylko.  W przeszłości rozszerzenia WAD powstała z jednego do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor. 

Z procedurą opisaną w tym artykule działa tylko w klasycznych maszyn wirtualnych z systemem operacyjnym Windows.

## <a name="pre-requisites"></a>Wymagania wstępne

- Musi być [administratorów usługi lub administratorów współpracujących](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) w subskrypcji platformy Azure 

- Twoja subskrypcja musi być zarejestrowana w [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Musisz mieć [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) zainstalowany, możesz też [usługi Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Tworzenie klasycznej maszyny wirtualnej i konta magazynu

1. Tworzenie klasycznej maszyny Wirtualnej przy użyciu witryny Azure portal ![Utwórz maszynę Wirtualną z klasycznego](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. Podczas tworzenia tej maszyny Wirtualnej, należy wybrać opcję utworzenia nowego klasycznego konta magazynu. Możemy użyć tego konta magazynu w kolejnych krokach.

1. W witrynie Azure portal przejdź do bloku zasobów konta magazynu, a następnie wybierz **klucze** i zanotuj nazwę konta magazynu i klucza konta magazynu. Potrzebujesz tych kluczy w kolejnych krokach ![kluczy dostępu do magazynu](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Tworzenie jednostki usługi

Tworzenie jednostki usługi w dzierżawie usługi Azure Active Directory, korzystając z instrukcji w [utworzyć nazwę główną usługi](../azure-resource-manager/resource-group-create-service-principal-portal.md). Należy pamiętać, że podczas przechodzenia przez ten proces: 
- Utwórz nowy wpis tajny klienta dla tej aplikacji  
- Zapisz klucz i identyfikator klienta do użycia w kolejnych krokach.

Nadaj tej aplikacji "Monitorowanie metryk Publisher" uprawnień do zasobów, które chcesz emitują metryki względem. Możesz użyć grupy zasobów lub całej subskrypcji.  

> [!NOTE]
> Rozszerzenie diagnostyki użyje nazwy głównej usługi do uwierzytelniania względem usługi Azure Monitor i emitują metryki dla klasycznej maszyny Wirtualnej.

## <a name="author-diagnostics-extension-configuration"></a>Konfiguracji rozszerzenia diagnostyki autora

1. Przygotuj plik konfiguracji rozszerzenia diagnostyki WAD. Ten plik decyduje o tym, które dzienniki i liczniki wydajności rozszerzenie diagnostyki należy zbierać klasycznej maszyny wirtualnej. Poniżej przedstawiono przykład.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
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
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
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
1. W sekcji "SinksConfig" w pliku diagnostyki Definiowanie nowych ujścia usługi Azure Monitor:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. W sekcji w pliku konfiguracji, w którym znajduje się lista liczników wydajności, które mają być zbierane należy kierować liczników wydajności do usługi Azure Monitor Sink "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. W konfiguracji prywatnej zdefiniować konto usługi Azure Monitor i dodawanie informacji o jednostce usługi na potrzeby emisji metryki.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Zapisz ten plik lokalnie.

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>Wdrażanie rozszerzenia diagnostyki usługi w chmurze

1. Uruchom program PowerShell i zaloguj się

    ```powershell
    Login-AzureRmAccount
    ```

1. Rozpocznij od kontekstu do klasycznej maszyny Wirtualnej

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Ustaw kontekst konta klasycznego magazynu, które zostało utworzone z maszyną Wirtualną.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Ustaw ścieżkę do pliku diagnostyki do zmiennej za pomocą poniższego polecenia.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Przygotowywanie aktualizacji do klasycznej maszyny Wirtualnej przy użyciu pliku diagnostyki za pomocą ujścia usługi Azure Monitor skonfigurowane

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Wdrażanie aktualizacji dla maszyny wirtualnej, uruchamiając poniższe polecenie

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Jest nadal wymagane, aby podać konto magazynu w ramach instalacji rozszerzenia diagnostyki. Dzienniki i/lub liczniki wydajności w pliku konfiguracyjnym diagnostyki zostanie zapisany do podanego konta magazynu.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Wykresu metryki w witrynie Azure portal

1.  Przejdź do witryny Azure portal

1.  W menu po lewej stronie, kliknij na monitorze

1.  W bloku Monitor kliknij **metryki**
   ![Przejdź metryki](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. W zasobie listy rozwijanej wybierz klasycznej maszyny Wirtualnej

1. W przestrzeniach nazw listy rozwijanej wybierz **azure.vm.windows.guest**

1. W metryki listę rozwijaną, wybierz **wartości pamięć\zadeklarowane bajty w użyciu**
   ![wykresu metryki](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).
