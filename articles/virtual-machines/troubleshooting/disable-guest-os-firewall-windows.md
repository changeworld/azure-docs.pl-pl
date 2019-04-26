---
title: Wyłącz gościa Zapora systemu operacyjnego na maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: a8856bd46f516aa3c64965648d4f23b9ba665b1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505465"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Wyłączanie zapory systemu operacyjnego gościa na maszynie wirtualnej platformy Azure

Ten artykuł zawiera odwołanie do sytuacji, w których istnieje podejrzenie, że Zapora systemu operacyjnego gościa jest filtrowanie ruchu częściowego lub ukończone z maszyną wirtualną (VM). To może wystąpić, jeśli celowo wprowadzono zmiany do zapory, która spowodowała połączenia RDP nie powiedzie się.

## <a name="solution"></a>Rozwiązanie

Proces, który jest opisany w tym artykule jest przeznaczona do użycia jako obejście tego problemu, dzięki czemu można skupić się nad rozwiązaniem problemu rzeczywistego problemu, czyli jak prawidłowo skonfigurować reguły zapory. S It\rquote najlepszym rozwiązaniem firmy Microsoft, aby mieć włączony składnik zapory Windows. Jak skonfigurować \cf3 reguły zapory zależy od poziomu dostępu do s that\rquote maszyn wirtualnych, które są wymagane.

### <a name="online-solutions"></a>Rozwiązania w trybie online 

Jeśli maszyna wirtualna jest w trybie online i jest dostępny w innej maszyny Wirtualnej w tej samej sieci wirtualnej, możesz tworzyć te środki zaradcze, przy użyciu innej maszyny Wirtualnej.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Ograniczenie 1: Niestandardowe rozszerzenie skryptu lub uruchom polecenie funkcji

Jeśli masz już działający agenta platformy Azure, możesz użyć [rozszerzenia niestandardowego skryptu](../extensions/custom-script-windows.md) lub [uruchamianie poleceń](../windows/run-command.md) funkcję (tylko maszyny wirtualne usługi Resource Manager), aby zdalnie korzystać z poniższych skryptów.

> [!Note]
> * Jeśli Zapora jest ustawiona lokalnie, uruchom następujący skrypt:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Jeśli Zapora jest ustawiona przy użyciu zasad usługi Active Directory, możesz użyć Uruchom następujący skrypt do tymczasowego dostępu. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Jednak zaraz po ponownym zastosowaniem zasady będzie można się z sesji zdalnej. Trwałe rozwiązanie tego problemu jest do modyfikowania zasad, które są stosowane na tym komputerze.

#### <a name="mitigation-2-remote-powershell"></a>Ograniczenie 2: Zdalnego programu PowerShell

1.  Łączenie z maszyną Wirtualną, która znajduje się w tej samej sieci wirtualnej co maszyna wirtualna, która nie można nawiązać połączenia przy użyciu połączenia RDP.

2.  Otwórz okno konsoli programu PowerShell.

3.  Uruchom następujące polecenia:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Jeśli Zapora jest ustawiona za pomocą obiektu zasad grupy, ta metoda może nie działać, ponieważ to polecenie powoduje zmianę tylko wpisy rejestru lokalnego. Jeśli zasady są spełnione, zastąpią tę zmianę. 

#### <a name="mitigation-3-pstools-commands"></a>Ograniczenie 3: Polecenia PSTools

1.  Na maszyny Wirtualnej rozwiązywania problemów pobrać [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Otwórz wystąpienie CMD, a następnie dostęp do maszyny Wirtualnej przy użyciu jego adresu DIP.

3.  Uruchom następujące polecenia:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Ograniczenie 4: Rejestr zdalny 

Wykonaj następujące kroki, aby użyć [Rejestr zdalny](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  W przypadku maszyny Wirtualnej rozwiązywania problemów Uruchom Edytor rejestru, a następnie przejdź do **pliku** > **rejestru połączenia sieciowego**.

2.  Otwórz *maszyna docelowa*\SYSTEM gałęzi, a następnie określ następujące wartości:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Uruchom ponownie usługę. Ponieważ nie możesz tego zrobić za pomocą Rejestr zdalny, należy użyć konsoli Usuń usługi.

4.  Otwórz wystąpienie **Services.msc**.

5.  Kliknij przycisk **usługi (lokalne)**.

6.  Wybierz **Podłącz do innego komputera**.

7.  Wprowadź **prywatny adres IP (DIP)** problemu maszyny Wirtualnej.

8.  Uruchom ponownie zasady lokalne zapory.

9.  Spróbuj ponownie nawiąż połączenie z maszyną wirtualną za pośrednictwem protokołu RDP z komputera lokalnego.

### <a name="offline-solutions"></a>Rozwiązania w trybie offline 

W przypadku sytuacji, w którym maszyna wirtualna nie można osiągnąć przez dowolną metodę rozszerzenia niestandardowego skryptu zakończy się niepowodzeniem, a trzeba będzie pracować w trybie OFFLINE dzięki pracy bezpośrednio w ramach dysku systemowego. W tym celu wykonaj następujące kroki:

1.  [Dołącz dysk systemu do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

2.  Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.

3.  Upewnij się, że dysk zostanie oflagowana jako w trybie Online w konsoli Zarządzanie dyskami. Należy pamiętać, literę dysku, która jest przypisana do dysku systemowego dołączone.

4.  Przed wprowadzeniem zmiany należy utworzyć kopię folderu \windows\system32\config w przypadku, gdy konieczne jest wycofanie zmian.

5.  Na maszyny Wirtualnej rozwiązywania problemów Uruchom Edytor rejestru (regedit.exe). 

6.  Do wykonania tej procedury rozwiązywania problemów firma Microsoft instalacji gałęzi jako BROKENSYSTEM i BROKENSOFTWARE.

7.  Zaznacz w kluczu HKEY_LOCAL_MACHINE, a następnie wybierz pozycję Plik > Załaduj gałąź rejestru z menu.

8.  Zlokalizuj plik \windows\system32\config\SYSTEM na dołączonym dysku.

9.  Otwórz wystąpienie programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenia:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Odłączanie dysku systemowego i ponowne utworzenie maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

11. Sprawdź, czy problem został rozwiązany.
