---
title: Włączanie licencjonowanego obrazu w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączanie licencjonowanego obrazu w usłudze Azure DevTest Labs przy użyciu witryny Azure portal
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61294339"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Włączanie licencjonowanego obrazu w laboratorium Azure DevTest Labs

W usłudze Azure DevTest Labs licencjonowanego obrazu jest taki, który zawiera warunki i postanowienia — zwykle pochodzących z innych firm — które musi zostać zaakceptowana przed obraz jest dostępny dla użytkowników w środowisku laboratoryjnym. W następujących sekcjach opisano sposób pracy z licencjonowane obrazy, aby były one dostępne do użycia do tworzenia maszyn wirtualnych.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Określanie, czy licencjonowanego obrazu jest dostępna dla użytkowników
Pierwszym krokiem do zezwolenie użytkownikom na tworzenie maszyn wirtualnych z licencjonowanego obrazu jest upewnij się, że warunki i postanowienia zostaną zakwalifikowane licencjonowanego obrazu. Poniższe kroki pokazują, jak można wyświetlić stan oferty licencjonowanego obrazu i, jeśli to konieczne, zaakceptowania jego postanowień.

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.

1. Z listy labs wybierz żądane laboratorium.  

1. W lewym panelu w obszarze **ustawienia**, wybierz opcję **konfiguracji i zasad**.

1. W lewym panelu w obszarze **BAZAMI maszyn wirtualnych**, wybierz opcję **obrazów z witryny Marketplace**. 

    ![Element menu obrazów witryny Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Jest wyświetlana lista wszystkich obrazów dostępnych w portalu marketplace, w tym **stanu oferty** dla każdego obrazu.

    ![Lista obrazów witryny marketplace przedstawiająca stan oferty dla każdego obrazu](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Licencjonowanego obrazu wskazuje stan oferty 
    
    - **Zaakceptowano postanowienia:** licencjonowanego obrazu jest dostępna dla użytkowników do tworzenia maszyn wirtualnych. 
    - **Wymagane przejrzenie postanowień:** licencjonowanego obrazu nie jest obecnie dostępna dla użytkowników. Należy zaakceptować warunki i postanowienia licencyjne, zanim użytkownicy laboratorium można go użyć do tworzenia maszyn wirtualnych. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Udostępnianie obrazu licencjonowanego użytkownikom laboratorium
Aby upewnić się, że licencjonowanego obrazu jest dostępne dla użytkowników laboratorium, właściciel laboratorium z uprawnieniami administratora musisz najpierw zaakceptować warunki i postanowienia licencjonowanego obrazu. Włączanie wdrażania programowego dla subskrypcji powiązanej z licencjonowanego obrazu automatycznie akceptuje postanowienia prawne i zasady zachowania poufności informacji dla tego obrazu. [Praca z obrazów z witryny Marketplace usługi Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) zawiera dodatkowe informacje na temat wdrażania programowego obrazów witryny marketplace.

Wdrażanie programowe licencjonowanego obrazu można włączyć, wykonaj następujące czynności:

1. W [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), przejdź do listy **obrazów z witryny Marketplace**.

1. Zidentyfikuj licencjonowanego obrazu, do którego użytkownicy mają mieć dostęp, ale którego warunki nie zostały zaakceptowane. Na przykład, można napotkać maszyny wirtualnej analizy danych, który pokazuje stan **zaakceptowano postanowienia** lub **wymagane przejrzenie postanowień**.

    ![Konfigurowanie wdrażania programowego okna](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Maszyny wirtualne do nauki o danych są obrazy maszyny wirtualnej platformy Azure, wstępnie zainstalowane, skonfigurowane i przetestowane za pomocą kilku popularnych narzędzi, które są często używane do analizy danych, uczenia maszynowego i szkolenia sztucznej Inteligencji. [Wprowadzenie do platformy Azure maszyny wirtualnej analizy danych dla systemów Linux i Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) zapewnia dużą informacji na temat maszyny.
   >
   >

1. W **stanu oferty** kolumny obrazu, wybierz opcję **wymagane przejrzenie postanowień**.

1. W oknie konfigurowania wdrażania programowego wybierz **Włącz**.

    ![Konfigurowanie wdrażania programowego okna](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Może pojawić się wiele subskrypcji, na liście w oknie konfigurowania wdrażania programowego. Upewnij się, że jest włączane wdrażania programowego tylko w przypadku zamierzonej subskrypcji.
   >
   >


1. Wybierz pozycję **Zapisz**. 

    Na liście obrazów z witryny marketplace, który obraz teraz przedstawia **zaakceptowano postanowienia** i jest dostępna dla użytkowników umożliwia tworzenie maszyn wirtualnych.

> [!NOTE]
> Użytkownicy mogą tworzyć obrazu niestandardowego z licencjonowanego obrazu. Zobacz [Tworzenie obrazu niestandardowego z pliku VHD](devtest-lab-create-template.md) Aby uzyskać więcej informacji.
>
>


## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne

- [Obrazy niestandardowe lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między usłudze Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie niestandardowego obrazu maszyny wirtualnej](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Tworzenie niestandardowego obrazu z pliku wirtualnego dysku twardego](devtest-lab-create-template.md)
- [Dodaj Maszynę wirtualną do środowiska laboratoryjnego](devtest-lab-add-vm.md)