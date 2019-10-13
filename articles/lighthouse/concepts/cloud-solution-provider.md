---
title: Zagadnienia dotyczące programu Cloud Solution Provider
description: W przypadku korzystania z funkcji zarządzania zasobami delegowanymi przez platformę Azure ważne jest, aby uwzględnić zabezpieczenia i kontrolę dostępu.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 76df2942b176fd7d0e785213e44655ffd11da410
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286207"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse i program dostawcy rozwiązań w chmurze

Jeśli jesteś partnerem [CSP (dostawca rozwiązań w chmurze)](https://docs.microsoft.com/partner-center/csp-overview) , możesz uzyskać dostęp do subskrypcji platformy Azure utworzonych dla klientów za pośrednictwem programu CSP przy użyciu funkcji [Administruj w imieniu (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Ten dostęp pozwala bezpośrednio obsługiwać i konfigurować subskrypcje klientów oraz zarządzać nimi.

Usługa Azure Lighthouse umożliwia zarządzanie zasobami delegowanymi na platformie Azure wraz z AOBO. Pozwala to zwiększyć bezpieczeństwo i ograniczyć niepotrzebny dostęp przez umożliwienie użytkownikom bardziej szczegółowych uprawnień. Umożliwia również zwiększenie wydajności i skalowalności, ponieważ użytkownicy mogą korzystać z wielu subskrypcji klienta przy użyciu jednej nazwy logowania w dzierżawie.

## <a name="administer-on-behalf-of-aobo"></a>Administruj w imieniu (AOBO)

W przypadku usługi AOBO każdy użytkownik z rolą [agenta administracyjnego](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) w Twojej dzierżawie będzie miał dostęp AOBO do subskrypcji platformy Azure utworzonych za pomocą programu CSP. Wszyscy użytkownicy, którzy potrzebują dostępu do subskrypcji klientów, muszą być członkami tej grupy. AOBO nie pozwala na elastyczność tworzenia odrębnych grup, które współpracują z różnymi klientami, lub aby włączyć różne role dla grup lub użytkowników.

![Zarządzanie dzierżawcą przy użyciu AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami delegowanymi na platformie Azure

Korzystając z funkcji zarządzania zasobami delegowanymi przez platformę Azure, można przypisać różne grupy do różnych klientów lub ról, jak pokazano na poniższym diagramie. Ze względu na to, że użytkownicy będą mieć odpowiedni poziom dostępu za pomocą zarządzania zasobami delegowanymi przez platformę Azure, można zmniejszyć liczbę użytkowników, którzy mają rolę agenta administratora (i w związku z tym mieć pełny dostęp AOBO). Pozwala to zwiększyć bezpieczeństwo przez ograniczenie niepotrzebnego dostępu do zasobów klientów. Zapewnia również większą elastyczność zarządzania wieloma klientami w odpowiedniej skali.

Aby dołączać subskrypcję utworzoną za pomocą programu CSP, wykonaj kroki opisane w sekcji Dołączanie [subskrypcji do zarządzania zasobami delegowanymi przez platformę Azure](../how-to/onboard-customer.md). Każdy użytkownik, który ma rolę Agent administracyjny w dzierżawie, może wykonać to dołączenie.

![Zarządzanie dzierżawcą przy użyciu AOBO i zarządzania zasobami delegowanymi przez platformę Azure](../media/csp-2.jpg)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się [, jak dołączyć subskrypcję do zarządzania zasobami delegowanymi przez platformę Azure](../how-to/onboard-customer.md).
- Dowiedz się więcej o [programie dostawcy rozwiązań w chmurze](https://docs.microsoft.com/partner-center/csp-overview).
