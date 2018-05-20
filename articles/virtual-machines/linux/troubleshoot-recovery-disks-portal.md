---
title: Użyj Linux, rozwiązywanie problemów z maszyny Wirtualnej w portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy dotyczące maszyny wirtualnej systemu Linux łącząc dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu portalu Azure
services: virtual-machines-linux
documentationCenter: ''
authors: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: iainfou
ms.openlocfilehash: 89c4c5c986375177918f14417c6b5a9a24925908
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Rozwiązywanie problemów z maszyny Wirtualnej systemu Linux, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu portalu Azure
Maszyny wirtualnej systemu Linux (VM) napotkał błąd podczas rozruchu lub dysk, należy wykonać kroki rozwiązywania problemów na wirtualnym dysku twardym, sam. Typowym przykładem może być nieprawidłowy wpis w `/etc/fstab` , w związku z było pomyślnie uruchomić maszynę Wirtualną. W tym artykule szczegółowo sposób łączenia wirtualnego dysku twardego do innej maszyny Wirtualnej systemu Linux, napraw błędy, a następnie ponownie utwórz oryginalnego maszyny Wirtualnej za pomocą portalu Azure.

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuwanie maszyny Wirtualnej, wystąpią problemy, utrzymując wirtualnych dysków twardych.
2. Dołączanie i zainstalować wirtualny dysk twardy do innej maszyny Wirtualnej systemu Linux na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytowanie plików lub uruchamianie narzędzi do rozwiązywania problemów z na oryginalny wirtualny dysk twardy.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz maszynę Wirtualną przy użyciu oryginalny wirtualny dysk twardy.

