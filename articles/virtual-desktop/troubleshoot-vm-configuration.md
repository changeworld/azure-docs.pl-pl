---
title: Tworzenie dzierżawy i puli hostów w programie Virtual Desktop systemu Windows — Azure
description: Jak rozwiązywać problemy podczas konfigurowania maszyny wirtualnej dzierżawy i hosta sesji w środowisku pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 0e32c81f37a8b81511cd009dfddbcc546aee1797
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876753"
---
# <a name="tenant-and-host-pool-creation"></a>Tworzenie dzierżawy i puli hosta

Ten artykuł służy do rozwiązywania problemów występujących podczas konfigurowania maszyn wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows.

## <a name="provide-feedback"></a>Przekazywanie opinii

Obecnie nie zajmują się pomocą techniczną, gdy pulpit wirtualny systemu Windows jest w wersji zapoznawczej. Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="vms-are-not-joined-to-the-domain"></a>Maszyny wirtualne nie są przyłączone do domeny

Postępuj zgodnie z tymi instrukcjami, jeśli masz problemy z przyłączaniem maszyn wirtualnych do domeny.

- Ręcznie Dołącz maszynę wirtualną przy użyciu procesu w przyłączeniu [maszyny wirtualnej z systemem Windows Server do domeny zarządzanej](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) lub za pomocą szablonu dołączania do [domeny](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Spróbuj wysłać polecenie ping do nazwy domeny z wiersza polecenia na maszynie wirtualnej.
- Przejrzyj listę komunikatów o błędach dołączania do domeny w [temacie Rozwiązywanie problemów z](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)przyłączaniem do domeny.

### <a name="error-incorrect-credentials"></a>Błąd: Nieprawidłowe poświadczenia

**Może** Wystąpił błąd podczas wprowadzania poświadczeń w ramach poprawek do interfejsu szablonu Azure Resource Manager.

**Wiązane** Postępuj zgodnie z tymi instrukcjami, aby poprawić poświadczenia.

1. Ręcznie Dodaj maszyny wirtualne do domeny.
2. Wdróż ponownie po potwierdzeniu poświadczeń. Zobacz [Tworzenie puli hostów przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Przyłączanie maszyn wirtualnych do domeny przy użyciu szablonu z przyłączaniem [istniejącej maszyny wirtualnej z systemem Windows do domeny usługi AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Błąd: Przekroczono limit czasu oczekiwania na dane wejściowe użytkownika

**Może** Konto używane do dołączania do domeny może mieć uwierzytelnianie wieloskładnikowe (MFA).

**Wiązane** Postępuj zgodnie z tymi instrukcjami, aby zakończyć przyłączanie do domeny.

1. Tymczasowe usuwanie usługi MFA dla konta.
2. Użyj konta usługi.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Błąd: Konto używane podczas aprowizacji nie ma uprawnień do ukończenia operacji

**Może** Używane konto nie ma uprawnień do przyłączania maszyn wirtualnych do domeny ze względu na zgodność i regulacje.

**Wiązane** Postępuj zgodnie z tymi instrukcjami.

1. Użyj konta, które jest członkiem grupy Administratorzy.
2. Przyznaj odpowiednie uprawnienia do używanego konta.

### <a name="error-domain-name-doesnt-resolve"></a>Błąd: Nazwa domeny nie jest rozpoznawana

**Przyczyna 1:** Maszyny wirtualne znajdują się w grupie zasobów, która nie jest skojarzona z siecią wirtualną (VNET), w której znajduje się domena.

**Poprawka 1:** Utwórz sieć równorzędną sieci wirtualnej między siecią wirtualną, w której zainicjowano maszyny wirtualne i sieć wirtualną, w której jest uruchomiony kontroler domeny. Zobacz [Tworzenie komunikacji równorzędnej sieci wirtualnej — Menedżer zasobów, różne subskrypcje](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Przyczyna 2:** W przypadku korzystania z AadService (AADS) nie ustawiono wpisów DNS.

**Poprawka 2:** Aby ustawić usługi domenowe, zobacz [Enable Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Nie zainstalowano programu Windows Virtual Desktop Agent i modułu ładującego rozruchu pulpitu wirtualnego systemu Windows

Zalecanym sposobem aprowizacji maszyn wirtualnych jest użycie szablonu **puli hostów usług pulpitu wirtualnego systemu Windows Azure Resource Manager tworzenie i udostępnianie** . Szablon automatycznie instaluje agenta pulpitu wirtualnego systemu Windows i moduł ładujący rozruchu agenta pulpitu wirtualnego systemu Windows.

Postępuj zgodnie z tymi instrukcjami, aby upewnić się, że składniki są zainstalowane i sprawdź komunikaty o błędach.

1. Upewnij się, że dwa składniki są zainstalowane, zaznaczając **panel** > sterowania**programy** > **programy i funkcje**. Jeśli program **Windows Virtual Desktop Agent** i **moduł ładujący rozruchu agenta wirtualnego systemu Windows** nie są widoczne, nie są one zainstalowane na maszynie wirtualnej.
2. Otwórz **Eksploratora plików** i przejdź do **C:\Windows\Temp\scriptlogs.log**. Jeśli brakuje pliku, oznacza to, że nie można uruchomić programu PowerShell DSC, który zainstalował dwa składniki, w podanym kontekście zabezpieczeń.
3. Jeśli plik **C:\Windows\Temp\scriptlogs.log** jest obecny, otwórz go i sprawdź komunikaty o błędach.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Błąd: Brak programu Windows Virtual Desktop Agent i modułu ładującego rozruchu agenta wirtualnego systemu Windows. Brak C:\Windows\Temp\scriptlogs.log również

**Przyczyna 1:** Poświadczenia podane podczas wprowadzania dla szablonu Azure Resource Manager były nieprawidłowe lub niewystarczające uprawnienia.

**Poprawka 1:** Ręcznie Dodaj brakujące składniki do maszyn wirtualnych za pomocą polecenia [Utwórz pulę hostów za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Przyczyna 2:** Program PowerShell DSC był w stanie uruchomić i wykonać, ale nie można go zakończyć, ponieważ nie może się zalogować do pulpitu wirtualnego systemu Windows i uzyskać wymaganych informacji.

**Poprawka 2:** Potwierdź elementy z poniższej listy.

- Upewnij się, że konto nie ma usługi MFA.
- Upewnij się, że nazwa dzierżawy jest dokładna, a dzierżawca istnieje na pulpicie wirtualnym systemu Windows.
- Upewnij się, że konto ma co najmniej uprawnienia współautora pulpitu zdalnego.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Błąd: Uwierzytelnianie nie powiodło się, błąd w C:\Windows\Temp\scriptlogs.log

**Może** Program PowerShell DSC był w stanie wykonać, ale nie mógł nawiązać połączenia z pulpitem wirtualnym systemu Windows.

**Wiązane** Potwierdź elementy z poniższej listy.

- Ręcznie Zarejestruj maszyny wirtualne za pomocą usługi pulpitu wirtualnego systemu Windows.
- Potwierdź, że konto używane do nawiązywania połączenia z pulpitem wirtualnym systemu Windows ma uprawnienia do tworzenia pul hostów w dzierżawie.
- Potwierdzenie konta nie ma uwierzytelniania MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agent pulpitu wirtualnego systemu Windows nie jest rejestrowany w usłudze pulpitu wirtualnego systemu Windows

Gdy Agent pulpitu wirtualnego systemu Windows jest instalowany po raz pierwszy na maszynach wirtualnych hosta sesji (ręcznie lub za pośrednictwem szablonu Azure Resource Manager i programu PowerShell DSC), udostępnia token rejestracji. W poniższej sekcji omówiono problemy związane z rozwiązywaniem problemów dotyczących agenta usług pulpitu wirtualnego systemu Windows i tokenu.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Błąd: Stan zgłoszony w poleceniu cmdlet Get-RdsSessionHost pokazuje stan jako niedostępny

![Polecenie cmdlet Get-RdsSessionHost wyświetla stan jako niedostępny.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Może** Agent nie może zaktualizować się do nowej wersji.

**Wiązane** Postępuj zgodnie z tymi instrukcjami, aby ręcznie zaktualizować agenta.

1. Pobierz nową wersję agenta na maszynie wirtualnej hosta sesji.
2. Uruchom Menedżera zadań, a następnie na karcie Usługa Zatrzymaj usługę RDAgentBootLoader.
3. Uruchom Instalatora, aby uzyskać nową wersję agenta pulpitu wirtualnego systemu Windows.
4. Po wyświetleniu monitu o token rejestracji Usuń wpis INVALID_TOKEN i naciśnij przycisk Dalej (nowy token nie jest wymagany).
5. Ukończ pracę Kreatora instalacji.
6. Otwórz Menedżera zadań i uruchom usługę RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Błąd:  Wpis rejestru programu Windows Virtual Desktop Agent IsRegistered zawiera wartość 0

**Może** Token rejestracji wygasł lub został wygenerowany z wartością wygaśnięcia wynoszącą 999999.

**Wiązane** Postępuj zgodnie z tymi instrukcjami, aby naprawić błąd rejestru agenta.

1. Jeśli istnieje już token rejestracji, usuń go z poleceniem Remove-RDSRegistrationInfo.
2. Generuj nowy token przy użyciu programu RDS-NewRegistrationInfo.
3. Upewnij się, że parametr-ExpriationHours jest ustawiony na 72 (wartość maksymalna to 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Błąd: Agent pulpitu wirtualnego systemu Windows nie zgłasza pulsu podczas uruchamiania Get-RdsSessionHost

**Przyczyna 1:** Usługa RDAgentBootLoader została zatrzymana.

**Poprawka 1:** Uruchom Menedżera zadań, a jeśli karta usługi zgłosi stan zatrzymania dla usługi RDAgentBootLoader, uruchom usługę.

**Przyczyna 2:** Port 443 może być zamknięty.

**Poprawka 2:** Postępuj zgodnie z tymi instrukcjami, aby otworzyć port 443.

1. Upewnij się, że port 443 jest otwarty przez pobranie narzędzia PSPing z [narzędzi sysinternal](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Zainstaluj program PSPing na maszynie wirtualnej hosta sesji, w której jest uruchomiony Agent programu.
3. Otwórz wiersz polecenia jako administrator i wydaj poniższe polecenie:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Potwierdź, że PSPing otrzymał informacje z RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Rozwiązywanie problemów z funkcją pulpitu wirtualnego systemu Windows — stos równoległy

Stos równoległy pulpitu wirtualnego systemu Windows jest automatycznie instalowany z systemem Windows Server 2019. Użyj Instalatora Microsoft (MSI), aby zainstalować stos równoległy w systemie Microsoft Windows Server 2016 lub Windows Server 2012 R2. W przypadku systemu Microsoft Windows 10 stos równoległy pulpitu wirtualnego systemu Windows jest włączony z **enablesxstackrs. ps1**.

Istnieją trzy główne sposoby, w których stos równoległy jest instalowany lub włączony na maszynach wirtualnych puli hostów sesji:

- Za pomocą Azure Resource Manager **tworzenia i udostępniania nowego szablonu puli hostów wirtualnego systemu Windows**
- Przez dołączenie i włączenie na obrazie głównym
- Ręcznie zainstalowane lub włączone dla każdej maszyny wirtualnej (lub z rozszerzeniami/programem PowerShell)

Jeśli występują problemy z stosem równoległym pulpitu wirtualnego systemu Windows, wpisz polecenie **qwinsta** z wiersza polecenia, aby upewnić się, że stos równoległy jest zainstalowany lub włączony.

Dane wyjściowe **qwinsta** będą wystawiać **protokół RDP-SxS** w danych wyjściowych, jeśli zostanie zainstalowany i włączony stos równoległy.

![Stos równoległy został zainstalowany lub włączony przy użyciu qwinsta, który jest wymieniony jako RDP-SxS w danych wyjściowych.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Sprawdź wpisy rejestru wymienione poniżej i upewnij się, że ich wartości pasują do siebie. Jeśli brakuje kluczy rejestru lub wartości są niezgodne, postępuj zgodnie z instrukcjami w temacie [Tworzenie puli hostów przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) na temat sposobu ponownej instalacji stosu równoległego.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Błąd: O_REVERSE_CONNECT_STACK_FAILURE

![Kod błędu O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Może** Stos równoległy nie jest zainstalowany na maszynie wirtualnej hosta sesji.

**Wiązane** Postępuj zgodnie z tymi instrukcjami, aby zainstalować stos równoległy na maszynie wirtualnej hosta sesji.

1. Użyj Remote Desktop Protocol (RDP), aby przejść bezpośrednio do maszyny wirtualnej hosta sesji jako administrator lokalny.
2. Pobierz i zaimportuj [moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.
3. Zainstaluj stos równoległy przy użyciu polecenia [Utwórz pulę hostów za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Jak naprawić stos równoległy pulpitu wirtualnego systemu Windows, który działa nieprawidłowo

Istnieją znane sytuacje, które mogą spowodować nieprawidłowe działanie stosu równoległego:

- Nie zgodnie z prawidłową kolejnością kroków, aby włączyć stos równoległy
- Autoaktualizacja do ulepszonego uniwersalnego dysku systemu Windows 10 (EVD)
- Brak roli hosta sesji Pulpit zdalny
- Uruchamianie enablesxsstackrc. ps1 wiele razy
- Uruchamianie enablesxsstackrc. ps1 na koncie, które nie ma uprawnień administratora lokalnego

Instrukcje przedstawione w tej sekcji mogą pomóc w odinstalowaniu stosu równoległego pulpitu wirtualnego systemu Windows. Po odinstalowaniu stosu równoległego przejdź do pozycji "Zarejestruj maszynę wirtualną w puli hostów systemu Windows Virtual Desktop" w temacie [Tworzenie puli hostów za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) , aby ponownie zainstalować stos równoległy.

Maszyna wirtualna używana do uruchamiania korygowania musi znajdować się w tej samej podsieci i domenie co maszyna wirtualna z nieprawidłowo działającym stosem równoległym.

Postępuj zgodnie z tymi instrukcjami, aby przeprowadzić korygowanie z tej samej podsieci i domeny:

1. Połącz się z maszyną wirtualną przy użyciu standardu Remote Desktop Protocol (RDP), z której zostanie zastosowana poprawka.
2. Pobierz PsExec z https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Rozpakuj pobrany plik.
4. Uruchom wiersz polecenia jako administrator lokalny.
5. Przejdź do folderu, w którym PsExec zostało rozpakowane.
6. W wierszu polecenia Użyj następującego polecenia:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname to nazwa maszyny wirtualnej z nieprawidłowym stosem równoległym.

7. Zaakceptuj umowę licencyjną PsExec, klikając pozycję Zgadzam się.

    ![Zrzut ekranu umowy licencji na oprogramowanie.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >To okno dialogowe będzie wyświetlane tylko po pierwszym uruchomieniu PsExec.

8. Po otwarciu sesji wiersza polecenia na maszynie wirtualnej z nieprawidłowym stosem równoległym Uruchom program qwinsta i upewnij się, że jest dostępny wpis o nazwie RDP-SXS. W przeciwnym razie stos równoległy nie jest obecny na maszynie wirtualnej, więc problem nie jest powiązany z stosem równoległym.

    ![Wiersz polecenia administratora](media/AdministratorCommandPrompt.png)

9. Uruchom następujące polecenie, które spowoduje wyświetlenie listy składników firmy Microsoft zainstalowanych na maszynie wirtualnej z nieprawidłowym stosem równoległym.

    ```cmd
        wmic product get name
    ```

10. Uruchom poniższe polecenie z nazwami produktów w powyższym kroku.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Odinstaluj wszystkie produkty, które zaczynają się od "Pulpit zdalny".

12. Po odinstalowaniu wszystkich składników pulpitu wirtualnego systemu Windows postępuj zgodnie z instrukcjami dla danego systemu operacyjnego:

13. Jeśli używany system operacyjny to Windows Server, należy ponownie uruchomić maszynę wirtualną, która ma nieprawidłowo działający stos (z Azure Portal lub z narzędzia PsExec).

Jeśli używany system operacyjny to Microsoft Windows 10, wykonaj poniższe instrukcje:

14. Na maszynie wirtualnej z systemem PsExec Otwórz Eksploratora plików i skopiuj disablesxsstackrc. ps1 na dysk systemowy maszyny wirtualnej z nieprawidłowo działającym stosem równoległym.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname to nazwa maszyny wirtualnej z nieprawidłowym stosem równoległym.

15. Zalecany proces: w narzędziu PsExec Uruchom program PowerShell i przejdź do folderu z poprzedniego kroku i uruchom polecenie disablesxsstackrc. ps1. Alternatywnie można uruchomić następujące polecenia cmdlet:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Po zakończeniu działania poleceń cmdlet Uruchom ponownie maszynę wirtualną z nieprawidłowym stosem Side-by-side.

## <a name="remote-licensing-model-is-not-configured"></a>Nie skonfigurowano modelu licencjonowania zdalnego

Jeśli zalogujesz się do wielosesyjnego systemu Windows 10 Enterprise przy użyciu konta administracyjnego, możesz otrzymać powiadomienie informujące o tym, że Pulpit zdalny tryb licencjonowania nie jest skonfigurowany, Usługi pulpitu zdalnego przestanie działać w ciągu X dni. Na serwerze brokera połączeń użyj Menedżer serwera, aby określić tryb licencjonowania Pulpit zdalny ". Jeśli zobaczysz ten komunikat, oznacza to, że trzeba ręcznie skonfigurować tryb licencjonowania na **użytkownika**.

Aby ręcznie skonfigurować tryb licencjonowania:  

1. Przejdź do pola wyszukiwania **menu Start** , a następnie Znajdź i Otwórz **gpedit. msc** , aby uzyskać dostęp do edytora zasady grupy lokalnego. 
2. Przejdź do **pozycji Konfiguracja** > komputera**Szablony administracyjne** > **składniki** > systemu Windows**usługi pulpitu zdalnego** > **pulpit zdalny hosta sesji**  >  **Licencjonowanie**. 
3. Wybierz pozycję **Ustaw tryb licencjonowania pulpit zdalny** i zmień go na **na użytkownika**.

Obecnie przeszukiwane są problemy z limitem czasu powiadomień i okresu prolongaty oraz zaplanowano ich rozwiązanie w przyszłej aktualizacji. 

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [pulpit zdalny połączenia klientów](troubleshoot-client-connection.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [środowisko Windows Virtual Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Aby przejść przez samouczek dotyczący rozwiązywania [problemów, zobacz Samouczek: Rozwiązywanie problemów z](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)wdrożeniami szablonów Menedżer zasobów.
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).