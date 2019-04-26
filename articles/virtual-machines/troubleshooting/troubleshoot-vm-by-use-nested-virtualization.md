---
title: Rozwiązać problem z maszyny Wirtualnej platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure | Dokumentacja firmy Microsoft
description: Jak rozwiązać problem z maszyny Wirtualnej platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: c84d015da907c8792f09d1d60e6bc8eddb7e2957
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444371"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Rozwiązać problem z maszyny Wirtualnej platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure

Ten artykuł pokazuje, jak utworzyć środowisko wirtualizacji zagnieżdżonej na platformie Microsoft Azure, dzięki czemu można zainstalować dysku problem maszyny Wirtualnej na hoście funkcji Hyper-V (VM ratunkowych) na potrzeby rozwiązywania problemów.

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj maszynę Wirtualną problemie, maszyna wirtualna ratownictwa musi spełniać następujące wymagania wstępne:

-   Maszyna wirtualna ratownictwa musi być w tej samej lokalizacji, problem maszyny Wirtualnej.

-   Maszyna wirtualna ratownictwa musi być w tej samej grupie zasobów jako problem maszyny Wirtualnej.

-   Ratownictwa maszyny Wirtualnej musi używać tego samego typu konta magazynu (standardowa / Premium), co problem maszyny Wirtualnej.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Krok 1: Utwórz Maszynę wirtualną ratownictwa i zainstalować rolę funkcji Hyper-V

