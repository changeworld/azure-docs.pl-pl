---
title: Tworzenie sieci wirtualnej platformy Azure z wieloma podsieciami - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną z wieloma podsieciami przy użyciu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880591"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Tworzenie sieci wirtualnej z wieloma podsieciami przy użyciu wiersza polecenia platformy Azure

Sieć wirtualna umożliwia kilka typów zasobów platformy Azure do komunikowania się z Internetem i prywatnie ze sobą. Tworzenie wielu podsieci w sieci wirtualnej umożliwia do segmentu sieci, dzięki czemu można filtrować lub sterowania przepływem ruchu między podsieciami. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie podsieci
> * Test łączności sieciowej między maszynami wirtualnymi

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli.md). 

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork* z prefiksem adresu *10.0.0.0/16*. Polecenie tworzy jedną podsieć o nazwie *publicznego*, prefiksie adresu *10.0.0.0/24*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Ponieważ nie określono lokalizacji w poprzednim poleceniu, platforma Azure tworzy sieci wirtualnej w tej samej lokalizacji który *myResourceGroup* grupa zasobów istnieje w. **Prefiksy adresów** i **prefiks podsieci** są określone w notacji CIDR. Prefiks adresu określonego obejmuje 10.0.0.0-10.0.255.254 adresów IP. Prefiks określonej podsieci musi znajdować się w prefiksie adresu zdefiniowana dla sieci wirtualnej. Azure DHCP przypisuje adresy IP z prefiksem adresu podsieci z zasobami wdrożonymi w podsieci. Azure przypisuje 10.0.0.4-10.0.0.254 adresy tylko z zasobami wdrożonymi w **publicznego** podsieci, ponieważ Azure rezerwuje pierwsze cztery adresów (10.0.0.0-10.0.0.3 podsieci, w tym przykładzie) i ostatnich adresów () 10.0.0.255 podsieci, w tym przykładzie) w każdej podsieci.

## <a name="create-a-subnet"></a>Tworzenie podsieci

