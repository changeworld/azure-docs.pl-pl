---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9b168231669c50c8f00d3527288fd03ab3bf9ce8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197520"
---
### <a name="noconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — brak połączenia bramy

Jeśli nie masz połączenia bramy i chcesz dodać lub usunąć prefiksy adresów IP, możesz użyć tego samego polecenia, za pomocą którego utworzono bramę sieci lokalnej: [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_create). To polecenie umożliwia również zaktualizowanie adresu IP bramy dla urządzenia sieci VPN. W celu zastąpienia bieżących ustawień użyj istniejącej nazwy bramy sieci lokalnej. Jeśli użyjesz innej nazwy, utworzysz nową bramę sieci lokalnej, a nie zastąpisz istniejącej.

Po wprowadzeniu każdej zmiany należy określić całą listę prefiksów, a nie tylko prefiksy, które chcesz zmienić. Określ tylko prefiksy, które chcesz zachować. W tym przypadku są to prefiksy 10.0.0.0/24 i 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — istniejące połączenie bramy

Jeśli masz połączenie bramy i chcesz dodać lub usunąć prefiksy adresów IP, możesz zaktualizować prefiksy przy użyciu polecenia [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update). Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania prefiksów adresów IP nie musisz usuwać bramy sieci VPN.

Po wprowadzeniu każdej zmiany należy określić całą listę prefiksów, a nie tylko prefiksy, które chcesz zmienić. W tym przykładzie już istnieją prefiksy 10.0.0.0/24 i 20.0.0.0/24. Dodano prefiksy 30.0.0.0/24 i 40.0.0.0/24 oraz określono wszystkie 4 prefiksy podczas aktualizowania.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
