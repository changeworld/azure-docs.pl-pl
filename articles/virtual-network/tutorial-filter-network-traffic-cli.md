---
title: Filtrowanie ruchu sieciowego — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule dowiesz się, jak filtrować ruch sieciowy do podsieci z sieciową grupą zabezpieczeń, za pomocą wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: a1ade6e823201419c3a742a36c66a50a9dc09976
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728799"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrowanie ruchu sieciowego z sieciową grupą zabezpieczeń przy użyciu wiersza polecenia platformy Azure

Ruch sieciowy przychodzący do podsieci sieci wirtualnej i wychodzący z niej możesz filtrować za pomocą sieciowej grupy zabezpieczeń. Sieciowe grupy zabezpieczeń zawierają reguły zabezpieczeń, które filtrują ruch sieciowy według adresów IP, portów i protokołów. Reguły zabezpieczeń są stosowane do zasobów wdrożonych w podsieci. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieciowej grupy zabezpieczeń i reguł zabezpieczeń
* Tworzenie sieci wirtualnej i kojarzenie sieciowej grupy zabezpieczeń z podsiecią
* Wdrażanie maszyn wirtualnych w podsieci
* Testowanie filtrów ruchu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera reguły zabezpieczeń. Reguły zabezpieczeń określają źródło i lokalizację docelową. Źródła i lokalizacje docelowe mogą być grupami zabezpieczeń aplikacji.

### <a name="create-application-security-groups"></a>Tworzenie grup zabezpieczeń aplikacji

