---
title: Oferta aplikacji platformy Azure | Portal Azure Marketplace
description: Przegląd procesu publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pabutler
ms.openlocfilehash: 9125b5c71b63b27c58ea72b7bfd49f730854b33d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818802"
---
# <a name="azure-application-offer"></a>Oferta aplikacji platformy Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> W tej sekcji opisano sposób publikowania nowej oferty aplikacji platformy Azure w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com).  Każda aplikacja platformy Azure zawiera szablon Azure Resource Manager, który definiuje wszystkie zasoby techniczne używane przez aplikację, która zwykle zawiera co najmniej jedną maszynę wirtualną i inne pomocnicze usługi platformy Azure lub sieci Web. Wszystkie oferty aplikacji platformy Azure muszą włączać zabezpieczenia dostępu za pomocą [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ikona aplikacji platformy Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Przegląd publikowania

Następujące wideo, [Budowanie szablonów rozwiązań i zarządzane aplikacje dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)to wprowadzenie: jakie typy ofert są dostępne, jakie zasoby techniczne są wymagane, sposób tworzenia szablonu Azure Resource Manager, opracowywanie i testowania interfejsu użytkownika aplikacji, sposobu publikowania oferty aplikacji oraz procesu przeglądu aplikacji.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typy aplikacji platformy Azure

Istnieją dwa rodzaje aplikacji platformy Azure: zarządzane aplikacje i szablony rozwiązań. 

- Szablony rozwiązań to jeden z głównych sposobów publikowania rozwiązania w portalu Marketplace. Ten typ oferty jest używany, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną. Możesz zautomatyzować dostarczanie więcej niż jednej maszyny wirtualnej przy użyciu szablonu rozwiązania. Ta Automatyzacja obejmuje obsługę zasobów sieciowych i magazynowych w celu zapewnienia złożonych rozwiązań IaaS. Aby zapoznać się z omówieniem wymagań szablonów rozwiązań i modelu rozliczeń, zobacz [aplikacje platformy Azure: szablony rozwiązań](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Aplikacje zarządzane są podobne do szablonów rozwiązań z jedną różnicą kluczową. Najważniejsza różnica między nimi polega na tym, że w aplikacji zarządzanej zasoby są wdrażane w grupie zasobów zarządzanej przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. Wydawca określa koszt bieżącej obsługi rozwiązania. Korzystaj z aplikacji zarządzanych przez platformę Azure, aby łatwo tworzyć i dostarczać klientom w pełni zarządzane aplikacje gotowe.

Oprócz portalu Azure Marketplace można również oferować zarządzane aplikacje w katalogu usług. Wykaz usług to wewnętrzny katalog zatwierdzonych rozwiązań, przeznaczonych dla użytkowników w organizacji. Katalog jest używany do zaspokajania standardów organizacyjnych, a jednocześnie oferuje rozwiązania dla grup w organizacji. Wykaz pozwala pracownikom łatwo znajdować aplikacje zalecane i zatwierdzone przez działy IT.

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](../../cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP.

Aby uzyskać więcej informacji o zaletach i typach zarządzanych aplikacji, zobacz [Omówienie usługi Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Przepływ pracy procesu publikowania

Na poniższym diagramie przedstawiono proces wysokiego poziomu służący do publikowania oferty aplikacji platformy Azure.

![Przepływ pracy dla oferty publikowania](./media/new-offer-process.png)

Poniżej przedstawiono ogólne kroki publikowania oferty aplikacji platformy Azure:

1. Spełnienie wymagań [wstępnych](./cpp-prerequisites.md) — (nie pokazano) Sprawdź, czy zostały spełnione wymagania biznesowe i techniczne dotyczące publikowania aplikacji platformy Azure w portalu Azure Marketplace. 

1. [Utwórz ofertę](./cpp-create-offer.md) — podaj szczegółowe informacje o ofercie. Te informacje obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

1. [Twórz lub Zbieraj istniejące zasoby biznesowe i techniczne](./cpp-create-technical-assets.md) — Twórz zasoby biznesowe (dokumenty prawne i materiały marketingowe) oraz zasoby techniczne dla powiązanego rozwiązania.

1. [Utwórz jednostkę SKU](./cpp-skus-tab.md) — Utwórz jednostki SKU skojarzone z ofertą. Dla każdego obrazu, który ma zostać opublikowany, wymagana jest unikatowa jednostka SKU.

1. Certyfikowanie i [Publikowanie oferty](./cpp-publish-offer.md) — po zakończeniu oferty i rozpoczęciu zasobów technicznych można przesłać ofertę. To przesyłanie rozpocznie proces publikowania. W trakcie tego procesu rozwiązanie jest testowane, weryfikowane, certyfikowane, a następnie "trafia na żywo" w portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

Przed zainstalowaniem tych kroków należy spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) dotyczące publikowania aplikacji zarządzanej w Microsoft Azure Marketplace.
