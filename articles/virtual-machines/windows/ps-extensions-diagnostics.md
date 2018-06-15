---
title: Włącz diagnostykę na maszynie Wirtualnej z systemem Windows przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationcenter: ''
description: Dowiedz się, jak włączyć diagnostyki Azure na maszynie wirtualnej z systemem Windows przy użyciu programu PowerShell
author: sbtron
manager: jeconnoc
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 49c6fe69afdee6a075d9f888f8a2417455bc7e51
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918612"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Używanie programu PowerShell do uruchamiania narzędzia Diagnostyka Azure na maszynie wirtualnej systemu Windows
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Diagnostyka Azure jest możliwość w ramach platformy Azure, która umożliwia zbieranie danych diagnostycznych na wdrożonej aplikacji. Rozszerzenie Diagnostyka służy do zbierania danych diagnostycznych, takich jak dzienniki aplikacji lub liczniki wydajności z maszyny wirtualnej platformy Azure (VM) z systemem Windows. W tym artykule opisano sposób użycia środowiska Windows PowerShell można włączyć rozszerzenia diagnostyki dla maszyny Wirtualnej. Zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) wymagań wstępnych wymagane dla tego artykułu.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Włączanie rozszerzenia diagnostyki, jeśli używasz modelu wdrażania usługi Resource Manager
Podczas tworzenia maszyny Wirtualnej systemu Windows za pośrednictwem modelu wdrażania usługi Azure Resource Manager przez dodanie Konfiguracja rozszerzenia do szablonu usługi Resource Manager, można włączyć rozszerzenia diagnostyki. Zobacz [Utwórz maszynę wirtualną systemu Windows z monitorowania i diagnostyki za pomocą szablonu usługi Azure Resource Manager](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby włączyć rozszerzenie diagnostyki na istniejącej maszyny Wirtualnej, który został utworzony za pośrednictwem modelu wdrażania usługi Resource Manager, można użyć [AzureRMVMDiagnosticsExtension zestaw](/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension) polecenia cmdlet programu PowerShell, jak pokazano poniżej.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* to ścieżka do pliku, który zawiera konfigurację diagnostyki w formacie XML, zgodnie z opisem w [próbki](#sample-diagnostics-configuration) poniżej.  

Jeśli plik konfiguracji diagnostyki Określa **StorageAccount** element z nazwą konta magazynu, a następnie *AzureRMVMDiagnosticsExtension zestaw* skryptu automatycznie ustawi rozszerzenia diagnostyki na wysyłanie danych diagnostycznych do tego konta magazynu. Aby to zrobić konta magazynu musi być w tej samej subskrypcji co maszyny Wirtualnej.

Jeśli nie **StorageAccount** został określony w konfiguracji diagnostyki muszą podawać *StorageAccountName* parametr w poleceniu cmdlet. Jeśli *StorageAccountName* określony parametr, a następnie polecenia cmdlet będzie zawsze używać konta magazynu, które jest określone w parametrze i nie ten, który jest określony w pliku konfiguracji diagnostyki.

Jeśli konto magazynu diagnostyki jest w innej subskrypcji z maszyny Wirtualnej, a następnie jawnie przekazywane w *StorageAccountName* i *StorageAccountKey* parametry polecenia cmdlet. *StorageAccountKey* parametru nie jest wymagana, gdy konto magazynu diagnostyki jest w tej samej subskrypcji, ponieważ polecenie cmdlet można automatycznie zapytania i ustaw wartość klucza podczas włączania rozszerzenia diagnostyki. Jednak jeśli konto magazynu diagnostyki jest w innej subskrypcji, to polecenie cmdlet nie może być może automatycznie pobrać klucz i musisz jawnie określić klucz przy użyciu *StorageAccountKey* parametru.  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Po włączeniu rozszerzenia diagnostyki na maszynie Wirtualnej można uzyskać bieżące ustawienia za pomocą [Get-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/get-azurermvmdiagnosticsextension) polecenia cmdlet.

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Polecenie cmdlet zwraca *PublicSettings*, który zawiera konfigurację diagnostyki. Istnieją dwa rodzaje konfiguracja jest obsługiwana, WadCfg i xmlCfg. WadCfg jest konfiguracji JSON, a xmlCfg jest konfiguracji XML w formacie zakodowanym w formacie Base64. Aby odczytać plik XML, należy go zdekodować.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[AzureRMVmDiagnosticsExtension Usuń](/powershell/module/azurerm.compute/remove-azurermvmdiagnosticsextension) polecenia cmdlet można używać do usuwania diagnostyki rozszerzenia z maszyny Wirtualnej.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Włączanie rozszerzenia diagnostyki, korzystając z klasycznym modelu wdrażania
Można użyć [AzureVMDiagnosticsExtension zestaw](/powershell/module/azure/set-azurevmdiagnosticsextension) polecenia cmdlet, aby włączyć rozszerzenie diagnostyki na maszynie Wirtualnej, który utworzono przy użyciu klasycznego modelu wdrażania. Poniższy przykład przedstawia sposób tworzenia nowej maszyny Wirtualnej za pośrednictwem klasycznego modelu wdrażania z rozszerzeniem diagnostyki włączone.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Aby włączyć rozszerzenie diagnostyki na istniejącej maszynie Wirtualnej, który został utworzony w klasycznym modelu wdrożenia, należy najpierw użyć [Get-AzureVM](/powershell/module/azure/get-azurevm) polecenia cmdlet można pobrać konfiguracji maszyny Wirtualnej. Następnie zaktualizuj konfigurację maszyny Wirtualnej, aby uwzględnić rozszerzenia diagnostyki za pomocą [AzureVMDiagnosticsExtension zestaw](/powershell/module/azure/set-azurevmdiagnosticsextension) polecenia cmdlet. Na koniec zastosowania zaktualizowanej konfiguracji do maszyny Wirtualnej za pomocą [AzureVM aktualizacji](/powershell/module/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Przykładowa konfiguracja diagnostyki
Następujący kod XML może służyć dla publicznej konfiguracji diagnostyki ze skryptami przedstawionymi powyżej. Ta przykładowa konfiguracja przekaże różnych liczniki wydajności konto magazynu diagnostyki, wraz z błędów z aplikacji, zabezpieczeń i kanały systemu w dzienniku zdarzeń systemu Windows oraz wszelkie błędy z dzienników diagnostycznych infrastruktury.

Konfiguracja musi zostać zaktualizowany do są następujące:

* *ResourceID* atrybutu **metryki** element musi zostać zaktualizowany o identyfikatorze zasobu dla maszyny Wirtualnej.
  
  * Identyfikator zasobu może być skonstruowany przy użyciu następującego wzorca: "/ subscriptions / {*identyfikator subskrypcji dla subskrypcji z maszyną Wirtualną*} /resourceGroups/ {*nazwa grupa zasobów dla maszyny Wirtualnej*} / providers/Microsoft.Compute/virtualMachines/ {*nazwę maszyny Wirtualnej*}".
  * Na przykład jeśli identyfikator subskrypcji dla subskrypcji, w którym jest uruchomiona maszyna wirtualna jest **11111111-1111-1111-1111-111111111111**, nazwa grupy zasobów dla grupy zasobów jest **MyResourceGroup**, i Nazwa maszyny Wirtualnej jest **MyWindowsVM**, następnie wartość *resourceID* będzie:
    
      ```
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Aby uzyskać więcej informacji na temat metryki są generowane na podstawie konfiguracji liczników i metryki wydajności, zobacz [diagnostyki Azure metryki tabeli w magazynie](extensions-diagnostics-template.md#wadmetrics-tables-in-storage).
* **StorageAccount** element musi zostać zaktualizowany o nazwie konto magazynu diagnostyki.
  
    ```
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

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać dodatkowe wskazówki na temat używania możliwości diagnostyki Azure i innych technik rozwiązywania problemów, zobacz [Włączanie diagnostyki w usług Azure Cloud Services i maszyn wirtualnych](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schemat konfiguracji diagnostyki](https://msdn.microsoft.com/library/azure/mt634524.aspx) opisano różne opcje konfiguracji XML rozszerzenia diagnostyki.

