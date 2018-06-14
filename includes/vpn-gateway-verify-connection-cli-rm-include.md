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
ms.openlocfilehash: 1c3103889bd11df45710cc0d6afaeeba022c9ace
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197724"
---
Możesz sprawdzić, czy połączenie powiodło się, używając polecenia [az network vpn-connection show](/cli/azure/network/vpn-connection#show). W podanym przykładzie opcja „--name” odnosi się do nazwy połączenia, które ma zostać przetestowane. Gdy proces nawiązywania połączenia trwa, jego stan połączenia to „Łączenie”. Gdy połączenie zostanie nawiązane, jego stan zmienia się na „Połączone”.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```