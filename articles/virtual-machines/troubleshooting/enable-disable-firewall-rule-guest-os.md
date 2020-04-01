---
title: Włączanie lub wyłączanie reguły zapory w szane gołym os w usłudze Azure VM | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać narzędzi zdalnych lub ustawień rejestru w trybie online lub offline do włączania lub wyłączania reguł zapory systemu operacyjnego gościa na zdalnej maszynie wirtualnej platformy Azure.
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
ms.openlocfilehash: e93dbd085ce99b8d555d6b9bb04e7eb6f60de0ee
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422896"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Włączanie lub wyłączanie reguły zapory w systemie operacyjnym gościa maszyny wirtualnej platformy Azure

Ten artykuł zawiera odwołanie do rozwiązywania problemów z sytuacją, w której można podejrzewać, że zapora systemu operacyjnego gościa filtruje częściowy ruch na maszynie wirtualnej (VM). Może to być przydatne z następujących powodów:

*   Jeśli celowo dokonano zmiany w zaporze, która spowodowała niepowodzenie połączeń RDP, użycie funkcji Niestandardowe rozszerzenie skryptu może rozwiązać ten problem.

*   Wyłączenie wszystkich profili zapory jest bardziej niezawodnym sposobem rozwiązywania problemów niż ustawienie reguły zapory specyficznej dla protokołu RDP.

## <a name="solution"></a>Rozwiązanie

Sposób konfigurowania reguł zapory zależy od poziomu dostępu do maszyny Wirtualnej, który jest wymagany. W poniższych przykładach użyto reguł PROW. Jednak te same metody mogą być stosowane do innego rodzaju ruchu, wskazując poprawny klucz rejestru.

### <a name="online-troubleshooting"></a>Rozwiązywanie problemów online 

#### <a name="mitigation-1-custom-script-extension"></a>Środki zaradcze 1: Niestandardowe rozszerzenie skryptu

1.  Utwórz skrypt przy użyciu następującego szablonu.

    *   Aby włączyć regułę:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Aby wyłączyć regułę:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Przekaż ten skrypt w witrynie Azure portal przy użyciu funkcji [rozszerzenia skryptu niestandardowego.](../extensions/custom-script-windows.md) 

#### <a name="mitigation-2-remote-powershell"></a>Łagodzenie 2: Zdalny program PowerShell

Jeśli maszyna wirtualna jest w trybie online i można uzyskać dostęp na innej maszynie wirtualnej w tej samej sieci wirtualnej, można wprowadzić następujące środki zaradcze przy użyciu innych maszyn wirtualnych.

1.  Na maszynie wirtualnej rozwiązywania problemów otwórz okno konsoli programu PowerShell.

2.  Uruchom następujące polecenia, odpowiednio.

    *   Aby włączyć regułę:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Aby wyłączyć regułę:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Łagodzenie 3: POLECENIA PSTools

Jeśli maszyna wirtualna jest w trybie online i można uzyskać dostęp na innej maszynie wirtualnej w tej samej sieci wirtualnej, można wprowadzić następujące środki zaradcze przy użyciu innych maszyn wirtualnych.

