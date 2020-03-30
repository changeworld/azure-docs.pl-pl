---
title: Ograniczanie eksfiltracji danych do usługi Azure Storage — interfejsu wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak ograniczyć i ograniczyć eksfiltrację danych sieci wirtualnej do zasobów usługi Azure Storage za pomocą zasad punktu końcowego usługi sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271183"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Zarządzanie eksfiltracją danych na kontach usługi Azure Storage za pomocą zasad punktu końcowego usługi sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Zasady punktu końcowego usługi sieci wirtualnej umożliwiają stosowanie kontroli dostępu do kont usługi Azure Storage z poziomu sieci wirtualnej za pośrednictwem punktów końcowych usługi. Jest to klucz do zabezpieczania obciążeń, zarządzania kontami magazynu są dozwolone i gdzie eksfiltracja danych jest dozwolona.
W tym artykule omówiono sposób wykonywania następujących zadań:

* Utwórz sieć wirtualną i dodaj podsieć.
* Włącz punkt końcowy usługi dla usługi Azure Storage.
* Utwórz dwa konta usługi Azure Storage i zezwalaj na dostęp do sieci z podsieci utworzonej powyżej.
* Utwórz zasady punktu końcowego usługi, aby zezwolić na dostęp tylko do jednego z kont magazynu.
* Wdrażanie maszyny wirtualnej (VM) w podsieci.
* Potwierdź dostęp do dozwolonego konta magazynu z podsieci.
* Potwierdzenie dostępu jest odmowa do konta magazynu niedozwolone z podsieci.

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi 

W tym przykładzie punkt końcowy usługi dla *microsoft.storage* jest tworzony dla podsieci *Private:* 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Ograniczanie dostępu do sieci do kont usługi Azure Storage

W tej sekcji wymieniono kroki, aby ograniczyć dostęp do sieci dla konta usługi Azure Storage z danej podsieci w sieci wirtualnej za pośrednictwem punktu końcowego usługi.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz dwa konta magazynu platformy Azure przy [tworzeniu konta magazynu az](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po utworzeniu kont magazynu pobierz parametry połączenia dla kont magazynu do zmiennej z [az storage account show-connection-string](/cli/azure/storage/account). Ciąg połączenia jest używany do tworzenia udziału plików w późniejszym kroku.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Wyświetl zawartość zmiennej i zanotuj wartość **klawisza AccountKey** zwróconego w danych wyjściowych, ponieważ jest on używany w późniejszym kroku.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

Utwórz udział plików na koncie magazynu za pomocą [az storage share create](/cli/azure/storage/share). W późniejszym kroku ten udział plików jest instalowany w celu potwierdzenia dostępu do sieci.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Odmów wszelkiego dostępu sieciowego do konta magazynu

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranych sieci, zmień akcję domyślną na *Odmów* z [aktualizacją konta magazynu az](/cli/azure/storage/account). Kiedy dostęp sieciowy jest blokowany, konto magazynu nie jest dostępne z żadnej sieci.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Włączanie dostępu do sieci z podsieci sieci wirtualnej

Zezwalaj na dostęp sieciowy do konta magazynu z podsieci *prywatnej* z [dodaniem reguły sieciowej konta magazynu AZ](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Stosowanie zasad w celu umożliwienia dostępu do prawidłowego konta magazynu

Zasady punktu końcowego usługi Azure są dostępne tylko dla usługi Azure Storage. Dlatego włączymy punkt końcowy usługi dla *microsoft.storage* w tej podsieci dla tej przykładowej konfiguracji.

Zasady punktu końcowego usługi są stosowane w punktach końcowych usługi. Rozpoczniemy od utworzenia zasad punktu końcowego usługi. Następnie utworzymy definicje zasad w ramach tych zasad dla kont usługi Azure Storage, które mają być umieszczane na białej liście dla tej podsieci

Tworzenie zasad punktu końcowego usługi

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Zapisz identyfikator URI zasobu dla dozwolonego konta magazynu w zmiennej. Przed wykonaniem poniższego polecenia * \<zastąp identyfikator subskrypcji>* rzeczywistą wartością identyfikatora subskrypcji.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Tworzenie & dodawanie definicji zasad umożliwiających zezwalanie na powyższe konto usługi Azure Storage do zasad punktu końcowego usługi

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

Aktualizowanie podsieci sieci wirtualnej w celu skojarzenia z nią zasad punktu końcowego usługi utworzonego w poprzednim kroku

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Sprawdzanie poprawności ograniczenia dostępu do kont usługi Azure Storage

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby przetestować dostęp sieciowy do konta magazynu, należy wdrożyć maszynę wirtualną w podsieci.

Utwórz maszynę wirtualną w podsieci *prywatnej* z [az vm create](/cli/azure/vm). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

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

### <a name="confirm-access-to-storage-account"></a>Potwierdzanie dostępu do konta magazynu

SSH do maszyny wirtualnej *myVmPrivate.* Zastąp * \<publicIpAddress>* publicznym adresem IP maszyny wirtualnej *myVmPrivate.*

```bash 
ssh <publicIpAddress>
```

Tworzenie folderu dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Zainstaluj udział plików platformy Azure w utworzonym katalogu. Przed wykonaniem polecenia poniżej zastąp * \<>klucz do przechowywania* klucza o wartości *AccountKey* od **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zostanie wyświetlony `user@myVmPrivate:~$` monit. Pomyślnie zainstalowany udział plików platformy Azure w */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Potwierdzanie odmowy dostępu do konta magazynu

Z tej samej maszyny Wirtualnej *myVmPrivate*, utworzyć katalog dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Spróbuj zainstalować udział plików platformy Azure z konta magazynu *notallowedstorageacc* do utworzonego katalogu. W tym artykule założono, że wdrożono najnowszą wersję Ubuntu. Jeśli używasz wcześniejszych wersji Ubuntu, zobacz [Mount on Linux,](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aby uzyskać dodatkowe instrukcje dotyczące montażu udziałów plików. 

Przed wykonaniem polecenia poniżej zastąp * \<>klucz do przechowywania* klucza o wartości *AccountKey* z **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Odmowa dostępu i zostanie `mount error(13): Permission denied` wyświetlony błąd, ponieważ to konto magazynu nie znajduje się na liście dozwolonych zasad punktu końcowego usługi zastosowanej do podsieci. 

Zamknij sesję SSH na maszynie *wirtualnej myVmPublic.*

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj [az group delete,](/cli/azure) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zastosowano zasady punktu końcowego usługi za pośrednictwem punktu końcowego usługi sieci wirtualnej platformy Azure do usługi Azure Storage. Utworzono konta usługi Azure Storage i ograniczony dostęp do sieci tylko do niektórych kont magazynu (a tym samym odmówiono innych) z podsieci sieci wirtualnej. Aby dowiedzieć się więcej o zasadach punktu końcowego usług, zobacz [Omówienie zasad dotyczących punktów końcowych usług](virtual-network-service-endpoint-policies-overview.md).
