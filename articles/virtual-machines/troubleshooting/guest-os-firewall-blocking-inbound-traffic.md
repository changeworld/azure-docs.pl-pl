---
title: Blokuje ruch przychodzący zaporę systemu operacyjnego gościa maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 0a0da446385c592bfeda2e01e209ef1fb75b7de3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711575"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Blokuje ruch przychodzący zaporę systemu operacyjnego gościa maszyny Wirtualnej platformy Azure

W tym artykule omówiono sposób rozwiązać ten problem Remote Desktop (Portal RDP), który występuje, jeśli ruch przychodzący Zapora blokuje systemu operacyjnego gościa.

## <a name="symptoms"></a>Objawy

Za pomocą połączenia RDP nie można połączyć z maszyną wirtualną (VM) platformy Azure. Z usługi rozruchu diagnostyki -> Zrzut ekranu, pokazuje, że system operacyjny jest w pełni załadowane na ekranie powitalnym (Ctrl + Alt + Del).

## <a name="cause"></a>Przyczyna

### <a name="cause-1"></a>Przyczyny 1

Aby zezwolić na ruch RDP nie skonfigurowano regułę protokołu RDP.

### <a name="cause-2"></a>Przyczyny 2

Profile zapory systemu gościa są skonfigurowane do blokowania wszystkich połączeń przychodzących, łącznie z ruchem RDP.

![Ustawienia zapory](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemowego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, użyj jednej z metod opisanych w [sposobu rozwiązywania problemów maszyny Wirtualnej platformy Azure przy użyciu narzędzi zdalnych](remote-tools-troubleshoot-azure-vm-issues.md) można połączyć z maszyną wirtualną, a następnie edytuj reguł zapory systemu operacyjnego gościa, które **Zezwalaj** ruch RDP .

### <a name="online-troubleshooting"></a>Rozwiązywanie problemów z usługi online

Połączyć się z [konsoli szeregowej, a następnie otwórz wystąpienie programu PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, przejdź do strony "[napraw Offline maszyny Wirtualnej](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Środki zaradcze 1

1.  Jeśli Azure Agent został zainstalowany i działa poprawnie na maszynie Wirtualnej, można użyć opcji "Tylko w przypadku konfiguracji resetowania" w obszarze **pomoc techniczna i rozwiązywanie problemów z** > **Resetuj hasło** w menu maszyny Wirtualnej.

2.  Uruchomienie tej opcji odzyskiwania wykonuje następujące czynności:

    *   Włącza składników protokołu RDP, jeśli jest ona wyłączona.

    *   Włącza wszystkie profile zapory Windows.

    *   Upewnij się, że reguła RDP jest włączony w Zaporze Windows.

    *   Jeśli poprzednie kroki nie zadziałają, ręcznie zresetować reguły zapory. W tym celu należy wykonać zapytanie o wszystkie reguły, które zawierają nazwę "Pulpit zdalny", uruchamiając następujące polecenie:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Jeżeli port protokołu RDP została skonfigurowana do innego portu niż 3389, należy znaleźć niestandardową regułę, która może być zostały utworzone i ustaw ten port. Aby wyszukać wszystkie reguły dla ruchu przychodzącego, które mają port niestandardowy, uruchom następujące polecenie:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Jeśli widzisz, że reguła jest wyłączona, należy ją włączyć. Aby otworzyć całej grupy, takie jak wbudowana grupa usług pulpitu zdalnego, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    W przeciwnym razie można otworzyć określonej pulpitu zdalnego (ruch przychodzący TCP) reguły, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Na potrzeby rozwiązywania problemów można włączyć profile zapory wyłączone:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Po zakończeniu rozwiązywania problemów i ustawienie zapory poprawnie, należy ponownie włączyć zapory.

    > [!Note]
    > Nie trzeba ponownie uruchomić maszynę Wirtualną, aby zastosować te zmiany.

5.  Spróbuj nawiązać połączenie RDP z dostęp do maszyny Wirtualnej.

#### <a name="mitigation-2"></a>Środki zaradcze 2

1.  Profile zapory, aby ustalić, czy zasady zapory dla ruchu przychodzącego są ustawione na zapytania *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Poniższe wskazówki dotyczą zasad zapory, w zależności od tego, jak to skonfigurować:
    >    * *BlockInbound*: Cały ruch przychodzący będzie blokowany, chyba że masz regułę obowiązuje zezwalającą na ruch.
    >    * *BlockInboundAlways*: Wszystkie reguły zapory zostanie zignorowany, a cały ruch będzie blokowany.

2.  Edytuj *DefaultInboundAction* ustawić te profile **Zezwalaj** ruchu. Aby to zrobić, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Wyślij zapytanie do profilów ponownie, aby upewnić się, że zmiana została wprowadzona pomyślnie. Aby to zrobić, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Nie trzeba ponownie uruchomić maszynę Wirtualną, aby zastosować zmiany.

4.  Dostęp do maszyny Wirtualnej za pośrednictwem protokołu RDP, spróbuj ponownie.

### <a name="offline-mitigations"></a>Środki zaradcze w trybie offline

1.  [Dołącz dysk systemu do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

2.  Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.

3.  Upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Należy pamiętać, literę dysku, która jest przypisana do dysku systemowego dołączone.

#### <a name="mitigation-1"></a>Środki zaradcze 1

Zobacz [sposób włączenia i wyłączenia zapory reguły w systemie operacyjnym gościa](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Środki zaradcze 2

1.  [Dołącz dysk systemu do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

2.  Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.

3.  Po dołączeniu dysku do maszyny Wirtualnej odzyskiwania, upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.

4.  Otwórz wystąpienie CMD z podwyższonym poziomem uprawnień, a następnie uruchom następujący skrypt:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Odłączanie dysku systemowego i ponowne utworzenie maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

6.  Sprawdź, czy problem został rozwiązany.
