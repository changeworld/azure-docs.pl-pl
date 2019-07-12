---
title: Używać systemu Linux maszyny Wirtualnej rozwiązywania problemów w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy maszyny wirtualnej systemu Linux, łącząc dysku systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu witryny Azure portal
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: genli
ms.openlocfilehash: 160e45ad5bf83f44bed2314ee5103825e265467c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709393"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Rozwiązywanie problemów z maszyny Wirtualnej z systemem Linux przez dołączenie dysku systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu witryny Azure portal
Linux maszyny wirtualnej (VM) napotkał błąd podczas rozruchu lub dysk, może być konieczne wykonanie kroków rozwiązywania problemów na samym wirtualnym dysku twardym. Typowym przykładem może być nieprawidłowy wpis w `/etc/fstab` że zapobiega maszyny Wirtualnej możliwość wykonania rozruchu pomyślnie. Ten artykuł szczegółowo opisuje sposób użycia witryny Azure portal połączyć wirtualny dysk twardy do innej maszyny Wirtualnej systemu Linux, aby naprawić wszystkie błędy, a następnie ponownie utworzyć oryginalną maszynę Wirtualną.

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuń maszynę Wirtualną napotyka problemy, utrzymując wirtualnych dysków twardych.
2. Dołącz i zainstaluj wirtualny dysk twardy do innej maszyny Wirtualnej systemu Linux na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub uruchom dowolnych narzędzi w celu rozwiązywania problemów na oryginalnym wirtualnym dysku twardym.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz Maszynę wirtualną za pomocą oryginalnego wirtualnego dysku twardego.

