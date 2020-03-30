---
title: Korzystanie z maszyny wirtualnej systemu Windows w witrynie Azure portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyną wirtualną systemu Windows na platformie Azure, łącząc dysk systemu operacyjnego z maszyną wirtualną odzyskiwania przy użyciu portalu Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249999"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Rozwiązywanie problemów z maszyną wirtualną systemu Windows, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu portalu Azure
Jeśli maszyna wirtualna systemu Windows (VM) na platformie Azure napotka błąd rozruchu lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów na samym wirtualnym dysku twardym. Typowym przykładem może być nieudana aktualizacja aplikacji, która uniemożliwia maszynie wirtualnej pomyślne uruchomienie. W tym artykule opisano, jak używać witryny Azure Portal do łączenia wirtualnego dysku twardego z inną maszyną wirtualną systemu Windows w celu naprawienia błędów, a następnie ponownego utworzenia oryginalnej maszyny Wirtualnej. 

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj maszynę wirtualną, na które dotyczy problem.
1. Utwórz migawkę dysku systemu operacyjnego maszyny Wirtualnej.
1. Utwórz wirtualny dysk twardy z migawki.
1. Podłącz i zamontuj wirtualny dysk twardy na innej maszynie wirtualnej systemu Windows w celu rozwiązywania problemów.
1. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub uruchom dowolne narzędzia, aby rozwiązać problemy na oryginalnym wirtualnym dysku twardym.
1. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
1. Zamień dysk systemu operacyjnego na maszynę wirtualną.

> [!NOTE]
> Ten artykuł nie dotyczy maszyny Wirtualnej z dyskiem niezarządzanym.

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
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
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

## <a name="attach-the-disk-to-another-vm"></a>Dołączanie dysku do innej maszyny Wirtualnej
W kolejnych kilku krokach należy użyć innej maszyny Wirtualnej do rozwiązywania problemów. Po dołączeniu dysku do maszyny wirtualnej do rozwiązywania problemów można przeglądać i edytować zawartość dysku. Ten proces umożliwia skorygowanie błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu. Aby dołączyć dysk do innej maszyny Wirtualnej, wykonaj następujące kroki:

1. Wybierz grupę zasobów z portalu, a następnie wybierz maszynę wirtualną do rozwiązywania problemów. Wybierz pozycję **Dyski**, wybierz pozycję **Edytuj**, a następnie kliknij pozycję **Dodaj dysk danych:**

    ![Dołączanie istniejącego dysku w portalu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na liście **Dyski danych** wybierz dysk systemu operacyjnego zidentyfikowanej maszyny wirtualnej. Jeśli dysk systemu operacyjnego nie jest widoczny, upewnij się, że rozwiązywanie problemów z maszyną wirtualną i dyskiem systemu operacyjnego znajduje się w tym samym regionie (lokalizacji). 
3. Wybierz **pozycję Zapisz,** aby zastosować zmiany.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Zamontuj dołączony dysk danych na maszynie wirtualnej

1. Otwórz połączenie pulpitu zdalnego na maszynie wirtualnej, która rozwiązuje problemy. 
2. W rozwiązywaniu problemów z maszyną wirtualną otwórz **Menedżera serwera**, a następnie wybierz pozycję Usługi plików **i magazynowania**. 

    ![Wybieranie usług plików i magazynowania w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Dysk danych jest automatycznie wykrywany i dołączany. Aby wyświetlić listę podłączonych dysków, wybierz pozycję **Dyski**. Można wybrać dysk danych, aby wyświetlić informacje o woluminie, w tym literę dysku. Poniższy przykład przedstawia dysk danych dołączony i **używający F:**:

    ![Informacje o dysku i woluminie w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów z oryginalnym wirtualnym dyskiem twardym
Po zamontowaniu istniejącego wirtualnego dysku twardego można teraz wykonać wszelkie czynności konserwacyjne i rozwiązywanie problemów zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odmontuj i odłącz oryginalny wirtualny dysk twardy
Po usunięciu błędów odłącz istniejący wirtualny dysk twardy od maszyny wirtualnej do rozwiązywania problemów. Nie można używać wirtualnego dysku twardego z żadną inną maszyną wirtualną, dopóki nie zostanie zwolniona dzierżawa dołączająca wirtualny dysk twardy do maszyny wirtualnej do rozwiązywania problemów.

1. Od sesji RDP do maszyny Wirtualnej otwórz **Menedżera serwera,** a następnie wybierz pozycję **Usługi plików i magazynu:**

    ![Wybieranie usług plików i magazynowania w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Wybierz **pozycję Dyski,** a następnie wybierz dysk danych. Kliknij prawym przyciskiem myszy dysk z danymi i wybierz pozycję **Przejmij w tryb offline:**

    ![Ustawianie dysku danych jako trybu offline w Menedżerze serwera](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Teraz odłącz wirtualny dysk twardy od maszyny Wirtualnej. Wybierz maszynę wirtualną w witrynie Azure portal i kliknij pozycję **Dyski**. 
4. Wybierz **pozycję Edytuj**, wybierz dołączony dysk systemu operacyjnego, a następnie kliknij pozycję **Odłącz**:

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
Jeśli masz problemy z połączeniem się z maszyną [wirtualną, zobacz Rozwiązywanie problemów z połączeniami protokołu RDP z maszyną wirtualną platformy Azure.](troubleshoot-rdp-connection.md) Aby uzyskać problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej systemu Windows](troubleshoot-app-connection.md).

Aby uzyskać więcej informacji na temat korzystania z Menedżera zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md).


