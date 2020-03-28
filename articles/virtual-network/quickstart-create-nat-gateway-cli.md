---
title: 'Szybki start: tworzenie bramy NAT — narzędzie interfejsu wiersza polecenia platformy Azure'
titlesuffix: Azure Virtual Network NAT
description: Ten przewodnik Szybki start pokazuje, jak utworzyć bramę NAT przy użyciu interfejsu wiersza polecenia platformy Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 9402960927f56092e226ab81bd3e6ede0cf6a52d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202200"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Szybki start: tworzenie bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki start pokazuje, jak korzystać z usługi Azure Virtual Network NAT. Utworzysz bramę NAT, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ten samouczek można wykonać przy użyciu usługi Azure Cloud Shell lub uruchomić odpowiednie polecenia lokalnie.  Jeśli nigdy nie używałeś usługi Azure Cloud Shell, [zaloguj się teraz,](https://shell.azure.com) aby przejść do początkowej konfiguracji.
Jeśli wybierzesz opcję uruchamiania tych poleceń lokalnie, musisz zainstalować interfejsu wiersza polecenia.  Ten samouczek wymaga, aby uruchomić wersję interfejsu wiersza polecenia platformy Azure w wersji 2.0.71 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy NAT

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do publicznego Internetu, potrzebujesz co najmniej jednego publicznego adresu IP bramy TRANSLATORA. Użyj [tworzenia az network public-ip,](https://docs.microsoft.com/cli/azure/network/public-ip) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIP** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu IP

Można użyć co najmniej jednego publicznego adresu IP, publicznych prefiksów IP lub obu z bramą NAT. Dodamy do tego scenariusza publiczny zasób prefiksu IP, aby zademonstrować.   Tworzenie [publicznego prefiksu az network public-ip](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) w aplikacji **myResourceGroupNAT**służy do tworzenia publicznego prefiksu IP o nazwie **myPublicIPprefix** .

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy TRANSLATORA

W tej sekcji opisano, jak można tworzyć i konfigurować następujące składniki usługi NAT przy użyciu zasobu bramy NAT:
  - Publiczna pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy NAT.
  - Zmień limit czasu bezczynności z domyślnego 4 minut na 10 minut.

Utwórz globalną bramę nat platformy Azure z [utworzeniem bramy sieciowej az](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) o nazwie **myNATgateway**. Polecenie używa zarówno publicznego adresu IP **myPublicIP,** jak i publicznego prefiksu IP **myPublicIPprefix**. Polecenie zmienia limit czasu bezczynnego do **10** minut.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

W tym momencie brama NAT jest funkcjonalna i brakuje tylko konfigurowania podsieci sieci wirtualnej.

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyny Wirtualnej i można korzystać z bramy NAT, musimy utworzyć sieć wirtualną.

Utwórz sieć wirtualną o nazwie **myVnet** z podsiecią o nazwie **mySubnet** w **myResourceGroupNAT** przy użyciu [sieci wirtualnej az .](https://docs.microsoft.com/cli/azure/network/vnet)  Przestrzeń adresowa IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej jest **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurowanie usługi NAT dla podsieci źródłowej

Skonfigurujemy podsieć źródłową **mySubnet** w sieci wirtualnej **myVnet,** aby używał określonego zasobu bramy **NAT myNATgateway** z [aktualizacją podsieci sieci AZ](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  To polecenie aktywuje usługę NAT w określonej podsieci.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Cały ruch wychodzący do miejsc docelowych w Internecie jest teraz przy użyciu bramy NAT.  Nie jest konieczne konfigurowanie UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Tworzenie maszyny Wirtualnej w celu korzystania z usługi NAT

Teraz utworzymy maszynę wirtualną do korzystania z usługi NAT.  Ta maszyna wirtualna ma publiczny adres IP do użycia jako publiczny adres IP na poziomie wystąpienia, aby umożliwić dostęp do maszyny Wirtualnej.  Usługa NAT jest świadoma kierunku przepływu i zastąpi domyślne miejsce docelowe Internetu w podsieci. Publiczny adres IP maszyny Wirtualnej nie będzie używany dla połączeń wychodzących.

### <a name="create-public-ip-for-source-vm"></a>Tworzenie publicznego adresu IP dla źródłowego wirtualnego

Tworzymy publiczny adres IP, który ma być używany do uzyskiwania dostępu do maszyny Wirtualnej.  Użyj [tworzenia az network public-ip,](https://docs.microsoft.com/cli/azure/network/public-ip) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIPVM** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Tworzenie sieci płciowych dla maszyny Wirtualnej

Ponieważ standardowe publiczne adresy IP są domyślnie "bezpieczne", musimy utworzyć sieć sieciową, aby umożliwić dostęp przychodzący dla dostępu ssh. Użyj [nsg sieci az create,](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) aby utworzyć zasób sieciowej grupy sieciowej o nazwie **myNSG** w **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Uwidacznianie punktu końcowego SSH na źródłowej maszynie wirtualnej

Tworzymy regułę w NSG dla dostępu SSH do źródłowej maszyny wirtualnej. Użyj [tworzenia reguły nsg sieci az,](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) aby utworzyć regułę sieciowej grupy sieciowej o nazwie **ssh** w sieciowej sieciowej o nazwie **myNSG** w **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Tworzenie karty sieciowej dla maszyny Wirtualnej

Utwórz interfejs sieciowy z [siecią sieciową az nic utworzyć](/cli/azure/network/nic#az-network-nic-create) i skojarzyć z publiczny adres IP i sieci grupy zabezpieczeń. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą [programu az vm create](/cli/azure/vm#az-vm-create).  Generujemy klucze ssh dla tej maszyny Wirtualnej i przechowujemy klucz prywatny do późniejszego użycia.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Poczekaj na wdrożenie maszyny Wirtualnej, a następnie kontynuuj pozostałe kroki.

## <a name="discover-the-ip-address-of-the-vm"></a>Odnajduj adres IP maszyny Wirtualnej

Najpierw musimy odnajdować adres IP utworzonej maszyny Wirtualnej. Aby pobrać publiczny adres IP maszyny Wirtualnej, użyj [programu AZ network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć do uzyskania dostępu do maszyny Wirtualnej.

### <a name="sign-in-to-vm"></a>Logowanie się do maszyny Wirtualnej

Poświadczenia SSH powinny być przechowywane w aplikacji Cloud Shell z poprzedniej operacji.  Otwórz usługę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej.

```bash
ssh <ip-address-destination>
```

Teraz możesz przystąpić do korzystania z usługi NAT.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć polecenia [delete grupy az,](/cli/azure/group#az-group-delete) aby usunąć grupę zasobów i wszystkie zasoby zawarte w.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono bramę NAT i maszynę wirtualną, aby z niej korzystać. 

Przejrzyj metryki w usłudze Azure Monitor, aby wyświetlić działanie usługi NAT. Diagnozowanie problemów, takich jak wyczerpanie zasobów dostępnych portów SNAT.  Wyczerpanie zasobów portów SNAT jest rozwiązywane przez dodanie dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu IP lub obu tych zasobów.


- Dowiedz się więcej o [translatoru adresów sieci wirtualnej platformy Azure](./nat-overview.md)
- Dowiedz się więcej o [zasobie bramy NAT](./nat-gateway-resource.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu programu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu portalu Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

