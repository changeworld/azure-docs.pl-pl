---
title: Dzierżawy i hosta tworzenie puli w Windows pulpitu wirtualnego — platformy Azure
description: Jak rozwiązać problemy podczas konfiguracji sesji i dzierżawy hosta maszyny wirtualnej (VM) w środowisku Windows pulpitu wirtualnego.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 38d59fb20776470cb683f2a2146838bb217addf7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928125"
---
# <a name="tenant-and-host-pool-creation"></a>Tworzenie puli dzierżawy i hosta

W tym artykule umożliwiają rozwiązywanie problemów, które występują podczas konfigurowania maszyny wirtualne hosta sesji pulpitu wirtualnego Windows (VM).

## <a name="provide-feedback"></a>Przekazywanie opinii

Firma Microsoft obecnie nie są zbyt przypadki pomocy technicznej Windows pulpitu wirtualnego jest dostępna w wersji zapoznawczej. Odwiedź stronę [społeczności technicznej pulpitu wirtualnego Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) w celu omówienia usługi Windows pulpitu wirtualnego przy użyciu zespół pracujący nad produktem i elementów członkowskich aktywnej społeczności użytkowników.

## <a name="vms-are-not-joined-to-the-domain"></a>Maszyny wirtualne nie są przyłączone do domeny

Wykonaj te instrukcje, jeśli występują problemy, przyłączanie maszyn wirtualnych do domeny.

