---
title: Wzmocnij stan zabezpieczeń dzięki Azure Security Center | Microsoft Docs
description: Ten artykuł pomaga zwiększyć stan bezpieczeństwa przez monitorowanie zasobów w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 57d47712fe20fac0bf370a9a100a07a37d7aa753
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996668"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Wzmocnij stan zabezpieczeń dzięki Azure Security Center
Ten artykuł pomoże Ci wzmocnić stan zabezpieczeń. Skorzystaj z możliwości monitorowania w Azure Security Center, aby upewnić się, że zabezpieczenia zasobów są tak ścisłe, jak to możliwe, i monitoruj zgodność z zasadami.

## <a name="how-do-you-strengthen-your-security-posture"></a>Jak wzmocnić stan zabezpieczeń?
Często uważamy, że monitorowanie jest monitorowane i trwa oczekiwanie na wystąpienie zdarzenia, dzięki czemu możemy reagować na sytuację. Wzmocnienie stan bezpieczeństwa odnosi się do strategii aktywnej, która przeprowadza inspekcję zasobów, aby identyfikować systemy, które nie spełniają standardów organizacyjnych lub najlepszych rozwiązań.

Po włączeniu [zasad zabezpieczeń](tutorial-security-policy.md) dla zasobów subskrypcji Security Center analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki. Informacje o konfiguracji sieci są dostępne natychmiast. W zależności od liczby maszyn wirtualnych i komputerów z zainstalowanym agentem korzystanie z informacji o konfiguracji maszyn wirtualnych i konfiguracji komputera, takich jak stan aktualizacji zabezpieczeń i konfiguracja systemu operacyjnego, będzie możliwe dopiero po godzinie lub dłużej. Można wyświetlić pełną listę problemów i sposobów zwiększania funkcjonalności sieci i korygowania ryzyka na kafelku **zalecenia** .

Możesz wyświetlić stan zabezpieczeń zasobów i wszelkie problemy dotyczące poszczególnych typów zasobów:

- Aby monitorować kondycję zasobów komputera i aplikacji oraz uzyskać zalecenia dotyczące poprawy ich zabezpieczeń, zobacz [Ochrona maszyn i aplikacji w Azure Security Center](security-center-virtual-machine-protection.md)
- Aby monitorować zasoby sieciowe, takie jak maszyny wirtualne, sieciowe grupy zabezpieczeń i punkty końcowe, i uzyskać zalecenia dotyczące poprawy ich zabezpieczeń, zobacz [Ochrona sieci w Azure Security Center](security-center-network-recommendations.md) , aby uzyskać więcej informacji. 
- Aby monitorować zasoby danych i magazynu, takie jak serwery SQL i konta magazynu, i uzyskać zalecenia dotyczące poprawy ich zabezpieczeń, zobacz [Ochrona usługi Azure SQL i danych w Azure Security Center](security-center-sql-service-recommendations.md) , aby uzyskać więcej informacji. 
- Aby monitorować swoje tożsamości i uzyskiwać dostęp do zasobów, w tym uwierzytelnianie MFA i uprawnienia kont, a także uzyskać zalecenia dotyczące poprawy ich zabezpieczeń, zobacz temat [monitorowanie tożsamości i dostępu w Azure Security Center](security-center-identity-access.md) , aby uzyskać więcej informacji. 
- Aby monitorować dostęp just in Time do zasobów, zobacz [Zarządzanie dostępem do maszyn wirtualnych za pomocą just in Time](security-center-just-in-time.md) , aby uzyskać więcej informacji. 


Więcej informacji o sposobach stosowania zaleceń znajduje się [w temacie Wdrażanie zaleceń dotyczących zabezpieczeń w Azure Security Center](security-center-recommendations.md).



![Kafelek kondycja zabezpieczeń zasobów](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w programie Azure Security Center. Aby dowiedzieć się więcej na temat Azure Security Center, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): informacje na temat konfigurowania ustawień zabezpieczeń w programie Azure Security Center.
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md): informacje na temat zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitorowanie rozwiązań partnerskich za pomocą Azure Security Center](security-center-partner-solutions.md): informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center często zadawane](security-center-faq.md)pytania: Znajdź często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/): Znajdź wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure.
