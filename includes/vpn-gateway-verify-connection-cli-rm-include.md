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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411817"
---
Możesz sprawdzić, czy połączenie powiodło się, używając polecenia [az network vpn-connection show](/cli/azure/network/vpn-connection). W podanym przykładzie opcja „--name” odnosi się do nazwy połączenia, które ma zostać przetestowane. Gdy proces nawiązywania połączenia trwa, jego stan połączenia to „Łączenie”. Gdy połączenie zostanie nawiązane, jego stan zmienia się na „Połączone”.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
