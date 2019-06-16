---
title: Ograniczanie dostępu sieciowego do zasobów PaaS — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule dowiesz się, jak ograniczyć i zablokować dostęp sieciowy do zasobów platformy Azure, takich jak Azure Storage i Azure SQL Database, za pomocą punktów końcowych usługi sieci wirtualnej przy użyciu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.openlocfilehash: e52829723b41f9274251ebe7432aa659251c0da4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695123"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Ograniczanie dostępu sieciowego do zasobów PaaS za pomocą punktów końcowych usługi sieci wirtualnej przy użyciu wiersza polecenia platformy Azure

Punkty końcowe usługi dla sieci wirtualnej umożliwiają ograniczenie dostępu sieciowego do niektórych zasobów usługi platformy Azure do podsieci sieci wirtualnej. Możesz również uniemożliwić dostęp internetowy do zasobów. Punkty końcowe usługi zapewniają bezpośrednie połączenie z sieci wirtualnej z obsługiwanymi usługami platformy Azure, umożliwiając korzystanie z prywatnej przestrzeni adresowej sieci wirtualnej w celu uzyskiwania dostępu do usług platformy Azure. Ruch kierowany do zasobów platformy Azure za pośrednictwem punktów końcowych usługi zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieci wirtualnej z jedną podsiecią
* Dodawanie podsieci i włączanie punktu końcowego usługi
* Tworzenie zasobów platformy Azure i zezwalanie na dostęp sieciowy do nich tylko z podsieci
* Wdrażanie maszyny wirtualnej w każdej podsieci
* Potwierdzanie dostępu do zasobu z podsieci
* Potwierdzanie zablokowania dostępu do zasobu z podsieci i z Internetu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej, należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz sieć wirtualną z jedną podsiecią z [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi 

Można włączyć punkty końcowe usługi tylko w przypadku usług, które obsługują punkty końcowe usługi. Wyświetlanie usług korzystających z punktu końcowego usługi dostępne w lokalizacji platformy Azure za pomocą [az sieci vnet w listy usług punktu końcowego](/cli/azure/network/vnet). Poniższy przykład zwraca listę usług włączony punkt końcowy usługi dostępne w *eastus* regionu. Na liście usług, zwracana będzie rosnąć wraz z upływem czasu, zgodnie z innymi usługami platformy Azure stają się włączonego punktu końcowego usługi.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Utwórz dodatkowe podsieci w sieci wirtualnej za pomocą [az podsieci sieci wirtualnej Utwórz](/cli/azure/network/vnet/subnet). W tym przykładzie punkt końcowy usługi dla *Microsoft.Storage* jest tworzony w podsieci: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Ograniczanie dostępu sieciowego dla podsieci

Utwórz sieciową grupę zabezpieczeń z [tworzenie az sieciowej](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Kojarzenie sieciowej grupy zabezpieczeń do *prywatnej* podsieć o [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet). W poniższym przykładzie *myNsgPrivate* sieciowej grupy zabezpieczeń *prywatnej* podsieci:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Tworzenie reguły zabezpieczeń o [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule). Reguła, która następuje po umożliwia dostęp ruchu wychodzącego do publicznych adresów IP przypisanych do usługi Azure Storage: 

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

Każda grupa zabezpieczeń sieci zawiera kilka [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules). Reguła, która następuje po zastępuje domyślną regułę zabezpieczeń, która umożliwia dostęp ruchu wychodzącego do wszystkich publicznych adresów IP. `destination-address-prefix "Internet"` Opcji nie zezwala na dostęp ruchu wychodzącego do wszystkich publicznych adresów IP. Poprzednie reguła zastępuje tej reguły, ze względu na wyższy priorytet, która zezwala na dostęp do publicznych adresów IP usługi Azure Storage.

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

Następująca reguła zezwala na ruch SSH ruch przychodzący do podsieci z dowolnego miejsca. Reguła zastępuje domyślną regułę zabezpieczeń, która zakazuje całego ruchu przychodzącego z Internetu. SSH jest dozwolone do podsieci, aby można było przetestować łączność w późniejszym kroku.

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

Kroki niezbędne do ograniczenia dostępu sieciowego do zasobów utworzonych za pomocą usług platformy Azure obsługujących punkty końcowe usługi różnią się w zależności od usługi. Zobacz dokumentację poszczególnych usług, aby poznać konkretne kroki dla każdej usługi. W dalszej części tego artykułu obejmuje kroki pozwalające ograniczające dostęp do konta usługi Azure Storage jako przykład.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Tworzenie konta usługi Azure storage za pomocą [Tworzenie konta magazynu az](/cli/azure/storage/account). Zastąp `<replace-with-your-unique-storage-account-name>` nazwą, która jest unikatowa dla wszystkich lokalizacji platformy Azure od 3 do 24 znaków długości, przy użyciu tylko cyfry i małe litery.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po utworzeniu konta magazynu, należy pobrać parametry połączenia dla konta magazynu w zmiennej o [az storage account show-connection-string](/cli/azure/storage/account). Ciąg połączenia jest używany do utworzenia udziału plików w późniejszym kroku.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Wyświetlanie zawartości zmiennej i zwróć uwagę na wartość dla **AccountKey** zwracany w danych wyjściowych, ponieważ jest ona używana w późniejszym kroku.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

Utwórz udział plików na koncie magazynu przy użyciu [Utwórz udział magazynu az](/cli/azure/storage/share). W późniejszym kroku tego udziału plików jest zainstalowany, aby upewnić się do niego dostęp w sieci.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Odmowa dostępu do całej sieci do konta magazynu

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranych sieci, należy zmienić domyślną akcję na *Odmów* z [aktualizacja konta magazynu az](/cli/azure/storage/account). Po odmówiono dostępu do sieci, konto magazynu nie są dostępne z dowolnej sieci.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Włączanie dostępu sieciowego z podsieci

Zezwalanie na dostęp sieciowy do konta magazynu z *prywatnej* podsieć o [az reguły sieci konta magazynu — Dodaj](/cli/azure/storage/account/network-rule).

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

Utwórz Maszynę wirtualną w *publicznych* podsieć o [tworzenie az vm](/cli/azure/vm). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej platformy AZURE wyświetli informacje podobne do poniższego przykładu: 

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

Zwróć uwagę na **publicznego adresu IP** zwróconych danych wyjściowych. Ten adres umożliwia dostęp do maszyny Wirtualnej z Internetu w późniejszym kroku.

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu, zwróć uwagę na **publicznego adresu IP** w dane wyjściowe zwracane. Ten adres umożliwia dostęp do maszyny Wirtualnej z Internetu w późniejszym kroku.

## <a name="confirm-access-to-storage-account"></a>Potwierdzanie dostępu do konta magazynu

Nawiąż połączenie *myVmPrivate* maszyny Wirtualnej. Zastąp  *\<publiczny adres IP >* publiczny adres IP Twojego *myVmPrivate* maszyny Wirtualnej.

```bash 
ssh <publicIpAddress>
```

Utwórz folder dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Instalowanie udziału plików platformy Azure do katalogu, w którym został utworzony. Przed uruchomieniem następującego polecenia, należy zastąpić `<storage-account-name>` nazwą konta i `<storage-account-key>` kluczem pobranym w [Tworzenie konta magazynu](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Pojawi się `user@myVmPrivate:~$` wiersza. Pomyślnie zainstalowany udział plików platformy Azure do *wolumin/mnt/MyAzureFileShare*.

Upewnij się, że maszyna wirtualna nie ma łączności wychodzącej do innych publicznych adresów IP:

```bash
ping bing.com -c 4
```

Nie otrzymasz żadnych odpowiedzi, ponieważ sieciowa grupa zabezpieczeń skojarzona z podsiecią *Private* nie zezwala na dostęp ruchu wychodzącego do publicznych adresów IP innych niż adresy przypisane do usługi Azure Storage.

Zamknij sesję SSH *myVmPrivate* maszyny Wirtualnej.

## <a name="confirm-access-is-denied-to-storage-account"></a>Potwierdzanie odmowy dostępu do konta magazynu

Użyj następującego polecenia, aby utworzyć sesję SSH z *myVmPublic* maszyny Wirtualnej. Zastąp `<publicIpAddress>` publiczny adres IP Twojego *myVmPublic* maszyny Wirtualnej: 

```bash 
ssh <publicIpAddress>
```

Utwórz katalog dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Podjęto próbę, aby zainstalować udział plików platformy Azure do katalogu, w którym został utworzony. W tym artykule przyjęto założenie, że wdrożyć najnowszą wersję Ubuntu. Jeśli używasz starszej wersji systemu Ubuntu, zobacz [instalowanie w systemie Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dodatkowe instrukcje dotyczące instalowania udziałów plików. Przed uruchomieniem następującego polecenia, należy zastąpić `<storage-account-name>` nazwą konta i `<storage-account-key>` kluczem pobranym w [Tworzenie konta magazynu](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Odmowa dostępu oraz otrzymasz `mount error(13): Permission denied` błąd, ponieważ *myVmPublic* maszyna wirtualna jest wdrożona w ramach *publicznych* podsieci. Podsieć *Public* nie ma punktu końcowego usługi obsługującego usługę Azure Storage, a konto magazynu zezwala tylko na dostęp sieciowy z podsieci *Private*, a nie z podsieci *Public*.

Zamknij sesję SSH *myVmPublic* maszyny Wirtualnej.

Z komputera, próba wyświetlenia akcji w ramach konta magazynu przy użyciu [az storage share lista](/cli/azure/storage/share?view=azure-cli-latest). Zastąp `<account-name>` i `<account-key>` przy użyciu nazwy konta magazynu i klucza z [Tworzenie konta magazynu](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Odmowa dostępu oraz otrzymasz *to żądanie nie ma autoryzacji do wykonania tej operacji* błąd, ponieważ komputer nie znajduje się w *prywatnej* podsieci *MyVirtualNetwork* sieci wirtualnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, należy użyć [usunięcie grupy az](/cli/azure) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule został włączony punkt końcowy usługi dla podsieci sieci wirtualnej. Wiesz teraz, że punkty końcowe usługi można włączyć dla zasobów wdrożonych w wielu usługach platformy Azure. Zostało utworzone konto usługi Azure Storage i dostęp sieciowy do konta magazynu został ograniczony tylko do zasobów w podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [Service endpoints overview (Omówienie punktów końcowych usługi)](virtual-network-service-endpoints-overview.md) i [Manage subnets (Zarządzanie podsieciami)](virtual-network-manage-subnet.md).

Jeśli masz wiele sieci wirtualnych w ramach Twojego konta, możesz chcieć połączyć ze sobą dwie sieci wirtualne, aby zasoby w każdej sieci wirtualnej mogły komunikować się ze sobą. Aby dowiedzieć się więcej, zobacz temat [łączenie sieci wirtualnych](tutorial-connect-virtual-networks-cli.md).
