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
ms.openlocfilehash: 5de227e5de5ef9b41f6e0f64db86b7195259f7d6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "30196814"
---
* **PolicyBased:** VPN PolicyBased wcześniej były nazywane statycznymi bramami routingu w klasycznym modelu wdrażania. Sieci VPN oparte na zasadach szyfrują i kierowania pakietów przez tunel protokołu IPsec na podstawie zasad protokołu IPsec skonfigurowanych przy użyciu kombinacji prefiksów adresów między siecią lokalną a sieci wirtualnej platformy Azure. Zasady (lub selektor ruchu) są zazwyczaj zdefiniowane jako lista dostępu w konfiguracji urządzenia sieci VPN. Wartość dla typu PolicyBased sieci VPN jest *PolicyBased*. Korzystając z PolicyBased sieci VPN, należy pamiętać o następujących ograniczeniach:
  
  * PolicyBased sieci VPN może **tylko** można używać w podstawowa jednostka SKU bramy. Ten typ sieci VPN nie jest zgodny z innymi jednostki SKU bramy.
  * Podczas korzystania z sieci VPN PolicyBased, może mieć tylko 1 tunelu.
  * PolicyBased sieci VPN można używać tylko dla połączeń S2S i tylko dla niektórych konfiguracji. Większość konfiguracji bramy sieci VPN wymaga RouteBased sieci VPN.
* **RouteBased**: sieci VPN z siecią typu RouteBased wcześniej były nazywane dynamicznymi bramami routingu w klasycznym modelu wdrażania. Sieci VPN z siecią typu RouteBased używają "tras" IP przekazywania lub tabeli routingu do kierowania pakietów do odpowiednich interfejsów tuneli. W dalszej kolejności interfejsy tuneli szyfrują lub odszyfrowują pakiety wchodzące do tuneli lub wychodzące z nich. Zasady (lub selektor ruchu) dla sieci VPN z siecią typu RouteBased są skonfigurowane jako dowolny z każdym (lub symbole wieloznaczne). Wartość dla typu RouteBased sieci VPN jest *RouteBased*.
