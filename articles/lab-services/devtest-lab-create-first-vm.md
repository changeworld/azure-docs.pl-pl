---
title: Tworzenie pierwszej maszyny Wirtualnej w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć pierwszą maszynę wirtualną w laboratorium Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 93ce9feaf52282b9477d49eaf270d6d89dca7811
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232532"
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Tworzenie pierwszej maszyny Wirtualnej w laboratorium Azure DevTest Labs

Przy pierwszym dostęp do usługi DevTest Labs i chcesz utworzyć pierwszą maszynę Wirtualną, użytkownik będzie prawdopodobnie to zrobić przy użyciu wstępnie załadowane [obrazu z witryny marketplace podstawowy](devtest-lab-configure-marketplace-images.md). Później będzie można również wybrać z [niestandardowego obrazu i formuły](devtest-lab-add-vm.md) podczas tworzenia więcej maszyn wirtualnych. 

Ten samouczek przeprowadzi Cię przez dodawanie pierwszej maszyny Wirtualnej do laboratorium w usłudze DevTest Labs przy użyciu witryny Azure portal.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki, aby dodać pierwszej maszyny Wirtualnej do laboratorium Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Zaznacz na liście laboratoriów laboratorium, w którym chcesz utworzyć maszynę Wirtualną.  
1. W laboratorium **Przegląd** bloku wybierz **+ Dodaj**.  

    ![Dodawanie przycisku maszyny Wirtualnej](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **wybierz podstawowej** bloku, wybierz opcję rynku obrazów do maszyny Wirtualnej.
1. Na **maszyny wirtualnej** bloku, wprowadź nazwę dla nowej maszyny wirtualnej w **nazwę maszyny wirtualnej** pola tekstowego.

    ![Blok maszyny Wirtualnej w laboratorium](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Wprowadź **nazwa_użytkownika** udzieleniu uprawnień administratora na maszynie wirtualnej.  
1. Wprowadź hasło w polu tekstowym etykietą **wpisz wartość**.
1. **Typ dysku maszyny wirtualnej** Określa typy dysku magazynu jest dozwolone dla maszyn wirtualnych w środowisku laboratoryjnym.
1. Wybierz **rozmiar maszyny wirtualnej** i wybierz jeden z wstępnie zdefiniowanych elementów, które określają rdzeni procesora, pamięci RAM i rozmiar dysku twardego maszyny wirtualnej, aby utworzyć.
1. Wybierz **artefaktów** i — z listy artefaktów — wybierz i skonfiguruj artefaktów, które chcesz dodać do obrazu podstawowego.
    **Uwaga:** Jeśli jesteś nowym użytkownikiem usługi DevTest Labs, lub konfigurowanie artefaktów, dotyczą [Dodaj istniejący artefakt do maszyny Wirtualnej](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) sekcji, a następnie wróć tutaj po zakończeniu.
1. Wybierz **Utwórz** dodać określoną maszynę Wirtualną do laboratorium.

   Blok laboratorium Wyświetla stan tworzenia maszyny Wirtualnej — najpierw jako **tworzenie**, następnie jako **systemem** po uruchomieniu maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
* Po utworzeniu maszyny Wirtualnej można nawiązać maszyny Wirtualnej, wybierając **Connect** w bloku maszyny Wirtualnej.
* Zapoznaj się z [Dodawanie maszyny Wirtualnej do laboratorium](devtest-lab-add-vm.md) uzyskać pełniejsze informacje dotyczące dodawania kolejnych maszyn wirtualnych w środowisku laboratoryjnym.
* Zapoznaj się z [galerii szablonów DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
