---
title: Zagadnienia dotyczące programu Cloud Solution Provider
description: W przypadku partnerów dostawcy usług kryptograficznych zarządzanie zasobami delegowanych platformy Azure pomaga zwiększyć bezpieczeństwo i kontrolę, włączając szczegółowe uprawnienia.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 66ea74751f12a499a1e2d9e083497da31746e3c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456914"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Latarnia morska platformy Azure i program dostawcy rozwiązań w chmurze

Jeśli jesteś partnerem [dostawcy usług kryptograficznych (Dostawca rozwiązań w chmurze),](https://docs.microsoft.com/partner-center/csp-overview) możesz już uzyskać dostęp do subskrypcji platformy Azure utworzonych dla klientów za pośrednictwem programu CSP przy użyciu funkcji [Admin On Behalf Of (AOBO).](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) Ten dostęp umożliwia bezpośrednie wsparcie, skonfigurowanie i zarządzanie subskrypcjami klientów.

Dzięki [latarni morskiej platformy Azure](../overview.md)można używać zarządzania zasobami delegowanymi platformy Azure wraz z usługą AOBO. Pomaga to zwiększyć bezpieczeństwo i zmniejsza niepotrzebny dostęp, włączając bardziej szczegółowe uprawnienia dla użytkowników. Pozwala również na większą wydajność i skalowalność, ponieważ użytkownicy mogą pracować w wielu subskrypcjach klientów przy użyciu jednego logowania w dzierżawie.

> [!TIP]
> Aby chronić zasoby klientów, należy zapoznać się z [zalecanymi praktykami bezpieczeństwa](recommended-security-practices.md) i przestrzegać ich zgodnie z [wymaganiami dotyczącymi zabezpieczeń.](https://docs.microsoft.com/partner-center/partner-security-requirements)

## <a name="administer-on-behalf-of-aobo"></a>Administrowanie w imieniu (AOBO)

Z AOBO, każdy użytkownik z roli [agenta administracyjnego](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) w dzierżawie będzie miał dostęp AOBO do subskrypcji platformy Azure, które można utworzyć za pośrednictwem programu CSP. Każdy użytkownik, który potrzebuje dostępu do subskrypcji klientów, musi być członkiem tej grupy. AOBO nie zezwala na elastyczność do tworzenia różnych grup, które pracują z różnymi klientami lub włączyć różne role dla grup lub użytkowników.

![Zarządzanie dzierżawą przy użyciu AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami delegowanymi na platformie Azure

Za pomocą usługi Azure zarządzania zasobami delegowanymi, można przypisać różne grupy do różnych klientów lub ról, jak pokazano na poniższym diagramie. Ponieważ użytkownicy będą mieli odpowiedni poziom dostępu za pośrednictwem usługi Azure zarządzania zasobami delegowanymi, można zmniejszyć liczbę użytkowników, którzy mają rolę agenta administracyjnego (a tym samym mają pełny dostęp AOBO). Pomaga to zwiększyć bezpieczeństwo, ograniczając niepotrzebny dostęp do zasobów klientów. Zapewnia również większą elastyczność w zarządzaniu wieloma klientami na dużą skalę.

Dołączanie subskrypcji utworzonej za pośrednictwem programu CSP jest zgodne z instrukcjami opisanymi w [pokładzie subskrypcji usługi Azure zarządzania zasobami delegowanymi](../how-to/onboard-customer.md). Każdy użytkownik, który ma rolę agenta administracyjnego w dzierżawie, może wykonać to dołączanie.

![Zarządzanie dzierżawą przy użyciu usługi AOBO i zarządzania zasobami delegowanymi platformy Azure](../media/csp-2.jpg)

> [!NOTE]
> Strona [ **Moi klienci** w witrynie Azure portal](../how-to/view-manage-customers.md) zawiera teraz sekcję Dostawca **rozwiązań w chmurze (Wersja zapoznawcza),** która wyświetla informacje rozliczeniowe i zasoby dla klientów usług CSP, którzy [podpisali umowę klienta firmy Microsoft (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) i są [w ramach planu platformy Azure.](https://docs.microsoft.com/partner-center/azure-plan-get-started) Aby uzyskać więcej informacji, zobacz [Wprowadzenie do konta rozliczeniowego umowy z partnerem firmy Microsoft](../../billing/mpa-overview.md).
>
> Klienci dostawcy usług kryptograficznych mogą pojawić się w tej sekcji, niezależnie od tego, czy również zostali włączeni do zarządzania zasobami delegowanymi platformy Azure. Jeśli tak, pojawią się również w sekcji **Klienci,** zgodnie z opisem w [sekcji Wyświetlanie i zarządzanie klientami oraz delegowanymi zasobami.](../how-to/view-manage-customers.md) Podobnie klient dostawcy usług kryptograficznych nie musi pojawiać się w sekcji Dostawca rozwiązań w **chmurze (wersja zapoznawcza)** **w witrynie Moi klienci,** aby można było ich przywdzielić do zarządzania zasobami delegowanymi platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się, jak [ująć subskrypcję usługi Azure w zakresie zarządzania zasobami delegowanymi.](../how-to/onboard-customer.md)
- Dowiedz się więcej o [programie Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
