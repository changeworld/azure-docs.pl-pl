---
title: Zmień rozmiar maszyny Wirtualnej w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić rozmiar maszyny wirtualnej w usłudze Azure DevTest Labs
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
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: a0bc618a9c0a02aae884d8be359df6bdbf4c0d2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868094"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Zmień rozmiar maszyny Wirtualnej w laboratorium Azure DevTest Labs
Jedną z ważnych funkcji maszyn wirtualnych platformy Azure jest, że dzięki temu można zmienić rozmiar maszyny wirtualnej (VM) w zależności od potrzeb dla procesora CPU, sieci lub wydajność dysku. Usługa Azure DevTest Labs obsługuje tę funkcję dla maszyn wirtualnych w laboratorium teraz. Funkcja zmiany rozmiaru jest zgodna z zasadami laboratorium dozwolonych rozmiarów maszyn wirtualnych w środowisku laboratoryjnym. Oznacza to, że można zmienić rozmiaru maszyny Wirtualnej do tylko dozwolone rozmiary w środowisku laboratoryjnym. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Kroki, aby zmienić rozmiar maszyny Wirtualnej w laboratorium 
Aby zmienić rozmiar maszyny Wirtualnej w laboratorium Azure DevTest Labs, wykonaj następujące czynności: 

> [!NOTE]
> Jeśli masz połączenie z maszyną wirtualną za pośrednictwem sesji pulpitu zdalnego (RDP), Zapisz swoją pracę, a następnie odłącz od maszyny Wirtualnej przed zmianą jego rozmiaru.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Zaznacz na liście laboratoriów laboratorium, który zawiera maszynę Wirtualną, aby zmienić rozmiar.  
4. W panelu po lewej stronie wybierz **Moje maszyny wirtualne**. 
5. Z listy maszyn wirtualnych wybierz Maszynę wirtualną.
6. Wybierz **zatrzymać** na pasku narzędzi, jeśli maszyna wirtualna jest uruchomiona. Sprawdzanie stanu operacji w **powiadomienia** okna. Zaczekaj, aż maszyna wirtualna jest zatrzymana, a następnie Zamknij **powiadomienia** okna. 

    ![Zatrzymywanie maszyny wirtualnej](media/devtest-lab-resize-vm/stop-vm.png)
1. Na stronie maszyny wirtualnej dla maszyny Wirtualnej, wybierz **rozmiar** w obszarze **ustawienia** w menu po lewej stronie.

    ![Rozmiar menu](media/devtest-lab-resize-vm/size-menu.png)
1. W **wybierz rozmiar** , Przeglądaj i wybierz rozmiar maszyny Wirtualnej, a kliknij **wybierz**.     
1. Sprawdź stan operacji zmiany rozmiaru w **powiadomienia** okna.

    ![Zmień rozmiar stanu](media/devtest-lab-resize-vm/resize-status.png)
10. Po zmienieniu rozmiaru operacja zakończy się powodzeniem, Zamknij **powiadomienia** okna. 
11. Wybierz **Przegląd** w menu po lewej stronie, a następnie wybierz **ponowne uruchomienie** na pasku narzędzi, aby ponownie uruchomić maszynę Wirtualną. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje dotyczące funkcji zmiany rozmiaru, obsługiwane przez maszyny wirtualne platformy Azure, zobacz [zmiany rozmiaru maszyn wirtualnych](https://azure.microsoft.com/blog/resize-virtual-machines/).


