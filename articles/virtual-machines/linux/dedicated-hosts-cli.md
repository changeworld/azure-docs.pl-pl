---
title: Wdrażanie maszyn wirtualnych z systemem Linux na dedykowanych hostach przy użyciu interfejsu wiersza polecenia
description: Wdrażanie maszyn wirtualnych do dedykowanych hostów przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127700"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach przy użyciu interfejsu wiersza polecenia platformy Azure
 

W tym artykule opisano, jak utworzyć [dedykowany host](dedicated-hosts.md) platformy Azure do obsługi maszyn wirtualnych (maszyn wirtualnych). 

Upewnij się, że zainstalowano platformę Azure CLI w wersji 2.0.70 lub nowszej i zalogowano się do konta platformy Azure przy użyciu `az login`. 


## <a name="limitations"></a>Ograniczenia

- Zestawy skalowania maszyny wirtualnej nie są obecnie obsługiwane na dedykowanych hostach.
- Rozmiary i typy sprzętu dostępne dla dedykowanych hostów różnią się w zależności od regionu. Więcej informacji można znaleźć na [stronie z cennikiem hosta.](https://aka.ms/ADHPricing)
 

## <a name="create-resource-group"></a>Tworzenie grupy zasobów 
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów z utworzeniem grupy AZ. Poniższy przykład tworzy grupę zasobów o nazwie *myDHResourceGroup* w lokalizacji *wschodnich stanów USA.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Tworzenie grupy hostów 

**Grupa hostów** jest zasobem reprezentującym kolekcję dedykowanych hostów. Tworzysz grupę hostów w regionie i strefie dostępności i dodajesz do niej hosty. Podczas planowania wysokiej dostępności, istnieją dodatkowe opcje. Z dedykowanymi hostami można użyć jednej lub obu następujących opcji: 
- Obejmuje wiele stref dostępności. W takim przypadku musisz mieć grupę hostów w każdej ze stref, których chcesz użyć.
- Obejmują wiele domen błędów, które są mapowane do stojaków fizycznych. 
 
W obu przypadkach należy podać liczbę domen błędów dla grupy hosta. Jeśli nie chcesz obejmować domen błędów w grupie, użyj liczby domen błędów 1. 

Można również zdecydować się na użycie zarówno stref dostępności, jak i domen błędów. 

W tym przykładzie [użyjemy az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) do utworzenia grupy hostów przy użyciu stref dostępności i domen błędów. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Inne przykłady

Można również użyć [az vm host group create,](/cli/azure/vm/host/group#az-vm-host-group-create) aby utworzyć grupę hostów w strefie dostępności 1 (i bez domen błędów).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Poniżej użyto [az vm grupy hosta utworzyć](/cli/azure/vm/host/group#az-vm-host-group-create) do utworzenia grupy hosta przy użyciu tylko domeny błędów (do użycia w regionach, w których strefy dostępności nie są obsługiwane). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Tworzenie hosta 

Teraz utwórzmy dedykowanego hosta w grupie hostów. Oprócz nazwy hosta, należy podać jednostkę SKU dla hosta. Jednostka SKU hosta przechwytuje obsługiwaną serię maszyn wirtualnych, a także generowanie sprzętu dla dedykowanego hosta.  

Aby uzyskać więcej informacji na temat jednostek SKU hosta i cen, zobacz [Cennik hosta dedykowanego platformy Azure](https://aka.ms/ADHPricing).

Użyj [az vm hosta utworzyć,](/cli/azure/vm/host#az-vm-host-create) aby utworzyć hosta. Jeśli ustawisz liczbę domen błędów dla grupy hostów, zostaniesz poproszony o określenie domeny błędów dla hosta.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej 
Utwórz maszynę wirtualną w dedykowanym hoście przy użyciu [programu az vm create](/cli/azure/vm#az-vm-create). Jeśli podczas tworzenia grupy hosta określono strefę dostępności, podczas tworzenia maszyny wirtualnej jest wymagana użycia tej samej strefy.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Jeśli utworzysz maszynę wirtualną na hoście, który nie ma wystarczającej ilości zasobów, maszyna wirtualna zostanie utworzona w stanie FAILED. 


## <a name="check-the-status-of-the-host"></a>Sprawdź stan hosta

Stan kondycji hosta i liczbę maszyn wirtualnych można nadal wdrażać na hoście przy użyciu [widoku get-instance-view hosta az vm.](/cli/azure/vm/host#az-vm-host-get-instance-view)

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Dane wyjściowe będą wyglądać podobnie do tego:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Eksportowanie jako szablonu 
Szablon można wyeksportować, jeśli teraz chcesz utworzyć dodatkowe środowisko programistyczne o tych samych parametrach lub środowisko produkcyjne, które go pasuje. Menedżer zasobów używa szablonów JSON, które definiują wszystkie parametry dla środowiska. Tworzenie całych środowisk przez odwoływanie się do tego szablonu JSON. Szablony JSON można tworzyć ręcznie lub eksportować istniejące środowisko, aby utworzyć szablon JSON. Eksportowanie [grupy AZ](/cli/azure/group#az-group-export) służy do eksportowania grupy zasobów.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

To polecenie `myDHResourceGroup.json` tworzy plik w bieżącym katalogu roboczym. Podczas tworzenia środowiska z tego szablonu, zostanie wyświetlony monit o wszystkie nazwy zasobów. Nazwy te można wypełnić w pliku szablonu, dodając `--include-parameter-default-value` parametr do `az group export` polecenia. Edytuj szablon JSON, aby określić nazwy zasobów, lub utwórz plik parameters.json określający nazwy zasobów.
 
Aby utworzyć środowisko na podstawie szablonu, użyj [programu AZ group deployment create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Czyszczenie 

Opłaty są naliczane za dedykowane hosty, nawet jeśli nie są wdrażane żadne maszyny wirtualne. Należy usunąć wszystkie hosty, których obecnie nie używasz, aby zaoszczędzić koszty.  

Hosta można usunąć tylko wtedy, gdy nie ma już maszyn wirtualnych korzystających z niego. Usuń maszyny wirtualne przy użyciu [az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Po usunięciu maszyn wirtualnych można usunąć hosta za pomocą [programu az vm host delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Po usunięciu wszystkich hostów można usunąć grupę hostów za pomocą [az vm host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Można również usunąć całą grupę zasobów w jednym poleceniu. Spowoduje to usunięcie wszystkich zasobów utworzonych w grupie, w tym wszystkich maszyn wirtualnych, hostów i grup hostów.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [przegląd dedykowanych hostów.](dedicated-hosts.md)

- Dedykowane hosty można również tworzyć za pomocą [portalu Azure](dedicated-hosts-portal.md).

- Istnieje przykładowy szablon, znaleziono [tutaj,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)który używa zarówno stref i domen błędów dla maksymalnej odporności w regionie.