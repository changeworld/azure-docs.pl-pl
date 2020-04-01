---
title: 'Samouczek: Tworzenie i testowanie bramy NAT — azure cli'
titlesuffix: Azure Virtual Network NAT
description: W tym samouczku pokazano, jak utworzyć bramę NAT przy użyciu interfejsu wiersza polecenia platformy Azure i przetestować usługę NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: b3e10b3abbe5c9815e51ce67786882dbd294df3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202237"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Samouczek: Tworzenie bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure i testowanie usługi NAT

W tym samouczku utworzysz bramę NAT, aby zapewnić łączność wychodzącą dla maszyn wirtualnych na platformie Azure. Aby przetestować bramę NAT, należy wdrożyć maszynę wirtualną źródłową i docelową. Przetestujesz bramę NAT, nawiązując połączenia wychodzące z publicznym adresem IP. Te połączenia będą pochodzić ze źródła do docelowej maszyny wirtualnej. Ten samouczek wdraża źródło i miejsce docelowe w dwóch różnych sieciach wirtualnych w tej samej grupie zasobów tylko dla uproszczenia.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ten samouczek można wykonać przy użyciu usługi Azure Cloud Shell lub uruchomić odpowiednie polecenia lokalnie.  Jeśli usługa Azure Cloud Shell nie została użyta, należy [zalogować się teraz.](https://shell.azure.com)

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

Aby uzyskać dostęp do publicznego Internetu, potrzebujesz co najmniej jednego publicznego adresu IP bramy TRANSLATORA. Użyj [tworzenia az network public-ip,](https://docs.microsoft.com/cli/azure/network/public-ip) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIPsource** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu IP

Można użyć co najmniej jednego publicznego adresu IP, publicznych prefiksów IP lub obu z bramą NAT. Dodamy publiczny zasób prefiksu IP do tego scenariusza, aby zademonstrować.   Tworzenie [publicznego prefiksu az network public-ip](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) w celu utworzenia publicznego zasobu prefiksu IP o nazwie **myPublicIPprefixsource** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy TRANSLATORA

W tej sekcji opisano, jak można tworzyć i konfigurować następujące składniki usługi NAT przy użyciu zasobu bramy NAT:
  - Publiczna pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy NAT.
  - Zmień limit czasu bezczynności z domyślnego 4 minut na 10 minut.

Utwórz globalną bramę nat platformy Azure z [utworzeniem bramy sieciowej az](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) o nazwie **myNATgateway**. Polecenie używa zarówno publicznego adresu IP **myPublicIP,** jak i publicznego prefiksu IP **myPublicIPprefix**. Polecenie zmienia również limit czasu bezczynnego do 10 minut.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

W tym momencie brama NAT jest funkcjonalna i brakuje tylko konfigurowania podsieci sieci wirtualnej.

## <a name="prepare-the-source-for-outbound-traffic"></a>Przygotowanie źródła dla ruchu wychodzącego

Poprowadzimy Cię przez konfigurację pełnego środowiska testowego. Zestawisz test przy użyciu narzędzi typu open source do weryfikacji bramy NAT. Zaczniemy od źródła, które będzie korzystać z bramy NAT, którą utworzyliśmy wcześniej.

### <a name="configure-virtual-network-for-source"></a>Konfigurowanie sieci wirtualnej dla źródła

Przed wdrożeniem maszyny Wirtualnej i można przetestować bramę NAT, musimy utworzyć sieć wirtualną.

Utwórz sieć wirtualną o nazwie **myVnetsource** z podsiecią o nazwie **mySubnetsource** w **myResourceGroupNAT** przy użyciu [sieci az Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet).  Przestrzeń adresowa IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej jest **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurowanie usługi NAT dla podsieci źródłowej

Skonfiguruj podsieć źródłową **mySubnetsource** w sieci wirtualnej **myVnetsource,** aby używał określonego zasobu bramy **NAT myNATgateway** z [aktualizacją podsieci az network Microsoft Azure Virtual Network](https://docs.microsoft.com/cli/azure/network/vnet/subnet). To polecenie aktywuje usługę NAT w określonej podsieci.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

Cały ruch wychodzący do miejsc docelowych w Internecie jest teraz korzystający z usługi NAT.  Nie jest konieczne konfigurowanie UDR.

Zanim będziemy mogli przetestować bramę NAT, musimy utworzyć źródłową maszynę wirtualną.  Firma Wezwie publiczny zasób adresu IP jako publiczny adres IP na poziomie wystąpienia, aby uzyskać dostęp do tej maszyny wirtualnej z zewnątrz. Ten adres jest używany tylko do uzyskania do niego dostęp do testu.  Zademonstrujemy, jak usługa NAT ma pierwszeństwo przed innymi opcjami wychodzącymi.

Można również utworzyć tę maszynę wirtualną bez publicznego adresu IP i utworzyć inną maszynę wirtualną do użycia jako jumpbox bez publicznego adresu IP jako ćwiczenia.

### <a name="create-public-ip-for-source-vm"></a>Tworzenie publicznego adresu IP dla źródłowego wirtualnego

Tworzymy publiczny adres IP, który ma być używany do uzyskiwania dostępu do źródłowej maszyny Wirtualnej. Użyj [tworzenia az network public-ip,](https://docs.microsoft.com/cli/azure/network/public-ip) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIPsourceVM** w **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Tworzenie sieciowych sieci płciowych dla źródłowego maszyny Wirtualnej

Ponieważ standardowe publiczne adresy IP są domyślnie "bezpieczne", musimy utworzyć sieć sieciową, aby umożliwić dostęp przychodzący dla dostępu ssh.  Usługa Azure NAT jest świadomy kierunku przepływu. Ta grupa sieciowa nie będzie używana do ruchu wychodzącego po skonfigurowaniu bramy NAT w tej samej podsieci. Użyj [nsg sieci az create,](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) aby utworzyć zasób sieciowej grupy roboczej o nazwie **myNSGsource** w **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Uwidacznianie punktu końcowego SSH na źródłowej maszynie wirtualnej

Tworzymy regułę w NSG dla dostępu SSH do źródłowej maszyny wirtualnej. Użyj [tworzenia reguły nsg sieci az,](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) aby utworzyć regułę sieciowej sieciowej o nazwie **ssh**. Ta reguła zostanie utworzona w grupie sieciowej o nazwie **myNSGsource** w grupie zasobów **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>Tworzenie karty sieciowej dla źródłowej maszyny Wirtualnej

Utwórz interfejs sieciowy z [siecią sieciową az nic utworzyć](/cli/azure/network/nic#az-network-nic-create) i skojarzyć z publicznego adresu IP i sieci grupy zabezpieczeń. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Tworzenie źródłowej maszyny Wirtualnej

Utwórz maszynę wirtualną za pomocą [programu az vm create](/cli/azure/vm#az-vm-create).  Generujemy klucze ssh dla tej maszyny Wirtualnej i przechowujemy klucz prywatny do późniejszego użycia.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Gdy polecenie powróci natychmiast, może upłynąć kilka minut, aby maszyna wirtualna została wdrożona.

## <a name="prepare-destination-for-outbound-traffic"></a>Przygotowanie miejsca docelowego dla ruchu wychodzącego

Teraz utworzymy miejsce docelowe dla ruchu wychodzącego przetłumaczonego przez usługę NAT, aby umożliwić jego przetestowanie.

### <a name="configure-virtual-network-for-destination"></a>Konfigurowanie sieci wirtualnej dla miejsca docelowego

 Musimy utworzyć sieć wirtualną, w której będzie docelowa maszyna wirtualna.  Te polecenia są te same kroki, jak dla źródłowej maszyny Wirtualnej z małych zmian, aby udostępnić docelowy punkt końcowy.

Utwórz sieć wirtualną o nazwie **myVnetdestination** z podsiecią o nazwie **mySubnetdestination** w **myResourceGroupNAT** przy użyciu [sieci az Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet).  Przestrzeń adresowa IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej jest **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Tworzenie publicznego adresu IP dla docelowej maszyny Wirtualnej

Tworzymy publiczny adres IP, który ma być używany do uzyskiwania dostępu do źródłowej maszyny Wirtualnej. Użyj [tworzenia az network public-ip,](https://docs.microsoft.com/cli/azure/network/public-ip) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIPdestinationVM** w **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Tworzenie sieci płciowych dla docelowej maszyny Wirtualnej

Standardowe publiczne adresy IP są "bezpieczne domyślnie", musisz utworzyć nsg, aby umożliwić dostęp przychodzący dla ssh. Usługa Azure NAT jest świadomy kierunku przepływu. Ta grupa sieciowa nie będzie używana do ruchu wychodzącego po skonfigurowaniu bramy NAT w tej samej podsieci. Użyj [nsg sieci az create,](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) aby utworzyć zasób sieciowej grupy roboczej o nazwie **myNSGdestination** w **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Uwidacznianie punktu końcowego SSH na docelowej maszynie wirtualnej

Tworzymy regułę w nsg dla dostępu SSH do docelowej maszyny wirtualnej. Użyj [tworzenia reguły nsg sieci az,](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) aby utworzyć regułę sieciowej sieciowej o nazwie **ssh**. Ta reguła zostanie utworzona w sieciowej grupy sieciowej o nazwie **myNSGdestination** w grupie zasobów **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Uwidacznianie punktu końcowego HTTP na docelowej maszynie wirtualnej

Tworzymy regułę w sieciowej sieciowej dla dostępu HTTP do docelowej maszyny wirtualnej. Użyj [tworzenia reguły nsg sieci az,](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) aby utworzyć regułę sieciowej grupy sieciowej o nazwie **http** w sieciowej sieciowej o nazwie **myNSGdestination** w **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>Tworzenie karty sieciowej dla docelowej maszyny Wirtualnej

Utwórz interfejs sieciowy z [siecią az nic utworzyć](/cli/azure/network/nic#az-network-nic-create) i skojarzyć z publiczny adres IP **myPublicIPdestinationVM** i sieci grupy zabezpieczeń **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>Tworzenie docelowej maszyny Wirtualnej

Utwórz maszynę wirtualną za pomocą [programu az vm create](/cli/azure/vm#az-vm-create).  Generujemy klucze ssh dla tej maszyny Wirtualnej i przechowujemy klucz prywatny do późniejszego użycia.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
Gdy polecenie powróci natychmiast, może upłynąć kilka minut, aby maszyna wirtualna została wdrożona.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Przygotowanie serwera sieci web i przetestowanie ładunku na docelowej maszynie wirtualnej

Najpierw musimy odnajdować adres IP docelowej maszyny Wirtualnej.  Aby uzyskać publiczny adres IP docelowej maszyny Wirtualnej, użyj [programu az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć w kolejnych krokach. Wskazać, że jest to docelowa maszyna wirtualna.

### <a name="sign-in-to-destination-vm"></a>Logowanie się do docelowej maszyny Wirtualnej

Poświadczenia SSH powinny być przechowywane w aplikacji Cloud Shell z poprzedniej operacji.  Otwórz usługę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej. 

```bash
ssh <ip-address-destination>
```

Po zalogowaniu się skopiuj i wklej następujące polecenia.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Te polecenia zaktualizują maszynę wirtualną, zainstalują nginx i utworzą plik o rozmiarze 100 KB. Ten plik zostanie pobrany ze źródłowej maszyny Wirtualnej przy użyciu usługi NAT.

Zamknij sesję SSH z docelową maszyną wirtualną.

## <a name="prepare-test-on-source-vm"></a>Przygotowanie testu na źródłowej maszynie wirtualnej

Najpierw musimy odnajdować adres IP źródłowej maszyny Wirtualnej.  Aby uzyskać publiczny adres IP źródłowej maszyny Wirtualnej, użyj [programu az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć w kolejnych krokach. Wskazać, że jest to źródło maszyny wirtualnej.

### <a name="sign-in-to-source-vm"></a>Logowanie się do źródłowego znaku wirtualnego

Ponownie poświadczenia SSH są przechowywane w usłudze Cloud Shell. Otwórz nową kartę usługi [Azure Cloud Shell](https://shell.azure.com) w przeglądarce.  Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej. 

```bash
ssh <ip-address-source>
```

Skopiuj i wklej następujące polecenia, aby przygotować się do testowania usługi NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

To polecenie zaktualizuje maszynę wirtualną, zainstaluje go, [zainstaluje hej](https://github.com/rakyll/hey) z GitHub i zaktualizuje środowisko powłoki.

Teraz możesz przystąpić do testowania usługi NAT.

## <a name="validate-nat-service"></a>Sprawdzanie poprawności usługi NAT

Po zalogowaniu się do źródłowej maszyny Wirtualnej można użyć **curl** i hej do **generowania** żądań do docelowego adresu IP.

Użyj curl, aby pobrać plik 100-KB.  Zastąp ** \<>docelowy adresu IP** w poniższym przykładzie docelowym adresem IP skopiowanym wcześniej.  **Parametr --output** wskazuje, że pobrany plik zostanie odrzucony.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Można również wygenerować serię żądań przy użyciu **hej**. Ponownie zastąp ** \<>docelowy adresu IP** na wcześniej skopiowany adres IP.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

To polecenie wygeneruje 100 żądań, 10 jednocześnie, z limitem czasu 30 sekund. Połączenie TCP nie zostanie ponownie użyczone.  Każde żądanie zostanie pobrane 100 Kbyt.  Pod koniec biegu **hej** zgłosi niektóre statystyki dotyczące tego, jak dobrze zrobiła to usługa NAT.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć polecenia [delete grupy az,](/cli/azure/group#az-group-delete) aby usunąć grupę zasobów i wszystkie zasoby zawarte w.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono bramę NAT, utworzono maszynę wirtualną źródłową i docelową, a następnie przetestowano bramę NAT.

Przejrzyj metryki w usłudze Azure Monitor, aby wyświetlić działanie usługi NAT. Diagnozowanie problemów, takich jak wyczerpanie zasobów dostępnych portów SNAT.  Wyczerpanie zasobów portów SNAT można łatwo rozwiązać, dodając dodatkowe zasoby publicznego adresu IP lub publiczne zasoby prefiksów IP lub oba te zasoby.

- Dowiedz się więcej o [nat sieci wirtualnej](./nat-overview.md)
- Dowiedz się więcej o [zasobie bramy NAT](./nat-gateway-resource.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu programu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu portalu Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

