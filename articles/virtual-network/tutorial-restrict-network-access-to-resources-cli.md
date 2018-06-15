---
title: Ograniczenie dostępu do sieci do zasobów PaaS - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się jak ograniczenia i ograniczenie dostępu do sieci do zasobów platformy Azure, takich jak usługi Azure Storage i bazy danych SQL Azure z punktów końcowych usługi sieci wirtualnej przy użyciu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f357861a7a44b249e06f091a8693b7f2d8dd5178
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841983"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Ograniczenie dostępu do sieci do PaaS zasobów z punktów końcowych usługi sieci wirtualnej przy użyciu wiersza polecenia platformy Azure

Punktów końcowych usługi sieci wirtualnej umożliwiają ograniczenie dostępu do sieci do niektórych zasobów usługi Azure z podsiecią sieci wirtualnej. Można również usunąć dostęp do zasobów Internetu. Punkty końcowe usługi zapewniają bezpośredniego połączenia z Twojej sieci wirtualnej do obsługiwanych usług platformy Azure, co umożliwia wykorzystanie wirtualnej sieci prywatnej przestrzeni adresowej do uzyskiwania dostępu do usług Azure. Ruch kierowany do zasobów platformy Azure za pomocą punktów końcowych usługi zawsze pozostaje w sieci Microsoft Azure w sieci szkieletowej. W tym artykule dowiesz się, jak:

