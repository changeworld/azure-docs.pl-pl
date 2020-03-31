---
title: Ograniczanie dostępu do sieci do zasobów PaaS — interfejs wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak ograniczyć i ograniczyć dostęp do sieci do zasobów platformy Azure, takich jak Usługa Azure Storage i usługa Azure SQL Database, z punktami końcowymi usługi sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: f2dcc714bc9052dd51f114e24f0b9bd74b87480c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186402"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Ograniczanie dostępu do sieci do zasobów PaaS za pomocą punktów końcowych usługi sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Punkty końcowe usługi dla sieci wirtualnej umożliwiają ograniczenie dostępu sieciowego do niektórych zasobów usługi platformy Azure do podsieci sieci wirtualnej. Możesz również uniemożliwić dostęp internetowy do zasobów. Punkty końcowe usługi zapewniają bezpośrednie połączenie z sieci wirtualnej z obsługiwanymi usługami platformy Azure, umożliwiając korzystanie z prywatnej przestrzeni adresowej sieci wirtualnej w celu uzyskiwania dostępu do usług platformy Azure. Ruch kierowany do zasobów platformy Azure za pośrednictwem punktów końcowych usługi zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieci wirtualnej z jedną podsiecią
* Dodawanie podsieci i włączanie punktu końcowego usługi
* Tworzenie zasobów platformy Azure i zezwalanie na dostęp sieciowy do nich tylko z podsieci
* Wdrażanie maszyny wirtualnej w każdej podsieci
* Potwierdzanie dostępu do zasobu z podsieci
* Potwierdzanie zablokowania dostępu do zasobu z podsieci i z Internetu

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie inne zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz sieć wirtualną za pomocą jednej podsieci z [siecią wirtualną AZ .](/cli/azure/network/vnet)

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi 

Punkty końcowe usługi można włączyć tylko dla usług, które obsługują punkty końcowe usługi. Wyświetlanie usług z obsługą punktu końcowego dostępnych w lokalizacji platformy Azure za pomocą [sieci az vnet list-endpoint-services](/cli/azure/network/vnet). Poniższy przykład zwraca listę usług z obsługą punktu końcowego dostępnych w regionie *eastus.* Lista zwróconych usług będzie rosnąć wraz z upływem czasu, ponieważ więcej usług platformy Azure stanie się włączonym punktem końcowym usługi.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Utwórz dodatkową podsieć w sieci wirtualnej za pomocą [podsieci sieci AZ .](/cli/azure/network/vnet/subnet) W tym przykładzie punkt końcowy usługi dla *microsoft.storage* jest tworzony dla podsieci: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Ograniczanie dostępu sieciowego dla podsieci

Utwórz grupę zabezpieczeń sieci [z nsg sieci az create](/cli/azure/network/nsg). Poniższy przykład tworzy grupę zabezpieczeń sieci o nazwie *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Skojarz grupę zabezpieczeń sieci z *podsiecią prywatną* z [aktualizacją podsieci sieci AZ](/cli/azure/network/vnet/subnet). Poniższy przykład kojarzy grupę zabezpieczeń sieci *myNsgPrivate* z *podsiecią prywatną:*

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Tworzenie reguł zabezpieczeń za pomocą [reguły nsg sieci az create](/cli/azure/network/nsg/rule). Następująca zasada umożliwia dostęp wychodzący do publicznych adresów IP przypisanych do usługi Azure Storage: 

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
```

Każda siećowa grupa zabezpieczeń zawiera kilka [domyślnych reguł zabezpieczeń](security-overview.md#default-security-rules). Reguła, która następuje, zastępuje domyślną regułę zabezpieczeń, która umożliwia dostęp wychodzący do wszystkich publicznych adresów IP. Opcja `destination-address-prefix "Internet"` odmawia dostępu wychodzącego do wszystkich publicznych adresów IP. Poprzednia reguła zastępuje tę regułę ze względu na jej wyższy priorytet, który umożliwia dostęp do publicznych adresów IP usługi Azure Storage.

```azurecli-interactive
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
```

Poniższa reguła umożliwia ruch SSH przychodzący do podsieci z dowolnego miejsca. Reguła zastępuje domyślną regułę zabezpieczeń, która zakazuje całego ruchu przychodzącego z Internetu. SSH jest dozwolone do podsieci, dzięki czemu łączność może być testowany w późniejszym kroku.

```azurecli-interactive
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

## <a name="restrict-network-access-to-a-resource"></a>Ograniczanie dostępu sieciowego do zasobu

Kroki niezbędne do ograniczenia dostępu sieciowego do zasobów utworzonych za pomocą usług platformy Azure obsługujących punkty końcowe usługi różnią się w zależności od usługi. Zobacz dokumentację poszczególnych usług, aby poznać konkretne kroki dla każdej usługi. Poniżej dalsza część artykułu Zawiera kroki, aby ograniczyć dostęp do sieci dla konta usługi Azure Storage, jako przykład.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu platformy Azure przy [tworzeniu konta magazynu az](/cli/azure/storage/account). Zamień `<replace-with-your-unique-storage-account-name>` na nazwę, która jest unikatowa we wszystkich lokalizacjach platformy Azure o długości od 3 do 24 znaków, używając tylko cyfr i mniejszych liter.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po utworzeniu konta magazynu pobierz parametry połączenia dla konta magazynu do zmiennej z [az storage account show-connection-string](/cli/azure/storage/account). Ciąg połączenia jest używany do tworzenia udziału plików w późniejszym kroku.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Wyświetl zawartość zmiennej i zanotuj wartość **klawisza AccountKey** zwróconego w danych wyjściowych, ponieważ jest on używany w późniejszym kroku.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

