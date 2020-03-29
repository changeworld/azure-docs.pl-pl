---
title: Używanie maszyny wirtualnej z systemem Linux do rozwiązywania problemów w witrynie Azure portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyną wirtualną systemu Linux, łącząc dysk systemu operacyjnego z maszyną wirtualną odzyskiwania przy użyciu witryny Azure portal
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: e45de5c12f0d93645a0b1253acf8300527cafdbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374645"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Linux, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu witryny Azure portal
Jeśli maszyna wirtualna systemu Linux (VM) napotka błąd rozruchu lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów na samym wirtualnym dysku twardym. Typowym przykładem może być `/etc/fstab` nieprawidłowy wpis, który uniemożliwia maszynie wirtualnej pomyślne uruchomienie. W tym artykule opisano, jak używać witryny Azure Portal do łączenia wirtualnego dysku twardego z inną maszyną wirtualną z systemem Linux w celu naprawienia błędów, a następnie ponownego utworzenia oryginalnej maszyny Wirtualnej.

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj maszynę wirtualną, na które dotyczy problem.
1. Zrób migawkę dla dysku systemu operacyjnego maszyny Wirtualnej.
1. Utwórz wirtualny dysk twardy z migawki.
1. Podłącz i zamontuj wirtualny dysk twardy na innej maszynie wirtualnej systemu Windows w celu rozwiązywania problemów.
1. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub uruchom dowolne narzędzia, aby rozwiązać problemy na oryginalnym wirtualnym dysku twardym.
1. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
1. Zamień dysk systemu operacyjnego na maszynę wirtualną.

> [!NOTE]
> Ten artykuł nie dotyczy maszyny Wirtualnej z dyskiem niezarządzanym.

## <a name="determine-boot-issues"></a>Określanie problemów z rozruchem
Sprawdź diagnostyki rozruchu i zrzut ekranu maszyny Wirtualnej, aby ustalić, dlaczego maszyna wirtualna nie jest w stanie uruchomić poprawnie. Typowym przykładem może być `/etc/fstab`nieprawidłowy wpis w programie lub podstawowy wirtualny dysk twardy, który zostanie usunięty lub przeniesiony.

Wybierz maszynę wirtualną w portalu, a następnie przewiń w dół do sekcji **Pomoc techniczna + Rozwiązywanie problemów.** Kliknij **pozycję Diagnostyka rozruchu,** aby wyświetlić komunikaty konsoli przesyłane strumieniowo z maszyny Wirtualnej. Przejrzyj dzienniki konsoli, aby sprawdzić, czy można ustalić, dlaczego maszyna wirtualna napotyka problem. W poniższym przykładzie przedstawiono maszynę wirtualną zablokowaną w trybie konserwacji, która wymaga ręcznej interakcji:

