---
title: Zarządzanie między dzierżawcami w Azure Security Center | Microsoft Docs
description: " Dowiedz się, jak włączyć funkcję zbierania danych w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: v-mohabe
ms.openlocfilehash: d6b5b528c3021bfb62bc30ad5910524db36e7e95
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950546"
---
# <a name="cross-tenant-management-in-security-center"></a>Zarządzanie między dzierżawcami w Security Center

Zarządzanie między dzierżawcami pozwala wyświetlać stan zabezpieczeń wielu dzierżawców w usłudze Security Center i zarządzać nimi przy użyciu delegowanego [zarządzania zasobami platformy Azure](../lighthouse/concepts/azure-delegated-resource-management.md) . Wydajnie Zarządzaj wieloma dzierżawcami, z poziomu jednego widoku, bez konieczności logowania się do katalogu każdej dzierżawy.

- Dostawcy usług mogą zarządzać stanem zabezpieczeń zasobów dla wielu klientów w ramach własnej dzierżawy.

- Zespoły ds. zabezpieczeń organizacji z wieloma dzierżawcami mogą wyświetlać stan zabezpieczeń i zarządzać nimi z jednej lokalizacji.

## <a name="set-up-cross-tenant-management"></a>Konfigurowanie zarządzania przez wiele dzierżawców

Skonfiguruj zarządzanie pośrednictwem wielu dzierżawców, delegując dostęp do zasobów zarządzanych dzierżawców do własnej dzierżawy przy użyciu funkcji [zarządzania zasobami delegowanych przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> Zarządzanie zasobami delegowanymi przez platformę Azure to jeden z najważniejszych składników usługi Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Jak działa zarządzanie między dzierżawcami w Security Center

Możesz przeglądać subskrypcje wielu dzierżawców i zarządzać nimi w taki sam sposób, w jaki zarządzasz wieloma subskrypcjami w ramach jednej dzierżawy.

Na górnym pasku menu kliknij ikonę filtru, a następnie wybierz subskrypcje, z katalogu każdego dzierżawy, chcesz wyświetlić.

  ![Filtrowanie dzierżawców](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Widoki i akcje są zasadniczo takie same. Oto kilka przykładów:

- **Zarządzanie zasadami zabezpieczeń**: W jednym widoku można zarządzać stan zabezpieczeń wielu zasobów przy użyciu [zasad](tutorial-security-policy.md), podejmować działania z zaleceniami dotyczącymi zabezpieczeń oraz zbierać dane związane z zabezpieczeniami i zarządzać nimi.
- **Ulepszanie bezpiecznego stan oceny i zgodności**: Widoczność między dzierżawcami umożliwia wyświetlenie ogólnych stan zabezpieczeń wszystkich dzierżawców oraz miejsce i sposób, w jaki najlepiej poprawić [bezpieczny wynik](security-center-secure-score.md) i [zgodność stan](security-center-compliance-dashboard.md) dla każdego z nich.
- **Skoryguj zalecenia**: Monitoruj i Koryguj [zalecenia](security-center-recommendations.md) dotyczące wielu zasobów z różnych dzierżawców jednocześnie. Następnie można natychmiast zaradzić sobie z lukami w zabezpieczeniach, które stwarzają największe ryzyko dla wszystkich dzierżawców.
- **Zarządzanie alertami**: Wykrywaj [alerty](security-center-alerts-overview.md) w różnych dzierżawach. Wykonaj działania dotyczące zasobów, które są niezgodne z krokami [korygowania](security-center-managing-and-responding-alerts.md)akcji.

- **Zarządzaj zaawansowanymi funkcjami obrony przed chmurą i nie tylko**: Zarządzaj różnymi usługami wykrywania zagrożeń i ochrony, takimi jak [dostęp do maszyn wirtualnych just-in-Time (JIT)](security-center-just-in-time.md), [adaptacyjne Zabezpieczanie sieci](security-center-adaptive-network-hardening.md), [adaptacyjne kontrolki aplikacji](security-center-adaptive-application.md)i nie tylko.
 
## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, jak zarządzanie przez wiele dzierżawców działa w Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Wzmocnij stan zabezpieczeń, korzystając z Azure Security Center](security-center-monitoring.md)— Dowiedz się, jak monitorować kondycję zasobów platformy Azure.
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
