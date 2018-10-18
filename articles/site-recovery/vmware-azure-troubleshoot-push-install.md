---
title: Rozwiązywanie problemów z błędami instalacji wypychanej usługi mobilności podczas Włącz Replication(VMware to Azure) | Dokumentacja firmy Microsoft
description: Podczas replikowania maszyn wirtualnych platformy Azure, należy rozwiązać błędy instalacji wypychanej/usługi mobilności.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 09/19/2018
ms.openlocfilehash: 4c57d048f4c3222ac180355a6a700562415f601c
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390197"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Rozwiązywanie problemów z instalacją wypychaną usługi mobilności

Instalacja usługi mobilności jest krokiem podczas włączania replikacji. Powodzenie ten krok zależy wyłącznie spełnienie wymagań wstępnych i pracą z nimi przy użyciu obsługiwanych konfiguracji. Najbardziej typowe błędy, stosowanych podczas instalacji usługi mobilności są ze względu na

* Poświadczenie/uprawnień błędy
* Błędy związane z łącznością
* Nieobsługiwanych systemów operacyjnych

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
  * Z wiersza polecenia komputera serwera źródłowego, użyj Telnet, aby wykonać polecenie ping do serwera konfiguracji / skalowalny w poziomie przetwarzania serwera przy użyciu portu https (domyślnie 9443), jak pokazano poniżej, aby zobaczyć, jeśli istnieją problemy z połączeniem sieciowym lub problemy z blokowaniem portów zapory.

     `telnet <CS/ scale-out PS IP address> <port>`

  * Jeśli nie można się połączyć, Zezwalaj na przychodzące port 9443 na serwerze konfiguracji / skalowalny w poziomie serwerze przetwarzania.
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

Aby uzyskać **nowsze wersje**, postępuj zgodnie z instrukcjami [tutaj](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) umożliwiające udostępnianie plików i drukarek

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

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](vmware-azure-tutorial.md) skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych VMware.