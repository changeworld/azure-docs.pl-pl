---
title: Usługi pulpitu zdalnego nie jest uruchamiania na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy związane z usługami pulpitu zdalnego, po nawiązaniu połączenia z maszyną wirtualną | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 5458a02c09a3600875c7300b27c5a87a735b2f1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318904"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Usługi pulpitu zdalnego nie jest uruchamiania na Maszynie wirtualnej platformy Azure

W tym artykule opisano sposób rozwiązywania problemów, po nawiązaniu połączenia z maszyny wirtualnej (VM) platformy Azure i usług pulpitu zdalnego lub TermService, nie jest uruchomienie lub nie została uruchomiona.

> [!NOTE]  
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Usługa Azure Resource Manager i Model Klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager. Zalecamy użycie tego modelu w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania.

## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną, występują następujące scenariusze:

- Zrzut ekranu maszyny Wirtualnej pokazuje, że system operacyjny jest w pełni załadowany i Oczekiwanie na poświadczenia.

    ![Zrzut ekranu przedstawiający stan maszyny Wirtualnej](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Zdalne wyświetlanie dzienników zdarzeń maszyny wirtualnej za pomocą Podglądu zdarzeń. Zobaczysz, że usług pulpitu zdalnego, TermService, nie jest uruchomienie lub nie została uruchomiona. Następujący dziennik znajduje się przykładowy:

    **Rejestrowanie nazwy**:      System </br>
    **Źródło**:        Menedżer sterowania usługami </br>
    **Data**:          2017-12-16 11:19:36: 00</br>
    **Identyfikator zdarzenia**:      7022</br>
    **Zadanie kategorii**: Brak</br>
    **Poziom**:         Błąd</br>
    **Keywords**:      Wdrożenie klasyczne</br>
    **Użytkownik**:          ND</br>
    **Computer**:      vm.contoso.com</br>
    **Opis**: Usługi pulpitu zdalnego zawiesiła się podczas uruchamiania. 

    Funkcja konsoli szeregowej dostępu umożliwia również wyszukać te błędy, uruchamiając następujące zapytanie: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Przyczyna
 
Ten problem występuje, ponieważ usług pulpitu zdalnego nie jest uruchomiona na maszynie Wirtualnej. Przyczyną może zależeć od następujących scenariuszy: 

- Usługa TermService jest ustawiona na **wyłączone**. 
- Usługa TermService uległa awarii lub nie odpowiada. 
- TermService nie rozpoczyna z powodu nieprawidłowej konfiguracji.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy użyć konsoli szeregowej. Lub [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline) , dołączając dysk systemu operacyjnego maszyny wirtualnej do maszyny Wirtualnej odzyskiwania.

### <a name="use-serial-console"></a>Użyj konsoli szeregowej

1. Dostęp do [konsoli szeregowej](serial-console-windows.md) , wybierając **pomoc techniczna i rozwiązywanie problemów** > **konsoli szeregowej**. Jeśli ta funkcja jest włączona na maszynie Wirtualnej, możesz połączyć maszynę Wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Wprowadź **CMD** Uruchom kanał i uzyskać nazwę kanału.

3. Przełącz się do kanału, który uruchamia wystąpienie polecenia. W takim przypadku należy kanał 1:

   ```
   ch -si 1
   ```

4. Wybierz **Enter** ponownie i wprowadź prawidłową nazwę użytkownika i hasło, lokalnego lub domeny Identyfikatora, dla maszyny Wirtualnej.

5. Kwerenda o stan usługi TermService:

   ```
   sc query TermService
   ```

6. Jeśli stan usługi wskazuje **zatrzymane**, spróbuj uruchomić usługę:

    ```
    sc start TermService
     ``` 

7. Zapytania usługę ponownie, aby upewnić się, że usługa została uruchomiona pomyślnie:

   ```
   sc query TermService
   ```
8. Jeśli usługi nie powiedzie się, postępuj zgodnie z rozwiązania, w oparciu o otrzymany błąd:

    |  Błąd |  Sugestia |
    |---|---|
    |5 — ODMOWA DOSTĘPU |Zobacz [TermService zostanie zatrzymana z powodu błędu dostępu](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Zobacz [TermService usługa zostanie wyłączona](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Zobacz [TermService usługa ulegnie awarii lub zawiesza się](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.|
    |1067 - ERROR_PROCESS_ABORTED  |Zobacz [TermService usługa ulegnie awarii lub zawiesza się](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Zobacz [usługi TermService kończy się niepowodzeniem z powodu niepowodzenia logowania](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Zobacz [TermService usługa ulegnie awarii lub zawiesza się](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Zobacz [TermService usługa zostanie wyłączona](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem. |
    |1753   |[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>TermService zostanie zatrzymana z powodu problemu odmowa dostępu

1. Połączyć się z [konsoli szeregowej](serial-console-windows.md) , a następnie otwórz wystąpienie programu PowerShell.
2. Pobierz narzędzia Monitor procesu w systemie, uruchamiając następujący skrypt:

   ```
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

4. Odtwórz problem przez uruchomienie usługi, która udostępnia **dostępu**: 

   ```
   sc start TermService 
   ```

   Jeśli nie powiedzie się, zakończenia śledzenia procesu monitora:

   ```   
   procmon /Terminate 
   ```

5. Zbieranie pliku **c:\temp\ProcMonTrace.PML**:

    1. [Dołączanie dysku danych do maszyny Wirtualnej](../windows/attach-managed-disk-portal.md
).
    2. Użyj konsoli szeregowej, możesz skopiować go na nowy dysk. Na przykład `copy C:\temp\ProcMonTrace.PML F:\`. W tym poleceniu F jest literą sterownika dołączonego dysku danych.
    3. Odłączanie dysku danych i dołączyć go na działającą maszynę Wirtualną, której Monitor procesu ubstakke zainstalowane.

6. Otwórz **ProcMonTrace.PML** za pomocą procesu monitora działającej maszyny Wirtualnej. Następnie filtrować dane według **wynik jest odmowa dostępu**, jak pokazano na poniższym zrzucie ekranu:

    ![Filtruj według wynik na liście Monitor procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Usuń klucze rejestru, foldery lub pliki, które znajdują się w danych wyjściowych. Zazwyczaj ten problem występuje po konto logowania, która jest używana w usłudze nie ma uprawnienia listy ACL dostępu do tych obiektów. Aby dowiedzieć się odpowiednie uprawnienie listy kontroli dostępu dla konta logowania, można sprawdzić w dobrej kondycji maszyny Wirtualnej. 

#### <a name="termservice-service-is-disabled"></a>Usługa TermService jest wyłączona.

1. Przywróć usługi do wartości domyślnej uruchamiania:

   ```
   sc config TermService start= demand 
   ```

2. Uruchom usługę:

   ```
   sc start TermService
   ```

3. Zbadać jego stan ponownie, aby upewnić się, że usługa jest uruchomiona:

   ```
   sc query TermService 
   ```

4. Spróbuj nawiązać połączenie z maszyną Wirtualną przy użyciu pulpitu zdalnego.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Usługa TermService zakończy się niepowodzeniem z powodu niepowodzenia logowania

1. Ten problem występuje, gdy zmieniono konto uruchamiania usługi. Zmieniony w tym na domyślny: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Uruchom usługę:

        sc start TermService
3. Spróbuj nawiązać połączenie z maszyną Wirtualną przy użyciu pulpitu zdalnego.

#### <a name="termservice-service-crashes-or-hangs"></a>Zawieszanie się lub awariach usługi TermService
1. Jeśli stan usługi utkwiła w automatycznej **od** lub **zatrzymywanie**, spróbuj zatrzymać usługę: 

        sc stop TermService
2. Izoluj usługę na własnym kontenerze "svchost":

        sc config TermService type= own
3. Uruchom usługę:

        sc start TermService
4. Jeśli usługa jest nadal nie można uruchomić, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md).
2. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania. Upewnij się, że dysk dołączony jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.
3. Otwórz wiersz polecenia z podwyższonym wystąpienie (**Uruchom jako administrator**). Następnie uruchom następujący skrypt. Przyjęto założenie, że litery dysku, która jest przypisana do dołączonym dysku systemu operacyjnego jest **F**. Zastąp go odpowiednią wartość w maszynie Wirtualnej. 

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

4. [Odłącz dysk systemu operacyjnego i ponowne utworzenie maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md). Sprawdź, czy problem został rozwiązany.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można rozwiązać swój problem.
