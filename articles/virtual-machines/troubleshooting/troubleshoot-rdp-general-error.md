---
title: Rozwiązywanie problemów z ogólnym błędem protokołu RDP na maszynie Wirtualnej systemu Windows na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem z ogólnym błędem protokołu RDP na maszynie Wirtualnej systemu Windows na platformie Azure | Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057998"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Rozwiązywanie problemów z ogólnym błędem protokołu RDP na maszynie Wirtualnej platformy Azure

W tym artykule opisano ogólny błąd, który może wystąpić podczas nawiązywać połączenie protokołu RDP (Remote Desktop Protocol) z maszyną wirtualną systemu Windows (VM) na platformie Azure.

## <a name="symptom"></a>Objaw

Po nawiązaniu połączenia RDP z maszyną wirtualną w oknie na platformie Azure może pojawić się następujący ogólny komunikat o błędzie:

**Pulpit zdalny nie może połączyć się z komputerem zdalnym z jednego z następujących powodów:**

1. **Dostęp zdalny do serwera nie jest włączony**

2. **Komputer zdalny jest wyłączony**

3. **Komputer zdalny nie jest dostępny w sieci**

**Upewnij się, że komputer zdalny jest włączony i podłączony do sieci, a dostęp zdalny jest włączony.**

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić z następujących przyczyn:

### <a name="cause-1"></a>Przyczyna 1

Składnik RDP jest wyłączony w następujący sposób:

- Na poziomie komponentu
- Na poziomie słuchacza
- Na serwerze terminali
- W roli Host sesji usług pulpitu zdalnego

### <a name="cause-2"></a>Przyczyna 2

Usługi pulpitu zdalnego (TermService) nie są uruchomione.

### <a name="cause-3"></a>Przyczyna 3

Odbiornik RDP jest nieprawidłowo skonfigurowany.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [wykonaj wykonaj zapasową kopii zapasowej dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md)i [dołącz dysk systemu operacyjnego do maszyny wirtualnej programu rescue,](troubleshoot-recovery-disks-portal-windows.md)a następnie wykonaj kroki.

### <a name="serial-console"></a>Konsola szeregowa

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Krok 1: Otwórz wystąpienie CMD w konsoli szeregowej

1. Dostęp do [konsoli szeregowej](serial-console-windows.md) można uzyskać, wybierając **pozycję Obsługa & konsoli** > rozwiązywania problemów z**serialem (Wersja zapoznawcza).** Jeśli funkcja jest włączona na maszynie Wirtualnej, można połączyć maszynę wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Wpisz **CMD,** aby uruchomić kanał, aby uzyskać nazwę kanału.

3. Przełącz się do kanału, który uruchamia wystąpienie CMD, w tym przypadku powinien być kanał 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Krok 2: Sprawdź wartości kluczy rejestru RDP:

1. Sprawdź, czy PROW jest wyłączony przez policję.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Jeśli zasady domeny istnieją, konfiguracja zasad lokalnych jest zastępowany.
      - Jeśli zasady domeny stwierdzają, że usługa RDP jest wyłączona (1), zaktualizuj zasady usługi AD z kontrolera domeny.
      - Jeśli zasady domeny stwierdza, że protokół RDP jest włączony (0), aktualizacja nie jest wymagana.
      - Jeśli zasady domeny nie istnieją, a zasady lokalne stwierdzają, że prow jest wyłączony (1), włącz prow. 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Sprawdź bieżącą konfigurację serwera terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Jeśli polecenie zwraca wartość 0, serwer terminali jest wyłączony. Następnie włącz serwer terminali w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Moduł serwera terminali jest ustawiony na tryb opróżniania, jeśli serwer znajduje się w farmie serwerów terminali (RDS lub Citrix). Sprawdź bieżący tryb modułu Serwera terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Jeśli polecenie zwraca wartość 1, moduł Serwera terminali jest ustawiony na tryb opróżniania. Następnie ustaw moduł w trybie pracy w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Sprawdź, czy można połączyć się z serwerem terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Jeśli polecenie zwraca wartość 1, nie można połączyć się z serwerem terminali. Następnie włącz połączenie w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Sprawdź bieżącą konfigurację odbiornika RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Jeśli polecenie zwraca wartość 0, odbiornik RDP jest wyłączony. Następnie włącz odbiornik w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Sprawdź, czy możesz połączyć się ze słuchaczem RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Jeśli polecenie zwraca wartość 1, nie można połączyć się z odbiornikiem RDP. Następnie włącz połączenie w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Uruchom ponownie maszynę wirtualną.

8. Wyjdź z instancji `exit`CMD, wpisując wpis , a następnie naciśnij klawisz **Enter** dwa razy.

9. Uruchom ponownie maszynę `restart`wirtualną, wpisując wpis ,, a następnie połącz się z maszyną wirtualną.

Jeśli problem nadal występuje, przejdź do kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Włączanie usług pulpitu zdalnego

Aby uzyskać więcej informacji, zobacz [Usługi pulpitu zdalnego nie uruchamia się na maszynie Wirtualnej platformy Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetowanie odbiornika RDP

Aby uzyskać więcej informacji, zobacz [Pulpit zdalny często rozłącza się w usłudze Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Naprawa w trybie offline

#### <a name="step-1-turn-on-remote-desktop"></a>Krok 1: Włączanie pulpitu zdalnego

1. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
2. Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.
3. Upewnij się, że dysk jest oflagowany jako **w trybie online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemu operacyjnego.
4. Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.
5. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**). Uruchom następujące skrypty. W tym skrypcie zakładamy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zamień tę literę dysku na odpowiednią wartość dla maszyny Wirtualnej.

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

6. Jeśli maszyna wirtualna jest przyłączona do domeny, sprawdź następujący klucz rejestru, aby sprawdzić, czy istnieje zasada grupy, która wyłączy prow. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Jeśli ta wartość klucza jest ustawiona na 1, co oznacza, że prow jest wyłączona przez zasady. Aby włączyć pulpit zdalny za pomocą zasad obiektu zasad grupy, zmień następujące zasady z kontrolera domeny:

   
      **Konfiguracja komputera\Zasady\Szablony administracyjne:**

      Definicje zasad\Składniki systemu Windows\Usługi pulpitu zdalnego\Host sesji usług pulpitu zdalnego\Połączenia\Zezwalaj użytkownikom na łączenie się zdalnie przy użyciu usług pulpitu zdalnego
  
1. Odłącz dysk od maszyny wirtualnej ratunkowej.
1. [Utwórz nową maszynę wirtualną z dysku](../windows/create-vm-specialized.md).

Jeśli problem nadal występuje, przejdź do kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Włączanie usług pulpitu zdalnego

Aby uzyskać więcej informacji, zobacz [Usługi pulpitu zdalnego nie uruchamia się na maszynie Wirtualnej platformy Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetowanie odbiornika RDP

Aby uzyskać więcej informacji, zobacz [Pulpit zdalny często rozłącza się w usłudze Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.
