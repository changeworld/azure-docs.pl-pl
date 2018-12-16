---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 267234cb9ecea1dc097f13739bf98ee11206ad06
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444268"
---
Możesz również sprawdzić, czy połączenie zostało pomyślnie nawiązane, używając polecenia cmdlet „Get-AzureRmVirtualNetworkGatewayConnection” z opcją „-Debug” lub bez niej. 

1. Można skorzystać z następującego przykładu użycia polecenia cmdlet, dopasowując wartości do własnych potrzeb. Po wyświetleniu monitu wybierz „A”, aby uruchomić wszystko. W podanym przykładzie opcja „-Name” odnosi się do nazwy połączenia, które ma zostać przetestowane.

   ```azurepowershell-interactive
   Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Po zakończeniu działania polecenia cmdlet sprawdź wartości. W poniższym przykładzie stan połączenia jest wyświetlany jako „Połączone” i można zobaczyć bajty przychodzące i wychodzące.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```