1.  Na maszynie wirtualnej rozwiązywania problemów pobierz [narzędzie PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Otwórz wystąpienie cmd i uzyskać dostęp do maszyny Wirtualnej za pośrednictwem jego wewnętrzny adres IP (DIP). 

    * Aby włączyć regułę:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Aby wyłączyć regułę:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Łagodzenie 4: Rejestr zdalny

Jeśli maszyna wirtualna jest w trybie online i można uzyskać dostęp na innej maszynie wirtualnej w tej samej sieci [wirtualnej,](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) można użyć rejestru zdalnego na innej maszynie wirtualnej.

1.  Na maszynie Wirtualnej rozwiązywania problemów uruchom Edytor rejestru (regedit.exe), a następnie wybierz pozycję**Rejestr sieciowy łączenia** **plików** > .

2.  Otwórz gałąź *TARGET MACHINE*\SYSTEM, a następnie określ następujące wartości:

    * Aby włączyć regułę, otwórz następującą wartość rejestru:
    
        *MASZYNA DOCELOWA*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Następnie zmień **Active=FALSE** na **Active=TRUE** w ciągu:

        **wersja 2.22| Action=Zezwalaj| Active=PRAWDA| Dir=W| Protokół=6| Profil=Domena| Profil=Prywatny| Profil=Publiczny| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Nazwa\@= FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| BedCtxt=\@FirewallAPI.dll,-28752|**
    
    * Aby wyłączyć regułę, otwórz następującą wartość rejestru:
    
        *MASZYNA DOCELOWA*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Następnie zmień **Active =TRUE** na **Active=FALSE**:
        
        **wersja 2.22| Action=Zezwalaj| Active=FAŁSZ| Dir=W| Protokół=6| Profil=Domena| Profil=Prywatny| Profil=Publiczny| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Nazwa\@= FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| BedCtxt=\@FirewallAPI.dll,-28752|**

3.  Uruchom ponownie maszynę wirtualną, aby zastosować zmiany.

### <a name="offline-troubleshooting"></a>Rozwiązywanie problemów w trybie offline 

Jeśli nie można uzyskać dostępu do maszyny Wirtualnej za pomocą dowolnej metody, przy użyciu niestandardowego rozszerzenia skryptu zakończy się niepowodzeniem i trzeba będzie pracować w trybie OFFLINE, pracując bezpośrednio za pośrednictwem dysku systemowego.

Przed wykonać następujące kroki, należy wykonać migawkę dysku systemowego maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).

1.  [Podłącz dysk systemowy do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

2.  Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.

3.  Upewnij się, że dysk jest oflagowany jako **w trybie online** w konsoli Zarządzanie dyskami. Należy zauważyć, że litera dysku przypisana do dołączonego dysku systemowego.

4.  Przed wprowadzeniem jakichkolwiek zmian należy utworzyć kopię folderu \windows\system32\config na wypadek konieczności wycofania zmian.

5.  Na maszynie wirtualnej rozwiązywania problemów uruchom Edytor rejestru (regedit.exe).

6.  Zaznacz klawisz **HKEY_LOCAL_MACHINE,** a następnie wybierz z menu pozycję**Gałąź obciążenia** **pliku.** > 

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Znajdź, a następnie otwórz plik \windows\system32\config\SYSTEM. 

    > [!Note]
    > Zostanie wyświetlony monit o podanie nazwy. Wprowadź **BROKENSYSTEM**, a następnie rozwiń **HKEY_LOCAL_MACHINE**. Zostanie wyświetlony dodatkowy klucz o nazwie **BROKENSYSTEM**. Do tego rozwiązywania problemów, montujemy te pokrzywki problem jako **BROKENSYSTEM**.

8.  Wykonuj następujące zmiany w gałęzi BROKENSYSTEM:

    1.  Sprawdź, od którego klucza rejestru **ControlSet** jest uruchamiany maszyna wirtualna. Jego numer klucza zostanie wyświetlony w HKLM\BROKENSYSTEM\Select\Current.

    2.  Aby włączyć regułę, otwórz następującą wartość rejestru:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Następnie zmień **Active=FALSE** na **Active=True**.
        
        **wersja 2.22| Action=Zezwalaj| Active=PRAWDA| Dir=W| Protokół=6| Profil=Domena| Profil=Prywatny| Profil=Publiczny| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Nazwa\@= FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| BedCtxt=\@FirewallAPI.dll,-28752|**

    3.  Aby wyłączyć regułę, otwórz następujący klucz rejestru:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Następnie zmień **Active=True** na **Active=FALSE**.
        
        **wersja 2.22| Action=Zezwalaj| Active=FAŁSZ| Dir=W| Protokół=6| Profil=Domena| Profil=Prywatny| Profil=Publiczny| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Nazwa\@= FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| BedCtxt=\@FirewallAPI.dll,-28752|**

9.  Zaznacz **pozycję BROKENSYSTEM**, a następnie z menu wybierz polecenie**Rozładuj** **plik.** > 

10. [Odłącz dysk systemowy i ponownie utwórz maszynę wirtualną](troubleshoot-recovery-disks-portal-windows.md).

11. Sprawdź, czy problem został rozwiązany.
