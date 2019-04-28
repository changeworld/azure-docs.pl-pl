---
title: Tworzenie niestandardowego obrazu usługi Azure DevTest Labs na podstawie pliku VHD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć obraz niestandardowy w usłudze Azure DevTest Labs z pliku VHD za pomocą witryny Azure portal
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
ms.openlocfilehash: 853c138c8cf73b41b0cebb6c1d349865e18eab6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296051"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Tworzenie niestandardowego obrazu z pliku wirtualnego dysku twardego

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

W poniższych krokach objaśniono proces tworzenia obrazu niestandardowego z pliku VHD za pomocą witryny Azure portal:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.

1. Z listy labs wybierz żądane laboratorium.  

1. W okienku głównym laboratorium, wybierz **konfiguracji i zasad**. 

1. Na **konfiguracji i zasad** okienku wybierz **niestandardowych obrazów**.

1. Na **niestandardowych obrazów** okienku wybierz **+ Dodaj**.

    ![Dodaj obraz niestandardowy](./media/devtest-lab-create-template/add-custom-image.png)

1. Wprowadź nazwę niestandardowego obrazu. Ta nazwa jest wyświetlana na liście obrazy podstawowe, podczas tworzenia maszyny Wirtualnej.

1. Wprowadź opis obrazu niestandardowego. Ten opis jest wyświetlany na liście obrazy podstawowe, podczas tworzenia maszyny Wirtualnej.

1. Aby uzyskać **typ systemu operacyjnego**, wybierz opcję **Windows** lub **Linux**.

    - Jeśli wybierzesz **Windows**, określ za pomocą pola wyboru czy *sysprep* została uruchomiona na komputerze. 
    - Jeśli wybierzesz **Linux**, określ za pomocą pola wyboru czy *deprovision* została uruchomiona na maszynie. 

1. Wybierz **wirtualnego dysku twardego** z menu rozwijanego. Jest to wirtualny dysk twardy, która będzie służyć do tworzenia nowego niestandardowego obrazu. W razie potrzeby zaznacz, aby **przekazania dysku VHD za pomocą programu PowerShell**.

1. Jeśli obraz użyty do utworzenia niestandardowego obrazu nie jest licencjonowanego obrazu (opublikowane przez firmę Microsoft), można wprowadzić nazwę planu, oferty planu i wydawcy planu.

   - **Nazwa planu:** Wprowadź nazwę obrazu z witryny Marketplace (SKU), z którym zostanie utworzony niestandardowy obraz 
   - **Oferta planu:** Wprowadź produkt (oferty) obrazu portalu Marketplace, z którego jest utworzony niestandardowy obraz 
   - **Zaplanuj wydawcy:** Wprowadź wydawcę obrazu portalu Marketplace, z którego jest utworzony niestandardowy obraz

   > [!NOTE]
   > Jeśli obraz używasz, aby utworzyć niestandardowy obraz jest **nie** licencjonowanego obrazu, a następnie te pola są puste, może być wypełnione, jeśli wybierzesz. Jeśli obraz **jest** licencjonowanego obrazu, a następnie pola są automatycznie wypełniane przy użyciu informacji o planie. Jeśli spróbujesz zmienić je w tym przypadku zostanie wyświetlony komunikat ostrzegawczy.
   >
   >

1. Wybierz **OK** do utworzenia obrazu niestandardowego.

Po kilku minutach niestandardowego obrazu zostanie utworzony i jest przechowywany w laboratorium, konta magazynu. Jeśli laboratorium użytkownik chce utworzyć nową maszynę Wirtualną, są dostępne na liście obrazy podstawowe.

![Obraz niestandardowy, które są dostępne na liście obrazy podstawowe](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne

- [Obrazy niestandardowe lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między usłudze Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj Maszynę wirtualną do środowiska laboratoryjnego](./devtest-lab-add-vm.md)