Dla maszyny Wirtualnej używającej dysków zarządzanych, zobacz [Rozwiązywanie problemów z maszyn wirtualnych dysków zarządzanych przez Trwa dołączanie nowego dysku systemu operacyjnego](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Określenie zagadnień rozruchu
Sprawdź, czy diagnostyki rozruchu i zrzut ekranu maszyny Wirtualnej w celu ustalenia, dlaczego nie będzie mógł poprawnie rozruchu maszyny Wirtualnej. Typowym przykładem może być nieprawidłowy wpis w `/etc/fstab`, lub podstawowej wirtualnego dysku twardego jest usunięty lub przeniesiony.

Wybierz maszyny Wirtualnej w portalu, a następnie przewiń w dół do **pomocy technicznej i rozwiązywania problemów** sekcji. Kliknij przycisk **diagnostyki rozruchu** wyświetlanie komunikatów konsoli strumieniowo z maszyny Wirtualnej. Przejrzyj dzienniki konsoli, aby zobaczyć, jeśli można określić, dlaczego napotyka problem maszyny Wirtualnej. W poniższym przykładzie pokazano, że zablokował maszyny Wirtualnej w trybie konserwacji, który wymaga interakcji ręczne:

![Wyświetlanie maszyny Wirtualnej diagnostyki rozruchu dzienniki konsoli](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

Możesz również kliknąć **zrzut ekranu** w górnej części dziennika diagnostyki rozruchu, aby pobrać przechwytywania zrzut ekranu maszyny Wirtualnej.


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

> [!NOTE]
> Poniższe przykłady szczegółowo opisano kroki wymagane w przypadku maszyny Wirtualnej systemu Ubuntu. Jeśli używasz różnych distro Linux, takich jak Red Hat Enterprise Linux i SUSE, lokalizacje plików dziennika i `mount` poleceń mogą być nieco inne. Zapoznaj się z dokumentacją dotyczącą Twojej distro określonych dla odpowiednie zmiany w poleceniach.

1. SSH do rozwiązywania problemów z maszyny Wirtualnej przy użyciu odpowiednich poświadczeń. Jeśli dysk jest pierwszym dysku danych dołączona do rozwiązywania problemów z maszyny Wirtualnej, prawdopodobnie jest on podłączony do `/dev/sdc`. Użyj `dmseg` do listy dołączonych dysków:

    ```bash
    dmesg | grep SCSI
    ```
    Dane wyjściowe są podobne do poniższego przykładu:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    W powyższym przykładzie dysk systemu operacyjnego jest w `/dev/sda` i dysku tymczasowym podana dla każdej maszyny Wirtualnej jest w `/dev/sdb`. Jeśli masz wiele dysków z danymi, należy je na `/dev/sdd`, `/dev/sde`i tak dalej.

2. Utwórz katalog do zainstalowania istniejącego wirtualnego dysku twardego. Poniższy przykład tworzy katalog o nazwie `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącego wirtualnego dysku twardego, zainstaluj wymagane partycji. Poniższy przykład instaluje pierwszej partycji podstawowej na `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest instalowanie dysków z danymi na maszynach wirtualnych na platformie Azure przy użyciu unikatowym identyfikatorem wirtualnego dysku twardego (UUID). Instalowanie wirtualnego dysku twardego za pomocą identyfikator UUID nie jest w tym scenariuszu rozwiązywania problemów z krótkim konieczne. Jednak w ramach normalnego użytkowania edycji `/etc/fstab` można zainstalować wirtualnych dysków twardych przy użyciu nazwy urządzenia zamiast identyfikatora UUID może spowodować niepowodzenie do rozruchu maszyny Wirtualnej.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalny wirtualny dysk twardy
Istniejącego wirtualnego dysku twardego zainstalowane można teraz wykonywać żadnych konserwacji i kroki rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstaluj obraz i odłączyć oryginalny wirtualny dysk twardy
Po rozwiązaniu błędy odłączyć istniejącego wirtualnego dysku twardego z rozwiązywania problemów z maszyny Wirtualnej. Nie można użyć wirtualnego dysku twardego z innych maszyn wirtualnych, do czasu zwolnienia dzierżawy dołączanie wirtualnego dysku twardego do rozwiązywania problemów z maszyny Wirtualnej.

1. W sesji SSH do rozwiązywania problemów z maszyny Wirtualnej należy odinstalować istniejącego wirtualnego dysku twardego. Najpierw zmienić poza katalogu nadrzędnego punktu instalacji:

    ```bash
    cd /
    ```

    Teraz należy odinstalować istniejącego wirtualnego dysku twardego. Poniższy przykład odinstalowuje urządzenia pod adresem `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz Odłącz wirtualny dysk twardy z maszyny Wirtualnej. Wybierz maszyny Wirtualnej w portalu i kliknij **dysków**. Wybierz istniejącego wirtualnego dysku twardego, a następnie kliknij przycisk **Detach**:

    ![Odłącz istniejącego wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Poczekaj, aż maszyna wirtualna została pomyślnie odłączono dysk danych przed kontynuowaniem.

## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny Wirtualnej z oryginalny dysk twardy
Aby utworzyć Maszynę wirtualną z oryginalny wirtualny dysk twardy, użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Szablon wdraża maszynę Wirtualną w istniejącej sieci wirtualnej przy użyciu adresu URL wirtualnego dysku twardego starszych polecenia. Kliknij przycisk **wdrażanie na platformie Azure** przycisku w następujący sposób:

![Wdrożenie maszyny Wirtualnej z szablonu z serwisu GitHub](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Szablon jest ładowany do portalu Azure do wdrożenia. Wprowadź nazwy dla nowej maszyny Wirtualnej i istniejących zasobów platformy Azure i wklej adres URL do istniejącego wirtualnego dysku twardego. Aby rozpocząć wdrażanie, kliknij przycisk **zakupu**:

![Wdrożenie maszyny Wirtualnej z szablonu](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Włączyć ponownie diagnostykę rozruchu
Po utworzeniu maszyny Wirtualnej z istniejącego wirtualnego dysku twardego diagnostyki rozruchu może nie automatycznie włączone. Aby sprawdzić stan diagnostyki rozruchu i włączyć, jeśli to konieczne, wybierz maszyny Wirtualnej w portalu. W obszarze **monitorowanie**, kliknij przycisk **ustawień diagnostycznych**. Upewnij się, stan jest **na**, a znacznik wyboru obok pozycji **diagnostyki rozruchu** jest zaznaczone. Jeśli wprowadzisz zmiany, kliknij przycisk **zapisać**:

![Aktualizowanie ustawień diagnostyki rozruchu](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Rozwiązywanie problemów z maszyn wirtualnych dysków zarządzanych przez Trwa dołączanie nowego dysku systemu operacyjnego
1. Zatrzymaj następujące zarządzane dysku maszyny Wirtualnej systemu Windows.
2. [Tworzenie migawki dysków zarządzanych w](../windows/snapshot-copy-managed-disk.md) dysku systemu operacyjnego maszyny wirtualnej dysku zarządzanego.
3. [Tworzenie dysku zarządzanego z migawki](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Dołącz zarządzanych dysku jako dysku danych maszyny wirtualnej](../windows/attach-disk-ps.md).
5. [Zmień dysk z danymi z kroku 4 dysk systemu operacyjnego](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy dotyczące nawiązywania połączenia z maszyną Wirtualną, zobacz [połączeń Rozwiązywanie problemów z SSH z maszyny Wirtualnej platformy Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej systemu Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby uzyskać więcej informacji dotyczących korzystania z Menedżera zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