1.  Utwórz nową maszynę Wirtualną ratownictwa:

    -  System operacyjny: Windows Server 2016 Datacenter

    -  Rozmiar: Dowolnej serii V3 z co najmniej dwa rdzenie, obsługujących wirtualizację zagnieżdżoną. Aby uzyskać więcej informacji, zobacz [Przedstawiamy nowe rozmiary Dv3 i Ev3 VM](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Tej samej lokalizacji, konto magazynu i grupie zasobów co problem maszyny Wirtualnej.

    -  Wybierz ten sam typ magazynu jako problem maszyny Wirtualnej (standardowa / Premium).

2.  Po ratownictwa maszyny Wirtualnej jest tworzony, pulpitu zdalnego z maszyną wirtualną ratunkowe.

3.  W Menedżerze serwera wybierz **Zarządzaj** > **Dodaj role i funkcje**.

4.  W **typu instalacji** zaznacz **Instalacja oparta na rolach lub oparta na funkcjach**.

5.  W **serwer docelowy wybierz** sekcji, upewnij się, że jest zaznaczona maszyna wirtualna ratownictwa.

6.  Wybierz **roli funkcji Hyper-V** > **Dodaj funkcje**.

7.  Wybierz **dalej** na **funkcji** sekcji.

8.  Jeśli przełącznik wirtualny jest dostępna, wybierz ją. W przeciwnym razie wybierz **dalej**.

9.  Na **migracji** zaznacz **dalej**

10. Na **magazyny domyślne** zaznacz **dalej**.

11. Pole wyboru, aby automatycznie uruchom ponownie serwer, jeśli jest to wymagane.

12. Wybierz pozycję **Zainstaluj**.

13. Zezwalaj na serwer zainstalować rolę funkcji Hyper-V. Trwa to kilka minut, a serwer zostanie automatycznie ponownie uruchomiony.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Krok 2: Utwórz problem maszyny Wirtualnej na serwerze funkcji Hyper-V ratownictwa maszyny Wirtualnej

1.  Zapisz nazwę dysku w problem maszyny Wirtualnej, a następnie usuń problem maszyny Wirtualnej. Upewnij się, że utrzymywanie wszystkich dołączonych dysków. 

2.  Dołącz dysk systemu operacyjnego maszyny Wirtualnej problemu jako dysku danych maszyny wirtualnej ratunkowe.

    1.  Po jego usunięciu maszyny Wirtualnej, przejdź do maszyny Wirtualnej ratunkowe.

    2.  Wybierz **dysków**, a następnie **Dodaj dysk danych**.

    3.  Wybierz dysk maszyny Wirtualnej problem, a następnie wybierz pozycję **Zapisz**.

3.  Po dysk został pomyślnie dołączone, zdalny pulpit maszyny Wirtualnej ratunkowe.

4.  Open Disk Management (diskmgmt.msc). Upewnij się, że dysk problem maszyny Wirtualnej, jest ustawiona na **Offline**.

5.  Otwórz Menedżera funkcji Hyper-V: W **Menedżera serwera**, wybierz opcję **roli funkcji Hyper-V**. Kliknij prawym przyciskiem myszy serwer, a następnie wybierz **Menedżera funkcji Hyper-V**.

6.  W Menedżerze funkcji Hyper-V kliknij prawym przyciskiem myszy maszynę Wirtualną ratownicze, a następnie wybierz **New** > **maszyny wirtualnej** > **dalej**.

7.  Wpisz nazwę maszyny Wirtualnej, a następnie wybierz **dalej**.

8.  Wybierz **generacji 1**.

9.  Ustaw pamięci początkowej na 1024 MB lub większej.

10. Wybierz przełącznik sieci funkcji Hyper-V, który został utworzony, jeśli ma to zastosowanie. Else przejście do następnej strony.

11. Wybierz **Dołącz wirtualny dysk twardy później**.

    ![Obraz dotyczący dołączania opcji później wirtualnego dysku twardego](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Wybierz **Zakończ** po utworzeniu maszyny Wirtualnej.

13. Kliknij prawym przyciskiem myszy maszyny Wirtualnej, który został utworzony, a następnie wybierz pozycję **ustawienia**.

14. Wybierz **kontroler IDE 0**, wybierz opcję **dysk twardy**, a następnie kliknij przycisk **Dodaj**.

    ![Obraz dotyczący dodaje nowy dysk twardy](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. W **fizyczny dysk twardy**, wybierz dysk problem maszyny Wirtualnej, który został załączony do maszyny Wirtualnej platformy Azure. Jeśli nie ma żadnych dysków na liście, sprawdź, jeśli dysk jest ustawiony na tryb offline przy użyciu przystawki Zarządzanie dyskami.

    ![Obraz dotyczący instaluje dysk](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.

18. Kliknij dwukrotnie na maszynie Wirtualnej, a następnie uruchom go.

19. Możesz teraz działać na maszynie Wirtualnej jako lokalną maszynę Wirtualną. Możesz wykonać kroki rozwiązywania problemów, które są potrzebne.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Krok 3: Ponownie utwórz maszynę Wirtualną platformy Azure na platformie Azure

1.  Po uzyskaniu z powrotem do trybu online maszyny Wirtualnej, zamknij maszynę Wirtualną w Menedżerze funkcji Hyper-V.

2.  Przejdź do [witryny Azure portal](https://portal.azure.com) i wybierz maszynę Wirtualną ratownictwa > dyski, skopiuj nazwę dysku. Nazwa będzie używana w następnym kroku. Odłączanie dysku stałego z ratownictwa maszyny Wirtualnej.

3.  Przejdź do **wszystkie zasoby**, wyszukaj nazwę dysku, a następnie wybierz dysk.

     ![Obraz dotyczący wyszukuje dysku](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Kliknij przycisk **tworzenie maszyny Wirtualnej**.

     ![Obraz dotyczący maszyna wirtualna jest tworzona z dysku](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Azure PowerShell umożliwia również tworzenie maszyny Wirtualnej z dysku. Aby uzyskać więcej informacji, zobacz [Utwórz nową maszynę Wirtualną z istniejącego dysku przy użyciu programu PowerShell](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Kolejne kroki

Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [połączenia RDP Rozwiązywanie problemów z Maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [aplikacji Rozwiązywanie problemów z łącznością na maszynie Wirtualnej Windows](troubleshoot-app-connection.md).
