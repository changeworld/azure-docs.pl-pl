---
title: Dołączanie lub odłączanie dysku danych do maszyny wirtualnej w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dołączanie lub odłączanie dysku danych do maszyny wirtualnej w usłudze Azure DevTest Labs
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
ms.openlocfilehash: 2e168867ed342fb0b0545b5fdc330ba790f78de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60304517"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Dołączanie lub odłączanie dysku danych do maszyny wirtualnej w usłudze Azure DevTest Labs
[Usługa Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) zarządza kontami magazynu, skojarzonymi z dyskami danych maszyny wirtualnej. Dołącza użytkownika, nowe dane na dysku do maszyny Wirtualnej, określa typ i rozmiar dysku, która jest potrzebna, a platforma Azure utworzy i automatycznie zarządza dysku. Dysk z danymi następnie może zostać odłączona od maszyny Wirtualnej i albo ponownie dołączyć do tej samej maszyny Wirtualnej lub dołączone do innej maszyny Wirtualnej, który należy do tego samego użytkownika.

Ta funkcja jest przydatna do zarządzania magazynu lub oprogramowania poza poszczególnych maszyn wirtualnych. Jeśli magazynu lub oprogramowania już istnieje wewnątrz dysk z danymi, jego może można łatwo dołączone, odłączone i ponownie dołączyć do dowolnej maszyny Wirtualnej, którego właścicielem jest użytkownik, który jest właścicielem tego dysku danych.

## <a name="attach-a-data-disk"></a>Dołączanie dysku danych
Przed dołączeniem dysk danych do maszyny Wirtualnej, przejrzyj te wskazówki:

- Liczba dysków z danymi można dołączać jest kontrolowana przez rozmiar maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Dysk z danymi można dołączać tylko do maszyny Wirtualnej, która jest uruchomiona. Upewnij się, że maszyna wirtualna jest uruchomiona, przed podjęciem próby dołączyć dysk danych.

### <a name="attach-a-new-disk"></a>Dołączyć nowy dysk
Wykonaj następujące kroki, aby utworzyć i dołączyć nowy dysk danych zarządzanych do maszyny Wirtualnej w usłudze Azure DevTest Labs.

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Z listy labs wybierz żądane laboratorium. 
1. Z listy **Moje maszyny wirtualne**, wybierz uruchomionej maszyny Wirtualnej.
1. W menu po lewej stronie wybierz **dysków**.

    ![Wybierz dyski z danymi dla maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Wybierz **Dołącz nowy** Aby utworzyć nowy dysk danych i dołączyć go do maszyny Wirtualnej.

    ![Dołącz nowy dysk danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Wykonaj **Dołącz nowy dysk** okienko, wprowadzając nazwę dysku danych, typ i rozmiar.

    ![Wypełnij formularz "Dołączanie nowego dysku"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Kliknij przycisk **OK**.

Po kilku chwilach nowy dysk danych jest utworzone i dołączone do maszyny Wirtualnej i pojawia się na liście **dysków z danymi** dla tej maszyny Wirtualnej.

### <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
Wykonaj następujące kroki, aby ponownie dołączyć istniejący dysk danych dostępnych do uruchomionej maszyny Wirtualnej. 

1. Wybierz uruchomionej maszyny Wirtualnej, dla którego chcesz ponownie dołączyć dysk danych.
1. W menu po lewej stronie wybierz **dysków**.
1. Wybierz **Dołącz istniejące** dołączyć dysk danych do maszyny Wirtualnej.

    ![Dołącz istniejący dysk danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Z **dołączyć istniejącego dysku** okienko, a następnie naciśnij przycisk OK.

    ![Dołącz istniejący dysk danych do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Po kilku chwilach dysku danych dołączonego do maszyny Wirtualnej i pojawia się na liście **dysków z danymi** dla tej maszyny Wirtualnej.

## <a name="detach-a-data-disk"></a>Odłączanie dysku danych
Gdy już nie potrzebujesz dysku danych, który jest dołączony do maszyny Wirtualnej, możesz go łatwo odłączyć. Trwa odłączanie Usuwa dysk od maszyny Wirtualnej, ale utrzymuje je w magazynie do użycia w przyszłości.

Jeśli chcesz ponownie wykorzystać istniejące dane na dysku, możesz dołączyć go do tej samej maszyny wirtualnej lub innej.

### <a name="detach-from-the-vms-management-pane"></a>Odłącz od maszyny Wirtualnej zarządzania okienko
1. Na liście maszyn wirtualnych wybierz maszynę Wirtualną, która ma podłączony dysk danych.
1. W menu po lewej stronie wybierz **dysków**.

    ![Wybierz dyski z danymi dla maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. Z listy **dysków z danymi**, wybierz dysk danych, który chcesz odłączyć.
1. Wybierz **Odłącz** w górnej części okienka szczegółów dysku.

    ![Odłączanie dysku danych](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Wybierz **tak** aby upewnić się, że chcesz odłączyć dysk z danymi.

Dysk jest odłączony i jest dostępne do dołączenia do innej maszyny Wirtualnej. 
### <a name="detach-from-the-labs-main-pane"></a>Odłączanie od laboratorium w okienku głównym
1. W okienku głównym środowiska laboratoryjnego, wybierz **dysków z danymi**.

    ![Dostęp do dysków z danymi w laboratorium](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Kliknij prawym przyciskiem myszy dysk danych, które chcesz odłączyć — lub wybierz jego przycisk wielokropka (...) — i wybierz pozycję **Odłącz**.

    ![Odłączanie dysku danych](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Wybierz **tak** aby upewnić się, że chcesz go odłączyć.

   > [!NOTE]
   > Jeśli dysk danych jest już odłączona, możesz usunąć go z listy dysków danych o dostępności, zaznaczając **Usuń**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Uaktualnij niezarządzanego dysku danych
Jeśli masz istniejącej maszyny Wirtualnej, która korzysta z niezarządzanych dysków danych, można z łatwością przekształcić maszynę Wirtualną i użyj usługi managed disks. Ten proces konwertuje dysk systemu operacyjnego i wszelkich dołączonych dysków danych.

Aby uaktualnić niezarządzanego dysku danych, wykonaj kroki opisane w tym artykule, aby [dołączanie dysku danych](#detach-a-data-disk) z niezarządzanej maszyny Wirtualnej. Następnie [ponownie podłącz dysk](#attach-an-existing-disk) na zarządzanej maszynie Wirtualnej, aby automatycznie uaktualnić dane dysk z niezarządzanych do zarządzanych.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak zarządzać dyskami danych dla [przejęcia maszyn wirtualnych](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

