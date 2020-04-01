---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501678"
---
Trasy zdefiniowane przez użytkownika z miejscem docelowym 0.0.0.0/0 i sieciami sieciowymi w sieci GatewaySubnet **nie są obsługiwane**. Bramy utworzone za pomocą tej konfiguracji zostaną zablokowane przed utworzeniem. Bramy wymagają dostępu do kontrolerów zarządzania, aby działały poprawnie. [Propagacja trasy protokołu BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) powinna być ustawiona na "Włączone" w sieci GatewaySubnet, aby zapewnić dostępność bramy. Jeśli jest to ustawione na wyłączone, brama nie będzie działać.
