---
title: Wysyłanie klasycznych metryk maszyn wirtualnych systemu Windows do bazy danych metryk usługi Azure Monitor
description: Wysyłanie metryk systemu operacyjnego gościa do magazynu danych usługi Azure Monitor dla maszyny wirtualnej systemu Windows (klasyczna)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 65bb1a3915ece384974da12b4e7a1ad0c1e08133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655821"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Wysyłanie metryk systemu operacyjnego gościa do bazy danych metryk usługi Azure Monitor dla maszyny wirtualnej systemu Windows (klasyczna)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Rozszerzenie diagnostyki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) usługi Azure Monitor (znane jako "WAD" lub "Diagnostyka") umożliwia zbieranie metryk i dzienników z systemu operacyjnego gościa (systemu operacyjnego gościa) uruchomionego jako część maszyny wirtualnej, usługi w chmurze lub klastra sieci szkieletowej usług. Rozszerzenie może wysyłać dane telemetryczne do [wielu różnych lokalizacji.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla maszyny wirtualnej systemu Windows (klasycznej) do bazy danych metryk Usługi Azure Monitor. Począwszy od diagnostyki w wersji 1.11, można zapisywać metryki bezpośrednio w magazynie metryk usługi Azure Monitor, gdzie standardowe metryki platformy są już zbierane. 

Przechowywanie ich w tej lokalizacji umożliwia dostęp do tych samych akcji, co w przypadku metryk platformy. Akcje obejmują alerty w czasie zbliżonym do rzeczywistego, wykresy, routing, dostęp z interfejsu API REST i inne. W przeszłości rozszerzenie diagnostyki napisał do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor. 

Proces opisany w tym artykule działa tylko na klasycznych maszynach wirtualnych z systemem operacyjnym Windows.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być [administratorem usługi lub współadministratorem](../../cost-management-billing/manage/add-change-subscription-administrator.md) w ramach subskrypcji platformy Azure. 

- Subskrypcja musi być zarejestrowana w [witrynie Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musisz mieć zainstalowany [program Azure PowerShell](/powershell/azure) lub [usługa Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- Zasób maszyny Wirtualnej musi znajdować się w [regionie obsługującym metryki niestandardowe](metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Tworzenie klasycznej maszyny wirtualnej i konta magazynu

1. Utwórz klasyczną maszynę wirtualną przy użyciu witryny Azure portal.
   ![Tworzenie klasycznej maszyny Wirtualnej](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Podczas tworzenia tej maszyny Wirtualnej wybierz opcję utworzenia nowego konta magazynu klasycznego. Używamy tego konta magazynu w późniejszych krokach.

1. W witrynie Azure portal przejdź do bloku zasobów **kont magazynu.** Wybierz opcję **Klucze**i zanotuj nazwę konta magazynu i klucz konta magazynu. Te informacje są potrzebne w późniejszych krokach.
   ![Klucze dostępu do magazynu](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Utwórz zasadę usługi w dzierżawie usługi Azure Active Directory przy użyciu instrukcji w [obszarze Tworzenie jednostki usługi.](../../active-directory/develop/howto-create-service-principal-portal.md) Należy zwrócić uwagę na następujące czynności podczas przechodzenia przez ten proces: 
- Utwórz nowy klucz tajny klienta dla tej aplikacji.
- Zapisz klucz i identyfikator klienta do użycia w późniejszych krokach.

Nadaj tej aplikacji uprawnienia "Monitorowanie programu Metryk" do zasobu, dla którego chcesz emitować metryki. Można użyć grupy zasobów lub całej subskrypcji.  

> [!NOTE]
> Rozszerzenie diagnostyki używa jednostki usługi do uwierzytelniania za pomocą usługi Azure Monitor i emitować metryki dla klasycznej maszyny Wirtualnej.

## <a name="author-diagnostics-extension-configuration"></a>Konfiguracja rozszerzenia diagnostyki autora

1. Przygotuj plik konfiguracji rozszerzenia diagnostyki. Ten plik określa, które dzienniki i liczniki wydajności rozszerzenie diagnostyki powinny zbierać dla klasycznej maszyny Wirtualnej. Oto przykład:

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
1. W sekcji "SinksConfig" pliku diagnostycznego zdefiniuj nowy ujście usługi Azure Monitor w następujący sposób:

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

1. W sekcji pliku konfiguracji, w której znajduje się lista liczników wydajności do zebrania, należy kierować liczniki wydajności do ujścia usługi Azure Monitor "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. W konfiguracji prywatnej zdefiniuj konto usługi Azure Monitor. Następnie dodaj informacje o jednostki usługi do użycia do emitowania metryk.

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Wdrażanie rozszerzenia diagnostyki w usłudze w chmurze

1. Uruchom program PowerShell i zaloguj się.

    ```powershell
    Login-AzAccount
    ```

1. Zacznij od ustawienia kontekstu klasycznej maszyny Wirtualnej.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Ustaw kontekst klasycznego konta magazynu, który został utworzony przy za pomocą maszyny Wirtualnej.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Ustaw ścieżkę pliku diagnostyki na zmienną za pomocą następującego polecenia:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Przygotuj aktualizację dla klasycznej maszyny Wirtualnej z plikiem diagnostycznym, który ma skonfigurowany ujście usługi Azure Monitor.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Wdrażanie aktualizacji na maszynie wirtualnej przez uruchomienie następującego polecenia:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Nadal jest obowiązkowe, aby zapewnić konto magazynu w ramach instalacji rozszerzenia diagnostyki. Wszystkie dzienniki lub liczniki wydajności, które są określone w pliku konfiguracji diagnostyki zostaną zapisane na określonym koncie magazynu.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Wykreślanie danych w witrynie Azure portal

1.  Przejdź do witryny Azure Portal. 

1.  W menu po lewej stronie wybierz pozycję **Monitor.**

1.  Na bloku **Monitor** wybierz **pozycję Metryki**.

    ![Nawigowanie do danych](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. W menu rozwijanym zasoby wybierz klasyczną maszynę wirtualną.

1. W menu rozwijanym obszary nazw wybierz pozycję **azure.vm.windows.guest**.

1. W menu rozwijanym metryki wybierz polecenie **Pamięć\Zatwierdzone bajty w użyciu**.
   ![Kreśl metryki](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](metrics-custom-overview.md).

