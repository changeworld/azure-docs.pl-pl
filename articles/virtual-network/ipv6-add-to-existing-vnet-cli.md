---
title: Dodawanie IPv6 do aplikacji IPv4 w sieci wirtualnej platformy Azure — interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć adresy IPv6 do istniejącej aplikacji w sieci wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 5dc231febc2e9b605b9e7f603f5d036b8a2c62eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240756"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>Dodawanie IPv6 do aplikacji IPv4 w sieci wirtualnej platformy Azure — interfejs wiersza polecenia platformy Azure (wersja zapoznawcza)

W tym artykule pokazano, jak dodać adresy IPv6 do aplikacji, która używa publicznego adresu IP IPv4 w sieci wirtualnej platformy Azure dla standardowego modułu równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure. Uaktualnienie w miejscu obejmuje sieć wirtualną i podsieć, standardowy moduł równoważenia obciążenia z konfiguracjami frontonu IPv4 + IPV6, maszyny wirtualne z kartami sieciowymi, które mają konfiguracje IPv4 + IPv6, grupę zabezpieczeń sieci i publiczne adresy IP.

> [!Important]
> Obsługa IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="register-the-service"></a>Zarejestruj usługę

Przed wdrożeniem aplikacji podwójnego stosu na platformie Azure, należy skonfigurować subskrypcję dla tej funkcji w wersji zapoznawczej przy użyciu następującej interfejsu wiersza polecenia platformy Azure:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Rejestracja funkcji trwa do 30 minut. Możesz sprawdzić stan rejestracji, uruchamiając następujące polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Po zakończeniu rejestracji uruchom następujące polecenie:

```azurecli
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa
W tym artykule przyjęto założenie, że wdrożono standardowy moduł równoważenia obciążenia zgodnie z opisem w [przewodniku Szybki start: Tworzenie standardowego modułu równoważenia obciążenia — Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>Tworzenie adresów IPv6

Utwórz publiczny adres IPv6 za pomocą [utworzenia sieci az public-ip](/cli/azure/network/public-ip) dla standardowego modułu równoważenia obciążenia. Poniższy przykład tworzy publiczny adres IP IPv6 o nazwie *PublicIP_v6* w grupie zasobów *myResourceGroupSLB:*

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurowanie frontendu modułu równoważenia obciążenia IPv6

COnfigure moduł równoważenia obciążenia z nowym adresem IP IPv6 przy użyciu [sieci az lb frontend-ip utworzyć](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) w następujący sposób:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurowanie puli wewnętrznej bazy modułu równoważenia obciążenia IPv6

Tworzenie puli wewnętrznej bazy danych dla kart sieciowych z adresami IPv6 przy użyciu [sieci az lb address-pool tworzy](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) się w następujący sposób:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurowanie reguł modułu równoważenia obciążenia IPv6

Utwórz reguły modułu równoważenia obciążenia IPv6 za pomocą [reguły az network lb .](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Dodawanie zakresów adresów IPv6

Dodaj zakresy adresów IPv6 do sieci wirtualnej i podsieci obsługującej moduł równoważenia obciążenia w następujący sposób:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Dodawanie konfiguracji IPv6 do kart sieciowych

Skonfiguruj karty sieciowe maszyn wirtualnych z adresem IPv6 przy użyciu [sieci az nic ip-config, utwórz](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) w następujący sposób:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej z dwoma stosami IPv6 w witrynie Azure portal
Sieć wirtualną z dwoma stosami IPv6 można wyświetlić w witrynie Azure Portal w następujący sposób:
1. W pasku wyszukiwania portalu wpisz *myVnet*.
2. Gdy **myVnet** pojawi się w wynikach wyszukiwania, wybierz go. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej z dwoma stosami o nazwie *myVNet*. Sieć wirtualna z dwoma stosami pokazuje trzy karty sieciowe z konfiguracjami IPv4 i IPv6 znajdującymi się w podsieci podwójnego stosu o nazwie *mySubnet*.

  ![Sieć wirtualna z dwoma stosami IPv6 na platformie Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Sieć wirtualna IPv6 dla platformy Azure jest dostępna w witrynie Azure portal tylko do odczytu w tej wersji w wersji zapoznawczej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zaktualizowano istniejący standardowy moduł równoważenia obciążenia z konfiguracją ip frontendu IPv4 do konfiguracji podwójnego stosu (IPv4 i IPv6). Dodano również konfiguracje IPv6 do kart sieciowych maszyn wirtualnych w puli wewnętrznej bazy danych. Aby dowiedzieć się więcej o obsłudze IPv6 w sieciach wirtualnych platformy Azure, zobacz [Co to jest IPv6 dla usługi Azure Virtual Network?](ipv6-overview.md)
