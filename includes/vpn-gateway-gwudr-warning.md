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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275334"
---
Trasy zdefiniowane przez użytkownika z miejscem docelowym 0.0.0.0/0 i sieciami sieciowymi w sieci GatewaySubnet **nie są obsługiwane**. Bramy utworzone za pomocą tej konfiguracji zostaną zablokowane przed utworzeniem. Bramy wymagają dostępu do kontrolerów zarządzania, aby działały poprawnie. [Propagacja trasy protokołu BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) powinna być ustawiona na "Włączone" w sieci GatewaySubnet, aby zapewnić dostępność bramy. Jeśli jest to ustawione na wyłączone, brama nie będzie działać.
