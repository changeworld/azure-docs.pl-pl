---
title: Pulpit zdalny często rozłącza się na platformie Azure VM| Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z częstymi rozłączaniami pulpitu zdalnego na maszynie Wirtualnej platformy Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: c22a401a6b25f7bb2c27a10e52214fa42ac6089b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918227"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Remote Desktop disconnects frequently in Azure VM (Pulpit zdalny często rozłącza się na maszynie wirtualnej platformy Azure)

W tym artykule wyjaśniono, jak rozwiązywać problemy z częstymi rozłączeniami z maszyną wirtualną platformy Azure za pośrednictwem protokołu RDP usługi pulpitu zdalnego.


## <a name="symptom"></a>Objaw

Podczas sesji występują sporadyczne problemy z łącznością rdp. Początkowo można połączyć się z maszyną wirtualną, ale następnie połączenie spada.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli odbiornik RDP jest nieprawidłowo skonfigurowany. Zazwyczaj ten problem występuje na maszynie Wirtualnej, która używa obrazu niestandardowego.

## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, [należy wykonać migawkę dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md) maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. 

Aby rozwiązać ten problem, należy użyć kontroli szeregowej lub [naprawić maszynę wirtualną w trybie offline,](#repair-the-vm-offline) dołączając dysk systemu operacyjnego maszyny wirtualnej do maszyny wirtualnej odzyskiwania.

### <a name="serial-control"></a>Sterowanie szeregowe

1. Połącz się z [konsolą szeregową i otwórz wystąpienie CMD](./serial-console-windows.md). Następnie uruchom następujące polecenia, aby zresetować konfiguracje RDP. Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, przejdź do następnego kroku.
2. Obniż warstwę zabezpieczeń RDP do 0. W tym ustawieniu komunikacja między serwerem a klientem używa natywnego szyfrowania RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Obniż poziom szyfrowania do ustawienia minimalnego, aby umożliwić starszym klientom RDP łączenie się.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Ustaw rdp, aby załadować konfigurację użytkownika komputera klienckiego.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Włącz kontrolę RDP Keep-Alive:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Ustaw kontrolę ponownego połączenia protokołu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Ustaw kontrolę czasu sesji protokołu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Ustaw kontrolę czasu rozłączenia prow: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Ustaw kontrolę czasu połączenia RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Ustaw kontrolę czasu bezczynności sesji protokołu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Ustaw kontrolę "Ogranicz maksymalną liczbę jednoczesnych połączeń":

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Uruchom ponownie maszynę wirtualną i spróbuj ponownie połączyć się z nią przy użyciu protokołu RDP.

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

1. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
2. Po dołączeniu dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemu operacyjnego.
3. Na podłączonym dysku systemu operacyjnego przejdź do folderu **\windows\system32\config.** Skopiuj wszystkie pliki w tym folderze jako kopię zapasową, w przypadku gdy wymagane jest wycofanie.
4. Uruchom Edytor rejestru (regedit.exe).
5. Wybierz **HKEY_LOCAL_MACHINE** klucz. W menu wybierz polecenie**Gałąź ładowania** **plików** > :
6. Przejdź do folderu **\windows\system32\config\SYSTEM** na dołączonym dysku systemu operacyjnego. Aby uzyskać nazwę gałęzi, wprowadź **BROKENSYSTEM**. Nowy gałąź rejestru jest wyświetlany pod kluczem **HKEY_LOCAL_MACHINE.** Następnie załaduj gałąź oprogramowania **\windows\system32\config\SOFTWARE** pod **HKEY_LOCAL_MACHINE** kluczem. Aby uzyskać nazwę oprogramowania hive, wprowadź **BROKENSOFTWARE**. 
7. Otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień **(Uruchom jako administrator)** i uruchom polecenia w pozostałych krokach, aby zresetować konfiguracje RDP. 
8. Obniż warstwę zabezpieczeń RDP do 0, aby komunikacja między serwerem a klientem używała natywnego szyfrowania RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Obniż poziom szyfrowania do ustawienia minimalnego, aby umożliwić starszym klientom RDP łączenie się:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Ustaw prow, aby załadować konfigurację użytkownika komputera klienckiego.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Włącz kontrolę RDP Keep-Alive:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Ustaw kontrolę ponownego połączenia protokołu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Ustaw kontrolę czasu sesji protokołu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Ustaw kontrolę czasu rozłączenia prow:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Ustaw kontrolę czasu połączenia RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Ustawianie kontroli czasu bezczynności sesji RDP: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Ustaw kontrolę "Ogranicz maksymalną liczbę jednoczesnych połączeń":

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Uruchom ponownie maszynę wirtualną i spróbuj ponownie połączyć się z nią przy użyciu protokołu RDP.

## <a name="need-help"></a>Potrzebujesz pomocy? 
Skontaktuj się z pomocą techniczną. Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.





