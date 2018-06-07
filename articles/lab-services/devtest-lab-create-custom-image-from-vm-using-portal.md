---
title: Tworzenie obrazu niestandardowego Azure DevTest Labs na podstawie maszyny Wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć obraz niestandardowy w usłudze Azure DevTest Labs elastycznie maszyny wirtualnej przy użyciu portalu Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22f1579b2df2acdc736ed4c1d5cee64d096c320a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635920"
---
# <a name="create-a-custom-image-from-a-vm"></a>Tworzenie niestandardowego obrazu z maszyny Wirtualnej

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Można utworzyć niestandardowy obraz z elastycznie maszyny Wirtualnej, a później użyć niestandardowego obrazu do utworzenia identycznych maszyn wirtualnych. Poniższe kroki pokazano, jak utworzyć obraz niestandardowy z maszyny Wirtualnej:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.

1. Z listy labs wybierz żądany laboratorium.  

1. W okienku głównym laboratorium, wybierz **Moje maszyny wirtualne**.
 
1. Na **Moje maszyny wirtualne** okienku, wybierz maszynę Wirtualną, z którego chcesz utworzyć niestandardowy obraz.

1. W okienku Zarządzanie maszyny Wirtualnej, wybierz **Tworzenie niestandardowego obrazu (VHD)**.

    ![Tworzenie niestandardowego obrazu elementu menu](./media/devtest-lab-create-template/create-custom-image.png)

1. Na **obraz niestandardowy** okienku, wprowadź nazwę i opis dla niestandardowego obrazu. Te informacje są wyświetlane na liście klas podstawowych, podczas tworzenia maszyny Wirtualnej. Niestandardowy obraz będzie zawierać dysk systemu operacyjnego i wszystkich dyskach danych dołączonych do maszyny wirtualnej.

    ![Utwórz okienko niestandardowego obrazu](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Określ, czy uruchomiono program sysprep na maszynie Wirtualnej. Jeśli nie uruchomiono programu sysprep na maszynie Wirtualnej, określ, czy uruchamiany po utworzeniu maszyny Wirtualnej z tego obrazu niestandardowego narzędzia sysprep.

1. Wybierz **OK** po zakończeniu można utworzyć niestandardowego obrazu.

Po kilku minutach obraz niestandardowy jest tworzony i jest przechowywany w laboratorium należy utworzyć konta magazynu. Jeśli laboratorium użytkownik chce, aby utworzyć nową maszynę Wirtualną, są dostępne na liście obrazów podstawowej.

![Obraz niestandardowy dostępne na liście podstawowej obrazów](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy na blogu pokrewne

- [Niestandardowe obrazy lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj Maszynę wirtualną do laboratorium](devtest-lab-add-vm.md)
