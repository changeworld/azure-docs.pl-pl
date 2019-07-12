---
title: Co to jest Azure morskiej?
description: Azure morskiej umożliwia dostawcy usług dostarczania usług zarządzanych dla ich klientom pracę z wyższym automatyzacji i wydajności na dużą skalę.
author: JnHs
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: eb55af5a1121eb193bb76efc9f9e0b833f4b5a1f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809820"
---
# <a name="what-is-azure-lighthouse"></a>Co to jest Azure morskiej?

Morskiej platformy Azure oferuje usługodawców płaszczyznę jednego formantu do przeglądania i zarządzania platformy Azure w swoim klientom wyższy automatyzacji, skalowanie i rozszerzone nadzoru. Za pomocą morskiej Azure dostawcy usług mogą dostarczać usługi zarządzane przy użyciu narzędzi zarządzania kompleksowe i niezawodne wbudowane w platformę Azure. Ta oferta również mogą korzystać przedsiębiorstwa organizacjom zarządzanie zasobami w wielu dzierżawach.

![Diagram przeglądowy morskiej platformy Azure](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Korzyści

Morskiej Azure pomaga wydajnej i efektywne tworzenie i dostarczanie usług zarządzanych dla swoich klientów. Korzyści:

- **Zarządzanie na dużą skalę**: Zaangażowania i cyklu życia operacji klienta do zarządzania zasobami klienta są łatwiejsze i bardziej skalowalne.
- **Zapewnia lepszy wgląd i dokładność dla klientów**: Klienci, do którego zasoby zarządzasz, mają lepszy wgląd w swoje działania i ścisłą kontrolę nad zakresem delegują one do zarządzania, gdy adres IP jest zachowywana.
- **Narzędzia platformy kompleksowe i ujednolicone**: Nasze środowisko narzędzi jest przeznaczona dla scenariuszy dostawcy usługi klucza, w tym wiele modeli licencjonowania, takich jak EA, dostawcy usług Kryptograficznych i płatność za rzeczywiste użycie. Nowe możliwości pracy z istniejącymi narzędziami i interfejsy API, modele, licencjonowania i partnerów takich jak programy [programu Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Opcje morskiej platformy Azure, możesz wybrać można zintegrować z istniejącymi przepływami pracy i aplikacjami oraz oddziaływania można śledzić na podczas pracy z klientami przez [łączenie się z Partnerem](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

Brak bez dodatkowych kosztów związanych z użyciem morskiej platformy Azure do zarządzania zasobami platformy Azure Twoich klientów.

## <a name="capabilities"></a>Możliwości

Morskiej platformy Azure obejmuje wiele sposobów, aby usprawnić zaangażowania użytkowników i zarządzania:

- **Azure delegowane zarządzanie zasobami**: Zarządzanie zasobami platformy Azure Twoich klientów bezpiecznie z w obrębie własnej dzierżawy, bez konieczności przełączania kontekstu i kontroli. Aby uzyskać więcej informacji, zobacz [Azure delegowane zarządzanie zasobami](./concepts/azure-delegated-resource-management.md).
- **Nowa witryna Azure portal napotyka**: Wyświetl informacje o wielu dzierżawach w nowym **moich klientów** strony w [witryny Azure portal](https://portal.azure.com). Odpowiedni **dostawców usług** bloku pozwala klientom wyświetlanie i zarządzanie dostępem do dostawcy usługi. Aby uzyskać więcej informacji, zobacz [widoku i zarządzania nimi klienci](./how-to/view-manage-customers.md) i [wyświetlanie i zarządzanie dostawcami usług](./how-to/view-manage-service-providers.md).
- **Szablony usługi Azure Resource Manager**: Łatwiej wykonywać zadania zarządzania, w tym dodawanie klientów na platformie Azure delegowane zarządzanie zasobami. Aby uzyskać więcej informacji, zobacz nasze [przykłady repozytorium](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) i [dołączanie klientów na platformie Azure delegowane zarządzanie zasobami](how-to/onboard-customer.md).
- **Zarządzane oferty usług w witrynie Azure Marketplace**: Oferta usługi dla klientów za pośrednictwem oferty prywatnej lub publicznej i dodano je automatycznie do środowiska zarządzania usługi Azure resource delegowanego, jako alternatywnego do dołączania za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [zarządzane oferty usług w witrynie Azure Marketplace](./concepts/managed-services-offers.md).
- **Usługi Azure managed applications**: Pakiet i dostarczaj aplikacje, które są łatwe dla swoich klientów, wdrażania i stosowania w ich własnych subskrypcjach. Aplikacja jest wdrażana w grupie zasobów, możesz uzyskać dostęp w dzierżawie, co pozwala zarządzać usługą jako część ogólnego środowiska Azure morskiej. Aby uzyskać więcej informacji, zobacz [usługi Azure managed applications overview](https://docs.microsoft.com/azure/managed-applications/overview).

> [!NOTE]
> Możliwości opisanych powyżej są obecnie dostępne w chmurach publicznych. Regionalne Dostępność poszczególnych usług, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/).