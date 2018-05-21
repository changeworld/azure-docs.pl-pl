---
title: Używanie okien, rozwiązywanie problemów z maszyny Wirtualnej w portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy dotyczące maszyny wirtualnej systemu Windows na platformie Azure łącząc dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu portalu Azure
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/07/2018
ms.author: genli
ms.openlocfilehash: db6a2279347b5746da706e7ad3629b141afd205b
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Rozwiązywanie problemów z maszyny Wirtualnej systemu Windows, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu portalu Azure
Maszyny wirtualnej systemu Windows (VM) na platformie Azure napotkał błąd podczas rozruchu lub dysk, konieczne może wykonać kroki rozwiązywania problemów na wirtualnym dysku twardym, do samej siebie. Typowym przykładem jest aktualizacja aplikacji nie powiodło się, który uniemożliwia było pomyślnie uruchomić maszynę Wirtualną. W tym artykule szczegółowo sposób łączenia wirtualnego dysku twardego do innej maszyny Wirtualnej systemu Windows, napraw błędy, a następnie ponownie utwórz oryginalnego maszyny Wirtualnej za pomocą portalu Azure.

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuwanie maszyny Wirtualnej, wystąpią problemy, utrzymując wirtualnych dysków twardych.
2. Dołączanie i zainstalować wirtualny dysk twardy do innej maszyny Wirtualnej systemu Windows na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytowanie plików lub uruchamianie narzędzi do rozwiązywania problemów z na oryginalny wirtualny dysk twardy.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz maszynę Wirtualną przy użyciu oryginalny wirtualny dysk twardy.

