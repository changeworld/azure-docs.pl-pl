---
title: Wysyłanie metryk systemu operacyjnego gościa do magazynu danych usługi Azure Monitor na maszynie wirtualnej Windows (wersja klasyczna)
description: Wysyłanie metryk systemu operacyjnego gościa do magazynu danych usługi Azure Monitor na maszynie wirtualnej Windows (wersja klasyczna)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 57212da1a8da7ee6c57faf2413b88a413df04817
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129553"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Wysyłanie metryk systemu operacyjnego gościa do magazynu danych usługi Azure Monitor na maszynie wirtualnej Windows (wersja klasyczna)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usługi Azure Monitor [rozszerzenie diagnostyki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (nazywane "WAD" lub "Diagnostyki") umożliwia zbieranie metryk i dzienników z systemu operacyjnego gościa (systemu operacyjnego gościa) uruchomiona w ramach maszyny wirtualnej, usługa w chmurze lub usługi Service Fabric klaster. Rozszerzenie może wysyłać telemetrię do [wielu różnych lokalizacjach.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryki wydajności systemu operacyjnego gościa na maszynie wirtualnej Windows (wersja klasyczna) w magazynie metryk usługi Azure Monitor. Począwszy od wersji 1.11 diagnostyki, można napisać metryki bezpośrednio do usługi Azure Monitor przechowywać metryki, którym już zbieranymi metrykami standardowa platforma. 

Przechowywania ich w tej lokalizacji umożliwia dostęp do tych samych czynności, jak w przypadku metryk platformy. Akcje obejmują niemal w czasie rzeczywistym alertów, wykresów i routing, dostęp z interfejsu API REST i nie tylko. W przeszłości rozszerzenie diagnostyki zapisano do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor. 

Proces, który jest opisany w tym artykule działa tylko w klasycznych maszyn wirtualnych z systemem operacyjnym Windows.

## <a name="prerequisites"></a>Wymagania wstępne

- Musi być [administratorów usługi lub administratorów współpracujących](../../billing/billing-add-change-azure-subscription-administrator.md) w subskrypcji platformy Azure. 

- Twoja subskrypcja musi być zarejestrowana w [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musisz mieć [programu Azure PowerShell](/powershell/azure) lub [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) zainstalowane.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Tworzenie klasycznej maszyny wirtualnej i konta magazynu

1. Tworzenie klasycznej maszyny Wirtualnej przy użyciu witryny Azure portal.
   ![Tworzenie klasycznej maszyny Wirtualnej](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Podczas tworzenia tej maszyny Wirtualnej, należy wybrać opcję utworzenia nowego klasycznego konta magazynu. Możemy użyć tego konta magazynu w kolejnych krokach.

1. W witrynie Azure portal przejdź do **kont magazynu** bloku zasobów. Wybierz **klucze**i zanotuj nazwę konta magazynu i klucza konta magazynu. Potrzebujesz tych informacji w kolejnych krokach.
   ![Klucze dostępu do magazynu](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Tworzenie jednostki usługi w dzierżawie usługi Azure Active Directory zgodnie z instrukcjami podanymi w [utworzyć nazwę główną usługi](../../active-directory/develop/howto-create-service-principal-portal.md). Należy pamiętać, że podczas przechodzenia przez ten proces: 
- Utwórz nowy wpis tajny klienta dla tej aplikacji.
- Zapisz klucz i identyfikator klienta do użycia w kolejnych krokach.

Nadaj tej aplikacji "Monitorowanie metryk Publisher" uprawnień do zasobów, które emitują metryki względem. Można użyć grupy zasobów lub całej subskrypcji.  

> [!NOTE]
> Rozszerzenie diagnostyki używa nazwy głównej usługi do uwierzytelniania względem usługi Azure Monitor i emitują metryki dla klasycznej maszyny Wirtualnej.

## <a name="author-diagnostics-extension-configuration"></a>Tworzenie konfiguracji rozszerzenia diagnostyki

1. Przygotuj plik konfiguracji rozszerzenia diagnostyki. Ten plik decyduje o tym, które dzienniki i liczniki wydajności rozszerzenie diagnostyki należy zbierać klasycznej maszyny wirtualnej. Poniżej znajduje się przykład:

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
1. W sekcji "SinksConfig" w pliku diagnostyki Zdefiniuj nowy obiekt sink usługi Azure Monitor w następujący sposób:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. W sekcji w pliku konfiguracji, w którym znajduje się lista liczników wydajności, które mają być zbierane należy kierować liczników wydajności do ujścia usługi Azure Monitor "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Prywatna konfiguracja umożliwia określenie konta usługi Azure Monitor. Następnie można dodać informacji o jednostce usługi na potrzeby emisji metryki.

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Wdrażanie rozszerzenia diagnostyki usługi w chmurze

1. Uruchom program PowerShell i zaloguj się.

    ```powershell
    Login-AzAccount
    ```

1. Rozpocznij od kontekstu dla klasycznej maszyny Wirtualnej.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Ustaw kontekst konta klasycznego magazynu, które zostało utworzone z maszyną Wirtualną.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Ustaw ścieżkę do pliku diagnostyki do zmiennej za pomocą następującego polecenia:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Przygotowywanie aktualizacji dla klasycznej maszyny Wirtualnej z plikiem diagnostyki, który ma ujścia usługi Azure Monitor skonfigurowane.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Wdrażanie aktualizacji dla maszyny wirtualnej, uruchamiając następujące polecenie:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Jest nadal wymagane, aby podać konto magazynu w ramach instalacji rozszerzenia diagnostyki. Wszystkie dzienniki lub liczniki wydajności, które są określone w pliku konfiguracji diagnostyki zostanie zapisany do podanego konta magazynu.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Wykresu metryki w witrynie Azure portal

1.  Przejdź do witryny Azure Portal. 

1.  W menu po lewej stronie wybierz **monitora.**

1.  Na **Monitor** bloku wybierz **metryki**.

    ![Przejdź metryki](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. W menu rozwijanym zasobów wybierz klasycznej maszyny Wirtualnej.

1. Wybierz z menu rozwijanego przestrzenie nazw **azure.vm.windows.guest**.

1. Wybierz z menu rozwijanego metryki **wartości pamięć\zadeklarowane bajty w użyciu**.
   ![Wykres metryk](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).

