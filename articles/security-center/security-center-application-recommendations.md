---
title: Ochrona aplikacji w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten adres dokumentu zalecenia w Centrum zabezpieczeń Azure, które pomagają chronić aplikacje platformy Azure i są aktualizowane zgodnie z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866268"
---
# <a name="protecting-your-applications-in-azure-security-center"></a>Ochrona aplikacji w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure analizuje stan zabezpieczeń zasobów platformy Azure. Jeśli Centrum zabezpieczeń zostanie zidentyfikowana potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzają użytkownika przez proces konfigurowania wymaganych elementów sterujących.  Zalecenia dotyczą typów zasobów platformy Azure: maszynach wirtualnych (VM), sieci, SQL i aplikacji.

W tym artykule opisano zaleceń, które mają zastosowanie do aplikacji.  Aplikacji Centrum zalecenia dotyczące wdrażania zapory aplikacji sieci web.  Użyj poniższej tabeli jako odwołanie ułatwią zrozumienie zalecenia dostępności aplikacji i każdego z nich działania w przypadku zastosowania.

## <a name="available-application-recommendations"></a>Zalecenia dotyczące dostępnych aplikacji
| Zalecenie | Opis |
| --- | --- |
| [Dodawanie zapory aplikacji sieci Web](security-center-add-web-application-firewall.md) |Zaleca się, że wdrażania zapory aplikacji sieci web (WAF) dla punktów końcowych sieci web. Wszelkie publicznego połączonej adresu IP (IP poziomie wystąpienia lub IP równoważenia obciążenia) zawierający sieciową grupę zabezpieczeń skojarzoną z portami Otwórz przychodzący sieci web (80,443) jest wyświetlane zalecenie zapory aplikacji sieci Web.</br></br>Centrum zabezpieczeń zaleca się zainicjowanie obsługi zapory aplikacji sieci Web pomagających chronić przed atakami przeznaczonych dla aplikacji sieci web na maszynach wirtualnych i środowiska usługi aplikacji. Środowisko aplikacji (ASE) jest [Premium](https://azure.microsoft.com/pricing/details/app-service/) opcji plan usługi aplikacji Azure, która udostępnia środowisko pełni izolowanym środowisku, aby bezpiecznie pracować z aplikacjami usługi App service. Aby dowiedzieć się więcej na temat ASE, zobacz [dokumentację środowiska usługi aplikacji](../app-service/environment/intro.md).</br></br>Wiele aplikacji sieci web w Centrum zabezpieczeń można chronić, dodając te aplikacje do istniejących wdrożeń zapory aplikacji sieci Web. |
| [Finalizowanie ochrony aplikacji](security-center-add-web-application-firewall.md#finalize-application-protection) |Aby ukończyć konfigurację zapory aplikacji sieci Web, ruch musi zostać przekierowany do urządzenia zapory aplikacji sieci Web. Zmiany konfiguracji niezbędne kończy się po tego zalecenia. |

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat zaleceń, które są stosowane do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w Centrum zabezpieczeń Azure](security-center-virtual-machine-recommendations.md)
* [Ochrona sieci w Centrum zabezpieczeń Azure](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w Centrum zabezpieczeń Azure](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
