---
title: Usługi pulpitu zdalnego nie jest uruchamiana na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy związane z usługami pulpitu zdalnego, podczas nawiązywania połączenia z maszyną wirtualną | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: a9967aec61aaab5bc6b4517407f36e2a6c7342c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238866"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Usługi pulpitu zdalnego nie jest uruchamiania na Maszynie wirtualnej platformy Azure

W tym artykule opisano, jak rozwiązywać problemy dotyczące łączenia się do maszyny wirtualnej (maszyny Wirtualnej platformy Azure), gdy usług pulpitu zdalnego (TermService) nie jest uruchomienie lub nie można uruchomić.

>[!NOTE]
>Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager. Zalecamy użycie tego modelu w przypadku nowych wdrożeń zamiast przy użyciu klasycznego modelu wdrażania.

## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną, występują następujące scenariusze:

- Zrzut ekranu maszyny Wirtualnej pokazuje, że system operacyjny jest w pełni załadowany i Oczekiwanie na poświadczenia.

    ![Zrzut ekranu przedstawiający stan maszyny Wirtualnej](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Możesz zdalnie wyświetlić dzienniki zdarzeń na maszynie wirtualnej za pomocą Podglądu zdarzeń, zobaczysz, że usługi pulpitu zdalnego (TermServ) nie jest od lub nie można uruchomić. Poniżej przedstawiono przykładowy dziennik:

    **Rejestrowanie nazwy**: System </br>
    **Źródło**: Menedżer sterowania usługami </br>
    **Data**: 2017-12-16 11:19:36: 00</br>
    **Identyfikator zdarzenia**: 7022</br>
    **Zadanie kategorii**: Brak</br>
    **Poziom**: błąd</br>
    **Słowa kluczowe**: klasyczny</br>
    **Użytkownik**: n/d</br>
    **Komputer**: vm.contoso.com</br>
    **Opis**: usługi pulpitu zdalnego zawiesiła się podczas uruchamiania. 

    Funkcja konsoli szeregowej dostępu umożliwia również wyszukać te błędy przy użyciu następującej kwerendy: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Przyczyna
 
Ten problem występuje, ponieważ usług pulpitu zdalnego nie jest uruchomiona na maszynie Wirtualnej. Przyczyną może zależeć od następujących scenariuszy: 

- Usługa TermService jest ustawiona na **wyłączone**. 
- Usługa TermService jest uległa awarii lub zawiesza się. 

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy użyć konsoli szeregowej lub [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline) , dołączając dysk systemu operacyjnego maszyny wirtualnej do maszyny Wirtualnej odzyskiwania.

### <a name="use-serial-console"></a>Użyj konsoli szeregowej

1. Dostęp do [konsoli szeregowej](serial-console-windows.md) , wybierając **pomoc techniczna i rozwiązywanie problemów** > **konsoli szeregowej**. Jeśli ta funkcja jest włączona na maszynie Wirtualnej, możesz połączyć maszynę Wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Typ **CMD** uruchomić kanału można pobrać nazwy kanału.

3. Przełącz się do kanału, w tym uruchomione wystąpienie CMD. W takim przypadku należy kanał 1.

   ```
   ch -si 1
   ```

4. Naciśnij klawisz **Enter** ponownie i wprowadź prawidłową nazwę użytkownika i hasło (identyfikator lokalnego lub domeny) dla maszyny Wirtualnej.

5. Kwerenda o stan usługi TermService.

   ```
   sc query TermService
   ```

6. Jeśli stan usługi wskazuje **zatrzymane**, spróbuj uruchomić usługę.

    ```
    sc start TermService
     ``` 

7. Zapytanie usługi ponownie, aby upewnić się, że usługa została uruchomiona pomyślnie.

   ```
   sc query TermService
   ```
    Jeśli usługi nie powiedzie się, postępuj zgodnie z rozwiązania, w oparciu o otrzymany błąd:

    |  Błąd |  Sugestia |
    |---|---|
    |5 — ODMOWA DOSTĘPU |Zobacz [TermService zostanie zatrzymana z powodu błędu odmowy dostępu](#termService-service-is-stopped-because-of-access-denied-error) |
    |1058 - ERROR_SERVICE_DISABLED  |Zobacz [TermService usługa zostanie wyłączona.](#termService-service-is-disabled)  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem|
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem    |
    |1070 - ERROR_SERVICE_START_HANG   | [Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem  |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Zobacz [TermService usługa jest wyłączona.](#termService-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem |
    |1753   |[Skontaktuj się z działem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem   |

#### <a name="termservice-service-is-stopped-because-of-access-denied-error"></a>TermService zostanie zatrzymana z powodu błędu odmowy dostępu

1. Połączyć się z [konsoli szeregowej](serial-console-windows.md#) , a następnie otwórz wystąpienie programu PowerShell.
2. Pobierz narzędzia Monitor procesu w systemie, uruchamiając następujący skrypt:

        remove-module psreadline  
        $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
        $destination = "c:\temp\ProcessMonitor.zip" 
        $wc = New-Object System.Net.WebClient 
        $wc.DownloadFile($source,$destination) 
3. Teraz można uruchomić śledzenia procmon:

        procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
4. Odmów odtworzenia problemu przez uruchomienie usługi, która jest przyznawania dostępu: 

        sc start TermService 
        
    Jeśli nie powiodło się, przejdź dalej i zakończyć śledzenia procesu monitora:

        procmon /Terminate 
5. Zbieranie pliku **c:\temp\ProcMonTrace.PML**, otwórz go za pomocą procmon, a następnie filtrować dane według **wynik jest odmowa dostępu** pokazuje, jak poniższy zrzut ekranu:

    ![Filtruj według wynik na liście Monitor procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Usuń klucze rejestru, foldery lub pliki, które znajdują się w danych wyjściowych. Zazwyczaj przyczyną tego problemu jest dziennika dla konta używane w usłudze nie masz uprawnień listy ACL dostępu do tych obiektów. Aby dowiedzieć się odpowiednie uprawnienie listy kontroli dostępu dla konta logowania, można sprawdzić w dobrej kondycji maszyny Wirtualnej. 

#### <a name="termservice-service-is-disabled"></a>Usługa TermService jest wyłączona.

1.  Przywróć usługi do wartości domyślnej uruchamiania:

        sc config TermService start= demand 
        
2.  Uruchom usługę:

        sc start TermService 
3.  Zbadać jego stan ponownie, aby upewnić się, usługa jest uruchomiona: sc kwerendy TermService 
4.  Spróbuj conntet do maszyny Wirtualnej przy użyciu pulpitu zdalnego.


### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md).
2. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania. Upewnij się, że dysk dołączony jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.
3.  Otwórz wiersz polecenia z podwyższonym wystąpienie (**Uruchom jako administrator**), a następnie uruchom następujący skrypt. Przyjęto założenie, że litery dysku, która jest przypisana do dołączonym dysku systemu operacyjnego jest Zamień F. ją z odpowiednią wartość w maszynie Wirtualnej. 

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
        REM Set default values back on the broken service 
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
4. [Odłącz dysk systemu operacyjnego i ponowne utworzenie maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md), a następnie sprawdź, czy problem został rozwiązany.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
