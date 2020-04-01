---
title: Rezerwuj publiczne adresy IPv6 i zakresy adresów w sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak zarezerwować publiczne adresy IPv6 i zakresy adresów w sieci wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a0dd56842174d89688c862397c373326ef50d1f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420540"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Rezerwowanie publicznego prefiksu adresu IPv6
IPv6 dla sieci wirtualnej platformy Azure (VNet) umożliwia hostowania aplikacji na platformie Azure z łącznością IPv6 i IPv4 zarówno w sieci wirtualnej, jak i do i z Internetu. Oprócz rezerwacji poszczególnych adresów IPv6 można zarezerwować ciągłe zakresy adresów Protokołu IPv6 platformy Azure (znany jako prefiks IP) do użytku. W tym artykule opisano sposób tworzenia publicznych adresów IP i zakresów adresów IPv6 przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Tworzenie pojedynczego zastrzeżonego publicznego adresu IP protokołu IPv6

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Można utworzyć pojedynczy zarezerwowany (statyczny) publiczny adres IP IPv6 przy użyciu programu Azure PowerShell z [new-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) w następujący sposób:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

 Można utworzyć pojedynczy zarezerwowany (statyczny) publiczny adres IP IPv6 Platformy Azure CLI z [siecią az public-ip utworzyć](/cli/azure/network/public-ip) w następujący sposób:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Tworzenie zastrzeżonego prefiksu IPv6 (zakresu)

Aby zarezerwować prefiks IPv6, dodaj rodzinę adresów IP protokołu IPv6 do tego samego polecenia, które jest używane do tworzenia prefiksów IPv4. Poniższe polecenia tworzą prefiks o rozmiarze /125 ( 8 adresów IPv6).  

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Publiczny adres IPv6 można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą [tworzenia sieci az public-ip](/powershell/module/az.network/new-azpublicipprefix) w następujący sposób:
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Publiczny adres IPv6 można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure w następujący sposób:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Przydzielanie publicznego adresu IP z zastrzeżonego prefiksu Protokołu IPv6

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

 Możesz utworzyć statyczny publiczny adres IP IPv6 z `-PublicIpPrefix` zarezerwowanego prefiksu, dodając argument podczas tworzenia publicznego adresu IP przy użyciu programu Azure PowerShell. W poniższym przykładzie przyjęto założenie, że prefiks został utworzony i przechowywany w zmiennej programu PowerShell o nazwie: *$myOwnIPv6Prefix*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
 
W poniższym przykładzie przyjęto założenie, że prefiks został utworzony i przechowywany w zmiennej cli o nazwie: *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prefiksie adresu IPv6](ipv6-public-ip-address-prefix.md).
- Dowiedz się więcej o [adresach IPv6](ipv6-overview.md).
