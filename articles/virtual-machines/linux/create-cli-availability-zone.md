---
title: Tworzenie nieupakowaną maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Tworzenie maszyny Wirtualnej z systemem Linux w strefie dostępności przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: cdd9910bfef96f56cfa8c8e81363ff9bdb40f444
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005506"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Utwórz maszynę wirtualną systemu Linux w strefie dostępności przy użyciu wiersza polecenia platformy Azure

Tym artykule omówiono tworzenie maszyny Wirtualnej z systemem Linux w strefie dostępności platformy Azure za pomocą wiersza polecenia platformy Azure. [Strefa dostępności](../../availability-zones/az-overview.md) to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby użyć strefy dostępność, utwórz maszynę wirtualną w [obsługiwanym regionie platformy Azure](../../availability-zones/az-overview.md#services-support-by-region).

Upewnij się, że zainstalowano najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się przy użyciu konta platformy Azure [az login](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Sprawdzanie dostępności jednostki SKU maszyny wirtualnej
Dostępność rozmiarów maszyn wirtualnych lub jednostek SKU może różnić się w zależności od regionu i strefy. Aby uzyskać pomoc w planowaniu użycia stref dostępności, możesz wyświetlić listę dostępnych jednostek SKU maszyn wirtualnych według regionu i strefy świadczenia usługi Azure. Ta możliwość gwarantuje wybranie odpowiedniego rozmiaru maszyny wirtualnej oraz uzyskanie żądanej odporności w różnych strefach. Aby uzyskać więcej informacji o różnych typach i rozmiarach maszyn wirtualnych, zobacz [Omówienie rozmiarów maszyn wirtualnych](sizes.md).

Możesz wyświetlić dostępne jednostki SKU maszyny Wirtualnej za pomocą [az vm list-skus](/cli/azure/vm) polecenia. Następujący przykład zawiera listę dostępnych jednostek SKU maszyn wirtualnych w regionie *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

Rezultat jest podobny do poniższego, skróconego przykładu, który pokazuje strefy dostępności, w których dostępne są poszczególne rozmiary maszyn wirtualnych:

```azurecli
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group).  

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną. W tym przykładzie grupa zasobów o nazwie *myResourceGroupVM* jest tworzony w *eastus2* regionu. Wschodnie stany USA 2, jest jednym z regionów świadczenia usługi Azure, które obsługują strefy dostępności.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Grupa zasobów jest określana, podczas tworzenia lub modyfikowania maszyn wirtualnych, co zostało przedstawione w tym artykule.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). 

Podczas tworzenia maszyny wirtualnej masz dostęp do kilku opcji, takich jak obraz systemu operacyjnego, ustalanie rozmiaru dysku i poświadczenia administracyjne. W tym przykładzie tworzona jest maszyna wirtualna o nazwie *myVM* z systemem Ubuntu Server. Maszyna wirtualna jest utworzona w strefie dostępności *1*. Domyślnie maszyny Wirtualnej jest tworzony w *standardowa_ds1_v2* rozmiar.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

Tworzenie maszyny wirtualnej może potrwać kilka minut. Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetla informacje o maszynie wirtualnej. Zwróć uwagę na `zones` wartości, co oznacza strefy dostępności, w którym maszyna wirtualna jest uruchomiona. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potwierdzanie strefy dla dysku zarządzanego i adresu IP

Po wdrożeniu maszyny Wirtualnej w strefie dostępności dysku zarządzanego dla maszyny Wirtualnej jest tworzony w tej samej strefie dostępności. Domyślnie publiczny adres IP jest tworzona w tej strefie. Poniższy przykład pobiera informacje o tych zasobów.

Aby sprawdzić, czy dysku zarządzanego maszyny Wirtualnej jest w strefie dostępności, użyj [az vm show](/cli/azure/vm) polecenie, aby zwrócić ten identyfikator dysku. W tym przykładzie identyfikator dysku jest przechowywany w zmiennej używanej w późniejszym kroku. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Teraz można uzyskać informacji na temat dysków zarządzanych:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Dane wyjściowe pokazują, że dysk zarządzany znajduje się w tej samej strefie dostępności co maszyna wirtualna:

```azurecli
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Użyj [az vm-— adresy ip](/cli/azure/vm) polecenie, aby zwrócić nazwę zasobu publicznego adresu IP w *myVM*. W tym przykładzie nazwa jest przechowywana w zmiennej używanej w późniejszym kroku.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Teraz możesz uzyskać informacje o adresie IP:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Dane wyjściowe pokazują, że adres IP jest w tej samej strefie dostępności co maszyna wirtualna:

```azurecli
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób tworzenia maszyny wirtualnej w strefie dostępności. Dowiedz się więcej o [regionach i dostępności](regions-and-availability.md) maszyn wirtualnych platformy Azure.




