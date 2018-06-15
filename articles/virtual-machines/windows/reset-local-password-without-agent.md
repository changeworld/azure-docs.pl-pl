---
title: Zresetować lokalne hasło systemu Windows bez agenta platformy Azure | Dokumentacja firmy Microsoft
description: Jak można zresetować hasła lokalnego konta użytkownika systemu Windows, gdy agent gościa Azure nie jest zainstalowany i działa na maszynie Wirtualnej
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: iainfou
ms.openlocfilehash: ad892aee646b1a5f8c96d5bdeca24b7a0d88f38e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915609"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Zresetować lokalne hasło systemu Windows dla maszyny Wirtualnej platformy Azure w trybie offline
Można zresetować lokalne hasło systemu Windows maszyny wirtualnej platformy Azure przy użyciu [portalu Azure lub programu Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pod warunkiem jest zainstalowany agent gościa platformy Azure. Ta metoda jest podstawową metodą resetowania hasła dla maszyny Wirtualnej platformy Azure. Jeśli wystąpią problemy z agentem Azure gościa nie odpowiada lub błąd instalacji po przekazaniu niestandardowego obrazu, można ręcznie zresetować hasło systemu Windows. Ten artykuł zawiera szczegóły dotyczące resetowania hasła konta lokalnego przez dołączenie dysku wirtualnego źródłowego systemu operacyjnego do innej maszyny Wirtualnej. Kroki opisane w tym artykule nie dotyczą kontrolery domeny systemu Windows. 

> [!WARNING]
> Ten proces można używać tylko w ostateczności. Zawsze próbować resetowania hasła przy użyciu [portalu Azure lub programu Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pierwszy.
> 
> 

## <a name="overview-of-the-process"></a>Omówienie procesu
Kroków core lokalnego resetowania hasła dla maszyny Wirtualnej systemu Windows na platformie Azure po Brak dostępu do agenta gościa Azure wygląda następująco:

* Usuń źródłowej maszyny Wirtualnej. Dyski wirtualne są zachowywane.
* Dołączenie dysku systemu operacyjnego źródłowej maszyny Wirtualnej do innej maszyny Wirtualnej na tej samej lokalizacji, w ramach Twojej subskrypcji platformy Azure. Tej maszyny Wirtualnej jest określana jako rozwiązywania problemów z maszyny Wirtualnej.
* Za pomocą rozwiązywania problemów z maszyny Wirtualnej, Utwórz niektóre pliki konfiguracji na dysku systemu operacyjnego źródłowej maszyny Wirtualnej.
* Odłączanie dysku systemu operacyjnego maszyny Wirtualnej z maszyny Wirtualnej, rozwiązywania problemów.
* Umożliwia utworzenie maszyny Wirtualnej za pomocą oryginalny wirtualny dysk szablonu usługi Resource Manager.
* Po rozruchu nowej maszyny Wirtualnej, pliki konfiguracji, które możesz utworzyć zaktualizować hasło użytkownika wymagane.

## <a name="detailed-steps"></a>Szczegółowe procedury

> [!NOTE]
> Kroki nie dotyczą kontrolery domeny systemu Windows. Tylko działa na serwerze autonomicznym lub serwerze, który jest członkiem domeny.
> 
> 

Zawsze próbować resetowania hasła przy użyciu [portalu Azure lub programu Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed podjęciem próby następujące kroki. Upewnij się, że masz kopię zapasową maszyny wirtualnej, przed rozpoczęciem. 

1. Usuń dotyczy maszyny Wirtualnej w portalu Azure. Usuwanie maszyny Wirtualnej usuwa tylko metadane odwołania maszyny wirtualnej w systemie Azure. Dyski wirtualne są zachowywane po usunięciu maszyny Wirtualnej:
   
   * Wybierz maszynę Wirtualną w portalu Azure, kliknij przycisk *usunąć*:
     
     ![Usuń istniejącą maszynę Wirtualną](./media/reset-local-password-without-agent/delete_vm.png)
2. Dołączenie dysku systemu operacyjnego źródłowej maszyny Wirtualnej do rozwiązywania problemów z maszyny Wirtualnej. Rozwiązywania problemów z maszyny Wirtualnej musi być w tym samym regionie co dysk systemu operacyjnego źródłowej maszyny Wirtualnej (takie jak `West US`):
   
   * Wybierz rozwiązywania problemów z maszyny Wirtualnej w portalu Azure. Kliknij przycisk *dysków* | *Attach istniejących*:
     
     ![Dołączanie istniejącego dysku](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Wybierz *pliku VHD* , a następnie wybierz konto magazynu, która zawiera źródło maszyny Wirtualnej:
     
     ![Wybieranie konta magazynu](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Wybierz kontener źródła. Kontener źródła jest zwykle *wirtualne dyski twarde*:
     
     ![Wybierz kontener magazynu](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Wybierz wirtualny dysk twardy systemu operacyjnego do dołączenia. Kliknij przycisk *wybierz* aby ukończyć proces:
     
     ![Wybierz wirtualny dysk źródłowy](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Połączenie z rozwiązywania problemów z maszyną Wirtualną przy użyciu pulpitu zdalnego i upewnij się, że widoczny jest dysk systemu operacyjnego źródłowej maszyny Wirtualnej:
   
   * Wybierz rozwiązywania problemów z maszyny Wirtualnej w portalu Azure i kliknij przycisk *Connect*.
   * Otwórz plik RDP, który pobiera. Wprowadź nazwę użytkownika i hasło, rozwiązywania problemów z maszyny wirtualnej.
   * W Eksploratorze plików poszukaj podłączony dysk z danymi. Źródło wirtualnego dysku twardego maszyny Wirtualnej jest dysk z danymi tylko dołączona do rozwiązywania problemów z maszyny Wirtualnej, należy na dysku F::
     
     ![Wyświetlanie dysków dołączonych danych](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Utwórz `gpt.ini` w `\Windows\System32\GroupPolicy` na dysku maszyny Wirtualnej źródłowego (jeśli istnieje gpt.ini, Zmień nazwę na gpt.ini.bak):
   
   > [!WARNING]
   > Upewnij się, że nie przypadkowo utworzysz następujące pliki w C:\Windows, dysk systemu operacyjnego dla maszyny Wirtualnej dotyczące rozwiązywania problemów. Utwórz następujące pliki na dysku systemu operacyjnego dla maszyny Wirtualnej, który jest dołączony jako dysk danych źródła.
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
5. Utwórz `scripts.ini` w `\Windows\System32\GroupPolicy\Machine\Scripts`. Upewnij się, że są wyświetlane w folderach ukrytych. W razie potrzeby utwórz `Machine` lub `Scripts` folderów.
   
   * Dodaj następujące wiersze `scripts.ini` utworzony plik:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Utwórz scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Utwórz `FixAzureVM.cmd` w `\Windows\System32` z następującą zawartość, zastępując `<username>` i `<newpassword>` z własne wartości:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Podczas definiowania nowego hasła, musi spełniać wymagania co do złożoności haseł skonfigurowane dla maszyny Wirtualnej.
7. W portalu Azure Odłącz dysk od maszyny Wirtualnej rozwiązywania problemów:
   
   * Wybierz rozwiązywania problemów z maszyny Wirtualnej w portalu Azure, kliknij przycisk *dysków*.
   * Wybierz dysk z danymi dołączona w kroku 2, kliknij przycisk *Detach*:
     
     ![Odłączanie dysku](./media/reset-local-password-without-agent/detach_disk.png)
8. Przed utworzeniem maszyny Wirtualnej, należy uzyskać identyfikator URI do źródłowego dysku systemu operacyjnego:
   
   * Wybierz konto magazynu w portalu Azure, kliknij przycisk *obiekty BLOB*.
   * Wybierz kontener. Kontener źródła jest zwykle *wirtualne dyski twarde*:
     
     ![Wybierz obiekt blob z konta magazynu](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Wybierz źródło wirtualnego dysku twardego systemu operacyjnego maszyny Wirtualnej, a następnie kliknij przycisk *kopiowania* znajdujący się obok *adres URL* nazwy:
     
     ![Skopiuj identyfikator URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Utwórz maszynę Wirtualną z dyskiem systemu operacyjnego źródłowej maszyny Wirtualnej:
   
   * Użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) Aby utworzyć Maszynę wirtualną z specjalne wirtualnego dysku twardego. Kliknij przycisk `Deploy to Azure` przycisk, aby otworzyć Azure portal z szablonem szczegóły wypełnione.
   * Jeśli chcesz zachować wszystkie poprzednie ustawienia dla maszyny Wirtualnej, wybierz *Edytuj szablon* zapewnienie istniejącej sieci wirtualnej, podsieci, karty sieciowej lub publicznego adresu IP.
   * W `OSDISKVHDURI` parametru pola tekstowego, Wklej Uzyskaj identyfikator URI dysku VHD źródła w poprzednim kroku:
     
     ![Utwórz maszynę Wirtualną z szablonu](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Po nowa maszyna wirtualna jest uruchomiona, połączenie z maszyną Wirtualną przy użyciu pulpitu zdalnego, podając nowe hasło określone w `FixAzureVM.cmd` skryptu.
11. Z sesji zdalnej do nowej maszyny Wirtualnej należy usunąć następujące pliki, aby wyczyścić środowisko:
    
    * Z %windir%\System32
      * remove FixAzureVM.cmd
    * From %windir%\System32\GroupPolicy\Machine\
      * Usuń scripts.ini
    * From %windir%\System32\GroupPolicy
      * Usuń gpt.ini (jeśli istniał gpt.ini wcześniej, i zostanie zmieniona na gpt.ini.bak, Zmień nazwę pliku bak z powrotem do gpt.ini)

## <a name="next-steps"></a>Kolejne kroki
Jeśli nadal nie możesz połączyć przy użyciu pulpitu zdalnego, zobacz [przewodnik rozwiązywania problemów RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Szczegółowy przewodnik rozwiązywania problemów RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) analizuje Rozwiązywanie problemów z metody zamiast wykonania określonych kroków. Możesz również [otwarcia żądania pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) praktyczne pomocy.

