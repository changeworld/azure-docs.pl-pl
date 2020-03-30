---
title: Filtrowanie ruchu sieciowego — interfejs wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak filtrować ruch sieciowy do podsieci, z sieciową grupą zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: 72c8b4d57b5064af34665cff1386179e62324938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235074"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure

Ruch sieciowy przychodzący do podsieci sieci wirtualnej i wychodzący z niej możesz filtrować za pomocą sieciowej grupy zabezpieczeń. Sieciowe grupy zabezpieczeń zawierają reguły zabezpieczeń, które filtrują ruch sieciowy według adresów IP, portów i protokołów. Reguły zabezpieczeń są stosowane do zasobów wdrożonych w podsieci. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieciowej grupy zabezpieczeń i reguł zabezpieczeń
* Tworzenie sieci wirtualnej i kojarzenie sieciowej grupy zabezpieczeń z podsiecią
* Wdrażanie maszyn wirtualnych w podsieci
* Testowanie filtrów ruchu

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera reguły zabezpieczeń. Reguły zabezpieczeń określają źródło i lokalizację docelową. Źródła i lokalizacje docelowe mogą być grupami zabezpieczeń aplikacji.

### <a name="create-application-security-groups"></a>Tworzenie grup zabezpieczeń aplikacji

Najpierw utwórz grupę zasobów dla wszystkich zasobów utworzonych w tym artykule z [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz grupę zabezpieczeń aplikacji z [asg sieci az .](/cli/azure/network/asg) Grupa zabezpieczeń aplikacji umożliwia grupowanie serwerów o podobnych wymaganiach w zakresie filtrowania portów. Poniższy przykład tworzy dwie grupy zabezpieczeń aplikacji.

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

Utwórz grupę zabezpieczeń sieci [z nsg sieci az create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Tworzenie reguł zabezpieczeń

Utwórz regułę zabezpieczeń z [regułą nsg sieci az .](/cli/azure/network/nsg/rule) Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myWebServers* przez porty 80 i 443:

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

Poniższy przykład tworzy regułę, która umożliwia ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myMgmtServers* nad portem 22:

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

W tym artykule SSH (port 22) jest narażony na działanie Internetu dla maszyny Wirtualnej *myAsgMgmtServers.* W środowiskach produkcyjnych zamiast udostępniania portu 22 w Internecie zaleca się łączenie się z zasobami platformy Azure, którymi chcesz zarządzać za pomocą [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub połączenia sieci [prywatnej.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Dodaj podsieć do sieci wirtualnej z [utworzeniem podsieci sieci AZ .](/cli/azure/network/vnet/subnet) Poniższy przykład dodaje podsieć o nazwie *mySubnet* do sieci wirtualnej i kojarzy z nią sieciową grupę zabezpieczeń *myNsg*:

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

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną, która będzie służyć jako serwer internetowy. Ta `--asgs myAsgWebServers` opcja powoduje, że platforma Azure sprawia, że interfejs sieciowy, który tworzy dla maszyny Wirtualnej, jest członkiem grupy zabezpieczeń aplikacji *myAsgWebServers.*

Opcja `--nsg ""` jest określona, aby uniemożliwić platformie Azure tworzenie domyślnej sieciowej grupy zabezpieczeń dla interfejsu sieciowego tworzonego przez platformę Azure podczas tworzenia maszyny Wirtualnej. Aby usprawnić ten artykuł, używane jest hasło. Klucze są zwykle używane w wdrożeniach produkcyjnych. Jeśli używasz kluczy, należy również skonfigurować przekazywanie agenta SSH dla pozostałych kroków. Aby uzyskać więcej informacji, zobacz dokumentację klienta SSH. Zastąp `<replace-with-your-password>` w poniższym poleceniu wybranym hasłem.

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej zwracane jest dane wyjściowe podobne do następującego przykładu: 

```output
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

Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany do uzyskiwania dostępu do maszyny Wirtualnej z Internetu w późniejszym kroku.  Utwórz maszynę wirtualną, która będzie służyć jako serwer zarządzania:

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny Wirtualnej, należy zwrócić uwagę **publicIpAddress** w zwróconych danych wyjściowych. Ten adres jest używany do uzyskiwania dostępu do maszyny Wirtualnej w następnym kroku. Nie przechodź do następnego kroku, dopóki platforma Azure nie ukończy tworzenia maszyny wirtualnej.

## <a name="test-traffic-filters"></a>Testowanie filtrów ruchu

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną *myVmMgmt.* Zastąp * \<publicIpAddress>* publicznym adresem IP maszyny Wirtualnej. W powyższym przykładzie adres IP to *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o podanie hasła wprowadź hasło wprowadzone w [obszarze Tworzenie maszyn wirtualnych](#create-virtual-machines).

Połączenie zakończy się pomyślnie, ponieważ port 22 jest dozwolony przychodzących z Internetu do grupy zabezpieczeń aplikacji *myAsgMgmtServers,* w których znajduje się interfejs sieciowy dołączony do maszyny Wirtualnej *myVmMgmt.*

Użyj następującego polecenia do SSH do *myVmWeb* VM z *myVmMgmt* VM:

```bash 
ssh azureuser@myVmWeb
```

Połączenie powiedzie się, ponieważ domyślne reguły zabezpieczeń w każdej sieciowej grupie zabezpieczeń zezwalają na ruch na wszystkich portach pomiędzy wszystkimi adresami IP w sieci wirtualnej. Nie można SSH do *myVmWeb* VM z Internetu, ponieważ reguła zabezpieczeń dla *myAsgWebServers* nie zezwala na port 22 przychodzące z Internetu.

Użyj następujących poleceń, aby zainstalować serwer sieci web nginx na maszynie wirtualnej *myVmWeb:*

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Maszyna *wirtualna myVmWeb* jest dozwolona wychodząco do Internetu w celu pobrania nginx, ponieważ domyślna reguła zabezpieczeń zezwala na cały ruch wychodzący do Internetu. Zamknij sesję *myVmWeb* SSH, która `username@myVmMgmt:~$` pozostawia cię w punkcie monitu maszyny wirtualnej *myVmMgmt.* Aby pobrać ekran powitalny nginx z maszyny wirtualnej *myVmWeb,* wprowadź następujące polecenie:

```bash
curl myVmWeb
```

Wyloguj się z maszyny Wirtualnej *myVmMgmt.* Aby potwierdzić, że można uzyskać dostęp do serwera sieci `curl <publicIpAddress>` web *myVmWeb* spoza platformy Azure, wprowadź z własnego komputera. Połączenie zakończy się pomyślnie, ponieważ port 80 jest dozwolony przychodzących z Internetu do grupy zabezpieczeń aplikacji *myAsgWebServers,* w których znajduje się interfejs sieciowy dołączony do maszyny Wirtualnej *myVmWeb.*

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj [az group delete,](/cli/azure/group) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono grupę zabezpieczeń sieci i skojarzyno ją z podsiecią sieci wirtualnej. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](security-overview.md) oraz [Manage a network security group (Zarządzanie sieciową grupą zabezpieczeń)](manage-network-security-group.md).

Platforma Azure domyślnie kieruje ruch pomiędzy podsieciami. Zamiast tego możesz przykładowo skierować ruch pomiędzy podsieciami przez maszynę wirtualną, która będzie służyć jako zapora. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie tabeli tras](tutorial-create-route-table-cli.md).
