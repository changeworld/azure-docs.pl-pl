---
title: Włączanie lub wyłączanie reguły zapory w systemie operacyjnym gościa na maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 7a547efb7af69c58f8e04615d24dd7c230f0c8b0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444645"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Włączanie lub wyłączanie reguły zapory w systemie operacyjnym gościa maszyny Wirtualnej platformy Azure

Ten artykuł zawiera odwołanie do rozwiązywania problemów w sytuacji, w której istnieje podejrzenie, że Zapora systemu operacyjnego gościa jest filtrowanie część ruchu na maszynie wirtualnej (VM). Może to być przydatne w następujących sytuacjach:

*   Jeśli celowo zmiany do zapory, który spowodował niepowodzenie połączenia RDP, za pomocą funkcji rozszerzenia niestandardowego skryptu można rozwiązać problem.

*   Wyłączanie wszystkich profilów zapory jest bardziej niezawodna sposób rozwiązywania problemów niż ustawienie reguły zapory specyficzne dla protokołu RDP.

## <a name="solution"></a>Rozwiązanie

Jak reguły zapory, zależy od poziomu dostępu do maszyny Wirtualnej, która jest wymagana. W poniższych przykładach używane reguły protokołu RDP. Te same metody mogą jednak stosowane do dowolnego rodzaju ruch poprzez wskazanie odpowiedni klucz rejestru.

### <a name="online-troubleshooting"></a>Rozwiązywanie problemów z usługi online 

#### <a name="mitigation-1-custom-script-extension"></a>Ograniczenie 1: Rozszerzenie niestandardowego skryptu

1.  Korzystając z poniższego szablonu, aby utworzyć skrypt.

    *   Aby włączyć regułę:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Aby wyłączyć regułę:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Przekaż ten skrypt w usłudze Azure portal przy użyciu [rozszerzenia niestandardowego skryptu](../extensions/custom-script-windows.md) funkcji. 

#### <a name="mitigation-2-remote-powershell"></a>Ograniczenie 2: Zdalnego programu PowerShell

Jeśli maszyna wirtualna jest w trybie online i jest dostępny w innej maszyny Wirtualnej w tej samej sieci wirtualnej, należy tworzyć środki zaradcze wykonaj, przy użyciu innej maszyny Wirtualnej.

1.  Na maszyny Wirtualnej rozwiązywania problemów Otwórz okno konsoli programu PowerShell.

2.  Uruchom następujące polecenia, zgodnie z potrzebami.

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

#### <a name="mitigation-3-pstools-commands"></a>Ograniczenie 3: Polecenia PSTools

Jeśli maszyna wirtualna jest w trybie online i jest dostępny w innej maszyny Wirtualnej w tej samej sieci wirtualnej, należy tworzyć środki zaradcze wykonaj, przy użyciu innej maszyny Wirtualnej.

