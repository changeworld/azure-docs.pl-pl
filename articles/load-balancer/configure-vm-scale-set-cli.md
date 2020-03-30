---
title: Konfigurowanie zestawu skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure — interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349730"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Konfigurowanie zestawu skalowania maszyny wirtualnej przy użyciu istniejącego modułu równoważenia obciążenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak skonfigurować zestaw skalowania maszyny wirtualnej przy pomocą istniejącego modułu równoważenia obciążenia platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Istniejący standardowy moduł równoważenia obciążenia sku w subskrypcji, w której zostanie wdrożony zestaw skalowania maszyny wirtualnej.
- Sieć wirtualna platformy Azure dla zestawu skalowania maszyny wirtualnej.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, że masz zainstalowaną wersję interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Zaloguj się na platformie Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Wdrażanie zestawu skalowania maszyny wirtualnej z istniejącym modułem równoważenia obciążenia

Zastąp wartości w nawiasach nazwami zasobów w konfiguracji.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

Poniższy przykład wdraża zestaw skalowania maszyny wirtualnej z:

- Zestaw skalowania maszyny wirtualnej o nazwie **myVMSS**
- Moduł równoważenia obciążenia platformy Azure o nazwie **myLoadBalancer**
- Pula wewnętrznej bazy danych modułu równoważenia obciążenia o nazwie **myBackendPool**
- Sieć wirtualna platformy Azure o nazwie **myVnet**
- Podsieć o nazwie **mySubnet**
- Grupa zasobów o nazwie **myResourceGroup**
- Obraz serwera Ubuntu dla zestawu skalowania maszyny wirtualnej

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Po utworzeniu zestawu skalowania nie można zmodyfikować portu wewnętrznej bazy danych dla reguły równoważenia obciążenia używanej przez sondę kondycji modułu równoważenia obciążenia. Aby zmienić port, można usunąć sondę kondycji, aktualizując zestaw skalowania maszyny wirtualnej platformy Azure, zaktualizować port, a następnie ponownie skonfigurować sondę kondycji.

## <a name="next-steps"></a>Następne kroki

W tym artykule wdrożono zestaw skalowania maszyny wirtualnej z istniejącym modułem równoważenia obciążenia platformy Azure.  Aby dowiedzieć się więcej o zestawach skalowania maszyn wirtualnych i równoważącym obciążenia, zobacz:

- [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)
- [Co to są zestawy skalowania maszyn wirtualnych?](../virtual-machine-scale-sets/overview.md)
                                