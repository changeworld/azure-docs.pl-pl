---
title: Filtrowanie ruchu sieciowego - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się jak filtrować ruch sieciowy do podsieci, z sieciową grupą zabezpieczeń, za pomocą wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: dfda95d2340d7dd57ac7b4d7ed7b0665c8e9294e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrowanie ruchu sieciowego za pomocą grupy zabezpieczeń sieci przy użyciu wiersza polecenia platformy Azure

Można filtrować ruchu sieciowego przychodzącego i wychodzącego z podsieci sieci wirtualnej z sieciową grupą zabezpieczeń. Grupy zabezpieczeń sieci zawiera zasady zabezpieczeń, które filtrowania ruchu sieciowego według adresu IP, portu i protokołu. Reguły zabezpieczeń są stosowane do zasobów wdrożone w podsieci. W tym artykule dowiesz się, jak:

* Tworzenie zasad grupy i zabezpieczeń zabezpieczenia sieci
* Tworzenie sieci wirtualnej i skojarzenia sieciowej grupy zabezpieczeń do podsieci
* Wdrażanie maszyn wirtualnych (VM) do podsieci
* Filtry ruchu testu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli do zainstalowania i używania interfejsu wiersza polecenia lokalnie, w tym artykule, wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Grupa zabezpieczeń sieci zawiera zasady zabezpieczeń. Reguły zabezpieczeń Określ źródłowym i docelowym. Grupy zabezpieczeń aplikacji mogą być źródeł i miejsc docelowych.

### <a name="create-application-security-groups"></a>Utwórz grupy zabezpieczeń aplikacji

