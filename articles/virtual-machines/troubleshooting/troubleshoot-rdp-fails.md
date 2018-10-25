---
title: Protokół RDP zakończy się niepowodzeniem, jeśli serwer licencji usług pulpitu zdalnego nie jest dostępny, mogą udostępnić licencję na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z RDP zakończy się niepowodzeniem w przypadku, ponieważ serwer licencji usług pulpitu zdalnego nie jest dostępny | Dokumentacja firmy Microsoft
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 659f2866e56762e4dec70fac4ce4074fc24b18cb
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989210"
---
# <a name="rdp-fails-if-no-remote-desktop-license-server-is-available-in-azure"></a>Protokół RDP zakończy się niepowodzeniem, jeśli serwer licencji usług pulpitu zdalnego nie jest dostępna na platformie Azure

Ten artykuł pomoże rozwiązać ten problem, w której nie można dołączyć do maszyny wirtualnej (maszyny Wirtualnej platformy Azure), ponieważ serwer licencji usług pulpitu zdalnego nie jest dostępny, mogą udostępnić licencję.

## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną, występują następujące scenariusze:

- Zrzut ekranu maszyny wirtualnej (VM) pokazuje, że system operacyjny jest załadowany i Oczekiwanie na poświadczenia.
- Pojawi się następujące komunikaty o błędach, podczas próby nawiązania połączenia Microsoft protokołu RDP (Remote Desktop):

  - **Sesja zdalna została rozłączona, ponieważ nie można udostępnić licencję serwerów licencji usług pulpitu zdalnego.**

  - **Serwer licencji usług pulpitu zdalnego nie jest dostępna. Usługi pulpitu zdalnego przestanie działać, ponieważ upłynął jej okresu prolongaty tego komputera i nie skontaktował się z co najmniej jeden prawidłowy serwer licencji systemu Windows Server 2008. Kliknij ten komunikat, aby otworzyć konfigurację serwera hosta sesji usług pulpitu zdalnego do użycia Diagnostyka licencjonowania.**

Jednakże można połączyć z maszyną wirtualną zwykle przy użyciu sesji administracyjnej:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli serwer licencji usług pulpitu zdalnego jest niedostępny mogą udostępnić licencję, aby rozpocząć sesję zdalną. Ten problem może być spowodowany kilka scenariuszy, nawet jeśli roli hosta sesji usług pulpitu zdalnego zostały skonfigurowane na maszynie Wirtualnej:

- Nigdy nie było roli licencjonowania usług pulpitu zdalnego w środowisku i umieszczeniu musi upłynąć okres prolongaty (180 dni).
- Zainstalowano licencji usług pulpitu zdalnego w środowisku, ale nigdy nie jest aktywowana.
- Licencji usług pulpitu zdalnego w środowisku nie ma licencji dostępu klienta (CAL) w celu skonfigurowania połączenia.
- Licencji usług pulpitu zdalnego została zainstalowana w środowisku. Są dostępne licencje CAL, ale nie zostały poprawnie skonfigurowane.
- Licencji usług pulpitu zdalnego ma licencje CAL, i została ona aktywowana. Jednak niektóre problemy, na serwerze licencji usług pulpitu zdalnego uniemożliwić jej dostarczanie licencji w środowisku.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Utwórz kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md) i wykonaj następujące czynności:

1. Połączenie z maszyną wirtualną przy użyciu sesji administracyjnej:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

  Jeśli nie możesz połączyć z maszyną wirtualną przy użyciu sesji administracyjnej, możesz użyć [konsoli szeregowej](serial-console-windows.md) na dostęp do maszyny Wirtualnej w następujący sposób:

  1. Dostęp do konsoli szeregowej, wybierając **pomoc techniczna i rozwiązywanie problemów** > **Konsola szeregowa (wersja zapoznawcza)**. Jeśli ta funkcja jest włączona na maszynie Wirtualnej, możesz połączyć maszynę Wirtualną pomyślnie.

  2. Utwórz nowy kanał dla wystąpienia CMD. Typ **CMD** uruchomić kanału można pobrać nazwy kanału.

  3. Przełącz się do kanału, na którym uruchomiono wystąpienie CMD, w tym przypadku powinna być kanał 1.

    ```
    ch -si 1
    ```

  4. Naciśnij klawisz **Enter** ponownie i wpisz prawidłową nazwę użytkownika i hasło (identyfikator lokalnego lub domeny) dla maszyny Wirtualnej.

2. Sprawdź, czy maszyna wirtualna ma włączoną rolę hosta sesji usług pulpitu zdalnego. Jeśli rola została włączona, upewnij się, że działa prawidłowo. Otwórz wystąpienie CMD z podwyższonym poziomem uprawnień, a następnie wykonaj następujące kroki:

  1. Aby sprawdzić stan roli hosta sesji usług pulpitu zdalnego, należy użyć następującego polecenia:

    ```
    reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
    ```

    Jeśli to polecenie zwróci wartość 0, oznacza to, że rola jest wyłączona, i możesz przejść do kroku 3.

  2. Aby sprawdzić zasady, a następnie ponownie skonfiguruj zgodnie z potrzebami, użyj następującego polecenia:

    ```
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
    ```

    Jeśli **LicensingMode** wartość jest ustawiona na jakąkolwiek inną wartość niż 4 (na użytkownika), a następnie ustaw wartość 4:

    ```
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
    ```

    Jeśli **SpecifiedLicenseServers** wartość nie istnieje lub ma ona informacje o serwerze niepoprawne licencji, zmień go w następujący sposób:

    ```
    reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
    ```

  3. Po wprowadzeniu zmian w rejestrze należy ponownie uruchomić maszynę Wirtualną.

  4. Jeśli nie masz licencji CAL, należy usunąć rolę hosta sesji usług pulpitu zdalnego. Następnie protokołu RDP zostanie powraca do normalnego i umożliwia tylko dwa równoczesne połączenia RDP z maszyną wirtualną.

    ```
    dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
    ```

    Jeśli maszyna wirtualna ma roli licencjonowania usług pulpitu zdalnego i nie jest używany, można również usunąć tę rolę.

    ```
    dism /ONLINE /Disable-feature /FeatureName:Licensing
    ```

  5. Upewnij się, że maszyna wirtualna może połączyć się serwera licencji usług pulpitu zdalnego. Możesz przetestować łączność z portu 135 protokołów między maszyną Wirtualną a serwerem licencji.

    ```
    telnet <FQDN / IP License Server> 135
    ```

3. Jeśli serwer licencji usług pulpitu zdalnego jest w środowisku, a chcesz go, możesz to zrobić [instalowania usługi roli licencjonowania usług pulpitu zdalnego ](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)), a następnie [konfigurowania licencjonowania usług pulpitu zdalnego](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Jeśli serwer licencji usług pulpitu zdalnego jest skonfigurowany i działa prawidłowo, upewnij się, że serwer licencji usług pulpitu zdalnego jest aktywowana z licencji CAL.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