Utwórz udział plików na koncie magazynu za pomocą [az storage share create](/cli/azure/storage/share). W późniejszym kroku ten udział plików jest instalowany w celu potwierdzenia dostępu do sieci.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Odmówienie dostępu sieciowego do konta magazynu

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranych sieci, zmień akcję domyślną na *Odmów* z [aktualizacją konta magazynu az](/cli/azure/storage/account). Kiedy dostęp sieciowy jest blokowany, konto magazynu nie jest dostępne z żadnej sieci.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Włączanie dostępu sieciowego z podsieci

Zezwalaj na dostęp sieciowy do konta magazynu z podsieci *prywatnej* z [dodaniem reguły sieciowej konta magazynu AZ](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Aby przetestować dostęp sieciowy do konta magazynu, należy wdrożyć maszynę wirtualną w każdej podsieci.

### <a name="create-the-first-virtual-machine"></a>Tworzenie pierwszej maszyny wirtualnej

Tworzenie maszyny Wirtualnej w podsieci *publicznej* za pomocą [az vm create](/cli/azure/vm). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej interfejsu wiersza polecenia platformy Azure zawiera informacje podobne do następującego przykładu: 

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

Zanotuj **publicIpAddress** w zwróconym wyjściu. Ten adres jest używany do uzyskiwania dostępu do maszyny Wirtualnej z Internetu w późniejszym kroku.

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu, należy wziąć pod uwagę **publicIpAddress** w danych wyjściowych zwróconych. Ten adres jest używany do uzyskiwania dostępu do maszyny Wirtualnej z Internetu w późniejszym kroku.

## <a name="confirm-access-to-storage-account"></a>Potwierdzanie dostępu do konta magazynu

SSH do maszyny wirtualnej *myVmPrivate.* Zastąp * \<publicIpAddress>* publicznym adresem IP maszyny wirtualnej *myVmPrivate.*

```bash 
ssh <publicIpAddress>
```

Tworzenie folderu dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Zainstaluj udział plików platformy Azure w utworzonym katalogu. Przed uruchomieniem następującego `<storage-account-name>` polecenia zastąp nazwą konta i `<storage-account-key>` kluczem pobranym w [umień konta magazynu](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zostanie wyświetlony `user@myVmPrivate:~$` monit. Pomyślnie zainstalowany udział plików platformy Azure w */mnt/MyAzureFileShare*.

Upewnij się, że maszyna wirtualna nie ma połączenia wychodzącego z innymi publicznymi adresami IP:

```bash
ping bing.com -c 4
```

Nie otrzymasz żadnych odpowiedzi, ponieważ sieciowa grupa zabezpieczeń skojarzona z podsiecią *Private* nie zezwala na dostęp ruchu wychodzącego do publicznych adresów IP innych niż adresy przypisane do usługi Azure Storage.

Zamknij sesję SSH na maszynie *wirtualnej myVmPrivate.*

## <a name="confirm-access-is-denied-to-storage-account"></a>Potwierdzanie odmowy dostępu do konta magazynu

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną *myVmPublic.* Zamień `<publicIpAddress>` publiczny adres IP maszyny Wirtualnej *myVmPublic:* 

```bash 
ssh <publicIpAddress>
```

Tworzenie katalogu dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Spróbuj zainstalować udział plików platformy Azure w utworzonym katalogu. W tym artykule założono, że wdrożono najnowszą wersję Ubuntu. Jeśli używasz wcześniejszych wersji Ubuntu, zobacz [Mount on Linux,](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aby uzyskać dodatkowe instrukcje dotyczące montażu udziałów plików. Przed uruchomieniem następującego `<storage-account-name>` polecenia zastąp nazwą konta i `<storage-account-key>` kluczem pobranym w [umień konta magazynu:](#create-a-storage-account)

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Odmowa dostępu i zostanie `mount error(13): Permission denied` wyświetlony błąd, ponieważ *maszyna wirtualna myVmPublic* jest wdrażana w podsieci *publicznej.* Podsieć *Public* nie ma punktu końcowego usługi obsługującego usługę Azure Storage, a konto magazynu zezwala tylko na dostęp sieciowy z podsieci *Private*, a nie z podsieci *Public*.

Zamknij sesję SSH na maszynie *wirtualnej myVmPublic.*

Na komputerze spróbuj wyświetlić udziały na koncie pamięci masowej z [listą udziałów magazynu AZ](/cli/azure/storage/share?view=azure-cli-latest). Zastąp `<account-name>` i `<account-key>` nazwę konta magazynu i klucz z tworzenia konta [magazynu:](#create-a-storage-account)

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Odmowa dostępu i zostanie wyświetlony *ten wniosek nie jest autoryzowany do wykonania tego* błędu operacji, ponieważ komputer nie znajduje się w podsieci *prywatnej* sieci wirtualnej *MyVirtualNetwork.*

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj [az group delete,](/cli/azure) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule włączono punkt końcowy usługi dla podsieci sieci wirtualnej. Wiesz teraz, że punkty końcowe usługi można włączyć dla zasobów wdrożonych w wielu usługach platformy Azure. Zostało utworzone konto usługi Azure Storage i dostęp sieciowy do konta magazynu został ograniczony tylko do zasobów w podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [Service endpoints overview (Omówienie punktów końcowych usługi)](virtual-network-service-endpoints-overview.md) i [Manage subnets (Zarządzanie podsieciami)](virtual-network-manage-subnet.md).

Jeśli masz wiele sieci wirtualnych w ramach Twojego konta, możesz chcieć połączyć ze sobą dwie sieci wirtualne, aby zasoby w każdej sieci wirtualnej mogły komunikować się ze sobą. Aby dowiedzieć się, jak to zrobić, zobacz [Łączenie sieci wirtualnych](tutorial-connect-virtual-networks-cli.md).
