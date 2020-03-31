---
title: Wyłączanie zapory systemu operacyjnego gościa na maszynie Wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: dbb9b0f865c7ec5d9d29e2310ae41abbec287bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464966"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Wyłączanie zapory systemu operacyjnego gościa na maszynie wirtualnej platformy Azure

Ten artykuł zawiera odwołanie do sytuacji, w których podejrzewasz, że zapora systemu operacyjnego gościa filtruje częściowy lub kompletny ruch na maszynie wirtualnej (VM). Może to nastąpić, jeśli celowo wprowadzono zmiany w zaporze, która spowodowała niepowodzenie połączeń RDP.

## <a name="solution"></a>Rozwiązanie

Proces opisany w tym artykule jest przeznaczony do użycia jako obejście, dzięki czemu można skupić się na naprawieniu rzeczywistego problemu, czyli jak poprawnie skonfigurować reguły zapory. Najlepszym rozwiązaniem firmy Microsoft jest włączenie składnika Zapora systemu Windows. Sposób konfigurowania reguł zapory zależy od wymaganego poziomu dostępu do maszyny Wirtualnej.

### <a name="online-solutions"></a>Rozwiązania online 

Jeśli maszyna wirtualna jest w trybie online i można uzyskać dostęp na innej maszynie wirtualnej w tej samej sieci wirtualnej, można wprowadzić te środki zaradcze przy użyciu innych maszyn wirtualnych.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Środki zaradcze 1: Niestandardowe rozszerzenie skryptu lub funkcja uruchom polecenie

Jeśli masz działającego agenta platformy Azure, możesz użyć [niestandardowego rozszerzenia skryptu](../extensions/custom-script-windows.md) lub funkcji [Uruchom polecenia](../windows/run-command.md) (tylko maszyny wirtualne Menedżera zasobów), aby zdalnie uruchomić następujące skrypty.

> [!Note]
> * Jeśli zapora jest ustawiona lokalnie, uruchom następujący skrypt:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Jeśli zapora jest ustawiona za pomocą zasad usługi Active Directory, można użyć następującego skryptu dostępu tymczasowego. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Jednak jak tylko zasady zostaną zastosowane ponownie, zostaniesz wyrzucony z sesji zdalnej. Stałą poprawką dla tego problemu jest zmodyfikowanie zasad zastosowanych na tym komputerze.

#### <a name="mitigation-2-remote-powershell"></a>Łagodzenie 2: Zdalny program PowerShell

1.  Połącz się z maszyną wirtualną, która znajduje się w tej samej sieci wirtualnej co maszyna wirtualna, do której nie można uzyskać, korzystając z połączenia RDP.

2.  Otwórz okno konsoli programu PowerShell.

3.  Uruchom następujące polecenia:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Jeśli zapora jest ustawiona za pośrednictwem obiektu zasad grupy, ta metoda może nie działać, ponieważ to polecenie zmienia tylko wpisy rejestru lokalnego. Jeśli zasada jest w miejscu, zastąpi tę zmianę. 

#### <a name="mitigation-3-pstools-commands"></a>Łagodzenie 3: POLECENIA PSTools

1.  Na maszynie wirtualnej rozwiązywania problemów pobierz [narzędzie PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Otwórz wystąpienie cmd, a następnie uzyskać dostęp do maszyny Wirtualnej za pośrednictwem dip.

3.  Uruchom następujące polecenia:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Łagodzenie 4: Rejestr zdalny 

Wykonaj następujące kroki, aby użyć [rejestru zdalnego](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  Na maszynie Wirtualnej rozwiązywania problemów uruchom edytor rejestru, a następnie przejdź do**rejestru sieciowego połączenia** **plików** > .

2.  Otwórz gałąź *TARGET MACHINE*\SYSTEM i określ następujące wartości:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Należy ponownie uruchomić usługę. Ponieważ nie można tego zrobić przy użyciu rejestru zdalnego, należy użyć Konsoli usług zdalnych.

4.  Otwórz wystąpienie **services.msc**.

5.  Kliknij **pozycję Usługi (lokalne)**.

6.  Wybierz pozycję **Połącz z innym komputerem**.

7.  Wprowadź **prywatny adres IP (DIP)** problemu maszyny Wirtualnej.

8.  Uruchom ponownie zasadę zapory lokalnej.

9.  Spróbuj ponownie połączyć się z maszyną wirtualną za pośrednictwem protokołu RDP z komputera lokalnego.

### <a name="offline-solutions"></a>Rozwiązania w trybie offline 

Jeśli masz sytuację, w której nie można dotrzeć do maszyny Wirtualnej za pomocą dowolnej metody, niestandardowe rozszerzenie skryptu zakończy się niepowodzeniem i będziesz musiał pracować w trybie OFFLINE, pracując bezpośrednio za pośrednictwem dysku systemowego. W tym celu wykonaj następujące kroki:

1.  [Podłącz dysk systemowy do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

2.  Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.

3.  Upewnij się, że dysk jest oflagowany jako w trybie online w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemowego.

4.  Przed wprowadzeniem jakichkolwiek zmian należy utworzyć kopię folderu \windows\system32\config na wypadek konieczności wycofania zmian.

5.  Na maszynie wirtualnej rozwiązywania problemów uruchom edytor rejestru (regedit.exe). 

6.  W przypadku tej procedury rozwiązywania problemów montujemy ule jako BROKENSYSTEM i BROKENSOFTWARE.

7.  Zaznacz klawisz HKEY_LOCAL_MACHINE, a następnie z menu wybierz polecenie Plik > Załaduj gałąź.

8.  Znajdź plik \windows\system32\config\SYSTEM na dołączonym dysku systemowym.

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
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
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

10. [Odłącz dysk systemowy i ponownie utwórz maszynę wirtualną](troubleshoot-recovery-disks-portal-windows.md).

11. Sprawdź, czy problem został rozwiązany.