Dla maszyny Wirtualnej używającej dysków zarządzanych, zobacz [Rozwiązywanie problemów z dysku zarządzanego maszyny Wirtualnej przez dołączenie dysku systemu operacyjnego nowy](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Określenia problemów rozruchu
Należy sprawdzić diagnostykę rozruchu i zrzut ekranu maszyny Wirtualnej, aby ustalić, dlaczego nie jest poprawnie rozruch maszyny Wirtualnej. Typowym przykładem może być nieprawidłowy wpis w `/etc/fstab`, albo podstawowy wirtualny dysk twardy jest usunięty lub przeniesiony.

Wybierz maszynę Wirtualną w portalu, a następnie przewiń w dół do **pomoc techniczna i rozwiązywanie problemów** sekcji. Kliknij przycisk **diagnostykę rozruchu** do wyświetlania konsoli komunikaty przesyłane strumieniowo ze swojej maszyny Wirtualnej. Przejrzyj dzienniki konsoli, aby zobaczyć, jeśli można określić, dlaczego maszyny Wirtualnej napotkała problem. Poniższy przykład pokazuje, że maszyna wirtualna została zablokowana w trybie konserwacji, wymaga ręcznego interakcji:

![Wyświetlanie maszyny Wirtualnej diagnostyki rozruchu dzienniki konsoli](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Możesz również kliknąć **zrzut ekranu** w górnej części dziennika diagnostyki rozruchu do pobrania Przechwyć zrzut ekranu maszyny Wirtualnej.


## <a name="view-existing-virtual-hard-disk-details"></a>Szczegółowe informacje są istniejącego wirtualnego dysku twardego
Zanim będzie możliwe dołączenie wirtualny dysk twardy do innej maszyny Wirtualnej, należy określić nazwę wirtualnego dysku twardego (VHD). 

Wybierz grupę zasobów z poziomu portalu, a następnie wybierz swoje konto magazynu. Kliknij przycisk **obiektów blob**, jak w poniższym przykładzie:

![Wybierz magazyn obiektów blob](./media/troubleshoot-recovery-disks-portal-linux/storage-account-overview.png)

Zazwyczaj mają kontener o nazwie **wirtualne dyski twarde** który przechowuje Twoje wirtualne dyski twarde. Wybierz kontener, aby wyświetlić listę wirtualnych dysków twardych. Zanotuj nazwę wirtualnego dysku twardego (prefiks jest zazwyczaj Nazwa maszyny Wirtualnej):

![Identyfikowanie dysku VHD w kontenerze magazynu](./media/troubleshoot-recovery-disks-portal-linux/storage-container.png)

Wybierz istniejący wirtualny dysk twardy z listy i skopiuj adres URL do użycia w kolejnych krokach:

![Skopiuj istniejący adres URL wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal-linux/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Usuń istniejącą maszynę Wirtualną
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy jest, gdzie są przechowywane sam system operacyjny, aplikacje i konfiguracje. Samej maszyny Wirtualnej jest po prostu metadanymi, definiują rozmiar lub lokalizację, która odwołuje się do zasobów, takich jak wirtualny dysk twardy lub karty interfejsu sieci wirtualnej (NIC). Każdy wirtualny dysk twardy ma przypisaną dzierżawę, kiedy dołączone do maszyny Wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dalszym ciągu będzie kojarzyła dysk systemu operacyjnego z maszyną Wirtualną, nawet wtedy, gdy ta maszyna wirtualna jest w stanie zatrzymania i ma cofnięty przydział dzierżawy.

Pierwszy krok, aby odzyskać maszynę Wirtualną jest nieusuwanie samego zasobu maszyny Wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny Wirtualnej możesz dołączyć wirtualny dysk twardy do innej maszyny Wirtualnej do rozwiązywania oraz usuwania błędów.

Wybierz maszynę Wirtualną w portalu, a następnie kliknij przycisk **Usuń**:

![Maszyna wirtualna rozruchu zrzut ekranu przedstawiający diagnostykę rozruchu wystąpił błąd](./media/troubleshoot-recovery-disks-portal-linux/stop-delete-vm.png)

Poczekaj, aż maszyny Wirtualnej zostało zakończone, usuwanie, przed dołączeniem wirtualny dysk twardy do innej maszyny Wirtualnej. Dzierżawy dla wirtualnego dysku twardego, który kojarzy ją z maszyny Wirtualnej musi zostać zwolnione, zanim będzie możliwe dołączenie wirtualny dysk twardy do innej maszyny Wirtualnej.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Dołącz istniejący wirtualny dysk twardy do innej maszyny Wirtualnej
W następnych kilku krokach należy użyć innej maszyny Wirtualnej na potrzeby rozwiązywania problemów. Istniejący wirtualny dysk twardy możesz dołączyć do tej maszyny Wirtualnej rozwiązywania problemów, aby można było do przeglądania i edytowania zawartości na dysku. Ten proces umożliwia Popraw wszelkie błędy konfiguracji lub przejrzyj dodatkową aplikację i plikach dziennika systemowego, na przykład. Wybierz lub Utwórz inną maszynę Wirtualną do użycia na potrzeby rozwiązywania problemów.

1. Wybierz grupę zasobów z poziomu portalu, a następnie wybierz maszynę Wirtualną rozwiązywania problemów. Wybierz **dysków** a następnie kliknij przycisk **Dołącz istniejące**:

    ![Dołączanie istniejącego dysku w portalu](./media/troubleshoot-recovery-disks-portal-linux/attach-existing-disk.png)

2. Aby wybrać istniejący wirtualny dysk twardy, kliknij pozycję **Plik VHD**:

    ![Przeglądnie w poszukiwaniu istniejącego dysku VHD](./media/troubleshoot-recovery-disks-portal-linux/select-vhd-location.png)

3. Wybierz swoje konto magazynu i kontener, a następnie kliknij przycisk istniejącego wirtualnego dysku twardego. Kliknij przycisk **wybierz** przycisk, aby potwierdzić wybór:

    ![Wybieranie istniejącego wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal-linux/select-vhd.png)

4. Za pomocą wirtualnego dysku twardego teraz zaznaczone, kliknij przycisk **OK** można dołączyć istniejącego wirtualnego dysku twardego:

    ![Potwierdź dołączenie istniejącego wirtualnego dysku twardego](./media/troubleshoot-recovery-disks-portal-linux/attach-disk-confirm.png)

5. Po kilku sekundach **dysków** okienko dla maszyny Wirtualnej zawiera istniejący wirtualny dysk twardy podłączony jako dysk z danymi:

    ![Istniejący wirtualny dysk twardy dołączony jako dysk danych](./media/troubleshoot-recovery-disks-portal-linux/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Zainstaluj dysk danych dołączony

> [!NOTE]
> Poniższe przykłady zawierają szczegółowe kroki wymagane na maszynie Wirtualnej systemu Ubuntu. Jeśli używasz innego dystrybucja systemu Linux, takie jak Red Hat Enterprise Linux lub SUSE, lokalizacje plików dziennika i `mount` polecenia mogą być nieco inne. Zapoznaj się z dokumentacją dla swojej dystrybucji określonych dla odpowiednie zmiany w poleceniach.

1. SSH z maszyną Wirtualną rozwiązywania problemów, przy użyciu odpowiednich poświadczeń. Jeśli ten dysk jest pierwszy dysk danych dołączony do maszyny Wirtualnej rozwiązywania problemów, prawdopodobnie jest ona połączona z `/dev/sdc`. Użyj `dmseg` do tworzenia listy dołączonych dysków:

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

    W powyższym przykładzie dysk systemu operacyjnego wynosi `/dev/sda` i podane dla każdej maszyny Wirtualnej znajduje się na dysku tymczasowego `/dev/sdb`. Jeśli masz wiele dysków z danymi, powinny one być w `/dev/sdd`, `/dev/sde`i tak dalej.

2. Utwórz katalog do zainstalowania istniejącego wirtualnego dysku twardego. Poniższy przykład tworzy katalog o nazwie `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącego wirtualnego dysku twardego, zainstaluj partycję wymagane. Poniższy przykład instaluje pierwszej partycji podstawowej na `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest instalowanie dysków z danymi na maszynach wirtualnych na platformie Azure przy użyciu powszechnie Unikatowy identyfikator (UUID) wirtualnego dysku twardego. Instalowanie wirtualnego dysku twardego, użyj identyfikator UUID nie jest w tym scenariuszu rozwiązywania problemów z krótkim konieczne. Jednak w trakcie normalnego użytkowania edytowanie `/etc/fstab` zainstalować wirtualne dyski twarde przy użyciu nazwy urządzenia zamiast identyfikatora UUID może spowodować maszynę Wirtualną, aby zakończyć się niepowodzeniem, rozruch.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalnym wirtualnym dysku twardym
Istniejący wirtualny dysk twardy zainstalowany można teraz wykonywać żadnej konserwacji i kroki rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstalowywanie i odłączanie oryginalnego wirtualnego dysku twardego
Gdy błędy są rozwiązywane, odłącz istniejący wirtualny dysk twardy z maszyny Wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z innej maszyny Wirtualnej, dopóki dzierżawa dołączanie wirtualnego dysku twardego do maszyny Wirtualnej rozwiązywania problemów zostanie zwolniona.

1. W sesji SSH dla maszyny Wirtualnej rozwiązywania problemów należy odinstalować istniejącego wirtualnego dysku twardego. Najpierw zmień poza katalog nadrzędny dla punktu instalacji:

    ```bash
    cd /
    ```

    Teraz odinstalować istniejącego wirtualnego dysku twardego. Poniższy przykład umożliwia odinstalowanie urządzenia pod adresem `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz Odłącz wirtualny dysk twardy z maszyny Wirtualnej. Wybierz maszynę Wirtualną w portalu i kliknij przycisk **dysków**. Wybierz istniejący wirtualny dysk twardy, a następnie kliknij przycisk **Odłącz**:

    ![Odłącz istniejący wirtualny dysk twardy](./media/troubleshoot-recovery-disks-portal-linux/detach-disk.png)

    Poczekaj, aż maszyny Wirtualnej ma pomyślne odłączenie dysku danych przed kontynuowaniem.

## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny Wirtualnej na podstawie oryginalnego dysku twardego
Aby utworzyć Maszynę wirtualną z oryginalnego wirtualnego dysku twardego, użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Szablon umożliwia wdrożenie maszyny Wirtualnej w istniejącej sieci wirtualnej przy użyciu adresu URL wirtualnego dysku twardego z wcześniej polecenia. Kliknij przycisk **Wdróż na platformie Azure** przycisk w następujący sposób:

![Wdrażanie maszyny Wirtualnej z szablonu z serwisu GitHub](./media/troubleshoot-recovery-disks-portal-linux/deploy-template-from-github.png)

Szablon jest ładowany do witryny Azure portal dla wdrożenia. Wprowadź nazwy dla nowej maszyny Wirtualnej i istniejące zasoby platformy Azure, a następnie wklej adres URL do istniejącego wirtualnego dysku twardego. Aby rozpocząć wdrożenie, kliknij przycisk **zakupu**:

![Wdrażanie maszyny Wirtualnej z szablonu](./media/troubleshoot-recovery-disks-portal-linux/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Włączyć ponownie diagnostykę rozruchu
Po utworzeniu maszyny Wirtualnej z istniejącego wirtualnego dysku twardego Diagnostyka rozruchu może nie być włączane automatycznie. Aby sprawdzić stan diagnostykę rozruchu i włączyć, jeśli to konieczne, wybierz maszynę Wirtualną w portalu. W obszarze **monitorowanie**, kliknij przycisk **ustawień diagnostycznych**. Upewnij się, stan **na**i znacznik wyboru obok pozycji **diagnostykę rozruchu** jest zaznaczone. Jeśli wprowadzisz jakiekolwiek zmiany, kliknij przycisk **Zapisz**:

![Aktualizowanie ustawień diagnostyki rozruchu](./media/troubleshoot-recovery-disks-portal-linux/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Rozwiązywanie problemów z dysku zarządzanego maszyny Wirtualnej przez dołączenie nowy dysk systemu operacyjnego
1. Zatrzymywanie danej maszyny Wirtualnej.
2. [Tworzenie migawki dysku zarządzanego](../windows/snapshot-copy-managed-disk.md) dysku systemu operacyjnego, dysku zarządzanego maszyny wirtualnej.
3. [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Dołączanie dysku zarządzanego jako dysku danych maszyny wirtualnej](../windows/attach-disk-ps.md).
5. [Zmień dysk z danymi z kroku 4, na dysku systemu operacyjnego](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Następne kroki
Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeń protokołu SSH z Maszyną wirtualną platformy Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [aplikacji Rozwiązywanie problemów z łącznością na maszynie Wirtualnej systemu Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby uzyskać więcej informacji na temat przy użyciu usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
