---
title: Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: W tym artykule pokazano, jak skonfigurować równoważenie obciążenia i reguły ruchu wychodzącego w standardowym modułem równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225467"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w usłudze Load Balancer w warstwie Standardowa przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki start pokazuje, jak skonfigurować reguły ruchu wychodzącego w standardowym równoważku obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure.  

Po zakończeniu zasób Moduł równoważenia obciążenia zawiera dwa frontonety i skojarzone z nimi reguły: jeden dla ruchu przychodzącego i drugi dla ruchu wychodzącego.  Każdy frontend ma odwołanie do publicznego adresu IP i w tym scenariuszu używa innego publicznego adresu IP dla ruchu przychodzącego i wychodzącego.   Reguła równoważenia obciążenia zapewnia tylko równoważenie obciążenia przychodzącego, a reguła wychodząca steruje wychodzącym translatorem z osią wirtualną pod warunkiem, że maszyna wirtualna.  Ten przewodnik Szybki start używa dwóch oddzielnych pul wewnętrznej bazy danych, jednej dla ruchu przychodzącego i jednej dla ruchu wychodzącego, aby zilustrować możliwości i umożliwić elastyczność w tym scenariuszu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myresourcegroupoutbound* w lokalizacji *eastus2:*

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną o nazwie *myvnetoutbound* z podsieci o nazwie *mysubnetoutbound* w *myresourcegroupoutbound* przy użyciu [sieci wirtualnej az utworzyć](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Tworzenie przychodzącego publicznego adresu IP 

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczne adresy IP w warstwie Standardowa. Użyj [tworzenia az network public-ip,](https://docs.microsoft.com/cli/azure/network/public-ip) aby utworzyć standardowy publiczny adres IP o nazwie *mypubliczny* w *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Tworzenie wychodzącego publicznego adresu IP 

Utwórz standardowy adres IP dla wewnętrznej konfiguracji wychodzącej modułu równoważenia obciążenia przy użyciu [programu az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Tworzenie modułu równoważenia w usłudze Azure Load Balancer

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
  - Frontend IP, który odbiera przychodzący ruch sieciowy na moduł równoważenia obciążenia.
  - Pula wewnętrznej bazy danych, w której adres IP wewnętrznej bazy danych wysyła ruch sieciowy z równoważenia obciążenia.
  - Pula wewnętrznej bazy danych dla połączeń wychodzących. 
  - Sonda kondycji, która określa kondycję wystąpień maszyny wirtualnej wewnętrznej bazy danych.
  - Reguła przychodząca modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.
  - Reguła wychodząca modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu z maszyn wirtualnych.

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz moduł równoważenia obciążenia z przychodzącym adresem IP przy użyciu [sieci AZ lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) named *lb,* która zawiera przychodzącą konfigurację ip frontonu i *bepoolinbound* puli wewnętrznej bazy danych, która jest skojarzona z publicznym adresem IP *mypubliczny,* który został utworzony w poprzednim kroku.

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

### <a name="create-outbound-pool"></a>Tworzenie puli wychodzącej

Utwórz dodatkową pulę adresów wewnętrznej bazy danych, aby zdefiniować łączność wychodzącą dla puli maszyn wirtualnych z [az sieci lb address-pool utworzyć](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie *bepooloutbound*.  Tworzenie oddzielnej puli wychodzącej zapewnia maksymalną elastyczność, ale można pominąć ten krok i używać tylko przychodzącego *bepoolinbound.*

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Tworzenie wychodzącego adresu IP międzynachu
Tworzenie wychodzącej konfiguracji IP frontonu dla modułu równoważenia obciążenia z [utworzeniem az network lb frontend-ip,](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) która zawiera i wychodzącą konfigurację IP frontonu o nazwie *myfrontendoutbound,* która jest skojarzona z publicznym adresem IP *mypublicznym adresem IP mypublicipoutbound*

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

### <a name="create-load-balancing-rule"></a>Tworzenie reguły równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje konfigurację ip frontendu dla ruchu przychodzącego i puli wewnętrznej bazy danych w celu odbierania ruchu wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę równoważenia obciążenia *myinboundlbrule* z [regułą lb sieci az do](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) nasłuchiwania portu 80 w puli frontonu *myfrontendinbound* i wysyłania ruchu sieciowego z równoważeniem obciążenia do puli adresów wewnętrznej *bepool* również przy użyciu portu 80. 

>[!NOTE]
>Ta reguła równoważenia obciążenia wyłącza automatyczny wychodzący (S)NAT w wyniku tej reguły z parametrem --disable-outbound-snat. Wychodzący translator z prawa najazdowego jest dostarczany tylko przez regułę ruchu wychodzącego.

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

Reguła wychodząca definiuje publiczny adres IP frontendu, reprezentowany przez frontend *myfrontendoutbound*, który będzie używany dla całego ruchu wychodzącego NAT, a także puli wewnętrznej bazy danych, do której ma zastosowanie ta reguła.  Utwórz regułę wychodzącą *myoutboundrule* dla wychodzącej translacji sieciowej wszystkich maszyn wirtualnych (konfiguracje adresów SIECIOWYCH) w puli zaplecza *bepool.*  Poniższe polecenie zmienia również limit czasu bezczynności ruchu wychodzącego z 4 do 15 minut i przydziela 10000 portów SNAT zamiast 1024.  Przejrzyj [reguły ruchu wychodzącego,](https://aka.ms/lboutboundrules) aby uzyskać więcej informacji.

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

Jeśli nie chcesz używać oddzielnej puli wychodzącej, możesz zmienić argument puli adresów w poprzednim poleceniu, aby określić *bepoolinbound* zamiast.  Zaleca się użycie oddzielnych pul dla elastyczności i czytelności wynikowej konfiguracji.

W tym momencie można kontynuować dodawanie maszyn wirtualnych do puli wewnętrznej bazy danych *bepoolinbound* __i__ *bepooloutbound,* aktualizując konfigurację IP odpowiednich zasobów karty sieciowej za pomocą [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, możesz użyć polecenia [az group delete](/cli/azure/group#az-group-delete), aby usunąć grupę zasobów, moduł równoważenia obciążenia oraz wszystkie pokrewne zasoby.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono standardowy moduł równoważenia obciążenia, skonfigurowano reguły ruchu przychodzącego modułu równoważenia obciążenia, skonfigurowaną i sondę kondycji maszyn wirtualnych w puli wewnętrznej bazy danych. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