- Dołączanie maszyny Wirtualnej ręcznie przy użyciu procesu w [dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) lub za pomocą [szablonu przyłączanie do domeny](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Spróbuj wykonać polecenie ping nazwa domeny z wiersza polecenia na maszynie Wirtualnej.
- Przejrzyj listę komunikatów o błędach przyłączanie do domeny w [Rozwiązywanie problemów z domeny sprzężenia komunikaty o błędach](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Błąd: Nieprawidłowe poświadczenia

**Przyczyna:** Wystąpił błąd pisowni wykonywane, gdy poświadczenia zostały wprowadzone w usługi Azure Resource Manager szablon interfejsu poprawki.

**Poprawka:** Wykonaj te instrukcje, aby poprawić poświadczenia.

1. Ręcznie Dodaj maszyny wirtualne do domeny.
2. Wdróż ponownie, gdy poświadczenia zostały potwierdzone. Zobacz [Utwórz pulę hosta przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Przyłączanie maszyn wirtualnych do domeny przy użyciu szablonu z [sprzężenia istniejącej maszyny Wirtualnej Windows do domeny AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Błąd: Limit czasu oczekiwania na dane wejściowe użytkownika

**Przyczyna:** Konto używane do przyłączania do domeny mogą mieć uwierzytelnianie wieloskładnikowe (MFA).

**Poprawka:** Wykonaj te instrukcje dotyczące przyłączania do domeny.

1. Tymczasowe usunięcie usługi MFA dla konta.
2. Użyj konta usługi.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Błąd: Konto używane podczas inicjowania obsługi nie ma uprawnień do ukończenia tej operacji

**Przyczyna:** Konto używane, nie ma uprawnienia do przyłączania maszyn wirtualnych do domeny z powodu zgodności i przepisów.

**Poprawka:** Wykonaj te instrukcje.

1. Użyj konta należącego do grupy administratorów.
2. Przyznaj uprawnienia niezbędne do kont używanych.

### <a name="error-domain-name-doesnt-resolve"></a>Błąd: Nazwa domeny nie jest rozpoznawane

**Przyczyny 1:** Maszyny wirtualne są w grupie zasobów, który nie został skojarzony z siecią wirtualną (VNET), gdzie znajduje się domeny.

**Napraw 1:** Utwórz wirtualną sieć równorzędną między siecią Wirtualną, której przeprowadzono aprowizację maszyn wirtualnych i sieci Wirtualnej, w którym działa kontroler domeny (DC). Zobacz [tworzenie komunikacji równorzędnej sieci wirtualnej — Resource Manager, w różnych subskrypcjach](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Przyczyny 2:** Korzystając z AadService (AADS), wpisy DNS nie zostały ustawione.

**Napraw 2:** Aby ustawić usług domain services, zobacz [włączyć usługi Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Wirtualny agenta pulpitu Windows i Windows wirtualnego pulpitu rozruchowym nie są zainstalowane

Zalecanym sposobem aprowizacji maszyn wirtualnych używa usługi Azure Resource Manager **tworzenie i aprowizowanie Windows pulpitu wirtualnego hosta puli** szablonu. Ten szablon automatycznie instaluje wirtualnej agenta pulpit Windows i Windows wirtualnego pulpitu agenta moduł ładujący rozruchu.

Wykonaj te instrukcje, aby upewnić się, że składniki są zainstalowane i sprawdzić komunikaty o błędach.

1. Upewnij się, że dwa składniki są zainstalowane, sprawdzając **Panelu sterowania** > **programy** > **programy i funkcje**. Jeśli **wirtualnej agenta pulpitu Windows** i **Windows wirtualnego pulpitu agenta moduł ładujący rozruchu** są niewidoczne, nie są zainstalowane na maszynie Wirtualnej.
2. Otwórz **Eksploratora plików** i przejdź do **C:\Windows\Temp\scriptlogs.log**. Brakuje pliku wskazuje, że DSC programu PowerShell, który zainstalowany dwa składniki nie był w stanie do uruchamiania w kontekście zabezpieczeń, pod warunkiem.
3. Jeśli plik **C:\Windows\Temp\scriptlogs.log** jest obecne, otwórz go i sprawdzić komunikaty o błędach.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Błąd: Brak wirtualnej agenta pulpit Windows i Windows wirtualnego pulpitu agenta moduł ładujący rozruchu. Ponadto brak C:\Windows\Temp\scriptlogs.log

**Przyczyny 1:** Poświadczenia podane podczas wprowadzania dla szablonu usługi Azure Resource Manager są niepoprawne lub uprawnienia były niewystarczające.

**Napraw 1:** Ręcznie dodaj brakujące składniki do maszyn wirtualnych przy użyciu [Utwórz pulę hosta przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Przyczyny 2:** DSC programu PowerShell można było uruchomić i wykonywanie, ale nie można wykonać, ponieważ nie można zalogować się do wirtualnego pulpitu Windows i uzyskać potrzebne informacje.

**Napraw 2:** Upewnij się, elementy z poniższej listy.

- Upewnij się, że konto nie ma usługi MFA.
- Upewnij się, że nazwa dzierżawy są dokładne i dzierżawa istnieje w Windows pulpitu wirtualnego.
- Upewnij się, konto ma co najmniej uprawnienia współautora usług pulpitu zdalnego.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Błąd: Uwierzytelnianie nie powiodło się, błąd C:\Windows\Temp\scriptlogs.log

**Przyczyna:** Nie można wykonać DSC programu PowerShell, ale nie można nawiązać połączenia z pulpitem wirtualnym Windows.

**Poprawka:** Upewnij się, elementy z poniższej listy.

- Maszyny wirtualne należy ręcznie zarejestrować w usłudze Windows pulpitu wirtualnego.
- Upewnij się, że konto używane do nawiązywania połączenia z pulpitem wirtualnym Windows ma uprawnienia w dzierżawie, aby utworzyć pule hosta.
- Upewnij się, że konto nie ma usługi MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Nie rejestruje Windows wirtualnego pulpitu agenta z usługą Windows pulpitu wirtualnego

Podczas pierwszej instalacji Windows Agent pulpitu wirtualnego w sesji hostować maszyny wirtualne (albo ręcznie lub za pomocą szablonu usługi Azure Resource Manager i programu PowerShell DSC), zapewnia tokenu rejestracji. W poniższej sekcji opisano rozwiązywanie problemów mające zastosowanie do Windows Agent pulpitu wirtualnego, a token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Błąd: Stan zgłoszone w poleceniu cmdlet Get-RdsSessionHost stan jest wyświetlany jako niedostępny

![Polecenie cmdlet Get-RdsSessionHost stan jest wyświetlany jako niedostępna.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Przyczyna:** Agent nie jest w stanie automatycznie zaktualizowana do nowej wersji.

**Poprawka:** Wykonaj te instrukcje, aby ręcznie zaktualizować agenta.

1. Pobierz nową wersję agenta na hoście sesji maszyny Wirtualnej.
2. Uruchom Menedżera zadań, a następnie na karcie usługi, należy zatrzymać usługę RDAgentBootLoader.
3. Uruchom Instalatora z nową wersją Windows Agent pulpitu wirtualnego.
4. Po wyświetleniu monitu dla tokenu rejestracji, Usuń wpis INVALID_TOKEN, a następnie naciśnij klawisz Dalej (nowy token nie jest wymagane).
5. Wykonaj Kreatora instalacji.
6. Otwórz Menedżera zadań, a następnie uruchom usługę RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Błąd:  Wpis rejestru wirtualnego agenta pulpitu Windows IsRegistered znajduje się wartość 0

**Przyczyna:** Token rejestracji wygasł lub został wygenerowany przy użyciu wartości wygaśnięcia 999999.

**Poprawka:** Wykonaj te instrukcje, aby naprawić błąd rejestru agenta.

1. Jeśli istnieje już tokenu rejestracji, należy go usunąć za pomocą Remove RDSRegistrationInfo.
2. Wygeneruj nowy token z NewRegistrationInfo usług pulpitu zdalnego.
3. Sprawdź, czy parametr - ExpriationHours jest ustawiona do 72 (wartość maksymalna to 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Błąd: Agent Windows pulpitu wirtualnego nie zgłasza pulsu, podczas uruchamiania Get RdsSessionHost

**Przyczyny 1:** RDAgentBootLoader usługa została zatrzymana.

**Napraw 1:** Uruchom Menedżera zadań i, jeśli karta usługi zgłasza stan zatrzymania usługi RDAgentBootLoader, uruchom usługę.

**Przyczyny 2:** Może zostać zamknięty port 443.

**Napraw 2:** Wykonaj te instrukcje, aby otworzyć port 443.

1. Upewnij się, port 443 został otwarty, pobierając narzędzie PSPing z [narzędzia Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Zainstaluj narzędzie PSPing na hoście sesji maszyny Wirtualnej, w którym działa agent.
3. Otwórz wiersz polecenia jako administrator i wydać poniższe polecenie:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Upewnij się, te informacje PSPing Odebrano powrót po awarii z RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Rozwiązywanie problemów ze stosem side-by-side Windows pulpitu wirtualnego

Windows pulpitu wirtualnego stosu side-by-side jest automatycznie instalowany z systemu Windows Server w 2019 r. Użyj Instalatora Microsoft (MSI), aby zainstalować stosu side-by-side na Microsoft Windows Server 2016 lub Windows Server 2012 R2. Program Microsoft Windows 10, Windows pulpitu wirtualnego stosu side-by-side jest włączona przy użyciu **enablesxstackrs.ps1**.

Istnieją trzy sposoby stosu side-by-side pobiera zainstalowane lub włączone w sesji hosta puli maszyn wirtualnych:

- Usługi za pomocą usługi Azure Resource Manager **tworzenie i aprowizowanie nowej puli hosta wirtualnego pulpitu Windows** szablonu
- Jest włączone i włączona obrazu wzorcowego
- Zainstalowana lub włączona ręcznie na każdej maszynie Wirtualnej (lub za pomocą rozszerzenia/programu PowerShell)

Jeśli występują problemy ze stosem side-by-side Windows pulpitu wirtualnego, wpisz **qwinsta** polecenie w wierszu polecenia, aby upewnić się, że stos side-by-side jest zainstalowana lub włączona.

Dane wyjściowe **qwinsta** zostanie wyświetlona lista **rdp sxs** w danych wyjściowych, jeśli stosu side-by-side jest zainstalowane i włączone.

![Stos Side-by-side jest zainstalowana lub włączona przy użyciu qwinsta wymieniony jako rdp-sxs w danych wyjściowych.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Sprawdź wpisy rejestru wymienionych poniżej i upewnij się, że ich wartości są zgodne. Jeśli wartości są niezgodne. Brak kluczy rejestru, postępuj zgodnie z instrukcjami [Utwórz pulę hosta przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) na temat sposobu ponownej instalacji stosu side-by-side.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Błąd: O_REVERSE_CONNECT_STACK_FAILURE

![Kod błędu O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Przyczyna:** Stos side-by-side nie jest zainstalowany na hoście sesji maszyny Wirtualnej.

**Poprawka:** Wykonaj te instrukcje, aby zainstalować stosu side-by-side na hoście sesji maszyny Wirtualnej.

1. Użyj protokołu RDP (Remote Desktop), aby uzyskać bezpośrednio do maszyny Wirtualnej hosta sesji jako administrator lokalny.
2. Pobierz i zaimportuj [modułu programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.
3. Instalowanie przy użyciu stosu side-by-side [Utwórz pulę hosta przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Jak naprawić stos side-by-side Windows pulpitu wirtualnego, który działa

Są to znane okoliczności, które mogą spowodować niepoprawne działanie stosu side-by-side:

- Nieobserwowane odpowiedniej kolejności kroki umożliwiające stosu side-by-side
- Automatyczna aktualizacja do systemu Windows 10 rozszerzone wszechstronna dysku (EVD)
- Brak roli hosta sesji usług pulpitu zdalnego (RDSH)
- Uruchamianie wielokrotne enablesxsstackrc.ps1
- Uruchamianie enablesxsstackrc.ps1 na koncie nie ma uprawnień administratora lokalnego

Instrukcje w tej sekcji mogą pomóc w odinstalować Windows pulpitu wirtualnego stosu side-by-side. Po odinstalowaniu stosu side-by-side, przejdź do obszaru "Zarejestruj maszynę Wirtualną z pulą hosta pulpitów wirtualnych Windows" [Utwórz pulę hosta przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) ponowna instalacja stosu side-by-side.

Maszyn wirtualnych używanych do uruchamiania korygowania musi być w tej samej podsieci i domen jako maszyny Wirtualnej ze stosem nieprawidłowo side-by-side.

Wykonaj te instrukcje, aby uruchomić korygowania z tej samej podsieci i domen:

1. Połącz przy użyciu standardowego protokołu RDP (Remote Desktop) do maszyny Wirtualnej, z której zostaną zastosowane poprawki.
2. Pobierz narzędzia PsExec z https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Rozpakuj pobrany plik.
4. Uruchom wiersz polecenia jako administrator lokalny.
5. Przejdź do folderu, w którym zostało rozpakowane PsExec.
6. Z wiersza polecenia użyj następującego polecenia:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname to nazwa komputera maszyny wirtualnej ze stosem nieprawidłowo side-by-side.

7. Zaakceptuj Umowę licencyjną programu PsExec, klikając przycisk Zgadzam się.

    ![Zrzut ekranu umowy licencyjne oprogramowania.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >To okno dialogowe będzie wyświetlana tylko podczas pierwszego uruchomienia narzędzia PsExec.

8. Po otwarciu sesji wiersza polecenia na maszynie Wirtualnej z nieprawidłowo stosu side-by-side Uruchom qwinsta i upewnij się, że wpis o nazwie sxs protokołu rdp jest dostępny. W przeciwnym razie stosu side-by-side nie jest obecny na maszynie Wirtualnej, więc ten problem nie jest powiązany z stosu side-by-side.

    ![Wiersz polecenia administratora](media/AdministratorCommandPrompt.png)

9. Uruchom następujące polecenie, które zostanie wyświetlona lista składników Microsoft zainstalowane na maszynie Wirtualnej ze stosem nieprawidłowo side-by-side.

    ```cmd
        wmic product get name
    ```

10. Uruchom poniższe polecenie za pomocą nazw produktów w poprzednim kroku.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Odinstaluj wszystkie produkty, które zaczyna się "Pulpit zdalny".

12. Po odinstalowaniu wszystkich składników pulpitu wirtualnego Windows postępuj zgodnie z instrukcjami, systemu operacyjnego:

13. Jeśli system operacyjny Windows Server, należy ponownie uruchomić maszynę Wirtualną, która ma nieprawidłowe działanie stosu side-by-side (lub za pomocą witryny Azure portal za pomocą narzędzia PsExec).

Jeśli system operacyjny Microsoft Windows 10, Kontynuuj z poniższymi instrukcjami:

14. Z maszyny Wirtualnej z uruchomionym programu PsExec Otwórz Eksploratora plików i skopiuj disablesxsstackrc.ps1 na dysk systemowy maszyny wirtualnej ze stosem malfunctioned side-by-side.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname to nazwa komputera maszyny wirtualnej ze stosem nieprawidłowo side-by-side.

15. Zalecana procedura: za pomocą narzędzia PsExec Uruchom program PowerShell i przejdź do folderu z poprzedniego kroku i uruchom disablesxsstackrc.ps1. Alternatywnie można uruchomić następujące polecenia cmdlet:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Gdy gotowe polecenia cmdlet uruchomione, ponowne uruchomienie maszyny Wirtualnej ze stosem nieprawidłowo side-by-side.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać ogólne na temat rozwiązywania problemów ścieżki eskalacji i pulpitu wirtualnego Windows zobacz [Rozwiązywanie problemów — omówienie, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązywać problemy podczas tworzenia puli dzierżawy i hosta w środowisku Windows pulpitu wirtualnego, zobacz [dzierżawy i hostów puli tworzenia](troubleshoot-set-up-issues.md).
- Aby rozwiązywać problemy podczas konfigurowania maszyny wirtualnej (VM) w Windows pulpitu wirtualnego, zobacz [konfigurację maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązywać problemy z połączeniami klienta pulpitu wirtualnego Windows, zobacz [połączeń klienta usług pulpitu zdalnego](troubleshoot-client-connection.md).
- Aby rozwiązywać problemy podczas korzystania z programu PowerShell z usługą Windows pulpitu wirtualnego, zobacz [środowiska PowerShell pulpitu wirtualnego Windows](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [Windows Desktop w wersji zapoznawczej środowiska](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [samouczka: Rozwiązywanie problemów z wdrożeniami szablonu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Aby dowiedzieć się więcej na temat inspekcji akcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach, aby określić błędy podczas wdrażania, zobacz [wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).