Dla maszyny Wirtualnej używającej dysków zarządzanych, zobacz [Rozwiązywanie problemów z maszyn wirtualnych dysków zarządzanych przez Trwa dołączanie nowego dysku systemu operacyjnego](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Określenie zagadnień rozruchu
Aby ustalić, dlaczego nie będzie mógł poprawnie rozruchu maszyny Wirtualnej, sprawdź diagnostyki rozruchu zrzut ekranu maszyny Wirtualnej. Typowym przykładem może być aktualizacji aplikacji nie powiodło się lub podstawowy wirtualny dysk twardy jest usunięty lub przeniesiony.

Wybierz maszyny Wirtualnej w portalu, a następnie przewiń w dół do **pomocy technicznej i rozwiązywania problemów** sekcji. Kliknij przycisk **diagnostyki rozruchu** do wyświetlania na zrzucie ekranu. Należy pamiętać, wszystkie określone komunikaty o błędach lub kody błędów, aby ustalić, dlaczego maszyny Wirtualnej napotyka problem. W poniższym przykładzie przedstawiono maszyny Wirtualnej Trwa oczekiwanie na zatrzymanie usługi:

![Wyświetlanie maszyny Wirtualnej diagnostyki rozruchu dzienniki konsoli](./media/troubleshoot-recovery-disks-portal/screenshot-error.png)

Możesz również kliknąć **zrzut ekranu** pobrać przechwytywania zrzut ekranu maszyny Wirtualnej.


## <a name="view-existing-virtual-hard-disk-details"></a>Szczegóły istniejącego wirtualnego dysku twardego
Przed dołączeniem wirtualnego dysku twardego do innej maszyny Wirtualnej, należy określić nazwę wirtualnego dysku twardego (VHD). 

Wybierz grupę zasobów z portalu, a następnie wybierz konto magazynu. Kliknij przycisk **obiekty BLOB**, jak w poniższym przykładzie:

![Wybierz magazyn obiektów blob](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Zwykle mają kontener o nazwie **wirtualne dyski twarde** który przechowuje wirtualnych dysków twardych. Wybierz kontener, aby wyświetlić listę wirtualnych dysków twardych. Zanotuj nazwę dysk VHD (prefiks jest zazwyczaj nazwą maszyny Wirtualnej):

![Określenie dysku VHD w programie kontenera magazynu](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Wybierz istniejącego wirtualnego dysku twardego z listy i skopiuj adres URL do użycia w kolejnych krokach:

![Skopiuj istniejący adres URL wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Usuń istniejącą maszynę Wirtualną
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy jest, gdzie są przechowywane sam system operacyjny, aplikacje i konfiguracje. Samej maszyny Wirtualnej są tylko metadane, który definiuje rozmiaru lub lokalizacji i odwołuje się do zasobów, takich jak wirtualny dysk twardy lub sieć wirtualna karta sieciowa (NIC). Każdy wirtualny dysk twardy ma dzierżawę przypisana w momencie dołączony do maszyny Wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dzierżawy w dalszym ciągu skojarzyć dysk systemu operacyjnego z maszyny Wirtualnej, nawet w przypadku tej maszyny Wirtualnej w stanie zatrzymania i deallocated.

Pierwszy krok w celu odzyskania maszyny Wirtualnej jest usunąć samego zasobu maszyny Wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny Wirtualnej, możesz dołączyć wirtualnego dysku twardego do innej maszyny Wirtualnej do rozwiązywania oraz usuwania błędów.

Wybierz maszyny Wirtualnej w portalu, a następnie kliknij przycisk **usunąć**:

![Maszyna wirtualna rozruchu diagnostyki zrzut ekranu przedstawiający błąd rozruchu](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Poczekaj na Maszynie wirtualnej zakończył, usuwanie przed dołączeniem wirtualnego dysku twardego do innej maszyny Wirtualnej. Dzierżawy na wirtualnym dysku twardym, który kojarzy go z maszyny Wirtualnej musi zostać zwolniony przed dołączeniem wirtualnego dysku twardego do innej maszyny Wirtualnej.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Dołączanie istniejącego wirtualnego dysku twardego do innej maszyny Wirtualnej
Dla następnych kilku krokach możesz użyć innej maszyny Wirtualnej na potrzeby rozwiązywania problemów. Istniejącego wirtualnego dysku twardego należy dołączyć do tej maszyny Wirtualnej dotyczące rozwiązywania problemów, aby móc przeglądać i edytowania zawartości na dysku. Ten proces umożliwia poprawić błędy konfiguracji lub przejrzyj dodatkowe aplikacji lub plików dziennika systemu, np. Wybierz lub Utwórz inną maszynę Wirtualną do użycia na potrzeby rozwiązywania problemów.

1. Wybierz grupę zasobów z portalu, a następnie wybierz rozwiązywania problemów z maszyny Wirtualnej. Wybierz **dysków** , a następnie kliknij przycisk **Attach istniejących**:

    ![Dołączanie istniejącego dysku w portalu](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Aby wybrać istniejący wirtualny dysk twardy, kliknij pozycję **Plik VHD**:

    ![Przeglądnie w poszukiwaniu istniejącego dysku VHD](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Wybierz konto magazynu i kontener, a następnie kliknij istniejący dysk VHD. Kliknij przycisk **wybierz** przycisk, aby potwierdzić wybór:

    ![Wybieranie istniejącego wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Teraz wybrać dysk VHD, kliknij polecenie **OK** można dołączyć istniejącego wirtualnego dysku twardego:

    ![Potwierdź dołączanie istniejącego wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Po kilku sekundach **dysków** okienko dla maszyny Wirtualnej zawiera istniejącego wirtualnego dysku twardego połączenia jako dysk z danymi:

    ![Istniejący wirtualny dysk twardy dołączony jako dysk danych](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Zainstalować dysk dołączonych danych

1. Otwórz połączenie pulpitu zdalnego do maszyny Wirtualnej. Wybierz maszyny Wirtualnej w portalu i kliknij **Connect**. Pobierz i Otwórz plik połączenia RDP. Wprowadź swoje poświadczenia, aby zalogować się do maszyny Wirtualnej w następujący sposób:

    ![Zaloguj się do maszyny Wirtualnej przy użyciu pulpitu zdalnego](./media/troubleshoot-recovery-disks-portal/open-remote-desktop.png)

2. Otwórz **Menedżera serwera**, a następnie wybierz pozycję **usług plików i magazynowania**. 

    ![Wybierz usługi plików i magazynowania w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

3. Dysk danych jest automatycznie wykrywane i dołączony. Aby wyświetlić listę podłączonych dysków, wybierz **dysków**. Możesz wybrać dysku danych, aby wyświetlić informacje o woluminie, wraz z literą dysku. W poniższym przykładzie przedstawiono dysku danych dołączona i przy użyciu **F:**:

    ![Dysk dołączony i informacji o woluminie w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalny wirtualny dysk twardy
Istniejącego wirtualnego dysku twardego zainstalowane można teraz wykonywać żadnych konserwacji i kroki rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstaluj obraz i odłączyć oryginalny wirtualny dysk twardy
Po rozwiązaniu błędy odłączyć istniejącego wirtualnego dysku twardego z rozwiązywania problemów z maszyny Wirtualnej. Nie można użyć wirtualnego dysku twardego z innych maszyn wirtualnych, do czasu zwolnienia dzierżawy dołączanie wirtualnego dysku twardego do rozwiązywania problemów z maszyny Wirtualnej.

1. Z sesji protokołu RDP do maszyny Wirtualnej, należy otworzyć **Menedżera serwera**, a następnie wybierz pozycję **usług plików i magazynowania**:

    ![Wybierz usługi plików i magazynowania w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

2. Wybierz **dysków** , a następnie wybierz dysk danych. Kliknij prawym przyciskiem myszy na dysku danych, a następnie wybierz **Przełącz do trybu Offline**:

    ![W określonych dysk z danymi w Menedżerze serwera w trybie offline](./media/troubleshoot-recovery-disks-portal/server-manager-set-disk-offline.png)

3. Teraz Odłącz wirtualny dysk twardy z maszyny Wirtualnej. Wybierz maszyny Wirtualnej w portalu Azure i kliknij przycisk **dysków**. Wybierz istniejącego wirtualnego dysku twardego, a następnie kliknij przycisk **Detach**:

    ![Odłącz istniejącego wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Poczekaj, aż maszyna wirtualna została pomyślnie odłączono dysk danych przed kontynuowaniem.

## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny Wirtualnej z oryginalny dysk twardy
Aby utworzyć Maszynę wirtualną z oryginalny wirtualny dysk twardy, użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Szablon wdraża maszynę Wirtualną w istniejącej sieci wirtualnej przy użyciu adresu URL wirtualnego dysku twardego starszych polecenia. Kliknij przycisk **wdrażanie na platformie Azure** przycisku w następujący sposób:

![Wdrożenie maszyny Wirtualnej z szablonu z serwisu Github](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Szablon jest ładowany do portalu Azure do wdrożenia. Wprowadź nazwy dla nowej maszyny Wirtualnej i istniejących zasobów platformy Azure i wklej adres URL do istniejącego wirtualnego dysku twardego. Aby rozpocząć wdrażanie, kliknij przycisk **zakupu**:

![Wdrożenie maszyny Wirtualnej z szablonu](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Włączyć ponownie diagnostykę rozruchu
Po utworzeniu maszyny Wirtualnej z istniejącego wirtualnego dysku twardego diagnostyki rozruchu może nie automatycznie włączone. Aby sprawdzić stan diagnostyki rozruchu i włączyć, jeśli to konieczne, wybierz maszyny Wirtualnej w portalu. W obszarze **monitorowanie**, kliknij przycisk **ustawień diagnostycznych**. Upewnij się, stan jest **na**, a znacznik wyboru obok pozycji **diagnostyki rozruchu** jest zaznaczone. Jeśli wprowadzisz zmiany, kliknij przycisk **zapisać**:

![Aktualizowanie ustawień diagnostyki rozruchu](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Rozwiązywanie problemów z maszyn wirtualnych dysków zarządzanych przez Trwa dołączanie nowego dysku systemu operacyjnego
1. Zatrzymaj następujące zarządzane dysku maszyny Wirtualnej systemu Windows.
2. [Tworzenie migawki dysków zarządzanych w](snapshot-copy-managed-disk.md) dysku systemu operacyjnego maszyny wirtualnej dysku zarządzanego.
3. [Tworzenie dysku zarządzanego z migawki](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Dołącz zarządzanych dysku jako dysku danych maszyny wirtualnej](attach-disk-ps.md).
5. [Zmień dysk z danymi z kroku 4 dysk systemu operacyjnego](os-disk-swap.md).

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy dotyczące nawiązywania połączenia z maszyną Wirtualną, zobacz [połączeniami RDP Rozwiązywanie problemów z maszyną wirtualną Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby uzyskać więcej informacji dotyczących korzystania z Menedżera zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