![Wyświetlanie dzienników konsoli diagnostyki rozruchu maszyny Wirtualnej](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Można również kliknąć **zrzut ekranu** w górnej części dziennika diagnostyki rozruchu, aby pobrać przechwytywanie zrzutu ekranu maszyny Wirtualnej.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tworzenie migawki dysku systemu operacyjnego
Migawka to pełna kopia wirtualnego dysku twardego (VHD) tylko do odczytu. Zaleca się, aby czysto zamknąć maszynę wirtualną przed wykonaniem migawki, aby wyczyścić wszystkie procesy, które są w toku. Aby wykonać migawkę dysku systemu operacyjnego, wykonaj następujące kroki:

1. Przejdź do [witryny Azure portal](https://portal.azure.com). Wybierz **maszyny wirtualne** z paska bocznego, a następnie wybierz maszynę wirtualną, która ma problem.
1. W lewym okienku wybierz pozycję **Dyski**, a następnie wybierz nazwę dysku systemu operacyjnego.
    ![Obraz dotyczący nazwy dysku systemu operacyjnego](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na stronie **Przegląd** dysku systemu operacyjnego wybierz pozycję **Utwórz migawkę**.
1. Utwórz migawkę w tym samym miejscu co dysk systemu operacyjnego.

## <a name="create-a-disk-from-the-snapshot"></a>Tworzenie dysku na podstawie migawki
Aby utworzyć dysk z migawki, wykonaj następujące kroki:

1. Wybierz **powłokę chmury** z witryny Azure portal.

    ![Obraz o otwartej powłoce chmury](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Uruchom następujące polecenia programu PowerShell, aby utworzyć dysk zarządzany z migawki. Należy zastąpić te przykładowe nazwy odpowiednimi nazwami.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Jeśli polecenia zostaną uruchomione pomyślnie, nowy dysk zostanie wyświetlony w podanej grupie zasobów.

## <a name="attach-disk-to-another-vm"></a>Dołączanie dysku do innej maszyny Wirtualnej
W kolejnych kilku krokach należy użyć innej maszyny Wirtualnej do rozwiązywania problemów. Po dołączeniu dysku do maszyny wirtualnej do rozwiązywania problemów można przeglądać i edytować zawartość dysku. Ten proces umożliwia skorygowanie błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu. Aby dołączyć dysk do innej maszyny Wirtualnej, wykonaj następujące kroki:

1. Wybierz grupę zasobów z portalu, a następnie wybierz maszynę wirtualną do rozwiązywania problemów. Wybierz pozycję **Dyski**, wybierz pozycję **Edytuj**, a następnie kliknij pozycję **Dodaj dysk danych:**

    ![Dołączanie istniejącego dysku w portalu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na liście **Dyski danych** wybierz dysk systemu operacyjnego zidentyfikowanej maszyny wirtualnej. Jeśli dysk systemu operacyjnego nie jest widoczny, upewnij się, że rozwiązywanie problemów z maszyną wirtualną i dyskiem systemu operacyjnego znajduje się w tym samym regionie (lokalizacji). 
3. Wybierz **pozycję Zapisz,** aby zastosować zmiany.

## <a name="mount-the-attached-data-disk"></a>Zamontowanie dołączonego dysku danych

> [!NOTE]
> Poniższe przykłady szczegółowo kroki wymagane na maszynie wirtualnej Ubuntu. Jeśli używasz innej dystrybucji Linuksa, takiej jak Red Hat Enterprise Linux `mount` lub SUSE, lokalizacje i polecenia plików dziennika mogą być nieco inne. Odpowiednie zmiany w poleceniach można znaleźć w dokumentacji określonej dystrybucji.

1. SSH do maszyny Wirtualnej rozwiązywania problemów przy użyciu odpowiednich poświadczeń. Jeśli ten dysk jest pierwszym dyskiem danych dołączonym do maszyny `/dev/sdc`wirtualnej do rozwiązywania problemów, prawdopodobnie jest połączony z programem . Służy `dmseg` do listy dołączonych dysków:

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

    W poprzednim przykładzie dysk systemu operacyjnego znajduje się na `/dev/sda` dysku tymczasowym dla każdej maszyny Wirtualnej jest na poziomie `/dev/sdb`. Jeśli masz wiele dysków z danymi, powinny one być na `/dev/sdd`, `/dev/sde`i tak dalej.

2. Utwórz katalog, aby zainstalować istniejący wirtualny dysk twardy. Poniższy przykład tworzy katalog `troubleshootingdisk`o nazwie:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącym wirtualnym dysku twardym, zainstaluj wymaganą partycję. W poniższym przykładzie montuje `/dev/sdc1`się pierwszą partycję podstawową w:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest instalowanie dysków danych na maszynach wirtualnych na platformie Azure przy użyciu uniwersalnego unikatowego identyfikatora (UUID) wirtualnego dysku twardego. W tym krótkim scenariuszu rozwiązywania problemów montaż wirtualnego dysku twardego przy użyciu UUID nie jest konieczne. Jednak w normalnym użyciu `/etc/fstab` edycja do zainstalowania wirtualnych dysków twardych przy użyciu nazwy urządzenia, a nie UUID może spowodować, że maszyna wirtualna nie może uruchomić.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów z oryginalnym wirtualnym dyskiem twardym
Po zamontowaniu istniejącego wirtualnego dysku twardego można teraz wykonać wszelkie czynności konserwacyjne i rozwiązywanie problemów zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odmontuj i odłącz oryginalny wirtualny dysk twardy
Po usunięciu błędów odłącz istniejący wirtualny dysk twardy od maszyny wirtualnej do rozwiązywania problemów. Nie można używać wirtualnego dysku twardego z żadną inną maszyną wirtualną, dopóki nie zostanie zwolniona dzierżawa dołączająca wirtualny dysk twardy do maszyny wirtualnej do rozwiązywania problemów.

1. Od sesji SSH do rozwiązywania problemów z maszyną wirtualną odinstaluj istniejący wirtualny dysk twardy. Najpierw zmień katalog nadrzędny dla punktu instalacji:

    ```bash
    cd /
    ```

    Teraz odinstaluj istniejący wirtualny dysk twardy. Poniższy przykład odinstalowuje `/dev/sdc1`urządzenie w:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz odłącz wirtualny dysk twardy od maszyny Wirtualnej. Wybierz maszynę wirtualną w portalu i kliknij pozycję **Dyski**. Wybierz istniejący wirtualny dysk twardy, a następnie kliknij przycisk **Odłącz**:

    ![Odłącz istniejące wirtualne dyski twarde](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Poczekaj, aż maszyna wirtualna pomyślnie odłączy dysk danych przed kontynuowaniem.

## <a name="swap-the-os-disk-for-the-vm"></a>Zamiana dysku systemu operacyjnego na maszynę wirtualną

Usługa Azure portal obsługuje teraz zmianę dysku systemu operacyjnego maszyny Wirtualnej. W tym celu wykonaj następujące kroki:

1. Przejdź do [witryny Azure portal](https://portal.azure.com). Wybierz **maszyny wirtualne** z paska bocznego, a następnie wybierz maszynę wirtualną, która ma problem.
1. W lewym okienku wybierz pozycję **Dyski**, a następnie wybierz pozycję **Zamień dysk systemu operacyjnego**.
        ![Obraz dotyczący dysku swap os w witrynie Azure portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Wybierz nowy naprawiony dysk, a następnie wpisz nazwę maszyny Wirtualnej, aby potwierdzić zmianę. Jeśli dysk nie jest widoczny na liście, odczekaj 10 ~ 15 minut po odłączeniu dysku od maszyny Wirtualnej do rozwiązywania problemów. Upewnij się również, że dysk znajduje się w tym samym miejscu co maszyna wirtualna.
1. Wybierz przycisk OK.

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z połączeniem się z maszyną [wirtualną, zobacz Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure.](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby uzyskać problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej z systemem Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby uzyskać więcej informacji na temat korzystania z Menedżera zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
