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
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197018"
---
# <a name="azure-application-offer"></a>Oferty aplikacji platformy Azure

W tej sekcji opisano sposób publikowania nowej oferty aplikacji platformy Azure, Microsoft <a href="https://azuremarketplace.microsoft.com">portalu Azure Marketplace</a>.
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Każda aplikacja platformy Azure zawiera szablon usługi Azure Resource Manager, który definiuje zasoby techniczne używane przez aplikację, która zwykle zawiera co najmniej jednej maszyny wirtualnej i inne pomocnicze usługi bazujących na platformie Azure lub do sieci Web. | ![Ikona aplikacji platformy Azure](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>Korzyści

Oto niektóre korzyści zapewnianych przez wyświetlanie listy aplikacji w portalu Microsoft marketplace:

* Osiągnięcie 100 milionów użytkowników w usłudze Azure Active Directory, usługi Office 365 i Dynamics 365.

* Rozszerzanie swojego zespołu: dotrzeć do użytkowników biznesowych na całym świecie i Uzyskaj wgląd w kanał sprzedaży, który angażuje użytkowników końcowych, ułatwia pozyskiwanie potencjalnych klientów i umożliwia inicjowanie rozmów z nowych klientów różnych branż.

* Wprowadzenie wskazujące: Firma Microsoft będzie udostępniać szczegółowych informacji o jak aplikacja działa w usłudze AppSource, co działa dobrze i dalsze ulepszenie własnych procedur sprzedaży.

## <a name="types-of-azure-applications"></a>Typy aplikacji platformy Azure

Istnieją dwa rodzaje aplikacji platformy Azure: zarządzanych aplikacji i szablon rozwiązania. Mimo że jest to podobne, istnieją pewne istotne różnice.

### <a name="solution-template"></a>Szablon rozwiązania

Szablony rozwiązań są jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Ten typ oferty jest używany, gdy rozwiązanie wymaga dodatkowej i automatyzację konfiguracji poza pojedynczej maszyny wirtualnej (VM). Można zautomatyzować, podając więcej niż jednej maszyny wirtualnej przy użyciu szablonu rozwiązania. W tym aprowizacji zasobów sieci i magazynu, aby zapewnić złożonych rozwiązań IaaS. Omówienie wymagań szablonu rozwiązania i model rozliczeń, zobacz [aplikacji platformy Azure: szablony rozwiązań](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

### <a name="managed-application"></a>Aplikacja zarządzana

Aplikacja zarządzana jest podobna do szablonu rozwiązania w witrynie Marketplace. Najważniejsza różnica między nimi polega na tym, że w aplikacji zarządzanej zasoby są wdrażane w grupie zasobów zarządzanej przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. Wydawca określa koszt bieżącej obsługi rozwiązania. Aplikacje zarządzane platformy Azure umożliwia łatwe tworzenie i dostarczanie aplikacji w pełni zarządzaną, gotową do użycia dla klientów.

Oprócz witryny Marketplace możesz zaoferować aplikacje zarządzane w katalogu usług. Wykaz usług to wewnętrzny katalog zatwierdzonych rozwiązań, przeznaczonych dla użytkowników w organizacji. Wykaz umożliwia spełniają standardy organizacji, jednocześnie oferując rozwiązania dla grup w organizacji. Wykaz pozwala pracownikom łatwo znajdować aplikacje zalecane i zatwierdzone przez działy IT.

Aby uzyskać więcej informacji o zaletach i typów zarządzanych aplikacji, zobacz [usługi Azure managed applications overview](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="publishing-overview"></a>Omówienie publikowania

Poniższy klip wideo [tworzenia szablonów rozwiązań, a Managed Applications dla witryny Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), znajduje się przegląd informacji na temat sposobu tworzenia szablonu usługi Azure Resource Manager do definiowania rozwiązania aplikacji platformy Azure i następnie sposobu następnie należy opublikować ofertę aplikacji w portalu Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>Publikowanie procesów przepływu pracy

Na poniższym diagramie przedstawiono ogólny proces programu stawiane ofertom aplikacji platformy Azure.

![Przepływ pracy dla publikacji oferty](./media/new-offer-process.png)

## <a name="offer-components"></a>Składniki oferty

W tej sekcji przedstawiono elementy publikowanie oferty aplikacji zarządzanej i służy jako przewodnik dla wydawcy w portalu Azure Marketplace. Publikowanie w podzielona na następujące główne elementy: 

* [Wymagania wstępne](./cpp-prerequisites.md) -wymieniono wymagania techniczne i biznesowe przed utworzeniem lub publikowanie oferty aplikacji zarządzanej. 
* [Utwórz ofertę](./cpp-create-offer.md) — zawiera kroki wymagane do utworzenia wpisu oferty aplikacji zarządzanej, za pomocą portalu Cloud Partner. 
* [Publikowanie oferty](./cpp-publish-offer.md)— w tym artykule opisano sposób w celu przesłania oferty do opublikowania w portalu Azure Marketplace.

## <a name="steps-in-the-publishing-process"></a>Kroki w procesie publikowania

Dostępne są następujące ogólne kroki programu stawiane ofertom aplikacji platformy Azure:

1. Utwórz ofertę — zawiera szczegółowe informacje o ofercie. Informacje te obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

2. Tworzenie biznesowych i zasoby techniczne — Tworzenie trwałych biznesowe (dokumenty prawne i materiały marketingowe) i zasoby techniczne dla skojarzonego rozwiązania.

3. Tworzenie jednostki SKU - tworzenie dla jednostek SKU, skojarzone z ofertą. Unikatowe jednostki SKU jest wymagana dla każdego obrazu, w którym planujesz opublikować.

4. Certyfikowanie i opublikować ofertę — po ukończeniu oferta i zasoby techniczne, możesz przesłać ofertę. Ten przesłany rozpoczyna się proces publikowania. W trakcie tego procesu rozwiązanie jest testowany, zweryfikowany, certyfikowane, następnie "miejsce na żywo" w witrynie Azure Marketplace.

## <a name="next-steps"></a>Kolejne kroki

Przed uznaniem następujące kroki, musisz spełnić [wymagania techniczne i biznesowe](./cpp-prerequisites.md) publikowania aplikacji zarządzanej do portalu Microsoft Azure Marketplace.