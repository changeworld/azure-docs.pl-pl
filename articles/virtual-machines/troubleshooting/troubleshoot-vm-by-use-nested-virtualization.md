---
title: Rozwiązywanie problemów z maszyną wirtualną platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure | Microsoft Docs
description: Jak rozwiązywać problemy z maszyną wirtualną platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure
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
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: ad359a19cb42bf115189aca7905d1908d0dc5284
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087056"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Rozwiązywanie problemów z maszyną wirtualną platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure

W tym artykule pokazano, jak utworzyć zagnieżdżone środowisko wirtualizacji w Microsoft Azure, aby można było zainstalować dysk maszyny wirtualnej z problemami na hoście funkcji Hyper-V (ratowniczej maszyny wirtualnej) na potrzeby rozwiązywania problemów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było zainstalować maszynę wirtualną problemu, maszyna wirtualna musi spełniać następujące wymagania wstępne:

-   Maszyna ratownicza musi znajdować się w tej samej lokalizacji co maszyna wirtualna problemu.

-   Ratownicza maszyna wirtualna musi znajdować się w tej samej grupie zasobów co maszyna wirtualna problemu.

-   W przypadku maszyny wirtualnej z problemową maszyną wirtualną należy używać tego samego typu konta magazynu (Standard lub Premium).

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Krok 1: Utwórz ratowniczą maszynę wirtualną i Zainstaluj rolę funkcji Hyper-V

