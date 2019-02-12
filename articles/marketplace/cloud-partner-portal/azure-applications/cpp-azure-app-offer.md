---
title: Oferta platformy Azure aplikacja | Dokumentacja firmy Microsoft
description: Omówienie procesu publikowania aplikacji na platformie Azure oferty w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pbutlerm
ms.openlocfilehash: 82d072cc6f86ae758bd0fdd4d02b68b1ac1de53a
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097154"
---
# <a name="azure-application-offer"></a>Oferty aplikacji platformy Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> W tej sekcji wyjaśniono, jak opublikować nową ofertę usługi Azure application [portalu Azure Marketplace](https://azuremarketplace.microsoft.com).  Każda aplikacja platformy Azure zawiera szablon usługi Azure Resource Manager, który definiuje zasoby techniczne używane przez aplikację, która zwykle zawiera co najmniej jednej maszyny wirtualnej i inne pomocnicze usługi bazujących na platformie Azure lub do sieci Web. Wszystkie oferty aplikacji platformy Azure, należy włączyć zabezpieczenia dostępu za pośrednictwem [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ikona aplikacji platformy Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Omówienie publikowania

Poniższy klip wideo [tworzenia szablonów rozwiązań, a Managed Applications dla witryny Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), stanowi wprowadzenie: co oferują dostępnych typów, jakie zasoby techniczne są wymagane, jak tworzyć usługi Azure Resource Manager Szablon tworzenia i testowania aplikacji interfejsu użytkownika, jak opublikować ofertę aplikacji, a procesu sprawdzenia aplikacji.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typy aplikacji platformy Azure

Istnieją dwa rodzaje aplikacji platformy Azure: zarządzane aplikacje i szablony rozwiązań. 

- Szablony rozwiązań są jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Ten typ oferty jest używany, gdy rozwiązanie wymaga dodatkowej i automatyzację konfiguracji poza pojedynczej maszyny wirtualnej (VM). Można zautomatyzować, podając więcej niż jednej maszyny wirtualnej przy użyciu szablonu rozwiązania. Ta Automatyzacja obejmuje Inicjowanie obsługi administracyjnej zasobów sieci i magazynu, aby zapewnić złożonych rozwiązań IaaS. Omówienie wymagań szablonu rozwiązania i model rozliczeń, zobacz [aplikacji platformy Azure: szablony rozwiązań](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Zarządzane aplikacje są podobne do szablonów rozwiązań, z jedną kluczową różnicą. Najważniejsza różnica między nimi polega na tym, że w aplikacji zarządzanej zasoby są wdrażane w grupie zasobów zarządzanej przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. Wydawca określa koszt bieżącej obsługi rozwiązania. Aplikacje zarządzane platformy Azure umożliwia łatwe tworzenie i dostarczanie aplikacji w pełni zarządzaną, gotową do użycia dla klientów.

Oprócz witryny Azure Marketplace możesz oferować aplikacje zarządzane w katalogu usług. Wykaz usług to wewnętrzny katalog zatwierdzonych rozwiązań, przeznaczonych dla użytkowników w organizacji. Wykaz umożliwia spełniają standardy organizacji, jednocześnie oferując rozwiązania dla grup w organizacji. Wykaz pozwala pracownikom łatwo znajdować aplikacje zalecane i zatwierdzone przez działy IT.

Aby uzyskać więcej informacji o zaletach i typów zarządzanych aplikacji, zobacz [usługi Azure managed applications overview](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Publikowanie procesów przepływu pracy

Na poniższym diagramie przedstawiono ogólny proces programu stawiane ofertom aplikacji platformy Azure.

![Przepływ pracy dla publikacji oferty](./media/new-offer-process.png)

Dostępne są następujące ogólne kroki programu stawiane ofertom aplikacji platformy Azure:

0. Spełnia [wymagania wstępne](./cpp-prerequisites.md) — (nie pokazano) sprawdź, czy są spełnione wymagania biznesowe i techniczne dotyczące publikowania aplikacji platformy Azure w portalu Azure Marketplace. 

1. [Utwórz ofertę](./cpp-create-offer.md) — zawiera szczegółowe informacje o ofercie. Informacje te obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

2. [Tworzenie lub zebrać istniejących biznesowe i techniczne zasoby](./cpp-create-technical-assets.md) — Tworzenie trwałych biznesowe (dokumenty prawne i materiały marketingowe) i zasoby techniczne dla skojarzonego rozwiązania.

3. [Tworzenie jednostki SKU](./cpp-skus-tab.md) -tworzenie dla jednostek SKU, skojarzone z ofertą. Unikatowe jednostki SKU jest wymagana dla każdego obrazu, w którym planujesz opublikować.

4. Certyfikowanie i [opublikować ofertę](./cpp-publish-offer.md) — po ukończeniu oferta i zasoby techniczne, możesz przesłać ofertę. Ten przesłany rozpoczyna się proces publikowania. W trakcie tego procesu rozwiązanie jest testowany, zweryfikowany, certyfikowane, następnie "miejsce na żywo" w witrynie Azure Marketplace.


## <a name="next-steps"></a>Kolejne kroki

Przed uznaniem następujące kroki, musisz spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) publikowania aplikacji zarządzanej do portalu Microsoft Azure Marketplace.
