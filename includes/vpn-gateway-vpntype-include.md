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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183113"
---
* **Zasady oparte na:** Sieci VPN oparte na zasadach były wcześniej nazywane statycznymi bramami routingu w klasycznym modelu wdrażania. Sieci VPN oparte na zasadach szyfrują i kierują pakiety za pośrednictwem tuneli IPsec na podstawie zasad IPsec skonfigurowanych przy kombinacji prefiksów adresów między siecią lokalną a siecią wirtualną platformy Azure. Zasady (lub selektor ruchu) są zazwyczaj zdefiniowane jako lista dostępu w konfiguracji urządzenia sieci VPN. Wartość typu sieci VPN opartej na zasadach to *PolicyBased*. Korzystając z sieci VPN opartej na zasadach, należy pamiętać o następujących ograniczeniach:
  
  * Sieci VPN oparte na zasadach mogą być używane **tylko** w jednostce SKU bramy podstawowej. Ten typ sieci VPN nie jest zgodny z innymi jednostkami SKU bramy.
  * Podczas korzystania z sieci VPN opartej na zasadach można mieć tylko 1 tunel.
  * Sieci VPN opartych na zasadach można używać tylko dla połączeń S2S i tylko dla niektórych konfiguracji. Większość konfiguracji bramy sieci VPN wymaga sieci VPN opartej na routinie.
* **RouteBased**: Sieci VPN oparte na routebased były wcześniej nazywane dynamicznymi bramami routingu w klasycznym modelu wdrażania. Sieci VPN oparte na routebased używają "tras" w tabeli przekierowania lub routingu IP do kierowania pakietów do odpowiednich interfejsów tunelowych. W dalszej kolejności interfejsy tuneli szyfrują lub odszyfrowują pakiety wchodzące do tuneli lub wychodzące z nich. Zasady (lub selektor ruchu) dla sieci VPN opartych na trasach są skonfigurowane jako karty typu dowolny (lub symboli wieloznacznych). Wartość typu sieci VPN opartej na routisłówsło *routebased*.
