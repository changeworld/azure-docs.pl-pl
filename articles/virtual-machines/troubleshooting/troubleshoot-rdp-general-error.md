---
title: Rozwiązywanie problemów z ogólnym błędem protokołu RDP na maszynie wirtualnej z systemem Windows na platformie Azure | Microsoft Docs
description: Informacje na temat rozwiązywania problemów z ogólnym błędem protokołu RDP na maszynie wirtualnej z systemem Windows na platformie Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: abff12e1a304c51cb0df394534c7da0a35518008
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089793"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Rozwiązywanie problemów z ogólnym błędem protokołu RDP na maszynie wirtualnej platformy Azure

W tym artykule opisano ogólny błąd, który może wystąpić podczas nawiązywania połączenia Remote Desktop Protocol (RDP) z maszyną wirtualną z systemem Windows na platformie Azure.

## <a name="symptom"></a>Objaw

Po nawiązaniu połączenia RDP z maszyną wirtualną z systemem Windows w systemie Azure może zostać wyświetlony następujący ogólny komunikat o błędzie:

**Pulpit zdalny nie może połączyć się z komputerem zdalnym dla jednego z następujących powodów:**

1. **Zdalny dostęp do serwera nie jest włączony.**

2. **Komputer zdalny jest wyłączona.**

3. **Komputer zdalny nie jest dostępny w sieci**

**Upewnij się, komputer zdalny jest włączone i połączone z siecią i włączenie dostępu zdalnego.**

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić z następujących powodów:

### <a name="cause-1"></a>Przyczyny 1

Składnik RDP jest wyłączony w następujący sposób:

- Na poziomie składnika
- Na poziomie odbiornika
- Na serwerze terminali
- W roli hosta sesji Pulpit zdalny

### <a name="cause-2"></a>Przyczyny 2

Usługi pulpitu zdalnego (TermService) nie jest uruchomiony.

### <a name="cause-3"></a>Przyczyna 3

Odbiornik RDP jest nieprawidłowo skonfigurowany.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Wykonaj kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md)i [Dołącz dysk systemu operacyjnego do ratowniczej maszyny wirtualnej](troubleshoot-recovery-disks-portal-windows.md), a następnie postępuj zgodnie z instrukcjami.

### <a name="serial-console"></a>Konsola szeregowa

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Krok 1: Otwórz wystąpienie CMD w Konsola szeregowa

1. Uzyskaj dostęp do [konsoli szeregowej](serial-console-windows.md) , wybierając pozycję **Obsługa & Rozwiązywanie problemów** > **konsola szeregowa (wersja zapoznawcza)** . Jeśli funkcja jest włączona na maszynie wirtualnej, można połączyć maszynę wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Wpisz **polecenie cmd** , aby uruchomić kanał, aby uzyskać nazwę kanału.

3. Przejdź do kanału, na którym działa wystąpienie CMD, w tym przypadku powinna to być kanał 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Krok 2: Sprawdź wartości kluczy rejestru RDP:

1. Sprawdź, czy protokół RDP jest wyłączony przez zasady.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Jeśli zasady domeny istnieją, Instalator zasad lokalnych zostanie nadpisany.
      - Jeśli zasady domeny określają, że protokół RDP jest wyłączony (1), zaktualizuj zasady usługi AD na kontrolerze domeny.
      - Jeśli zasady domeny określają, że protokół RDP jest włączony (0), aktualizacja nie jest wymagana.
      - Jeśli zasady domeny nie istnieją i lokalne Stany zasad, że protokół RDP jest wyłączony (1), Włącz protokół RDP przy użyciu następującego polecenia: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Sprawdź bieżącą konfigurację serwera terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Jeśli polecenie zwróci wartość 0, serwer terminali jest wyłączony. Następnie Włącz serwer terminali w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Jeśli serwer znajduje się w farmie serwerów terminali (RDS lub Citrix), moduł serwera terminali jest ustawiony na tryb opróżniania. Sprawdź bieżący tryb modułu serwera terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Jeśli polecenie zwróci wartość 1, moduł serwera terminali jest ustawiony na opróżnianie. Następnie ustaw moduł na tryb roboczy w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Sprawdź, czy można nawiązać połączenie z serwerem terminali.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Jeśli polecenie zwróci wartość 1, nie można nawiązać połączenia z serwerem terminali. Następnie Włącz połączenie w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Sprawdź bieżącą konfigurację odbiornika RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Jeśli polecenie zwróci wartość 0, odbiornik RDP jest wyłączony. Następnie Włącz odbiornik w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Sprawdź, czy można nawiązać połączenie z odbiornikiem RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Jeśli polecenie zwróci wartość 1, nie można nawiązać połączenia z odbiornikiem RDP. Następnie Włącz połączenie w następujący sposób:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Uruchom ponownie maszynę wirtualną.

8. Wyjdź z wystąpienia cmd, wpisując `exit`polecenie, a następnie naciskając dwa razy klawisz **Enter** .

9. Uruchom ponownie maszynę wirtualną `restart`, wpisując polecenie, a następnie nawiąż połączenie z maszyną wirtualną.

Jeśli problem nadal występuje, przejdź do kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Włącz usługi pulpitu zdalnego

Aby uzyskać więcej informacji, zobacz [usługi pulpitu zdalnego nie jest uruchamiane na maszynie wirtualnej platformy Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetuj odbiornik RDP

Aby uzyskać więcej informacji, zobacz [pulpit zdalny rozłącza często na maszynie wirtualnej platformy Azure](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Naprawa w trybie offline

#### <a name="step-1-turn-on-remote-desktop"></a>Krok 1: Włącz Pulpit zdalny

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

6. Jeśli maszyna wirtualna jest przyłączona do domeny, Sprawdź następujący klucz rejestru, aby sprawdzić, czy istnieją zasady grupy, które wyłączają protokół RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Jeśli wartość tego klucza jest ustawiona na 1, oznacza to, że protokół RDP jest wyłączony przez zasady. Aby włączyć Pulpit zdalny za pomocą zasad obiektu zasad grupy, Zmień następujące zasady z kontrolera domeny:

   
      **Szablony biokonfiguracja komputera:**

      Zasady definitions\Windows \ usługi \ usługi pulpitu Zdalnego\host sesji pulpitu Host\Connections\Allow użytkownikom do zdalnego nawiązywania połączenia przy użyciu Usługi pulpitu zdalnego
  
1. Odłącz dysk od ratowniczej maszyny wirtualnej.
1. [Utwórz nową maszynę wirtualną na podstawie dysku](../windows/create-vm-specialized.md).

Jeśli problem nadal występuje, przejdź do kroku 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Krok 2: Włącz usługi pulpitu zdalnego

Aby uzyskać więcej informacji, zobacz [usługi pulpitu zdalnego nie jest uruchamiane na maszynie wirtualnej platformy Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Krok 3: Resetuj odbiornik RDP

Aby uzyskać więcej informacji, zobacz [pulpit zdalny rozłącza często na maszynie wirtualnej platformy Azure](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.
