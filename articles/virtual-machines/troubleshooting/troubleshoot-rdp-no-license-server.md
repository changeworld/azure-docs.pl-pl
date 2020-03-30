---
title: Serwer licencji usług pulpitu zdalnego jest niedostępny po nawiązaniu połączenia z maszyną wirtualną platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problemy z niepowodzeniem protokołu RDP, ponieważ nie jest dostępny serwer licencji usług pulpitu zdalnego | Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71088523"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Serwer licencji usług pulpitu zdalnego jest niedostępny po nawiązaniu połączenia z maszyną wirtualną platformy Azure

Ten artykuł pomaga rozwiązać ten problem, gdy nie można połączyć się z maszyną wirtualną platformy Azure (VM), ponieważ nie jest dostępny żaden serwer licencji usług pulpitu zdalnego, aby zapewnić licencję.

## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną (VM) występują następujące scenariusze:

- Zrzut ekranu maszyny Wirtualnej pokazuje, że system operacyjny jest w pełni załadowany i oczekuje na poświadczenia.
- Podczas próby nawiązywać połączenia z protokołem RDP (RDP) są wysyłane następujące komunikaty o błędach:

  - Sesja zdalna została rozłączona, ponieważ nie ma dostępnych serwerów licencji usług pulpitu zdalnego do dostarczenia licencji.

  - Nie jest dostępny żaden serwer licencji usług pulpitu zdalnego. Usługi pulpitu zdalnego przestaną działać, ponieważ ten komputer po upływie okresu prolongaty i nie skontaktował się z co najmniej prawidłowym serwerem licencji systemu Windows Server 2008. Wybierz ten komunikat, aby otworzyć konfigurację serwera hosta sesji usług pulpitu zdalnego, aby użyć diagnostyki licencjonowania.

Można jednak normalnie połączyć się z maszyną wirtualną przy użyciu sesji administracyjnej:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli serwer licencji usług pulpitu zdalnego jest niedostępny w celu udzielenia licencji na uruchomienie sesji zdalnej. Może to być spowodowane przez kilka scenariuszy, nawet jeśli rola hosta sesji usług pulpitu zdalnego została skonfigurowana na maszynie Wirtualnej:

- Nigdy nie było roli licencjonowania usług pulpitu zdalnego w środowisku, a okres prolongaty, 180 dni, jest skończona.
- Licencja pulpitu zdalnego została zainstalowana w środowisku, ale nigdy nie została aktywowana.
- Licencja pulpitu zdalnego w środowisku nie ma licencji dostępu klienta (CAL) wstrzykniętych w celu skonfigurowania połączenia.
- Licencja pulpitu zdalnego została zainstalowana w środowisku. Dostępne są licencje CAL, ale nie zostały poprawnie skonfigurowane.
- Licencja pulpitu zdalnego ma licencje CAL i została aktywowana. Jednak niektóre inne problemy na serwerze licencji usług pulpitu zdalnego uniemożliwiają mu dostarczanie licencji w środowisku.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [wykonaj wykonaj następujące czynności, wykonaj następujące](../windows/snapshot-copy-managed-disk.md) czynności:

1. Połącz się z maszyną wirtualną przy użyciu sesji administracyjnej:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Jeśli nie można połączyć się z maszyną wirtualną przy użyciu sesji administracyjnej, można użyć [konsoli szeregowej maszyny wirtualnej na platformie Azure,](serial-console-windows.md) aby uzyskać dostęp do maszyny Wirtualnej w następujący sposób:

    1. Dostęp do konsoli szeregowej można uzyskać, wybierając **pozycję Obsługa & konsoli** > rozwiązywania problemów z**serialem (Wersja zapoznawcza).** Jeśli funkcja jest włączona na maszynie Wirtualnej, można połączyć maszynę wirtualną pomyślnie.

    2. Utwórz nowy kanał dla wystąpienia CMD. Wprowadź **CMD,** aby uruchomić kanał i uzyskać nazwę kanału.

    3. Przełącz się do kanału, który uruchamia wystąpienie CMD. W takim przypadku powinien to być kanał 1:

       ```
       ch -si 1
       ```

    4. Wybierz **pozycję Wprowadź** ponownie i wprowadź prawidłową nazwę użytkownika i hasło, identyfikator lokalny lub identyfikator domeny dla maszyny Wirtualnej.

2. Sprawdź, czy maszyna wirtualna ma włączoną rolę Host sesji usług pulpitu zdalnego. Jeśli rola jest włączona, upewnij się, że działa poprawnie. Otwórz wystąpienie CMD z podwyższonym poziomem uprawnień i wykonaj następujące czynności:

    1. Aby sprawdzić stan roli Host sesji usług pulpitu zdalnego, użyj następującego polecenia:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Jeśli to polecenie zwraca wartość 0, oznacza to, że rola jest wyłączona i można przejść do kroku 3.

    2. Użyj następującego polecenia, aby sprawdzić zasady i ponownie skonfigurować w razie potrzeby:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Jeśli wartość **LicensingMode** jest ustawiona na dowolną wartość inną niż 4, na użytkownika, należy ustawić wartość na 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Jeśli wartość **SpecifiedLicenseServers** nie istnieje lub zawiera nieprawidłowe informacje o serwerze licencji, zmień ją w następujący sposób:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Po dokonaniu jakichkolwiek zmian w rejestrze uruchom ponownie maszynę wirtualną.

    4. Jeśli nie masz licencji CAL, usuń rolę Host sesji usług pulpitu zdalnego. Następnie PROW zostanie ustawiony na normalny. Zezwala tylko na dwa równoczesne połączenia RDP z maszyną wirtualną:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Jeśli maszyna wirtualna ma rolę licencjonowania usług pulpitu zdalnego i nie jest używana, można również usunąć tę rolę:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Upewnij się, że maszyna wirtualna może łączyć się z serwerem licencji usług pulpitu zdalnego. Można przetestować łączność z portem 135 między maszyną wirtualną a serwerem licencji: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Jeśli w środowisku nie ma serwera licencji usług pulpitu zdalnego i chcesz go zainstalować, możesz [zainstalować usługę roli licencjonowania usług pulpitu zdalnego.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)) Następnie [skonfiguruj licencjonowanie usług RDS](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Jeśli serwer licencji usług pulpitu zdalnego jest skonfigurowany i jest w dobrej kondycji, upewnij się, że serwer licencji usług pulpitu zdalnego jest aktywowany za pomocą licencji CAL.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby rozwiązać problem.
