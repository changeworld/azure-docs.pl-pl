---
title: Co to jest Azure Lighthouse?
description: Usługa Azure Lighthouse umożliwia dostawcom usług dostarczanie usług zarządzanych klientom o większej wydajności i skalowalności.
author: JnHs
ms.author: jenhayes
ms.date: 11/11/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: ae8194e6d5764769019ac803a52cd54497a618c1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929582"
---
# <a name="what-is-azure-lighthouse"></a>Co to jest Azure Lighthouse?

Usługa Azure Lighthouse oferuje dostawcom usług jedną płaszczyznę kontroli do wyświetlania i zarządzania platformą Azure wśród wszystkich klientów, którzy mają wyższą automatyzację, skalę i ulepszone zarządzanie. Dzięki usłudze Azure Lighthouse dostawcy usług mogą dostarczać zarządzane usługi przy użyciu kompleksowych i niezawodnych narzędzi do zarządzania wbudowanych w platformę Azure. Ta oferta może również przynieść organizacjom IT przedsiębiorstwa zarządzanie zasobami w wielu dzierżawach.

![Diagram omówienia usługi Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Korzyści

Usługa Azure Lighthouse pozwala na opłacalną i wydajną kompilację i dostarczanie usług zarządzanych klientom. Korzyści obejmują:

- **Zarządzanie na dużą skalę**: zaangażowanie klientów i operacje związane z cyklem życia w celu zarządzania zasobami klientów są łatwiejsze i bardziej skalowalne.
- **Większa widoczność i precyzja dla klientów**: Klienci, których zasoby są zarządzane, będą mieli lepszy wgląd w swoje działania i precyzyjną kontrolę nad zakresem delegowanym do zarządzania, podczas gdy adres IP jest zachowywany.
- **Kompleksowe i ujednolicone narzędzia platformy**: nasze środowisko ułatwiające korzystanie z kluczowych scenariuszy dostawców usług, w tym wielu modeli licencjonowania, takich jak EA, CSP i płatność zgodnie z rzeczywistym użyciem. Nowe możliwości działają z istniejącymi narzędziami i interfejsami API, modelami licencjonowania i programami partnerskimi, takimi jak [program Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Wybrane opcje usługi Azure Lighthouse można zintegrować z istniejącymi przepływami pracy i aplikacjami. możesz śledzić wpływ na zaangażowanie klientów, [ŁĄCZĄC identyfikator partnera](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

Nie ma dodatkowych kosztów związanych z usługą Azure Lighthouse do zarządzania zasobami platformy Azure dla klientów.

## <a name="capabilities"></a>Możliwości

Usługa Azure Lighthouse obejmuje wiele sposobów usprawniania zaangażowania klientów i zarządzania nimi:

- **Zarządzanie zasobami delegowanymi przez platformę Azure**: bezpieczne zarządzanie zasobami platformy Azure klientów w ramach własnej dzierżawy bez konieczności przełączania kontekstu i płaszczyzny kontroli. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami delegowanymi przez platformę Azure](./concepts/azure-delegated-resource-management.md).
- **Nowe Azure Portal środowiska**: Wyświetl informacje między dzierżawcami na stronie nowe **moi klienci** w [Azure Portal](https://portal.azure.com). Odpowiedni blok **dostawców usług** umożliwia klientom wyświetlanie dostępu dostawcy usług i zarządzanie nim. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie klientami](./how-to/view-manage-customers.md) oraz [Wyświetlanie i zarządzanie dostawcami usług](./how-to/view-manage-service-providers.md).
- **Szablony Azure Resource Manager**: łatwiejsze wykonywanie zadań zarządzania, w tym klientów dołączania do zarządzania zasobami delegowanymi przez platformę Azure. Aby uzyskać więcej informacji, zobacz nasze [przykładowe repozytorium](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) i Dołącz [klienta do zarządzania zasobami delegowanymi przez platformę Azure](how-to/onboard-customer.md).
- **Oferty usług zarządzanych w witrynie Azure Marketplace**: umożliwiają oferowanie usług klientom za pośrednictwem ofert prywatnych i publicznych oraz automatyczne dołączanie do funkcji zarządzania zasobami delegowanymi przez platformę Azure jako alternatywę do dołączania przy użyciu szablonów Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [oferty usług zarządzanych w witrynie Azure Marketplace](./concepts/managed-services-offers.md).
- **Aplikacje zarządzane przez platformę Azure**: aplikacje pakietu i dostarczania dla klientów, które są łatwe do wdrożenia i użycia w swoich własnych subskrypcjach. Aplikacja jest wdrażana w grupie zasobów, do której masz dostęp z dzierżawy, co pozwala na zarządzanie usługą w ramach ogólnego środowiska Azure Lighthouse. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](concepts/azure-delegated-resource-management.md).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](concepts/cross-tenant-management-experience.md).
