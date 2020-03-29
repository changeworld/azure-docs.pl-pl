---
title: Rozwiązywanie problemów z wadliwą maszyną wirtualną platformy Azure przy użyciu zagnieżdżonej wirtualizacji na platformie Azure | Dokumenty firmy Microsoft
description: Jak rozwiązać problem z maszyną wirtualną platformy Azure przy użyciu zagnieżdżonej wirtualizacji na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119622"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Rozwiązywanie problemów z wadliwą maszyną wirtualną platformy Azure przy użyciu zagnieżdżonej wirtualizacji na platformie Azure

W tym artykule pokazano, jak utworzyć zagnieżdżone środowisko wirtualizacji na platformie Microsoft Azure, dzięki czemu można zainstalować dysk uszkodzonej maszyny Wirtualnej na hoście funkcji Hyper-V (Rescue VM) w celu rozwiązywania problemów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować wadliwą maszynę wirtualną, maszyna wirtualna rescue musi używać tego samego typu konta magazynu (standardowego lub premium) co uszkodzona maszyna wirtualna.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Krok 1: Tworzenie maszyny wirtualnej rescue i instalowanie roli funkcji funkcji Funkcji Hyper-V

1.  Utwórz nową maszynę wirtualną rescue:

    -  System operacyjny: Centrum danych systemu Windows Server 2016

    -  Rozmiar: Dowolna seria V3 z co najmniej dwoma rdzeniami obsługującymi zagnieżdżoną wirtualizację. Aby uzyskać więcej informacji, zobacz [Przedstawianie nowych rozmiarów maszyn wirtualnych Dv3 i Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Ta sama lokalizacja, konto magazynu i grupa zasobów jako wadliwa maszyna wirtualna.

    -  Wybierz ten sam typ magazynu co uszkodzona maszyna wirtualna (standardowa lub premium).

2.  Po utworzeniu maszyny Wirtualnej rescue pulpit zdalny do maszyny Wirtualnej rescue.

3.  W Menedżerze serwera wybierz pozycję **Zarządzaj** > **dodawaniem ról i funkcji**.

4.  W sekcji **Typ instalacji** wybierz pozycję **Instalacja oparta na rolach lub funkcjach**.

5.  W sekcji **Wybierz serwer docelowy** upewnij się, że wybrano maszynę wirtualną rescue.

6.  Wybierz >  **rolę Hyper-V****Dodaj funkcje**.

7.  Wybierz **pozycję Dalej** w sekcji **Operacje.**

8.  Jeśli przełącznik wirtualny jest dostępny, wybierz go. W przeciwnym razie wybierz **pozycję Dalej**.

9.  W sekcji **Migracja** wybierz pozycję **Dalej**

10. W sekcji **Sklepy domyślne** wybierz pozycję **Dalej**.

11. Zaznacz to pole wyboru, aby w razie potrzeby automatycznie ponownie uruchomić serwer.

12. Wybierz pozycję **Zainstaluj**.

13. Zezwalaj serwerowi na zainstalowanie roli funkcji Hyper-V. Trwa to kilka minut, a serwer uruchomi się ponownie automatycznie.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Krok 2: Tworzenie wadliwej maszyny Wirtualnej na serwerze Hyper-V maszyny wirtualnej rescue

1.  [Utwórz dysk migawki](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) dla dysku systemu operacyjnego maszyny Wirtualnej, który ma problem, a następnie dołącz dysk migawki do maszyny Wirtualnej recydywy.

2.  Pulpit zdalny na maszynie wirtualnej rescue.

3.  Otwórz zarządzanie dyskami (diskmgmt.msc). Upewnij się, że dysk uszkodzonej maszyny Wirtualnej jest ustawiony na **tryb offline**.

4.  Otwórz Menedżera funkcji Hyper-V: w **Menedżerze serwera**wybierz **rolę funkcji Hyper-V**. Kliknij prawym przyciskiem myszy serwer, a następnie wybierz **menedżera funkcji Hyper-V**.

5.  W Menedżerze funkcji Hyper-V kliknij prawym przyciskiem myszy maszynę wirtualną rescue, a następnie wybierz polecenie **Nowa** > **maszyna** > **wirtualna Dalej**.

6.  Wpisz nazwę maszyny Wirtualnej, a następnie wybierz pozycję **Dalej**.

7.  Wybierz **generację 1**.

8.  Ustaw pamięć startową na 1024 MB lub więcej.

9. W stosownych przypadkach wybierz utworzony przełącznik sieciowy funkcji Hyper-V. W przeciwnym razie przejdź do następnej strony.

10. Wybierz **pozycję Dołącz wirtualny dysk twardy później**.

    ![obraz dotyczący opcji Dołącz wirtualny dysk twardy później](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Wybierz **zakończ** podczas tworzenia maszyny Wirtualnej.

12. Kliknij prawym przyciskiem myszy utworzoną maszynę wirtualną, a następnie wybierz polecenie **Ustawienia**.

13. Wybierz **kontroler IDE 0**, wybierz pozycję **Dysk twardy**, a następnie kliknij przycisk **Dodaj**.

    ![obraz o dodaje nowy dysk twardy](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Na **fizycznym dysku twardym**wybierz dysk uszkodzonej maszyny Wirtualnej dołączonej do maszyny Wirtualnej platformy Azure. Jeśli na liście nie są wyświetlane żadne dyski, sprawdź, czy dysk jest ustawiony na tryb offline przy użyciu przystawki Zarządzanie dyskami.

    ![obraz dotyczący montażu dysku](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.

16. Kliknij dwukrotnie maszynę wirtualną, a następnie uruchom ją.

17. Teraz można pracować na maszynie Wirtualnej jako lokalnej maszyny Wirtualnej. Możesz wykonać wszystkie potrzebne kroki rozwiązywania problemów.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Krok 3: Wymień dysk systemu operacyjnego używany przez wadliwą maszynę wirtualną

1.  Po powrocie maszyny wirtualnej do trybu online zamknij maszynę wirtualną w menedżerze funkcji Hyper-V.

2.  [Odmontuj i odłącz naprawiony dysk systemu operacyjnego](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Zastąp dysk systemu operacyjnego używany przez maszynę wirtualną naprawionym dyskiem systemu operacyjnego](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z połączeniem się z maszyną [wirtualną, zobacz Rozwiązywanie problemów z połączeniami protokołu RDP z maszyną wirtualną platformy Azure.](troubleshoot-rdp-connection.md) Aby uzyskać problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej systemu Windows](troubleshoot-app-connection.md).