* Utwórz sieć wirtualną z jedną podsiecią
* Dodaj podsieć i włączyć punkt końcowy usługi
* Tworzenie zasobów platformy Azure i zezwolić na dostęp do sieci z go z tylko podsieci
* Wdróż maszynę wirtualną (VM) do każdej podsieci
* Potwierdź dostęp do zasobu z podsieci
* Upewnij się, że odmowa dostępu do zasobu z podsieci i z Internetu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli do zainstalowania i używania interfejsu wiersza polecenia lokalnie tego przewodnika Szybki Start, wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej, należy utworzyć grupę zasobów dla sieci wirtualnej i innych zasobów utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz sieć wirtualną z jedną podsiecią z [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Włącz punkt końcowy usługi 

Można włączyć punkty końcowe usługi tylko dla usług, które obsługują punkty końcowe usługi. Wyświetl usług korzystających z punktu końcowego usługi dostępne w lokalizacji platformy Azure z [sieci wirtualnej listy — punkt końcowy — usługi sieci az](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services). Poniższy przykład zwraca listę usług włączony punkt końcowy usługi dostępne w *eastus* regionu. Na liście usług zwrócił będzie rosnąć wraz z upływem czasu, zgodnie z usług Azure więcej stają się włączony punkt końcowy usługi.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Utworzyć dodatkowe podsieci w sieci wirtualnej z [Utwórz podsieć sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). W tym przykładzie punkt końcowy usługi dla *Microsoft.Storage* jest tworzony w podsieci: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Ograniczenie dostępu do sieci dla podsieci

Utwórz grupę zabezpieczeń sieci z [utworzyć nsg sieci az](/cli/azure/network/nsg#az_network_nsg_create). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Skojarz grupę zabezpieczeń sieci, aby *prywatnej* podsieć o [zaktualizować podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). W poniższym przykładzie *myNsgPrivate* sieciową grupę zabezpieczeń do *prywatnej* podsieci:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Utwórz zasady zabezpieczeń z [Tworzenie reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Reguła, która wykonuje umożliwia dostęp ruchu wychodzącego na publiczne adresy IP przypisane do usługi Azure Storage: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Ograniczenie dostępu do sieci do zasobu

Kroki niezbędne do ograniczania dostępu do sieci do zasobów został utworzony za pomocą usług Azure włączone dla punktów końcowych usługi różni się w usługach. W dokumentacji dla poszczególnych usług, aby poznać konkretne kroki dla każdej usługi. W dalszej części tego artykułu zawiera kroki, aby ograniczyć dostęp do konta usługi Azure Storage, na przykład.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu platformy Azure z [Tworzenie konta magazynu az](/cli/azure/storage/account#az_storage_account_create). Zastąp `<replace-with-your-unique-storage-account-name>` nazwę, która jest unikatowa dla wszystkich lokalizacji platformy Azure, w zakresie od 3 do 24 znaków długości, używając tylko cyfry i małe litery.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po utworzeniu konta magazynu, należy pobrać parametry połączenia dla konta magazynu do zmiennej o [Pokaż parametry konta magazynu az](/cli/azure/storage/account#az_storage_account_show_connection_string). Ciąg połączenia jest używany do utworzenia udziału plików w kolejnym kroku.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Wyświetlanie zawartości zmiennej i zanotuj wartość **AccountKey** zwracany w danych wyjściowych, ponieważ jest on używany w kolejnym kroku.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

Utwórz udział plików na koncie magazynu z [utworzyć udział magazynu az](/cli/azure/storage/share#az_storage_share_create). W kolejnym kroku ten udział plików jest zainstalowany dostępu do sieci, aby go potwierdzić.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Odmowa dostępu do całej sieci na konto magazynu

Domyślnie kont magazynu akceptować połączenia od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranej sieci, należy zmienić domyślną akcję do *Odmów* z [aktualizacja konta magazynu az](/cli/azure/storage/account#az_storage_account_update). Po odmowa dostępu do sieci konto magazynu nie jest dostępny z żadną siecią.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Włączanie dostępu do sieci z podsiecią

Zezwalaj na dostęp sieciowy do konta magazynu z *prywatnej* podsieć o [Dodawanie konta magazynu az sieci rule](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Aby przetestować dostępu do sieci na konto magazynu, należy wdrożyć maszynę Wirtualną do każdej podsieci.

### <a name="create-the-first-virtual-machine"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę Wirtualną w *publicznego* podsieć o [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Jeśli kluczy SSH już nie istnieją w domyślnej lokalizacji klucza, polecenie tworzy je. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Maszyna wirtualna ma kilka minut na utworzenie. Po utworzeniu maszyny Wirtualnej, interfejsu wiersza polecenia Azure zawiera informacje podobne do poniższego przykładu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Zwróć uwagę na **publicznego adresu IP** zwróconych danych wyjściowych. Ten adres jest używany na dostęp do maszyny Wirtualnej z Internetu w kolejnym kroku.

### <a name="create-the-second-virtual-machine"></a>Tworzenie drugiej maszyny wirtualnej

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Maszyna wirtualna ma kilka minut na utworzenie. Po utworzeniu, zwróć uwagę na **publicznego adresu IP** w dane wyjściowe zwrócone. Ten adres jest używany na dostęp do maszyny Wirtualnej z Internetu w kolejnym kroku.

## <a name="confirm-access-to-storage-account"></a>Potwierdź dostęp do konta magazynu

SSH do *myVmPrivate* maszyny Wirtualnej. Zastąp *<publicIpAddress>* publiczny adres IP Twojego *myVmPrivate* maszyny Wirtualnej.

```bash 
ssh <publicIpAddress>
```

Utwórz folder na potrzeby punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Instalowanie udziału plików na platformę Azure do katalogu, który został utworzony. Przed uruchomieniem następujące polecenie, Zastąp `<storage-account-name>` nazwą konta i `<storage-account-key>` z kluczem pobierana w [Utwórz konto magazynu](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Pojawi się `user@myVmPrivate:~$` wiersza. Udział plików na platformę Azure pomyślnie zainstalowany */mnt/MyAzureFileShare*.

Upewnij się, że maszyna wirtualna nie ma wychodzącego łączności z innymi publicznych adresów IP:

```bash
ping bing.com -c 4
```

Nie odpowiedzi jest wyświetlany, ponieważ grupa zabezpieczeń sieci skojarzonych z *prywatnej* podsieci nie zezwala na dostęp ruchu wychodzącego do publicznych adresów IP innego niż adres przypisany do usługi Magazyn Azure.

Zakończyć sesję SSH, aby *myVmPrivate* maszyny Wirtualnej.

## <a name="confirm-access-is-denied-to-storage-account"></a>Upewnij się, że odmowa dostępu do konta magazynu

Użyj następującego polecenia, aby utworzyć sesję SSH z *myVmPublic* maszyny Wirtualnej. Zastąp `<publicIpAddress>` publiczny adres IP Twojego *myVmPublic* maszyny Wirtualnej: 

```bash 
ssh <publicIpAddress>
```

Utwórz katalog dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Próba instalowanie udziału plików na platformę Azure do katalogu, który został utworzony. W tym artykule przyjęto założenie, że wdrożone najnowszą wersję Ubuntu. Jeśli korzystasz z wcześniejszymi wersjami systemu Ubuntu, zobacz [zainstalować w systemie Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dodatkowe instrukcje dotyczące instalowania udziałów plików. Przed uruchomieniem następujące polecenie, Zastąp `<storage-account-name>` nazwą konta i `<storage-account-key>` z kluczem pobierana w [Utwórz konto magazynu](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Odmowa dostępu i pojawi się `mount error(13): Permission denied` błąd, ponieważ *myVmPublic* maszyna wirtualna jest wdrażana w obrębie *publicznego* podsieci. *Publicznego* podsieci nie ma punktu końcowego usługi włączone dla usługi Azure Storage i konta magazynu tylko zezwala na dostęp do sieci z *prywatnej* podsieci, nie *publicznego*podsieci.

Zakończyć sesję SSH, aby *myVmPublic* maszyny Wirtualnej.

Z komputera, próba przeglądania udziałów na koncie magazynu z [listy udziału magazynu az](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Zastąp `<account-name>` i `<account-key>` z nazwą konta magazynu i klucza z [Utwórz konto magazynu](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Odmowa dostępu i pojawi się *tego żądania nie ma uprawnień do wykonania tej operacji* błąd, ponieważ komputer nie znajduje się w *prywatnej* podsieć *MyVirtualNetwork* sieci wirtualnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, użyj [usunięcie grupy az](/cli/azure#az_group_delete) można usunąć grupy zasobów i wszystkie zasoby zawiera.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule należy włączyć punkt końcowy usługi dla podsieci sieci wirtualnej. Wiesz, że punkty końcowe usługi można włączyć dla zasobów z wielu usług Azure. Utworzono konto magazynu Azure i ograniczania dostępu do sieci do konta magazynu, aby tylko zasoby w podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [Omówienie punktów końcowych usługi](virtual-network-service-endpoints-overview.md) i [Zarządzanie podsieci](virtual-network-manage-subnet.md).

Jeśli masz wiele sieci wirtualnych w ramach Twojego konta, możesz połączyć ze sobą dwie sieci wirtualne, więc zasoby w każdej sieci wirtualnej mogą komunikować się ze sobą. Aby dowiedzieć się więcej, zobacz temat [połączyć sieci wirtualnych](tutorial-connect-virtual-networks-cli.md).