Najpierw utwórz grupę zasobów dla wszystkich zasobów, które są tworzone w tym artykule i [Tworzenie grupy az](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupy zasobów w *eastus* lokalizacji: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Tworzenie grupy zabezpieczeń aplikacji z [tworzenie asg sieci az](/cli/azure/network/asg#az_network_asg_create). Grupa zabezpieczeń aplikacji umożliwia utworzenie grupy serwerów z portem podobne wymagania filtrowania. Poniższy przykład tworzy dwie grupy zabezpieczeń aplikacji.

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

Utwórz grupę zabezpieczeń sieci z [utworzyć nsg sieci az](/cli/azure/network/nsg#az_network_nsg_create). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Tworzenie reguły zabezpieczeń

Utwórz regułę zabezpieczeń z [Tworzenie reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu, aby *myWebServers* grupy zabezpieczeń aplikacji przez porty 80 i 443:

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

Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu, aby *myMgmtServers* grupy zabezpieczeń aplikacji za pośrednictwem portu 22:

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

W tym artykule SSH (port 22) jest uwidoczniony w Internecie dla *myAsgMgmtServers* maszyny Wirtualnej. W środowiskach produkcyjnych, zamiast udostępnianie port 22 z Internetem, zaleca się nawiązać zasobów platformy Azure, które mają być zarządzane przy użyciu [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [prywatnej](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) połączenie sieciowe.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy wirtualny o nazwie *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Dodaj podsieć do sieci wirtualnej z [Utwórz podsieć sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). W poniższym przykładzie dodano podsieć o nazwie *mySubnet* sieć wirtualną i skojarzy *myNsg* sieciowej grupy zabezpieczeń do niej:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby móc weryfikować ruchu filtrowanie w kolejnym kroku. 

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy maszynę Wirtualną, która będzie służyć jako serwer sieci web. `--asgs myAsgWebServers` Opcja powoduje, że Azure interfejsu sieciowego, tworzy dla maszyny Wirtualnej jest członkiem *myAsgWebServers* grupy zabezpieczeń aplikacji.

`--nsg ""` Określono opcję, aby uniemożliwić tworzenie domyślnej grupy zabezpieczeń sieci dla interfejsu sieciowego, system Azure utworzy podczas tworzenia maszyny Wirtualnej Azure. Aby usprawnić w tym artykule, użyto hasła. Klucze są zazwyczaj używane w przypadku wdrożeń produkcyjnych. Użycie kluczy, należy również skonfigurować agenta przekazywania SSH dla pozostałych kroków. Aby uzyskać więcej informacji zobacz dokumentację klienta SSH. Zastąp `<replace-with-your-password>` w poniższym poleceniu hasłem wybrane.

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

Maszyna wirtualna ma kilka minut na utworzenie. Po utworzeniu maszyny Wirtualnej, jest zwracany dane wyjściowe podobne do poniższego przykładu: 

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

Zwróć uwagę na **publicznego adresu IP**. Ten adres jest używany na dostęp do maszyny Wirtualnej z Internetu w kolejnym kroku.  Utwórz maszynę Wirtualną jako serwera zarządzania:

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

Maszyna wirtualna ma kilka minut na utworzenie. Po utworzeniu maszyny Wirtualnej, należy zwrócić uwagę **publicznego adresu IP** zwróconych danych wyjściowych. Ten adres jest używany do maszyny Wirtualnej w następnym kroku. Nie Kontynuuj do następnego kroku, dopóki platforma Azure zakończy tworzenie maszyny Wirtualnej.

## <a name="test-traffic-filters"></a>Filtry ruchu testu

Użyj polecenia stosowanej do utworzenia sesji SSH z *myVmMgmt* maszyny Wirtualnej. Zastąp *<publicIpAddress>* z publicznym adresem IP w sieci maszyny wirtualnej. W powyższym przykładzie adres IP jest *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o podanie hasła, wprowadź hasło w [Tworzenie maszyn wirtualnych](#create-virtual-machines).

Połączenie powiedzie się, ponieważ port 22 jest dozwolone przychodzące z Internetu, aby *myAsgMgmtServers* aplikacji grupy zabezpieczeń, który interfejs sieciowy jest dołączony do *myVmMgmt* maszyna wirtualna jest w.

Użyj następującego polecenia do SSH *myVmWeb* maszyny Wirtualnej z *myVmMgmt* maszyny Wirtualnej:

```bash 
ssh azureuser@myVmWeb
```

Połączenie powiedzie się, ponieważ domyślną regułę zabezpieczeń w każdej grupie zabezpieczeń sieci zezwala na ruch przez wszystkie porty między wszystkich adresów IP w sieci wirtualnej. Nie można wykonać SSH *myVmWeb* maszyny Wirtualnej z Internetu ponieważ reguły zabezpieczeń dla *myAsgWebServers* nie zezwala na port 22 przychodzące z Internetu.

Użyj następujących poleceń do zainstalowania na serwerze sieci web nginx *myVmWeb* maszyny Wirtualnej:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

*MyVmWeb* maszyna wirtualna może wychodzące z Internetem, aby pobrać nginx, ponieważ domyślną regułę zabezpieczeń zezwala na ruch wychodzący do Internetu. Zakończ *myVmWeb* sesji SSH, co spowoduje pozostawienie w `username@myVmMgmt:~$` Monituj o *myVmMgmt* maszyny Wirtualnej. Można pobrać ekran powitalny nginx z *myVmWeb* maszyny Wirtualnej, wprowadź następujące polecenie:

```bash
curl myVmWeb
```

Wyloguj się z *myVmMgmt* maszyny Wirtualnej. Aby upewnić się, że masz dostęp *myVmWeb* sieci web serwera z poza platformą Azure, wprowadź `curl <publicIpAddress>` ze swojego komputera. Połączenie powiedzie się, ponieważ port 80 jest dozwolone przychodzące z Internetu, aby *myAsgWebServers* aplikacji grupy zabezpieczeń, który interfejs sieciowy jest dołączony do *myVmWeb* maszyna wirtualna jest w.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) można usunąć grupy zasobów i wszystkie zasoby zawiera.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzyć grupę zabezpieczeń sieci i jego skojarzone z podsiecią sieci wirtualnej. Aby dowiedzieć się więcej na temat grup zabezpieczeń sieci, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md) i [Zarządzanie sieciowej grupy zabezpieczeń](virtual-network-manage-nsg-arm-ps.md).

Azure tras ruchu między podsieciami domyślnie. Zamiast tego można kierować ruchem między podsieciami przez maszynę Wirtualną, uznawany za zaporą, np. Aby dowiedzieć się więcej, zobacz temat [utworzyć tabelę tras](tutorial-create-route-table-cli.md).