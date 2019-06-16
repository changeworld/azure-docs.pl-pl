---
title: Pulpit zdalny rozłączy się często w maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z częstych odłączenia pulpitu zdalnego na maszynie Wirtualnej platformy Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: 7fecf8c5fdafb64f7922054dd2bb9755b0dec031
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60386180"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Pulpit zdalny rozłączy się często w maszynie Wirtualnej platformy Azure

W tym artykule wyjaśniono, jak rozwiązywać problemy z częstych odłączenia na maszynie wirtualnej platformy Azure (VM) za pośrednictwem protokołu RDP Remote Desktop).

> [!NOTE] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager. Zalecamy użycie tego modelu w przypadku nowych wdrożeń zamiast przy użyciu klasycznego modelu wdrażania.

## <a name="symptom"></a>Objaw

Sporadyczne problemy z łącznością RDP stają się podczas sesji użytkownika. Początkowo można połączyć z maszyny Wirtualnej, ale spadnie połączenia.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli odbiornik protokołu RDP jest nieprawidłowo skonfigurowana. Zazwyczaj ten problem występuje na maszynie Wirtualnej, który używa niestandardowego obrazu.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków [migawki dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md) dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. 

Aby rozwiązać ten problem, należy użyć formantu szeregowego lub [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline) , dołączając dysk systemu operacyjnego maszyny wirtualnej do maszyny Wirtualnej odzyskiwania.

### <a name="serial-control"></a>Kontrolka szeregowej

1. Połączyć się z [konsoli szeregowej i otwórz wystąpienie CMD](./serial-console-windows.md). Następnie uruchom następujące polecenia, aby zresetować konfiguracji RDP. Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, przejdź do następnego kroku.
2. Obniż warstwy zabezpieczeń RDP na 0. Na to ustawienie, komunikacja między serwerem a klientem za pomocą szyfrowanie natywne protokołu RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Obniż poziom szyfrowania do minimalnej ustawienie, aby umożliwić starszych klientów protokołu RDP do połączenia.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Ustaw protokołu RDP, aby załadować konfiguracji użytkownika komputera klienckiego.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Włącz protokół RDP Keep-Alive kontrolki:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Ustaw kontrolkę ponownie protokołu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Ustawić czas sesji protokołu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Ustawianie kontroli czasu rozłączenia protokołu RDP: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Ustawianie kontroli czasu połączenia RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Ustawianie kontroli czasu bezczynności sesji protokołu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Ustaw formant "Limit: Maksymalna liczba równoczesnych połączeń":

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Ponowne uruchomienie maszyny Wirtualnej i spróbuj ponownie, aby nawiązać z nim przy użyciu protokołu RDP.

### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb offline

1. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md).
2. Po dołączeniu dysku systemu operacyjnego do maszyny Wirtualnej odzyskiwania, upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.
3. Na dołączonym dysku systemu operacyjnego, przejdź do **\windows\system32\config** folderu. Skopiuj wszystkie pliki z tego folderu do przechowywania kopii zapasowych, w przypadku, gdy wymagane będzie wycofanie.
4. Uruchom Edytor rejestru (regedit.exe).
5. Wybierz **HKEY_LOCAL_MACHINE** klucza. W menu, wybierz **pliku** > **Załaduj gałąź rejestru**:
6. Przejdź do **\windows\system32\config\SYSTEM** folder na dołączonym dysku systemu operacyjnego. Nazwa gałęzi, wprowadź **BROKENSYSTEM**. Nowej gałęzi rejestru jest wyświetlana w obszarze **HKEY_LOCAL_MACHINE** klucza. Następnie załaduj gałąź oprogramowania **\windows\system32\config\SOFTWARE** w obszarze **HKEY_LOCAL_MACHINE** klucza. Nazwa oprogramowania hive, można wprowadzić **BROKENSOFTWARE**. 
7. Otwórz okno wiersza polecenia z podwyższonym (**Uruchom jako administrator**), a następnie uruchom polecenia w kolejnych krokach można zresetować konfiguracji RDP. 
8. Dolny warstwy zabezpieczeń RDP na 0, aby komunikacja między serwerem a klientem używać szyfrowanie natywne protokołu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Obniż poziom szyfrowania do minimalnej ustawienie, aby umożliwić starszych klientów protokołu RDP połączyć:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Ustaw protokołu RDP, aby załadować konfiguracji użytkownika z komputera klienckiego.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Włącz protokół RDP Keep-Alive kontrolki:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Ustaw kontrolkę ponownie protokołu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Ustawić czas sesji protokołu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Ustawianie kontroli czasu rozłączenia protokołu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Ustawianie kontroli czasu połączenia RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Ustawianie kontroli czasu bezczynności sesji protokołu RDP:     Polecenie REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP Tcp" /v "fInheritMaxIdleTime" /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Ustaw formant "Limit: Maksymalna liczba równoczesnych połączeń":

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Ponowne uruchomienie maszyny Wirtualnej i spróbuj ponownie, aby nawiązać z nim przy użyciu protokołu RDP.

## <a name="need-help"></a>Potrzebujesz pomocy? 
Skontaktuj się z pomocą techniczną. Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.





