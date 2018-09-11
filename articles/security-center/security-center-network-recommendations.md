---
title: Ochrona sieci w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Adresy tego dokumentu, zalecenia w usłudze Azure Security Center, które ułatwiają ochronę sieci platformy Azure i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 12c00d6dfac6c9c2a377a8c142118ff6fd0af751
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302276"
---
# <a name="protecting-your-network-in-azure-security-center"></a>Ochrona sieci w usłudze Azure Security Center
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek.  Zalecenia odnoszą się do typów zasobów platformy Azure: maszyny wirtualne (VM), networking, SQL i aplikacji.

W tym artykule opisano zaleceń, które są stosowane do sieci.  Centrum zalecenia dotyczące sieci wokół następnej generacji zapory sieciowe grupy zabezpieczeń, konfigurowanie reguł ruchu przychodzącego i więcej.  Użyj poniższej tabeli jako odwołanie ułatwiające zrozumienie zalecenia dotyczące dostępności sieci i każdy z nich działanie w przypadku zastosowania.

## <a name="available-network-recommendations"></a>Zalecenia dotyczące sieci dostępne
| Zalecenie | Opis |
| --- | --- |
| [Dodawanie zapory nowej generacji](security-center-add-next-generation-firewall.md) |Zaleca się, możesz dodać następnej generacji zapory nowej (NGFW) z partnerem firmy Microsoft, aby zwiększyć Twoje zabezpieczenia. |
| [Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Zaleca się, aby skonfigurować reguły Sieciowej grupy zabezpieczeń sieci, które wymuszają ruch przychodzący do maszyny Wirtualnej za pośrednictwem swojej zapory nowej generacji. |
| [Włącz sieciowe grupy zabezpieczeń w podsieci lub maszyn wirtualnych](security-center-enable-network-security-groups.md) |Zaleca się włączenie sieciowe grupy zabezpieczeń dla podsieci lub maszyn wirtualnych. |
| [Ogranicz dostęp za pośrednictwem punktu końcowego z Internetem](security-center-restrict-access-through-internet-facing-endpoints.md) |Zaleca się, aby skonfigurować reguły ruchu przychodzącego dla sieciowych grup zabezpieczeń. |

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w usłudze Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrona aplikacji w usłudze Azure Security Center](security-center-application-recommendations.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
