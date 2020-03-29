---
title: Zarządzanie między dzierżawcami w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować zarządzanie między dzierżawcami w celu zarządzania postawą zabezpieczeń wielu dzierżaw w usłudze Security Center przy użyciu zarządzania zasobami delegowanymi platformy Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919485"
---
# <a name="cross-tenant-management-in-security-center"></a>Zarządzanie między dzierżawcami w centrum zabezpieczeń

Zarządzanie między dzierżawcami umożliwia wyświetlanie i zarządzanie postawą zabezpieczeń wielu dzierżaw w usłudze Security Center przy użyciu [usługi Azure zarządzania zasobami delegowanymi.](../lighthouse/concepts/azure-delegated-resource-management.md) Zarządzaj wieloma dzierżawami wydajnie, z jednego widoku, bez konieczności logowania się do katalogu każdej dzierżawy.

- Dostawcy usług mogą zarządzać postawą zabezpieczeń zasobów dla wielu klientów, z poziomu ich własnej dzierżawy.

- Zespoły zabezpieczeń organizacji z wieloma dzierżawcami mogą wyświetlać i zarządzać swoją postawą zabezpieczeń z jednej lokalizacji.

## <a name="set-up-cross-tenant-management"></a>Konfigurowanie zarządzania między dzierżawami

Konfigurowanie zarządzania między dzierżawami przez delegowanie dostępu do zasobów zarządzanych dzierżawców do własnej dzierżawy przy użyciu [usługi Azure zarządzanie zasobami delegowanymi](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> Zarządzanie zasobami delegowanymi platformy Azure jest jednym z kluczowych składników latarni morskiej platformy Azure.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Jak działa zarządzanie między dzierżawcami w umykarze zabezpieczeń

Możesz przeglądać subskrypcje i zarządzać nimi w wielu dzierżawach w taki sam sposób, w jaki zarządzasz wieloma subskrypcjami w jednej dzierżawie.

Na górnym pasku menu kliknij ikonę filtru i wybierz subskrypcje z katalogu każdej dzierżawy, które chcesz wyświetlić.

  ![Filtruj dzierżawców](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Widoki i działania są w zasadzie takie same. Oto kilka przykładów:

- **Zarządzanie zasadami zabezpieczeń:** z jednego widoku zarządzaj postawą zabezpieczeń wielu zasobów za pomocą [zasad,](tutorial-security-policy.md)podejmuj akcje z zaleceniami dotyczącymi zabezpieczeń oraz zbieraj i zarządzaj danymi związanymi z zabezpieczeniami.
- **Popraw bezpieczny wynik i postawę zgodności:** widoczność między dzierżawcami umożliwia wyświetlanie ogólnej postawy zabezpieczeń wszystkich dzierżawców oraz miejsca i najlepszego ulepszenia [postawy bezpiecznego wyniku](security-center-secure-score.md) i [zgodności](security-center-compliance-dashboard.md) dla każdego z nich.
- **Korygowanie zaleceń:** monitoruj i koryguj [zalecenie](security-center-recommendations.md) dotyczące wielu zasobów od różnych dzierżaw jednocześnie. Następnie można natychmiast rozwiązać luki, które stanowią największe ryzyko we wszystkich dzierżawców.
- **Zarządzanie alertami:** [wykrywanie alertów](security-center-alerts-overview.md) w różnych dzierżawach. Podejmij działania w odniesieniu do zasobów, które nie są zgodne z [krokami zaradczymi.](security-center-managing-and-responding-alerts.md)

- **Zarządzanie zaawansowanymi funkcjami ochrony w chmurze i nie tylko:** Zarządzaj różnymi usługami ochrony przed zagrożeniami, takimi jak [dostęp do maszyn wirtualnych just-in-time (JIT),](security-center-just-in-time.md) [adaptacyjne hartowanie sieci,](security-center-adaptive-network-hardening.md) [adaptacyjne sterowanie aplikacjami](security-center-adaptive-application.md)i inne.
 
## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, jak działa zarządzanie między dzierżawcami w umykarze zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Wzmocnij swoją postawę zabezpieczeń dzięki usłudze Azure Security Center](security-center-monitoring.md) — dowiedz się, jak monitorować kondycję zasobów platformy Azure.
* [Często zadawane pytania dotyczące usługi Azure Security Center](faq-general.md) — znajdź często zadawane pytania dotyczące korzystania z usługi.
* [Dowiedz się więcej o latarni morskiej platformy Azure w scenariuszach korporacyjnych](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) — dowiedz się, jak latarnia morska platformy Azure może uprościć zarządzanie między dzierżawcami w przedsiębiorstwie, które korzysta z wielu dzierżaw usługi Azure AD.