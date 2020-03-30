---
title: Co to jest Azure Lighthouse?
description: Usługa Azure Lighthouse umożliwia dostawcom usług świadczenie usług zarządzanych dla swoich klientów dzięki wyższej automatyzacji i wydajności na dużą skalę.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0bec1f5b727ca3ecd604d2654358ca9a5507abe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75646198"
---
# <a name="what-is-azure-lighthouse"></a>Co to jest Azure Lighthouse?

Usługa Azure Lighthouse oferuje dostawcom usług pojedynczą płaszczyznę sterowania do wyświetlania platformy Azure i zarządzania nią we wszystkich swoich klientach dzięki wyższej automatyzacji, skalowaniu i ulepszonemu zarządzaniu. Dzięki latarni morskiej platformy Azure dostawcy usług mogą dostarczać usługi zarządzane przy użyciu kompleksowych i niezawodnych narzędzi do zarządzania wbudowanych w platformę Azure. Ta oferta może również przynieść korzyści organizacjom IT przedsiębiorstwa zarządzającym zasobami w wielu dzierżawach.

![Diagram przeglądowy latarni morskiej platformy Azure](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Korzyści

Latarnia morska platformy Azure pomaga w opłacalnym i wydajnym tworzeniu i dostarczaniu usług zarządzanych dla klientów. Korzyści:

- **Zarządzanie na dużą skalę**: zaangażowanie klientów i operacje cyklu życia w celu zarządzania zasobami klienta są łatwiejsze i bardziej skalowalne.
- **Większa widoczność i precyzja dla klientów:** Klienci, których zasoby zarządzasz, będą mieli lepszy wgląd w twoje działania i precyzyjną kontrolę nad zakresem delegowania do zarządzania, podczas gdy twój adres IP jest zachowywany.
- **Kompleksowe i ujednolicone narzędzia platformy:** Nasze środowisko narzędziowe dotyczy kluczowych scenariuszy dostawców usług, w tym wielu modeli licencjonowania, takich jak EA, CSP i płatności zgodnie z rzeczywistymi oczekiwaniami. Nowe funkcje współpracują z istniejącymi narzędziami i interfejsami API, modelami licencjonowania i programami partnerskimi, takimi jak [program Dostawcy rozwiązań w chmurze (CSP).](https://docs.microsoft.com/partner-center/csp-overview) Opcje latarni morskiej platformy Azure można zintegrować z istniejącymi przepływami pracy i aplikacjami, a także śledzić wpływ na zaangażowanie klientów, [łącząc identyfikator partnera.](../billing/billing-partner-admin-link-started.md)

Nie ma żadnych dodatkowych kosztów związanych z używaniem latarni morskiej platformy Azure do zarządzania zasobami platformy Azure klientów.

## <a name="capabilities"></a>Możliwości

Latarnia morska platformy Azure oferuje wiele sposobów, aby usprawnić zaangażowanie klientów i zarządzanie nimi:

- **Zarządzanie zasobami delegowanymi platformy Azure:** bezpieczne zarządzanie zasobami platformy Azure klientów z poziomu własnej dzierżawy bez konieczności przełączania płaszczyzn kontekstu i sterowania nimi. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami delegowanymi platformy Azure](concepts/azure-delegated-resource-management.md).
- **Nowe środowiska portalu Azure:** wyświetlanie informacji między dzierżawcami na nowej stronie **Moi klienci** w [witrynie Azure portal](https://portal.azure.com). Odpowiedni **blok dostawców usług** umożliwia klientom wyświetlanie dostępu do usługodawcy i zarządzanie nim. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie klientami](./how-to/view-manage-customers.md) oraz [Wyświetlanie dostawców usług i zarządzanie nimi](how-to/view-manage-service-providers.md).
- **Szablony usługi Azure Resource Manager:** łatwiejsze wykonywanie zadań związanych z zarządzaniem, w tym dołączanie klientów do zarządzania zasobami delegowanymi platformy Azure. Aby uzyskać więcej informacji, zobacz nasze [przykładowe repozytorium](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) i [wbudowane klienta do zarządzania zasobami delegowanymi platformy Azure.](how-to/onboard-customer.md)
- **Oferty usług zarządzanych w portalu Azure Marketplace:** Zaoferuj swoje usługi klientom za pośrednictwem ofert prywatnych lub publicznych i automatycznie dołączaj je do zarządzania zasobami delegowanymi platformy Azure, jako alternatywę dla dołączania przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Oferty usług zarządzanych w portalu Azure Marketplace](concepts/managed-services-offers.md).
- **Aplikacje zarządzane na platformie Azure:** aplikacje pakietowe i wysyłkowe, które są łatwe do wdrożenia i użycia w ich własnych subskrypcjach. Aplikacja jest wdrażana w grupie zasobów, do której uzyskujesz dostęp od dzierżawy, co pozwala zarządzać usługą w ramach ogólnego środowiska latarni morskiej platformy Azure. Aby uzyskać więcej informacji, zobacz [omówienie zarządzanych aplikacji platformy Azure](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](concepts/azure-delegated-resource-management.md).
- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](concepts/cross-tenant-management-experience.md).
