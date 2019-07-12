---
title: Włączanie diagnostyki na maszyny Wirtualnej z systemem Windows przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationcenter: ''
description: Dowiedz się, jak włączyć diagnostykę platformy Azure na maszynie wirtualnej z systemem Windows przy użyciu programu PowerShell
author: sbtron
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 19d30d207e67e1dfd8cdec2fe9951c763a921a0e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706069"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Używanie programu PowerShell do uruchamiania narzędzia Diagnostyka Azure na maszynie wirtualnej systemu Windows

Narzędzie diagnostyczne systemu Azure jest funkcja w systemie Azure umożliwia zbieranie danych diagnostycznych na rozmieszczonej aplikacji. Rozszerzenie diagnostyki umożliwia zbieranie danych diagnostycznych, takich jak dzienniki aplikacji lub liczników wydajności z maszyn wirtualnych (VM) z systemem Windows. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Włączanie rozszerzenia diagnostyki, jeśli używasz modelu wdrażania usługi Resource Manager
Podczas tworzenia maszyny Wirtualnej z systemem Windows za pomocą modelu wdrażania usługi Azure Resource Manager, dodając konfiguracji rozszerzenia do szablonu usługi Resource Manager, można włączyć rozszerzenie diagnostyki. Zobacz [Utwórz maszynę wirtualną Windows z funkcjami monitorowania i diagnostyki przy użyciu szablonu usługi Azure Resource Manager](diagnostics-template.md).

Aby włączyć rozszerzenie diagnostyki w istniejącej maszyny Wirtualnej, który został utworzony za pomocą modelu wdrażania usługi Resource Manager, można użyć [AzVMDiagnosticsExtension zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) polecenia cmdlet programu PowerShell, jak pokazano poniżej.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* jest ścieżka do pliku, który zawiera konfigurację diagnostyki w formacie XML, zgodnie z opisem w [przykładowe](#sample-diagnostics-configuration) poniżej.  

Jeśli plik konfiguracji diagnostyki nie określa **StorageAccount** element nazwą konta magazynu, a następnie *AzVMDiagnosticsExtension zestaw* skryptu automatycznie ustawi diagnostyki Rozszerzenie do wysyłania danych diagnostycznych do tego konta magazynu. Aby to działało konto magazynu musi znajdować się w tej samej subskrypcji co maszyna wirtualna.

Jeśli nie **StorageAccount** została określona w konfiguracji diagnostyki, wówczas należy przekazać *StorageAccountName* parametru do polecenia cmdlet. Jeśli *StorageAccountName* parametr jest określony, a następnie polecenie cmdlet zawsze będzie korzystać z konta magazynu, który jest określony w parametrze i nie ten, który jest określony w pliku konfiguracji diagnostyki.

Jeśli konto magazynu diagnostyki jest w innej subskrypcji z maszyny Wirtualnej, a następnie musisz przekazać jawnie *StorageAccountName* i *StorageAccountKey* parametry do polecenia cmdlet. *StorageAccountKey* parametr nie jest potrzebny, gdy konto magazynu diagnostyki jest w tej samej subskrypcji, jak polecenie cmdlet automatycznie wykonywać zapytania i ustaw wartość klucza podczas włączania rozszerzenia diagnostyki. Jeśli jednak konto magazynu diagnostyki w innej subskrypcji, to polecenie cmdlet może nie móc automatycznie uzyskać klucz i należy jawnie określić klucz przy użyciu *StorageAccountKey* parametru.  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Po włączeniu rozszerzenia diagnostyki na maszynie Wirtualnej można uzyskać bieżące ustawienia za pomocą [Get AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension) polecenia cmdlet.

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Polecenie cmdlet zwraca *PublicSettings*, który zawiera konfiguracji diagnostyki. Istnieją dwa rodzaje konfiguracja jest obsługiwana, WadCfg i xmlCfg. WadCfg jest Konfiguracja JSON, a xmlCfg jest plik XML konfiguracji w formacie algorytmem Base64. Do odczytywania pliku XML, należy go zdekodować.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[AzVmDiagnosticsExtension Usuń](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) polecenia cmdlet można usunąć rozszerzenie diagnostyki z maszyny Wirtualnej.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Włączanie rozszerzenia diagnostyki, jeśli używasz klasycznego modelu wdrażania
Możesz użyć [AzureVMDiagnosticsExtension zestaw](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) polecenia cmdlet, aby włączyć rozszerzenie diagnostyki na maszynie Wirtualnej, który utworzono przy użyciu klasycznego modelu wdrażania. Poniższy przykład pokazuje, jak utworzyć nową maszynę Wirtualną za pośrednictwem klasycznego modelu wdrażania przy użyciu rozszerzenia diagnostyki włączone.

    $VM = New-AzVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

Aby włączyć rozszerzenie diagnostyki w istniejącej maszyny Wirtualnej, który został utworzony za pomocą klasycznego modelu wdrażania, należy najpierw użyć [Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) polecenie cmdlet umożliwiające pobranie konfiguracji maszyny Wirtualnej. Następnie zaktualizuj konfigurację maszyny Wirtualnej, aby dołączyć rozszerzenie diagnostyki za pomocą [AzureVMDiagnosticsExtension zestaw](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) polecenia cmdlet. Na koniec zastosowania zaktualizowanej konfiguracji do maszyny Wirtualnej przy użyciu [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Przykładowa konfiguracja diagnostyki
Następujący kod XML może służyć do konfiguracji publicznej diagnostyki za pomocą skryptów powyżej. Tej przykładowej konfiguracji transferować różne liczniki wydajności do konta magazynu diagnostyki oraz błędy z aplikacji, zabezpieczeń i kanały systemu Windows dzienniki zdarzeń i ewentualne błędy z dzienniki infrastruktury diagnostycznej.

Konfiguracja musi zostać zaktualizowany i zawiera następujące czynności:

* *ResourceID* atrybutu **metryki** element musi zostać zaktualizowany o identyfikatorze zasobu dla maszyny Wirtualnej.
  
  * Identyfikator zasobu można skonstruować przy użyciu następującego wzorca: "/ subscriptions / {*identyfikator subskrypcji dla subskrypcji z maszyną Wirtualną*} /resourceGroups/ {*nazwę grupy zasobów dla maszyny Wirtualnej*} / providers/Microsoft.Compute/virtualMachines/ {*Nazwa maszyny Wirtualnej*} ".
  * Na przykład, jeśli subskrypcji IDENTYFIKATORA subskrypcji, w którym maszyna wirtualna jest uruchomiona jest **11111111-1111-1111-1111-111111111111**, nazwa grupy zasobów dla grupy zasobów jest **MyResourceGroup**i Nazwa maszyny Wirtualnej jest **MyWindowsVM**, następnie wartość *resourceID* będzie:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Aby uzyskać więcej informacji na temat metryki są generowane na podstawie konfiguracji liczników i metryki wydajności, zobacz [tabeli metryk usługi Azure Diagnostics w magazynie](diagnostics-template.md#wadmetrics-tables-in-storage).
* **StorageAccount** element musi zostać zaktualizowany o nazwie konto magazynu diagnostyki.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać dodatkowe wskazówki na temat korzystania z możliwości diagnostyki platformy Azure i innych technik rozwiązywania problemów, zobacz [Włączanie diagnostyki w usługach Azure Cloud Services i Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schemat usługi Diagnostyka konfiguracje](https://msdn.microsoft.com/library/azure/mt634524.aspx) opisano różne opcje konfiguracji rozszerzenia diagnostyki XML.

