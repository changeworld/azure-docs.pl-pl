---
title: Rozwiązywanie problemów z błędami instalacji wypychanej usługi mobilności, podczas włączania replikacji na potrzeby odzyskiwania po awarii | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z błędami instalacji usługi mobilności, podczas włączania replikacji na potrzeby odzyskiwania po awarii
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 10/29/2018
ms.openlocfilehash: a9738f95ce8a0de750ffa348e167bce3b0e659f6
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821399"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Rozwiązywanie problemów z instalacją wypychaną usługi mobilności

Instalacja usługi mobilności jest krokiem podczas włączania replikacji. Powodzenie ten krok zależy wyłącznie spełnienie wymagań wstępnych i pracą z nimi przy użyciu obsługiwanych konfiguracji. Najbardziej typowe błędy, stosowanych podczas instalacji usługi mobilności są ze względu na

* Poświadczenie/uprawnień błędy
* Błędy związane z łącznością
* Nieobsługiwanych systemów operacyjnych
* Błędy instalacji usługi VSS

Po włączeniu replikacji usługa Azure Site Recovery próbuje wypchnąć zainstalować agenta usługi mobilności na maszynie wirtualnej. W ramach tego serwera konfiguracji próbuje nawiązywanie połączenia z maszyną wirtualną i kopiowanie agenta. Aby umożliwić pomyślną instalację, postępuj zgodnie z wskazówki dotyczące rozwiązywania problemów krok po kroku przedstawionych poniżej.

## <a name="credentials-check-errorid-95107--95108"></a>Sprawdź poświadczenia (identyfikator błędu: 95107 & 95108)

* Sprawdź, czy konto użytkownika podczas. Włączanie replikacji **prawidłowy, dokładne**.
* Usługa Azure Site Recovery wymaga **uprawnienie administratora** do przeprowadzenia instalacji wypychanej.
  * Windows, sprawdź, czy konto użytkownika ma dostęp administracyjny lokalnego lub domeny, na maszynie źródłowej.
  * Jeśli nie używasz konta domeny, należy wyłączyć kontrolę dostępu użytkowników zdalnych na komputerze lokalnym.
    * Aby wyłączyć kontrolę dostępu użytkowników zdalnych, w kluczu rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System Dodaj nową wartość typu DWORD: LocalAccountTokenFilterPolicy. Ustaw wartość na 1. Aby wykonać ten krok, uruchom następujące polecenie w wierszu polecenia:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Dla systemu Linux musisz wybrać konto główne dla pomyślnej instalacji agenta mobilności.

