---
title: Włącz diagnostykę na maszynie Wirtualnej systemu Windows za pomocą programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
description: Dowiedz się, jak włączyć diagnostykę platformy Azure na maszynie wirtualnej z systemem Windows za pomocą programu PowerShell
author: sbtron
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 55afeb52323ead7db8be7e8fd1dabc880328e888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921542"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Włącz diagnostykę platformy Azure na maszynie wirtualnej z systemem Windows za pomocą programu PowerShell

Diagnostyka azure to możliwość w ramach platformy Azure, która umożliwia zbieranie danych diagnostycznych w wdrożonej aplikacji. Rozszerzenie diagnostyki służy do zbierania danych diagnostycznych, takich jak dzienniki aplikacji lub liczniki wydajności z maszyny wirtualnej platformy Azure (VM), która jest uruchomiona w systemie Windows. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Włącz rozszerzenie diagnostyki, jeśli używasz modelu wdrażania Menedżera zasobów
Rozszerzenie diagnostyki można włączyć podczas tworzenia maszyny Wirtualnej systemu Windows za pośrednictwem modelu wdrażania usługi Azure Resource Manager, dodając konfigurację rozszerzenia do szablonu Menedżera zasobów. Zobacz [Tworzenie maszyny wirtualnej systemu Windows z monitorowaniem i diagnostyką przy użyciu szablonu Usługi Azure Resource Manager](diagnostics-template.md).

Aby włączyć rozszerzenie diagnostyki na istniejącej maszynie wirtualnej, która została utworzona za pośrednictwem modelu wdrażania Menedżera zasobów, można użyć polecenia cmdlet Programu PowerShell [Set-AzVMDiagnosticsExtension,](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) jak pokazano poniżej.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* jest ścieżką do pliku, który zawiera konfigurację diagnostyki w XML, jak opisano w [poniższym przykładzie.](#sample-diagnostics-configuration)  

Jeśli plik konfiguracji diagnostyki określa **element StorageAccount** o nazwie konta magazynu, skrypt *Set-AzVMDiagnosticsExtension* automatycznie ustawi rozszerzenie diagnostyki w celu wysyłania danych diagnostycznych do tego konta magazynu. Aby to działało, konto magazynu musi znajdować się w tej samej subskrypcji co maszyna wirtualna.

Jeśli w konfiguracji diagnostyki nie określono konta **StorageAccount,** należy przekazać parametr *StorageAccountName* do polecenia cmdlet. Jeśli *parametr StorageAccountName* jest określony, polecenie cmdlet zawsze będzie używać konta magazynu określonego w parametrze, a nie tego, które jest określone w pliku konfiguracji diagnostyki.

Jeśli konto magazynu diagnostyki znajduje się w innej subskrypcji niż maszyna wirtualna, należy jawnie przekazać w *StorageAccountName* i *StorageAccountKey* parametry do polecenia cmdlet. *Parametr StorageAccountKey* nie jest potrzebny, gdy konto magazynu diagnostyki znajduje się w tej samej subskrypcji, ponieważ polecenie cmdlet może automatycznie wysyłać zapytania i ustawiać wartość klucza podczas włączania rozszerzenia diagnostyki. Jednak jeśli konto magazynu diagnostyki jest w innej subskrypcji, a następnie polecenie cmdlet może nie być w stanie uzyskać klucz automatycznie i trzeba jawnie określić klucz za pośrednictwem *StorageAccountKey* parametru.  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Po włączeniu rozszerzenia diagnostyki na maszynie Wirtualnej można uzyskać bieżące ustawienia za pomocą polecenia cmdlet [Get-AzVmDiagnosticsExtension.](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension)

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Polecenie cmdlet zwraca *publicsettings*, który zawiera konfigurację diagnostyki. Istnieją dwa rodzaje konfiguracji obsługiwane, WadCfg i xmlCfg. WadCfg jest konfiguracją JSON, a xmlCfg jest konfiguracją XML w formacie zakodowanym w formacie Base64. Aby odczytać kod XML, musisz go zdekodować.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

Polecenie cmdlet [Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) może służyć do usuwania rozszerzenia diagnostyki z maszyny Wirtualnej.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Włącz rozszerzenie diagnostyki, jeśli używasz klasycznego modelu wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Można użyć polecenia cmdlet [Set-AzureVMDiagnosticsExtension,](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) aby włączyć rozszerzenie diagnostyki na maszynie Wirtualnej, które można utworzyć za pośrednictwem klasycznego modelu wdrażania. W poniższym przykładzie pokazano, jak utworzyć nową maszynę wirtualną za pośrednictwem klasycznego modelu wdrażania z włączonym rozszerzeniem diagnostyki.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

Aby włączyć rozszerzenie diagnostyki na istniejącej maszynie wirtualnej, która została utworzona za pośrednictwem klasycznego modelu wdrażania, najpierw użyj polecenia cmdlet [Get-AzureVM,](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) aby uzyskać konfigurację maszyny Wirtualnej. Następnie zaktualizuj konfigurację maszyny Wirtualnej, aby uwzględnić rozszerzenie diagnostyki przy użyciu polecenia cmdlet [Set-AzureVMDiagnosticsExtension.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) Na koniec zastosuj zaktualizowaną konfigurację do maszyny Wirtualnej przy użyciu [update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Konfiguracja diagnostyki próbek
Poniższy kod XML może służyć do diagnostyki publicznej konfiguracji z powyższych skryptów. Ta przykładowa konfiguracja przeniesie różne liczniki wydajności do konta magazynu diagnostyki, wraz z błędami z aplikacji, zabezpieczeń i kanałów systemowych w dziennikach zdarzeń systemu Windows i wszelkimi błędami z dzienników infrastruktury diagnostycznej.

Konfiguracja musi zostać zaktualizowana w celu uwzględnienia następujących elementów:

* Atrybut *resourceID* elementu **Metryki** musi zostać zaktualizowany o identyfikator zasobu dla maszyny Wirtualnej.
  
  * Identyfikator zasobu można skonstruować przy użyciu następującego wzorca: "/subscriptions/{*subscription id for the subscription with the VM*}/resourceGroups/{*Nazwa grupy zasobów maszyny Wirtualnej*}/providers/Microsoft.Compute/virtualMachines/{ Nazwa maszyny*Wirtualnej*}".
  * Na przykład jeśli identyfikator subskrypcji dla subskrypcji, w której jest uruchomiona maszyna wirtualna, to **1111111-1111-1111-1111-11111111111111111111,** nazwa grupy zasobów dla grupy zasobów to **MyResourceGroup**, a nazwa maszyny Wirtualnej to **MyWindowsVM**, wówczas wartość *identyfikatora zasobów* będzie:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Aby uzyskać więcej informacji na temat sposobu generowania metryk na podstawie liczników wydajności i konfiguracji metryk, zobacz [tabela metryk diagnostyki platformy Azure w magazynie](diagnostics-template.md#wadmetrics-tables-in-storage).
* **Element StorageAccount** musi zostać zaktualizowany o nazwę konta magazynu diagnostyki.
  
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
* Aby uzyskać dodatkowe wskazówki dotyczące korzystania z funkcji diagnostyki platformy Azure i innych technik rozwiązywania problemów, zobacz [Włączanie diagnostyki w usługach w chmurze azure i maszynach wirtualnych.](../../cloud-services/cloud-services-dotnet-diagnostics.md)
* [Schemat konfiguracji diagnostyki](https://msdn.microsoft.com/library/azure/mt634524.aspx) wyjaśnia różne opcje konfiguracji XML dla rozszerzenia diagnostyki.

