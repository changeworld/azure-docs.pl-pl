---
title: Zagadnienia dotyczące programu Cloud Solution Provider
description: W przypadku partnerów programu CSP delegowane zarządzanie zasobami systemu Azure pomaga zwiększyć bezpieczeństwo i kontrolę dzięki włączeniu szczegółowych uprawnień.
ms.date: 10/23/2019
ms.topic: overview
ms.openlocfilehash: 3ea32418cdf6808ad311d343d28ba2778740dbe4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132510"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse i program dostawcy rozwiązań w chmurze

Jeśli jesteś partnerem [CSP (dostawca rozwiązań w chmurze)](https://docs.microsoft.com/partner-center/csp-overview) , możesz uzyskać dostęp do subskrypcji platformy Azure utworzonych dla klientów za pośrednictwem programu CSP przy użyciu funkcji [Administruj w imieniu (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Ten dostęp pozwala bezpośrednio obsługiwać i konfigurować subskrypcje klientów oraz zarządzać nimi.

[Usługa Azure Lighthouse](../overview.md)umożliwia zarządzanie zasobami delegowanymi na platformie Azure wraz z AOBO. Pozwala to zwiększyć bezpieczeństwo i ograniczyć niepotrzebny dostęp przez umożliwienie użytkownikom bardziej szczegółowych uprawnień. Umożliwia również zwiększenie wydajności i skalowalności, ponieważ użytkownicy mogą korzystać z wielu subskrypcji klienta przy użyciu jednej nazwy logowania w dzierżawie.

> [!TIP]
> Aby pomóc w zabezpieczeniu zasobów klientów, należy zapoznać się z naszymi [zalecanymi rozwiązaniami](recommended-security-practices.md) dotyczącymi zabezpieczeń oraz z [wymaganiami dotyczącymi zabezpieczeń partnerów](https://docs.microsoft.com/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Administruj w imieniu (AOBO)

W przypadku usługi AOBO każdy użytkownik z rolą [agenta administracyjnego](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) w Twojej dzierżawie będzie miał dostęp AOBO do subskrypcji platformy Azure utworzonych za pomocą programu CSP. Wszyscy użytkownicy, którzy potrzebują dostępu do subskrypcji klientów, muszą być członkami tej grupy. AOBO nie pozwala na elastyczność tworzenia odrębnych grup, które współpracują z różnymi klientami, lub aby włączyć różne role dla grup lub użytkowników.

![Zarządzanie dzierżawcą przy użyciu AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami delegowanymi na platformie Azure

Korzystając z funkcji zarządzania zasobami delegowanymi przez platformę Azure, można przypisać różne grupy do różnych klientów lub ról, jak pokazano na poniższym diagramie. Ze względu na to, że użytkownicy będą mieć odpowiedni poziom dostępu za pomocą zarządzania zasobami delegowanymi przez platformę Azure, można zmniejszyć liczbę użytkowników, którzy mają rolę agenta administratora (i w związku z tym mieć pełny dostęp AOBO). Pozwala to zwiększyć bezpieczeństwo przez ograniczenie niepotrzebnego dostępu do zasobów klientów. Zapewnia również większą elastyczność zarządzania wieloma klientami w odpowiedniej skali.

Aby dołączać subskrypcję utworzoną za pomocą programu CSP, wykonaj kroki opisane w sekcji Dołączanie [subskrypcji do zarządzania zasobami delegowanymi przez platformę Azure](../how-to/onboard-customer.md). Każdy użytkownik, który ma rolę Agent administracyjny w dzierżawie, może wykonać to dołączenie.

![Zarządzanie dzierżawcą przy użyciu AOBO i zarządzania zasobami delegowanymi przez platformę Azure](../media/csp-2.jpg)

> [!NOTE]
> Na [stronie **moi klienci** w Azure Portal](../how-to/view-manage-customers.md) teraz znajduje się sekcja **dostawca rozwiązań w chmurze (wersja zapoznawcza)** , która wyświetla informacje o rozliczeniach i zasoby dla klientów programu CSP, którzy [podpisali umowę klienta firmy Microsoft (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) i są objęte planem platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł Wprowadzenie [do rozliczeń umowy partnerskiej firmy Microsoft](https://docs.microsoft.com/azure/billing/mpa-overview).
>
> Klienci korzystający z programu CSP mogą pojawić się w tej sekcji niezależnie od tego, czy zostały dołączone do zarządzania zasobami delegowanymi przez platformę Azure. Jeśli są one dostępne, pojawią się również w sekcji **klienci** zgodnie z opisem w temacie [Wyświetlanie i zarządzanie klientami oraz delegowanymi zasobami](../how-to/view-manage-customers.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się [, jak dołączyć subskrypcję do zarządzania zasobami delegowanymi przez platformę Azure](../how-to/onboard-customer.md).
- Dowiedz się więcej o [programie dostawcy rozwiązań w chmurze](https://docs.microsoft.com/partner-center/csp-overview).
