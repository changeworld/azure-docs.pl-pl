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
ms.openlocfilehash: 05dc8ae48a9164e4f7118d378ab0eb7c30a4249e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
Podczas tworzenia bramy sieci wirtualnej musisz wybrać jednostkę SKU bramy do użycia. Wybierz jednostkę SKU spełniającą Twoje wymagania na podstawie typów obciążeń, przepustowości, funkcji i umów SLA.

###  <a name="benchmark"></a>Jednostki SKU bramy według tunelowania, połączenia i przepływności

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>Jednostki SKU bramy przez zestaw funkcji

Nową bramę sieci VPN jednostki SKU usprawnić zestawy funkcji, które są oferowane w bramy:

| **SKU**| **Funkcje**|
| ---    | ---         |
|**Podstawowe** (*)   | **VPN opartej na trasach**: 10 tuneli o P2S; nie uwierzytelniania RADIUS na potrzeby P2S; nie protokołu IKEv2 dla P2S<br>**Sieć VPN oparta na zasadach** (IKEv1): 1 tunel, bez połączeń typu punkt-lokacja|
| **VpnGw1, VpnGw2 i VpnGw3** | **VPN opartej na trasach**: maksymalnie 30 tuneli (*), P2S, protokołu BGP, zasady IPsec i IKE aktywny aktywny, niestandardowe, instalacją programu ExpressRoute lub sieć VPN |
|        |             |

( * ) Można skonfigurować „PolicyBasedTrafficSelectors” do łączenia bramy sieci VPN opartej na trasach (VpnGw1, VpnGw2, VpnGw3) z wieloma lokalnymi urządzeniami zapory opartymi na zasadach. Aby zapoznać się ze szczegółami, zobacz artykuł [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Połączenie bram sieci VPN z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach przy użyciu programu PowerShell).

(**) Podstawowy SKU jest uważany za starszej wersji jednostki SKU. Podstawowy SKU ma pewne ograniczenia funkcji. Nie można zmienić rozmiaru bramy, która korzysta z podstawowej jednostki SKU na jedną z nowych jednostek SKU bramy, należy zmienić do nowej jednostki SKU, który obejmuje usunięcie i ponowne utworzenie bramy sieci VPN.

###  <a name="workloads"></a>Jednostki SKU bramy - produkcyjnej wersji programu vs. w środowisku tworzenia i testowania

Z powodu różnic w SLA i zestawy funkcji zaleca się następujące jednostki SKU w środowisku produkcyjnym, a tworzenie i testowanie:

| **Obciążenie**                       | **Jednostki SKU**               |
| ---                                | ---                    |
| **Tryb produkcyjny, obciążenia krytyczne** | VpnGw1, VpnGw2, VpnGw3 |
| **Środowisko tworzenia i testowania lub weryfikacja koncepcji**   | Basic (*)                 |
|                                    |                        |

(**) Podstawowy SKU jest uznawany za starszej wersji jednostki SKU i ma ograniczenia funkcji. Sprawdź, czy funkcji, które są potrzebne jest obsługiwana, przed użyciem podstawowy SKU.

Jeśli używasz starego jednostki SKU (starsze) zalecenia SKU produkcji są standardowe i wysokowydajnej. Informacje i instrukcje dotyczące starego jednostki SKU dla [jednostki SKU bramy (starsze)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).