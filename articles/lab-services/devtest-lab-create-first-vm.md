---
title: Tworzenie pierwszej maszyny Wirtualnej w laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć pierwszy maszynę wirtualną w laboratorium w usłudze Azure DevTest Labs
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
ms.openlocfilehash: 0240bbdf2055ac44f8836adfaad3bf6c44893b77
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Tworzenie pierwszej maszyny Wirtualnej w laboratorium w usłudze Azure DevTest Labs

Przy pierwszym dostępu DevTest Labs i chcesz utworzyć pierwszy maszyny Wirtualnej, możesz będzie prawdopodobnie to zrobić za pomocą wstępnie załadowane [obrazu z witryny marketplace podstawowej](devtest-lab-configure-marketplace-images.md). Później również będzie można wybrać [niestandardowego obrazu oraz formułę](devtest-lab-add-vm.md) podczas tworzenia więcej maszyn wirtualnych. 

Ten samouczek przeprowadzi Cię przez dodawanie pierwszej maszyny Wirtualnej do laboratorium w usłudze DevTest Labs przy użyciu portalu Azure.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Kroki, aby dodać pierwszy maszyny Wirtualnej do laboratorium w usłudze Azure DevTest Labs
1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
1. Z listy labs wybierz laboratorium, w którym chcesz utworzyć maszynę Wirtualną.  
1. W laboratorium **omówienie** bloku, wybierz opcję **+ Dodaj**.  

    ![Dodawanie przycisku maszyny Wirtualnej](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **wybierz podstawowej** bloku, wybierz pozycję marketplace obrazu dla maszyny Wirtualnej.
1. Na **maszyny wirtualnej** bloku, wprowadź nazwę dla nowej maszyny wirtualnej w **nazwę maszyny wirtualnej** pola tekstowego.

    ![Bloku maszyny Wirtualnej laboratorium](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Wprowadź **nazwy użytkownika** udzieleniu uprawnień administratora na maszynie wirtualnej.  
1. Wprowadź hasło w polu tekstowym etykietą **wpisz wartość**.
1. **Typu dysku maszyny wirtualnej** Określa, który typ dysku magazynu jest dozwolony dla maszyn wirtualnych w laboratorium.
1. Wybierz **rozmiar maszyny wirtualnej** i wybierz jedną z wstępnie zdefiniowane elementy, które Określ rdzeni procesora, rozmiar pamięci RAM i rozmiar dysku twardego maszyny wirtualnej do utworzenia.
1. Wybierz **artefakty** a — z listy artefakty — wybierz i skonfiguruj artefaktów, które chcesz dodać do obrazu podstawowego.
    **Uwaga:** Jeśli nowych użytkowników programu DevTest Labs lub konfigurowanie artefaktów, zapoznaj się [Dodaj istniejący artefakt do maszyny Wirtualnej](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) sekcji, a następnie wróć tutaj po zakończeniu.
1. Wybierz **Utwórz** dodać określoną maszynę Wirtualną do laboratorium.

   Blok laboratorium Wyświetla stan tworzenia maszyny Wirtualnej — najpierw jako **tworzenie**, następnie jako **systemem** po uruchomieniu maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
* Po utworzeniu maszyny Wirtualnej można Połącz się z maszyną Wirtualną, wybierając **Connect** w bloku maszyny Wirtualnej.
* Zapoznaj się z [dodać maszyny Wirtualnej do laboratorium](devtest-lab-add-vm.md) na pełniejsze informacje dotyczące dodawania kolejnych maszyn wirtualnych w laboratorium.
* Eksploruj [galerię szablonów DevTest Labs Azure Resource Manager — Szybki Start](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
