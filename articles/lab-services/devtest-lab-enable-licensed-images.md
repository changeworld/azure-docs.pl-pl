---
title: Włącz licencjonowanego obrazu w laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć licencjonowanego obrazu w usłudze Azure DevTest Labs przy użyciu portalu Azure
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
ms.openlocfilehash: 0e5de93f8a10d27c28b3f07567f9b6fa7e41d482
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Włącz licencjonowanego obrazu w laboratorium w usłudze Azure DevTest Labs

W usłudze Azure DevTest Labs licencjonowanego obrazu jest taki, który zawiera warunki i postanowienia — zwykle z innych firm — które musi zostać zaakceptowana przed obraz jest dostępna dla użytkowników w środowisku laboratoryjnym. W poniższych sekcjach opisano sposób pracy z licencjonowanym obrazy, aby były dostępne do użycia podczas tworzenia maszyn wirtualnych.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Określanie, czy licencjonowanego obrazu są dostępne dla użytkowników
Pierwszy krok w celu zezwolenie użytkownikom na tworzenie maszyn wirtualnych za pomocą licencjonowanego obrazu jest upewnij się, że warunki i postanowienia zaakceptowano licencjonowanego obrazu. W poniższej procedurze pokazano, jak można wyświetlić stan oferty licencjonowanego obrazu i, w razie potrzeby Zaakceptuj jego warunków i postanowień.

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.

1. Z listy labs wybierz żądany laboratorium.  

1. W lewym panelu w obszarze **ustawienia**, wybierz pozycję **konfiguracji i zasadach**.

1. W lewym panelu w obszarze **BAZ maszyn wirtualnych**, wybierz pozycję **obrazów Marketplace**. 

    ![Element menu Marketplace obrazów](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Listę wszystkich dostępnych marketplace obrazów jest wyświetlany, łącznie z **stanu oferty** dla każdego obrazu.

    ![Listy obrazów marketplace przedstawiający stan oferty dla każdego obrazu](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Licencjonowanego obrazu jest widoczny stan oferty 
    
    - **Zaakceptowano postanowienia:** licencjonowanego obrazu jest dostępna dla użytkowników w celu utworzenia maszyn wirtualnych. 
    - **Warunki przeglądu wymagane:** licencjonowanego obrazu nie jest obecnie dostępny dla użytkowników. Przed użytkownikami laboratorium służy do tworzenia maszyn wirtualnych należy zaakceptować warunki i postanowienia licencyjne. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Udostępnianie użytkownikom laboratorium licencjonowanego obrazu
Aby upewnić się, że licencjonowane obraz jest dostępny dla użytkowników w laboratorium, przez właściciela laboratorium z uprawnieniami administratora musi najpierw zaakceptować warunki i postanowienia licencjonowanego obrazu. Programowe wdrażanie subskrypcji skojarzonych z obrazem licencjonowane automatycznie akceptuje postanowienia prawne i zasady zachowania poufności dla tego obrazu. [Praca z obrazami Marketplace na usługi Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) udostępnia dodatkowe informacje dotyczące programistyczny wdrażania obrazów w witrynie marketplace.

Można włączyć wdrożenia programowe licencjonowanego obrazu wykonaj następujące czynności:

1. W [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), przejdź do listy **obrazów Marketplace**.

1. Zidentyfikuj licencjonowanego obrazu, do którego użytkownicy mają mieć dostęp, ale nie zaakceptowano warunki, których. Na przykład można napotkać pokazujący stan maszyny wirtualnej nauki danych **zaakceptowane postanowienia** lub **warunki przeglądu potrzebne**.

    ![Konfigurowanie okna wdrożenia programowe](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Maszyny wirtualne nauki danych są obrazy maszyny wirtualnej Azure, wstępnie zainstalować, skonfigurować i przetestować kilku popularnych narzędzia, które są często używane do analizy danych, uczenie maszynowe i szkolenia AI. [Wprowadzenie do danych nauki maszyny wirtualnej platformy Azure dla systemów Linux i Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) zapewnia dużą ilość informacji na temat DSVMs.
   >
   >

1. W **stanu oferty** obrazu, wybierz opcję **warunki przeglądu potrzebne**.

1. W oknie konfigurowania wdrażania programowego wybierz **włączyć**.

    ![Konfigurowanie okna wdrożenia programowe](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Może być wyświetlanych wiele subskrypcji na liście w oknie konfigurowania wdrażania programowego. Upewnij się, że jest włączane wdrażaniem programowym tylko w przypadku zamierzonej subskrypcji.
   >
   >


1. Wybierz pozycję **Zapisz**. 

    Na liście obrazów marketplace, który obraz przedstawia teraz **zaakceptowane postanowienia** i są dostępne dla użytkowników w celu utworzenia maszyny wirtualnej.

> [!NOTE]
> Użytkownicy mogą tworzyć niestandardowego obrazu z licencjonowanego obrazu. Zobacz [utworzyć niestandardowy obraz z pliku VHD](devtest-lab-create-template.md) Aby uzyskać więcej informacji.
>
>


## <a name="related-blog-posts"></a>Wpisy na blogu pokrewne

- [Niestandardowe obrazy lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie niestandardowego obrazu z maszyny Wirtualnej](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Tworzenie niestandardowego obrazu z pliku VHD](devtest-lab-create-template.md)
- [Dodaj Maszynę wirtualną do laboratorium](devtest-lab-add-vm.md)