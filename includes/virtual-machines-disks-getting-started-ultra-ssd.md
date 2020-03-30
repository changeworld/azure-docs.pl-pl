---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d081a8cec088f4743bd0dc7d3cc37a9fade61d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116966"
---
Ultra dyski platformy Azure oferują wysoką przepływność, wysokie usługi We/Wy i spójne magazyny dysków o małych opóźnieniach dla maszyn wirtualnych usługi Azure IaaS (VM). Ta nowa oferta zapewnia najwyższą wydajność linii na tym samym poziomie dostępności, co nasze istniejące oferty dysków. Jedną z głównych zalet dysków ultra jest możliwość dynamicznej zmiany wydajności dysku SSD wraz z obciążeniami bez konieczności ponownego uruchamiania maszyn wirtualnych. Dyski w warstwie Ultra to rozwiązanie odpowiednie w przypadku obciążeń intensywnie korzystających z danych, takich jak platforma SAP HANA, bazy danych górnej warstwy i obciążenia z dużą liczbą transakcji.

## <a name="ga-scope-and-limitations"></a>Zakres i ograniczenia ga

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Określanie rozmiaru maszyny Wirtualnej i dostępności regionu

Aby wykorzystać dyski ultra, musisz określić, w której strefie dostępności się znajdujesz. Nie każdy region obsługuje każdy rozmiar maszyny Wirtualnej za pomocą dysków ultra. Aby ustalić, czy rozmiar regionu, strefy i maszyny Wirtualnej obsługuje dyski ultra, uruchom jeden z następujących poleceń, najpierw należy zastąpić wartości **regionu**, **vmSize**i **subskrypcji:**

Cli:

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

Program PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Odpowiedź będzie podobna do poniższego formularza, gdzie X jest strefą używana do wdrażania w wybranym regionie. X może być 1, 2 lub 3.

Zachowaj **zones** wartość, reprezentuje strefę dostępności i będzie jej potrzebne w celu wdrożenia ultra dysku.

|ResourceType  |Nazwa  |Lokalizacja  |Strefy  |Ograniczenie  |Możliwości  |Wartość  |
|---------|---------|---------|---------|---------|---------|---------|
|Dysków     |UltraSSD_LRS         |okręg wyborczy eastus2         |X         |         |         |         |

> [!NOTE]
> Jeśli nie było odpowiedzi z polecenia, wybrany rozmiar maszyny Wirtualnej nie jest obsługiwany z dyskami ultra w wybranym regionie.

Teraz, gdy wiesz, w której strefie należy wdrożyć, wykonaj kroki wdrażania opisane w tym artykule, aby wdrożyć maszynę wirtualną z dołączonym dyskiem ultra lub dołączyć dysk ultra do istniejącej maszyny Wirtualnej.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Wdrażanie ultradysu przy użyciu usługi Azure Resource Manager

