---
title: Resetowanie lokalnego hasła systemu Windows bez agenta platformy Azure | Microsoft Docs
description: Jak zresetować hasło lokalnego konta użytkownika systemu Windows, gdy Agent gościa platformy Azure nie jest zainstalowany lub działa na maszynie wirtualnej
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 5354ebc8c25125f86a0208382d176c84372cadc1
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369866"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Resetowanie lokalnego hasła systemu Windows dla maszyny wirtualnej platformy Azure w trybie offline
Możesz zresetować lokalne hasło systemu Windows maszyny wirtualnej na platformie Azure przy użyciu [Azure Portal lub Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pod warunkiem, że zainstalowano agenta gościa platformy Azure. Ta metoda jest podstawowym sposobem resetowania hasła dla maszyny wirtualnej platformy Azure. Jeśli wystąpią problemy z agentem gościa platformy Azure, który nie odpowiada, lub instalacja nie powiedzie się po przesłaniu obrazu niestandardowego, można ręcznie zresetować hasło systemu Windows. W tym artykule szczegółowo opisano sposób resetowania hasła do konta lokalnego przez dołączenie źródłowego dysku wirtualnego systemu operacyjnego do innej maszyny wirtualnej. Kroki opisane w tym artykule nie dotyczą kontrolerów domeny systemu Windows. 

> [!WARNING]
> Ta metoda powinna być używana w ostateczności. Zawsze należy próbować zresetować hasło przy użyciu [Azure Portal lub Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

## <a name="overview-of-the-process"></a>Przegląd procesu
Podstawowe kroki służące do przeprowadzania lokalnego resetowania hasła dla maszyny wirtualnej z systemem Windows na platformie Azure, gdy nie ma dostępu do agenta gościa platformy Azure:

1. Usuń źródłową maszynę wirtualną. Dyski wirtualne są zachowywane.

2. Dołącz dysk systemu operacyjnego źródłowej maszyny wirtualnej do innej maszyny wirtualnej w tej samej lokalizacji w ramach subskrypcji platformy Azure. Ta maszyna wirtualna jest określana jako Rozwiązywanie problemów z maszyną wirtualną.

3. Za pomocą rozwiązywania problemów z maszyną wirtualną Utwórz pliki konfiguracji na dysku systemu operacyjnego źródłowej maszyny wirtualnej.

4. Odłącz dysk systemu operacyjnego maszyny wirtualnej z maszyny wirtualnej rozwiązywania problemów.

5. Użyj szablonu Menedżer zasobów, aby utworzyć maszynę wirtualną przy użyciu oryginalnego dysku wirtualnego.

6. Podczas uruchamiania nowej maszyny wirtualnej pliki konfiguracyjne, które tworzysz, zaktualizują hasło wymaganego użytkownika.

> [!NOTE]
> Można zautomatyzować następujące procesy:
>
> - Tworzenie maszyny wirtualnej rozwiązywania problemów
> - Dołączanie dysku systemu operacyjnego
> - Ponowne tworzenie oryginalnej maszyny wirtualnej
> 
> W tym celu należy użyć [skryptów odzyskiwania maszyny wirtualnej platformy Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Jeśli zdecydujesz się użyć skryptów odzyskiwania maszyny wirtualnej platformy Azure, możesz użyć następującego procesu w sekcji "szczegółowe kroki":
> 1. Pomiń kroki 1 i 2 przy użyciu skryptów, aby dołączyć dysk systemu operacyjnego objętej maszyny wirtualnej do maszyny wirtualnej odzyskiwania.
> 2. Wykonaj kroki od 3 do 6, aby zastosować środki zaradcze.
> 3. Pomiń kroki 7 – 9 przy użyciu skryptów w celu odbudowania maszyny wirtualnej.
> 4. Wykonaj kroki 10 i 11.

## <a name="detailed-steps-for-resource-manager"></a>Szczegółowe instrukcje dotyczące Menedżer zasobów

> [!NOTE]
> Kroki nie dotyczą kontrolerów domeny systemu Windows. Działa tylko na serwerze autonomicznym lub serwerze, który jest członkiem domeny.

Przed podjęciem próby wykonania poniższych kroków zawsze należy spróbować zresetować hasło przy użyciu [Azure Portal lub Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Przed rozpoczęciem upewnij się, że masz kopię zapasową maszyny wirtualnej. 

1. Usuń zaatakowaną maszynę wirtualną w Azure Portal. Usunięcie maszyny wirtualnej powoduje usunięcie tylko metadanych, odwołania do maszyny wirtualnej na platformie Azure. Dyski wirtualne są zachowywane po usunięciu maszyny wirtualnej:
   
   * Wybierz maszynę wirtualną w Azure Portal, a następnie kliknij pozycję *Usuń*:
     
     ![Usuń istniejącą maszynę wirtualną](./media/reset-local-password-without-agent/delete-vm.png)

2. Dołącz dysk systemu operacyjnego źródłowej maszyny wirtualnej do rozwiązywania problemów z maszyną wirtualną. Maszyna wirtualna rozwiązywania problemów musi znajdować się w tym samym regionie co dysk systemu operacyjnego źródłowej `West US`maszyny wirtualnej (np.):
   
   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal. Kliknij pozycję *dyski* | *Dołącz istniejące*:
     
     ![Dołącz istniejący dysk](./media/reset-local-password-without-agent/disks-attach-existing.png)
     
   2. Wybierz pozycję *plik VHD* , a następnie wybierz konto magazynu, które zawiera ŹRÓDŁową maszynę wirtualną:
     
     ![Wybierz konto magazynu](./media/reset-local-password-without-agent/disks-select-storage-account.png)
     
   3. Wybierz kontener źródłowy. Kontener źródłowy jest zazwyczaj *dyskami VHD*:
     
     ![Wybieranie kontenera magazynu](./media/reset-local-password-without-agent/disks-select-container.png)
     
   4. Wybierz wirtualny dysk twardy systemu operacyjnego do dołączenia. Kliknij przycisk *Wybierz* , aby zakończyć proces:
     
     ![Wybierz źródłowy dysk wirtualny](./media/reset-local-password-without-agent/disks-select-source-vhd.png)

3. Połącz się z maszyną wirtualną rozwiązywania problemów przy użyciu Pulpit zdalny i upewnij się, że dysk systemu operacyjnego źródłowej maszyny wirtualnej jest widoczny:
   
   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal i kliknij przycisk *Połącz*.

   2. Otwórz pobrany plik RDP. Wprowadź nazwę użytkownika i hasło do rozwiązywania problemów z maszyną wirtualną.

   3. W Eksploratorze plików Wyszukaj dołączony dysk danych. Jeśli wirtualny dysk twardy maszyny wirtualnej jest jedynym dyskiem danych dołączonym do maszyny wirtualnej rozwiązywania problemów, powinien to być dysk F:
     
     ![Wyświetl dołączony dysk danych](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer.png)

4. Utwórz `gpt.ini`na dysku źródłowej maszyny wirtualnej (jeśli istnieje tabela GPT. ini, Zmień nazwę na GPT. ini. bak): `\Windows\System32\GroupPolicy`
   
   > [!WARNING]
   > Upewnij się, że nie utworzysz przypadkowo następujących plików w folderze C:\Windows, dysk systemu operacyjnego do rozwiązywania problemów z maszyną wirtualną. Utwórz następujące pliki na dysku systemu operacyjnego dla źródłowej maszyny wirtualnej, która jest dołączana jako dysk danych.
   
   * Dodaj następujące wiersze do `gpt.ini` utworzonego pliku:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Create gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

5. Utwórz `scripts.ini` w `\Windows\System32\GroupPolicy\Machines\Scripts\`. Upewnij się, że ukryte foldery są wyświetlane. W `Machine` razie konieczności Utwórz foldery lub `Scripts` .
   
   * Dodaj następujący wiersz `scripts.ini` utworzonego pliku:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Utwórz skrypt. ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

6. Utwórz `FixAzureVM.cmd` w `\Windows\System32` programie, używając następującej zawartości, `<username>` zastępując `<newpassword>` i korzystając z własnych wartości:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Podczas definiowania nowego hasła należy spełnić skonfigurowane wymagania dotyczące złożoności hasła dla maszyny wirtualnej.

7. W Azure Portal Odłącz dysk od maszyny wirtualnej rozwiązywania problemów:
   
   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal, kliknij pozycję *dyski*.

   2. Wybierz dysk danych dołączony w kroku 2, kliknij przycisk *Odłącz*:
     
     ![Odłączanie dysku](./media/reset-local-password-without-agent/detach-disk.png)

8. Przed utworzeniem maszyny wirtualnej należy uzyskać identyfikator URI na źródłowym dysku systemu operacyjnego:
   
   1. Wybierz konto magazynu w Azure Portal kliknij pozycję *obiekty blob*.

   2. Wybierz kontener. Kontener źródłowy jest zazwyczaj *dyskami VHD*:
     
     ![Wybierz obiekt BLOB konta magazynu](./media/reset-local-password-without-agent/select-storage-details.png)
     
   3. Wybierz źródłowy wirtualny dysk twardy systemu operacyjnego, a następnie kliknij przycisk *kopiowania* obok nazwy *adresu URL* :
     
     ![Kopiuj identyfikator URI dysku](./media/reset-local-password-without-agent/copy-source-vhd-uri.png)

9. Utwórz maszynę wirtualną na podstawie dysku systemu operacyjnego źródłowej maszyny wirtualnej:
   
   1. Użyj [tego szablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) , aby utworzyć maszynę wirtualną na podstawie wyspecjalizowanego wirtualnego dysku twardego. Kliknij ten `Deploy to Azure` przycisk, aby otworzyć Azure Portal z wypełnionymi szczegółami z szablonu.

   2. Jeśli chcesz zachować wszystkie poprzednie ustawienia dla maszyny wirtualnej, wybierz pozycję *Edytuj szablon* , aby podać istniejącą sieć wirtualną, podsieci, kartę sieciową lub publiczny adres IP.

   3. W polu tekstowym parametr wklej identyfikator URI źródłowego wirtualnego dysku twardego w poprzednim kroku: `OSDISKVHDURI`
     
     ![Tworzenie maszyny wirtualnej na podstawie szablonu](./media/reset-local-password-without-agent/create-new-vm-from-template.png)

10. Po uruchomieniu nowej maszyny wirtualnej Nawiąż połączenie z maszyną wirtualną przy użyciu pulpit zdalny z nowym hasłem określonym w `FixAzureVM.cmd` skrypcie.

11. Z sesji zdalnej na nową maszynę wirtualną Usuń następujące pliki, aby wyczyścić środowisko:
    
    * Z%windir%\System32
      * remove FixAzureVM.cmd
    * Z%windir%\System32\GroupPolicy\Machine\Scripts
      * Usuń skrypty. ini
    * From %windir%\System32\GroupPolicy
      * Usuń plik GPT. ini (Jeśli plik GPT. ini istniał wcześniej i zmieniono jego nazwę na GPT. ini. bak, Zmień nazwę pliku. bak z powrotem na GPT. ini)

## <a name="detailed-steps-for-classic-vm"></a>Szczegółowe kroki dla klasycznej maszyny wirtualnej

> [!NOTE]
> Kroki nie dotyczą kontrolerów domeny systemu Windows. Działa tylko na serwerze autonomicznym lub serwerze, który jest członkiem domeny.

Przed podjęciem próby wykonania poniższych kroków zawsze należy spróbować zresetować hasło przy użyciu [Azure Portal lub Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) . Przed rozpoczęciem upewnij się, że masz kopię zapasową maszyny wirtualnej. 

1. Usuń zaatakowaną maszynę wirtualną w Azure Portal. Usunięcie maszyny wirtualnej powoduje usunięcie tylko metadanych, odwołania do maszyny wirtualnej na platformie Azure. Dyski wirtualne są zachowywane po usunięciu maszyny wirtualnej:
   
   * Wybierz maszynę wirtualną w Azure Portal, a następnie kliknij pozycję *Usuń*:
     
     ![Usuń istniejącą maszynę wirtualną](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Dołącz dysk systemu operacyjnego źródłowej maszyny wirtualnej do rozwiązywania problemów z maszyną wirtualną. Maszyna wirtualna rozwiązywania problemów musi znajdować się w tym samym regionie co dysk systemu operacyjnego źródłowej `West US`maszyny wirtualnej (np.):
   
   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal. Kliknij pozycję *dyski* | *Dołącz istniejące*:
     
      ![Dołącz istniejący dysk](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Wybierz pozycję *plik VHD* , a następnie wybierz konto magazynu, które zawiera ŹRÓDŁową maszynę wirtualną:
     
      ![Wybierz konto magazynu](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Zaznacz pole wyboru *Pokaż klasyczne konta magazynu*, a następnie wybierz kontener źródłowy. Kontener źródłowy jest zazwyczaj *dyskami VHD*:
     
      ![Wybieranie kontenera magazynu](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Wybieranie kontenera magazynu](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Wybierz wirtualny dysk twardy systemu operacyjnego do dołączenia. Kliknij przycisk *Wybierz* , aby zakończyć proces:
     
      ![Wybierz źródłowy dysk wirtualny](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Kliknij przycisk OK, aby dołączyć dysk

      ![Dołącz istniejący dysk](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Połącz się z maszyną wirtualną rozwiązywania problemów przy użyciu Pulpit zdalny i upewnij się, że dysk systemu operacyjnego źródłowej maszyny wirtualnej jest widoczny:

   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal i kliknij przycisk *Połącz*.

   2. Otwórz pobrany plik RDP. Wprowadź nazwę użytkownika i hasło do rozwiązywania problemów z maszyną wirtualną.

   3. W Eksploratorze plików Wyszukaj dołączony dysk danych. Jeśli wirtualny dysk twardy maszyny wirtualnej jest jedynym dyskiem danych dołączonym do maszyny wirtualnej rozwiązywania problemów, powinien to być dysk F:
     
      ![Wyświetl dołączony dysk danych](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Utwórz na dysku źródłowej maszyny wirtualnej (Jeśli `gpt.ini` istnieje, Zmień nazwę na `gpt.ini.bak`): `gpt.ini` `\Windows\System32\GroupPolicy`
   
   > [!WARNING]
   > Upewnij się, że nie utworzysz przypadkowo następujących plików w programie `C:\Windows`, dysk systemu operacyjnego do rozwiązywania problemów z maszyną wirtualną. Utwórz następujące pliki na dysku systemu operacyjnego dla źródłowej maszyny wirtualnej, która jest dołączana jako dysk danych.
   
   * Dodaj następujące wiersze do `gpt.ini` utworzonego pliku:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Create gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Utwórz `scripts.ini` w `\Windows\System32\GroupPolicy\Machines\Scripts\`. Upewnij się, że ukryte foldery są wyświetlane. W `Machine` razie konieczności Utwórz foldery lub `Scripts` .
   
   * Dodaj następujący wiersz `scripts.ini` utworzonego pliku:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Utwórz skrypt. ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. Utwórz `FixAzureVM.cmd` w `\Windows\System32` programie, używając następującej zawartości, `<username>` zastępując `<newpassword>` i korzystając z własnych wartości:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Podczas definiowania nowego hasła należy spełnić skonfigurowane wymagania dotyczące złożoności hasła dla maszyny wirtualnej.

7. W Azure Portal Odłącz dysk od maszyny wirtualnej rozwiązywania problemów:
   
   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal, kliknij pozycję *dyski*.
   
   2. Wybierz dysk danych dołączony w kroku 2, kliknij przycisk *Odłącz:* , a następnie kliknij przycisk *OK*.

     ![Odłączanie dysku](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Odłączanie dysku](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Utwórz maszynę wirtualną na podstawie dysku systemu operacyjnego źródłowej maszyny wirtualnej:
   
     ![Tworzenie maszyny wirtualnej na podstawie szablonu](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Tworzenie maszyny wirtualnej na podstawie szablonu](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Tworzenie maszyny wirtualnej na podstawie szablonu](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Ukończ środowisko tworzenia maszyny wirtualnej

1. Po uruchomieniu nowej maszyny wirtualnej Nawiąż połączenie z maszyną wirtualną przy użyciu pulpit zdalny z nowym hasłem określonym w `FixAzureVM.cmd` skrypcie.

2. Z sesji zdalnej na nową maszynę wirtualną Usuń następujące pliki, aby wyczyścić środowisko:
    
    * Wniosek`%windir%\System32`
      * usuwa`FixAzureVM.cmd`
    * Wniosek`%windir%\System32\GroupPolicy\Machine\Scripts`
      * usuwa`scripts.ini`
    * Wniosek`%windir%\System32\GroupPolicy`
      * Usuń `gpt.ini` (Jeśli `gpt.ini` istniały wcześniej i zmieniono jego nazwę na `gpt.ini.bak`, Zmień nazwę `.bak` pliku z powrotem na `gpt.ini`)

## <a name="next-steps"></a>Kolejne kroki
Jeśli nadal nie można nawiązać połączenia przy użyciu Pulpit zdalny, zobacz [Przewodnik rozwiązywania problemów](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)z protokołem RDP. [Szczegółowy przewodnik rozwiązywania problemów](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) z PROTOKOŁem RDP dotyczy metod rozwiązywania problemów, a nie konkretnych kroków. Możesz również [otworzyć żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) , aby uzyskać pomoc techniczną.
