---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 326382339e2b4aeaa488d3d7f76b7ff35f9bc620
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147778"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Włączyć i wdrożyć SSD ultra platformy Azure (wersja zapoznawcza)

Usługa Azure ultra dyski półprzewodnikowe (SSD) (wersja zapoznawcza) oferta wysokiej przepływności, wysoka operacje We/Wy i stale krótki czas Magazyn dyskowy dla maszyn wirtualnych IaaS platformy Azure (maszyny wirtualne). Ta nowa oferta zapewnia początku wydajności wiersza w poziomach dostępności jako ofert istniejących dysków. Jedną z głównych zalet ultra dysków SSD jest możliwość dynamicznie zmieniać wydajność dysków SSD, wraz z obciążeń, bez konieczności ponownego uruchamiania maszyn wirtualnych. Największa dyski SSD są odpowiednie dla obciążeń intensywnie korzystających z danych, takich jak SAP HANA, najwyższej warstwy bazy danych i transakcji duże obciążenia.

Obecnie najwyższej dyski SSD są w wersji zapoznawczej, więc użytkownik musi [rejestrowanie](https://aka.ms/UltraSSDPreviewSignUp) w wersji zapoznawczej, aby można było uzyskiwać do nich dostęp.

## <a name="determine-your-availability-zone"></a>Określić strefy dostępności

Po zatwierdzeniu, należy określić strefę dostępności, które znajdują się w, aby można było używać ultra dysków SSD. Uruchom dowolne z poniższych poleceń, aby określić, która strefa w regionie wschodnie stany USA 2 ultra dysku, aby wdrożyć:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

INTERFEJS WIERSZA POLECENIA: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

Odpowiedź będzie miała podobny do poniższego, formularza, gdzie X jest strefa będzie używana do wdrażania w regionie wschodnie stany USA 2. X może być 1, 2 lub 3.

Zachowaj **stref** wartość reprezentuje strefy dostępności i będą one potrzebne w celu wdrożenia ultra dyski SSD.

|ResourceType  |Name (Nazwa)  |Lokalizacja  |Strefy  |Ograniczenie  |Możliwości  |Wartość  |
|---------|---------|---------|---------|---------|---------|---------|
|dyski     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Jeśli nie ma odpowiedzi w poleceniu, a następnie rejestrację, aby ta funkcja jest nadal oczekujące na zatwierdzenie lub niezatwierdzone jeszcze.

Skoro już wiesz, które strefy do wdrożenia, postępuj zgodnie z instrukcjami wdrażania, w tym artykule, aby uzyskać pierwszy maszyn wirtualnych wdrożonych przy użyciu najwyższej dysków SSD.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Wdrażanie ultra dyski SSD przy użyciu usługi Azure Resource Manager

Najpierw Ustal, rozmiar maszyny Wirtualnej do wdrożenia. W ramach tej wersji zapoznawczej obsługiwane są tylko rodziny DsV3 i EsV3 maszyn wirtualnych. Znajduje się w drugiej tabeli, w tym [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) Aby uzyskać szczegółowe informacje o tych rozmiarach maszyn wirtualnych.

Jeśli chcesz utworzyć maszynę Wirtualną przy użyciu wielu ultra dysków SSD, odnoszą się do przykładu [Utwórz Maszynę wirtualną przy użyciu wielu SSD ultra](https://aka.ms/UltraSSDTemplate).

Jeśli zamierzasz używać własnego szablonu, upewnij się, że **apiVersion** dla `Microsoft.Compute/virtualMachines` i `Microsoft.Compute/Disks` jest ustawiony jako `2018-06-01` (lub nowsza).

Ustaw jednostkę sku dysku **UltraSSD_LRS**, następnie ustawić pojemności dysku, operacje We/Wy, strefa dostępności i przepustowości w MB/s, aby utworzyć dysk największa.

Po zaaprowizowaniu maszyny Wirtualnej można podzielić na partycje i sformatować dyski z danymi i skonfigurować je dla obciążeń.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Wdrażanie ultra dyski SSD przy użyciu interfejsu wiersza polecenia

Najpierw Ustal, rozmiar maszyny Wirtualnej do wdrożenia. W ramach tej wersji zapoznawczej obsługiwane są tylko rodziny DsV3 i EsV3 maszyn wirtualnych. Znajduje się w drugiej tabeli, w tym [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) Aby uzyskać szczegółowe informacje o tych rozmiarach maszyn wirtualnych.

Aby użyć ultra dysków SSD, należy utworzyć maszynę Wirtualną, która jest w stanie przy użyciu najwyższej dysków SSD.

Zastąp lub ustaw **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** zmienne własnymi wartościami. Ustaw **$zone** wartość uzyskany ze strefy dostępności [start części tego artykułu](#determine-your-availability-zone). Następnie uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć ultra włączone maszyny Wirtualnej:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Tworzenie najwyższej dyski SSD przy użyciu interfejsu wiersza polecenia

Teraz, gdy masz maszynę Wirtualną, która jest w stanie przy użyciu najwyższej dysków SSD, można tworzyć i do niej dołączyć ultra dyski SSD.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Dostosuj wydajność ultra dyski SSD przy użyciu interfejsu wiersza polecenia

Największa SSD oferują unikatowa funkcja pozwala na dostosowanie ich wydajności, następujące polecenie przedstawia sposób używania tej funkcji:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Wdrażanie ultra dyski SSD przy użyciu programu PowerShell

Najpierw Ustal, rozmiar maszyny Wirtualnej do wdrożenia. W ramach tej wersji zapoznawczej obsługiwane są tylko rodziny DsV3 i EsV3 maszyn wirtualnych. Znajduje się w drugiej tabeli, w tym [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) Aby uzyskać szczegółowe informacje o tych rozmiarach maszyn wirtualnych.

Aby użyć ultra dysków SSD, należy utworzyć maszynę Wirtualną, która jest w stanie przy użyciu najwyższej dysków SSD. Zastąp lub ustaw **$resourcegroup** i **$vmName** zmienne własnymi wartościami. Ustaw **$zone** wartość uzyskany ze strefy dostępności [start części tego artykułu](#determine-your-availability-zone). Następnie uruchom następujące polecenie [New-AzVm](/powershell/module/az.compute/new-azvm) maszyny Wirtualnej z obsługą polecenie, aby utworzyć ultra:

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

### <a name="create-an-ultra-ssd-using-powershell"></a>Tworzenie najwyższej dyski SSD przy użyciu programu PowerShell

Teraz, gdy masz maszynę Wirtualną, która jest w stanie przy użyciu najwyższej dysków SSD, można tworzyć i do niej dołączyć ultra dyski SSD:

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Dostosuj wydajność ultra dyski SSD przy użyciu programu PowerShell

Największa SSD ma unikatowa funkcja pozwala na dostosowanie ich wydajności, polecenie znajduje się przykład, który dostosowuje wydajność bez konieczności Odłącz dysk:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz spróbować nowy typ dysku [żądanie dostępu do wersji zapoznawczej w tej ankiecie](https://aka.ms/UltraSSDPreviewSignUp).