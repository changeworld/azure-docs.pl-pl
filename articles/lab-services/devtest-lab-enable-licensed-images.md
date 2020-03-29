---
title: Włączanie licencjonowanego obrazu w laboratorium w laboratorium usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć licencjonowany obraz w laboratoriach devtest azure przy użyciu witryny Azure portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 11b6553fe8aceef0d3d15977998dd870c275128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61294339"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Włączanie licencjonowanego obrazu w laboratorium w laboratorium usługi Azure DevTest Labs

W programie Azure DevTest Labs licencjonowany obraz zawiera warunki — zazwyczaj od strony trzeciej — które muszą zostać zaakceptowane, zanim obraz będzie dostępny dla użytkowników w laboratorium. W poniższych sekcjach opisano sposób pracy z licencjonowanymi obrazami, dzięki czemu są one dostępne do tworzenia maszyn wirtualnych.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Określanie, czy licencjonowany obraz jest dostępny dla użytkowników
Pierwszym krokiem do umożliwienia użytkownikom tworzenia maszyn wirtualnych z licencjonowanego obrazu jest upewnienie się, że warunki zostały zaakceptowane dla licencjonowanego obrazu. Poniższe kroki pokazują, jak można wyświetlić stan oferty licencjonowanego obrazu i, jeśli to konieczne, zaakceptować jego warunki.

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**

1. Z listy laboratoriów wybierz żądane laboratorium.  

1. W lewym panelu w obszarze **USTAWIENIA**wybierz **pozycję Konfiguracja i zasady**.

1. W lewym panelu w obszarze **BAZY MASZYN WIRTUALNYCH**wybierz pozycję **Obrazy z marketplace**. 

    ![Element menu Obrazy marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Zostanie wyświetlona lista wszystkich dostępnych obrazów z rynku, w tym **STAN OFERTY** dla każdego obrazu.

    ![Lista obrazów rynkowych ze stanem oferty dla każdego obrazu](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Licencjonowany obraz przedstawia stan oferty 
    
    - **Akceptowane warunki:** licencjonowany obraz jest dostępny dla użytkowników do tworzenia maszyn wirtualnych. 
    - **Wymagana recenzja warunków:** licencjonowany obraz nie jest obecnie dostępny dla użytkowników. Warunki licencji muszą zostać zaakceptowane, zanim użytkownicy laboratorium będą mogli jej używać do tworzenia maszyn wirtualnych. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Udostępnianie licencjonowanego obrazu użytkownikom laboratorium
Aby upewnić się, że licencjonowany obraz jest dostępny dla użytkowników laboratorium, właściciel laboratorium z uprawnieniami administratora musi najpierw zaakceptować warunki dla tego licencjonowanego obrazu. Włączenie wdrożenia programowego dla subskrypcji skojarzonej z licencjonowanym obrazem automatycznie akceptuje warunki prawne i zasady zachowania poufności informacji dla tego obrazu. [Praca z obrazami portalu Marketplace w usłudze Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) zawiera dodatkowe informacje na temat programowego wdrażania obrazów witryny marketplace.

Wdrażanie programowe dla licencjonowanego obrazu można włączyć, wykonując następujące kroki:

1. W [witrynie Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)przejdź do listy **obrazów marketplace**.

1. Zidentyfikuj licencjonowany obraz, do którego użytkownicy mają mieć dostęp, ale którego warunki nie zostały zaakceptowane. Na przykład może zostać wyświetlona maszyna wirtualna do nauki o danych, która pokazuje stan **zaakceptowanych warunków** lub **wymagana recenzja warunków.**

    ![Okno Konfigurowanie wdrażania programowego](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Maszyny wirtualne do nauki o danych to obrazy maszyn wirtualnych platformy Azure, wstępnie zainstalowane, skonfigurowane i przetestowane przy użyciu kilku popularnych narzędzi, które są powszechnie używane do analizy danych, uczenia maszynowego i szkolenia w zakresie sztucznej inteligencji. [Wprowadzenie do maszyny wirtualnej do nauki o danych platformy Azure dla systemów Linux i Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) zawiera wiele informacji na temat maszyn DSVM.
   >
   >

1. W kolumnie **OFERTA STATUS** obrazu wybierz pozycję Wymagana **recenzja warunków**.

1. W oknie Konfigurowanie wdrażania programowego wybierz pozycję **Włącz**.

    ![Okno Konfigurowanie wdrażania programowego](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > W oknie Konfigurowanie wdrażania programowego może być wyświetlanych wiele subskrypcji. Upewnij się, że włączasz wdrażanie programowe tylko dla zamierzonej subskrypcji.
   >
   >


1. Wybierz **pozycję Zapisz**. 

    Na liście obrazów marketplace, że obraz teraz pokazuje **warunki akceptowane** i jest dostępny dla użytkowników do tworzenia maszyn wirtualnych.

> [!NOTE]
> Użytkownicy mogą tworzyć obraz niestandardowy na podstawie licencjonowanego obrazu. Aby uzyskać więcej informacji, zobacz [Tworzenie obrazu niestandardowego z pliku VHD.](devtest-lab-create-template.md)
>
>


## <a name="related-blog-posts"></a>Podobne posty na blogu

- [Niestandardowe obrazy lub formuły?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między laboratoriami devtest platformy Azure](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie obrazu niestandardowego z poziomu maszyny wirtualnej](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Tworzenie obrazu niestandardowego z pliku VHD](devtest-lab-create-template.md)
- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)