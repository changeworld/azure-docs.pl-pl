---
title: Tworzenie niestandardowego obrazu usługi Azure DevTest Labs z pliku VHD | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć obraz niestandardowy w laboratorium devtest w usłudze Azure z pliku VHD przy użyciu portalu Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61296051"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Tworzenie obrazu niestandardowego z pliku VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przebiegają przez tworzenie niestandardowego obrazu z pliku VHD przy użyciu witryny Azure portal:

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**

1. Z listy laboratoriów wybierz żądane laboratorium.  

1. W głównym okienku laboratorium wybierz pozycję **Konfiguracja i zasady**. 

1. W okienku **Konfiguracja i zasady** wybierz pozycję **Obrazy niestandardowe**.

1. W okienku **Obrazy niestandardowe** wybierz pozycję **+Dodaj**.

    ![Dodawanie obrazu niestandardowego](./media/devtest-lab-create-template/add-custom-image.png)

1. Wprowadź nazwę obrazu niestandardowego. Ta nazwa jest wyświetlana na liście obrazów podstawowych podczas tworzenia maszyny Wirtualnej.

1. Wprowadź opis obrazu niestandardowego. Ten opis jest wyświetlany na liście obrazów podstawowych podczas tworzenia maszyny Wirtualnej.

1. W przypadku **typu systemu operacyjnego**wybierz system **Windows** lub **Linux**.

    - Jeśli wybierzesz **system Windows,** określ za pomocą pola wyboru, czy *sysprep* został uruchomiony na komputerze. 
    - Jeśli wybierzesz **Linux**, określić za pomocą pola wyboru, czy *deprovision* został uruchomiony na komputerze. 

1. Wybierz **dysk VHD** z menu rozwijanego. Jest to dysk VHD, który będzie używany do tworzenia nowego obrazu niestandardowego. W razie potrzeby wybierz opcję **Przekaż dysk VHD za pomocą programu PowerShell**.

1. Można również wprowadzić nazwę planu, ofertę planu i wydawcę planu, jeśli obraz użyty do utworzenia obrazu niestandardowego nie jest obrazem licencjonowanym (opublikowanym przez firmę Microsoft).

   - **Nazwa planu:** Wprowadź nazwę obrazu Marketplace (SKU), z którego tworzony jest ten obraz niestandardowy 
   - **Oferta planu:** Wprowadź produkt (ofertę) obrazu Marketplace, z którego tworzony jest ten obraz niestandardowy 
   - **Wydawca planu:** Wprowadź wydawcę obrazu marketplace, z którego tworzony jest ten obraz niestandardowy

   > [!NOTE]
   > Jeśli obraz używany do tworzenia obrazu niestandardowego **nie** jest obrazem licencjonowanym, pola te są puste i można je wypełnić, jeśli wybierzesz. Jeśli obraz **jest** obrazem licencjonowanym, pola są automatycznie wypełniane informacjami o planie. Jeśli spróbujesz je zmienić w tym przypadku, zostanie wyświetlony komunikat ostrzegawczy.
   >
   >

1. Wybierz **przycisk OK,** aby utworzyć obraz niestandardowy.

Po kilku minutach obraz niestandardowy jest tworzony i jest przechowywany wewnątrz konta magazynu laboratorium. Gdy użytkownik laboratorium chce utworzyć nową maszynę wirtualną, obraz jest dostępny na liście obrazów podstawowych.

![Obraz niestandardowy dostępny na liście obrazów podstawowych](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Podobne posty na blogu

- [Niestandardowe obrazy lub formuły?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między laboratoriami devtest platformy Azure](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie maszyny wirtualnej do laboratorium](./devtest-lab-add-vm.md)
