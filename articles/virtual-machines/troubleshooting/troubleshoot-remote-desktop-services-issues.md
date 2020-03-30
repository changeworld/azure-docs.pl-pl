---
title: Usługi pulpitu zdalnego nie uruchamia się na maszynie Wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z usługami pulpitu zdalnego podczas łączenia się z maszyną wirtualną | Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 4b314fbdb9cbc0c0b797cbee8e92ee4702bbea81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919468"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Usługi pulpitu zdalnego nie uruchamia się na maszynie Wirtualnej platformy Azure

W tym artykule opisano, jak rozwiązywać problemy podczas łączenia się z maszyną wirtualną platformy Azure (Maszyna wirtualna) i usługi pulpitu zdalnego lub TermService, nie uruchamia się lub nie można go uruchomić.


## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną występują następujące scenariusze:

- Zrzut ekranu maszyny Wirtualnej pokazuje, że system operacyjny jest w pełni załadowany i oczekuje na poświadczenia.

    ![Zrzut ekranu przedstawiający stan maszyny Wirtualnej](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Dzienniki zdarzeń można wyświetlać zdalnie na maszynie Wirtualnej przy użyciu Podglądu zdarzeń. Widać, że usługi pulpitu zdalnego, TermService, nie uruchamia się lub nie można go uruchomić. Poniższy dziennik jest przykładem:

    **Nazwa dziennika**: System </br>
    **Źródło**: Menedżer sterowania usługami </br>
    **Data**: 16/16/2017 11:19:36</br>
    **Identyfikator zdarzenia**: 7022</br>
    **Kategoria zadania**: Brak</br>
    **Poziom**: Błąd</br>
    **Słowa kluczowe**: Klasyczny</br>
    **Użytkownik**: Nie dotyczy</br>
    **Komputer**: vm.contoso.com</br>
    **Opis:** Usługa Usług pulpitu zdalnego zawieszona przy uruchomieniu. 

    Za pomocą funkcji Konsoli dostępu szeregowego można również wyszukać te błędy, uruchamiając następującą kwerendę: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Przyczyna
 
Ten problem występuje, ponieważ usługi pulpitu zdalnego nie jest uruchomiony na maszynie Wirtualnej. Przyczyna może zależeć od następujących scenariuszy: 

- Usługa TermService jest **ustawiona**na Wyłączona . 
- Usługa TermService ulega awarii lub nie odpowiada. 
- TermService nie uruchamia się z powodu niepoprawnej konfiguracji.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj konsoli szeregowej. Lub [naprawy maszyny Wirtualnej w trybie offline,](#repair-the-vm-offline) dołączając dysk systemu operacyjnego maszyny Wirtualnej do odzyskiwania maszyny Wirtualnej.

### <a name="use-serial-console"></a>Korzystanie z konsoli szeregowego

1. Dostęp do [konsoli szeregowej](serial-console-windows.md) można uzyskać, wybierając pozycję **Pomoc techniczna & rozwiązywanie problemów z konsolą** > **szeregową**. Jeśli funkcja jest włączona na maszynie Wirtualnej, można połączyć maszynę wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Wprowadź **CMD,** aby uruchomić kanał i uzyskać nazwę kanału.

3. Przełącz się do kanału, który uruchamia wystąpienie CMD. W takim przypadku powinien to być kanał 1:

   ```
   ch -si 1
   ```

4. Wybierz **pozycję Wprowadź** ponownie i wprowadź prawidłową nazwę użytkownika i hasło, identyfikator lokalny lub identyfikator domeny dla maszyny Wirtualnej.

5. Kwerenda o stan usługi TermService:

   ```
   sc query TermService
   ```

6. Jeśli stan usługi pokazuje **zatrzymane**, spróbuj uruchomić usługę:

    ```
    sc start TermService
     ``` 

7. Ponownie skonsuj kwerendę, aby upewnić się, że usługa została pomyślnie uruchomiona:

   ```
   sc query TermService
   ```
8. Jeśli uruchomienie usługi nie powiedzie się, postępuj zgodnie z rozwiązaniem opartym na wyświetlonym błędzie:

    |  Błąd |  Sugestia |
    |---|---|
    |5- ODMOWA DOSTĘPU |Zobacz [Usługa TermService jest zatrzymana z powodu błędu odmowy dostępu](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Zobacz [Usługa TermService jest wyłączona](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Zobacz [Usługa TermService ulega awarii lub zawiesza się](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.|
    |1067 - ERROR_PROCESS_ABORTED  |Zobacz [Usługa TermService ulega awarii lub zawiesza się](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Zobacz [Usługa TermService kończy się niepowodzeniem z powodu błędu logowania](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Zobacz [Usługa TermService ulega awarii lub zawiesza się](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Zobacz [Usługa TermService jest wyłączona](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem. |
    |1753   |[Skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Usługa TermService została zatrzymana z powodu problemu z odmową dostępu

1. Połącz się [z konsolą szeregową](serial-console-windows.md) i otwórz wystąpienie programu PowerShell.
2. Pobierz narzędzie Monitor procesu, uruchamiając następujący skrypt:

   ```
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

4. Odtwórz problem, uruchamiając usługę, która daje **odmowę dostępu:** 

   ```
   sc start TermService 
   ```

   Gdy to się nie powiedzie, zakończ śledzenie Monitora procesu:

   ```   
   procmon /Terminate 
   ```

5. Zbierz plik **c:\temp\ProcMonTrace.PML**:

    1. [Podłącz dysk danych do maszyny Wirtualnej](../windows/attach-managed-disk-portal.md
).
    2. Użyj konsoli szeregowej, możesz skopiować plik na nowy dysk. Na przykład `copy C:\temp\ProcMonTrace.PML F:\`. W tym poleceniu F jest literą sterownika dołączonego dysku danych.
    3. Odłącz dysk danych i dołącz go do działającej maszyny Wirtualnej z zainstalowanym monitorem procesu ubstakke.

6. Otwórz **ProcMonTrace.PML** przy użyciu process monitor pracy maszyny Wirtualnej. Następnie filtruj według **wyniku jest ODMOWA DOSTĘPU**, jak pokazano na poniższym zrzucie ekranu:

    ![Filtrowanie według wyniku w Monitorze procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Napraw klucze rejestru, foldery lub pliki, które znajdują się na danych wyjściowych. Zazwyczaj ten problem jest spowodowany, gdy konto logowania, który jest używany w usłudze nie ma uprawnień ACL dostępu do tych obiektów. Aby poznać poprawne uprawnienie listy ACL dla konta logowania, możesz sprawdzić na dobrej kondycji maszyny Wirtualnej. 

#### <a name="termservice-service-is-disabled"></a>Usługa TermService jest wyłączona

1. Przywróć domyślną wartość uruchamiania usługi:

   ```
   sc config TermService start= demand 
   ```

2. Uruchom usługę:

   ```
   sc start TermService
   ```

3. Ponownie zapytaj o jego stan, aby upewnić się, że usługa jest uruchomiona:

   ```
   sc query TermService 
   ```

4. Spróbuj połączyć się z maszyną wirtualną za pomocą pulpitu zdalnego.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Usługa TermService kończy się niepowodzeniem z powodu błędu logowania

1. Ten problem występuje, jeśli konto startowe tej usługi zostało zmienione. Zmieniono ten powrót do ustawień domyślnych: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Uruchom usługę:

        sc start TermService
3. Spróbuj połączyć się z maszyną wirtualną za pomocą pulpitu zdalnego.

#### <a name="termservice-service-crashes-or-hangs"></a>Usługa TermService ulega awarii lub zawiesza się
1. Jeśli stan usługi jest zablokowany w **uruchamianiu** lub **zatrzymywaniu,** spróbuj zatrzymać usługę: 

        sc stop TermService
2. Wyizolować usługę na własnym kontenerze "svchost":

        sc config TermService type= own
3. Uruchom usługę:

        sc start TermService
4. Jeśli uruchomienie usługi nadal nie może się uruchomić, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
2. Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania. Upewnij się, że dołączony dysk jest oznaczony jako **w trybie online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemu operacyjnego.
3. Otwórz wystąpienie wiersza polecenia z podwyższonym poziomem uprawnień **(Uruchom jako administrator).** Następnie uruchom następujący skrypt. Zakładamy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to **F**. Wymień go na odpowiednią wartość w maszynie wirtualnej. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Odłącz dysk systemu operacyjnego i ponownie stwórz maszynę wirtualną](../windows/troubleshoot-recovery-disks-portal.md). Następnie sprawdź, czy problem został rozwiązany.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby rozwiązać problem.
