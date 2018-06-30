---
title: Przewodnik dotyczący tworzenia szablonu rozwiązania dla witryny Marketplace | Dokumentacja firmy Microsoft
description: Szczegółowe instrukcje dotyczące sposobu tworzenia, certyfikować i wdrażania wielu maszyn wirtualnych szablon rozwiązania obrazu dla kupić w witrynie Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: mbaldwin
ms.openlocfilehash: 147b0b9b4a3fe789544457d17fed3d29badbe12c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113956"
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Przewodnik, aby utworzyć szablon rozwiązania dla portalu Azure Marketplace
Po ukończeniu kroku 1, [o tworzeniu konta i rejestracji][link-acct-creation], możemy przewodnikiem po utworzeniu szablonu rozwiązania Azure zgodnego w [techniczne wymagania wstępne dotyczące tworzenia Szablon rozwiązania](marketplace-publishing-solution-template-creation-prerequisites.md). Obecnie firma Microsoft przeprowadzi Cię przez kolejne etapy tworzenia szablonu rozwiązanie dla wielu maszyn wirtualnych na [Portal publikowania] [ link-pubportal] dla portalu Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Utwórz ofertę szablon rozwiązania w portalu publikowania
Przejdź do [ https://publish.windowsazure.com ](http://publish.windowsazure.com). Po zalogowaniu po raz pierwszy do [Portal publikowania](https://publish.windowsazure.com/), użyj tego samego konta z został zarejestrowany w firmie sprzedawcy profilu. Później możesz dodać każdy pracownik firmy jako współadministrator w portalu publikowania.

### <a name="1-select-solution-templates"></a>1. Wybierz opcję "Szablony rozwiązań"
  ![Rysowanie][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Utwórz nowy szablon rozwiązania
  ![Rysowanie][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Rozpoczynać się od topologii
Szablon rozwiązania jest nadrzędny dla wszystkich jego topologii. Można zdefiniować wiele topologii w jednym szablonie oferty/rozwiązania. Gdy oferta zostanie przeniesiona do etapu przemieszczania, razem z nią zostaną przeniesione wszystkie jej topologie. Wykonaj poniższe kroki, aby zdefiniować ofertę:     

* Utworzyć topologię: "Identyfikator topologii" jest zazwyczaj nazwą topologii szablon rozwiązania. Identyfikator topologii jest używana w adresie URL, jak pokazano poniżej:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portalu Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Dodaj nową wersję.

### <a name="4-get-your-topology-versions-certified"></a>4. Pobieranie Twojej wersji topologii certyfikowane
Przekaż plik zip, który zawiera wszystkie wymagane pliki do udostępnienia tej konkretnej wersji topologii. Ten plik zip musi zawierać następujące informacje:

* *mainTemplate.json* i *createUiDefinition.json* pliku w katalogu głównym.
* Szablony połączone i wszystkie wymagane skrypty.

  > [!TIP]
  > Podczas pracy deweloperów na temat tworzenia rozwiązania topologie szablonu i pobieranie ich certyfikowane, business, marketingu i/lub prawnych działy firmy mogą pracować na zawartość marketing i prawnych.
  >
  >

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy zostanie utworzony szablon rozwiązania i przekazać plik zip, postępuj zgodnie z instrukcjami wyświetlanymi w [Marketplace Przewodnik po zawartości marketingowej](marketplace-publishing-push-to-staging.md) przed wypchnięciem ofertę przemieszczania. Aby wyświetlić pełny zestaw marketplace publikowanie artykułów, odwiedź stronę [wprowadzenie: jak publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md).

Być może zainteresuje te pokrewne artykuły:

* Obrazów maszyn wirtualnych: [o obrazy maszyny wirtualnej na platformie Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Rozszerzenia maszyny Wirtualnej: [omówienie rozszerzeń maszyny Wirtualnej i Agent maszyny Wirtualnej](https://msdn.microsoft.com/library/azure/dn832621.aspx) i [rozszerzeń maszyny Wirtualnej platformy Azure i funkcje](https://docs.microsoft.com/azure/virtual-machines/extensions/features-windows)
* Usługa Azure Resource Manager: [tworzenia szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) i [przykłady prostego szablonu](https://github.com/rjmax/ArmExamples)
* Ogranicza konta magazynu: [jak monitorować dla ograniczania konta magazynu](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) i [magazyn w warstwie Premium](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
