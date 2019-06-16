---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161199"
---
W zależności od systemu operacyjnego klienta procedury, aby zdalnie połączyć się z urządzeniem różnią się.

### <a name="remotely-connect-from-a-windows-client"></a>Połączenie zdalne z klienta Windows

Przed rozpoczęciem upewnij się, klienta Windows jest uruchomiony program Windows PowerShell 5.0 lub nowszy.

Wykonaj następujące kroki na połączenie zdalne z klienta Windows.

1. Uruchom sesję programu Windows PowerShell jako administrator.
2. Upewnij się, że usługa Windows Remote Management jest uruchomiona na komputerze klienckim. W wierszu polecenia wpisz polecenie:

    `winrm quickconfig`

3. Przypisz zmienną do adresu IP urządzenia.

    $ip = "< device_ip >"

    Zastąp `<device_ip>` przy użyciu adresu IP urządzenia.

4. Aby dodać adres IP urządzenia, do listy zaufanych hostów klienta, wpisz następujące polecenie:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Uruchom sesję programu Windows PowerShell na urządzeniu:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Podaj hasło po wyświetleniu monitu. Użyj tego samego hasła, która jest używana do logowania się do lokalnego Interfejsu w przeglądarce. Hasło domyślnego lokalnego internetowego interfejsu użytkownika jest *Password1*. Po pomyślnym nawiązaniu połączenia z urządzeniem przy użyciu zdalnego programu PowerShell, zostanie wyświetlony następujące przykładowe dane wyjściowe:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Połączenie zdalne z klienta systemu Linux

Na komputerze klienckim systemu Linux, która będzie używana do łączenia:

- [Zainstaluj najnowszy program PowerShell Core dla systemu Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) z witryny GitHub można pobrać funkcja komunikacji zdalnej SSH. 
- [Zainstaluj tylko `gss-ntlmssp` pakietu z modułu NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Dla klientów z systemem Ubuntu Użyj następującego polecenia:
    - `sudo apt-get install gss-ntlmssp`

Aby uzyskać więcej informacji, przejdź do [komunikacji zdalnej programu PowerShell za pomocą protokołu SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Wykonaj następujące kroki na połączenie zdalne z klienta systemu plików NFS.

1. Aby otworzyć sesję programu PowerShell, wpisz:

    `sudo pwsh`
 
2. Łączenie, za pomocą zdalnego klienta, wpisz:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Po wyświetleniu monitu podaj hasło użyte do logowania się do urządzenia.
 
> [!NOTE]
> Ta procedura nie działa w systemie Mac OS.
