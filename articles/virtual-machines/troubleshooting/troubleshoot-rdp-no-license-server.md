---
title: Serwer licencji usług pulpitu zdalnego nie jest dostępna, po nawiązaniu połączenia z Maszyną wirtualną platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z niepowodzeniem RDP w przypadku, ponieważ serwer licencji usług pulpitu zdalnego nie jest dostępny | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 550b971602d1736e0ba3981a5b7ca546862ea034
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318956"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Serwer licencji usług pulpitu zdalnego nie jest dostępna, po nawiązaniu połączenia z Maszyną wirtualną platformy Azure

Ten artykuł pomoże rozwiązać ten problem, gdy nie można dołączyć do maszyny wirtualnej (VM) platformy Azure, ponieważ serwer licencji usług pulpitu zdalnego nie jest dostępny, mogą udostępnić licencję.

## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną (VM), występują następujące scenariusze:

- Zrzut ekranu maszyny Wirtualnej pokazuje, że system operacyjny jest załadowany i Oczekiwanie na poświadczenia.
- Pojawi się następujące komunikaty o błędach, podczas próby nawiązania połączenia Microsoft protokołu RDP (Remote Desktop):

  - Sesja zdalna została rozłączona, ponieważ nie można udostępnić licencję serwerów licencji usług pulpitu zdalnego.

  - Serwer licencji usług pulpitu zdalnego nie jest dostępna. Usługi pulpitu zdalnego przestanie działać, ponieważ upłynął jej okresu prolongaty tego komputera i nie łączyło się z co najmniej jeden prawidłowy serwer licencji systemu Windows Server 2008. Wybierz ten komunikat, aby otworzyć konfigurację serwera hosta sesji usług pulpitu zdalnego do użycia Diagnostyka licencjonowania.

Jednakże można połączyć z maszyną wirtualną zwykle przy użyciu sesji administracyjnej:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli serwer licencji usług pulpitu zdalnego jest niedostępny mogą udostępnić licencję, aby rozpocząć sesję zdalną. Go może być spowodowany kilka scenariuszy, nawet jeśli roli hosta sesji usług pulpitu zdalnego zostały skonfigurowane na maszynie Wirtualnej:

- Nigdy nie było roli licencjonowania usług pulpitu zdalnego w środowisku, a okres prolongaty umieszczeniu 180 dni.
- Licencji usług pulpitu zdalnego została zainstalowana w środowisku, ale nigdy nie jest aktywowana.
- Licencji usług pulpitu zdalnego w środowisku nie ma licencji dostępu klienta (CAL) w celu skonfigurowania połączenia.
- Licencji usług pulpitu zdalnego została zainstalowana w środowisku. Są dostępne licencje CAL, ale nie zostały poprawnie skonfigurowane.
- Licencji usług pulpitu zdalnego ma licencje CAL, a jego został aktywowany. Jednak niektóre problemy, na serwerze licencji usług pulpitu zdalnego uniemożliwić jej dostarczanie licencji w środowisku.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Utwórz kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md) i wykonaj następujące czynności:

1. Połączenie z maszyną wirtualną przy użyciu sesji administracyjnej:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Jeśli nie możesz połączyć z maszyną wirtualną przy użyciu sesji administracyjnej, możesz użyć [Konsola szeregowa maszyny wirtualnej na platformie Azure](serial-console-windows.md) na dostęp do maszyny Wirtualnej w następujący sposób:

    1. Dostęp do konsoli szeregowej, wybierając **pomoc techniczna i rozwiązywanie problemów** > **Konsola szeregowa (wersja zapoznawcza)** . Jeśli ta funkcja jest włączona na maszynie Wirtualnej, możesz połączyć maszynę Wirtualną pomyślnie.

    2. Utwórz nowy kanał dla wystąpienia CMD. Wprowadź **CMD** Uruchom kanał i uzyskać nazwę kanału.

    3. Przełącz się do kanału, który uruchamia wystąpienie polecenia. W takim przypadku należy kanał 1:

       ```
       ch -si 1
       ```

    4. Wybierz **Enter** ponownie i wprowadź prawidłową nazwę użytkownika i hasło, lokalnego lub domeny Identyfikatora, dla maszyny Wirtualnej.

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

        Jeśli **LicensingMode** wartość jest ustawiona na wartość inną niż 4 dla każdego użytkownika, a następnie ustaw wartość 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Jeśli **SpecifiedLicenseServers** wartość nie istnieje lub ma informacje o serwerze niepoprawne licencji, zmień go w następujący sposób:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Po wprowadzeniu zmian w rejestrze należy ponownie uruchomić maszynę Wirtualną.

    4. Jeśli nie masz licencji CAL, należy usunąć rolę hosta sesji usług pulpitu zdalnego. Następnie protokołu RDP zostanie ponownie ustawiona na normalny. Umożliwia tylko dwa równoczesne połączenia RDP z maszyną wirtualną:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Jeśli maszyna wirtualna ma roli licencjonowania usług pulpitu zdalnego i nie jest on używany, można również usunąć tej roli:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Upewnij się, że maszyna wirtualna może połączyć się serwera licencji usług pulpitu zdalnego. Możesz przetestować łączność z portu 135 protokołów między maszyną Wirtualną a serwerem licencji: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Jeśli serwer licencji usług pulpitu zdalnego jest w środowisku, a chcesz go, możesz to zrobić [instalowania usługi roli licencjonowania usług pulpitu zdalnego](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Następnie [konfigurowania licencjonowania usług pulpitu zdalnego](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Jeśli serwer licencji usług pulpitu zdalnego jest skonfigurowany i działa prawidłowo, upewnij się, że serwer licencji usług pulpitu zdalnego jest aktywowana z licencji CAL.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można rozwiązać swój problem.
