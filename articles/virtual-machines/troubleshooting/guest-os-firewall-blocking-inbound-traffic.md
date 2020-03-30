---
title: Zapora systemu operacyjnego gościa platformy Azure VM blokuje ruch przychodzący | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 0cbd1a24f5c460e248d55777735da6809befba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72028794"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Zapora systemu operacyjnego gościa platformy Azure VM blokuje ruch przychodzący

W tym artykule omówiono sposób rozwiązania problemu z portalem usług pulpitu zdalnego (RDP), który występuje, jeśli zapora systemu operacyjnego gościa blokuje ruch przychodzący.

## <a name="symptoms"></a>Objawy

Nie można użyć połączenia RDP do łączenia się z maszyną wirtualną platformy Azure (Maszyna wirtualna). Z diagnostyki rozruchowej -> Zrzut ekranu pokazuje, że system operacyjny jest w pełni załadowany na ekranie powitalnym (Ctrl +Alt+Del).

## <a name="cause"></a>Przyczyna

### <a name="cause-1"></a>Przyczyna 1

Reguła RDP nie jest skonfigurowana w celu zezwalania na ruch RDP.

### <a name="cause-2"></a>Przyczyna 2

Profile zapory systemowej gościa są skonfigurowane do blokowania wszystkich połączeń przychodzących, w tym ruchu RDP.

![Ustawienie zapory](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, należy wykonać migawkę dysku systemowego maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, użyj jednej z metod w [jak używać narzędzi zdalnych do rozwiązywania problemów z maszyną wirtualną platformy Azure,](remote-tools-troubleshoot-azure-vm-issues.md) aby połączyć się z maszyną wirtualną zdalnie, a następnie edytuj reguły zapory systemu operacyjnego gościa, aby **zezwolić na** ruch RDP.

### <a name="online-troubleshooting"></a>Rozwiązywanie problemów online

Połącz się z [konsolą szeregową, a następnie otwórz wystąpienie programu PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, przejdź do "[Napraw maszynę wirtualną w trybie offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Łagodzenie 1

1.  Jeśli usługa Azure Agent jest zainstalowana i działa poprawnie na maszynie Wirtualnej, można użyć opcji "Tylko resetowanie konfiguracji" w obszarze **Obsługa + rozwiązywanie problemów Resetowanie** > **hasła** w menu maszyny Wirtualnej.

2.  Uruchomienie tej opcji odzyskiwania wykonuje następujące czynności:

    *   Włącza składnik RDP, jeśli jest wyłączony.

    *   Włącza wszystkie profile zapory systemu Windows.

    *   Upewnij się, że reguła RDP jest włączona w Zaporze systemu Windows.

    *   Jeśli poprzednie kroki nie zadziałają, ręcznie zresetuj regułę zapory. Aby to zrobić, należy zbadać wszystkie reguły zawierające nazwę "Pulpit zdalny", uruchamiając następujące polecenie:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Jeśli port RDP został ustawiony na inny port inny niż 3389, musisz znaleźć dowolną regułę niestandardową, która mogła zostać utworzona i ustawiona na ten port. Aby zbadać wszystkie reguły przychodzące, które mają port niestandardowy, uruchom następujące polecenie:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Jeśli widzisz, że reguła jest wyłączona, włącz ją. Aby otworzyć całą grupę, taką jak wbudowana grupa Pulpitu zdalnego, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    W przeciwnym razie, aby otworzyć określoną regułę pulpitu zdalnego (TCP-In), uruchom następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Aby rozwiązać problemy, można wyłączyć profile zapory:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Po zakończeniu rozwiązywania problemów i prawidłowym ustawieniu zapory ponownie włącz zaporę.

    > [!Note]
    > Nie trzeba ponownie uruchomić maszynę wirtualną, aby zastosować te zmiany.

5.  Spróbuj nawiązać połączenie RDP, aby uzyskać dostęp do maszyny Wirtualnej.

#### <a name="mitigation-2"></a>Łagodzenie 2

1.  Kwerenda w profilach zapory, aby ustalić, czy zasada zapory przychodzącej jest ustawiona na *BlockInboundAlways:*

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Wszystkie profile](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Następujące wskazówki dotyczą zasad zapory, w zależności od sposobu jej skonfigurowania:
    >    * *BlockInbound:* Cały ruch przychodzący zostanie zablokowany, chyba że obowiązuje reguła zezwalana na ten ruch.
    >    * *BlockInboundAlways*: Wszystkie reguły zapory zostaną zignorowane, a cały ruch zostanie zablokowany.

2.  Edytuj *DefaultInboundAction,* aby ustawić te profile **na Zezwalaj na** ruch. Aby to zrobić, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Ponownie przekonuj profile, aby upewnić się, że zmiana została pomyślnie dokonana. Aby to zrobić, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Nie trzeba ponownie uruchomić maszynę wirtualną, aby zastosować zmiany.

4.  Spróbuj ponownie uzyskać dostęp do maszyny Wirtualnej za pośrednictwem protokołu RDP.

### <a name="offline-mitigations"></a>Środki zaradcze w trybie offline

1.  [Podłącz dysk systemowy do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

2.  Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.

3.  Upewnij się, że dysk jest oflagowany jako **w trybie online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemowego.

#### <a name="mitigation-1"></a>Łagodzenie 1

Zobacz [Jak włączyć i wyłączyć regułę zapory w szane.](enable-disable-firewall-rule-guest-os.md)

#### <a name="mitigation-2"></a>Łagodzenie 2

1.  [Podłącz dysk systemowy do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).

2.  Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.

3.  Po dołączeniu dysku systemowego do odzyskiwania maszyny Wirtualnej upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemu operacyjnego.

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

5.  [Odłącz dysk systemowy i ponownie utwórz maszynę wirtualną](troubleshoot-recovery-disks-portal-windows.md).

6.  Sprawdź, czy problem został rozwiązany.
