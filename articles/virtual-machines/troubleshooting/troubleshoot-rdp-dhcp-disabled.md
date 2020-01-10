---
title: Nie można zdalne łączenie z usługą Azure Virtual Machines ponieważ DHCP jest wyłączona | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z RDP problem powodowany przez usługi klienta DHCP jest wyłączona w systemie Microsoft Azure. | Dokumentacja firmy Microsoft
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
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749896"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Nie można wykonać protokołu RDP na maszynach wirtualnych platformy Azure, ponieważ usługa klienta DHCP jest wyłączona

W tym artykule opisano problem, w którym nie jest możliwe pulpitu zdalnego do platformy Azure Windows Virtual Machines (VMs) po Usługa klienta DHCP jest wyłączona na maszynie wirtualnej.


## <a name="symptoms"></a>Objawy
Nie można wprowadzać z połączeniem RDP maszyny Wirtualnej na platformie Azure, ponieważ usługa klienta DHCP jest wyłączona na maszynie wirtualnej. Podczas ewidencjonowania zrzucie ekranu [diagnostykę rozruchu](../troubleshooting/boot-diagnostics.md) w witrynie Azure portal, zostanie wyświetlony wykonać normalnego rozruchu maszyny Wirtualnej i czeka na poświadczenia na ekranie logowania. Zdalne wyświetlanie dzienników zdarzeń maszyny wirtualnej za pomocą Podglądu zdarzeń. Zobaczysz, że usługa klienta DHCP nie jest uruchomiona lub nie została uruchomiona. Następujące przykładowe dziennika:

**Rejestrowanie nazwy**: System </br>
**Źródło**: Menedżer sterowania usługami </br>
**Data**: 2015-12-16 11:19:36: 00 </br>
**Identyfikator zdarzenia**: 7022 </br>
**Zadanie kategorii**: Brak </br>
**Poziom**: błąd </br>
**Słowa kluczowe**: klasyczny</br>
**Użytkownik**: n/d </br>
**Komputer**: myvm.cosotos.com</br>
**Opis**: Usługa klienta DHCP zawiesiła się podczas uruchamiania.</br>

W przypadku maszyn wirtualnych usługi Resource Manager można użyć funkcji konsoli szeregowej dostęp do wykonywania zapytań, zdarzenia dzienników 7022 przy użyciu następującego polecenia:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Dla klasycznych maszyn wirtualnych konieczne będzie działać w trybie OFFLINE i zebrać dzienniki ręcznie.

## <a name="cause"></a>Przyczyna

Usługa klienta DHCP nie jest uruchomiona na maszynie Wirtualnej.

> [!NOTE]
> Ten artykuł ma zastosowanie tylko w przypadku usługi klienta DHCP, a nie serwera DHCP.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć Serial kontroli, Włącz protokół DHCP lub [interfejs sieciowy resetowania](reset-network-interface.md) dla maszyny Wirtualnej.

### <a name="use-serial-control"></a>Korzystanie z kontroli szeregowej

