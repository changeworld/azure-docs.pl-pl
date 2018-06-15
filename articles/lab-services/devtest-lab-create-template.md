---
title: Tworzenie obrazu niestandardowego Azure DevTest Labs na podstawie pliku VHD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć obraz niestandardowy w usłudze Azure DevTest Labs z pliku VHD za pomocą portalu Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: a961565815ca0d89dc98a8d6a3e14b338b649398
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787465"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Tworzenie niestandardowego obrazu z pliku VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

W poniższych krokach objaśniono przez proces tworzenia niestandardowego obrazu z pliku VHD za pomocą portalu Azure:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.

1. Z listy labs wybierz żądany laboratorium.  

1. W okienku głównym laboratorium, wybierz **konfiguracji i zasadach**. 

1. Na **konfiguracji i zasadach** okienku wybierz **niestandardowych obrazów**.

1. Na **niestandardowych obrazów** okienku wybierz **+ Dodaj**.

    ![Dodaj niestandardowy obraz](./media/devtest-lab-create-template/add-custom-image.png)

1. Wprowadź nazwę niestandardowego obrazu. Ta nazwa będzie wyświetlana na liście podstawowej obrazów, podczas tworzenia maszyny Wirtualnej.

1. Wprowadź opis niestandardowego obrazu. Ten opis jest wyświetlany na liście obrazów podstawowej podczas tworzenia maszyny Wirtualnej.

1. Dla **typ systemu operacyjnego**, wybierz opcję **Windows** lub **Linux**.

    - W przypadku wybrania **Windows**, określ za pomocą pola wyboru czy *sysprep* zostało uruchomione na tym komputerze. 
    - W przypadku wybrania **Linux**, określ za pomocą pola wyboru czy *deprovision* zostało uruchomione na tym komputerze. 

1. Wybierz **wirtualnego dysku twardego** z menu rozwijanego. Jest to wirtualny dysk twardy, który będzie używany do utworzenia nowego obrazu niestandardowego. Jeśli to konieczne, umożliwia **przekazania dysku VHD za pomocą programu PowerShell**.

1. Jeśli obraz używany do tworzenia niestandardowego obrazu nie jest obrazem licencjonowane (opublikowane przez firmę Microsoft), można wprowadzić nazwę planu, oferty planu i plan wydawcy.

   - **Nazwa planu:** wprowadź nazwę obrazu z witryny Marketplace (SKU) z jest tworzony ten obraz niestandardowy 
   - **Planowanie oferta:** wprowadź produktu (Oferta) z obrazu witryny Marketplace, z którego utworzono ten obraz niestandardowy 
   - **Planowanie wydawcy:** wprowadź wydawcy obrazu witryny Marketplace, z którego utworzono ten obraz niestandardowy

   > [!NOTE]
   > Jeśli używasz, aby utworzyć obraz niestandardowy obraz jest **nie** licencjonowanego obrazu, a następnie te pola są puste, mogą być wypełniane w przypadku wybrania. Jeśli obraz **jest** licencjonowanego obrazu, a następnie pola są automatycznie wypełniane przy użyciu informacji o planie. Jeśli spróbujesz zmienić ich w takim przypadku zostanie wyświetlony komunikat ostrzegawczy.
   >
   >

1. Wybierz **OK** można utworzyć niestandardowego obrazu.

Po kilku minutach obraz niestandardowy jest tworzony i jest przechowywany w laboratorium należy utworzyć konta magazynu. Jeśli laboratorium użytkownik chce, aby utworzyć nową maszynę Wirtualną, są dostępne na liście obrazów podstawowej.

![Obraz niestandardowy dostępne na liście podstawowej obrazów](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy na blogu pokrewne

- [Niestandardowe obrazy lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj Maszynę wirtualną do laboratorium](./devtest-lab-add-vm.md)
