---
title: Resetowanie lokalnego hasła systemu Windows bez agenta platformy Azure | Dokumenty firmy Microsoft
description: Jak zresetować hasło lokalnego konta użytkownika systemu Windows, gdy agent gościa platformy Azure nie jest zainstalowany lub działa na maszynie Wirtualnej
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: becbf88aeda164f7d916cbc1f1ace89262cc1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921627"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Resetowanie lokalnego hasła systemu Windows dla maszyny wirtualnej platformy Azure w trybie offline
Można zresetować lokalne hasło systemu Windows maszyny Wirtualnej na platformie Azure przy użyciu [witryny Azure portal lub programu Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pod warunkiem zainstalowania agenta gościa platformy Azure. Ta metoda jest podstawowym sposobem resetowania hasła dla maszyny Wirtualnej platformy Azure. Jeśli wystąpią problemy z agentem gościa platformy Azure nie odpowiada lub nie można zainstalować po przekazaniu obrazu niestandardowego, można ręcznie zresetować hasło systemu Windows. W tym artykule opisano sposób resetowania hasła konta lokalnego przez dołączenie źródłowego dysku wirtualnego systemu operacyjnego do innej maszyny Wirtualnej. Kroki opisane w tym artykule nie dotyczą kontrolerów domeny systemu Windows. 

> [!WARNING]
> Ta metoda powinna być używana w ostateczności. Zawsze najpierw próbuj zresetować hasło przy użyciu [witryny Azure portal lub programu Azure PowerShell.](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="overview-of-the-process"></a>Przegląd procesu
Podstawowe kroki wykonywania lokalnego resetowania hasła dla maszyny Wirtualnej systemu Windows na platformie Azure, gdy nie ma dostępu do agenta gościa platformy Azure jest następujący:

1. Zatrzymaj maszynę wirtualną, na które dotyczy problem.
1. Utwórz migawkę dysku systemu operacyjnego maszyny Wirtualnej.
1. Utwórz kopię dysku systemu operacyjnego z migawki.
1. Dołącz i zamontuj skopiowany dysk systemu operacyjnego na innej maszynie wirtualnej systemu Windows, a następnie utwórz kilka plików konfiguracyjnych na dysku. Pliki pomogą Ci zresetować hasło.
1. Odmontuj i odłącz skopiowany dysk systemu operacyjnego z maszyny wirtualnej do rozwiązywania problemów.
1. Zamień dysk systemu operacyjnego na maszynę wirtualną, którego dotyczy problem.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Szczegółowe kroki wdrażania maszyny Wirtualnej z Menedżerem zasobów

> [!NOTE]
> Kroki nie dotyczą kontrolerów domeny systemu Windows. Działa tylko na serwerze autonomicznym lub serwerze, który jest członkiem domeny.

Zawsze spróbuj zresetować hasło przy użyciu [witryny Azure portal lub programu Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed wypróbowaniem następujących kroków. Przed rozpoczęciem upewnij się, że masz kopię zapasową maszyny Wirtualnej.

1. Zrób migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, utwórz dysk z migawki, a następnie dołącz dysk do maszyny wirtualnej w celu rozwiązania problemu. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną systemu Windows, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu portalu Azure](troubleshoot-recovery-disks-portal-windows.md).
2. Połącz się z maszyną wirtualną do rozwiązywania problemów za pomocą pulpitu zdalnego.
3. `gpt.ini` Utwórz `\Windows\System32\GroupPolicy` na dysku źródłowej maszyny Wirtualnej (jeśli gpt.ini istnieje, zmień nazwę na gpt.ini.bak):
   
   > [!WARNING]
   > Upewnij się, że nie przypadkowo utworzyć następujące pliki w C:\Windows, dysku systemu operacyjnego dla rozwiązywania problemów z maszyną wirtualną. Utwórz następujące pliki na dysku systemu operacyjnego dla źródłowej maszyny Wirtualnej, która jest dołączona jako dysk danych.
   
   * Dodaj następujące wiersze `gpt.ini` do utworzonego pliku:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Tworzenie pliku gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. `scripts.ini` Utwórz `\Windows\System32\GroupPolicy\Machines\Scripts\`w pliku . Upewnij się, że są wyświetlane ukryte foldery. W razie potrzeby `Machine` `Scripts` utwórz foldery lub foldery.
   
   * Dodaj następujące wiersze utworzonego `scripts.ini` pliku:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Tworzenie pliku scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. `FixAzureVM.cmd` Utwórz `\Windows\System32` z następującą `<username>` zawartością, zastępując i `<newpassword>` z własnymi wartościami:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Tworzenie pliku FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Podczas definiowania nowego hasła należy spełnić wymagania dotyczące złożoności skonfigurowanych haseł dla maszyny Wirtualnej.

6. W witrynie Azure portal odłączyć dysk od rozwiązywania problemów z maszyną wirtualną.

7. [Zmień dysk systemu operacyjnego dla maszyny wirtualnej, którego dotyczy problem](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Po uruchomieniu nowej maszyny Wirtualnej połącz się z maszyną wirtualną `FixAzureVM.cmd` przy użyciu pulpitu zdalnego przy użyciu nowego hasła określonego w skrypcie.

9. Z sesji zdalnej do nowej maszyny Wirtualnej usuń następujące pliki, aby oczyścić środowisko:
    
    * Od %windir%\System32
      * usuń FixAzureVM.cmd
    * Z %windir%\System32\GroupPolicy\Machine\Skrypty
      * usuwanie pliku scripts.ini
    * Od %windir%\System32\GroupPolicy
      * usuń gpt.ini (jeśli gpt.ini istniało wcześniej, a zmieniłeś nazwę na gpt.ini.bak, zmień nazwę pliku .bak z powrotem na gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Szczegółowe kroki dla klasycznej maszyny Wirtualnej

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Kroki nie dotyczą kontrolerów domeny systemu Windows. Działa tylko na serwerze autonomicznym lub serwerze, który jest członkiem domeny.

Zawsze spróbuj zresetować hasło przy użyciu [witryny Azure portal lub programu Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) przed wypróbowaniem następujących kroków. Przed rozpoczęciem upewnij się, że masz kopię zapasową maszyny Wirtualnej. 

1. Usuń maszynę wirtualną, którego dotyczy problem, w witrynie Azure portal. Usunięcie maszyny Wirtualnej usuwa tylko metadane, odwołanie do maszyny Wirtualnej na platformie Azure. Dyski wirtualne są zachowywane po usunięciu maszyny Wirtualnej:
   
   * Wybierz maszynę wirtualną w witrynie Azure portal, a następnie kliknij przycisk *Usuń:*
     
     ![Usuwanie istniejącej maszyny wirtualnej](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Podłącz dysk systemu operacyjnego źródłowej maszyny Wirtualnej do maszyny wirtualnej do rozwiązywania problemów z maszyną wirtualną. Maszyna wirtualna do rozwiązywania problemów musi znajdować się w tym samym `West US`regionie co dysk systemu operacyjnego źródłowej maszyny Wirtualnej (np.
   
   1. Wybierz maszynę wirtualną rozwiązywania problemów w witrynie Azure portal. Kliknij *pozycję Dyski* | *Dołącz istniejące:*
     
      ![Dołączanie istniejącego dysku](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Wybierz *plik VHD,* a następnie wybierz konto magazynu, które zawiera źródło maszyny Wirtualnej:
     
      ![Wybieranie konta magazynu](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Zaznacz pole wyboru *Pokaż klasyczne konta magazynu,* a następnie wybierz kontener źródłowy. Kontener źródłowy jest zazwyczaj *vhds:*
     
      ![Wybierz kontener magazynowy](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Wybierz kontener magazynowy](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Wybierz system operacyjny vhd do dołączenia. Kliknij *przycisk Wybierz,* aby zakończyć proces:
     
      ![Wybierz źródłowy dysk wirtualny](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Kliknij przycisk Ok, aby dołączyć dysk

      ![Dołączanie istniejącego dysku](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Połącz się z maszyną wirtualną, która rozwiązujesz problemy za pomocą pulpitu zdalnego i upewnij się, że dysk systemu operacyjnego źródłowej maszyny Wirtualnej jest widoczny:

   1. Wybierz maszynę wirtualną w witrynie Azure portal i kliknij przycisk *Połącz*.

   2. Otwórz pobierany plik RDP. Wprowadź nazwę użytkownika i hasło maszyny wirtualnej do rozwiązywania problemów.

   3. W Eksploratorze plików poszukaj dołączonego dysku danych. Jeśli dysk VHD źródłowej maszyny Wirtualnej jest jedynym dyskiem danych dołączonym do maszyny wirtualnej do rozwiązywania problemów, powinien to być dysk F::
     
      ![Wyświetlanie dołączonego dysku danych](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. `gpt.ini` Utwórz `\Windows\System32\GroupPolicy` na dysku źródłowej maszyny `gpt.ini` Wirtualnej (jeśli `gpt.ini.bak`istnieje, zmień nazwę na):
   
   > [!WARNING]
   > Upewnij się, że nie przypadkowo utworzysz następujące pliki w `C:\Windows`, dysku systemu operacyjnego dla rozwiązywania problemów z maszyną wirtualną. Utwórz następujące pliki na dysku systemu operacyjnego dla źródłowej maszyny Wirtualnej, która jest dołączona jako dysk danych.
   
   * Dodaj następujące wiersze `gpt.ini` do utworzonego pliku:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Tworzenie pliku gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. `scripts.ini` Utwórz `\Windows\System32\GroupPolicy\Machines\Scripts\`w pliku . Upewnij się, że są wyświetlane ukryte foldery. W razie potrzeby `Machine` `Scripts` utwórz foldery lub foldery.
   
   * Dodaj następujące wiersze utworzonego `scripts.ini` pliku:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Tworzenie pliku scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `FixAzureVM.cmd` Utwórz `\Windows\System32` z następującą `<username>` zawartością, zastępując i `<newpassword>` z własnymi wartościami:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Tworzenie pliku FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Podczas definiowania nowego hasła należy spełnić wymagania dotyczące złożoności skonfigurowanych haseł dla maszyny Wirtualnej.

7. W witrynie Azure portal odłączyć dysk od rozwiązywania problemów z maszyną wirtualną:
   
   1. Wybierz maszynę wirtualną w witrynie Azure portal, kliknij pozycję *Dyski*.
   
   2. Wybierz dysk danych dołączony w kroku 2, kliknij przycisk **Odłącz**, a następnie kliknij przycisk **OK**.

     ![Odłączyć dysk](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Odłączyć dysk](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Utwórz maszynę wirtualną na dysku źródłowego systemu operacyjnego maszyny Wirtualnej:
   
     ![Tworzenie maszyny wirtualnej na podstawie szablonu](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Tworzenie maszyny wirtualnej na podstawie szablonu](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Tworzenie maszyny wirtualnej na podstawie szablonu](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Ukończ środowisko Tworzenie maszyny wirtualnej

1. Po uruchomieniu nowej maszyny Wirtualnej połącz się z maszyną wirtualną `FixAzureVM.cmd` przy użyciu pulpitu zdalnego przy użyciu nowego hasła określonego w skrypcie.

2. Z sesji zdalnej do nowej maszyny Wirtualnej usuń następujące pliki, aby oczyścić środowisko:
    
    * Z`%windir%\System32`
      * Usunąć`FixAzureVM.cmd`
    * Z`%windir%\System32\GroupPolicy\Machine\Scripts`
      * Usunąć`scripts.ini`
    * Z`%windir%\System32\GroupPolicy`
      * remove `gpt.ini` `gpt.ini` (jeśli istniał wcześniej, i zmieniono jego `.bak` nazwę na `gpt.ini` `gpt.ini.bak`, zmień nazwę pliku z powrotem na )

## <a name="next-steps"></a>Następne kroki
Jeśli nadal nie można połączyć się za pomocą pulpitu zdalnego, zobacz [przewodnik rozwiązywania problemów z prok.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Szczegółowy [przewodnik rozwiązywania problemów z prow.](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Można również [otworzyć żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) dla praktycznej pomocy.
