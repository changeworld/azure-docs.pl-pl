---
title: Dołączanie lub odłączanie dysku danych do maszyny wirtualnej w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dołączyć lub odłączyć dysku danych do maszyny wirtualnej w usłudze Azure DevTest Labs
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 193b66cf8bdaaefed5f073bec3ecb9050d076f19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787591"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Dołączanie lub odłączanie dysku danych do maszyny wirtualnej w usłudze Azure DevTest Labs
[Azure dysków zarządzanych](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) zarządza konta magazynu skojarzone z dysków danych maszyny wirtualnej. Dołącza użytkownika, nowe dane na dysku do maszyny Wirtualnej, określa typ i rozmiar dysku, które ma potrzebne, a Azure tworzy i automatycznie zarządza dysku. Dysk z danymi można następnie można odłączyć od maszyny Wirtualnej i ponownie albo nałożona do tej samej maszyny Wirtualnej lub dołączone do innej maszyny Wirtualnej, który należy do tego samego użytkownika.

Ta funkcja jest przydatna do zarządzania magazynu lub oprogramowania poza poszczególnych maszyn wirtualnych. Jeśli magazynu lub oprogramowania już istnieje wewnątrz dysku danych, jej można można łatwo dołączony, odłączyć i ponownie nałożona do żadnej maszyny Wirtualnej należącej do użytkownika, który jest właścicielem tego dysku danych.

## <a name="attach-a-data-disk"></a>Dołączanie dysku danych
Przed dołączenie dysku danych do maszyny Wirtualnej, przejrzyj następujące wskazówki:

- Rozmiar maszyny wirtualnej Określa, ile można dołączać dysków z danymi. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Możesz jeszcze dołączyć dysku danych do maszyny Wirtualnej, w którym jest uruchomiony. Upewnij się, że maszyna wirtualna działa, przed podjęciem próby dołączenie dysku danych.

### <a name="attach-a-new-disk"></a>Dołączanie nowego dysku
Wykonaj następujące kroki, aby utworzyć i dołączyć nowego dysku danych zarządzanych do maszyny Wirtualnej w usłudze Azure DevTest Labs.

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
1. Z listy labs wybierz żądany laboratorium. 
1. Z listy **Moje maszyny wirtualne**, wybierz uruchomionej maszyny Wirtualnej.
1. Wybierz z menu po lewej stronie **dysków**.

    ![Wybierz dyski danych dla maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Wybierz **Dołącz nowy** Utwórz nowy dysk danych i dołączenie go do maszyny Wirtualnej.

    ![Dołączanie nowego dysku danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Zakończenie **Dołączanie nowego dysku** okienka, wprowadzając nazwę dysku danych, typ i rozmiar.

    ![Wypełnij formularz "dołączyć nowego dysku"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Kliknij przycisk **OK**.

Po kilku chwilach nowy dysk danych jest utworzony i dołączony do maszyny Wirtualnej i zostanie wyświetlony na liście **dysków z danymi** dla tej maszyny Wirtualnej.

### <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
Wykonaj następujące kroki, aby ponownie dołączyć istniejącego dysku danych dostępnych do uruchomionej maszyny Wirtualnej. 

1. Wybierz uruchomionej maszyny Wirtualnej, dla którego chcesz ponownie dołączyć dysku danych.
1. Wybierz z menu po lewej stronie **dysków**.
1. Wybierz **dołączyć istniejącą** można dołączyć dysku danych do maszyny Wirtualnej.

    ![Dołączanie istniejącego dysku danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Z **dołączyć istniejącego dysku** okienku, wybierz OK.

    ![Dołączanie istniejącego dysku danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Po kilku chwilach dysk danych jest dołączony do maszyny Wirtualnej i zostanie wyświetlony na liście **dysków z danymi** dla tej maszyny Wirtualnej.

## <a name="detach-a-data-disk"></a>Odłączanie dysku danych
Jeśli nie ma potrzeby dysku danych, który jest dołączony do maszyny Wirtualnej, możesz ją łatwo odłączyć. Odłączanie Usuwa dysk od maszyny Wirtualnej, ale utrzymuje je w magazynie do użycia w przyszłości.

Jeśli chcesz ponownie użyć istniejących danych na dysku, możesz dołączyć go do tej samej maszyny wirtualnej lub innej.

### <a name="detach-from-the-vms-management-pane"></a>Odłącz od maszyny Wirtualnej w okienku Zarządzanie
1. Z listy maszyn wirtualnych wybierz maszynę Wirtualną, która ma podłączony dysk danych.
1. Wybierz z menu po lewej stronie **dysków**.

    ![Wybierz dyski danych dla maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. Z listy **dysków z danymi**, wybierz dysk danych, który chcesz odłączyć.
1. Wybierz **Detach** w górnej części okienka szczegółów dysku.

    ![Odłączanie dysku danych](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Wybierz **tak** aby upewnić się, że chcesz odłączyć dysk z danymi.

Dysk jest odłączona i jest dostępne do dołączenia do innej maszyny Wirtualnej. 
### <a name="detach-from-the-labs-main-pane"></a>Odłączyć od głównego okienka laboratorium
1. W okienku głównym środowiska laboratoryjnego, wybierz **dysków z danymi**.

    ![Dostęp do dysków danych laboratorium](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Kliknij prawym przyciskiem myszy dysk z danymi chcesz odłączyć — lub zaznacz jego wielokropek (...) — i wybierz polecenie **Detach**.

    ![Odłączanie dysku danych](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Wybierz **tak** potwierdzenie zostanie odłączony.

   > [!NOTE]
   > Jeśli już odłączono dysk danych, można go usunąć z listy dostępnych danych dysków, wybierając **usunąć**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Uaktualnienie dysku niezarządzanych danych
Jeśli masz istniejącą maszynę Wirtualną, która używa dysków niezarządzanych danych, można z łatwością przekształcić maszynę Wirtualną do obsługi dysków zarządzanych. Ten proces konwersji zarówno dysk systemu operacyjnego i wszystkich dysków dołączonych danych.

Aby uaktualnić dysku niezarządzanych danych, wykonaj kroki opisane w tym artykule, aby [odłączyć dysku danych](#detach-a-data-disk) z niezarządzanego maszyny Wirtualnej. Następnie [Podłącz dysk](#attach-an-existing-disk) na zarządzanej maszynie Wirtualnej, aby automatycznie uaktualniał dane dysku z niezarządzanych do zarządzanych.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak zarządzać dysków z danymi dla [claimable maszyn wirtualnych](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

