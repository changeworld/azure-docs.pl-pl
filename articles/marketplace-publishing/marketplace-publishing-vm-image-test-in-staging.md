---
title: Testowanie ofertę maszyny Wirtualnej dla witryny Marketplace | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu testowania obrazu maszyny Wirtualnej w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mbaldwin
ms.openlocfilehash: 7f225f113dd465f79d5d195b59a809fe3be6a1f8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936943"
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Testowanie ofertę maszyny Wirtualnej do portalu Azure Marketplace tymczasowych
Przemieszczania oznacza wdrażanie Twojego jednostki SKU w prywatnej "piaskownicy", gdzie testowania i zweryfikować jego działanie przed jego wdrożeniem w portalu Marketplace. Jednostka SKU jest wyświetlany w przemieszczania analogiczny, jak na rzecz klienta, który został wdrożony go. Obraz maszyny Wirtualnej muszą być certyfikowane do zostać przeniesiony do tymczasowego.

## <a name="step-1-push-your-offer-to-staging"></a>Krok 1: Push Twojej oferty przemieszczania
1. Na **publikowania** , kliknij pozycję **wypychania do przemieszczania**.
   
    ![Rysowanie](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Jeśli Portal publikowania powiadamia użytkownika o błędach, należy je poprawić.
3. W **kto ma dostęp do Twojej oferty przemieszczanego?** okna dialogowego wprowadź listę subskrypcji platformy Azure, które będą używane do podglądu ofertę w [portalu Azure w wersji zapoznawczej](https://portal.azure.com).
   
   > [!NOTE]
   > W przypadku maszyn wirtualnych i szablonów rozwiązania należy **nie** dozwolonych subskrypcje typu Dostawca usług Kryptograficznych, DreamSpark lub Azure otwartym.
   > 
   > 

    > W przypadku maszyn wirtualnych, po kliknięciu przycisku **WYPYCHANYCH STAGING**, są wykonywane następujące czynności, za sceny. Można wyświetlić postęp każdego kroku na karcie PUBLIKUJ w publikacji portalu. Należy sprawdzić tę stronę w regularnych odstępach czasu (do czasu stan pokazuje przejściowa) Aby uzyskać informacje o błędzie, który konieczne korekty użytkownika końcowego.

    > - Na początku przemieszczania żądania prowadzi do zespołu certyfikacji, który zweryfikować dysku vhd. Jednak jeśli — Otrzymano żądanie tylko marketingu zmiany, następnie certyfikacji krok zostanie pominięty.
    > - Po zakończeniu certyfikacji rozpoczęcia replikacji oferty, we wszystkich centrach danych platformy Azure. Zazwyczaj zajmuje 24-48hours na zakończenie replikacji ale może potrwać do tygodnia, w zależności od rozmiaru wirtualnego dysku twardego. Jeśli jednak — Otrzymano żądanie tylko marketingu zmiany, następnie replikacji jest szybsze.
    > - Po ukończeniu replikacji jest to oferta, dla której będą dostępne w [portalu Azure](http:/portal.azure.com). W tym czasie stan zostać UMIESZCZONE w publikacji portalu. Przemieszczanego oferta nie jest widoczna [portalu Azure](http:/portal.azure.com) wyłącznie przy użyciu nazwy e-mail skojarzonego z subskrypcją, z którym są przygotowywane oferty.

1. Zaloguj się do [portalu Azure w wersji zapoznawczej](https://portal.azure.com) przy użyciu jednej z subskrypcji platformy Azure wymienionych w poprzednim kroku.
2. Znajdź Twoją ofertę i zweryfikować punkty obrazu maszyny Wirtualnej:
   
   * Upewnij się, że marketingu zawartości wyświetlane prawidłowo w witrynie Marketplace.
   * Wdrażanie na trasie obrazu maszyny Wirtualnej.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Ofertę pozostanie w przejściowym dopóki powiadamiania firmy Microsoft za pośrednictwem portalu publikowania [**publikowania** kartę > kliknij przycisk **"Żądania zatwierdzenia do wypychania do produkcji"**] można przystąpić do produkcji. Jest to idealne czasu na wszystkie elementy członkowskie wyboru Twojego zespołu nad wszystko w ramach przygotowania do Twojej oferty, przechodząc do listy.
> 
> Platformy przemieszczania jest przeznaczony do testowania oferty w trybie podglądu przez wydawcę. Zdecydowanie zniechęcić się przy użyciu tego platofrm do celów dostępnych w handlu.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy ofertę jest "przemieszczone" i jego funkcje zostały przetestowane i marketingu zawartości, możesz przejść do fazy publikowania końcowego **krok 4**: [wdrażanie Twojej oferty w portalu Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