1.  Na maszyny Wirtualnej rozwiązywania problemów pobrać [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Otwórz wystąpienie CMD, a dostęp do maszyny Wirtualnej za pośrednictwem jego wewnętrznego adresu IP (DIP). 

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

#### <a name="mitigation-4-remote-registry"></a>Ograniczenie 4: Rejestr zdalny

Jeśli maszyna wirtualna jest w trybie online i jest dostępny w innej maszyny Wirtualnej w tej samej sieci wirtualnej, możesz użyć [Rejestr zdalny](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) na maszynie Wirtualnej.

1.  W przypadku maszyny Wirtualnej rozwiązywania problemów Uruchom Edytor rejestru (regedit.exe), a następnie wybierz **pliku** > **rejestru połączenia sieciowego**.

2.  Otwórz *maszyna docelowa*\SYSTEM gałęzi, a następnie określ następujące wartości:

    * Aby włączyć regułę, otwórz następującą wartość rejestru:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Następnie zmień **Active = FALSE** do **Active = TRUE** w ciągu:

        **v2.22 | Akcja = Zezwalaj | Aktywne = TRUE | Dir = In | Protokół = 6 | Profile = Domain | Profil prywatny = | Profil publiczny = | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Nazwa =\@FirewallAPI.dll,-28775 | Desc =\@FirewallAPI.dll,-28756 | EmbedCtxt =\@FirewallAPI.dll,-28752 |**
    
    * Aby wyłączyć regułę, otwórz następującą wartość rejestru:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Następnie zmień **Active = TRUE** do **Active = FALSE**:
        
        **v2.22 | Akcja = Zezwalaj | Aktywne = FALSE | Dir = In | Protokół = 6 | Profile = Domain | Profil prywatny = | Profil publiczny = | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Nazwa =\@FirewallAPI.dll,-28775 | Desc =\@FirewallAPI.dll,-28756 | EmbedCtxt =\@FirewallAPI.dll,-28752 |**

3.  Uruchom ponownie maszynę Wirtualną, aby zastosować zmiany.

### <a name="offline-troubleshooting"></a>Rozwiązywanie problemów w trybie offline 

Jeśli nie masz dostępu maszyny Wirtualnej przez każdą metodę, przy użyciu rozszerzenia niestandardowego skryptu zakończy się niepowodzeniem, a trzeba będzie pracować w trybie OFFLINE dzięki pracy bezpośrednio w ramach dysku systemowego.

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemowego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

1.  [Dołącz dysk systemu do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

2.  Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.

3.  Upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Należy pamiętać, że dysk litery jest przypisana do dysku systemowego dołączone.

4.  Przed wprowadzeniem zmiany należy utworzyć kopię folderu \windows\system32\config w przypadku, gdy konieczne jest wycofanie zmian.

5.  Na maszyny Wirtualnej rozwiązywania problemów Uruchom Edytor rejestru (regedit.exe).

6.  Wyróżnij **HKEY_LOCAL_MACHINE** klucza, a następnie wybierz **pliku** > **Załaduj gałąź rejestru** z menu.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Znajdź, a następnie otwórz plik \windows\system32\config\SYSTEM. 

    > [!Note]
    > Zostanie wyświetlony monit o podanie nazwy. Wprowadź **BROKENSYSTEM**, a następnie rozwiń węzeł **HKEY_LOCAL_MACHINE**. Zostanie wyświetlona dodatkowy klucz o nazwie **BROKENSYSTEM**. Aby uzyskać procedury rozwiązywania problemów, firma Microsoft instalowania tych gałęzi problem jako **BROKENSYSTEM**.

8.  Wprowadź następujące zmiany w gałęzi BROKENSYSTEM:

    1.  Sprawdzić, która **ControlSet** klucz rejestru jest od maszyny Wirtualnej. Zostanie wyświetlony numer klucza HKLM\BROKENSYSTEM\Select\Current.

    2.  Aby włączyć regułę, otwórz następującą wartość rejestru:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Następnie zmień **Active = FALSE** do **Active = True**.
        
        **v2.22 | Akcja = Zezwalaj | Aktywne = TRUE | Dir = In | Protokół = 6 | Profile = Domain | Profil prywatny = | Profil publiczny = | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Nazwa =\@FirewallAPI.dll,-28775 | Desc =\@FirewallAPI.dll,-28756 | EmbedCtxt =\@FirewallAPI.dll,-28752 |**

    3.  Aby wyłączyć regułę, otwórz następujący klucz rejestru:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Następnie zmień **Active = True** do **Active = FALSE**.
        
        **v2.22 | Akcja = Zezwalaj | Aktywne = FALSE | Dir = In | Protokół = 6 | Profile = Domain | Profil prywatny = | Profil publiczny = | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Nazwa =\@FirewallAPI.dll,-28775 | Desc =\@FirewallAPI.dll,-28756 | EmbedCtxt =\@FirewallAPI.dll,-28752 |**

9.  Wyróżnij **BROKENSYSTEM**, a następnie wybierz pozycję **pliku** > **Zwolnij gałąź rejestru** z menu.

10. [Odłączanie dysku systemowego i ponowne utworzenie maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

11. Sprawdź, czy problem został rozwiązany.
