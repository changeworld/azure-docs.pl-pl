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
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159356"
---
* **PolicyBased:** Sieci VPN typu PolicyBased wcześniej były nazywane statycznymi bramami routingu w klasycznym modelu wdrażania. Sieci VPN oparte na zasadach szyfrują i kierowania pakietów przez tunel protokołu IPsec na podstawie zasad protokołu IPsec, skonfigurowane przy użyciu kombinacji prefiksów adresów między siecią lokalną a siecią wirtualną platformy Azure. Zasady (lub selektor ruchu) są zazwyczaj zdefiniowane jako lista dostępu w konfiguracji urządzenia sieci VPN. Wartość dla typu sieci PolicyBased VPN jest *PolicyBased*. Podczas korzystania z sieci VPN PolicyBased, należy pamiętać o następujących ograniczeniach:
  
  * Sieci VPN typu PolicyBased można **tylko** można używać na podstawowej jednostki SKU bramy. Ten typ sieci VPN nie jest zgodny z innych jednostkach SKU bramy.
  * Może mieć tylko 1 tunel, korzystając z sieci VPN typu PolicyBased.
  * Sieci VPN typu PolicyBased można używać tylko dla połączeń S2S i tylko w przypadku niektórych konfiguracji. Większość konfiguracji bramy sieci VPN wymagają sieci VPN typu RouteBased.
* **RouteBased**: Sieci VPN typu RouteBased wcześniej były nazywane dynamicznymi bramami routingu w klasycznym modelu wdrażania. Sieci VPN typu RouteBased używają "tras" w IP przesyłania dalej lub tabeli routingu do kierowania pakietów do odpowiednich interfejsów tuneli. W dalszej kolejności interfejsy tuneli szyfrują lub odszyfrowują pakiety wchodzące do tuneli lub wychodzące z nich. Zasady (lub selektor ruchu) dla sieci VPN typu RouteBased są skonfigurowane jako dowolna dowolna (lub symbole wieloznaczne). Jest wartością dla typu sieci VPN typu RouteBased *RouteBased*.