Jeśli chcesz zmodyfikować poświadczeń konta wybranego użytkownika, postępuj zgodnie z instrukcjami [tutaj](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="connectivity-check-errorid-95117--97118"></a>**Sprawdzenie łączności (identyfikator błędu: 95117 & 97118)**

* Upewnij się, że możesz wysłać polecenie ping do komputera źródłowego z serwera konfiguracji. Jeśli serwera przetwarzania skalowalnego w poziomie zostały wybrane podczas. Włączanie replikacji, upewnij się, że jesteś w stanie wysłać polecenie ping z serwera przetwarzania maszyna źródłowa.
  * Z wiersza polecenia komputera serwera źródłowego, użyj Telnet, aby wykonać polecenie ping do serwera konfiguracji / skalowalny w poziomie serwera przetwarzania za pomocą portu https (135), jak pokazano poniżej, aby zobaczyć, jeśli istnieją problemy z połączeniem sieciowym lub problemy z blokowaniem portów zapory.

     `telnet <CS/ scale-out PS IP address> <135>`
  * Sprawdź stan usługi **InMage Scout VX Agent — Sentinel/Outpost**. Uruchom usługę, jeśli nie jest uruchomiona.
* Ponadto w przypadku **maszyny Wirtualnej systemu Linux**,
  * Sprawdź, czy są zainstalowane najnowsze pakiety openssh, openssh-server i openssl.
  * Sprawdź i upewnij się, że Secure Shell (SSH) jest włączona i działa na porcie 22.
  * SFTP usługi powinny być uruchomione. Aby włączyć SFTP podsystem i uwierzytelnianie hasłem w pliku sshd_config
    * Zaloguj się jako użytkownik główny.
    * Przejdź do pliku /etc/ssh/sshd_config i znajdź wiersz, który rozpoczyna się od PasswordAuthentication.
    * Usuń znaczniki komentarza i zmień wartość na tak
    * Znajdź wiersz, który rozpoczyna się od podsystemu i usuń znaczniki komentarza
    * Uruchom ponownie usługę sshd.
* Próba połączenia może nie powiodło się jeśli nie będzie odpowiednie odpowiedzi po upływie określonego czasu lub ustanowionego połączenia nie powiodło się, ponieważ połączony host nie odpowiedział.
* Łączność / / domeny sieciowej może być problemu związanego z. Można również ze względu na nazwę DNS, rozwiązywania problemów lub problem wyczerpanie portów TCP. Sprawdź, czy istnieją znane problemy w domenie.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Sprawdzanie usług udostępniania plików i drukarek (identyfikator błędu: 95105 & 95106)

Po sprawdzenie łączności Sprawdź, czy na maszynie wirtualnej włączono usługi udostępniania plików i drukarek.

Aby uzyskać **windows 2008 R2 i wcześniejsze wersje**,

* Aby włączyć udostępnianie plików i drukarek przez zaporę Windows
  * Otwórz panel sterowania -> System i Zabezpieczenia -> zapory Windows -> w okienku po lewej stronie, kliknij przycisk Zaawansowane ustawienia -> w drzewie konsoli kliknij pozycję reguły ruchu przychodzącego.
  * Znajdź reguły pliku i udostępnianie drukarki (sesji NB — ruch przychodzący) i udostępnianie plików i drukarek (ruch przychodzący SMB). Dla każdej reguły, kliknij prawym przyciskiem myszy regułę, a następnie kliknij przycisk **Włącz regułę**.
* Aby włączyć udostępnianie za pomocą zasad grupy plików
  * Przejdź do ekranu startowego, wpisz polecenie gpmc.msc i wyszukiwania.
  * W okienku nawigacji otwórz następujące foldery: lokalne zasady komputera, Konfiguracja użytkownika, Szablony administracyjne, Windows, składników i udostępniania w sieci.
  * W okienku szczegółów kliknij dwukrotnie **uniemożliwić użytkownikom udostępnianie plików w swoim profilu**. Aby wyłączyć ustawienia zasad grupy, a następnie włącz możliwość jego udostępniania plików, kliknij przycisk wyłączone. Kliknij przycisk OK, aby zapisać zmiany. Aby dowiedzieć się więcej, kliknij przycisk [tutaj](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Aby uzyskać **nowsze wersje**, postępuj zgodnie z instrukcjami [tutaj](vmware-azure-install-mobility-service.md) umożliwiające udostępnianie plików i drukarek.

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Sprawdzenie konfiguracji Instrumentacji zarządzania Windows (WMI)

Po sprawdzić usług plików i drukarek, należy włączyć usługę WMI przez zaporę.

* W Panelu sterowania kliknij pozycję zabezpieczenia, a następnie kliknij zaporę Windows.
* Kliknij przycisk Zmień ustawienia, a następnie kliknij kartę Wyjątki.
* W oknie wyjątki zaznacz pole wyboru dla Windows Management Instrumentation (WMI) aby umożliwić ruch usługi WMI przez zaporę. 

Można również włączyć ruch usługi WMI przez zaporę, w tym celu w wierszu polecenia. Użyj następującego polecenia `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Inne artykuły dotyczące rozwiązywania problemów WMI można znaleźć w następujących artykułach.

* [Podstawowe testy usługi WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Rozwiązywanie problemów z usługą WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Rozwiązywanie problemów ze skryptami WMI i usługi WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nieobsługiwanych systemów operacyjnych

Inny najbardziej typową przyczyną błędu może być spowodowany nieobsługiwany system operacyjny. Upewnij się, że używasz obsługiwanej wersji jądra systemu operacyjnego/pomyślną instalację usługi mobilności.

Aby dowiedzieć się, o które systemy operacyjne są obsługiwane przez usługę Azure Site Recovery, zobacz nasze [dokumencie macierz obsługi](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="vss-installation-failures"></a>Błędy instalacji usługi VSS

Instalacja usługi VSS jest częścią instalacji agenta mobilności. Ta usługa jest używana podczas generowania punktów odzyskiwania zapewniających spójność aplikacji. Błędy podczas instalacji usługi VSS, może wystąpić z kilku powodów. Aby zidentyfikować dokładną błędy, zapoznaj się **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Kilka typowych błędów i kroki rozwiązania zostaną wyróżnione w poniższej sekcji.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Błąd VSS-2147023170 [0x800706BE] - kod zakończenia 511

Ten problem występuje najczęściej, gdy oprogramowanie antywirusowe blokuje operacje usługi Azure Site Recovery. Aby rozwiązać problem,

1. Wyklucz wszystkie foldery, o których wspomniano [tutaj](vmware-azure-set-up-source.md#exclude-antivirus-on-the-configuration-server).
2. Postępuj zgodnie z wytycznymi opublikowanych przez dostawcą oprogramowania antywirusowego w taki sposób, aby odblokować rejestracja biblioteki DLL w Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Błąd VSS 7 [0x7] - kod zakończenia 511

To jest błąd w czasie wykonywania i wynika z powodu braku pamięci, aby zainstalować usługę VSS. Upewnij się, że Zwiększ ilość miejsca na dysku w przypadku pomyślnego wykonania tej operacji.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Błąd VSS-2147023824 [0x80070430] - kod zakończenia 517

Ten błąd występuje, gdy usługa dostawcy usługi VSS usługi Azure Site Recovery jest [oznaczone do usunięcia](https://msdn.microsoft.com/en-us/library/ms838153.aspx). Spróbuj ręcznie zainstalować usługi VSS na maszynie źródłowej, uruchamiając następujące polecenie w wierszu

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Błąd VSS-2147023841 [0x8007041F] - 512. kod zakończenia

Ten błąd występuje, gdy baza danych usługi Dostawca VSS usługi Azure Site Recovery jest [zablokowane](https://msdn.microsoft.com/en-us/library/ms833798.aspx). Spróbuj ręcznie zainstalować usługi VSS na maszynie źródłowej, uruchamiając następujące polecenie w wierszu

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>Kod 806 zakończenia usługi VSS

Ten błąd występuje, gdy konto użytkownika używane do instalacji nie ma uprawnień do wykonania polecenia CSScript. Zapewniają wystarczających uprawnień do konta użytkownika do wykonywania skryptu i spróbuj ponownie wykonać operację.

### <a name="other-vss-errors"></a>Inne błędy usługi VSS

Spróbuj ręcznie zainstalować usługę dostawcy usługi VSS na maszynie źródłowej, uruchamiając następujące polecenie w wierszu

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](vmware-azure-tutorial.md) skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych VMware.