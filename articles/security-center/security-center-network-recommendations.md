---
title: Ochrona sieci w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten adres dokumentu zalecenia w Centrum zabezpieczeń Azure, które ułatwiają ochronę sieci platformy Azure i są aktualizowane zgodnie z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866478"
---
# <a name="protecting-your-network-in-azure-security-center"></a>Ochrona sieci w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure analizuje stan zabezpieczeń zasobów platformy Azure. Jeśli Centrum zabezpieczeń zostanie zidentyfikowana potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzają użytkownika przez proces konfigurowania wymaganych elementów sterujących.  Zalecenia dotyczą typów zasobów platformy Azure: maszynach wirtualnych (VM), sieci, SQL i aplikacji.

W tym artykule opisano zaleceń, które są stosowane do sieci.  Centrum zalecenia dotyczące sieci wokół następnej generacji zapór, grup zabezpieczeń sieci, konfigurowanie reguł ruchu przychodzącego i.  Użyj poniższej tabeli jako odwołanie ułatwią zrozumienie zalecenia dotyczące dostępnych sieci i jak każdą z nich działa w przypadku zastosowania.

## <a name="available-network-recommendations"></a>Zalecenia dostępnej sieci
| Zalecenie | Opis |
| --- | --- |
| [Dodawanie zapory nowej generacji](security-center-add-next-generation-firewall.md) |Zaleca się dodanie dalej zapory generacji (NGFW) z partnerem firmy Microsoft, aby zwiększyć z ochrony zabezpieczeń. |
| [Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Zaleca się, aby skonfigurować reguły grupy (NSG) zabezpieczeń sieciowych, który wymusza ruch przychodzący do maszyny Wirtualnej za pośrednictwem sieci NGFW. |
| [Włączenie grup zabezpieczeń sieci dla podsieci lub maszyny wirtualne](security-center-enable-network-security-groups.md) |Zaleca się włączenie grup NSG dla podsieci lub maszyn wirtualnych. |
| [Ogranicz dostęp za pośrednictwem internetowy punkt końcowy](security-center-restrict-access-through-internet-facing-endpoints.md) |Zaleca się, aby skonfigurować reguły ruchu przychodzącego dla grup NSG. |

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat zaleceń, które są stosowane do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w Centrum zabezpieczeń Azure](security-center-virtual-machine-recommendations.md)
* [Ochrona aplikacji w Centrum zabezpieczeń Azure](security-center-application-recommendations.md)
* [Ochrona usługi Azure SQL w Centrum zabezpieczeń Azure](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
