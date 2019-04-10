---
title: Konfigurowanie równoważenia obciążenia i reguł dla ruchu wychodzącego przy użyciu wiersza polecenia platformy Azure
titlesuffix: Azure Load Balancer
description: W tym artykule przedstawiono sposób konfigurowania reguł obciążenia równoważenia i wychodzącego w standardowego modułu równoważenia obciążenia przy użyciu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: kumud
ms.openlocfilehash: f28088a1a0586964092a0b5f86ce8bf0f95402cd
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281951"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurowanie równoważenia obciążenia i reguł dla ruchu wychodzącego w standardowego modułu równoważenia obciążenia przy użyciu wiersza polecenia platformy Azure

Ten przewodnik Szybki Start pokazano, jak skonfigurować reguły dla ruchu wychodzącego w standardowego modułu równoważenia obciążenia przy użyciu wiersza polecenia platformy Azure.  

Gdy wszystko będzie gotowe, zasób modułu równoważenia obciążenia zawiera dwa Frontony oraz skojarzone z nimi reguły: jeden dla ruchu przychodzącego i inny wpis dla ruchu wychodzącego.  Każdym frontonem odwołuje się do publicznego adresu IP i używa tego scenariusza, inny publiczny adres IP dla ruchu przychodzącego i wychodzącego.   Reguły równoważenia obciążenia zapewnia tylko dla ruchu przychodzącego równoważenia obciążenia i reguły ruchu wychodzącego kontroluje NAT dla ruchu wychodzącego podane dla maszyny Wirtualnej.  Ten przewodnik Szybki Start używa dwa oddzielne pule zaplecza, jeden dla ruchu przychodzącego, a drugi dla ruchu wychodzącego, aby zilustrować możliwości i umożliwić elastyczność zapewniają dodatkowe funkcje, w tym scenariuszu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myresourcegroupoutbound* w *eastus2* lokalizacji:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Tworzenie sieci wirtualnej o nazwie *myvnetoutbound* z podsiecią o nazwie *mysubnetoutbound* w *myresourcegroupoutbound* przy użyciu [az sieci w sieci wirtualnej Utwórz](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Tworzenie dla ruchu przychodzącego publiczny adres IP 

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczne adresy IP w warstwie Standardowa. Użyj [tworzenie sieci az public-ip](https://docs.microsoft.com/cli/azure/network/public-ip) do utworzenia standardowego publicznego adresu IP o nazwie *mypublicipinbound* w *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Tworzenie wychodzącego publicznego adresu IP 

Utwórz adres IP w warstwie standardowa dla ruchu wychodzącego konfigurację frontonu modułu równoważenia obciążenia za pomocą polecenia [tworzenie sieci az public-ip](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia platformy Azure

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
  - Adres IP do serwera sieci Web, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  - Pula zaplecza, gdzie adres IP frontonu wysyła obciążenia równoważenia ruchu sieciowego.
  - Pula zaplecza dla łączności wychodzącej. 
  - Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  - Reguły modułu równoważenia obciążenia dla ruchu przychodzącego, który definiuje sposób dystrybucji ruchu do maszyn wirtualnych.
  - Reguły modułu równoważenia obciążenia ruchu wychodzącego, który definiuje sposób dystrybucji ruchu z maszyn wirtualnych.

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Tworzenie modułu równoważenia obciążenia za pomocą przychodzących adres IP, korzystając [tworzenie równoważenia obciążenia sieciowego az](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie *lb* zawierającej konfigurację adresu IP frontonu dla ruchu przychodzącego i pulę zaplecza *bepoolinbound*skojarzony z publicznym adresem IP *mypublicipinbound* utworzonego w poprzednim kroku.

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

### <a name="create-outbound-pool"></a>Tworzenie puli ruchu wychodzącego

Utwórz pulę adresów zaplecza dodatkowe do definiowania łączności wychodzącej do puli maszyn wirtualnych za pomocą [az lb puli adresów sieciowych — tworzenie](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie *bepooloutbound*.  Tworzenie oddzielną pulę wychodzącego zapewnia maksymalną elastyczność, ale można pominąć ten krok i składać się tylko przychodzącego *bepoolinbound* także.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Tworzenie adresu IP frontonu ruchu wychodzącego
Utwórz konfigurację adresów IP frontonu ruchu wychodzącego dla modułu równoważenia obciążenia za pomocą [tworzenie az sieci lb frontend-ip](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) zawierającej i konfigurację adresu IP ruchu wychodzącego frontonu o nazwie *myfrontendoutbound* oznacza to skojarzony z publicznym adresem IP *mypublicipoutbound*

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

Reguła modułu równoważenia obciążenia definiuje konfigurację adresu IP frontonu dla ruchu przychodzącego oraz pulę zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myinboundlbrule* z [Utwórz regułę modułu równoważenia obciążenia sieciowego az](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) w celu nasłuchiwania na porcie 80 w puli frontonu *myfrontendinbound* i wysyłanie równoważenia obciążenia ruchu sieciowego do puli adresów zaplecza *bepool* również przy użyciu portu 80. 

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

Regułę dla ruchu wychodzącego definiuje publicznego adresu IP frontonu, reprezentowany przez frontonu *myfrontendoutbound*, który będzie używany dla całego ruchu wychodzącego NAT, a także puli zaplecza, do którego zostanie zastosowana ta reguła.  Utwórz regułę dla ruchu wychodzącego *myoutboundrule* sieciowe dotyczące połączeń wychodzących tłumaczenia wszystkich maszyn wirtualnych (konfiguracji adresów IP kart Sieciowych) w *bepool* puli zaplecza.  Poniższe polecenie również zmieni wychodzące limitu czasu bezczynności od 4 do 15 minut, a także przydziela 10000 SNAT porty zamiast 1024.  Przegląd [reguł dla ruchu wychodzącego](https://aka.ms/lboutboundrules) Aby uzyskać więcej informacji.

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

Jeśli nie chcesz używać oddzielną pulę ruchu wychodzącego, można zmienić argument puli adresów w poprzednim poleceniu, aby określić *bepoolinbound* zamiast tego.  Zaleca się użycie oddzielnych pul elastyczność i lepszą czytelność Wynikowa konfiguracja.

W tym momencie można przystąpić do dodawania maszyny Wirtualnej do puli zaplecza *bepoolinbound* __i__ *bepooloutbound* , aktualizując konfiguracji IP odpowiedniego interfejsu Sieciowego zasobów przy użyciu [az nic ip-config puli adresów sieciowych — Dodaj](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, możesz użyć polecenia [az group delete](/cli/azure/group#az-group-delete), aby usunąć grupę zasobów, moduł równoważenia obciążenia oraz wszystkie pokrewne zasoby.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Kolejne kroki
W tym artykule tworzone standardowego modułu równoważenia obciążenia, skonfigurowane reguły ruchu sieciowego modułu równoważenia obciążenia dla ruchu przychodzącego, skonfigurowane i sondy kondycji dla maszyn wirtualnych w puli zaplecza. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