1.  Utwórz nową ratowniczą maszynę wirtualną:

    -  System operacyjny: Windows Server 2016 Datacenter

    -  Rozmiar: Każda seria v3 z co najmniej dwoma rdzeniami obsługującymi wirtualizację zagnieżdżoną. Aby uzyskać więcej informacji, zobacz [wprowadzenie nowych rozmiarów maszyn wirtualnych Dv3 i EV3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Ta sama lokalizacja, konto magazynu i Grupa zasobów jest taka sama jak maszyna wirtualna problemu.

    -  Wybierz ten sam typ magazynu co maszyna wirtualna o problemach (standardowa lub Premium).

2.  Po utworzeniu łodziowej maszyny wirtualnej do ratowniczej maszyny wirtualnej.

3.  W Menedżer serwera wybierz pozycję **Zarządzaj** > **Dodaj role i funkcje**.

4.  W sekcji **Typ instalacji** wybierz pozycję **Instalacja oparta na rolach lub oparta na funkcjach**.

5.  Upewnij się, że w sekcji **Wybierz serwer docelowy** została wybrana maszyna wirtualna.

6.  Wybierz >  **rolę funkcji Hyper-V** **Dodaj funkcje**.

7.  W sekcji **funkcje** wybierz pozycję **dalej** .

8.  Jeśli przełącznik wirtualny jest dostępny, zaznacz go. W przeciwnym razie wybierz pozycję **dalej**.

9.  W sekcji **migracja** wybierz pozycję **dalej** .

10. W sekcji **magazyny domyślne** wybierz pozycję **dalej**.

11. Zaznacz pole wyboru w celu automatycznego ponownego uruchomienia serwera, jeśli jest to wymagane.

12. Wybierz pozycję **Zainstaluj**.

13. Zezwól serwerowi na zainstalowanie roli funkcji Hyper-V. Trwa to kilka minut, a serwer zostanie automatycznie uruchomiony ponownie.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Krok 2: Tworzenie maszyny wirtualnej problemu na serwerze funkcji Hyper-V z systemem ratowniczej maszyny wirtualnej

1.  Zapisz nazwę dysku w maszynie wirtualnej problemu, a następnie usuń maszynę wirtualną problemu. Upewnij się, że wszystkie dołączone dyski są przechowywane. 

2.  Dołącz dysk systemu operacyjnego maszyny wirtualnej z problemami jako dysk z danymi na maszynie ratowniczej.

    1.  Po usunięciu problemu z maszyną wirtualną przejdź do ratowniczej maszyny wirtualnej.

    2.  Wybierz pozycję **dyski**, a następnie **Dodaj dysk danych**.

    3.  Wybierz dysk maszyny wirtualnej z problemami, a następnie wybierz pozycję **Zapisz**.

3.  Po pomyślnym dołączeniu dysku zdalnego pulpitu do ratowniczej maszyny wirtualnej.

4.  Otwórz przystawkę Zarządzanie dyskami (diskmgmt. msc). Upewnij się, że dysk maszyny wirtualnej problemu jest ustawiony na **tryb offline**.

5.  Otwórz Menedżera funkcji Hyper-V: W **Menedżer serwera**wybierz **rolę funkcji Hyper-V**. Kliknij prawym przyciskiem myszy serwer, a następnie wybierz **Menedżera funkcji Hyper-V**.

6.  W Menedżerze funkcji Hyper-V kliknij prawym przyciskiem myszy ratowniczą maszynę wirtualną, a następnie wybierz pozycję **Nowa** > **maszyna** > wirtualna**dalej**.

7.  Wpisz nazwę maszyny wirtualnej, a następnie wybierz przycisk **dalej**.

8.  Wybierz pozycję **generacja 1**.

9.  Ustaw pamięć początkową o 1024 MB lub więcej.

10. W razie potrzeby wybierz przełącznik sieci funkcji Hyper-V, który został utworzony. W przeciwnym razie przejdź do następnej strony.

11. Wybierz pozycję **Dołącz wirtualny dysk twardy później**.

    ![obraz przedstawiający możliwość późniejszego dołączenia wirtualnego dysku twardego](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Po utworzeniu maszyny wirtualnej wybierz pozycję **Zakończ** .

13. Kliknij prawym przyciskiem myszy utworzoną maszynę wirtualną, a następnie wybierz pozycję **Ustawienia**.

14. Wybierz pozycję **kontroler IDE 0**, wybierz pozycję **dysk twardy**, a następnie kliknij przycisk **Dodaj**.

    ![obraz przedstawiający Dodawanie nowego dysku twardego](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. Na stronie **fizyczny dysk twardy**wybierz dysk maszyny wirtualnej problemu dołączonej do maszyny wirtualnej platformy Azure. Jeśli nie widzisz żadnych dysków na liście, sprawdź, czy dysk jest ustawiony do trybu offline przy użyciu przystawki Zarządzanie dyskami.

    ![obraz dotyczący instalowania dysku](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Wybierz **Zastosuj**, a następnie wybierz pozycję **OK**.

18. Kliknij dwukrotnie maszynę wirtualną, a następnie ją Uruchom.

19. Teraz możesz współpracować z maszyną wirtualną jako lokalną maszynę wirtualną. Można wykonać dowolne kroki rozwiązywania problemów.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Krok 3: Utwórz ponownie maszynę wirtualną platformy Azure na platformie Azure

1.  Po przejściu maszyny wirtualnej w tryb online Zamknij maszynę wirtualną w Menedżerze funkcji Hyper-V.

2.  Przejdź do [Azure Portal](https://portal.azure.com) i wybierz dyski >owej maszyny wirtualnej, skopiuj nazwę dysku. Nazwa zostanie użyta w następnym kroku. Odłącz dysk stały od ratowniczej maszyny wirtualnej.

3.  Przejdź do pozycji **wszystkie zasoby**, wyszukaj nazwę dysku, a następnie wybierz dysk.

     ![obraz przedstawiający wyszukiwanie dysku](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Kliknij pozycję **Utwórz maszynę wirtualną**.

     ![obraz dotyczący tworzenia maszyny wirtualnej na podstawie dysku](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Możesz również użyć Azure PowerShell, aby utworzyć maszynę wirtualną na podstawie dysku. Aby uzyskać więcej informacji, zobacz [Tworzenie nowej maszyny wirtualnej na podstawie istniejącego dysku przy użyciu programu PowerShell](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami RDP z maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md). Problemy dotyczące uzyskiwania dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć w temacie Rozwiązywanie problemów z [łącznością aplikacji na maszynie wirtualnej z systemem Windows](troubleshoot-app-connection.md).