Najpierw należy określić rozmiar maszyny Wirtualnej do wdrożenia. Aby uzyskać listę obsługiwanych rozmiarów maszyn wirtualnych, zobacz zakres [ga i ograniczenia](#ga-scope-and-limitations) sekcji.

Jeśli chcesz utworzyć maszynę wirtualną z wieloma dyskami ultra, zapoznaj się [z przykładową próbką Utwórz maszynę wirtualną z wieloma dyskami ultra.](https://aka.ms/ultradiskArmTemplate)

Jeśli zamierzasz użyć własnego szablonu, upewnij `Microsoft.Compute/virtualMachines` się, że `2018-06-01` **apiVersion** dla i `Microsoft.Compute/Disks` jest ustawiony jako (lub nowszych).

Ustaw sku dysku **na UltraSSD_LRS**, a następnie ustaw pojemność dysku, IOPS, strefę dostępności i przepływność w MBps, aby utworzyć dysk ultra.

Po zainicjowaniu obsługi administracyjnej maszyny Wirtualnej można podzielić i sformatować dyski z danymi oraz skonfigurować je dla obciążeń.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Wdrażanie ultradysu za pomocą witryny Azure portal

W tej sekcji opisano wdrażanie maszyny wirtualnej wyposażonej w dysk ultra jako dysk danych. Zakłada się, że masz znajomość wdrażania maszyny wirtualnej, jeśli nie, zobacz nasz [szybki start: Tworzenie maszyny wirtualnej systemu Windows w witrynie Azure portal](../articles/virtual-machines/windows/quick-create-portal.md).

- Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do wdrożenia maszyny wirtualnej (VM).
- Upewnij się, że wybierz [obsługiwany rozmiar maszyny Wirtualnej i region](#ga-scope-and-limitations).
- Wybierz **strefę Dostępność** w **obszarze Opcje dostępności**.
- Wypełnij pozostałe wpisy wybranymi selekcjami.
- Wybierz pozycję **Dyski**.

![tworzenie-ultra-dysk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- W bloku Dyski wybierz pozycję **Tak,** **aby włączyć zgodność z dyskami ultradysłowymi**.
- Wybierz **pozycję Utwórz i dołącz nowy dysk,** aby dołączyć teraz dysk ultra.

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- W obszarze **Utwórz nowy blok dysku** wprowadź nazwę, a następnie wybierz pozycję **Zmień rozmiar**.
- Zmień **typ konta** na **dysk ultradys**.
- Zmień wartości **niestandardowego rozmiaru dysku (GiB),** **IOPS dysku**i **przepływności dysku** na wybrane.
- Wybierz **przycisk OK** w obu ostrzach.
- Kontynuuj wdrażanie maszyny Wirtualnej, będzie taka sama, jak można wdrożyć inne maszyny Wirtualnej.

![tworzenie-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Dołączanie dysku ultra przy użyciu portalu Azure

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może używać dysków ultra, można korzystać z dysków ultra bez konieczności tworzenia nowej maszyny Wirtualnej. Włączając dyski ultra na istniejącej maszynie wirtualnej, a następnie dołączając je jako dyski z danymi.

- Przejdź do maszyny Wirtualnej i wybierz pozycję **Dyski**.
- Wybierz pozycję **Edit** (Edytuj).

![opcje-selektor-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Wybierz **pozycję Tak,** **aby włączyć zgodność z dyskami ultradyskowym**.

![ultra-opcje-tak-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Wybierz **pozycję Zapisz**.
- Wybierz **pozycję Dodaj dysk danych,** a następnie w menu rozwijanym dla opcji **Nazwa** wybierz **pozycję Utwórz dysk**.

![tworzenie i dołączanie-nowy-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Wpisz nazwę nowego dysku, a następnie wybierz pozycję **Zmień rozmiar**.
- Zmień **typ konta** na **dysk ultradys**.
- Zmień wartości **niestandardowego rozmiaru dysku (GiB),** **IOPS dysku**i **przepływności dysku** na wybrane.
- Wybierz **przycisk OK,** a następnie wybierz pozycję **Utwórz**.

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Po powrocie do bloku dysku wybierz pozycję **Zapisz**.

![zapisywanie i podłączanie-nowy-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Dostosowywanie wydajności dysku ultra za pomocą witryny Azure portal

Dyski ultra oferują unikalną możliwość, która pozwala dostosować ich wydajność. Można wprowadzić te zmiany z witryny Azure portal, na samych dyskach.

- Przejdź do maszyny Wirtualnej i wybierz pozycję **Dyski**.
- Wybierz dysk ultra, którego wydajność chcesz zmodyfikować.

![wybór-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Wybierz **opcję Konfiguracja,** a następnie dokonaj zmian.
- Wybierz **pozycję Zapisz**.

![konfigurowanie-ultra-dysk-wydajność-i-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Wdrażanie dysku ultra przy użyciu interfejsu wiersza polecenia

Najpierw należy określić rozmiar maszyny Wirtualnej do wdrożenia. Zobacz sekcję [zakres ga i ograniczenia](#ga-scope-and-limitations) listy obsługiwanych rozmiarów maszyn wirtualnych.

Należy utworzyć maszynę wirtualną, która może używać dysków ultra, aby dołączyć dysk ultra.

Zastąp lub ustaw **zmienne $vmname**, **$rgname**, **$diskname,** **$location**, **$password** **, $user** własnymi wartościami. Ustaw **$zone** wartości strefy dostępności, którą otrzymałeś od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć maszynę wirtualną z obsługą ultra:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Tworzenie dysku ultra przy użyciu interfejsu wiersza polecenia

Teraz, gdy masz maszynę wirtualną, która jest w stanie dołączyć dyski ultra, można utworzyć i dołączyć do niego ultra dysk.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Dołączanie dysku ultra do maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może używać dysków ultra, można korzystać z dysków ultra bez konieczności tworzenia nowej maszyny Wirtualnej.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Dostosowywanie wydajności dysku ultra przy użyciu interfejsu wiersza polecenia

Dyski ultra oferują unikalną funkcję, która pozwala dostosować ich wydajność, następujące polecenie przedstawia, jak korzystać z tej funkcji:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Wdrażanie ultradysu przy użyciu programu PowerShell

Najpierw należy określić rozmiar maszyny Wirtualnej do wdrożenia. Zobacz sekcję [zakres ga i ograniczenia](#ga-scope-and-limitations) listy obsługiwanych rozmiarów maszyn wirtualnych.

Aby używać dysków ultra, należy utworzyć maszynę wirtualną, która może używać dysków ultra. Zastąp lub ustaw **zmienne $resourcegroup** i **$vmName** własnymi wartościami. Ustaw **$zone** wartości strefy dostępności, którą otrzymałeś od [początku tego artykułu](#determine-vm-size-and-region-availability). Następnie uruchom następujące polecenie [New-AzVm,](/powershell/module/az.compute/new-azvm) aby utworzyć ultra włączoną maszynę wirtualną:

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

### <a name="create-an-ultra-disk-using-powershell"></a>Tworzenie dysku ultra przy użyciu programu PowerShell

Teraz, gdy masz maszynę wirtualną, która jest w stanie korzystać z dysków ultra, można utworzyć i dołączyć ultra dysk do niego:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Dołączanie dysku ultra do maszyny wirtualnej przy użyciu programu PowerShell

Alternatywnie, jeśli istniejąca maszyna wirtualna znajduje się w strefie regionu/dostępności, która może używać dysków ultra, można korzystać z dysków ultra bez konieczności tworzenia nowej maszyny Wirtualnej.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Dostosowywanie wydajności dysku ultra za pomocą programu PowerShell

Dyski ultra mają unikalną funkcję, która pozwala dostosować ich wydajność, następujące polecenie jest przykładem, który dostosowuje wydajność bez konieczności odłączania dysku:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz wypróbować nowy typ dysku [żądanie dostępu z tej ankiety](https://aka.ms/UltraDiskSignup).
