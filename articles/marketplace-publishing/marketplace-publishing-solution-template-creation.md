---
title: Przewodnik opisujący Tworzenie szablonu rozwiązania w portalu Marketplace | Dokumentacja firmy Microsoft
description: Szczegółowe instrukcje dotyczące sposobu tworzenia, certyfikować i wdrażania wielu maszyn wirtualnych szablon rozwiązań dla obrazu dla zakupu w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: 914dece4ba064af00c5b2c34d43dedbb1d62e2e2
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073745"
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Przewodnik, aby utworzyć szablon rozwiązań dla witryny Azure Marketplace
Po wykonaniu kroku 1, [o tworzeniu konta i rejestracji][link-acct-creation], firma Microsoft przewodnikiem po utworzeniu szablonu rozwiązań zgodnych z platformą Azure w [techniczne wymagania wstępne dotyczące tworzenia Szablon rozwiązania](marketplace-publishing-solution-template-creation-prerequisites.md). Teraz przeprowadzimy Cię przez kroki tworzenia szablonu rozwiązania dla wielu maszyn wirtualnych na [portalu wydawców] [ link-pubportal] dla witryny Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Utwórz ofertę szablonu rozwiązania w portalu wydawców
Przejdź do [ https://publish.windowsazure.com ](http://publish.windowsazure.com). Podczas logowania się po raz pierwszy do [portalu wydawców](https://publish.windowsazure.com/), użyj tego samego konta, z którego zarejestrowano profil sprzedawcy firmy. Później można dodać każdy pracownik firmy jako coadmin w portalu wydawców.

### <a name="1-select-solution-templates"></a>1. Wybierz opcję "Szablony rozwiązań"
  ![Rysowanie][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Utwórz nowy szablon rozwiązania
  ![Rysowanie][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Uruchom za pomocą topologii
Szablon rozwiązania jest nadrzędny dla wszystkich jego topologii. Można zdefiniować wiele topologii w jednym szablonie oferty/rozwiązania. Gdy oferta zostanie przeniesiona do etapu przemieszczania, razem z nią zostaną przeniesione wszystkie jej topologie. Wykonaj poniższe kroki, aby zdefiniować oferty:     

* Utworzyć topologię: "Identyfikator topologii" zazwyczaj jest nazwa topologii dla szablonu rozwiązania. Identyfikator topologii jest używany w adresie URL, jak pokazano poniżej:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Witryna Azure portal: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Dodaj nową wersję.

### <a name="4-get-your-topology-versions-certified"></a>4. Pobierz swoje wersje topologii certyfikat
Przekaż plik zip, który zawiera wszystkie wymagane pliki do aprowizacji tej konkretnej wersji tej topologii. Ten plik zip musi zawierać następujące pliki:

* *mainTemplate.json* i *createUiDefinition.json* pliku w katalogu głównym.
* Połączone szablony i wszystkie wymagane skrypty.

  > [!TIP]
  > Podczas pracy deweloperów, na temat tworzenia rozwiązania topologii szablonu i ich certyfikowane, typu firm, działów marketingu i/lub prawnych w firmie może działać względem zawartości marketingowej i prawne.
  >
  >

## <a name="next-steps"></a>Kolejne kroki
Teraz, utworzony szablon rozwiązania i przekazany plik zip, postępuj zgodnie z instrukcjami [Przewodnik po zawartości marketingowej witryny Marketplace](marketplace-publishing-push-to-staging.md) przed wypchnięciem oferty do wdrażania przejściowego. Aby wyświetlić pełny zestaw publikowanie artykułów w witrynie marketplace, odwiedź stronę [wprowadzenie: Jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md).

Użytkownik może również zainteresować te pokrewne artykuły:

* Obrazy maszyn wirtualnych: [Informacje o obrazach maszyn wirtualnych na platformie Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Rozszerzenia maszyn wirtualnych: [Rozszerzenia maszyny Wirtualnej platformy Azure i funkcji](../virtual-machines/extensions/features-windows.md)
* Usługa Azure Resource Manager: [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) i [przykłady prostego szablonu](https://github.com/rjmax/ArmExamples)
* Ogranicza konto magazynu: [Jak monitorować ograniczania konta magazynu](https://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) i [magazynu w warstwie Premium](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
