---
title: Rozwiązywanie problemów z problemem netvsc.sys podczas zdalnego łączenia się z maszyną wirtualną systemu Windows 10 lub Windows Server 2016 na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem z proksem RDP związanym z usługą netsvc.sys podczas łączenia się z maszyną wirtualną systemu Windows 10 lub Windows Server 2016 na platformie Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057982"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nie można połączyć się zdalnie z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 na platformie Azure z powodu pliku netvsc.sys

W tym artykule wyjaśniono, jak rozwiązać problem, w którym nie ma połączenia sieciowego podczas łączenia się z maszyną wirtualną centrum danych systemu Windows 10 lub Windows Server 2016 na hoście programu Hyper-V Server 2016.

## <a name="symptoms"></a>Objawy

Nie można połączyć się z maszyną wirtualną systemu Azure Windows 10 lub Windows Server 2016 przy użyciu protokołu RDP (Remote Desktop Protocol). W [diagnostyce rozruchu](boot-diagnostics.md)na ekranie jest wyświetlany czerwony krzyżyk nad kartą interfejsu sieciowego (NIC). Oznacza to, że maszyna wirtualna nie ma łączności po pełnym załadowaniu systemu operacyjnego.

Zazwyczaj ten problem występuje w kompilacji systemu Windows [14393](https://support.microsoft.com/help/4093120/) i [kompilacji 15063](https://support.microsoft.com/help/4015583/). Jeśli wersja systemu operacyjnego jest późniejsza niż te wersje, ten artykuł nie ma zastosowania do scenariusza. Aby sprawdzić wersję systemu, otwórz sesję CMD w [funkcji Konsoli dostępu szeregowego,](serial-console-windows.md)a następnie uruchom **program Ver**.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli wersja zainstalowanego pliku systemowego netvsc.sys jest **10.0.14393.594** lub **10.0.15063.0**. Te wersje netvsc.sys może uniemożliwić system interakcji z platformą Azure.


## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, [należy wykonać migawkę dysku systemowego](../windows/snapshot-copy-managed-disk.md) maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby rozwiązać ten problem, użyj konsoli szeregowej lub [napraw maszynę wirtualną w trybie offline,](#repair-the-vm-offline) dołączając dysk systemowy maszyny Wirtualnej do maszyny Wirtualnej odzyskiwania.


### <a name="use-the-serial-console"></a>Korzystanie z konsoli szeregowej

Połącz się [z konsolą szeregową, otwórz wystąpienie programu PowerShell,](serial-console-windows.md)a następnie wykonaj następujące kroki.

> [!NOTE]
> Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, przejdź do sekcji naprawy maszyny [Wirtualnej w trybie offline.](#repair-the-vm-offline)

1. Uruchom następujące polecenie w wystąpieniu programu PowerShell, aby uzyskać wersję pliku (**c:\windows\system32\drivers\netvsc.sys):**

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Pobierz odpowiednią aktualizację na nowy lub istniejący dysk danych dołączony do działającej maszyny Wirtualnej z tego samego regionu:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) lub późniejsza aktualizacja
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) lub późniejsza aktualizacja

3. Odłącz dysk narzędzia od działającej maszyny Wirtualnej, a następnie podłącz go do uszkodzonej maszyny Wirtualnej.

4. Uruchom następujące polecenie, aby zainstalować aktualizację na maszynie Wirtualnej:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Uruchom ponownie maszynę wirtualną.

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

1. [Podłącz dysk systemowy do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).

2. Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.

3. Upewnij się, że dysk jest oflagowany jako **w trybie online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemowego.

4. Utwórz kopię folderu **\Windows\System32\config** w przypadku konieczności wycofania zmian.

5. Na maszynie wirtualnej ratownictwa uruchom Edytor rejestru (regedit.exe).

6. Wybierz klawisz **HKEY_LOCAL_MACHINE,** a następnie z menu wybierz polecenie**Gałąź obciążenia** **pliku.** > 

7. Zlokalizuj plik SYSTEM w folderze **\Windows\System32\config.**

8. Wybierz **otwórz**, wpisz **BROKENSYSTEM** dla nazwy, rozwiń **HKEY_LOCAL_MACHINE** klawisz, a następnie znajdź dodatkowy klucz o nazwie **BROKENSYSTEM**.

9. Przejdź do następującej lokalizacji:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. W każdym podkluczu (takim jak 0000) sprawdź wartość **DriverDesc** wyświetlaną jako **karta sieciowa Microsoft HYPER-V**.

11. W podkluczzie sprawdź **DriverVersion** wartość, która jest wersją sterownika karty sieciowej maszyny Wirtualnej.

12. Pobierz odpowiednią aktualizację:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) lub późniejsza aktualizacja
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) lub późniejsza aktualizacja

13. Podłącz dysk systemowy jako dysk danych na maszynie wirtualnej ratunkowej, na której można pobrać aktualizację.

14. Uruchom następujące polecenie, aby zainstalować aktualizację na maszynie Wirtualnej:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Uruchom następujące polecenie, aby odinstalować gałęzie:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Odłącz dysk systemowy i ponownie utwórz maszynę wirtualną](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną platformy Azure,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.
