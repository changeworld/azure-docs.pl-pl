---
title: Dołączanie lub odłączenie dysku danych do maszyny wirtualnej w laboratorium usługi Azure DevTest Labs
description: Dowiedz się, jak dołączyć lub odłączyć dysk danych do maszyny wirtualnej w laboratorium devtest azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78198874"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Dołączanie lub odłączenie dysku danych do maszyny wirtualnej w laboratorium usługi Azure DevTest Labs
[Usługi Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) zarządza kontami magazynu skojarzonymi z dyskami danych maszyny wirtualnej. Użytkownik dołącza nowy dysk danych do maszyny Wirtualnej, określa typ i rozmiar dysku, który jest potrzebny, a platforma Azure automatycznie tworzy dysk i zarządza nim. Dysk danych można następnie odłączyć od maszyny Wirtualnej i albo ponownie dołączyć później do tej samej maszyny Wirtualnej lub dołączone do innej maszyny Wirtualnej, która należy do tego samego użytkownika.

Ta funkcja jest przydatna do zarządzania pamięcią masową lub oprogramowaniem poza poszczególnymi maszynami wirtualnymi. Jeśli magazyn lub oprogramowanie już istnieje wewnątrz dysku danych, można je łatwo dołączyć, odłączyć i ponownie dołączyć do dowolnej maszyny Wirtualnej, która jest własnością użytkownika, który jest właścicielem tego dysku danych.

## <a name="attach-a-data-disk"></a>Dołączanie dysku danych
Przed dołączeniem dysku danych do maszyny Wirtualnej zapoznaj się z tymi wskazówkami:

- Rozmiar maszyny Wirtualnej określa, ile dysków z danymi można dołączyć. Aby uzyskać szczegółowe informacje, zobacz [Rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Dysk danych można dołączyć tylko do uruchomionej maszyny Wirtualnej. Upewnij się, że maszyna wirtualna jest uruchomiona przed próbą dołączenia dysku danych.

### <a name="attach-a-new-disk"></a>Dołączanie nowego dysku
Wykonaj następujące kroki, aby utworzyć i dołączyć nowy dysk danych zarządzanych do maszyny Wirtualnej w laboratorium devtest platformy Azure.

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
1. Z listy laboratoriów wybierz żądane laboratorium. 
1. Z listy **Moje maszyny wirtualne**wybierz uruchomiona maszynę wirtualną.
1. Z menu po lewej stronie wybierz pozycję **Dyski**.
1. Wybierz **pozycję Dołącz nowy,** aby utworzyć nowy dysk danych i dołączyć go do maszyny Wirtualnej.

    ![Dołączanie nowego dysku danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Uzupełnij **okienko Dołącz nowy dysk,** wprowadzając nazwę, typ i rozmiar dysku danych.

    ![Wypełnij formularz "Dołącz nowy dysk"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Kliknij przycisk **OK**.

Po kilku chwilach nowy dysk danych jest tworzony i dołączany do maszyny Wirtualnej i pojawia się na liście **dysków danych** dla tej maszyny Wirtualnej.

### <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
Wykonaj następujące kroki, aby ponownie dołączyć istniejący dostępny dysk danych do uruchomionej maszyny Wirtualnej. 

1. Wybierz działającą maszynę wirtualną, dla której chcesz ponownie dołączyć dysk danych.
1. Z menu po lewej stronie wybierz pozycję **Dyski**.
1. Wybierz **dołącz istniejący,** aby dołączyć dostępny dysk danych do maszyny Wirtualnej.

    ![Dołączanie istniejącego dysku danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. Z okienka **Dołączanie istniejącego dysku** wybierz przycisk OK.

    ![Dołączanie istniejącego dysku danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Po kilku chwilach dysk danych jest dołączony do maszyny Wirtualnej i pojawia się na liście **dysków danych** dla tej maszyny Wirtualnej.

## <a name="detach-a-data-disk"></a>Odłączanie dysku danych
Jeśli nie potrzebujesz już dysku danych, który jest dołączony do maszyny Wirtualnej, można łatwo ją odłączyć. Odłączanie usuwa dysk z maszyny Wirtualnej, ale przechowuje go w magazynie do późniejszego użycia.

Jeśli chcesz ponownie użyć istniejących danych na dysku, możesz ponownie dołączyć je do tej samej maszyny wirtualnej lub do innej maszyny.

### <a name="detach-from-the-vms-management-pane"></a>Odłączyć okienko zarządzania maszyny Wirtualnej
1. Z listy maszyn wirtualnych wybierz maszynę wirtualną z dołączonym dyskiem danych.
1. Z menu po lewej stronie wybierz pozycję **Dyski**.
1. Z listy **dysków danych**wybierz dysk danych, który chcesz odłączyć.

    ![Wybieranie dysków danych dla maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Wybierz **pozycję Odłącz** z górnej części okienka szczegółów dysku.

    ![Odłączanie dysku danych](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Wybierz **pozycję Tak,** aby potwierdzić, że chcesz odłączyć dysk danych.

Dysk jest odłączony i jest dostępny do podłączenia do innej maszyny Wirtualnej. 
### <a name="detach-from-the-labs-main-pane"></a>Odłączyć od głównego okienka laboratorium
1. W głównym okienku laboratorium wybierz pozycję **Moje dyski z danymi**.
1. Kliknij prawym przyciskiem myszy dysk danych, który chcesz odłączyć , lub wybierz jego wielokropek (**...**) - i wybierz opcję **Odłącz**.

    ![Odłączanie dysku danych](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Wybierz **pozycję Tak,** aby potwierdzić, że chcesz go odłączyć.

   > [!NOTE]
   > Jeśli dysk danych jest już odłączony, można go usunąć z listy dostępnych dysków z danymi, wybierając pozycję **Usuń**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Uaktualnianie niezarządzanego dysku danych
Jeśli masz istniejącą maszynę wirtualną, która używa niezarządzanych dysków z danymi, możesz łatwo przekonwertować maszynę wirtualną na dyski zarządzane. Ten proces konwertuje zarówno dysk systemu operacyjnego, jak i wszystkie dołączone dyski danych.

Aby uaktualnić niezarządzany dysk danych, wykonaj kroki opisane w tym artykule, aby [odłączyć dysk danych](#detach-a-data-disk) od niezarządzanej maszyny Wirtualnej. Następnie [ponownie dołącz dysk](#attach-an-existing-disk) do zarządzanej maszyny Wirtualnej, aby automatycznie uaktualnić dysk danych z niezarządzanego do zarządzanego.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zarządzać dyskami danych dla [maszyn wirtualnych, które mogą zgłaszać roszczenia.](devtest-lab-add-claimable-vm.md#unclaim-a-vm)