Najpierw utwórz grupę zasobów dla wszystkich zasobów utworzonych w tym artykule [Tworzenie grupy az](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Tworzenie grupy zabezpieczeń aplikacji przy użyciu [tworzenie az sieci w grupie asg](/cli/azure/network/asg). Grupa zabezpieczeń aplikacji umożliwia grupowanie serwerów o podobnych wymaganiach w zakresie filtrowania portów. Poniższy przykład tworzy dwie grupy zabezpieczeń aplikacji.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń z [tworzenie az sieciowej](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Tworzenie reguł zabezpieczeń

Utwórz regułę zabezpieczeń przy użyciu [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule). Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myWebServers* przez porty 80 i 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu do *myMgmtServers* grupy zabezpieczeń aplikacji za pośrednictwem portu 22:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

W tym artykule SSH (port 22) jest uwidaczniany w Internecie *myAsgMgmtServers* maszyny Wirtualnej. W środowiskach produkcyjnych zamiast uwidaczniania portu 22 z Internetem, zalecane jest, że łączysz się z zasobami platformy Azure, które mają być zarządzane przy użyciu [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [prywatnej](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) połączenia sieciowego.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Dodaj podsieć do sieci wirtualnej z [az podsieci sieci wirtualnej Utwórz](/cli/azure/network/vnet/subnet). Poniższy przykład dodaje podsieć o nazwie *mySubnet* do sieci wirtualnej i kojarzy z nią sieciową grupę zabezpieczeń *myNsg*:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby umożliwić weryfikację filtrowania ruchu w kolejnym kroku. 

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną, która będzie służyć jako serwer internetowy. `--asgs myAsgWebServers` Opcja powoduje, że Azure, aby interfejs sieciowy, tworzy dla maszyny Wirtualnej jest członkiem *myAsgWebServers* grupy zabezpieczeń aplikacji.

`--nsg ""` Określono opcję, aby uniemożliwić tworzenie domyślną sieciową grupę zabezpieczeń dla interfejsu sieciowego, platforma Azure utworzy podczas tworzenia maszyny Wirtualnej platformy Azure. Aby uprościć ten artykuł, używane jest hasło. Klucze są zazwyczaj używane we wdrożeniach produkcyjnych. Jeśli używasz kluczy, należy również skonfigurować agenta przekazywania SSH dla pozostałych kroków. Aby uzyskać więcej informacji zobacz dokumentację używanego klienta SSH. Zastąp `<replace-with-your-password>` w następującym poleceniu za pomocą wybrane hasło.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej, zwracana jest dane wyjściowe podobne do poniższego przykładu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość adresu **publicIpAddress**. Ten adres umożliwia dostęp do maszyny Wirtualnej z Internetu w późniejszym kroku.  Utwórz maszynę wirtualną, która będzie służyć jako serwer zarządzania:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej Zanotuj **publicznego adresu IP** zwróconych danych wyjściowych. Ten adres umożliwia dostęp do maszyny Wirtualnej w następnym kroku. Nie przechodź do następnego kroku, dopóki platforma Azure nie ukończy tworzenia maszyny wirtualnej.

## <a name="test-traffic-filters"></a>Testowanie filtrów ruchu

Użyj polecenia poniżej, aby utworzyć sesję SSH z *myVmMgmt* maszyny Wirtualnej. Zastąp  *\<publiczny adres IP >* z publicznym adresem IP swojej maszyny wirtualnej. W powyższym przykładzie adres IP jest *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o hasło, wprowadź hasło wprowadzone w [tworzyć maszyny wirtualne](#create-virtual-machines).

Połączenie powiedzie się, ponieważ port 22 zezwala na ruch przychodzący z Internetu do *myAsgMgmtServers* grupy zabezpieczeń aplikacji, które interfejs sieciowy dołączony do *myVmMgmt* maszyna wirtualna jest w.

Użyj następującego polecenia SSH *myVmWeb* maszynę Wirtualną z *myVmMgmt* maszyny Wirtualnej:

```bash 
ssh azureuser@myVmWeb
```

Połączenie powiedzie się, ponieważ domyślne reguły zabezpieczeń w każdej sieciowej grupie zabezpieczeń zezwalają na ruch na wszystkich portach pomiędzy wszystkimi adresami IP w sieci wirtualnej. Nie SSH *myVmWeb* maszynę Wirtualną z Internetu, ponieważ reguły zabezpieczeń *myAsgWebServers* nie zezwalaj na port 22 dla ruchu przychodzącego z Internetu.

Użyj następujących poleceń, aby zainstalować serwer internetowy nginx na *myVmWeb* maszyny Wirtualnej:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

*MyVmWeb* maszyny Wirtualnej jest dozwolony ruch wychodzący do Internetu, można pobrać serwera nginx, ponieważ domyślna reguła zabezpieczeń zezwala na ruch wychodzący do Internetu. Zakończ *myVmWeb* sesji SSH, co spowoduje pozostawienie w `username@myVmMgmt:~$` monitu o *myVmMgmt* maszyny Wirtualnej. Można pobrać ekran powitalny nginx z *myVmWeb* maszynę Wirtualną, wprowadź następujące polecenie:

```bash
curl myVmWeb
```

Wyloguj się z *myVmMgmt* maszyny Wirtualnej. Aby upewnić się, że masz dostęp *myVmWeb* sieci web serwera z spoza platformy Azure, wprowadź `curl <publicIpAddress>` ze swojego komputera. Połączenie powiedzie się, ponieważ port 80 zezwala na ruch przychodzący z Internetu do *myAsgWebServers* grupy zabezpieczeń aplikacji, które interfejs sieciowy dołączony do *myVmWeb* maszyna wirtualna jest w.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, należy użyć [usunięcie grupy az](/cli/azure/group) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono sieciową grupę zabezpieczeń i skojarzono ją z podsiecią sieci wirtualnej. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](security-overview.md) oraz [Manage a network security group (Zarządzanie sieciową grupą zabezpieczeń)](manage-network-security-group.md).

Platforma Azure domyślnie kieruje ruch pomiędzy podsieciami. Zamiast tego możesz przykładowo skierować ruch pomiędzy podsieciami przez maszynę wirtualną, która będzie służyć jako zapora. Aby dowiedzieć się więcej, zobacz temat [Utwórz tabelę tras](tutorial-create-route-table-cli.md).
