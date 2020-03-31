---
title: Rozwiązywanie problemów z hostem sesji pulpitu wirtualnego systemu Windows — platforma Azure
description: Jak rozwiązać problemy podczas konfigurowania maszyn wirtualnych hosta sesji pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127588"
---
# <a name="session-host-virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej hosta sesji

Ten artykuł służy do rozwiązywania problemów z konfigurowaniem maszyn wirtualnych hosta sesji pulpitu wirtualnego systemu Windows.

## <a name="provide-feedback"></a>Przekazywanie opinii

Odwiedź [społeczność techniczną pulpitu wirtualnego systemu Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) aby omówić usługę Pulpit wirtualny systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="vms-are-not-joined-to-the-domain"></a>Maszyny wirtualne nie są przyłączone do domeny

Jeśli masz problemy z dołączeniem maszyn wirtualnych do domeny, postępuj zgodnie z tymi instrukcjami.

- Dołącz do maszyny Wirtualnej ręcznie, używając procesu [dołączania maszyny wirtualnej systemu Windows Server do domeny zarządzanej](../active-directory-domain-services/join-windows-vm.md) lub przy użyciu [szablonu sprzężenia domeny](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Spróbuj wykonać polecenie pingowanie nazwy domeny z wiersza polecenia na maszynie Wirtualnej.
- Przejrzyj listę komunikatów o błędach sprzężenia do domeny w [komunikatach o błędach sprzężenia z domeną](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Błąd: nieprawidłowe poświadczenia

**Przyczyna:** Podczas wprowadzania poświadczeń w poprawkach interfejsu szablonu usługi Azure Resource Manager wprowadzono literówkę.

**Poprawka:** Aby rozwiązać jedną z następujących czynności.

- Ręcznie dodaj maszyny wirtualne do domeny.
- Ponownie rozmieszczaj szablon po potwierdzeniu poświadczeń. Zobacz [Tworzenie puli hostów za pomocą programu PowerShell](create-host-pools-powershell.md).
- Dołączanie maszyn wirtualnych do domeny przy użyciu szablonu za pomocą [łączy istniejącą maszynę wirtualną systemu Windows z domeną usługi AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Błąd: limit czasu oczekiwania na dane wejściowe użytkownika

**Przyczyna:** Konto używane do ukończenia sprzężenia domeny może mieć uwierzytelnianie wieloskładnikowe (MFA).

**Poprawka:** Aby rozwiązać jedną z następujących czynności.

- Tymczasowo usuń uwierzytelnianie wieloskładnikowe dla konta.
- Użyj konta usługi.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Błąd: konto używane podczas inicjowania obsługi administracyjnej nie ma uprawnień do ukończenia operacji

**Przyczyna:** Konto używane nie ma uprawnień do dołączania maszyn wirtualnych do domeny ze względu na zgodność i przepisy.

**Poprawka:** Aby rozwiązać jedną z następujących czynności.

- Użyj konta, które jest członkiem grupy Administrator.
- Udziel niezbędnych uprawnień do używanego konta.

### <a name="error-domain-name-doesnt-resolve"></a>Błąd: nazwa domeny nie jest rozpoznawana

**Przyczyna 1:** Maszyny wirtualne znajdują się w sieci wirtualnej, która nie jest skojarzona z siecią wirtualną (VNET), w której znajduje się domena.

**Poprawka 1:** Utwórz komunikację równorzędnych sieci wirtualnej między siecią wirtualną, w której zostały zainicjowane maszyny wirtualne, a siecią wirtualną, w której jest uruchomiony kontroler domeny. Zobacz [Tworzenie komunikacji równorzędnej w sieci wirtualnej — Menedżer zasobów, różne subskrypcje](../virtual-network/create-peering-different-subscriptions.md).

**Przyczyna 2:** Podczas korzystania z usług domenowych Usługi active directory platformy Azure (Usługi Azure AD DS), sieć wirtualna nie ma ustawień serwera DNS aktualizowane w celu wskazywanie kontrolerów domeny zarządzane.

**Poprawka 2:** Aby zaktualizować ustawienia DNS sieci wirtualnej zawierającej usługi Azure AD DS, zobacz [Aktualizowanie ustawień DNS dla sieci wirtualnej platformy Azure](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Przyczyna 3:** Ustawienia serwera DNS interfejsu sieciowego nie wskazują odpowiedniego serwera DNS w sieci wirtualnej.

**Poprawka 3:** Aby rozwiązać jedną z następujących czynności, wykonując czynności opisane w [Zmień serwery DNS].
- Zmień ustawienia serwera DNS interfejsu sieciowego na **Niestandardowe,** postępuje zgodnie z instrukcjami zmień [serwery DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers) i określ prywatne adresy IP serwerów DNS w sieci wirtualnej.
- Zmień ustawienia serwera DNS interfejsu sieciowego na **Dziedzicz z sieci wirtualnej** krokami z [polecenia Zmień serwery DNS,](../virtual-network/virtual-network-network-interface.md#change-dns-servers)a następnie zmień ustawienia serwera DNS sieci wirtualnej za pomocą kroków z [polecenia Zmień serwery DNS](../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Nie są zainstalowane nie są zainstalowane usługi Agent pulpitu wirtualnego systemu Windows i modułu ładującego pulpitu wirtualnego systemu Windows

Zalecanym sposobem aprowizowania maszyn wirtualnych jest użycie szablonu puli hostów hosta usługi Azure Resource Manager **i aprowizowania go z pulpitu wirtualnego systemu Windows.** Szablon automatycznie instaluje program Ładujący programu Windows Virtual Desktop Agent i Programu ładującego programu Windows Virtual Desktop Agent.

Postępuj zgodnie z tymi instrukcjami, aby potwierdzić, że składniki są zainstalowane i sprawdzić, czy nie ma komunikatów o błędach.

1. Upewnij się, że oba składniki są zainstalowane, zaewidencjonuj w **panelu** > **sterowania programy** > **i funkcje**. Jeśli **program ładujący programu Windows Virtual Desktop Agent** i programu **ładującego agenta pulpitu wirtualnego systemu Windows** nie są widoczne, nie są one instalowane na maszynie wirtualnej.
2. Otwórz **Eksploratora plików** i przejdź do **pozycji C:\Windows\Temp\ScriptLog.log**. Jeśli brakuje pliku, oznacza to, że program PowerShell DSC, który zainstalował dwa składniki, nie mógł działać w podanym kontekście zabezpieczeń.
3. Jeśli plik **C:\Windows\Temp\ScriptLog.log** jest obecny, otwórz go i sprawdź, czy nie ma komunikatów o błędach.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Błąd: Brakuje programu Windows Virtual Desktop Agent i programu ładującego agenta pulpitu wirtualnego systemu Windows. C:\Windows\Temp\ScriptLog.log również brakuje

**Przyczyna 1:** Poświadczenia podane podczas wprowadzania danych dla szablonu usługi Azure Resource Manager były niepoprawne lub uprawnienia były niewystarczające.

**Poprawka 1:** Ręcznie dodaj brakujące składniki do maszyn wirtualnych przy użyciu [funkcji Utwórz pulę hostów za pomocą programu PowerShell](create-host-pools-powershell.md).

**Przyczyna 2:** Program PowerShell DSC był w stanie uruchomić i wykonać, ale nie udało się ukończyć, ponieważ nie można zalogować się do pulpitu wirtualnego systemu Windows i uzyskać potrzebne informacje.

**Poprawka 2:** Potwierdź elementy na poniższej liście.

- Upewnij się, że konto nie ma usługi MFA.
- Upewnij się, że nazwa dzierżawy jest dokładna i dzierżawy istnieje w windows virtual desktop.
- Upewnij się, że konto ma co najmniej uprawnienia współautora usług RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Błąd: uwierzytelnianie nie powiodło się, błąd w C:\Windows\Temp\ScriptLog.log

**Przyczyna:** Program PowerShell DSC był w stanie wykonać, ale nie mógł połączyć się z pulpitem wirtualnym systemu Windows.

**Poprawka:** Potwierdź elementy na poniższej liście.

- Ręcznie zarejestruj maszyny wirtualne w usłudze pulpitu wirtualnego systemu Windows.
- Potwierdź konto używane do łączenia się z pulpitem wirtualnym systemu Windows ma uprawnienia do dzierżawy do tworzenia pul hostów.
- Upewnij się, że konto nie ma usługi MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agent pulpitu wirtualnego systemu Windows nie rejestruje się w usłudze pulpitu wirtualnego systemu Windows

Gdy agent pulpitu wirtualnego systemu Windows jest po raz pierwszy zainstalowany na maszynach wirtualnych hosta sesji (ręcznie lub za pośrednictwem szablonu usługi Azure Resource Manager i programu PowerShell DSC), udostępnia token rejestracji. W poniższej sekcji opisano problemy z rozwiązywaniem problemów mających zastosowanie do agenta pulpitu wirtualnego systemu Windows i tokenu.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Błąd: Stan złożony w pokręteł get-rdssessionhost pokazuje stan jako niedostępny

![Polecenie cmdlet Get-RdsSessionHost pokazuje stan jako niedostępny.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Przyczyna:** Agent nie może zaktualizować się do nowej wersji.

**Poprawka:** Postępuj zgodnie z tymi instrukcjami, aby ręcznie zaktualizować agenta.

1. Pobierz nową wersję agenta na maszynie wirtualnej hosta sesji.
2. Uruchom Menedżera zadań i na karcie Usługa zatrzymaj usługę RDAgentBootLoader.
3. Uruchom instalator dla nowej wersji programu Windows Virtual Desktop Agent.
4. Po wyświetleniu monitu o token rejestracji usuń wpis INVALID_TOKEN i naciśnij dalej (nowy token nie jest wymagany).
5. Ukończ Kreatora instalacji.
6. Otwórz Menedżera zadań i uruchom usługę RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Błąd: Wpis rejestru programu Windows Virtual Desktop Agent IsRegistered zawiera wartość 0

**Przyczyna:** Token rejestracji wygasł lub został wygenerowany z wartością wygaśnięcia 999999.

**Poprawka:** Postępuj zgodnie z tymi instrukcjami, aby naprawić błąd rejestru agenta.

1. Jeśli istnieje już token rejestracji, usuń go z Remove-RDSRegistrationInfo.
2. Generowanie nowego tokenu za pomocą Rds-NewRegistrationInfo.
3. Upewnij się, że parametr -ExpriationHours jest ustawiony na 72 (wartość maksymalna to 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Błąd: Agent pulpitu wirtualnego systemu Windows nie zgłasza pulsu podczas uruchamiania programu Get-RdsSessionHost

**Przyczyna 1:** Usługa RDAgentBootLoader została zatrzymana.

**Poprawka 1:** Uruchom Menedżera zadań i, jeśli karta usługi zgłasza stan zatrzymania usługi RDAgentBootLoader, uruchom usługę.

**Przyczyna 2:** Port 443 może być zamknięty.

**Poprawka 2:** Postępuj zgodnie z tymi instrukcjami, aby otworzyć port 443.

1. Potwierdź, że port 443 jest otwarty, pobierając narzędzie PSPing z [narzędzi Sysinternal](/sysinternals/downloads/psping/).
2. Zainstaluj psping na maszynie wirtualnej hosta sesji, gdzie agent jest uruchomiony.
3. Otwórz wiersz polecenia jako administrator i wyemiuj poniższe polecenie:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Potwierdź, że PSPing otrzymał informacje z powrotem z RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Rozwiązywanie problemów ze stosem pulpitu wirtualnego systemu Windows obok siebie

Stos pulpitu wirtualnego systemu Windows obok siebie jest automatycznie instalowany w systemie Windows Server 2019. Instalator Microsoft (MSI) służy do instalowania stosu obok siebie w systemie Microsoft Windows Server 2016 lub Windows Server 2012 R2. W systemie Microsoft Windows 10, Windows Virtual Desktop obok siebie stos jest włączony z **enablesxstackrs.ps1**.

Istnieją trzy główne sposoby instalowania lub włączania stosu obok siebie na maszynach wirtualnych puli hostów sesji:

- Dzięki usłudze Azure Resource Manager **Tworzenie i inicjowania obsługi administracyjnej nowego szablonu puli hostów pulpitu wirtualnego systemu Windows**
- Dzięki dołączeniu i włączeniu obrazu głównego
- Instalowane lub włączane ręcznie na każdej maszynie wirtualnej (lub z rozszerzeniami/programem PowerShell)

Jeśli masz problemy ze stosem pulpitu wirtualnego systemu Windows obok siebie, wpisz polecenie **qwinsta** z wiersza polecenia, aby potwierdzić, że stos obok siebie jest zainstalowany lub włączony.

Wyjście **qwinsta** będzie lista **rdp-sxs** w danych wyjściowych, jeśli stos side-by-side jest zainstalowany i włączony.

![Stos obok siebie zainstalowany lub włączony z qwinsta wymienione jako rdp-sxs w danych wyjściowych.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Sprawdź wpisy rejestru wymienione poniżej i upewnij się, że ich wartości są zgodne. Jeśli brakuje kluczy rejestru lub wartości są niezgodne, postępuj zgodnie z instrukcjami w [tworzenie puli hostów z programem PowerShell,](create-host-pools-powershell.md) aby ponownie zainstalować stos obok siebie.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Błąd: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE kod błędu.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Przyczyna:** Stos obok siebie nie jest zainstalowany na maszynie wirtualnej hosta sesji.

**Poprawka:** Postępuj zgodnie z tymi instrukcjami, aby zainstalować stos side-by-side na maszynie wirtualnej hosta sesji.

1. Użyj protokołu RDP (Remote Desktop Protocol), aby uzyskać bezpośredni dostęp do maszyny Wirtualnej hosta sesji jako administrator lokalny.
2. Pobierz i [zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś, uruchom to polecenie cmdlet, aby zalogować się na swoje konto:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Zainstaluj stos obok siebie przy użyciu [funkcji Utwórz pulę hostów za pomocą programu PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Jak naprawić pulpit wirtualny systemu Windows obok stosu, który działa nieprawidłowo

Istnieją znane okoliczności, które mogą spowodować nieprawidłowe działanie stosu obok siebie:

- Nieprzestrzeganie prawidłowej kolejności kroków, aby włączyć stos obok siebie
- Automatyczna aktualizacja do systemu Windows 10 Enhanced Versatile Disc (EVD)
- Brak roli Host sesji usług pulpitu zdalnego (RDSH)
- Uruchamianie enablesxsstackrc.ps1 wiele razy
- Uruchamianie enablesxsstackrc.ps1 na koncie, które nie ma uprawnień administratora lokalnego

Instrukcje zawarte w tej sekcji mogą pomóc w odinstalowaniu stosu pulpitu wirtualnego systemu Windows obok siebie. Po odinstalowaniu stosu obok siebie przejdź do "Zarejestruj maszynę wirtualną z pulą hosta pulpitu wirtualnego systemu Windows" w [programie Utwórz pulę hostów z programem PowerShell,](create-host-pools-powershell.md) aby ponownie zainstalować stos obok siebie.

Maszyna wirtualna używana do uruchamiania korygowania musi znajdować się w tej samej podsieci i domenie co maszyna wirtualna z nieprawidłowo działającym stosem obok siebie.

Postępuj zgodnie z tymi instrukcjami, aby uruchomić korygowanie z tej samej podsieci i domeny:

1. Połącz się ze standardowym protokołem RDP (Remote Desktop Protocol) z maszyną wirtualną, z której zostanie zastosowana poprawka.
2. Pobierz PsExec https://docs.microsoft.com/sysinternals/downloads/psexecz .
3. Rozpaj pobrany plik.
4. Uruchom wiersz polecenia jako administrator lokalny.
5. Przejdź do folderu, w którym psexec został rozpakowany.
6. W wierszu polecenia użyj następującego polecenia:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >Nazwa wirtualna to nazwa maszyny wirtualnej z nieprawidłowo działającym stosem obok siebie.

7. Zaakceptuj Umowę Licencyjną PsExec, klikając przycisk Zgadzam się.

    ![Zrzut ekranu umowy licencyjnej oprogramowania.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >To okno dialogowe pojawi się tylko przy pierwszym uruchomieniu PsExec.

8. Po otwarciu sesji wiersza polecenia na maszynie wirtualnej z nieprawidłowo działającym stosem obok siebie uruchom qwinsta i upewnij się, że dostępny jest wpis o nazwie rdp-sxs. Jeśli nie, stos obok siebie nie jest obecny na maszynie wirtualnej, więc problem nie jest związany ze stosem obok siebie.

    ![Wiersz polecenia administratora](media/AdministratorCommandPrompt.png)

9. Uruchom następujące polecenie, które wyświetli listę składników firmy Microsoft zainstalowanych na maszynie Wirtualnej z nieprawidłowo działającym stosem obok siebie.

    ```cmd
        wmic product get name
    ```

10. Uruchom poniższe polecenie z nazwami produktów z kroku powyżej.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Odinstaluj wszystkie produkty rozpoczynające się od "Pulpitu zdalnego".

12. Po odinstalowaniu wszystkich składników pulpitu wirtualnego systemu Windows postępuj zgodnie z instrukcjami dotyczącymi systemu operacyjnego:

13. Jeśli system operacyjny jest Windows Server, uruchom ponownie maszynę wirtualną, która miała nieprawidłowo działający stos obok siebie (za pomocą portalu Azure lub narzędzia PsExec).

Jeśli twoim systemem operacyjnym jest Microsoft Windows 10, przejdź do poniższych instrukcji:

14. Z maszyny Wirtualnej z systemem PsExec otwórz Eksploratora plików i skopiuj disablesxsstackrc.ps1 na dysk systemowy maszyny Wirtualnej z uszkodzonym stosem obok siebie.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >Nazwa wirtualna to nazwa maszyny wirtualnej z nieprawidłowo działającym stosem obok siebie.

15. Zalecany proces: z narzędzia PsExec uruchom program PowerShell i przejdź do folderu z poprzedniego kroku i uruchom disablesxsstackrc.ps1. Alternatywnie można uruchomić następujące polecenia cmdlet:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Po uruchomieniu poleceń cmdlet uruchom ponownie maszynę wirtualną z nieprawidłowo działającym stosem obok siebie.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Tryb licencjonowania pulpitu zdalnego nie jest skonfigurowany

Jeśli zalogujesz się do wielu sesji systemu Windows 10 Enterprise przy użyciu konta administracyjnego, może zostać wyświetlone powiadomienie z informacją: "Tryb licencjonowania usług pulpitu zdalnego nie jest skonfigurowany, usługi pulpitu zdalnego przestaną działać w ciągu X dni. Na serwerze Broker połączeń użyj Menedżera serwera, aby określić tryb licencjonowania usług pulpitu zdalnego."

Jeśli limit czasu wygaśnie, pojawi się komunikat o błędzie z informacją "Sesja zdalna została rozłączona, ponieważ dla tego komputera nie ma licencji dostępu klienta usług pulpitu zdalnego".

Jeśli widzisz jedną z tych wiadomości, oznacza to, że obraz nie ma zainstalowanych najnowszych aktualizacji systemu Windows lub że w trybie licencjonowania pulpitu zdalnego jest instalowany za pomocą zasad grupy. Wykonaj kroki opisane w następnych sekcjach, aby sprawdzić ustawienie zasad grupy, zidentyfikować wersję systemu Windows 10 Enterprise i zainstalować odpowiednią aktualizację.  

>[!NOTE]
>Pulpit wirtualny systemu Windows wymaga licencji dostępu klienta usług pulpitu zdalnego (CAL), gdy pula hostów zawiera hosty sesji systemu Windows Server. Aby dowiedzieć się, jak skonfigurować licencję CAL usług pulpitu zdalnego, zobacz [Licencjonowanie wdrożenia usług pulpitu zdalnego z licencjami dostępu klienta](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Wyłączanie ustawienia zasad grupy trybu licencjonowania usług pulpitu zdalnego

Sprawdź ustawienie zasad grupy, otwierając Edytor zasad grupy na maszynie Wirtualnej i przechodząc do **szablonów administracyjnych** > **Składniki** > **pulpitu zdalnego Usługi pulpitu zdalnego** > **Licencjonowanie** > **hosta** > sesji Usług pulpitu zdalnego Ustaw tryb**licencjonowania usług pulpitu zdalnego**. Jeśli ustawienie zasad grupy to **Włączone,** zmień go na **Wyłączone**. Jeśli jest już wyłączona, pozostaw ją w stanie czystym.

>[!NOTE]
>Jeśli ustawisz zasady grupy za pośrednictwem domeny, wyłącz to ustawienie dla zasad, które są przeznaczone dla tych maszyn wirtualnych z wieloma sesjami systemu Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Określanie używanej wersji wielu sesji systemu Windows 10 Enterprise

Aby sprawdzić, która wersja systemu Windows 10 Enterprise ma posiadaną sesję wielosesyjną:

1. Zaloguj się za pomocą konta administratora.
2. Wpisz "Informacje" na pasku wyszukiwania obok menu Start.
3. Wybierz pozycję **Informacje o komputerze**.
4. Sprawdź numer obok pozycji "Wersja". Numer powinien być "1809" lub "1903", jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu przedstawiający okno Specyfikacje systemu Windows. Numer wersji jest wyróżniony na niebiesko.](media/windows-specifications.png)

Teraz, gdy znasz swój numer wersji, przejdź do odpowiedniej sekcji.

### <a name="version-1809"></a>Wersja 1809

Jeśli numer wersji jest komunikat "1809", zainstaluj [aktualizację KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Wersja 1903

Ponowne wdrożenie systemu operacyjnego hosta za pomocą najnowszej wersji obrazu systemu Windows 10 w wersji 1903 z galerii azure.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia puli dzierżawy i hosta w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli dzierżawy i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej (VM) na pulpicie wirtualnym systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klientów pulpitu wirtualnego systemu Windows, zobacz [Połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z klientami usług pulpitu zdalnego, zobacz [Rozwiązywanie problemów z klientem pulpitu zdalnego](troubleshoot-client.md)
- Aby rozwiązać problemy podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Pulpit wirtualny systemu Windows PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej o usłudze, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrażaniem szablonów Menedżera zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji za pomocą Menedżera zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach w celu określenia błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
