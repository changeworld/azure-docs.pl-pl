---
title: Oferta aplikacji platformy Azure | Azure Marketplace
description: Omówienie procesu publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285320"
---
# <a name="azure-application-offer"></a>Oferta aplikacji platformy Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> W tej sekcji wyjaśniono, jak opublikować nową ofertę aplikacji platformy Azure w [portalu Azure Marketplace.](https://azuremarketplace.microsoft.com)  Każda aplikacja platformy Azure zawiera szablon usługi Azure Resource Manager, który definiuje wszystkie zasoby techniczne używane przez aplikację, która zazwyczaj obejmuje jedną lub więcej maszyn wirtualnych i innych usług obsługujących platformę Azure lub usługi oparte na sieci Web. Wszystkie oferty aplikacji platformy Azure muszą włączać zabezpieczenia dostępu za pośrednictwem [usługi Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/)  </div> | ![Ikona aplikacji platformy Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Omówienie publikowania

W poniższym klipie wideo, [Szablony rozwiązań budowlanych i aplikacje zarządzane dla portalu Azure Marketplace,](https://channel9.msdn.com/Events/Build/2018/BRK3603)jest wprowadzenie: jakie typy ofert są dostępne, jakie zasoby techniczne są wymagane, jak autor szablonu usługi Azure Resource Manager, tworzenie i testowanie interfejsu użytkownika aplikacji, jak opublikować ofertę aplikacji i proces przeglądu aplikacji.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typy aplikacji platformy Azure

Istnieją dwa rodzaje aplikacji platformy Azure: aplikacje zarządzane i szablony rozwiązań. 

- Szablony rozwiązań są jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Ten typ oferty jest używany, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną (maszynę wirtualną). Można zautomatyzować dostarczanie więcej niż jednej maszyny Wirtualnej przy użyciu szablonu rozwiązania. Ta automatyzacja obejmuje inicjowanie obsługi administracyjnej zasobów sieciowych i magazynowych w celu zapewnienia złożonych rozwiązań IaaS. Aby zapoznać się z omówieniem wymagań dotyczących szablonów rozwiązań i modelu rozliczeń, zobacz [Szablony rozwiązań usługi Azure Applications:](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Aplikacje zarządzane są podobne do szablonów rozwiązań, z jedną kluczową różnicą. Najważniejsza różnica między nimi polega na tym, że w aplikacji zarządzanej zasoby są wdrażane w grupie zasobów zarządzanej przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. Wydawca określa koszt bieżącej obsługi rozwiązania. Użyj aplikacji zarządzanych platformy Azure, aby łatwo tworzyć i dostarczać klientom w pełni zarządzane, gotowe aplikacje.

Oprócz portalu Azure Marketplace można również oferować aplikacje zarządzane w katalogu usług. Wykaz usług to wewnętrzny katalog zatwierdzonych rozwiązań, przeznaczonych dla użytkowników w organizacji. Katalog służy do spełniania standardów organizacyjnych, jednocześnie oferując rozwiązania dla grup w organizacji. Wykaz pozwala pracownikom łatwo znajdować aplikacje zalecane i zatwierdzone przez działy IT.

>[!Note]
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](../../cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft.

Aby uzyskać więcej informacji na temat zalet i typów aplikacji zarządzanych, zobacz [omówienie aplikacji zarządzanych platformy Azure](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Przepływ pracy procesu publikowania

Na poniższym diagramie przedstawiono proces wysokiego poziomu publikowania oferty aplikacji platformy Azure.

![Przepływ pracy dla oferty publikowania](./media/new-offer-process.png)

Kroki wysokiego poziomu publikowania oferty aplikacji platformy Azure to:

1. Spełnij [wymagania wstępne](./cpp-prerequisites.md) — (nie pokazano) Sprawdź, czy spełniasz wymagania biznesowe i techniczne dotyczące publikowania aplikacji platformy Azure w portalu Azure Marketplace. 

1. [Utwórz ofertę](./cpp-create-offer.md) — podaj szczegółowe informacje o ofercie. Informacje te obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

1. [Tworzenie lub zbieranie istniejących zasobów biznesowych i technicznych](./cpp-create-technical-assets.md) — tworzenie zasobów biznesowych (dokumentów prawnych i materiałów marketingowych) oraz zasobów technicznych dla skojarzonego rozwiązania.

1. [Utwórz jednostkę SKU](./cpp-skus-tab.md) — utwórz jednostki SKU skojarzone z ofertą. Unikatowa jednostka SKU jest wymagana dla każdego obrazu, który planujesz opublikować.

1. Poświadczaj i [publikuj ofertę](./cpp-publish-offer.md) - Po zakończeniu oferty i aktywów technicznych można złożyć ofertę. To przesłanie rozpoczyna proces publikowania. Podczas tego procesu rozwiązanie jest testowane, sprawdzane, certyfikowane, a następnie "przechodzi na rynek" w portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

Przed rozważeniem tych kroków należy spełnić [wymagania techniczne i biznesowe dotyczące](./cpp-prerequisites.md) publikowania aplikacji zarządzanej w witrynie Microsoft Azure Marketplace.
