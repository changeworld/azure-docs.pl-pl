---
title: Zwiększanie poziomu bezpieczeństwa w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zwiększanie poziomu bezpieczeństwa, monitorując swoje zasoby w Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 28b4667a9ceb4b3534d85ba28668f06c750e22c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703880"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Zwiększanie poziomu bezpieczeństwa w usłudze Azure Security Center
Ten artykuł pomoże Ci zwiększanie poziomu bezpieczeństwa. Korzystanie z funkcji monitorowania w usłudze Azure Security Center, aby upewnić się, że bezpieczeństwo zasobu jest jak ścisłej, jak to możliwe i monitorując zgodność z zasadami.

## <a name="how-do-you-strengthen-your-security-posture"></a>Jak zwiększanie poziomu bezpieczeństwa
Monitorowanie jest często postrzegane jako obserwowanie i oczekiwanie na wystąpienie zdarzenia, aby można było zareagować na zaistniałą sytuację. Wzmocnienie poziomu bezpieczeństwa odnosi się do sytuacji aktywnej strategii przeprowadzania inspekcji zasobów w celu identyfikacji systemów, które nie spełniają standardów organizacji lub najlepszych rozwiązań.

Po włączeniu [zasad zabezpieczeń](tutorial-security-policy.md) dla zasobów subskrypcji usługa Security Center analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki. Informacje o konfiguracji sieci są dostępne natychmiast. W zależności od liczby maszyn wirtualnych i komputerów z zainstalowanym agentem, zebranie informacji o konfiguracji maszyn wirtualnych i komputerów, takich jak stan aktualizacji zabezpieczeń i konfiguracja systemu operacyjnego, może potrwać godzinę lub więcej, zanim zostaną one udostępnione. Można wyświetlić pełną listę problemów i sposobów ograniczenia funkcjonalności sieci i eliminowania ryzyka w **zalecenia** kafelka.

Możesz wyświetlić stan zabezpieczeń zasobów oraz informacje o problemach na typ zasobu:

- Aby monitorować kondycję swojej aplikacji i zasobów komputera i odbierać zalecenia dotyczące poprawy zabezpieczeń ich, zobacz [ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
- Do monitorowania zasobów sieciowych, takich jak maszyny wirtualne, sieciowe grupy zabezpieczeń i punktów końcowych i otrzymywać zalecenia dotyczące poprawy zabezpieczeń ich, zobacz [ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md) Aby uzyskać więcej informacji informacje. 
- Aby monitorować dane i Magazyn zasobów, takich jak serwery SQL i kont magazynu i odbierać zalecenia dotyczące poprawy ich zabezpieczeń, zobacz [Usługa ochrony usługi Azure SQL i danych w usłudze Azure Security Center](security-center-sql-service-recommendations.md) Aby uzyskać więcej informacji . 
- Monitorowanie zasobów tożsamościami i dostępem, w tym uwierzytelnianie wieloskładnikowe i konto uprawnienia, i odbierać zalecenia dotyczące poprawy zabezpieczeń ich, zobacz [monitorowania tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md) Aby uzyskać więcej informacji. 
- Aby monitorować tylko w czasie dostępu do zasobów, zobacz [zarządzanie dostępem maszyny wirtualnej przy użyciu dokładnie na czas](security-center-just-in-time.md) Aby uzyskać więcej informacji. 


Więcej informacji dotyczących stosowania zaleceń można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).



![Kafelek Kondycja zabezpieczeń zasobów](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md): Informacje o sposobie konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Usługa Azure Security Center — często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
