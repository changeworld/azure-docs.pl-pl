---
title: Tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z maszyny Wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć obraz niestandardowy w usłudze Azure DevTest Labs z aprowizowanej maszyny Wirtualnej przy użyciu witryny Azure portal
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868503"
---
# <a name="create-a-custom-image-from-a-vm"></a>Tworzenie niestandardowego obrazu maszyny wirtualnej

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Tworzenie obrazu niestandardowego z aprowizowanej maszyny Wirtualnej i później Użyj tego obrazu niestandardowego do tworzenia identycznych maszyn wirtualnych. Poniższe kroki ilustrują Tworzenie niestandardowego obrazu maszyny wirtualnej:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.

1. Z listy labs wybierz żądane laboratorium.  

1. W okienku głównym laboratorium, wybierz **Moje maszyny wirtualne**.
 
1. Na **Moje maszyny wirtualne** okienku wybierz maszynę Wirtualną, z którego chcesz utworzyć obraz niestandardowy.

1. W okienku zarządzania maszyny Wirtualnej, wybierz **Tworzenie niestandardowego obrazu (VHD)**.

    ![Tworzenie niestandardowego obrazu elementu menu.](./media/devtest-lab-create-template/create-custom-image.png)

1. Na **obraz niestandardowy** okienku, wprowadź nazwę i opis obrazu niestandardowego. Te informacje są wyświetlane na liście zasad, po utworzeniu maszyny Wirtualnej. Niestandardowy obraz będzie zawierać dysk systemu operacyjnego i dysków danych dołączonych do maszyny wirtualnej.

    ![Tworzenie obrazu niestandardowego okienka](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Wybierz, czy uruchomiono program sysprep na maszynie Wirtualnej. Jeśli sysprep nie zostało uruchomione na maszynie Wirtualnej, określ, czy program sysprep do uruchamiania na maszynie Wirtualnej, po utworzeniu obrazu niestandardowego.

1. Wybierz **OK** po zakończeniu, aby utworzyć obraz niestandardowy.

Po kilku minutach niestandardowego obrazu zostanie utworzony i jest przechowywany w laboratorium, konta magazynu. Jeśli laboratorium użytkownik chce utworzyć nową maszynę Wirtualną, są dostępne na liście obrazy podstawowe.

![Obraz niestandardowy, które są dostępne na liście obrazy podstawowe](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne

- [Obrazy niestandardowe lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między usłudze Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj Maszynę wirtualną do środowiska laboratoryjnego](devtest-lab-add-vm.md)
