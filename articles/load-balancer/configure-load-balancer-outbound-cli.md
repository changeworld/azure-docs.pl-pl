---
title: Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: W tym artykule przedstawiono sposób konfigurowania reguł obciążenia równoważenia i wychodzącego w standardowego modułu równoważenia obciążenia przy użyciu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 7230b0c2b80137b068bbeacf43ab2133491a69b0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225467"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w usłudze Load Balancer w warstwie Standardowa przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki Start pokazano, jak skonfigurować reguły dla ruchu wychodzącego w standardowego modułu równoważenia obciążenia przy użyciu wiersza polecenia platformy Azure.  

Gdy wszystko będzie gotowe, zasób modułu równoważenia obciążenia zawiera dwa Frontony oraz skojarzone z nimi reguły: jeden dla ruchu przychodzącego i inny wpis dla ruchu wychodzącego.  Każdym frontonem odwołuje się do publicznego adresu IP i używa tego scenariusza, inny publiczny adres IP dla ruchu przychodzącego i wychodzącego.   Reguły równoważenia obciążenia zapewnia tylko dla ruchu przychodzącego równoważenia obciążenia i reguły ruchu wychodzącego kontroluje NAT dla ruchu wychodzącego podane dla maszyny Wirtualnej.  Ten przewodnik Szybki Start używa dwóch oddzielnych pul zaplecza, jednej dla ruchu przychodzącego i jednego dla ruchu wychodzącego, aby zilustrować możliwości i zapewnić elastyczność w tym scenariuszu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myresourcegroupoutbound* w lokalizacji *eastus2* :

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną o nazwie *myvnetoutbound* z podsiecią o nazwie *mysubnetoutbound* w *myresourcegroupoutbound* za pomocą polecenia [AZ Network VNET Create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Tworzenie dla ruchu przychodzącego publiczny adres IP 

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczne adresy IP w warstwie Standardowa. Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) , aby utworzyć standardowy publiczny adres IP o nazwie *mypublicipinbound* w *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Tworzenie wychodzącego publicznego adresu IP 

Utwórz standardowy adres IP dla konfiguracji wychodzącej frontonu Load Balancer przy użyciu polecenia [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia platformy Azure

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
  - Adres IP do serwera sieci Web, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  - Pula zaplecza, w której adres IP frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  - Pula zaplecza dla łączności wychodzącej. 
  - Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  - Reguły modułu równoważenia obciążenia dla ruchu przychodzącego, który definiuje sposób dystrybucji ruchu do maszyn wirtualnych.
  - Reguły modułu równoważenia obciążenia ruchu wychodzącego, który definiuje sposób dystrybucji ruchu z maszyn wirtualnych.

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz Load Balancer z przychodzącym adresem IP przy użyciu polecenia [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie *lb* zawierającego konfigurację przychodzącego adresu IP frontonu i puli zaplecza *bepoolinbound* , która jest skojarzona z publicznym adresem *IP mypublicipinbound* utworzony w poprzednim kroku.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Utwórz pulę wychodzącą

Utwórz dodatkową pulę adresów zaplecza, aby zdefiniować łączność wychodzącą dla puli maszyn wirtualnych za pomocą [AZ Network lb Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie *bepooloutbound*.  Utworzenie oddzielnej puli wychodzącej zapewnia maksymalną elastyczność, ale można pominąć ten krok i używać tylko *bepoolinbound* dla ruchu przychodzącego.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Tworzenie adresu IP frontonu ruchu wychodzącego
Utwórz wychodzącą konfigurację adresu IP frontonu dla Load Balancer za pomocą [AZ Network lb fronton-IP Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) , która obejmuje i wychodzącą konfigurację adresu IP frontonu o nazwie *myfrontendoutbound* , która jest skojarzona z publicznym adresem *IP mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utwórz sondę kondycji za pomocą polecenia [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) w celu monitorowania kondycji maszyn wirtualnych. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Utwórz regułę równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje konfigurację adresu IP frontonu dla ruchu przychodzącego oraz pulę zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myinboundlbrule* za pomocą [AZ Network lb Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) w celu nasłuchiwania na porcie 80 w puli frontonu *myfrontendinbound* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *bepool* również przy użyciu portu 80. 

>[!NOTE]
>Tej reguły równoważenia obciążenia wyłącza automatyczne ruchu wychodzącego (S) translatora adresów Sieciowych w wyniku tej reguły z parametrem--disable wychodzącego snat. NAT dla ruchu wychodzącego jest dostępna wyłącznie przez reguły ruchu wychodzącego.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Tworzenie reguły ruchu wychodzącego

Reguła wychodząca definiuje publiczny adres IP frontonu reprezentowany przez *myfrontendoutbound*frontonu, który będzie używany do całego ruchu wychodzącego NAT, a także do puli zaplecza, do której zostanie zastosowana ta reguła.  Utwórz regułę wychodzącą *myoutboundrule* na potrzeby translacji sieci wychodzącej wszystkich maszyn wirtualnych (konfiguracje protokołu IP kart sieciowych) w puli zaplecza *bepool* .  Poniższe polecenie również zmieni wychodzące limitu czasu bezczynności od 4 do 15 minut, a także przydziela 10000 SNAT porty zamiast 1024.  Przejrzyj [reguły ruchu wychodzącego](https://aka.ms/lboutboundrules) , aby uzyskać więcej szczegółów.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Jeśli nie chcesz używać oddzielnej puli wychodzącej, możesz zmienić argument puli adresów w poprzednim poleceniu, aby określić *bepoolinbound* .  Zalecamy używanie oddzielnych pul do zapewnienia elastyczności i czytelności powstającej konfiguracji.

W tym momencie można kontynuować dodawanie maszyny wirtualnej do puli zaplecza *bepoolinbound* __i__ *bepooloutbound* przez aktualizację konfiguracji protokołu IP odpowiednich zasobów kart sieciowych za pomocą polecenia [AZ Network nic IP-config Address-Pool Add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, możesz użyć polecenia [az group delete](/cli/azure/group#az-group-delete), aby usunąć grupę zasobów, moduł równoważenia obciążenia oraz wszystkie pokrewne zasoby.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Następne kroki
W tym artykule tworzone standardowego modułu równoważenia obciążenia, skonfigurowane reguły ruchu sieciowego modułu równoważenia obciążenia dla ruchu przychodzącego, skonfigurowane i sondy kondycji dla maszyn wirtualnych w puli zaplecza. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
