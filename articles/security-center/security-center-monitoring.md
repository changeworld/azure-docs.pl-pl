---
title: Wzmocnij swoją postawę zabezpieczeń dzięki usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Ten artykuł pomaga wzmocnić postawę zabezpieczeń, monitorując zasoby w usłudze Azure Security Center.
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
ms.openlocfilehash: 40352b7ae1f3fb6b1178f2dfe70cdca9871c76b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603774"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Zwiększanie poziomu bezpieczeństwa za pomocą usługi Azure Security Center
Ten artykuł pomaga wzmocnić postawę bezpieczeństwa. Skorzystaj z funkcji monitorowania w usłudze Azure Security Center, aby upewnić się, że zabezpieczenia zasobów są tak rygorystyczne, jak to możliwe i monitorować zgodność z zasadami.

## <a name="how-do-you-strengthen-your-security-posture"></a>Jak wzmocnić swoją postawę bezpieczeństwa?
Monitorowanie jest często postrzegane jako obserwowanie i oczekiwanie na wystąpienie zdarzenia, aby można było zareagować na zaistniałą sytuację. Wzmocnienie postawy zabezpieczeń odnosi się do posiadania proaktywnej strategii, która przeprowadza inspekcje zasobów w celu zidentyfikowania systemów, które nie spełniają standardów organizacyjnych lub najlepszych praktyk.

Po włączeniu [zasad zabezpieczeń](tutorial-security-policy.md) dla zasobów subskrypcji usługa Security Center analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki. Informacje o konfiguracji sieci są dostępne natychmiast. W zależności od liczby maszyn wirtualnych i komputerów z zainstalowanym agentem, zebranie informacji o konfiguracji maszyn wirtualnych i komputerów, takich jak stan aktualizacji zabezpieczeń i konfiguracja systemu operacyjnego, może potrwać godzinę lub więcej, zanim zostaną one udostępnione. Pełną listę problemów i sposobów wzmocnienia sieci i korygowania ryzyka można wyświetlić na kafelku **Zalecenia.**

Można wyświetlić stan zabezpieczeń zasobów i wszelkie problemy dla każdego typu zasobu:

- Aby monitorować kondycję zasobów komputera i aplikacji oraz otrzymywać zalecenia dotyczące poprawy ich bezpieczeństwa, zobacz [Ochrona komputerów i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
- Aby monitorować zasoby sieciowe, takie jak maszyny wirtualne, grupy zabezpieczeń sieci i punkty końcowe, oraz otrzymywać zalecenia dotyczące poprawy ich bezpieczeństwa, zobacz [Ochrona sieci w usłudze Azure Security Center, aby](security-center-network-recommendations.md) uzyskać więcej informacji. 
- Aby monitorować zasoby danych i magazynu, takie jak serwery SQL i konta magazynu, i otrzymywać zalecenia dotyczące poprawy ich bezpieczeństwa, zobacz [Ochrona usługi SQL i danych platformy Azure w usłudze Azure Security Center, aby](security-center-sql-service-recommendations.md) uzyskać więcej informacji. 
- Aby monitorować tożsamość i dostęp do zasobów, w tym usługi MFA i uprawnień do konta, oraz otrzymywać zalecenia dotyczące poprawy ich bezpieczeństwa, zobacz [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center, aby](security-center-identity-access.md) uzyskać więcej informacji. 
- Aby monitorować dostęp just-in-time do zasobów, zobacz [Zarządzanie dostępem do maszyny wirtualnej przy użyciu just-in-time,](security-center-just-in-time.md) aby uzyskać więcej informacji. 


Więcej informacji dotyczących stosowania zaleceń można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).



![Kafelek Kondycja zabezpieczeń zasobów](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.