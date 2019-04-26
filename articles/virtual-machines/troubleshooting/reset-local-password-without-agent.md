---
title: Resetowanie hasła lokalnego Windows bez agenta platformy Azure | Dokumentacja firmy Microsoft
description: Jak zresetować hasło konta użytkownika lokalnego Windows, gdy agent gościa platformy Azure nie jest zainstalowana lub działający na maszynie Wirtualnej
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6b77ceb2ab9abe232cec75254b30ce37c3dbbf60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307728"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Resetowanie hasła lokalnego Windows maszyny wirtualnej platformy Azure w trybie offline
Możesz zresetować lokalne hasło maszyny Wirtualnej na platformie Azure przy użyciu Windows [witryny Azure portal lub programu Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pod warunkiem jest zainstalowany agent gościa platformy Azure. Ta metoda jest podstawowym sposobem, aby zresetować hasło dla maszyny Wirtualnej platformy Azure. Jeśli wystąpią problemy z powodu braku odpowiedzi agenta gościa platformy Azure lub niepowodzenia instalacji po przekazaniu obrazu niestandardowego, można ręcznie zresetować hasło Windows. W tym artykule opisano, jak zresetować hasło do konta lokalnego, dołączając dysk wirtualny źródłowego systemu operacyjnego do innej maszyny Wirtualnej. Kroki opisane w tym artykule nie dotyczą Windows kontrolerów domeny. 

> [!WARNING]
> Ta metoda powinna być używana w ostateczności. Zawsze należy starać się zresetować hasło przy użyciu [witryny Azure portal lub programu Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pierwszy.
> 
> 

## <a name="overview-of-the-process"></a>Omówienie procesu
Kroki core do wykonywania lokalnego resetowania hasła dla maszyny Wirtualnej z systemem Windows na platformie Azure po Brak dostępu do agenta gościa platformy Azure jest następująca:

* Usunąć źródłową maszynę Wirtualną. Dyski wirtualne są zachowywane.
* Dołącz dysk systemu operacyjnego źródłowej maszyny Wirtualnej do innej maszyny Wirtualnej w tej samej lokalizacji, w ramach subskrypcji platformy Azure. Ta maszyna wirtualna nazywa się maszyny Wirtualnej rozwiązywania problemów.
* Za pomocą maszyny Wirtualnej rozwiązywania problemów, Utwórz niektóre pliki konfiguracji na dysku systemu operacyjnego źródłowej maszyny Wirtualnej.
* Odłączanie dysku systemu operacyjnego maszyny Wirtualnej z maszyny Wirtualnej rozwiązywania problemów.
* Szablon usługi Resource Manager umożliwia utworzenie maszyny Wirtualnej za pomocą oryginalnego wirtualnego dysku.
* Po uruchomieniu nowej maszyny Wirtualnej, pliki konfiguracji, które możesz utworzyć aktualizacji hasła użytkownika wymagane.

> [!NOTE]
> Można zautomatyzować następujące procesy:
>
> - Tworzenie maszyny Wirtualnej rozwiązywania problemów
> - Dołączanie dysku systemu operacyjnego
> - Ponowne tworzenie oryginalnej maszyny Wirtualnej
> 
> Aby to zrobić, należy użyć [skrypty odzyskiwania maszyny Wirtualnej platformy Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Jeśli zdecydujesz się używać skryptów odzyskiwania maszyny Wirtualnej platformy Azure, można użyć następującego procesu w sekcji "Szczegółową procedurę":
> 1. Powtórz kroki 1 i 2 za pomocą skryptów można dołączyć dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do maszyny Wirtualnej odzyskiwania.
> 2. Wykonaj kroki 3 – 6, aby zastosować środki zaradcze.
> 3. Pomiń kroki 7 – 9 maja, za pomocą skryptów, aby ponownie utworzyć maszynę Wirtualną.
> 4. Wykonaj kroki 10 i 11.

## <a name="detailed-steps"></a>Szczegółowe procedury

> [!NOTE]
> Kroki nie dotyczą Windows kontrolerów domeny. Działa tylko na serwerze autonomicznym lub serwerze, który jest członkiem domeny.
> 
> 

Zawsze należy starać się zresetować hasło przy użyciu [witryny Azure portal lub programu Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed podjęciem próby następujące kroki. Upewnij się, że masz kopię zapasową maszyny wirtualnej przed rozpoczęciem. 

1. Usuń dotyczy maszyny Wirtualnej w witrynie Azure portal. Usuwanie maszyny Wirtualnej usuwa tylko metadane, odwołanie do maszyny Wirtualnej w ramach platformy Azure. Dyski wirtualne są zachowywane po usunięciu maszyny Wirtualnej:
   
   * Wybierz maszynę Wirtualną w witrynie Azure portal, kliknij przycisk *Usuń*:
     
     ![Usuń istniejącą maszynę Wirtualną](./media/reset-local-password-without-agent/delete_vm.png)
2. Dołącz dysk systemu operacyjnego źródłowej maszyny Wirtualnej do maszyny Wirtualnej rozwiązywania problemów. Maszyny Wirtualnej rozwiązywania problemów musi należeć do tego samego regionu co dysk systemu operacyjnego źródłowej maszyny Wirtualnej (takie jak `West US`):
   
   * Wybierz maszyny Wirtualnej rozwiązywania problemów w witrynie Azure portal. Kliknij przycisk *dysków* | *Dołącz istniejące*:
     
     ![Dołącz istniejący dysk](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Wybierz *pliku wirtualnego dysku twardego* i następnie wybierz konto magazynu, który zawiera źródłowa maszyna wirtualna:
     
     ![Wybieranie konta magazynu](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Wybierz kontener źródła. Kontener źródłowy jest zazwyczaj *wirtualne dyski twarde*:
     
     ![Wybierz kontener magazynu](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Wybierz wirtualny dysk twardy systemu operacyjnego, aby dołączyć. Kliknij przycisk *wybierz* do ukończenia procesu:
     
     ![Wybierz wirtualny dysk źródłowy](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Łączenie z maszyną Wirtualną rozwiązywania problemów, przy użyciu pulpitu zdalnego i upewnij się, że dysk systemu operacyjnego źródłowej maszyny Wirtualnej jest widoczne:
   
   * Wybierz maszyny Wirtualnej rozwiązywania problemów w witrynie Azure portal, a następnie kliknij przycisk *Connect*.
   * Otwórz plik RDP, które pliki do pobrania. Wprowadź nazwę użytkownika i hasło maszyny Wirtualnej rozwiązywania problemów.
   * W Eksploratorze plików Znajdź dysk danych, do którego zostanie dołączony. Źródłowy wirtualny dysk twardy maszyny Wirtualnej jest dysk tylko dane, dołączony do maszyny Wirtualnej rozwiązywania problemów, należy na napęd F::
     
     ![Wyświetl dołączonego dysku danych](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Tworzenie `gpt.ini` w `\Windows\System32\GroupPolicy` na dysku źródłowej maszyny Wirtualnej (jeśli istnieje gpt.ini, Zmień nazwę na gpt.ini.bak):
   
   > [!WARNING]
   > Upewnij się, że nie przypadkowo utworzysz następujące pliki w C:\Windows, dysk systemu operacyjnego dla maszyny Wirtualnej rozwiązywania problemów. Utwórz następujące pliki na dysku systemu operacyjnego dla maszyny Wirtualnej, który jest dołączony jako dysk danych źródła.
   > 
   > 
   
   * Dodaj następujące wiersze do `gpt.ini` utworzony plik:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Create gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Tworzenie `scripts.ini` w `\Windows\System32\GroupPolicy\Machine\Scripts\Startup`. Upewnij się, że są wyświetlane w folderach ukrytych. W razie potrzeby utwórz `Machine` lub `Scripts` folderów.
   
   * Dodaj następujące wiersze `scripts.ini` utworzony plik:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Utwórz scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Tworzenie `FixAzureVM.cmd` w `\Windows\System32` z następującą zawartością, zastępując `<username>` i `<newpassword>` własnymi wartościami:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Podczas definiowania nowego hasła, musi spełniać wymagania co do złożoności haseł skonfigurowanej dla maszyny Wirtualnej.
7. W witrynie Azure portal Odłącz dysk od maszyny Wirtualnej rozwiązywania problemów:
   
   * Wybierz maszyny Wirtualnej rozwiązywania problemów w witrynie Azure portal, kliknij przycisk *dysków*.
   * Dysk danych dołączony w kroku 2, wybierz pozycję *Odłącz*:
     
     ![Odłączanie dysku](./media/reset-local-password-without-agent/detach_disk.png)
8. Przed utworzeniem maszyny Wirtualnej, Uzyskaj identyfikator URI źródłowego dysku systemu operacyjnego:
   
   * Wybierz konto magazynu w witrynie Azure portal, kliknij przycisk *obiektów blob*.
   * Wybierz kontener. Kontener źródłowy jest zazwyczaj *wirtualne dyski twarde*:
     
     ![Wybierz obiekt blob konta magazynu](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Wybierz źródło maszyny Wirtualnej, wirtualny dysk twardy Zawarty, a następnie kliknij przycisk *kopiowania* znajdujący się obok *adresu URL* nazwy:
     
     ![Skopiuj identyfikator URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Utwórz Maszynę wirtualną z dyskiem systemu operacyjnego źródłowej maszyny Wirtualnej:
   
   * Użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) Aby utworzyć Maszynę wirtualną na podstawie wyspecjalizowanego wirtualnego dysku twardego. Kliknij przycisk `Deploy to Azure` przycisk, aby otworzyć portalu Azure przy użyciu szczegółów oparte na szablonach, wypełnione dla Ciebie.
   * Jeśli chcesz zachować wszystkie poprzednie ustawienia dla maszyny Wirtualnej, wybierz opcję *Edytuj szablon* zapewnienie istniejącej sieci wirtualnej, podsieci, karta sieciowa lub publicznego adresu IP.
   * W `OSDISKVHDURI` parametru pola tekstowego, wklej identyfikator URI dysku VHD źródła uzyskać w poprzednim kroku:
     
     ![Tworzenie maszyny Wirtualnej na podstawie szablonu](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Po uruchomieniu nowej maszyny Wirtualnej Połącz z maszyną wirtualną przy użyciu pulpitu zdalnego na nowe hasło określone w `FixAzureVM.cmd` skryptu.
11. W sesji zdalnej programu do nowej maszyny Wirtualnej należy usunąć następujące pliki, aby wyczyścić środowisko:
    
    * Z %windir%\System32
      * remove FixAzureVM.cmd
    * From %windir%\System32\GroupPolicy\Machine\
      * Usuń scripts.ini
    * From %windir%\System32\GroupPolicy
      * Usuń gpt.ini (jeśli gpt.ini istniał wcześniej, a nazwa została zmieniona na gpt.ini.bak, zmiana nazwy pliku bak z powrotem do gpt.ini)

## <a name="next-steps"></a>Kolejne kroki
Jeśli nadal nie możesz połączyć przy użyciu pulpitu zdalnego, zobacz [RDP przewodnik rozwiązywania problemów](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Szczegółowe RDP przewodnik rozwiązywania problemów z](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) patrzy na rozwiązywanie problemów z metody zamiast określonych kroków. Możesz również [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) praktyczne pomocy.

