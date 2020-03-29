---
title: Tworzenie niestandardowego obrazu laboratorium usługi Azure DevTest labs na maszynie wirtualnej | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć obraz niestandardowy w laboratoriach devtest platformy Azure z aprowizowana maszyna wirtualna przy użyciu witryny Azure portal
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
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60868503"
---
# <a name="create-a-custom-image-from-a-vm"></a>Tworzenie obrazu niestandardowego z poziomu maszyny wirtualnej

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Można utworzyć obraz niestandardowy z aprowizowana maszyna wirtualna, a następnie użyć tego obrazu niestandardowego do tworzenia identycznych maszyn wirtualnych. W poniższych krokach pokazano, jak utworzyć obraz niestandardowy z maszyny Wirtualnej:

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**

1. Z listy laboratoriów wybierz żądane laboratorium.  

1. W głównym okienku laboratorium wybierz pozycję **Moje maszyny wirtualne**.
 
1. W okienku **Moje maszyny wirtualne** wybierz maszynę wirtualną, z której chcesz utworzyć obraz niestandardowy.

1. W okienku zarządzania maszyny Wirtualnej wybierz pozycję **Utwórz obraz niestandardowy (VHD)**.

    ![Tworzenie niestandardowego elementu menu obrazu](./media/devtest-lab-create-template/create-custom-image.png)

1. W okienku **Obraz niestandardowy** wprowadź nazwę i opis obrazu niestandardowego. Te informacje są wyświetlane na liście baz podczas tworzenia maszyny Wirtualnej. Obraz niestandardowy będzie zawierał dysk systemu operacyjnego i wszystkie dyski danych dołączone do maszyny wirtualnej.

    ![Tworzenie niestandardowego okienka obrazu](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Wybierz, czy sysprep został uruchomiony na maszynie Wirtualnej. Jeśli sysprep nie został uruchomiony na maszynie Wirtualnej, określ, czy sysprep ma być uruchamiany na maszynie Wirtualnej podczas tworzenia obrazu niestandardowego.

1. Po zakończeniu wybierz **przycisk OK,** aby utworzyć obraz niestandardowy.

Po kilku minutach obraz niestandardowy jest tworzony i jest przechowywany wewnątrz konta magazynu laboratorium. Gdy użytkownik laboratorium chce utworzyć nową maszynę wirtualną, obraz jest dostępny na liście obrazów podstawowych.

![Obraz niestandardowy dostępny na liście obrazów podstawowych](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Podobne posty na blogu

- [Niestandardowe obrazy lub formuły?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między laboratoriami devtest platformy Azure](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)
