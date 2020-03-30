---
title: Nie można połączyć się zdalnie z maszynami wirtualnymi platformy Azure, ponieważ usługa DHCP jest wyłączona| Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem z prok. Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 2c5b0556554d280e57b2df51875e1b057b5fb4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749896"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Nie można rdp do maszyn wirtualnych platformy Azure, ponieważ usługa klienta DHCP jest wyłączona

W tym artykule opisano problem, w którym nie można zdalnie pulpitu do maszyn wirtualnych systemu Azure Windows (VMs) po wyłączenia usługi klienta DHCP na maszynie wirtualnej.


## <a name="symptoms"></a>Objawy
Nie można nawiązać połączenia RDP maszyny Wirtualnej na platformie Azure, ponieważ usługa klienta DHCP jest wyłączona na maszynie wirtualnej. Po sprawdzeniu zrzutu ekranu w [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md) w witrynie Azure portal, widzisz bootiowania maszyny Wirtualnej normalnie i czeka na poświadczenia na ekranie logowania. Dzienniki zdarzeń można wyświetlać zdalnie na maszynie Wirtualnej przy użyciu Podglądu zdarzeń. Widać, że usługa klienta DHCP nie została uruchomiona lub nie można jej uruchomić. Następujący dziennik przykładowy:

**Nazwa dziennika**: System </br>
**Źródło**: Menedżer sterowania usługami </br>
**Data**: 16/16/2015 11:19:36 </br>
**Identyfikator zdarzenia**: 7022 </br>
**Kategoria zadania**: Brak </br>
**Poziom**: Błąd </br>
**Słowa kluczowe**: Klasyczny</br>
**Użytkownik**: Nie dotyczy </br>
**Komputer**: myvm.cosotos.com</br>
**Opis:** Usługa klienta DHCP zawieszona przy uruchamianiu.</br>

W przypadku maszyn wirtualnych Menedżera zasobów można użyć funkcji Konsoli dostępu szeregowego do wykonywania zapytań dotyczących dzienników zdarzeń 7022 za pomocą następującego polecenia:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

W przypadku klasycznych maszyn wirtualnych należy pracować w trybie OFFLINE i ręcznie zbierać dzienniki.

## <a name="cause"></a>Przyczyna

Usługa klienta DHCP nie jest uruchomiona na maszynie wirtualnej.

> [!NOTE]
> Ten artykuł dotyczy tylko usługi klienta DHCP, a nie serwera DHCP.

## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, należy wykonać migawkę dysku systemu operacyjnego maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, użyj kontrolki szeregowej, aby włączyć usługę DHCP lub [zresetować interfejs sieciowy](reset-network-interface.md) maszyny Wirtualnej.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowego

