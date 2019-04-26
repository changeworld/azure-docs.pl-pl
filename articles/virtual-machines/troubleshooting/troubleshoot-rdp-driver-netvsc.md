---
title: Rozwiązywanie problemów z problemem netvsc.sys podczas nawiązywania połączenia zdalnego systemu Windows 10 lub Windows Server 2016 w maszynie Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy związane z netsvc.sys RDP wystawiania, kiedy użytkownik nawiązywania połączenia z systemem Windows 10 lub Windows Server 2016 w maszynie Wirtualnej na platformie Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: e6685a5e77d92bb9e05ab9578e48c99e80a64b74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362258"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nie można zdalnie połączyć się z systemem Windows 10 lub Windows Server 2016 VM in Azure z netvsc.sys

W tym artykule wyjaśniono, jak rozwiązać problem, w których jest braku połączenia sieciowego podczas łączenia z systemem Windows 10 lub Windows Server 2016 Datacenter maszynę wirtualną (VM) na hoście funkcji Hyper-V Server 2016.

## <a name="symptoms"></a>Objawy

Nie można nawiązać Azure Windows 10 lub maszyny Wirtualnej systemu Windows Server 2016, za pomocą protokołu RDP (Remote Desktop). W [diagnostykę rozruchu](boot-diagnostics.md), ekran pokazuje czerwony krzyżyk w karcie interfejsu sieciowego (NIC). Oznacza to, że maszyna wirtualna nie ma łączności po w pełni załadowany system operacyjny.

Zazwyczaj ten problem występuje w Windows [kompilacji 14393](https://support.microsoft.com/help/4093120/) i [kompilacji 15063](https://support.microsoft.com/help/4015583/). Jeśli wersja systemu operacyjnego jest późniejsza niż te wersje, w tym artykule nie ma zastosowania do danego scenariusza. Aby sprawdzić wersję systemu, otwórz sesję CMD w [funkcji konsoli szeregowej dostępu](serial-console-windows.md), a następnie uruchom **Ver**.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli jest wersja pliku systemowego zainstalowanych netvsc.sys **10.0.14393.594** lub **10.0.15063.0**. Te wersje netvsc.sys może uniemożliwić interakcji z platformą Azure przez system.


## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków [migawki dysku systemowego](../windows/snapshot-copy-managed-disk.md) dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby rozwiązać ten problem, należy użyć konsoli szeregowej lub [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline) przez dołączenie dysku maszyny wirtualnej do maszyny Wirtualnej odzyskiwania.


### <a name="use-the-serial-console"></a>Użyj konsoli szeregowej

Połączyć się z [konsoli szeregowej, otwórz wystąpienie programu PowerShell](serial-console-windows.md), a następnie wykonaj poniższe kroki.

> [!NOTE]
> Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, przejdź do strony [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline) sekcji.

1. Uruchom następujące polecenie w wystąpieniu programu PowerShell, aby pobrać wersję pliku (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Pobierz odpowiednią aktualizację na dysku nowych lub istniejących danych, który jest dołączony do działającej maszyny Wirtualnej z tym samym regionie:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) lub nowsza aktualizacja
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) lub nowsza aktualizacja

3. Odłączanie dysku narzędzia z działającej maszyny Wirtualnej, a następnie dołączyć go do maszyny Wirtualnej w uszkodzona.

4. Uruchom następujące polecenie, aby zainstalować aktualizację na maszynie Wirtualnej:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Uruchom ponownie maszynę wirtualną.

### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb Offline

1. [Dołącz dysk systemu do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).

2. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.

3. Upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Należy pamiętać, literę dysku, która jest przypisana do dysku systemowego dołączone.

4. Utwórz kopię **\Windows\System32\config** niezbędne jest folder, w przypadku a wycofywania na zmiany.

5. Uruchom Edytor rejestru (regedit.exe) na ratownictwa maszyny Wirtualnej.

6. Wybierz **HKEY_LOCAL_MACHINE** klucza, a następnie wybierz **pliku** > **Załaduj gałąź rejestru** z menu.

7. Zlokalizuj plik systemowy w **\Windows\System32\config** folderu.

8. Wybierz **Otwórz**, typ **BROKENSYSTEM** dla nazwy, rozwiń węzeł **HKEY_LOCAL_MACHINE** klucza, a następnie zlokalizuj dodatkowy klucz, który nosi nazwę **BROKENSYSTEM** .

9. Przejdź do następującej lokalizacji:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. W każdym podkluczu (np. 0000), sprawdź **DriverDesc** wartość, która jest wyświetlana jako **karty sieciowej funkcji HYPER-V Microsoft**.

11. W podkluczu zbadać **DriverVersion** wartość, która jest wersja sterownika karty sieciowej maszyny wirtualnej.

12. Pobierz odpowiednią aktualizację:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) lub nowsza aktualizacja
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) lub nowsza aktualizacja

13. Dołącz dysk systemu jako dysk z danymi ratownictwa maszyny wirtualnej, na której można pobrać aktualizacji.

14. Uruchom następujące polecenie, aby zainstalować aktualizację na maszynie Wirtualnej:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Uruchom następujące polecenie, aby odinstalować gałęzi:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Odłączanie dysku systemowego i ponownie utworzyć maszynę Wirtualną](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z działem pomocy technicznej systemu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
