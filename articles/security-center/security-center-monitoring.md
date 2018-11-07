---
title: Zwiększanie poziomu bezpieczeństwa w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zwiększanie poziomu bezpieczeństwa, monitorując swoje zasoby w Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 23369ac9f6cef74bac045017e8f956470e9a1159
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248864"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Zwiększanie poziomu bezpieczeństwa w usłudze Azure Security Center
Ten artykuł pomoże Ci zwiększanie poziomu bezpieczeństwa. Korzystanie z funkcji monitorowania w usłudze Azure Security Center, aby upewnić się, że bezpieczeństwo zasobu jest jak ścisłej, jak to możliwe i monitorując zgodność z zasadami.

## <a name="how-do-you-strengthen-your-security-posture"></a>Jak zwiększanie poziomu bezpieczeństwa
Monitorowanie jest często postrzegane jako obserwowanie i oczekiwanie na wystąpienie zdarzenia, aby można było zareagować na zaistniałą sytuację. Wzmocnienie poziomu bezpieczeństwa odnosi się do sytuacji aktywnej strategii przeprowadzania inspekcji zasobów w celu identyfikacji systemów, które nie spełniają standardów organizacji lub najlepszych rozwiązań.

Po włączeniu [zasad zabezpieczeń](security-center-policies.md) dla zasobów subskrypcji usługa Security Center analizuje zabezpieczenia zasobów, aby zidentyfikować potencjalne luki. Informacje o konfiguracji sieci są dostępne natychmiast. W zależności od liczby maszyn wirtualnych i komputerów z zainstalowanym agentem, zebranie informacji o konfiguracji maszyn wirtualnych i komputerów, takich jak stan aktualizacji zabezpieczeń i konfiguracja systemu operacyjnego, może potrwać godzinę lub więcej, zanim zostaną one udostępnione. Można wyświetlić pełną listę problemów i sposobów ograniczenia funkcjonalności sieci i eliminowania ryzyka w **zalecenia** kafelka.

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

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
