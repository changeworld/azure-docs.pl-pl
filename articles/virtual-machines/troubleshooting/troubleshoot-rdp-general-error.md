---
title: Rozwiązywanie problemów z błędem Ogólne RDP maszyny Wirtualnej z systemem Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z błędem Ogólne RDP maszyny Wirtualnej z systemem Windows na platformie Azure | Dokumentacja firmy Microsoft
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f290a7e16938c66d45fab9b78086f77bfdfe4839
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319517"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Rozwiązywanie problemów z błędu ogólnego protokołu RDP w maszynie Wirtualnej platformy Azure

W tym artykule opisano ogólny błąd, które mogą wystąpić po nawiązaniu połączenia protokołu RDP (Remote Desktop) do Windows maszyn wirtualnych (VM) na platformie Azure.

## <a name="symptom"></a>Objaw

Po wprowadzeniu z połączeniem RDP z maszyną wirtualną okna, na platformie Azure może być wyświetlany następujący komunikat ogólny błąd:

**Pulpit zdalny nie może połączyć się z komputerem zdalnym dla jednego z następujących powodów:**

1. **Zdalny dostęp do serwera nie jest włączony.**

2. **Komputer zdalny jest wyłączona.**

3. **Komputer zdalny nie jest dostępny w sieci**

**Upewnij się, komputer zdalny jest włączone i połączone z siecią i włączenie dostępu zdalnego.**

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić z powodu następujących przyczyn:

### <a name="cause-1"></a>Przyczyny 1

Składnik protokołu RDP jest wyłączona w następujący sposób:

- Na poziomie składnika
- Na poziomie odbiornika
- Na serwerze terminali
- W roli hosta sesji usług pulpitu zdalnego

### <a name="cause-2"></a>Przyczyny 2

Usługi pulpitu zdalnego (TermService) nie jest uruchomiony.

### <a name="cause-3"></a>Przyczyny 3

Odbiornik protokołu RDP jest nieprawidłowo skonfigurowana.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Utwórz kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md), i [Dołącz dysk systemu operacyjnego do ratownictwa maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md), a następnie postępuj zgodnie z instrukcjami.

### <a name="serial-console"></a>Konsola szeregowa

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Krok 1: Otwórz wystąpienie CMD w konsoli szeregowej

1. Dostęp do [konsoli szeregowej](serial-console-windows.md) , wybierając **pomoc techniczna i rozwiązywanie problemów** > **Konsola szeregowa (wersja zapoznawcza)**. Jeśli ta funkcja jest włączona na maszynie Wirtualnej, możesz połączyć maszynę Wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Typ **CMD** uruchomić kanału można pobrać nazwy kanału.

3. Przełącz się do kanału, na którym uruchomiono wystąpienie CMD, w tym przypadku powinna być kanał 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Krok 2: Sprawdź wartości kluczy rejestru protokołu RDP:

1. Sprawdź, czy protokołu RDP zostało wyłączone przez zasady.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Jeśli zasady domeny istnieje, zostanie zastąpiony ustawień zasad lokalnych.
      - Jeśli zasady domeny określają, czy RDP jest wyłączona (1), a następnie zasady aktualizacji AD z kontrolera domeny.
      - Jeśli zasady domeny stwierdza, że protokół RDP jest włączony (0), aktualizacja nie jest potrzebna.
      - Jeśli zasady domeny nie istnieje, a zasady lokalne stany wyłączenia protokołu RDP (1), Włącz protokół RDP przy użyciu następującego polecenia: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Sprawdź bieżącą konfigurację serwera terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Jeśli polecenie zwróci wartość 0, serwer terminali jest wyłączone. Następnie należy włączyć serwera terminali w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Moduł serwera terminali ustawiono trybu opróżniania, jeśli serwer znajduje się w farmie serwerów terminali (usług pulpitu zdalnego lub Citrix). Sprawdź, czy bieżący tryb modułu serwera terminali

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Jeśli polecenie zwróci 1, moduł serwera terminali ustawiono trybu opróżniania. Następnie ustaw moduł do trybu pracy w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Sprawdź, czy użytkownik może łączyć się z serwera terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Polecenie zwraca wartość 1, nie można nawiązać serwera terminali. Następnie Włącz połączenie w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Sprawdź bieżącą konfigurację odbiornika protokołu RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Jeśli polecenie zwróci wartość 0, odbiornik protokołu RDP jest wyłączone. Następnie Włącz odbiornik w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Sprawdź, czy możesz nawiązać połączenie odbiornik protokołu RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Polecenie zwraca wartość 1, nie można nawiązać odbiornik protokołu RDP. Następnie Włącz połączenie w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Uruchom ponownie maszynę wirtualną.

8. Wyjdź z wystąpienia CMD, wpisując `exit`, a następnie naciśnij klawisz **Enter** dwa razy.

9. Uruchom ponownie maszynę Wirtualną, wpisując `restart`, a następnie nawiązać połączenie z maszyną wirtualną.

Jeśli problem nadal występuje, przejdź do kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Włączyć usługi pulpitu zdalnego

Aby uzyskać więcej informacji, zobacz [usług pulpitu zdalnego nie jest uruchamiania na Maszynie wirtualnej platformy Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetuj odbiornik protokołu RDP

Aby uzyskać więcej informacji, zobacz [pulpitu zdalnego rozłączy się często w maszynie Wirtualnej platformy Azure](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Naprawy w trybie offline

#### <a name="step-1-turn-on-remote-desktop"></a>Krok 1: Włączanie pulpitu zdalnego

1. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md).
2. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.
3. Upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.
4. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.
5. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**). Uruchom następujące skrypty. W tym skrypcie przyjęto założenie, że litery dysku, która jest przypisana do dołączonym dysku systemu operacyjnego jest F. Zastąp tę literę dysku z odpowiednią wartością dla maszyny Wirtualnej.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Jeśli maszyna wirtualna jest przyłączona do domeny, sprawdź następujący klucz rejestru, aby zobaczyć, czy zasady grupy, które spowoduje wyłączenie protokołu RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Jeśli ta wartość klucza jest równa 1, oznacza to, że protokół RDP jest wyłączona przez zasady. Aby włączyć Pulpit zdalny przy użyciu zasad obiektu zasad grupy, należy zmienić następujące zasady z kontrolera domeny:

   
      **Szablony w obszarze Konfiguracja komputera\Zasady\Szablony komputera:**

      Zasady definitions\Windows Windows\Usługi pulpitu usług pulpitu sesji Host\Connections\Allow użytkownikom łączyć się zdalnie za pomocą usług pulpitu zdalnego
  
1. Odłącz dysk od ratownictwa maszyny Wirtualnej.
1. [Tworzenie nowej maszyny Wirtualnej na podstawie dysku](../windows/create-vm-specialized.md).

Jeśli problem nadal występuje, przejdź do kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Włączyć usługi pulpitu zdalnego

Aby uzyskać więcej informacji, zobacz [usług pulpitu zdalnego nie jest uruchamiania na Maszynie wirtualnej platformy Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetuj odbiornik protokołu RDP

Aby uzyskać więcej informacji, zobacz [pulpitu zdalnego rozłączy się często w maszynie Wirtualnej platformy Azure](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