Utwórz podsieć o [Utwórz podsieć sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Poniższy przykład tworzy podsieć o nazwie *prywatnej* w *myVirtualNetwork* sieci wirtualnej z prefiksem adresu *10.0.1.0/24*. Prefiks adresu musi znajdować się w prefiksie adresu zdefiniowana dla sieci wirtualnej i nie może nakładać się prefiksu adresu innych podsieci w sieci wirtualnej.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

Przed wdrożeniem sieci wirtualnych platformy Azure i podsieci w środowisku produkcyjnym, zalecane jest, że należy dokładnie zapoznać się z przestrzeni adresowej [zagadnienia](manage-virtual-network.md#create-a-virtual-network) i [limity sieci wirtualnej](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Gdy zasoby są wdrażane na podsieci, niektóre sieci wirtualnej i zmiany podsieci, takie jak zmiana zakresów adresów, może wymagać ponownego wdrażania istniejących zasobów platformy Azure, wdrażana w obrębie podsieci.

## <a name="test-network-communication"></a>Test łączności sieciowej

Sieć wirtualna umożliwia kilka typów zasobów platformy Azure do komunikowania się z Internetem i prywatnie ze sobą. Jeden typ zasobów, które można wdrożyć w sieci wirtualnej jest maszyną wirtualną. Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby można było sprawdzić komunikację sieciową między nimi i Internetu w kolejnym kroku.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVmWeb* w *publicznego* podsieci. `--no-wait` Parametru zapewnia Azure można wykonać polecenia w tle, dzięki czemu można kontynuować do następnego polecenia. Usprawniające w tym samouczku użyto hasła. Klucze są zazwyczaj używane w przypadku wdrożeń produkcyjnych. Jeśli używasz kluczy, należy skonfigurować agenta przekazywania do wykonaj pozostałe kroki SSH. Aby uzyskać więcej informacji na temat agenta przekazywania SSH zobacz dokumentację klienta SSH. Zastąp `<replace-with-your-password>` w poniższym poleceniu hasłem wybrane.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Azure automatycznie przypisać 10.0.0.4 jako prywatny adres IP maszyny wirtualnej, ponieważ 10.0.0.4 jest pierwszy dostępny adres IP w *publicznego* podsieci. 

Utwórz maszynę wirtualną w *prywatnej* podsieci.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
Maszyna wirtualna ma kilka minut na utworzenie. Po utworzeniu maszyny wirtualnej Azure CLI zwraca dane wyjściowe podobne do poniższego przykładu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

W przykładowe dane wyjściowe, zobaczysz, że **elementu privateIpAddress** jest *10.0.1.4*. Azure utworzony [interfejsu sieciowego](virtual-network-network-interface.md), on dołączony do maszyny wirtualnej, przypisany prywatny adres IP interfejsu sieciowego i **macAddress**. Azure DHCP automatycznie przypisać 10.0.1.4 do interfejsu sieciowego, ponieważ jest pierwszy dostępny adres IP w *prywatnej* podsieci. Prywatne adresy IP i MAC pozostają przypisane do interfejsu sieciowego, aż do usunięcia interfejsu sieciowego. 

Zwróć uwagę na **publicznego adresu IP**. Ten adres jest używany na dostęp do maszyny wirtualnej z Internetu w kolejnym kroku. Jeśli maszyna wirtualna nie musi mieć publiczny adres IP przypisane do niej, Azure przypisuje publicznego adresu IP do każdej maszyny wirtualnej, które tworzysz, domyślnie. Aby komunikować się z Internetu na maszynę wirtualną, publiczny adres IP musi można przypisać do maszyny wirtualnej. Wszystkie maszyny wirtualne mogą komunikować się wychodzące z Internetem, czy przypisano publiczny adres IP do maszyny wirtualnej. Aby dowiedzieć się więcej na temat połączenia wychodzące Internet na platformie Azure, zobacz [połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Maszyny wirtualne utworzone w tym artykule istnieje [interfejs sieciowy](virtual-network-network-interface.md) z jednym adresem IP, który jest przypisywany dynamicznie do interfejsu sieciowego. Po wdrożeniu maszyny Wirtualnej, można [dodać wiele prywatnych i publicznych adresów IP lub zmień metoda przypisywania adresów IP statycznej](virtual-network-network-interface-addresses.md#add-ip-addresses). Możesz [dodać interfejsów sieciowych](virtual-network-network-interface-vm.md#vm-add-nic), w granicach obsługiwane przez [rozmiar maszyny Wirtualnej](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) wybranym podczas tworzenia maszyny wirtualnej. Możesz również [Włącz wirtualizację we/wy pojedynczego elementu głównego (SR-IOV)](create-vm-accelerated-networking-cli.md) dla maszyny Wirtualnej, ale tylko wtedy, gdy tworzenie maszyny Wirtualnej z rozmiar maszyny Wirtualnej, która obsługuje możliwości.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Komunikację między maszynami wirtualnymi i z Internetu

Użyj następującego polecenia, aby utworzyć sesję SSH z *myVmMgmt* maszyny wirtualnej. Zastąp `<publicIpAddress>` z publicznym adresem IP maszyny wirtualnej. W poprzednim przykładzie, publiczny adres IP jest *13.90.242.231*. Po wyświetleniu monitu o podanie hasła, wprowadź hasło w [Tworzenie maszyn wirtualnych](#create-virtual-machines).

```bash 
ssh azureuser@<publicIpAddress>
```

Ze względów bezpieczeństwa jest często, aby ograniczyć liczbę maszyn wirtualnych, które można zdalnie łączyć się w sieci wirtualnej. W tym samouczku *myVmMgmt* maszyny wirtualnej jest używany do zarządzania *myVmWeb* maszyny wirtualnej w sieci wirtualnej. Użyj następującego polecenia do SSH *myVmWeb* maszynę wirtualną z *myVmMgmt* maszyny wirtualnej:

```bash 
ssh azureuser@myVmWeb
```

Do komunikacji *myVmMgmt* maszynę wirtualną z *myVmWeb* maszyny wirtualnej, wprowadź następujące polecenie w wierszu polecenia:

```
ping -c 4 myvmmgmt
```

Pojawi się dane wyjściowe podobne do następujących przykładowe dane wyjściowe:
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
Można stwierdzić, że adres *myVmMgmt* 10.0.1.4 jest maszyny wirtualnej. 10.0.1.4 był pierwszy dostępny adres IP z zakresu adresów *prywatnej* podsieci, która została wdrożona *myVmMgmt* maszynę wirtualną w poprzednim kroku.  Zobaczysz, że w pełni kwalifikowaną nazwę maszyny wirtualnej jest *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*. Chociaż *hxehizax3z1udjnrx1r4gr30pg* część nazwy domeny różni się dla maszyny wirtualnej, pozostałej części nazwy domeny są takie same. Domyślnie wszystkie maszyny wirtualne Azure używają domyślna usługa Azure DNS. Wszystkie maszyny wirtualne sieci wirtualnej można rozpoznawania nazw innych maszyn wirtualnych w tej samej sieci wirtualnej przy użyciu usługi DNS domyślne platformy Azure. Zamiast przy użyciu usługi DNS domyślne platformy Azure, możesz użyć serwera DNS lub możliwości prywatnej domeny usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) lub [przy użyciu usługi Azure DNS dla domen prywatnej](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Użyj następujących poleceń do zainstalowania na serwerze sieci web nginx *myVmWeb* maszyny wirtualnej:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Po zainstalowaniu nginx, Zamknij *myVmWeb* sesji SSH, co spowoduje pozostawienie w wierszu *myVmMgmt* maszyny wirtualnej. Wprowadź następujące polecenie, aby pobrać ekran powitalny nginx z *myVmWeb* maszyny wirtualnej.

```bash
curl myVmWeb
```

Ekran powitalny nginx są zwracane.

Zamknij sesję SSH z *myVmMgmt* maszyny wirtualnej.

Podczas tworzenia Azure *myVmWeb* maszynę wirtualną, publiczny adres IP o nazwie *myVmWebPublicIP* została także utworzona i przypisana do maszyny wirtualnej. Pobierz publiczny adres z przypisaną Azure [az sieci ip publicznego Pokaż](/cli/azure/network/public-ip#az_network_public_ip_show).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

Z tego komputera, wprowadź następujące polecenie, zastępując `<publicIpAddress>` z adresem zwrócony z poprzedniego polecenia:

```bash
curl <publicIpAddress>
```

Próba curl ekran powitalny nginx z tego komputera nie powiedzie się. Próba nie powiedzie się, ponieważ gdy maszyny wirtualne zostały wdrożone, Azure utworzyć grupę zabezpieczeń sieci dla każdej maszyny wirtualnej domyślnie. 

Grupa zabezpieczeń sieci zawiera zasady zabezpieczeń, które dozwolonych lub zablokowanych dla ruchu przychodzącego i wychodzącego ruchu sieciowego przez port i adres IP. Domyślne grupy zabezpieczeń sieci utworzone Azure umożliwia komunikację za pośrednictwem wszystkie porty między zasobami w tej samej sieci wirtualnej. Dla maszyn wirtualnych systemu Linux domyślna grupa zabezpieczeń sieci nie zezwala na cały ruch przychodzący z Internetu przez wszystkie porty, Zaakceptuj portu TCP 22 (SSH). W związku z tym domyślnie można również utworzyć sesję SSH bezpośrednio do *myVmWeb* maszyny wirtualnej z Internetu, nawet jeśli nie możesz port 22 Otwórz na serwerze sieci web. Ponieważ `curl` polecenia komunikuje się za pośrednictwem portu 80, komunikacja nie powiedzie się z Internetu ponieważ nie istnieje żadna reguła w domyślnej grupie zabezpieczeń sieci zezwala na ruch za pośrednictwem portu 80.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) można usunąć grupy zasobów i wszystkie zasoby zawiera.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wdrażania sieci wirtualnej z wieloma podsieciami. Również wiesz, że podczas tworzenia maszyny wirtualnej systemu Linux Azure tworzy interfejs sieciowy on dołączony do maszyny wirtualnej i tworzy sieciową grupę zabezpieczeń, który tylko zezwala na ruch przez port 22, z Internetu. Przejście do dalej samouczkiem, aby dowiedzieć się, jak do filtrowania ruchu sieciowego do podsieci, a nie do poszczególnych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego do podsieci](./virtual-networks-create-nsg-arm-cli.md)