1. Połączyć się z [konsoli szeregowej i otwórz wystąpienie CMD](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, zobacz [interfejs sieciowy resetowania](reset-network-interface.md).
2. Sprawdź, czy DHCP jest wyłączony w interfejsie sieciowym:

        sc query DHCP
3. Jeżeli DHCP jest zatrzymana, spróbuj uruchomić usługę

        sc start DHCP

4. Zapytanie usługi ponownie, aby upewnić się, że usługa została uruchomiona pomyślnie.

        sc query DHCP

    Spróbuj nawiązać połączenie z maszyną Wirtualną i zobacz, czy problem został rozwiązany.
5. Jeśli usługa nie zostanie uruchomiona, należy użyć następujących właściwego rozwiązania oparte na otrzymany komunikat o błędzie:

    | Błąd  |  Rozwiązanie |
    |---|---|
    | 5 — ODMOWA DOSTĘPU  | Zobacz [Usługa klienta DHCP jest zatrzymana z powodu błędu dostępu](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Zobacz [Usługa klienta DHCP ulega awarii lub zawiesza się](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Zobacz [Usługa klienta DHCP jest wyłączona](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybko rozwiązać problem.   |
    | 1067 - ERROR_PROCESS_ABORTED |Zobacz [Usługa klienta DHCP ulega awarii lub zawiesza się](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybko rozwiązać problem.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Zobacz [Usługa klienta DHCP zakończy się niepowodzeniem z powodu niepowodzenia logowania](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Zobacz [Usługa klienta DHCP ulega awarii lub zawiesza się](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Zobacz [Usługa klienta DHCP jest wyłączona](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybko rozwiązać problem.  |
    |1053 | [Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybko rozwiązać problem.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Usługa klienta DHCP jest zatrzymana z powodu błędu odmowy dostępu

1. Połączyć się z [konsoli szeregowej](serial-console-windows.md) , a następnie otwórz wystąpienie programu PowerShell.
2. Pobierz narzędzia Monitor procesu w systemie, uruchamiając następujący skrypt:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Teraz rozpocząć **procmon** śledzenia:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Odtwórz problem przez uruchomienie usługi, która generuje **dostępu** komunikat:

   ```
   sc start DHCP
   ```

   Jeśli nie powiedzie się, zakończenia śledzenia procesu monitora:

   ```
   procmon /Terminate
   ```
5. Zbieraj **c:\temp\ProcMonTrace.PML** pliku:

    1. [Dołączanie dysku danych do maszyny Wirtualnej](../windows/attach-managed-disk-portal.md
).
    2. Użyj konsoli szeregowej, możesz skopiować go na nowy dysk. Na przykład `copy C:\temp\ProcMonTrace.PML F:\`. W tym poleceniu F jest literą sterownika dołączonego dysku danych. Zastąp literę zgodnie z potrzebami poprawnej wartości.
    3. Odłączanie dysku danych, a następnie dołączyć go do pracy maszyny Wirtualnej, która ma ubstakke Monitor procesu zainstalowane.

6. Otwórz **ProcMonTrace.PML** za pomocą procesu monitora na działającej maszyny Wirtualnej. Następnie filtrować dane według **wynik jest odmowa dostępu**, jak pokazano na poniższym zrzucie ekranu:

    ![Filtruj według wynik na liście Monitor procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Usuń klucze rejestru, foldery lub pliki, które znajdują się w danych wyjściowych. Zazwyczaj ten problem występuje po konto logowania, która jest używana w usłudze nie ma uprawnienia listy ACL dostępu do tych obiektów. Aby określić odpowiednie uprawnienie listy kontroli dostępu dla konta logowania, można sprawdzić w dobrej kondycji maszyny Wirtualnej.

#### <a name="dhcp-client-service-is-disabled"></a>Usługa klienta DHCP jest wyłączona.

1. Przywróć usługi do wartości domyślnej uruchamiania:

   ```
   sc config DHCP start= auto
   ```

2. Uruchom usługę:

   ```
   sc start DHCP
   ```

3. Kwerenda o stan usługi ponownie, aby upewnić się, że jest uruchomiona:

   ```
   sc query DHCP
   ```

4. Spróbuj nawiązać połączenie z maszyną Wirtualną przy użyciu pulpitu zdalnego.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Usługa klienta DHCP zakończy się niepowodzeniem z powodu niepowodzenia logowania

1. Ponieważ ten problem występuje, gdy konto uruchamiania usługi został zmieniony, przywróć konto do stanu domyślnego:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Uruchom usługę:

        sc start DHCP
3. Spróbuj nawiązać połączenie z maszyną Wirtualną przy użyciu pulpitu zdalnego.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Usługa klienta DHCP ulega awarii lub zawiesza się

1. Jeśli stan usługi utkwiła w automatycznej **od** lub **zatrzymywanie** stanu, spróbuj zatrzymać usługę:

        sc stop DHCP
2. Izoluj usługę na własnym kontenerze "svchost":

        sc config DHCP type= own
3. Uruchom usługę:

        sc start DHCP
4. Jeśli usługa nadal nie zostanie uruchomiona, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md).
2. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania. Upewnij się, że dysk dołączony jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.
3.  Otwórz wiersz polecenia z podwyższonym wystąpienie (**Uruchom jako administrator**). Następnie uruchom następujący skrypt. Ten skrypt zakłada, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to **F**. Zamień literę odpowiednio do wartości w maszynie wirtualnej.

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

4. [Odłącz dysk systemu operacyjnego i ponowne utworzenie maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md). Sprawdź, czy problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać problem rozwiązany.