1. Połącz się z [konsolą szeregową i otwórz wystąpienie CMD](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, zobacz [Resetowanie interfejsu sieciowego](reset-network-interface.md).
2. Sprawdź, czy usługa DHCP jest wyłączona w interfejsie sieciowym:

        sc query DHCP
3. Jeśli usługa DHCP zostanie zatrzymana, spróbuj uruchomić usługę

        sc start DHCP

4. Ponownie skonsuj kwerendę, aby upewnić się, że usługa została pomyślnie uruchomiona.

        sc query DHCP

    Spróbuj połączyć się z maszyną wirtualną i sprawdź, czy problem został rozwiązany.
5. Jeśli usługa nie zostanie uruchomiony, użyj następującego odpowiedniego rozwiązania, na podstawie komunikatu o błędzie, który został odebrany:

    | Błąd  |  Rozwiązanie |
    |---|---|
    | 5- ODMOWA DOSTĘPU  | Zobacz [Usługa klienta DHCP została zatrzymana z powodu błędu odmowy dostępu](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Zobacz [Usługa klienta DHCP ulega awarii lub zawiesza się](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Zobacz [Usługa klienta DHCP jest wyłączona](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.   |
    | 1067 - ERROR_PROCESS_ABORTED |Zobacz [Usługa klienta DHCP ulega awarii lub zawiesza się](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Zobacz [Usługa klienta DHCP kończy się niepowodzeniem z powodu błędu logowania](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Zobacz [Usługa klienta DHCP ulega awarii lub zawiesza się](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Zobacz [Usługa klienta DHCP jest wyłączona](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.  |
    |1053 | [Skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Usługa klienta DHCP została zatrzymana z powodu błędu odmowy dostępu

1. Połącz się [z konsolą szeregową](serial-console-windows.md) i otwórz wystąpienie programu PowerShell.
2. Pobierz narzędzie Monitor procesu, uruchamiając następujący skrypt:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Teraz rozpocząć śledzenie **procmon:**

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Odtwórz problem, uruchamiając usługę, która generuje komunikat **Odmowa dostępu:**

   ```
   sc start DHCP
   ```

   Gdy to się nie powiedzie, zakończ śledzenie Monitora procesu:

   ```
   procmon /Terminate
   ```
5. Zbierz plik **c:\temp\ProcMonTrace.PML:**

    1. [Podłącz dysk danych do maszyny Wirtualnej](../windows/attach-managed-disk-portal.md
).
    2. Użyj konsoli szeregowej, możesz skopiować plik na nowy dysk. Na przykład `copy C:\temp\ProcMonTrace.PML F:\`. W tym poleceniu F jest literą sterownika dołączonego dysku danych. Wymień literę odpowiednio na prawidłową wartość.
    3. Odłącz dysk danych, a następnie podłącz go do działającej maszyny Wirtualnej z zainstalowanym monitorem procesu ubstakke.

6. Otwórz **ProcMonTrace.PML** przy użyciu Monitora procesów na działającej maszynie wirtualnej. Następnie filtruj według **wyniku jest ODMOWA DOSTĘPU**, jak pokazano na poniższym zrzucie ekranu:

    ![Filtrowanie według wyniku w Monitorze procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Napraw klucze rejestru, foldery lub pliki, które znajdują się na danych wyjściowych. Zazwyczaj ten problem jest spowodowany, gdy konto logowania, który jest używany w usłudze nie ma uprawnień ACL dostępu do tych obiektów. Aby określić prawidłowe uprawnienie ACL dla konta logowania, można sprawdzić na dobrej kondycji maszyny Wirtualnej.

#### <a name="dhcp-client-service-is-disabled"></a>Usługa klienta DHCP jest wyłączona

1. Przywróć domyślną wartość uruchamiania usługi:

   ```
   sc config DHCP start= auto
   ```

2. Uruchom usługę:

   ```
   sc start DHCP
   ```

3. Ponownie skonserwuj stan usługi, aby upewnić się, że jest uruchomiona:

   ```
   sc query DHCP
   ```

4. Spróbuj połączyć się z maszyną wirtualną za pomocą pulpitu zdalnego.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Usługa klienta DHCP kończy się niepowodzeniem z powodu błędu logowania

1. Ponieważ ten problem występuje, jeśli konto startowe tej usługi zostało zmienione, przywróć konto do stanu domyślnego:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Uruchom usługę:

        sc start DHCP
3. Spróbuj połączyć się z maszyną wirtualną za pomocą pulpitu zdalnego.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Usługa klienta DHCP ulega awarii lub zawiesza się

1. Jeśli stan usługi jest zablokowany w stanie **Uruchamianie** lub **Zatrzymywanie,** spróbuj zatrzymać usługę:

        sc stop DHCP
2. Wyizolować usługę na własnym kontenerze "svchost":

        sc config DHCP type= own
3. Uruchom usługę:

        sc start DHCP
4. Jeśli usługa nadal nie zostanie uruchomiony, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
2. Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania. Upewnij się, że dołączony dysk jest oznaczony jako **w trybie online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemu operacyjnego.
3.  Otwórz wystąpienie wiersza polecenia z podwyższonym poziomem uprawnień **(Uruchom jako administrator).** Następnie uruchom następujący skrypt. Ten skrypt zakłada, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to **F**. W razie potrzeby zastąp literę wartością maszyny Wirtualnej.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Odłącz dysk systemu operacyjnego i ponownie stwórz maszynę wirtualną](../windows/troubleshoot-recovery-disks-portal.md). Następnie sprawdź, czy problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby rozwiązać problem.