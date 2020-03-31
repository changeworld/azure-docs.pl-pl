---
title: Ponowne rozmiary maszyny wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs
description: Dowiedz się, jak zmienić rozmiar maszyny wirtualnej (VM) w laboratorium devtest platformy Azure na podstawie potrzeb procesora, sieci lub wydajności dysku.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756181"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ponowne rozmiary maszyny wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs
Jedną z ważnych funkcji maszyn wirtualnych platformy Azure jest to, że umożliwia zmianę rozmiaru maszyny wirtualnej (VM) na podstawie potrzeb procesora CPU, sieci lub wydajności dysku. Usługa Azure DevTest Labs obsługuje tę funkcję dla maszyn wirtualnych w laboratorium teraz. Funkcja zmiany rozmiaru jest zgodna z zasadami laboratoryjnymi dla dozwolonych rozmiarów maszyn wirtualnych w laboratorium. Oznacza to, że można zmienić rozmiar maszyny Wirtualnej tylko dozwolonych rozmiarów w laboratorium. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Kroki, aby zmienić rozmiar maszyny Wirtualnej w laboratorium 
Aby zmienić rozmiar maszyny Wirtualnej w laboratorium w laboratoriach devtest azure, należy wykonać następujące kroki: 

> [!NOTE]
> Jeśli masz połączenie z maszyną wirtualną za pośrednictwem sesji pulpitu zdalnego (RDP), zapisz pracę i odłącz się od maszyny Wirtualnej przed jej ponownym rozmiarem.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz laboratorium, które zawiera maszynę wirtualną, której rozmiar chcesz zmienić.  
4. W lewym panelu wybierz pozycję **Moje maszyny wirtualne**. 
5. Z listy maszyn wirtualnych wybierz maszynę wirtualną.
6. Wybierz **pozycję Zatrzymaj** na pasku narzędzi, jeśli maszyna wirtualna jest uruchomiona. Sprawdź stan operacji w oknie **Powiadomienia.** Poczekaj, aż maszyna wirtualna zostanie zatrzymana, a następnie zamknij okno **Powiadomienia.** 

    ![Zatrzymywanie maszyny wirtualnej](media/devtest-lab-resize-vm/stop-vm.png)
1. Na stronie Maszyna wirtualna maszyny wirtualnej wybierz pozycję **Rozmiar** w obszarze **USTAWIENIA** w menu po lewej stronie.

    ![Menu Rozmiar](media/devtest-lab-resize-vm/size-menu.png)
1. W oknie **Wybieranie rozmiaru** przeglądaj i wybierz rozmiar maszyny Wirtualnej, a następnie kliknij przycisk **Wybierz**.     
1. Sprawdź stan operacji ponownego rozmiaru w oknie **Powiadomienia.**

    ![Stan rozmiaru](media/devtest-lab-resize-vm/resize-status.png)
10. Po pomyślnym zakończeniu operacji ponownego rozmiaru zamknij okno **Powiadomienia.** 
11. Wybierz **opcję Przegląd** w menu po lewej stronie, a następnie wybierz pozycję Uruchom **ponownie** na pasku narzędzi, aby ponownie uruchomić maszynę wirtualną. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje na temat funkcji ponownego rozmiaru obsługiwanej przez maszyny wirtualne platformy Azure, zobacz [Resize maszyny wirtualne](https://azure.microsoft.com/blog/resize-virtual-machines/).


