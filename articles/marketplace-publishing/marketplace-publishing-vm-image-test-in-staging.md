---
title: Testowanie oferty maszyny Wirtualnej w portalu Marketplace | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do testowania obrazu maszyny Wirtualnej w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
ms.openlocfilehash: bf41953300c3042853118c815dbf64e4474106fa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194117"
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Przetestuj swoje oferty maszyny Wirtualnej w portalu Azure Marketplace w przemieszczania
Wdrożenie przejściowe oznacza wdrożenie jednostki SKU w prywatnej "piaskownicy", której można przetestować i zweryfikować jego działanie przed wdrożeniem jej w portalu Marketplace. Jednostka SKU pojawia się na etapie wdrażania przejściowego, tak jak do klienta, który wdrożył ją. Obraz maszyny Wirtualnej musi mieć certyfikat ma zostać wypchnięty do wdrażania przejściowego.

## <a name="step-1-push-your-offer-to-staging"></a>Krok 1. Wypchnięcia oferty do wdrażania przejściowego
1. Na **Publikuj** kliknij pozycję **Wypychanie do wdrażania przejściowego**.
   
    ![Rysowanie](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Jeśli w portalu wydawców powiadamia o błędach, popraw je.
3. W **kto ma dostęp do oferty przygotowanych?** okna dialogowego wprowadź listę subskrypcji platformy Azure, które będą używane do wyświetlenia podglądu oferty w [portalu Azure w wersji zapoznawczej](https://portal.azure.com).
   
   > [!NOTE]
   > W przypadku maszyn wirtualnych i szablony rozwiązań, **nie** subskrypcji listy dozwolonych elementów typu dostawcy usług Kryptograficznych, DreamSpark i Azure in Open.
   > 
   > 
   >
   > W przypadku maszyn wirtualnych, po kliknięciu przycisku **WYPYCHANIE do tymczasowego**, poniższe kroki są wykonywane za sceną. Można wyświetlić postęp każdego kroku na karcie publikowanie w publikowania portalu. Musisz sprawdzić tę stronę w regularnych odstępach czasu (do czasu jest wyświetlany stan przejściowa) Aby uzyskać informacje o błędzie, który potrzebne korekty od użytkownika końcowego.

   > - Na początku przemieszczania żądania jest przesyłany do zespołu certyfikacji, który zweryfikować wirtualnego dysku twardego. Jednakże, jeśli żądanie ma stało się tylko marketingowych zmiany, następnie certyfikacji krok zostanie pominięty.
   > - Po zakończeniu certyfikacji rozpoczęcia replikacji, oferty, we wszystkich centrach danych platformy Azure. Zazwyczaj zajmuje 24-48hours na zakończenie replikacji ale może potrwać tydzień w zależności od rozmiaru wirtualnego dysku twardego. Jeśli jednak żądanie ma stało się tylko marketingowych zmiany, następnie replikacji jest szybsze.
   > - Po zakończeniu replikacji to oferta będzie dostępna w [witryny Azure portal](http:/portal.azure.com). W tym czasie stan stają się PRZYGOTOWANE podczas publikowania w portalu. Przygotowane oferty jest widoczna w [witryny Azure portal](http:/portal.azure.com) wyłącznie przy użyciu identyfikatorów adres e-mail skojarzony z subskrypcją za pomocą której oferty są przygotowywane.

1. Zaloguj się do [portalu Azure w wersji zapoznawczej](https://portal.azure.com) przy użyciu jednej z subskrypcji platformy Azure wymienionych w poprzednim kroku.
2. Znajdź swoją ofertę i sprawdzanie poprawności punktów obrazu maszyny Wirtualnej:
   
   * Upewnij się, że marketingowych zawartości prawidłowo wyświetlane w portalu Marketplace.
   * Wdrażanie end-to-end obrazu maszyny Wirtualnej.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Oferta pozostanie na etapie wdrażania przejściowego, dopóki nie można powiadomić firmę Microsoft za pośrednictwem portalu wydawców [**Publikuj** kartę > kliknij przycisk **"Żądania zatwierdzenia do wypychania do produkcji"**], możesz przystąpić do wypchnięcia do Produkcja. Jest to najlepszy czas na mają wszyscy członkowie zespołu ewidencjonowanie w ramach przygotowania do ofertą, przechodząc do listy.
> 
> Przemieszczania platformy jest przeznaczona do testowania oferty w trybie podglądu przez wydawcę. Stanowczo odradza się przy użyciu tego platform do celów aparatów.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy oferty jest "przygotowane", należy przetestować jego działanie i marketingu zawartość, możesz przejść do końcowej fazy publikowania **kroku 4**: [Wdrażanie oferty w portalu Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: Jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

