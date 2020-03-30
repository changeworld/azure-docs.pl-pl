---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183693"
---
W zależności od systemu operacyjnego klienta procedury zdalnego łączenia się z urządzeniem są różne.

### <a name="remotely-connect-from-a-windows-client"></a>Zdalne łączenie się z klientem systemu Windows

Przed rozpoczęciem upewnij się, że na kliencie systemu Windows jest uruchomiony program Windows PowerShell 5.0 lub nowszy.

Wykonaj następujące kroki, aby zdalnie połączyć się z klientem systemu Windows.

1. Uruchamianie sesji programu Windows PowerShell jako administrator.
2. Upewnij się, że usługa zdalnego zarządzania systemem Windows jest uruchomiona na kliencie. W wierszu polecenia wpisz polecenie:

    `winrm quickconfig`

3. Przypisz zmienną do adresu IP urządzenia.

    $ip = "<device_ip>"

    Zamień `<device_ip>` na adres IP urządzenia.

4. Aby dodać adres IP urządzenia do listy zaufanych hostów klienta, wpisz następujące polecenie:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Uruchom sesję programu Windows PowerShell na urządzeniu:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Po wyświetleniu monitu podaj hasło. Użyj tego samego hasła, które jest używane do logowania się do lokalnego interfejsu użytkownika sieci Web. Domyślnym lokalnym hasłem interfejsu użytkownika sieci Web jest *Password1*. Po pomyślnym połączeniu się z urządzeniem przy użyciu zdalnego programu PowerShell są widoczne następujące przykładowe dane wyjściowe:  

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

### <a name="remotely-connect-from-a-linux-client"></a>Zdalne łączenie z klienta systemu Linux

Na kliencie Systemu Linux, którego użyjesz do połączenia:

- [Zainstaluj najnowszy program PowerShell Core dla systemu Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) z GitHub, aby uzyskać funkcję komunikacji zdalnej SSH. 
- [Zainstaluj tylko `gss-ntlmssp` pakiet z modułu NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). W przypadku klientów Ubuntu użyj następującego polecenia:
    - `sudo apt-get install gss-ntlmssp`

Aby uzyskać więcej informacji, przejdź do programu [PowerShell komunikacji zdalnej za sprawą SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Wykonaj następujące kroki, aby zdalnie połączyć się z klientem NFS.

1. Aby otworzyć sesję programu PowerShell, wpisz:

    `sudo pwsh`
 
2. Aby połączyć się za pomocą klienta zdalnego, wpisz:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Po wyświetleniu monitu podaj hasło używane do logowania się do urządzenia.
 
> [!NOTE]
> Ta procedura nie działa w systemie Mac OS.
