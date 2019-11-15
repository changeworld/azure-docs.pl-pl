---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 63045bf1b836215b00b9b7c1b46dd208152fa772
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74101299"
---
Usługa Azure Ultra disks oferuje wysoką przepływność, dużą liczbę IOPS i spójny magazyn dyskowy o małym opóźnieniu dla maszyn wirtualnych IaaS platformy Azure. Ta nowa oferta zapewnia najwyższą wydajność linii na tych samych poziomach dostępności co istniejące dyski. Jedną z głównych zalet funkcji Ultra disks jest możliwość dynamicznego zmieniania wydajności dysków SSD wraz z obciążeniami bez konieczności ponownego uruchamiania maszyn wirtualnych. Ultra dyski są odpowiednie dla obciążeń intensywnie korzystających z danych, takich jak SAP HANA, baz danych najwyższej warstwy i obciążeń intensywnie korzystających z transakcji.

## <a name="ga-scope-and-limitations"></a>Zakres i ograniczenia dotyczące GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Określanie rozmiaru maszyny wirtualnej i dostępności regionów

Aby korzystać z Ultra disks, należy określić, w której strefie dostępności znajduje się. Nie każdy region obsługuje wszystkie rozmiary maszyn wirtualnych za pomocą Ultra Disks. Aby określić, czy rozmiar regionu, strefy i maszyny wirtualnej obsługuje Ultra disks, Uruchom jedno z następujących poleceń, pamiętaj, aby najpierw zastąpić wartości **regionu**, **vmSize**i **subskrypcji** :

Interfejs

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

Program PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Odpowiedź będzie wyglądać podobnie do poniższego formularza, gdzie X jest strefą używaną do wdrożenia w wybranym regionie. Wartość X może być 1, 2 lub 3.

Zachowywanie wartości **strefy** reprezentuje strefę dostępności i będzie potrzebna do wdrożenia Ultra Disk.

|ResourceType  |Name (Nazwa)  |Lokalizacja  |Strefy  |Ograniczenie  |Możliwości  |Wartość  |
|---------|---------|---------|---------|---------|---------|---------|
|dysku     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Jeśli nie ma odpowiedzi z polecenia, wybrany rozmiar maszyny wirtualnej nie jest obsługiwany w przypadku Ultra dysków w wybranym regionie.

Teraz, gdy znasz strefę do wdrożenia, wykonaj kroki wdrażania opisane w tym artykule, aby wdrożyć maszynę wirtualną z dołączonym dyskiem Ultra lub dołączyć dysk Ultra do istniejącej maszyny wirtualnej.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Wdróż dysk Ultra przy użyciu Azure Resource Manager

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Aby zapoznać się z listą obsługiwanych rozmiarów maszyn wirtualnych, zobacz sekcję dotyczącą [zakresu i ograniczeń](#ga-scope-and-limitations) .

Jeśli chcesz utworzyć maszynę wirtualną z wieloma dyskami, zapoznaj się z przykładem [Tworzenie maszyny wirtualnej z wieloma dyskami](https://aka.ms/ultradiskArmTemplate).

Jeśli zamierzasz użyć własnego szablonu, upewnij się, że **apiVersion** dla `Microsoft.Compute/virtualMachines` i `Microsoft.Compute/Disks` są ustawione jako `2018-06-01` (lub nowsze).

Ustaw jednostkę SKU dysku na **UltraSSD_LRS**, a następnie ustaw wartość dyskową, liczbę operacji we/wy na sekundę, strefę dostępności i przepływność w MB/s, aby utworzyć dysk o najwyższej wydajności.

Po aprowizacji maszyny wirtualnej można podzielić na partycje i sformatować dyski danych i skonfigurować je do obciążeń.

## <a name="deploy-an-ultra-disk-using-cli"></a>Wdrażanie Ultra Disk przy użyciu interfejsu wiersza polecenia

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Zapoznaj się z sekcją " [ga Scope and ograniczenia](#ga-scope-and-limitations) ", aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych.

Aby dołączyć dysk Ultra, należy utworzyć maszynę wirtualną, która może korzystać z usługi Ultra Disks.

Zastąp lub ustaw **$VMName**, **$rgName**, **$diskname**, **$Location**, **$Password**, **$User** zmienne z własnymi wartościami. Ustaw **$Zone** na wartość strefy dostępności uzyskaną od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć maszynę wirtualną o niezwykle włączonej:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Tworzenie Ultra Disk przy użyciu interfejsu wiersza polecenia

Teraz, gdy masz maszynę wirtualną, która może dołączać Ultra disks, możesz utworzyć i dołączyć do niej dysk.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Dołączanie dysku Ultra do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może korzystać z usługi Ultra disks, można użyć funkcji Ultra disks bez konieczności tworzenia nowej maszyny wirtualnej.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Dostosowywanie wydajności Ultra Disk przy użyciu interfejsu wiersza polecenia

Funkcja Ultra disks oferuje unikatową funkcję umożliwiającą dostosowanie ich wydajności, następujące polecenie przedstawia sposób korzystania z tej funkcji:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Wdrażanie programu Ultra Disk przy użyciu programu PowerShell

Najpierw Ustal rozmiar maszyny wirtualnej do wdrożenia. Zapoznaj się z sekcją " [ga Scope and ograniczenia](#ga-scope-and-limitations) ", aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych.

Aby korzystać z usługi Ultra disks, należy utworzyć maszynę wirtualną, która może korzystać z usługi Ultra Disks. Zastąp lub Ustaw zmienne **$resourcegroup** i **$vmName** własnymi wartościami. Ustaw **$Zone** na wartość strefy dostępności uzyskaną od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie [New-AzVm](/powershell/module/az.compute/new-azvm) , aby utworzyć maszynę wirtualną z funkcją Ultra Enabled:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>Tworzenie Ultra Disk przy użyciu programu PowerShell

Teraz, gdy masz maszynę wirtualną, która może korzystać z usługi Ultra disks, możesz utworzyć i dołączyć do niej dysk:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Dołączanie dysku Ultra do maszyny wirtualnej przy użyciu programu PowerShell

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może korzystać z usługi Ultra disks, można użyć funkcji Ultra disks bez konieczności tworzenia nowej maszyny wirtualnej.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Dostosowywanie wydajności Ultra Disk przy użyciu programu PowerShell

Funkcja Ultra disks ma unikatową funkcję, która pozwala na dostosowanie ich wydajności, ale następujące polecenie umożliwia dostosowanie wydajności bez konieczności odłączania dysku:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz wypróbować nowy typ dysku, [Zażądaj dostępu do tej ankiety](https://aka.ms/UltraDiskSignup).
