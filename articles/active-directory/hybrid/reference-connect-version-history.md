---
title: 'Program Azure AD Connect: Historia wersji | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera listę wszystkich wersji programu Azure AD Connect i Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fafd853250ed76b49b66b86ffda9c91240c8ce48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109153"
---
# <a name="azure-ad-connect-version-release-history"></a>Program Azure AD Connect: Historia wersji
Zespół usługi Azure Active Directory (Azure AD) regularnie aktualizuje program Azure AD Connect z nowych funkcji i funkcji. Nie wszystkie dodatki mają zastosowanie do wszystkich odbiorców.


W tym artykule została zaprojektowana, aby pomóc Ci śledzić wersje, które zostały zwolnione i aby zrozumieć, jakie zmiany są w najnowszej wersji.

Ta tabela znajduje się lista Tematy pokrewne:

Temat |  Szczegóły
--------- | --------- |
Kroki, aby uaktualnić program Azure AD Connect | Różne metody [uaktualnianie z poprzedniej wersji do najnowszej wersji](how-to-upgrade-previous-version.md) wersji Azure AD Connect.
Wymagane uprawnienia | Uprawnienia wymagane do zastosowania aktualizacji, zobacz [konta i uprawnienia](reference-connect-accounts-permissions.md#upgrade).

Pobierz | [Pobieranie programu Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Udostępnia nową wersję programu Azure AD Connect to proces, który wymaga kilku kroku kontroli jakości w celu zapewnienia ich funkcjonalności operacji usługi, a gdy przejdziemy przez ten proces zostanie zaktualizowany numer wersji w nowej wersji, a także stanu wydania aby odzwierciedlały najnowsze stanu.
Gdy przejdziemy przez ten proces, numer wersji, wersji będą wyświetlane ze znakiem "X" w pozycji numeru wersji pomocniczej, jak "1.3.X.0" — oznacza to, że informacje o wersji, w tym dokumencie są prawidłowe dla wszystkich wersji rozpoczynające się od "1.3.". Jak najszybciej ostatecznych procesu tworzenia wersji numer wydania zostaną zaktualizowane do wersji niedawno wydany i stanu wersji zostanie zaktualizowana w celu "Wydania do pobrania i automatyczne uaktualnianie".
Nie wszystkie wersje programu Azure AD Connect będzie dostępna dla uaktualnienie automatyczne. Stan wersji wskazuje, czy wydanie jest udostępniana dla uaktualnienie automatyczne lub tylko pobieranie. Jeśli automatyczne uaktualnianie zostało włączone na serwerze programu Azure AD Connect tego serwera zostanie automatycznie uaktualniona do najnowszej wersji programu Azure AD Connect, wydawanego dla uaktualnienie automatyczne. Należy pamiętać, że nie wszystkie konfiguracje program Azure AD Connect kwalifikujące się do automatycznego uaktualnienia. Postępuj zgodnie z tego linku, aby dowiedzieć się więcej o [automatyczne uaktualnianie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)

## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Istnieje znany problem z uaktualniania starszej wersji usługi Azure AD Connect do 1.3.21.0 gdzie portalu usługi Office 365 nie będzie odzwierciedlał zaktualizowanej wersji, nawet jeśli program Azure AD Connect uaktualniona pomyślnie.
>
> Aby rozwiązać problem należy zaimportować **AdSync** modułu, a następnie uruchom`Set-ADSyncDirSyncConfiguration` polecenia cmdlet programu powershell na serwerze programu Azure AD Connect.  Można użyć następujących czynności:
>
>1. Otwórz program Powershell w trybie administratora
>2. Uruchom polecenie `Import-Module "ADSync"`
>3. Uruchom polecenie `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`
 


### <a name="release-status"></a>Stan zlecenia 

05/14/2019: Ogólnie dostępnych do pobrania


### <a name="fixed-issues"></a>Rozwiązane problemy 

- Naprawiono podniesienie uprawnień w Microsoft Azure Active Directory Connect kompilacji 1.3.20.0.  Tę lukę w zabezpieczeniach, w pewnych okolicznościach może umożliwić atakującemu dwóch poleceń cmdlet programu powershell są wykonywane w kontekście konta uprzywilejowanego i wykonania uprzywilejowanych akcji.  Ta aktualizacja zabezpieczeń usuwa ten problem, wyłączając te polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [aktualizacji zabezpieczeń](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Stan zlecenia 

04/24/2019: Ogólnie dostępnych do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 

- Dodano obsługę Odśwież domeny 
- Funkcja foldery publiczne poczty programu Exchange przechodzi GA 
- Poprawa Kreatora obsługi błędów dla błędów usługi 
- Dodano łącze ostrzeżenie dla starego interfejsu użytkownika na stronie właściwości łącznika. 
- Ta funkcja ujednoliconego zapisywania zwrotnego grup jest teraz ogólnie 
- Ulepszone samoobsługowego resetowania HASEŁ komunikat o błędzie podczas kontroler domeny nie ma kontrolki LDAP 
- Dodano diagnostykę dla modelu DCOM rejestru błędy podczas instalacji  
- Ulepszone śledzenie błędów wersji RPC 
- Zezwalaj na poświadczenia EA z domeny podrzędnej 
- Zezwalaj na nazwę bazy danych ma zostać nawiązane podczas instalacji (domyślna nazwa ADSync)
- Uaktualnij do biblioteki ADAL 3.19.8 na odebranie poprawkę WS-Trust, dla polecenia Ping i dodanie obsługi dla nowych wystąpień platformy Azure 
- Modyfikuj grupę reguł synchronizacji przepływ samAccountName, DomainNetbios i DomainFQDN do chmury — wymagane oświadczenia 
- Zmodyfikowane domyślna obsługa reguły synchronizacji — Dowiedz się więcej [tutaj](how-to-connect-fix-default-rules.md).
- Dodano nowy agent, uruchomione jako usługa systemu windows. Ten agent o nazwie "Admin Agent", umożliwia bardziej szczegółową diagnostykę zdalnego z serwera Azure AD Connect, aby ułatwić Engineers firmy Microsoft rozwiązywanie problemów podczas otwierania zgłoszenia do pomocy technicznej. Ten agent nie jest zainstalowane i jest domyślnie włączone.  Aby uzyskać więcej informacji na temat sposobu instalowania i włączyć agenta zobacz [co to jest Agent programu Azure AD Connect administratora?](whatis-aadc-admin-agent.md). 
- Zaktualizowana Umowa licencyjna użytkownika (oprogramowania EULA) 
- Dodano obsługę uaktualniania automatycznie w przypadku wdrożeń korzystających z usług AD FS jako ich typ logowania.  To również usunięcie wymagania aktualizacji usługi AD FS Azure AD zaufania jednostki uzależnionej w ramach procesu uaktualniania. 
- Dodano zadanie zarządzania zaufania usługi Azure AD, która oferuje dwie opcje: analizowanie/zaktualizuj zaufania i resetowania relacji zaufania. 
- Zmienione zachowanie relacji zaufania jednostki uzależnionej strona usługi AD FS Azure AD, aby zawsze używa przełącznika - SupportMultipleDomain (obejmuje zaufania i domeny usługi Azure AD aktualizacji). 
- Tak, że wymaga certyfikatu PFX, usuwając możliwość korzystania z wstępnie zainstalowany certyfikat, należy zmienić zachowanie instalacji nowej usługi AD FS farmy.
- Zaktualizowany przepływ pracy instalacji nowej usługi AD FS farmy umożliwia tylko wdrożenie 1 AD FS i 1 serwer proxy aplikacji sieci Web.  Wszystkie dodatkowe serwery zostaną wykonane po wstępnej instalacji. 

### <a name="fixed-issues"></a>Rozwiązane problemy 


- Poprawka SQL ponowne łączenie logiki dla usługi ADSync 
- Napraw, aby umożliwić czysta instalacja przy użyciu pustego SQL DB AOA 
- Napraw skrypt PS uprawnień, aby dostosować uprawnienia GWB 
- Napraw błędy usługi VSS z bazą danych LocalDB  
- Naprawiono mylący komunikat o błędzie, gdy typ obiektu jest poza zakresem 
- Rozwiązany problem gdzie instalacji programu Azure AD PowerShell na serwerze może powodować konflikt typu zestawu za pomocą usługi Azure AD Connect. 
- Usunięto usterkę występującą na serwer przejściowy, po zaktualizowaniu poświadczenia łącznika w Interfejsie użytkownika starej wersji. 
- Rozwiązano niektóre przecieki pamięci 
- Różne poprawki Autoupgrade 
- Różne poprawki do eksportowania i niepotwierdzone przetwarzania importu 
- Usunięto usterkę za pomocą obsługi ukośnik odwrotny w domenie i jednostce Organizacyjnej filtrowania 
- Rozwiązano problem, w którym ma więcej niż 2 minuty, aby zatrzymać usługi ADSync, a powoduje, że problem podczas uaktualniania. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Stan zlecenia

12/18/2018: Ogólnie dostępnych do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

Ta kompilacja aktualizacji niestandardowych łączników (na przykład ogólnego łącznika LDAP i ogólnego łącznika SQL), dostarczane z programem Azure AD Connect. Więcej informacji na temat łączników dotyczy, na ten temat można znaleźć w wersji 1.1.911.0 w [Historia wersji łącznika](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Stan zlecenia
12/11/2018: Ogólnie dostępnych do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy
Ta kompilacja poprawka umożliwia użytkownikowi wybranie domeny docelowej, w określonym lesie, dla kontenera RegisteredDevices podczas włączania zapisywania zwrotnego urządzeń.  W poprzednich wersjach, które zawierają nowe funkcje opcji urządzenia (1.1.819.0 — 1.2.68.0), lokalizację kontenera RegisteredDevices była ograniczona do katalogu głównego lasu i nie zezwolono na domeny podrzędne.  To ograniczenie tylko dyskowe widoczne sam w nowych wdrożeniach — nie miała wpływu uaktualnienia w miejscu.  

Jeśli każda kompilacja zawierająca zaktualizowanej funkcji Opcje urządzenia został wdrożony na nowym serwerze i zapisywania zwrotnego urządzeń zostało włączone, należy ręcznie określić lokalizację kontenera, jeśli nie chcesz go w katalogu głównym lasu.  Aby to zrobić, musisz wyłączyć funkcję zapisywania zwrotnego urządzeń i ponownie włącz go, która pozwoli określić lokalizację kontenera na stronie "Las zapisywania zwrotnego".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Stan zlecenia 

11/30/2018:  Ogólnie dostępnych do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

Ta kompilacja poprawka rozwiązuje konflikt, gdzie może wystąpić błąd uwierzytelniania z powodu obecności niezależny moduł MSOnline galerii programu PowerShell na serwerze synchronizacji.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Stan zlecenia 

11/19/2018:  Ogólnie dostępnych do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

Ta kompilacja poprawkę rozwiązuje regresji w poprzedniej kompilacji, gdzie funkcji zapisywania zwrotnego haseł nie powiedzie się, korzystając z DODAJE kontroler domeny w systemie Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Stan zlecenia 

10 25 2018: ogólnie dostępnych do pobrania

 
### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 


- Zmienione funkcje zapisu atrybutu zapewnienie hostowanej poczta głosowa działa zgodnie z oczekiwaniami.  W niektórych scenariuszach usługi Azure AD zostało zastępowanie atrybutu msExchUcVoicemailSettings podczas zapisu o wartości null.  Usługi Azure AD obecnie nie jest już usunie w środowisku lokalnym wartość tego atrybutu, jeśli nie ustawiono wartości chmury.
- W kreatorze program Azure AD Connect, aby zbadać i zidentyfikować problemy z łącznością z usługą Azure AD, należy dodać diagnostyki. Te funkcje diagnostyki w tej samej można także uruchomić bezpośrednio za pomocą programu Powershell przy użyciu polecenia AdSyncAzureServiceConnectivity Cmdlet Test. 
- W kreatorze program Azure AD Connect, aby zbadać i zidentyfikować problemy z łącznością z usługi AD, należy dodać diagnostyki. Te funkcje diagnostyki w tej samej można także uruchomić bezpośrednio za pomocą programu Powershell przy użyciu funkcji uruchamiania ConnectivityValidation w module ADConnectivityTools Powershell.  Aby uzyskać więcej informacji, zobacz [co to jest moduł programu PowerShell ADConnectivityTool?](how-to-connect-adconnectivitytools.md)
- Dodano AD w wersji wstępnej sprawdzanie schematu do hybrydowej usługi Azure Active Directory Join i zapisywania zwrotnego urządzeń 
- Zmienić być liter — wyszukiwanie atrybut strony rozszerzenie katalogu.
-   Dodano pełną obsługę protokołu TLS 1.2. Ta wersja obsługuje wszystkie pozostałe protokoły są wyłączone i tylko protokołu TLS 1.2 są włączone na komputerze, na którym jest zainstalowany program Azure AD Connect.  Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu TLS 1.2, programu Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Rozwiązane problemy   

- Usunięto usterkę, w której usługa Azure AD Connect Uaktualnianie może zakończyć się niepowodzeniem jeśli SQL Always On było używane. 
- Usunięto usterkę można poprawnie przeanalizować nazwy jednostki Organizacyjnej, zawierające ukośnik. 
- Rozwiązano problem, w którym zostałoby wyłączone uwierzytelnianie przekazywane czystej instalacji w trybie przejściowym. 
- Usunięto usterkę uniemożliwiającą modułu programu PowerShell, należy załadować podczas uruchamiania narzędzia do rozwiązywania problemów 
- Naprawiono usterkę, które mogłyby spowodować zablokowanie klientów przy użyciu wartości liczbowe w pierwszym znakiem nazwy hosta. 
- Usunięto usterkę, w którym program Azure AD Connect pozwoliłoby nieprawidłowy partycji i wybór kontenera 
- Naprawiono "nieprawidłowe hasło" komunikat o błędzie podczas logowania jednokrotnego na komputerze jest włączona. 
- Różne poprawki dla zarządzania zaufania usług AD FS  
- Podczas konfigurowania zapisywania zwrotnego urządzeń — Naprawiono sprawdzanie schematu do wyszukiwania klasy obiektów msDs-DeviceContainer (zostanie wprowadzony w WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

9 7 2018: ogólnie dostępnych do pobrania, nie będą w wersji dla uaktualnienie automatyczne 

### <a name="fixed-issues"></a>Rozwiązane problemy  

Usługa Azure AD Connect uaktualnianie kończy się niepowodzeniem, jeśli zawsze na dostępności SQL jest skonfigurowana dla bazy danych ADSync. Ta poprawka rozwiązuje ten problem i umożliwia uaktualnienie zakończyło się sukcesem. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Stan zlecenia

8/21/2018: Zwolnione w celu pobrania i automatyczne uaktualnianie. 

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Integracja Federację Ping w programie Azure AD Connect jest teraz dostępne za usługi ogólnie dostępne. [Dowiedz się więcej o sposobie federacyjnych usługi Azure AD za pomocą polecenia Ping Federację](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Program Azure AD Connect utworzy teraz kopię zapasową zaufania usługi Azure AD w usługach AD FS, za każdym razem, gdy aktualizacja ma zostać i zapisuje go w osobny plik dla ułatwia przywracanie, jeśli jest to wymagane. [Dowiedz się więcej na temat nowych funkcji i usługi Azure AD zaufania zarządzania w programie Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Nowe narzędzia do rozwiązywania problemów ułatwia rozwiązywanie problemów z zmiany podstawowego adresu e-mail i ukrywanie Zmiana warstwy konta z globalnej listy adresowej
- Program Azure AD Connect została zaktualizowano o najnowsze programu SQL Server 2012 Native Client
- Po przełączeniu logowania użytkownika synchronizacja skrótów haseł lub uwierzytelniania przekazywanego w zadaniu "Zmiana logowanie użytkownika", bezproblemowe logowanie jednokrotne pole wyboru jest domyślnie włączona.
- Dodano obsługę systemu Windows Server Essentials 2019
- Agent usługi Azure AD Connect Health został zaktualizowany do najnowszej wersji 3.1.7.0
- Podczas uaktualniania Jeśli Instalator wykryje zmiany w domyślnych reguł synchronizacji administratora jest zostanie wyświetlony monit z ostrzeżeniem, zanim ją zastąpisz zmodyfikowane zasady. Umożliwi to użytkownikowi akcje naprawcze i wznowić później. Stare zachowanie: Jeśli było żadnych modyfikacji reguły out-of-box następnie ręczne uaktualnienie zostało zastąpienie tych reguł bez ostrzeżenia dla użytkownika i harmonogram synchronizacji zostało wyłączone bez informowania o tym użytkownika. Nowe zachowanie: Użytkownik jest monitowany o ostrzeżenie przed zastąpieniem reguły modyfikacji synchronizacji out-of-box. Użytkownik będzie miał możliwość Zatrzymaj proces uaktualniania i wznowić później, po wykonaniu działań naprawczych.
- Zapewnia lepszą obsługę problem ze zgodnością ze standardem FIPS, zapewniając komunikat o błędzie dla generowania wyznaczania wartości skrótu MD5 w środowiskach zgodnych z FIPS i link do dokumentacji, która umożliwia obejście tego problemu.
- Interfejs użytkownika aktualizacji usprawniających zadania federacji w kreatorze, które są teraz w grupie oddzielne sub dla Federacji. 
- Wszelkie dodatkowe zadania federacji są teraz pogrupowane w ramach pojedynczego menu podrzędne dla łatwość użycia.
- Nowy moduł programu Posh ADSyncConfig (AdSyncConfig.psm1) odnowionych z nowych funkcji uprawnień usługi AD, przeniesione z ADSyncPrep.psm1 stare, (które mogą wkrótce wycofane)

### <a name="fixed-issues"></a>Rozwiązane problemy 

- Usunięto usterkę, w którym serwer AAD Connect pokazywałaby wysokie użycie procesora CPU po uaktualnieniu do wersji .NET 4.7.2
- Naprawiono usterkę, która sporadycznie wywołałoby komunikat o błędzie dotyczącym problemu automatycznie rozwiązany zakleszczenia SQL
- Rozwiązano kilka problemów ułatwień dostępu dla Edytor reguł synchronizacji i synchronizacji Service Manager  
- Usunięto usterkę, w którym program Azure AD Connect nie można uzyskać informacje o ustawieniach rejestru
- Naprawiono usterkę, która utworzone problemy, gdy użytkownik przechodzi do przodu i Wstecz w Kreatorze
- Naprawiono usterkę, aby zapobiec błędem ze względu na nieprawidłowe wątek wielu pierwszemu w Kreatorze
- Gdy strona Filtrowanie synchronizacji grupy napotka błąd LDAP, podczas rozpoznawania grup zabezpieczeń, program Azure AD Connect teraz zwraca wyjątek z pełnej rozdzielczości.  Przyczyny, dla wyjątku odwołania jest nadal nieznane i zostanie rozwiązany przez różnych błędów.
-  Usunięto usterkę, w której uprawnienia do kluczy STK i NGC (ms-DS-KeyCredentialLink atrybut obiektów użytkownika/urządzenie dla funkcji WHfB) nie zostały prawidłowo ustawione.     
- Usunięto usterkę, w którym "Set-ADSyncRestrictedPermissions" nie została wywołana poprawnie
-  Dodanie obsługi, które udzielają na zapisywanie zwrotne grup w Kreatorze instalacji AADConnect firmy uprawnień
- Po zmianie logowania w metodzie z synchronizacją skrótów haseł z usługami AD FS, synchronizacji skrótów haseł nie zostało wyłączone.
- Dodano weryfikacji dla adresów IPv6 w konfiguracji usług AD FS
- Zaktualizowano komunikat powiadomienia, aby poinformować, że istnieje istniejącej konfiguracji.
- Zapisywanie zwrotne urządzeń nie powiodło się wykrywanie kontenera w niezaufanym lesie. To została zaktualizowana w celu zapewnienia udoskonalony komunikat o błędzie oraz łącze do odpowiedniej dokumentacji
- Usunięcie zaznaczenia tej opcji, jednostki Organizacyjnej, a następnie synchronizacji/zapisywanie zwrotne odpowiadający, że jednostki Organizacyjnej daje błąd rodzajowy synchronizacji. To został zmieniony na tworzenie bardziej zrozumiały komunikat o błędzie.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Stan zlecenia

5/14/2018: Zwolnione w celu automatycznego uaktualniania i pobierania.

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

Nowe funkcje i ulepszenia

- Ta wersja obejmuje publiczną wersję zapoznawczą integracji serwera PingFederate w programie Azure AD Connect. W tej wersji klienci mogą łatwo i niezawodnie skonfigurować swoich środowiskach usługi Azure Active Directory, aby korzystać z serwera PingFederate jako ich dostawcy federacyjnego. Aby dowiedzieć się więcej na temat korzystania z nowej funkcji, odwiedź nasz [dokumentacji online](plan-connect-user-signin.md#federation-with-pingfederate). 
- Zaktualizowano usługi Azure AD Connect kreatora Rozwiązywanie problemów z narzędziu gdzie teraz analizuje więcej scenariuszu błąd, takie jak połączone skrzynek pocztowych i grupy dynamiczne usługi AD. Przeczytaj więcej na temat rozwiązywania problemów narzędzie [tutaj](tshoot-connect-objectsync.md).
- Konfiguracja zapisywania zwrotnego urządzeń jest teraz zarządzana wyłącznie w ramach usługi Azure AD Kreatora programu Connect.
- Nowy moduł programu PowerShell o nazwie ADSyncTools.psm1 zostanie dodany używanym rozwiązywać problemy z połączeniem SQL i różnych innych narzędzi rozwiązywania problemów. Dowiedz się więcej o ADSyncTools module [tutaj](tshoot-connect-tshoot-sql-connectivity.md). 
- Dodano dodatkowe nowe zadanie "Konfiguruj opcje urządzenia". Zadania umożliwiają skonfigurowanie następujące dwie operacje: 
  - **Dołączenie do hybrydowej usługi Azure AD**: Jeśli środowisko ma lokalną infrastrukturę usługi AD i chcesz również wykorzystać możliwości zapewniane przez usługę Azure Active Directory, możesz wdrożyć urządzenia dołączone hybrydowo do usługi Azure AD. Są to urządzenia dołączone do lokalnej usługi Active Directory oraz usługi Azure Active Directory.
  - **Zapisywanie zwrotne urządzeń**: Zapisywanie zwrotne urządzeń służy do włączania dostępu warunkowego opartego na urządzeniach z usługami AD FS (2012 R2 lub nowszy) chronione urządzeń

    >[!NOTE] 
    > - Opcja włączania zapisywania zwrotnego urządzeń z Dostosowywanie opcji synchronizacji będą wyszarzone. 
    > -  Moduł programu PowerShell dla programu ADPrep jest przestarzała w tej wersji.



### <a name="fixed-issues"></a>Rozwiązane problemy 

- W tej wersji aktualizacji instalacji programu SQL Server Express do programu SQL Server 2012 z dodatkiem SP4, oferujący, między innymi poprawki dla kilku luk w zabezpieczeniach.  Zobacz [tutaj](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) Aby uzyskać więcej informacji na temat programu SQL Server 2012 z dodatkiem SP4.
- Przetwarzanie reguł synchronizacji: reguły synchronizacji ruchu wychodzącego sprzężenia z żadnego warunku Join powinna być cofnąć stosowane, jeśli reguła synchronizacji nadrzędna
- Kilka poprawki dotyczące ułatwień dostępu zostały zastosowane do interfejsu użytkownika Synchronization Service Manager i edytorem reguły synchronizacji
- Kreator programu Azure AD Connect: Wystąpił błąd podczas tworzenia konta łącznika AD, gdy program Azure AD Connect jest w grupie roboczej
- Kreator programu Azure AD Connect: Na stronie logowania usługi AD Azure wyświetla pola wyboru weryfikacji, gdy brak zgodności którychkolwiek AD domen i domen usługi Azure AD Verified
- Poprawka programu PowerShell automatyczną aktualizację poprawne ustawienie automatyczne uaktualnienia stanu w niektórych przypadkach po uaktualnieniu automatycznie podjęto próbę.
- Kreator programu Azure AD Connect: Zaktualizowane dane telemetryczne do przechwytywania wcześniej brakujące informacje
- Kreator programu Azure AD Connect: Wprowadzono następujące zmiany, gdy używasz **zmiana użytkownika logowania** zadań, aby przełączyć się z usług AD FS do uwierzytelniania przekazywanego:
    - Agent uwierzytelniania przekazywanego jest zainstalowany na serwerze usługi Azure AD Connect i włączeniu funkcji uwierzytelniania przekazywanego, zanim możemy przekonwertować domeny z federacyjnego zarządzane.
    - Użytkownicy są już przekonwertowana z federacyjnego na zarządzanego. Tylko domeny są konwertowane.
- Kreator programu Azure AD Connect: Usługi AD FS wielu domen w wyrażeń regularnych jest nieprawidłowy, gdy ma nazwę UPN użytkownika "aktualizacji wyrażenia regularnego znak specjalny do obsługi znaków specjalnych
- Kreator programu Azure AD Connect: Usuń fałszywe wiadomości "Konfiguruj atrybut zakotwiczenia źródła", gdy brak zmian 
- Kreator programu Azure AD Connect: Obsługa scenariusz podwójną federacyjnej usługi AD FS
- Kreator programu Azure AD Connect: Usługi AD FS oświadczenia nie zostaną zaktualizowane dla domeny dodanych podczas konwertowania domeny zarządzanej federacyjnego
- Kreator programu Azure AD Connect: Podczas wykrywania zainstalowane pakiety, możemy znaleźć starych Dirsync/Azure AD Sync/usługi Azure AD Connect pokrewnych produktów. Firma Microsoft podejmie teraz próbę odinstalowania starych produktów.
- Kreator programu Azure AD Connect: Popraw mapowania komunikat o błędzie podczas instalacji agenta uwierzytelniania programu Przekazywanie zakończy się niepowodzeniem.
- Kreator programu Azure AD Connect: Usunięto kontener "Konfiguracja" ze strony filtrowania jednostek organizacyjnych domeny
- Instalowanie aparatu synchronizacji: Usuń niepotrzebne starszych logikę, która czasami nie powiodło się z aparatem synchronizacji instalacji msi
- Kreator programu Azure AD Connect: Usuń tekst pomocy okna podręcznego na stronie funkcje opcjonalne dla synchronizacji skrótów haseł
- Środowisko uruchomieniowe aparat synchronizacji: Napraw scenariusz, w którym obiekt CS został zaimportowany delete i spróbować ponownie zainicjować obsługę administracyjną obiektu reguły synchronizacji.
- Środowisko uruchomieniowe aparat synchronizacji: Dodaj łącze Pomoc Online łączności dotyczące rozwiązywania problemów w przewodniku w dzienniku zdarzeń Błąd importowania
- Środowisko uruchomieniowe aparat synchronizacji: Ograniczenia użycia pamięci programu Sync Scheduler podczas wyliczania łączników
- Kreator programu Azure AD Connect: Rozwiązać problem, rozpoznawanie niestandardowe konto usługi synchronizacji, które ma uprawnienia odczytu AD, nie
- Kreator programu Azure AD Connect: Poprawa rejestrowanie domenę i jednostkę Organizacyjną, opcje filtrowania
- Kreator programu Azure AD Connect: Usługi AD FS Dodaj domyślny oświadczeń relację zaufania federacji, utworzone w scenariuszu dla usługi MFA
- Kreator programu Azure AD Connect: AD FS Deploy WAP: Dodawanie serwera nie powiodło się do użycia nowego certyfikatu
- Kreator programu Azure AD Connect: Wyjątek DSSO podczas onPremCredentials nie są inicjowane dla domeny 
- Preferencyjne przepływu atrybut distinguishedName AD z obiektu aktywnego użytkownika.
- Naprawiono usterkę kosmetycznych zostały pierwszeństwo pierwszą regułę synchronizacji OOB została ustawiona na 99 zamiast 100



## <a name="117510"></a>1.1.751.0
Stan 4/12/2018 r.: Zwolnione w celu pobrania tylko

>[!NOTE]
>Ta wersja nie jest poprawki programu Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Synchronizacja programu Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
Rozwiązany problem zostały odnajdywanie automatyczne wystąpienia platformy Azure dla dzierżaw (Chiny) od czasu do czasu został kończy się niepowodzeniem.  

### <a name="ad-fs-management"></a>AD FS Management
#### <a name="fixed-issues"></a>Rozwiązane problemy

Wystąpił problem w konfiguracji Logika ponawiania, które mogłyby spowodować ArgumentException z informacją "element o takim samym kluczem już został dodany."  To spowoduje, że wszystkie operacje ponownych prób nie powiedzie się.

## <a name="117500"></a>1.1.750.0
Stan 22/3/2018: Zwolnione w celu automatycznego uaktualniania i pobierania.
>[!NOTE]
>Po zakończeniu uaktualniania do tej nowej wersji automatycznie spowoduje wyzwolenie pełnej synchronizacji i pełnego importowania dla łącznika usługi Azure AD i pełnej synchronizacji dla łącznika usługi AD. Ponieważ może to potrwać pewien czas, w zależności od wielkości środowiska Azure AD Connect, upewnij się, czy wykonano czynności niezbędne do obsługi to lub Wstrzymaj się na uaktualnienie, aby znaleźć dogodnym momencie, aby to zrobić.

>[!NOTE]
>"Funkcja AutoUpgrade niepoprawnie został wyłączony dla niektórych dzierżawców, którzy później niż w 1.1.524.0 kompilacji. Aby upewnić się, że wystąpienie usługi Azure AD Connect jest nadal uprawnieni do skorzystania AutoUpgrade, uruchom następujące polecenie cmdlet programu PowerShell: "Set ADSyncAutoUpgrade - AutoupGradeState włączone"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy

* Polecenia cmdlet Set-ADSyncAutoUpgrade wcześniej zablokowanie Autoupgrade, jeśli ustawiono automatyczne uaktualnianie stan zawieszone. Ta funkcja zostanie zmieniona, więc nie blokuje AutoUpgrade kompilacji w przyszłości.
* Zmienione **logowania użytkownika** stronie opcji "Synchronizacji haseł" na "Synchronizację skrótów haseł".  Azure AD Connect synchronizuje skrótów haseł, a nie haseł, dzięki czemu jest to zgodne z co faktycznie się dzieje.  Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Stan: Ogólnie dla wybranych klientów

>[!NOTE]
>Po zakończeniu uaktualniania do tej nowej wersji automatycznie spowoduje wyzwolenie pełnej synchronizacji i pełnego importowania dla łącznika usługi Azure AD i pełnej synchronizacji dla łącznika usługi AD. Ponieważ może to potrwać pewien czas, w zależności od wielkości środowiska Azure AD Connect, upewnij się, czy wykonano czynności niezbędne do obsługi to lub Wstrzymaj się na uaktualnienie, aby znaleźć dogodnym momencie, aby to zrobić.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Usuń okna czasowego na zadania w tle dla strony filtrowanie partycji, podczas przełączania do następnej strony.

* Naprawiono usterkę, który spowodował naruszenie zasad dostępu podczas ConfigDB akcji niestandardowej.

* Usunięto usterkę do odzyskania z limit czasu połączenia SQL.

* Usunięto usterkę, w którym certyfikatów z symbolami wieloznacznymi sieci SAN nie powiodło się sprawdzanie wymagań wstępnych.

* Naprawiono usterkę, co powoduje, że miiserver.exe ulega awarii podczas eksportowania do łącznika usługi Azure AD.

* Naprawiono usterkę, która próba nieprawidłowego hasła rejestrowane na kontrolerze domeny, podczas uruchamiania kreatora Azure AD Connect, aby zmienić konfigurację.


#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Dodawanie ustawień prywatności ogólne rozporządzenie o ochronie danych (RODO).  Aby uzyskać więcej informacji, zobacz artykuł [tutaj](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetria usługi Application - admin można przełączać data włączenia/wyłączenia tej klasy w momencie

* Danych kondycji usługi AD platformy Azure — administratora, należy odwiedzić portal kondycji do kontrolowania ustawień ich kondycji.
   Po zmianie zasad usługi agentami odczytu, a jej wymusić.

* Dodane urządzenie zapisu czynności konfiguracyjnych i pasek postępu inicjowania strony

* Ulepszona diagnostyka ogólnego z raportu w formacie HTML i zbierania pełnych danych w pliku ZIP tekstu / raport HTML

* Poprawiono niezawodność automatyczne uaktualnianie i dodano dodatkową telemetrię, aby upewnić się, że można ustalić kondycję serwera

* Ogranicz uprawnienia dostępne do kont uprzywilejowanych dla konta łącznika AD

  * W przypadku nowej instalacji Kreator ograniczyć uprawnienia, które konta uprzywilejowane ma konta MSOL po utworzeniu konta MSOL.

Zmiany zajmie się następujące czynności:
1. Instalacji ekspresowej
2. Instalacje niestandardowe przy użyciu automatycznego tworzenia konta
3. Zmiany Instalatora, dlatego nie wymaga uprawnień administratora systemu w czystej instalacji programu Azure AD Connect

* Dodano nowe narzędzie do rozwiązywania problemów z synchronizacją dla konkretnego obiektu. Jest on dostępny w obszarze "Rozwiązywanie problemów z synchronizacją obiektu" możliwości usługi Azure AD Connect kreatora Rozwiązywanie problemów z dodatkowego zadania. Obecnie narzędzie sprawdza, czy następujące czynności:

  * UserPrincipalName niezgodność między obiektów synchronizowanych użytkowników i konta użytkownika w dzierżawy usługi Azure AD.
  * Jeśli obiekt został odfiltrowany z synchronizacji z powodu filtrowanie domeny
  * Jeśli obiekt został odfiltrowany z synchronizacji z powodu jednostki organizacyjnej (OU), filtrowanie

* Dodano nowe narzędzie do synchronizacji bieżąca wartość skrótu hasła przechowywane w lokalnej usłudze Active Directory dla określonego konta użytkownika.

Narzędzie nie wymaga zmiany hasła. Jest on dostępny w obszarze "Rozwiązywanie problemów z synchronizacji skrótów haseł w" możliwości usługi Azure AD Connect kreatora Rozwiązywanie problemów z dodatkowego zadania.






## <a name="116540"></a>1.1.654.0
Stan: 12 grudnia 2017 r.

>[!NOTE]
>Ta wersja nie jest zabezpieczenie powiązanej poprawki programu Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Ulepszenia został dodany do usługi Azure AD Connect w wersji 1.1.654.0 (i po) do upewnij się, że zalecanych uprawnień zmian opisanych w sekcji [blokowanie dostępu do konta usługi AD DS](#lock) są stosowane automatycznie po usługi Azure AD Połącz tworzy konto usług AD DS. 

- Podczas konfigurowania usługi Azure AD Connect, instalowanie administratora można podać istniejące konto usług AD DS, lub pozwól automatycznie utworzyć konto usługi Azure AD Connect. Zmiany uprawnień są automatycznie stosowane do konta usługi AD DS, który jest tworzony przy użyciu usługi Azure AD Connect, podczas instalacji. Nie są stosowane do istniejącego konta usług AD DS, podane przez administratora w instalacji.
- Dla klientów, którzy zrezygnowali ze starszej wersji programu Azure AD Connect do 1.1.654.0 (lub po) uprawnienie zmiany zostaną nie wstecznie zastosowane do istniejących kont usług AD DS, utworzonych przed uaktualnieniem. Są one stosowane tylko do nowych kont usług AD DS, utworzonych po uaktualnieniu. Dzieje się tak, podczas dodawania nowych lasów usługi AD, które mają być synchronizowane z usługą Azure AD.

>[!NOTE]
>W tej wersji powoduje usunięcie tylko luk w zabezpieczeniach dla nowych instalacji programu Azure AD Connect, gdzie konto usługi jest tworzony przez proces instalacji. W przypadku istniejących instalacji lub w przypadkach, w którym podać konto użytkownika należy upewnić się, że tę lukę w zabezpieczeniach nie istnieje.

#### <a name="lock"></a> Zablokowanie dostępu do konta usługi AD DS
Blokowanie dostępu do konta usługi AD DS, implementując następujące zmiany uprawnień w lokalnej usługi AD:  

*   Wyłącz dziedziczenie dla określonego obiektu
*   Usuń wszystkie wpisy kontroli dostępu dla określonego obiektu, z wyjątkiem ACE specyficzne dla siebie. Chcemy zachować uprawnienia domyślne, jeśli chodzi o SAMODZIELNIE.
*   Przypisz te określone uprawnienia:

Typ     | Name (Nazwa)                          | Access               | Dotyczy
---------|-------------------------------|----------------------|--------------|
Zezwalaj    | SYSTEM                        | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Enterprise Admins             | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy domeny                 | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy                | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Enterprise Domain Controllers | Wyświetl zawartość        | Ten obiekt  |
Zezwalaj    | Enterprise Domain Controllers | Odczyt wszystkich właściwości  | Ten obiekt  |
Zezwalaj    | Enterprise Domain Controllers | Uprawnienia do odczytu     | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Wyświetl zawartość        | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Odczyt wszystkich właściwości  | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Uprawnienia do odczytu     | Ten obiekt  |

Aby zwiększyć ustawień dla konta usług AD DS, należy uruchomić [ten skrypt programu PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). Skrypt programu PowerShell spowoduje przypisanie uprawnień wymienionych powyżej konto usług AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Skrypt programu PowerShell, aby zwiększyć istniejące konto usługi

Aby użyć skryptu programu PowerShell, aby zastosować te ustawienia do istniejącego konta usługi AD DS, (ether udostępnioną przez Twoją organizację lub utworzony w poprzedniej instalacji programu Azure AD Connect, Pobierz skrypt z podany link powyżej.

##### <a name="usage"></a>Użycie:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Lokalizacja 

**$ObjectDN** = konto usługi Active Directory, w której uprawnienia muszą być ściągane.

**$Credential** = poświadczenia administratora, który ma niezbędne uprawnienia, aby ograniczyć uprawnienia na koncie $ObjectDN. Te uprawnienia są zazwyczaj przechowywane przez administratora przedsiębiorstwa lub domeny. Użyj w pełni kwalifikowana nazwa domeny konta administratora, aby uniknąć niepowodzenia wyszukiwania kont. Przykład: contoso.com\admin.

>[!NOTE] 
>$credential. Nazwa użytkownika powinna mieć format FQDN\username. Przykład: contoso.com\admin 

##### <a name="example"></a>Przykład:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Tę lukę w zabezpieczeniach użytego do uzyskania nieautoryzowanego dostępu?

Aby zobaczyć, jeśli tę lukę w zabezpieczeniach zostało użyte do naruszenia bezpieczeństwa usługi Azure AD Connect konfiguracji należy sprawdzić, ostatnie hasło zresetować daty konta usługi.  Jeśli sygnatura czasowa w nieoczekiwany, dalszych badań, przy użyciu dziennika zdarzeń dla zdarzenia resetowania tego hasła, należy podjąć.

Aby uzyskać więcej informacji, zobacz [4056318 poradnik zabezpieczeń firmy Microsoft](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Stan: Październik 2017 27

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect automatyczne uaktualnianie.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Rozwiązano problem
* Rozwiązano problem ze zgodnością w wersji między Azure AD Connect i agenta Azure AD Connect Health (do celów synchronizacji). Ten problem ma wpływ na klientów, którzy wykonują program Azure AD Connect miejscowe uaktualnienie do wersji 1.1.647.0, ale obecnie ma agenta kondycji wersji 3.0.127.0. Po uaktualnieniu agenta programu Health nie może wysyłać dane o usługi Azure AD Connect synchronizacji kondycji do usługi Azure AD Health. Dzięki tej poprawce Agent kondycji wersji 3.0.129.0 jest instalowany podczas uaktualnienia w miejscu program Azure AD Connect. Agent kondycji wersji 3.0.129.0 nie ma problem ze zgodnością za pomocą usługi Azure AD Connect w wersji 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Stan: Październik 2017 19

> [!IMPORTANT]
> Istnieje znany problem ze zgodnością między program Azure AD Connect w wersji 1.1.647.0 i wersji agenta Azure AD Connect Health (do celów synchronizacji) 3.0.127.0. Ten problem uniemożliwia wysyłanie danych kondycji dotyczące usługi Azure AD Connect synchronizacji (w tym błędów synchronizacji obiektów i danych historii uruchamiania) do usługi Azure AD kondycji agenta programu Health. Przed rozpoczęciem uaktualniania ręcznego wdrożenia usługi Azure AD Connect do wersji 1.1.647.0 Sprawdź, czy bieżąca wersja Agent programu Azure AD Connect Health zainstalowane na serwerze usługi Azure AD Connect. Możesz to zrobić, przechodząc do *Panelu sterowania → Dodaj/Usuń programy* i Wyszukaj aplikację *agenta Microsoft Azure AD Connect Health do celów synchronizacji*. Jeśli jego wersja jest 3.0.127.0, zalecane jest, poczekaj następnej wersji Azure AD Connect była dostępna przed uaktualnieniem. Jeśli wersja agenta programu Health nie 3.0.127.0, jest w dobrym stanie kontynuować uaktualnianie ręczne, w miejscu. Należy pamiętać, że ten problem nie wpływa na uaktualnienia przez przesunięcia lub klienci, którzy wykonują nowej instalacji programu Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem z *zmiana użytkownika logowania* zadań w kreatorze program Azure AD Connect:

  * Ten problem występuje, jeśli masz istniejące wdrożenie usługi Azure AD Connect z synchronizacją haseł **włączone**, i chcesz ustawić metodę logowania użytkownika jako *uwierzytelniania przekazywanego*. Zanim zmiana zostanie zastosowana, Kreator niepoprawnie pokazuje "*wyłączyć synchronizację haseł*" wiersza. Jednak synchronizacja haseł pozostaje włączony nawet po zastosowaniu zmiany. Dzięki tej poprawce kreatora nie jest już wyświetlany monit.

  * Zgodnie z projektem, Kreator nie wyłączać synchronizację haseł po zaktualizowaniu metodę logowania użytkownika za pomocą *zmiana użytkownika logowania* zadania. Pozwoli to uniknąć zakłócenia dla klientów, którzy chcesz zachować synchronizację haseł, nawet jeśli są one Włączanie uwierzytelniania przekazywanego i Federacji jako metody logowania użytkownika podstawowego.
  
  * Jeśli chcesz wyłączyć synchronizację haseł po zaktualizowaniu metody logowania użytkownika, należy wykonać *dostosować konfigurację synchronizacji* zadań w kreatorze. Po przejściu do *funkcje opcjonalne* strony, usuń zaznaczenie pola wyboru *synchronizacji haseł* opcji.
  
  * Należy pamiętać, że ten sam problem również występuje, jeśli zostanie podjęta próba Włączanie/wyłączanie bezproblemowego logowania jednokrotnego. W szczególności masz istniejące wdrożenie usługi Azure AD Connect z włączoną synchronizacją haseł i metody logowania użytkownika jest już skonfigurowany jako *uwierzytelniania przekazywanego*. Za pomocą *zmiana użytkownika logowania* zadania próby Zaznacz/Usuń zaznaczenie *Włącz bezproblemowe logowanie jednokrotne* opcji, gdy metody logowania użytkownika jest skonfigurowany jako "Uwierzytelnianie przekazujących". Zanim zmiana zostanie zastosowana, Kreator niepoprawnie pokazuje "*wyłączyć synchronizację haseł*" wiersza. Jednak synchronizacja haseł pozostaje włączony nawet po zastosowaniu zmiany. Dzięki tej poprawce kreatora nie jest już wyświetlany monit.

* Rozwiązano problem z *zmiana użytkownika logowania* zadań w kreatorze program Azure AD Connect:

  * Ten problem występuje, jeśli masz istniejące wdrożenie usługi Azure AD Connect z synchronizacją haseł **wyłączone**, i chcesz ustawić metodę logowania użytkownika jako *uwierzytelniania przekazywanego*. Po zastosowaniu zmiany Kreator włącza synchronizację haseł i uwierzytelniania przekazywanego. Dzięki tej poprawce nie jest już włączone w Kreatorze synchronizacji haseł.

  * Wcześniej można było to wymaganie wstępne dotyczące włączania uwierzytelniania przekazywanego synchronizacji haseł. Gdy ustawisz metodę logowania użytkownika jako *uwierzytelniania przekazywanego*, Kreator będzie Włączanie synchronizacji haseł i uwierzytelniania przekazywanego. Niedawno synchronizacja haseł został usunięty jako informacje jako warunek wstępny. W ramach usługi Azure AD Connect w wersji 1.1.557.0, wprowadzono zmian do programu Azure AD Connect nie Włączanie synchronizacji haseł, gdy ustawisz metodę logowania użytkownika jako *uwierzytelniania przekazywanego*. Jednak zmiana została zastosowana tylko do instalacji programu Azure AD Connect. Dzięki tej poprawce tę samą zmianę również stosowane do *zmiana użytkownika logowania* zadania.
  
  * Należy pamiętać, że ten sam problem również występuje, jeśli zostanie podjęta próba Włączanie/wyłączanie bezproblemowego logowania jednokrotnego. W szczególności masz istniejące wdrożenie usługi Azure AD Connect z synchronizacji haseł wyłączone i metody logowania użytkownika jest już skonfigurowany jako *uwierzytelniania przekazywanego*. Za pomocą *zmiana użytkownika logowania* zadania próby Zaznacz/Usuń zaznaczenie *Włącz bezproblemowe logowanie jednokrotne* opcji, gdy metody logowania użytkownika jest skonfigurowany jako "Uwierzytelnianie przekazujących". Zmiana zostanie zastosowana, włączone w Kreatorze synchronizacji haseł. Dzięki tej poprawce nie jest już włączone w Kreatorze synchronizacji haseł. 

* Rozwiązano problem powodujący program Azure AD Connect uaktualnienie kończy się niepowodzeniem z powodu błędu "*nie można uaktualnić usługę synchronizacji*". Ponadto usługa synchronizacji nie można uruchomić z powodu błędu zdarzenia "*usługa nie może uruchomić, ponieważ wersja bazy danych jest nowsza niż wersja plików binarnych, zainstalowane*". Ten problem występuje, gdy administrator przeprowadzania uaktualnienia nie ma uprawnień administratora systemu do programu SQL server, który jest używany przez program Azure AD Connect. Dzięki tej poprawce Azure AD Connect wymaga tylko administratorowi uprawnień db_owner do bazy danych ADSync podczas uaktualniania.

* Rozwiązano problem usługi Azure AD Connect uaktualnianie, która dotyczy klientów, którzy włączyli [bezproblemowego logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Po uaktualnieniu program Azure AD Connect bezproblemowego logowania jednokrotnego niepoprawnie pojawia się jako wyłączone w kreatorze program Azure AD Connect, nawet jeśli ta funkcja pozostanie włączony i w pełni funkcjonalne. Dzięki tej poprawce funkcji zostanie teraz wyświetlona poprawnie jako włączone w kreatorze.

* Rozwiązano problem powodujący Kreator Azure AD Connect, aby zawsze show "*Konfiguruj zakotwiczenie źródła*" Monituj o *gotowe do konfiguracji* strony, nawet wtedy, gdy nie wprowadzono żadnych zmian, które dotyczą zakotwiczenie źródła.

* Podczas przeprowadzania ręcznego uaktualnienia w miejscu programu Azure AD Connect, klienta jest wymagane podanie poświadczeń administratora globalnego odpowiedniego dzierżawy usługi Azure AD. Wcześniej, można kontynuować uaktualniania, nawet jeśli poświadczenia administratora globalnego należały do innej usługi Azure AD dzierżawy. Podczas uaktualniania pojawi się pomyślnie, niektóre konfiguracje nie są poprawnie zachowywane po uaktualnieniu. Dzięki tej zmianie kreatora uniemożliwia uaktualnienie kontynuowanie, jeśli podane poświadczenia nie są zgodne z dzierżawą usługi Azure AD.

* Usunąć nadmiarowe logikę, która niepotrzebnie ponownie uruchomić usługę Azure AD Connect Health na początku ręcznego uaktualnienia.


#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano logikę w celu uproszczenia kroki wymagane do skonfigurowania usługi Azure AD Connect przy użyciu Microsoft Cloud (Niemcy). Wcześniej wymagane jest aby zaktualizować określone klucze rejestru na serwerze program Azure AD Connect, aby mogła działać poprawnie z Microsoft Cloud (Niemcy), zgodnie z opisem w tym artykule. Teraz program Azure AD Connect może automatycznie wykrywać, gdy dzierżawy w Microsoft Cloud (Niemcy) opiera się na poświadczenia administratora globalnego, podane podczas instalacji.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Uwaga: Usługa synchronizacji ma interfejs usługi WMI, który pozwala tworzyć własny niestandardowy harmonogram. Ten interfejs jest już przestarzały i zostanie usunięte z przyszłych wersji programu Azure AD Connect zostały wysłane po 30 czerwca 2018 r. Klienci, którzy chcą, aby dostosować harmonogram synchronizacji, należy użyć [wbudowanych harmonogramu (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Rozwiązane problemy
* Gdy Kreator Azure AD Connect tworzy konta łącznika AD, wymagane, aby zsynchronizować zmiany z usługi Active Directory w środowisku lokalnym, go nie ma poprawnie przypisanego konta uprawnień wymaganych do odczytania PublicFolder obiektów. Ten problem dotyczy zarówno instalacja ekspresowa i instalacji niestandardowej. Ta zmiana rozwiązuje ten problem.

* Rozwiązano problem powodujący kreatora Azure AD Connect rozwiązywania problemów nie renderowanej strony prawidłowo dla Administratorzy korzystający z systemu Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Podczas rozwiązywania problemów z synchronizacją haseł za pomocą Kreatora Azure AD Connect, strona rozwiązywania problemów, stronę rozwiązywania problemów teraz zwraca stan specyficznego dla domeny.

* Wcześniej, jeśli próbowano włączyć synchronizację skrótów haseł usługi Azure AD Connect nie sprawdza, czy konta łącznika AD ma uprawnienia wymagane do synchronizacji skrótów haseł z lokalnej usługi AD. Teraz Kreator Azure AD Connect Sprawdź i ostrzega o tym, jeśli konta łącznika AD nie ma wystarczających uprawnień.

### <a name="ad-fs-management"></a>AD FS Management
#### <a name="fixed-issue"></a>Rozwiązano problem
* Rozwiązano problem związany z zastosowaniem [ms-DS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funkcji. Ten problem dotyczy klientów, którzy skonfigurowali *Federacja z usługami AD FS* jako metodę logowania użytkownika. Podczas wykonywania *Konfiguruj zakotwiczenie źródła* zadań w kreatorze, Azure AD Connect zmienia się na użyciu * ms-DS-ConsistencyGuid jako atrybut źródłowy dla immutableId. W ramach tej zmiany program Azure AD Connect próbuje zaktualizować reguł oświadczeń dla ImmutableId w usługach AD FS. Jednak w tym kroku nie powiodło się, ponieważ program Azure AD Connect nie ma poświadczeń administratora wymagane do skonfigurowania usług AD FS. Dzięki tej poprawce program Azure AD Connect teraz wyświetli monit o wprowadzenie poświadczeń administratora dla usług AD FS podczas wykonywania *Konfiguruj zakotwiczenie źródła* zadania.



## <a name="116140"></a>1.1.614.0
Stan: We wrześniu 2017 r. 05

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Znane problemy
* Jest to znany problem, który powoduje uaktualnienie usługi Azure AD Connect kończy się niepowodzeniem z powodu błędu "*nie można uaktualnić usługę synchronizacji*". Ponadto usługa synchronizacji nie można uruchomić z powodu błędu zdarzenia "*usługa nie może uruchomić, ponieważ wersja bazy danych jest nowsza niż wersja plików binarnych, zainstalowane*". Ten problem występuje, gdy administrator przeprowadzania uaktualnienia nie ma uprawnień administratora systemu do programu SQL server, który jest używany przez program Azure AD Connect. Uprawnienia dbo nie są wystarczające.

* Jest to znany problem za pomocą usługi Azure AD Connect uaktualnianie, ma wpływ na klientów, którzy włączyli [bezproblemowego logowania jednokrotnego](how-to-connect-sso.md). Po uaktualnieniu program Azure AD Connect funkcji pojawia się jako wyłączone w kreatorze, mimo że ta funkcja pozostanie włączony. Poprawka dla tego problemu będzie świadczona w przyszłych wersji. Klienci, którzy są zainteresowani, o tym problemie wyświetlania ręcznie rozwiązać go przez włączenie bezproblemowego logowania jednokrotnego w kreatorze.

#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem, który uniemożliwił aktualizowanie reguł oświadczeń w lokalnym programie Azure AD Connect usług AD FS podczas włączania [ms-DS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funkcji. Ten problem występuje, jeśli zostanie podjęta próba włączenia tej funkcji do istniejącego wdrożenia usługi Azure AD Connect, zawierającej usług AD FS, skonfigurowany jako metodę logowania. Ten problem występuje, ponieważ kreatora nie jest wyświetlany monit o poświadczenia usług AD FS przed podjęciem próby można zaktualizować reguł oświadczeń w usługach AD FS.
* Rozwiązano problem powodujący usługi Azure AD Connect, aby instalacja się niepowodzeniem, jeśli w środowisku lokalnym lasem usługi AD jest wyłączone uwierzytelnianie NTLM. Problem jest następstwem Kreator Azure AD Connect nie dostarcza poświadczenia w pełni kwalifikowaną, tworząc konteksty zabezpieczeń wymaganych do uwierzytelniania Kerberos. Powoduje to niepowodzenie uwierzytelnienia Kerberos i Kreator Azure AD Connect, aby wrócić korzystania z uwierzytelniania NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem, w których nie można utworzyć nową regułę synchronizacji Jeśli atrybut Tag nie jest wypełnione.
* Rozwiązano problem powodujący usługi Azure AD Connect, aby nawiązać połączenie z lokalnej usługi AD do synchronizacji haseł przy użyciu metod NTLM, nawet jeśli protokół Kerberos jest dostępna. Ten problem występuje, gdy lokalne topologii AD ma jeden lub więcej kontrolerów domeny, które zostały przywrócone z kopii zapasowej.
* Rozwiązano problem powodujący instrukcje pełną synchronizację niepotrzebnie wystąpić po uaktualnieniu. Ogólnie rzecz biorąc uruchomienie pełnej synchronizacji czynności jest wymagane po uaktualnieniu w przypadku zmiany reguł synchronizacji out-of-box. Ten problem był z powodu błędu w Zmień logikę wykrywania, który niepoprawnie wykryty zmiany w przypadku napotkania wyrażenie reguły synchronizacji ze znakami nowego wiersza. Znaki nowego wiersza są wstawiane do wyrażenie reguły synchronizacji w celu poprawienia czytelności.
* Rozwiązano problem, który może spowodować, że serwer Azure AD Connect może nie działać poprawnie po automatycznego uaktualniania. Ten problem dotyczy serwerów Azure AD Connect w wersji 1.1.443.0 (lub starszy). Aby uzyskać szczegółowe informacje o problemie, można znaleźć w artykule [program Azure AD Connect nie działa prawidłowo po automatyczną aktualizację](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Rozwiązano problem, który może powodować automatyczne uaktualnienie do ponawiana co 5 minut, gdy zostaną napotkane błędy. Za pomocą poprawki automatycznego uaktualniania ponownych prób, przy użyciu wykładniczego wycofywania, gdy zostaną napotkane błędy.
* Rozwiązano problem, w którym zdarzenie synchronizacji haseł 611 jest nieprawidłowo wyświetlany w dziennikach zdarzeń aplikacji Windows jako **informacyjny** zamiast **błąd**. Zdarzenie 611 jest generowane, gdy synchronizacja haseł napotka jakiś problem. 
* Rozwiązano problem z Kreatora Azure AD Connect, która umożliwia funkcji zapisywania zwrotnego grup można włączyć bez zaznaczania jednostki Organizacyjnej wymagane dla zapisu zwrotnego grup.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodane zadanie rozwiązywania problemów do kreatora Azure AD Connect w obszarze zadania dodatkowe. Klienci mogą wykorzystać do rozwiązywania problemów związanych z synchronizacją haseł i zbieranie danych diagnostycznych ogólne tego zadania. W przyszłości zadanie rozwiązywanie zostanie rozszerzone, aby inne problemy dotyczące synchronizacji katalogu.
* Usługa Azure AD Connect obsługuje teraz nowy tryb instalacji o nazwie **Użyj istniejącej bazy danych**. Ten tryb instalacji pozwala klientom zainstalować program Azure AD Connect, która określa istniejącej bazy danych ADSync. Aby uzyskać więcej informacji na temat tej funkcji można znaleźć w artykule [Użyj istniejącej bazy danych](how-to-connect-install-existing-database.md).
* Ze względów bezpieczeństwa usługa Azure AD Connect teraz domyślnie korzysta TLS1.2 do łączenia z usługą Azure AD do synchronizacji katalogów. Wcześniej wartość domyślna: TLS1.0.
* Podczas uruchamiania agenta Azure AD Connect hasło synchronizacji próbuje nawiązać połączenie z dobrze znanego punktu końcowego usługi Azure AD do synchronizacji haseł. Po pomyślnym nawiązaniu połączenia zostanie przeniesiona do punktu końcowego określonego regionu. Wcześniej agenta synchronizacji haseł buforuje punktu końcowego określonego regionu, dopóki nie zostanie uruchomiona ponownie. Teraz agenta powoduje wyczyszczenie pamięci podręcznej i ponownych prób przy użyciu dobrze znanych punktu końcowego, jeśli wykryje problem z połączeniem z punktem końcowym specyficzne dla regionu. Ta zmiana zapewnia, że synchronizacji haseł w pamięci podręcznej punktu końcowego określonego regionu nie jest już dostępny, można trybu failover do innego punktu końcowego określonego regionu.
* Aby zsynchronizować zmiany z lokalnego lasu usługi AD, wymagane jest konto usługi AD DS. Można (i) utworzyć usług AD DS, konto użytkownika i podaj swoje poświadczenia do usługi Azure AD Connect lub (ii), podaj poświadczenia administratora przedsiębiorstwa i pozwól program Azure AD Connect, Utwórz konto usług AD DS dla Ciebie. Wcześniej (i) jest opcją domyślną w kreatorze program Azure AD Connect. Teraz (ii) jest opcją domyślną.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano obsługę dla chmury platformy Microsoft Azure dla instytucji rządowych i Microsoft Cloud w Niemczech.

### <a name="ad-fs-management"></a>AD FS Management
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Polecenie cmdlet ADSyncNGCKeysWriteBack inicjowania w module powershell przygotowania usługi AD został niepoprawnie zastosowania list ACL do kontenera rejestracji urządzeń i w związku z tym może tylko dziedziczyć istniejące uprawnienia.  Zaktualizowano, aby konto usługi synchronizacji ma odpowiednie uprawnienia.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Zadanie usługi AAD Connect Sprawdź usług AD FS Login został zaktualizowany tak, aby sprawdzi logowania przy użyciu witryny Microsoft Online i nie tylko token pobierania z usług AD FS.
* Podczas konfigurowania nowej farmy usług AD FS przy użyciu programu AAD Connect, strony, pytanie o poświadczenia usług AD FS został przeniesiony, aby go teraz występuje, zanim użytkownik jest pytany, aby zapewnić serwerów usług AD FS i WAP.  Dzięki temu AAD Connect sprawdzić, czy określone konto ma odpowiednie uprawnienia.
* Podczas uaktualniania program AAD Connect firma Microsoft nie jest już skończyło uaktualniania zaufania usług AD FS usługi AAD nie może zaktualizować.  Jeśli tak się stanie, użytkownik zostanie wyświetlony odpowiedni komunikat ostrzegawczy i przejdzie do resetowania relacji zaufania za pomocą zadania dodatkowe AAD Connect.

### <a name="seamless-single-sign-on"></a>Bezproblemowe logowanie jednokrotne
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem powodujący Kreator Azure AD Connect, zostać zwrócony błąd, jeśli zostanie podjęta próba włączenia [bezproblemowego logowania jednokrotnego](how-to-connect-sso.md). Komunikat o błędzie *"Configuration of Microsoft Azure AD Connect Agent uwierzytelniania nie powiodło się."* Ten problem wpływa na istniejących klientów, którzy ręcznie przeprowadzili uaktualnienie z wersji zapoznawczej agentów uwierzytelniania dla [uwierzytelniania przekazywanego](how-to-connect-sso.md) na podstawie kroków opisanych w tym [artykułu](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stan: Lipiec 2017 23

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem

* Rozwiązano problem powodujący reguły synchronizacji out-of-box "Poza usługą AD - ImmutableId użytkownika" do usunięcia:

  * Ten problem występuje, gdy program Azure AD Connect zostanie uaktualniona, lub gdy opcja zadania *Zaktualizuj konfigurację synchronizacji* w programie Azure AD Connect Kreator służy do aktualizowania konfiguracji synchronizacji usługi Azure AD Connect.
  
  * Ta reguła synchronizacji ma zastosowanie do klientów, którzy włączyli [ms-DS-ConsistencyGuid jako funkcja zakotwiczenie źródła](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Ta funkcja została wprowadzona w 1.1.524.0 wersji i po. Po usunięciu reguły synchronizacji Azure AD Connect nie będzie można wypełnić lokalnych atrybutu ms-DS-ConsistencyGuid usługi AD z wartością atrybutu ObjectGuid. Go nie uniemożliwia nowym użytkownikom aprowizowane w usłudze Azure AD.
  
  * Poprawki gwarantuje, że reguła synchronizacji zostanie nie jest już usunięte podczas uaktualniania lub podczas zmiany konfiguracji, tak długo, jak ta funkcja jest włączona. Dla istniejących klientów, którzy mają zostać objęte tym problemem poprawki gwarantuje również, że reguła synchronizacji zostanie dodany po uaktualnieniu do tej wersji programu Azure AD Connect.

* Rozwiązano problem, który powoduje, że reguły synchronizacji out-of-box, aby mieć wartość priorytetu, która jest mniejsza niż 100:

  * Ogólnie rzecz biorąc pierwszeństwo wartości 0 - 99 są zarezerwowane dla reguły synchronizacji niestandardowych. Podczas uaktualniania wartości pierwszeństwo reguły synchronizacji out-of-box są aktualizowane w celu uwzględnienia zmiany reguł synchronizacji. Z powodu tego problemu reguły synchronizacji out-of-box można przypisać wartość pierwszeństwa, która jest mniejsza niż 100.
  
  * Poprawki zapobiega problem występuje podczas uaktualniania. Jednak nie są przywracane wartości priorytetu dla istniejących klientów, którzy została dotknięta problem. Oddzielna poprawka zostanie udzielona w przyszłości pomoc w przywróceniu.

* Rozwiązano problem, gdzie [domeny i jednostki Organizacyjnej filtrowania na ekranie](how-to-connect-install-custom.md#domain-and-ou-filtering) w programie Azure AD Connect kreatora są wyświetlane *Synchronizuj wszystkie domeny i jednostki organizacyjne* opcję wybrane, mimo że filtrowanie na podstawie jednostki Organizacyjnej jest włączona.

*   Rozwiązano problem, który spowodował [ekran Konfigurowanie partycji katalogu](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) w Menedżerze usługi synchronizacji zostać zwrócony błąd, jeśli *Odśwież* przycisku. Komunikat o błędzie *"Wystąpił błąd podczas odświeżania domen: Nie można rzutować obiektu typu "System.Collections.ArrayList" na typ "Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* Ten błąd występuje, gdy nowej domeny usługi AD został dodany do istniejącego lasu usługi AD i próbujesz zaktualizować program Azure AD Connect, korzystając z przycisku odświeżania.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* [Funkcji automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) została rozszerzona, aby wspierać klientów skonfigurowane w następujący sposób:
  * Włączono funkcję zapisywania zwrotnego urządzeń.
  * Włączono funkcję zapisywania zwrotnego grup.
  * Instalacja nie jest ustawień ekspresowych lub uaktualnienie narzędzia DirSync.
  * Masz ponad 100 000 obiektów w magazynie metaverse.
  * Łączysz się do więcej niż jednym lesie. Instalacja ekspresowa tylko łączy się z jednego lasu.
  * Konta łącznika AD nie jest już domyślne konto MSOL_.
  * Serwer jest ustawiona na tryb przejściowy.
  * Włączono funkcję zapisywania zwrotnego użytkowników.
  
  >[!NOTE]
  >Rozszerzenie zakresu funkcji automatycznego uaktualniania ma wpływ na klientów za pomocą usługi Azure AD Connect 1.1.105.0 kompilacji i po nim. Jeśli chcesz, aby serwer usługi Azure AD Connect ma zostać automatycznie uaktualniony, należy uruchomić poniższe polecenie cmdlet na serwerze programu Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Więcej informacji na temat włączania/wyłączania automatycznego uaktualniania można znaleźć w artykule [program Azure AD Connect: Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Stan: Nie zostanie zwolniony. Zmiany w tej kompilacji są uwzględnione w wersji 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem

* Rozwiązano problem powodujący reguły synchronizacji out-of-box "Poza usługą AD - ImmutableId użytkownika" została usunięta po zaktualizowaniu konfiguracji filtrowania opartego na jednostce Organizacyjnej. Ta reguła synchronizacji jest wymagany dla [ms-DS-ConsistencyGuid jako funkcja zakotwiczenie źródła](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Rozwiązano problem, gdzie [domeny i jednostki Organizacyjnej filtrowania na ekranie](how-to-connect-install-custom.md#domain-and-ou-filtering) w programie Azure AD Connect kreatora są wyświetlane *Synchronizuj wszystkie domeny i jednostki organizacyjne* opcję wybrane, mimo że filtrowanie na podstawie jednostki Organizacyjnej jest włączona.

*   Rozwiązano problem, który spowodował [ekran Konfigurowanie partycji katalogu](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) w Menedżerze usługi synchronizacji zostać zwrócony błąd, jeśli *Odśwież* przycisku. Komunikat o błędzie *"Wystąpił błąd podczas odświeżania domen: Nie można rzutować obiektu typu "System.Collections.ArrayList" na typ "Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* Ten błąd występuje, gdy nowej domeny usługi AD został dodany do istniejącego lasu usługi AD i próbujesz zaktualizować program Azure AD Connect, korzystając z przycisku odświeżania.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* [Funkcji automatycznego uaktualniania](how-to-connect-install-automatic-upgrade.md) została rozszerzona, aby wspierać klientów skonfigurowane w następujący sposób:
  * Włączono funkcję zapisywania zwrotnego urządzeń.
  * Włączono funkcję zapisywania zwrotnego grup.
  * Instalacja nie jest ustawień ekspresowych lub uaktualnienie narzędzia DirSync.
  * Masz ponad 100 000 obiektów w magazynie metaverse.
  * Łączysz się do więcej niż jednym lesie. Instalacja ekspresowa tylko łączy się z jednego lasu.
  * Konta łącznika AD nie jest już domyślne konto MSOL_.
  * Serwer jest ustawiona na tryb przejściowy.
  * Włączono funkcję zapisywania zwrotnego użytkowników.
  
  >[!NOTE]
  >Rozszerzenie zakresu funkcji automatycznego uaktualniania ma wpływ na klientów za pomocą usługi Azure AD Connect 1.1.105.0 kompilacji i po nim. Jeśli chcesz, aby serwer usługi Azure AD Connect ma zostać automatycznie uaktualniony, należy uruchomić poniższe polecenie cmdlet na serwerze programu Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Więcej informacji na temat włączania/wyłączania automatycznego uaktualniania można znaleźć w artykule [program Azure AD Connect: Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Stan: Lipca 2017 r.

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect automatyczne uaktualnianie.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązano problem
* Rozwiązano problem z poleceniem cmdlet Initialize-ADSyncDomainJoinedComputerSync, który spowodował zweryfikowanej domeny, które są skonfigurowane na istniejący obiekt punktu połączenia usługi można zmienić, nawet jeśli nadal jest prawidłową domenę. Ten problem występuje, gdy dzierżawy usługi Azure AD ma więcej niż jeden zweryfikowanych domen, które mogą służyć do konfigurowania punktu połączenia usługi.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Zapisywanie zwrotne haseł jest teraz dostępna w wersji zapoznawczej z platformy Microsoft Azure Government cloud i Microsoft Cloud w Niemczech. Aby uzyskać więcej informacji o obsłudze wystąpień innej usługi Azure AD Connect można znaleźć w artykule [program Azure AD Connect: Specjalne uwagi dotyczące wystąpień](reference-connect-instances.md).

* Polecenia cmdlet Initialize-ADSyncDomainJoinedComputerSync dodano nowy parametr opcjonalny o nazwie AzureADDomain. Ten parametr umożliwia określenie, które zweryfikowane domeny ma być używany do konfigurowania punktu połączenia usługi.

### <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Nazwa agenta wymagane do uwierzytelniania przekazywanego została zmieniona z *łącznika serwera Proxy w aplikacji pakietu Microsoft Azure AD* do *agenta: Microsoft Azure AD Connect uwierzytelniania*.

* Włączanie uwierzytelniania przekazującego już umożliwia synchronizację skrótów haseł domyślnie.


## <a name="115530"></a>1.1.553.0
Stan: Czerwca 2017 r.

> [!IMPORTANT]
> Brak schematu i synchronizowanie zmian reguły, wprowadzone w tej kompilacji. Usługa Azure AD Connect synchronizacji wyzwoli pełne importowanie i pełną synchronizację czynności po uaktualnieniu. Poniżej opisano szczegóły zmiany. Aby tymczasowo Odrocz pełne importowanie i pełną synchronizację czynności po uaktualnieniu, zobacz artykuł [jak mają być odroczone pełnej synchronizacji po uaktualnieniu](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Znany problem
* Występuje problem, który wpływa na klientów, którzy używają [filtrowania na podstawie jednostki Organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) za pomocą synchronizacji programu Azure AD Connect. Po przejściu do [domeny i jednostki Organizacyjnej filtrowanie strony](how-to-connect-install-custom.md#domain-and-ou-filtering) w kreatorze program Azure AD Connect oczekuje się następujące zachowanie:
  * Jeśli włączono filtrowanie na podstawie jednostki Organizacyjnej **Synchronizuj wybrane domeny i jednostki organizacyjne** opcja jest zaznaczona.
  * W przeciwnym razie **Synchronizuj wszystkie domeny i jednostki organizacyjne** opcja jest zaznaczona.

Jest to problem, który pojawia się, że **Synchronizuj wszystkie domeny i jednostki organizacyjne opcja** jest zawsze zaznaczone, po uruchomieniu kreatora.  Dzieje się tak, nawet jeśli filtrowanie na podstawie jednostki Organizacyjnej był wcześniej skonfigurowany. Przed zapisaniem zmian konfiguracji usługi AAD Connect, upewnij się, że **Synchronizowanie wybranych domen i jednostek organizacyjnych opcja jest zaznaczona** i upewnij się, że włączono wszystkich jednostek organizacyjnych, które należy zsynchronizować ponownie. W przeciwnym razie filtrowania na podstawie jednostki Organizacyjnej, zostaną wyłączone.

#### <a name="fixed-issues"></a>Rozwiązane problemy

* Rozwiązano problem z zapisywaniem zwrotnym haseł, która umożliwia administratorowi usługi Azure AD można zresetować hasła lokalnego AD uprzywilejowane konto użytkownika. Ten problem występuje, gdy program Azure AD Connect otrzymuje uprawnienia do resetowania hasła za pośrednictwem konta uprzywilejowanego. Problem został rozwiązany w tej wersji programu Azure AD Connect, nie zezwalając Administrator usługi Azure AD można zresetować hasła dowolnego lokalnego AD uprzywilejowane konto użytkownika, o ile nie jest właścicielem tego konta. Aby uzyskać więcej informacji, zobacz [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453).

* Rozwiązano problem, związane z [ms-DS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funkcji, w którym program Azure AD Connect jest nie funkcja zapisywania zwrotnego w środowisku lokalnym atrybutu ms-DS-ConsistencyGuid usługi AD. Ten problem występuje w przypadku wielu lokalnych lasów usługi AD dodane do programu Azure AD Connect i *tożsamości użytkowników istnieją w wielu opcji katalogi* jest zaznaczone. W przypadku takiej konfiguracji reguły synchronizacji wynikowy nie wypełnić atrybut sourceAnchorBinary w magazynie Metaverse. Atrybut sourceAnchorBinary służy jako atrybut źródłowy dla atrybutu ms-DS-ConsistencyGuid. W wyniku zapisywania zwrotnego do atrybutu ms-DSConsistencyGuid nie występuje. Aby rozwiązać ten problem, następujące reguły synchronizacji zostały zaktualizowanie, aby upewnić się, że atrybut sourceAnchorBinary w magazynie Metaverse zawsze jest wypełniana:
  * W z usługi AD - InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * W z usługi AD - AccountEnabled.xml użytkownika
  * W z usługi AD - wstawić użytkownika
  * W z usługi AD — użytkownik przyłączyć SOAInAAD.xml

* Wcześniej nawet jeśli [ms-DS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funkcja nie jest włączona, reguła synchronizacji "Poza usługą AD — wartość ImmutableId użytkownika" nadal jest dodawana do usługi Azure AD Connect. Efekt jest nieszkodliwe i nie powoduje zapisywanie zwrotne atrybutu ms-DS-ConsistencyGuid wystąpić. Aby uniknąć nieporozumień, dodano logiki upewnij się, że reguła synchronizacji jest dodawana tylko po włączeniu tej funkcji.

* Rozwiązano problem powodujący synchronizacji skrótów haseł nie powiedzie się z zdarzenie błędu 611. Ten problem występuje, gdy jedna lub więcej domeny kontrolerów zostały usunięte z lokalnej usługi AD. Na końcu każdego cyklu synchronizacji haseł, pliku cookie synchronizacji wystawiony przez lokalnej usługi AD zawierają identyfikatory wywołania usuniętych kontrolerów domeny przy użyciu numeru USN (numeru sekwencji aktualizacji) wartość 0. Menedżer synchronizacji haseł nie będzie mógł zachować synchronizacji plik cookie zawierający wartość USN 0 i kończy się niepowodzeniem z zdarzenie błędu 611. Podczas następnego cyklu synchronizacji Menedżer synchronizacji haseł ponownie używa ostatniego pliku cookie synchronizacji utrwalonych, który nie zawiera numeru USN równą 0. Powoduje to identycznych zmian hasła są ponownie zsynchronizowane. Dzięki tej poprawce Menedżera synchronizacji haseł są utrwalane pliku cookie synchronizacji poprawnie.

* Wcześniej nawet jeśli automatyczne uaktualnianie zostało wyłączone za pomocą polecenia cmdlet Set-ADSyncAutoUpgrade, automatycznego uaktualniania proces jest kontynuowany do wyszukania okresowo uaktualniania i opiera się na pobrany program instalacyjny respektować niepełnosprawność. Dzięki tej poprawce procesu automatycznego uaktualniania nie sprawdza, czy uaktualnienie okresowo. Poprawka jest automatycznie stosowana podczas uaktualniania Instalatora dla tej wersji Azure AD Connect jest wykonywane raz.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Wcześniej [ms-DS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funkcja była dostępna tylko dla nowych. Teraz jest dostępna dla istniejących wdrożeń. Więcej szczegółów:
  * Aby uzyskać dostęp do tej funkcji, uruchom Kreatora programu Azure AD Connect, a następnie wybierz *zakotwiczenie źródła aktualizacji* opcji.
  * Ta opcja jest widoczna tylko do istniejących wdrożeń, które używają objectGuid jako atrybutu sourceAnchor.
  * Podczas konfigurowania opcji, Kreator sprawdza stan atrybutu ms-DS-ConsistencyGuid w usłudze Active Directory w środowisku lokalnym. Jeśli ten atrybut nie jest skonfigurowany dla dowolnego obiektu użytkownika w katalogu, kreator używa ms-DS-ConsistencyGuid jako atrybutu sourceAnchor. Jeśli ten atrybut jest skonfigurowany na co najmniej jeden obiekt użytkownika w katalogu, Kreator stwierdza, ten atrybut jest używany przez inne aplikacje nie nadaje się jako atrybut sourceAnchor i nie zezwala na zmianę zakotwiczenia źródła, aby kontynuować. Jeśli masz pewność, że ten atrybut nie jest używane przez istniejące aplikacje, należy się z pomocą techniczną, aby uzyskać informacje dotyczące pomijania błędu.

* Specyficzne dla **userCertificate** atrybutu obiekty urządzeń, Azure teraz AD Connect wyszukuje certyfikatów wartości wymagane dla [Podłączanie urządzeń przyłączonych do domeny do usługi Azure AD dla systemu Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) i odfiltrowuje rest przed synchronizacją z usługą Azure AD. Aby włączyć to zachowanie, reguły synchronizacji out-of-box "Się do usługi AAD — urządzenie Dołącz SOAInAD" został zaktualizowany.

* Usługa Azure AD Connect teraz obsługuje funkcję zapisywania zwrotnego dla usługi Exchange Online **cloudPublicDelegates** atrybutu do lokalnej usługi AD **publicDelegates** atrybutu. Dzięki temu scenariusz, w którym skrzynki pocztowej programu Exchange Online można udzielić praw SendOnBehalfTo użytkownikom ze skrzynki pocztowej programu Exchange w środowisku lokalnym. Aby obsługiwać tę funkcję, nową regułę synchronizacji out-of-box "Poza usługą AD — zapisywanie zwrotne PublicDelegates hybrydowego programu Exchange użytkownika" został dodany. Ta reguła synchronizacji jest dodawana do usługi Azure AD Connect, tylko po włączeniu funkcji hybrydowego programu Exchange.

* Usługa Azure AD Connect obsługuje teraz Synchronizowanie **altRecipient** atrybutu z usługi Azure AD. Aby obsługiwać tę zmianę, następujące reguły synchronizacji out-of-box zostały zaktualizowane do uwzględnienia przepływu wymaganego atrybutu:
  * W z usługi AD — użytkownik programu Exchange
  * Out do usługi AAD — ExchangeOnline użytkownika
  
* **CloudSOAExchMailbox** atrybutów w magazynie Metaverse wskazuje, czy dany użytkownik ma pocztową usługi Exchange Online, czy nie. Aby uwzględnić dodatkowe Exchange Online RecipientDisplayTypes jako takie skrzynki pocztowe urządzeń i Sala konferencyjna został zaktualizowany jego definicji. Aby włączyć tę zmianę, definicja atrybutu cloudSOAExchMailbox, który znajduje się w obszarze reguła synchronizacji out-of-box "W z usługi AAD — użytkownik wdrożenie hybrydowe programu Exchange", został zaktualizowany od:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... do następującego:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Dodano następujący zestaw funkcji zgodnego z X509Certificate2 do tworzenia wyrażenia reguł do obsługi certyfikatów wartości danego atrybutu userCertificate synchronizacji:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Wybierz|
    |CertKeyAlgorithmParams|CertHashString|Lokalizacja|
    |||Zawiera|

* Aby umożliwić klientom tworzenie reguły synchronizacji niestandardowych do przepływu, sAMAccountName, domainNetBios i domainFQDN dla grupy obiektów, a także distinguishedName obiektów użytkownika zostały wprowadzone następujące zmiany schematu:

  * Następujące atrybuty zostały dodane do schematu MV:
    * Grupa: AccountName
    * Grupa: domainNetBios
    * Grupa: domainFQDN
    * Osoby: distinguishedName

  * Następujące atrybuty zostały dodane do schematu łącznik usługi Azure AD:
    * Grupa: OnPremisesSamAccountName
    * Grupa: NetBiosName
    * Grupa: DnsDomainName
    * Użytkownik: OnPremisesDistinguishedName

* Skrypt polecenia cmdlet ADSyncDomainJoinedComputerSync dodano nowy parametr opcjonalny o nazwie AzureEnvironment. Parametr służy do określania, który region odpowiedniego dzierżawy usługi Azure Active Directory znajduje się w. Prawidłowe wartości to:
  * AzureCloud (default)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Zaktualizowano Edytor reguł synchronizacji do użycia Join (zamiast Aprowizować) jako wartość domyślna typu łącza podczas tworzenia reguły synchronizacji.

### <a name="ad-fs-management"></a>Zarządzanie usługami AD FS

#### <a name="issues-fixed"></a>Problemy rozwiązane

* Następujące adresy URL są nowe usługi WS-Federation punkty końcowe wprowadzone przez usługę Azure AD, aby zwiększyć odporność na awarię uwierzytelniania i zostanie dodany do lokalnej konfiguracji usług AD FS odpowiedzieć ze stron relacji zaufania:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Rozwiązano problem powodujący usług AD FS do generowania oświadczenia niepoprawne wartości dla IssuerID. Ten problem występuje, jeśli jest wiele domen zweryfikowanej w dzierżawie usługi Azure AD i sufiks domeny atrybutu userPrincipalName służącego do generowania oświadczenie IssuerID jest co najmniej 3 poziomy szczegółowe (na przykład johndoe@us.contoso.com). Problem zostanie rozwiązany, aktualizując wyrażenie regularne używane przez reguły oświadczeń.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Wcześniej funkcji usług AD FS zarządzania certyfikatami, dostarczanej przez program Azure AD Connect należy używać tylko z farmy usług AD FS, zarządzane przez program Azure AD Connect. Teraz używając funkcji z farmy usług AD FS, które nie są zarządzane za pomocą usługi Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Wydanie: Maja 2017 r.

> [!IMPORTANT]
> Brak schematu i synchronizowanie zmian reguły, wprowadzone w tej kompilacji. Usługa Azure AD Connect synchronizacji wyzwoli pełne importowanie i pełną synchronizację czynności po uaktualnieniu. Poniżej opisano szczegóły zmiany.
>
>

**Rozwiązano problemy:**

Synchronizacja programu Azure AD Connect

* Rozwiązano problem powodujący automatyczne uaktualnienie do odbywa się na serwerze usługi Azure AD Connect, nawet jeśli klient wyłączył tę funkcję, za pomocą polecenia cmdlet Set-ADSyncAutoUpgrade. Dzięki tej poprawce proces automatycznego uaktualniania na serwerze nadal sprawdza, czy uaktualnienie okresowo, ale pobrany program instalacyjny honoruje konfiguracji automatycznego uaktualniania.
* Podczas uaktualniania w miejscu narzędzia DirSync Azure AD Connect tworzy konto usługi Azure AD do użycia przez łącznik usługi Azure AD do synchronizacji z usługą Azure AD. Po utworzeniu konta usługi Azure AD Connect uwierzytelnia się za pomocą usługi Azure AD przy użyciu konta. Czasami, uwierzytelnianie nie powiedzie się z powodu przejściowych problemów, co z kolei powoduje, że uaktualnienie w miejscu narzędzia DirSync z powodu błędu *"Wystąpił błąd podczas wykonywania zadania Konfiguruj AAD Sync: AADSTS50034: Aby zalogować się do tej aplikacji, to konto należy dodać do katalogu xxx.onmicrosoft.com."* Aby zwiększyć odporność uaktualnienie narzędzia DirSync, Azure AD Connect teraz ponawia etap uwierzytelniania.
* Wystąpił problem z kompilacją 443, który powoduje, że uaktualnienie w miejscu narzędzia DirSync została wykonana pomyślnie, ale nie zostaną utworzone profile przebiegu, wymagane do synchronizacji katalogów. Naprawianie logiki znajduje się w tej kompilacji programu Azure AD Connect. Podczas uaktualniania klienta do tej kompilacji, Azure AD Connect wykrywa brak profilów uruchamiania i ich tworzenia.
* Rozwiązano problem, który powoduje, że proces synchronizacji haseł nie można uruchomić za pomocą 6900 identyfikator zdarzenia i błąd *"został już dodany element o takim samym kluczem"* . Ten problem występuje, jeśli zaktualizujesz jednostki Organizacyjnej konfigurację filtrowania do uwzględnienia w partycji konfiguracji usługi AD. Aby rozwiązać ten problem, proces synchronizacji haseł synchronizuje zmiany haseł z tylko partycji domeny usługi AD. Domeny inne niż partycje, takich jak partycja konfiguracji są pomijane.
* Podczas instalacji ekspresowej, Azure AD Connect tworzy lokalne konto usług AD DS do użycia przez AD connector do komunikowania się z lokalnej usługi AD. Wcześniej konto zostanie utworzone z flagą PASSWD_NOTREQD, ustaw dla atrybutu Kontrola konta użytkownika i hasła losowego jest ustawiona na koncie. Teraz program Azure AD Connect jawnie usuwa flagę PASSWD_NOTREQD, po ustawieniu hasła dla konta.
* Rozwiązano problem, który powoduje, że uaktualnienie narzędzia DirSync kończy się niepowodzeniem z powodu błędu *"zakleszczenie wystąpił w programie sql server próby uzyskania blokady aplikacji"* gdy atrybut mailNickname znajduje się w lokalnej schematu usługi AD, ale nie jest ograniczany do Klasa obiektu użytkownika AD.
* Rozwiązano problem powodujący funkcji zapisywania zwrotnego urządzeń automatycznie były wyłączone, gdy administrator aktualizuje konfigurację synchronizacji programu Azure AD Connect przy użyciu Kreatora Azure AD Connect. Ten problem jest spowodowany przez kreatora sprawdzanie wymaganego wstępnie istniejącej konfiguracji funkcji zapisywania zwrotnego urządzeń w lokalnej usługi AD i sprawdzenie nie powiedzie się. Obejście polega na pomijania sprawdzania, jeśli zapisywania zwrotnego urządzeń jest już włączone wcześniej.
* Aby skonfigurować filtrowanie jednostki Organizacyjnej, możesz użyć Kreatora programu Azure AD Connect lub Menedżera usługi synchronizacji. Wcześniej korzystając z Kreatora programu Azure AD Connect do konfigurowania filtrowania jednostki Organizacyjnej, nowe jednostki organizacyjne utworzone później są dołączone do synchronizacji katalogów. Jeśli chcesz, aby nowe jednostki organizacyjne zostanie uwzględniona, należy skonfigurować jednostkę Organizacyjną, filtrowanie, przy użyciu Menedżera usługi synchronizacji. Teraz możesz uzyskać takie samo zachowanie za pomocą Kreatora Azure AD Connect.
* Rozwiązano problem, który powoduje, że wymagane przez program Azure AD Connect ma zostać utworzony w ramach schematu instalowanie administratora, a nie w schemacie dbo procedury składowane.
* Rozwiązano problem, który powoduje, że atrybut TrackingId zwracane przez usługę Azure AD, można pominąć w usłudze AAD Connect dzienników zdarzeń serwera. Ten problem występuje, jeśli program Azure AD Connect odbiera komunikat przekierowania z usługi Azure AD i Azure AD Connect nie jest w stanie połączyć się z określony punkt końcowy. Identyfikator śledzenia jest używany przez pracowników działu pomocy technicznej można skorelować z dziennikami po stronie usługi podczas rozwiązywania problemów.
* Gdy program Azure AD Connect odbiera błąd LargeObject z usługi Azure AD, Azure AD Connect generuje zdarzenie z EventID 6941 i komunikatem *"elastycznie obiektu jest zbyt duży. Ogranicz liczbę wartości atrybutu dla tego obiektu."* W tym samym czasie, program Azure AD Connect generuje również mylący zdarzeń z EventID 6900 i komunikatem *"Microsoft.Online.Coexistence.ProvisionRetryException: Nie można nawiązać połączenia z usługą Windows Azure Active Directory."* Aby zminimalizować ryzyko pomyłek, program Azure AD Connect nie generuje już ostatnim przypadku, gdy zostanie odebrany błąd LargeObject.
* Rozwiązano problem powodujący Synchronization Service Manager przestanie odpowiadać, podczas próby zaktualizowania konfiguracji ogólny łącznik LDAP.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Zastosowano zmiany reguł synchronizacji — następujące zmiany reguł synchronizacji:
  * Reguły synchronizacji Zaktualizowano domyślne ustawienie Eksportuj atrybuty **userCertificate** i **userSMIMECertificate** Jeśli atrybutów ma więcej niż 15 wartości.
  * Atrybuty usługi AD **employeeID** i **msExchBypassModerationLink** są teraz objęte domyślny zestaw reguł synchronizacji.
  * Atrybut usługi AD **zdjęcie** została usunięta z domyślnego zestawu reguł synchronizacji.
  * Dodano **preferredDataLocation** schematu Metaverse i schemat łącznika usługi AAD. Klienci, którzy chcą, aby zaktualizować obu atrybutów w usłudze Azure AD można zaimplementować reguły synchronizacji niestandardowych, aby to zrobić. 
  * Dodano **userType** schematu Metaverse i schemat łącznika usługi AAD. Klienci, którzy chcą, aby zaktualizować obu atrybutów w usłudze Azure AD można zaimplementować reguły synchronizacji niestandardowych, aby to zrobić.

* Program Azure AD Connect automatycznie umożliwia obecnie korzystanie z atrybutu ConsistencyGuid jako atrybutu zakotwiczenia źródła dla lokalnego obiektami usługi AD. Dalsze, Azure AD Connect wypełnia atrybut ConsistencyGuid z wartością atrybutu objectGuid, jeśli jest on pusty. Ta funkcja dotyczy tylko nowe wdrożenie. Aby dowiedzieć się więcej na temat tej funkcji, można znaleźć w sekcji artykułu [program Azure AD Connect: Design concepts - Using msDS-ConsistencyGuid as sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) (Azure AD Connect: zagadnienia dotyczące projektowania — korzystanie z atrybutu msDS-ConsistencyGuid jako atrybutu sourceAnchor).
* Rozwiązywania problemów polecenia cmdlet Invoke-ADSyncDiagnostics została dodana do zdiagnozowania synchronizacji skrótów haseł problemy związane z usługą. Aby dowiedzieć się, jak za pomocą polecenia cmdlet, można znaleźć w artykule [Rozwiązywanie problemów z synchronizacją skrótów haseł z usługą Azure AD Connect sync](tshoot-connect-password-hash-synchronization.md).
* Program Azure AD Connect, teraz obsługuje folderu publicznego z włączoną obsługą poczty Synchronizowanie obiektów z lokalnej usługi AD z usługą Azure AD. Można włączyć funkcję przy użyciu Kreatora Azure AD Connect w obszarze funkcje opcjonalne. Aby dowiedzieć się więcej na temat tej funkcji, można znaleźć w artykule [Office 365 katalogu na podstawie blokowanie na serwerze granicznym na obsługę lokalną włączone foldery publiczne poczty](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect wymaga przy użyciu usług AD DS konto do synchronizacji z lokalnej usługi AD. Wcześniej Jeśli zainstalowano program Azure AD Connect przy użyciu trybu Express można podajesz poświadczenia konta administratora przedsiębiorstwa i Azure AD Connect utworzyłoby wymagane jest konto usług AD DS. Do instalacji niestandardowej i dodawanie lasów do istniejącego wdrożenia były wymagane zamiast tego podać konto usług AD DS. Teraz masz również opcję, aby podać poświadczenia konta administratora przedsiębiorstwa podczas instalacji niestandardowej i pozwól program Azure AD Connect, utworzyć wymagane jest konto usług AD DS.
* Program Azure AD Connect obsługuje teraz SQL AOA. Przed zainstalowaniem programu Azure AD Connect, należy włączyć SQL AOA. Podczas instalacji program Azure AD Connect wykrywa, czy podane wystąpienie programu SQL jest włączone dla SQL AOA. Po włączeniu SQL AOA Azure dalsze AD Connect wpadł na pomysł Jeśli SQL AOA jest skonfigurowana do replikacji synchronicznej lub asynchronicznej replikacji. Podczas konfigurowania odbiornika grupy dostępności, zalecane jest, ustaw właściwość RegisterAllProvidersIP na 0. To zalecenie jest, ponieważ aktualnie program Azure AD Connect używa programu SQL Native Client nawiązać połączenia z programem SQL i programu SQL Native Client nie obsługuje korzystanie z właściwości MultiSubNetFailover.
* Jeśli jest używany program LocalDB jako bazy danych dla serwera Azure AD Connect i osiągnęła limit rozmiaru 10 GB, usługa synchronizacji nie będzie można uruchomić. Wcześniej należy wykonać operacji ShrinkDatabase na LocalDB do odzyskania wystarczającej ilości miejsca w bazie danych usługi synchronizacji rozpocząć. Po upływie którego można użyć Menedżera usługi synchronizacji do usunięcia historii uruchamiania w celu odzyskania więcej miejsca w bazie danych. Teraz umożliwia polecenia cmdlet Start-ADSyncPurgeRunHistory przeczyszczania Uruchom historyczne dane z lokalnej bazy danych do odzyskania miejsca w bazie danych. Ponadto to polecenie cmdlet obsługuje tryb offline (, określając parametr - offline) którego można użyć, gdy usługa synchronizacji nie jest uruchomiona. Uwaga: W trybie offline można używać tylko w przypadku, jeśli usługa synchronizacji nie jest uruchomiona, a baza danych, używane jest LocalDB.
* Aby zmniejszyć ilość miejsca do magazynowania wymaganego, usługi Azure AD Connect teraz kompresuje szczegóły błędu synchronizacji przed ich zapisaniem w bazach danych LocalDB/SQL. Podczas uaktualniania ze starszej wersji programu Azure AD Connect do tej wersji, program Azure AD Connect wykonuje jednorazowe kompresji na istniejące szczegóły błędu synchronizacji.
* Wcześniej po zaktualizowaniu konfiguracji filtrowania jednostek organizacyjnych, należy ręcznie uruchomić pełny import, aby upewnić się, że istniejące obiekty są poprawnie uwzględniony/wykluczony z synchronizacji katalogów. Teraz program Azure AD Connect automatyczne wyzwolenie pełny import podczas następnej synchronizacji cyklu. Dalsze, pełny import są stosowane tylko do łączników usługi AD, aktualizacja dotyczy. Uwaga: ta poprawa ma zastosowanie do filtrowania aktualizacji zostało nawiązane przy użyciu Kreatora Azure AD Connect tylko jednostki Organizacyjnej. Nie ma zastosowania do filtrowania aktualizacji zostało nawiązane przy użyciu Menedżera usługi synchronizacji w jednostce Organizacyjnej.
* Poprzednio filtrowania na podstawie grupy obsługuje użytkowników, grup i skontaktuj się z tylko do obiektów. Teraz filtrowanie na podstawie grupy obsługuje również obiekty komputerów.
* Wcześniej można usunąć przestrzeni łącznika danych bez konieczności wyłączania harmonogram synchronizacji Azure AD Connect. Teraz Menedżera usługi synchronizacji blokuje usunięcie obszaru łącznika danych, jeśli wykryje, że harmonogram jest włączona. Co więcej ostrzeżenie jest zwracany do natychmiastowego informowania klientów o możliwej utracie danych, jeśli dane przestrzeni łącznika zostanie usunięty.
* Wcześniej należy wyłączyć transkrypcji programu PowerShell do poprawnego działania przy użyciu Kreatora Azure AD Connect. Ten problem nie zostanie rozwiązany częściowo. Można włączyć PowerShell transkrypcji, jeśli używasz kreatora Azure AD Connect do zarządzania konfiguracją synchronizacji. Jeśli używasz kreatora Azure AD Connect do zarządzania konfiguracji usług AD FS, należy wyłączyć transkrypcji programu PowerShell.



## <a name="114860"></a>1.1.486.0
Wydanie: Kwietnia 2017 r.

**Rozwiązano problemy:**
* Rozwiązano problem, w którym program Azure AD Connect nie zostaną zainstalowane pomyślnie na zlokalizowanej wersji systemu Windows Server.

## <a name="114840"></a>1.1.484.0
Wydanie: Kwietnia 2017 r.

**Znane problemy:**

* Ta wersja programu Azure AD Connect nie zostaną pomyślnie zainstalowane, jeśli są spełnione wszystkie następujące warunki:
   1. Wykonywana albo narzędzia DirSync w miejscu uaktualnienia lub nowej instalacji programu Azure AD Connect.
   2. Używasz zlokalizowanej wersji systemu Windows Server, której nazwa wbudowanej grupy administratorów na serwerze nie jest "Administratorzy".
   3. Używane są domyślne programu SQL Server 2012 Express LocalDB, zainstalowane za pomocą usługi Azure AD Connect, zamiast podawać własnego pełnego programu SQL.

**Rozwiązano problemy:**

Synchronizacja programu Azure AD Connect
* Rozwiązano problem, gdzie programu sync scheduler pomija kroku całego synchronizacji, jeśli jeden lub więcej łączników nie ma profilu uruchamiania dla tego kroku synchronizacji. Na przykład ręcznie dodać łącznik bez tworzenia importu zmian, profilu uruchamiania go za pomocą Menedżera usługi synchronizacji. Ta poprawka gwarantuje, że harmonogram synchronizacji w dalszym ciągu uruchomić importu zmian dla innych łączników.
* Rozwiązano problem, w którym usługa synchronizacji natychmiast zatrzymuje przetwarzanie profilu uruchamiania znajduje się w napotka jakiś problem z wykonywania czynności. Ta poprawka gwarantuje, że usługa synchronizacji pomija uruchamianą w kroku i kontynuuje przetwarzanie pozostałych. Na przykład masz importu zmian, uruchom profilu dla łącznika usługi AD z wielu kroków wykonywania (jednej dla każdego lokalnej domeny usługi AD). Usługa synchronizacji zostanie uruchomiona importu zmian z innymi domenami usługi AD, nawet wtedy, gdy jeden z nich ma problemy z połączeniem sieciowym.
* Rozwiązano problem, który powoduje, że aktualizacja łącznik usługi Azure AD zostać pominięte podczas automatycznego uaktualniania.
* Rozwiązano problem, który powoduje, że usługa Azure AD Connect, aby niepoprawnie ustalić, czy serwer jest kontrolerem domeny podczas instalacji, z kolei powoduje, że uaktualnienie narzędzia DirSync kończy się niepowodzeniem.
* Rozwiązano problem, który powoduje, że narzędzie DirSync uaktualnienia w miejscu nie tworzyć żadnego profilu uruchamiania dla łącznika usługi Azure AD.
* Rozwiązano problem, w którym interfejs użytkownika Menedżera usługi synchronizacji przestaje odpowiadać podczas próby skonfigurowania ogólnego łącznika LDAP.

Zarządzanie usługami AD FS
* Rozwiązano problem, w której Kreator Azure AD Connect zakończy się niepowodzeniem, jeśli węzeł podstawowy usług AD FS został przeniesiony do innego serwera.

Usługa rejestracji Jednokrotnej w pulpitu
* Rozwiązano problem z Kreatora programu Azure AD Connect, w którym ekran logowania nie pozwala włączyć funkcję logowania jednokrotnego na komputerze, jeśli została wybrana opcja synchronizacji haseł możliwość logowania podczas instalacji nowego.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Azure AD Connect Sync obsługuje teraz korzystanie z wirtualnego konta usługi i konta usługi zarządzanego konta usługi zarządzanego przez grupę jako jego konta usługi. Dotyczy to nowa instalacja programu Azure tylko AD Connect. Po zainstalowaniu programu Azure AD Connect:
    * Domyślnie Kreator Azure AD Connect spowoduje utworzenie konta usługi wirtualnej i wykorzystuje je jako jego konta usługi.
    * Jeśli instalujesz na kontrolerze domeny, program Azure AD Connect jest powraca do poprzednie zachowanie, w którym zostanie utworzone konto użytkownika domeny i używa go jako jego konta usługi.
    * Zachowanie domyślne można przesłonić, podając jedną z następujących czynności:
      * Konta usługi zarządzanego przez grupę
      * Zarządzane konto usługi
      * Konto użytkownika domeny
      * Konto użytkownika lokalnego
* Poprzednio po uaktualnieniu do nowej kompilacji programu Azure AD Connect zawierające łączników aktualizacji lub zmiany reguł synchronizacji, program Azure AD Connect spowoduje wyzwolenie cyklu pełną synchronizację. Teraz program Azure AD Connect selektywnie wyzwala pełny Import krok tylko w przypadku łączników o aktualizacji i pełną synchronizację tylko w przypadku łączników o zmiany reguł synchronizacji.
* Wcześniej próg usuwania eksportu ma zastosowanie tylko do eksportu, które są wyzwalane za pomocą programu sync scheduler. Funkcja jest teraz rozszerzone, aby eksporty ręcznie wyzwolony przez klienta za pomocą Menedżera usługi synchronizacji.
* Na dzierżawę usługi Azure AD jest konfiguracji usługi, która wskazuje, czy funkcji synchronizacji haseł jest włączone dla Twojej dzierżawy. Wcześniej jest łatwy w konfiguracji usługi i być niepoprawnie skonfigurowana przy użyciu usługi Azure AD Connect, jeśli masz aktywną i serwer przejściowy. Teraz program Azure AD Connect będzie próbował zachować konfigurację usługi zgodne z aktywnych tylko serwera usługi Azure AD Connect.
* Program Azure AD Connect, kreator wykrywa teraz i zwraca ostrzeżenie, jeśli lokalne usługi AD nie ma Kosz usługi AD włączone.
* Wcześniej wyeksportować limit czasu usługi Azure AD i kończy się niepowodzeniem, jeśli łączny rozmiar obiektów w zadaniu wsadowym przekroczy określony próg. Teraz Usługa synchronizacji spróbuje ponownie wysłać obiektów jest dzielona na oddzielne, mniejsze segmenty, jeśli problem występuje.
* W Windows Start Menu usunięciu aplikacji Zarządzanie kluczami usługi synchronizacji. Zarządzanie klucz szyfrowania, będą w dalszym ciągu być obsługiwane za pomocą interfejsu wiersza polecenia przy użyciu miiskmu.exe. Informacji na temat zarządzania kluczem szyfrowania, można znaleźć w artykule [porzucenie klucza szyfrowania usługi Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Wcześniej Jeśli zmienisz hasło do konta usługi Azure AD Connect sync, usługa synchronizacji nie będzie możliwe start poprawnie dopiero po przeprowadzeniu zostały porzucone klucza szyfrowania i ponownie zainicjować hasło do konta usługi Azure AD Connect sync. Teraz ten proces nie jest już wymagane.

Usługa rejestracji Jednokrotnej w pulpitu

* Kreator Azure AD Connect nie wymaga już portu 9090 były otwierane w sieci, w przypadku konfigurowania uwierzytelniania przekazywanego i logowania jednokrotnego na komputerze. Tylko port 443 jest wymagany. 

## <a name="114430"></a>1.1.443.0
Wydanie: Marca 2017 r.

**Rozwiązano problemy:**

Synchronizacja programu Azure AD Connect
* Rozwiązano problem, co powoduje, że Kreator Azure AD Connect zakończyć się niepowodzeniem, jeśli nazwa wyświetlana łącznika usługi Azure AD nie zawiera początkowej domeny onmicrosoft.com przypisane do dzierżawy usługi Azure AD.
* Rozwiązano problem powoduje błąd podczas tworzenia połączenia z bazą danych SQL, gdy hasło konta usługi synchronizacji programu zawiera znaki specjalne, takie jak apostrof, dwukropek i miejsca na kreatora Azure AD Connect.
* Rozwiązano problem, co powoduje błąd "obraz ma kotwicę, który jest inny niż obraz" występuje na serwerze programu Azure AD Connect w trybie przejściowym, po zostały tymczasowo wyłączone w lokalnym AD obiektu synchronizowanie i następnie dołączony ponownie do synchronizacji.
* Rozwiązano problem, co powoduje błąd "obiekt znajduje się przez nazwa Wyróżniająca jest Fantom" występuje na serwerze programu Azure AD Connect w trybie przejściowym, po zostały tymczasowo wyłączone w lokalnym AD obiektu synchronizowanie i następnie dołączony ponownie do synchronizacji.

Zarządzanie usługami AD FS
* Rozwiązano problem, w której Kreator Azure AD Connect nie zaktualizować konfigurację usług AD FS i ustaw odpowiednie oświadczeń na jednostki uzależnionej zaufania, po skonfigurowaniu alternatywnego Identyfikatora logowania.
* Rozwiązano problem, w których Kreator Azure AD Connect jest w stanie poprawnie obsługuje serwery usług AD FS, w których konta usług są skonfigurowane przy użyciu formatu userPrincipalName zamiast formacie sAMAccountName.

Uwierzytelnianie przekazywane
* Rozwiązano problem, co powoduje, że Kreator Azure AD Connect zakończyć się niepowodzeniem, jeśli wybrano opcję przekazania za pomocą uwierzytelniania, ale rejestracja jego łącznika nie powiedzie się.
* Rozwiązano problem, co powoduje, że Kreator Azure AD Connect, aby pominąć weryfikacji sprawdza, czy wybrany, gdy jest włączona funkcja logowania jednokrotnego na komputerze metody logowania.

Resetowanie hasła
* Rozwiązano problem, który może spowodować, że serwer Azure AAD Connect nie próbować połączyć się ponownie, jeśli połączenie został skasowany przez zapory lub serwera proxy.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Polecenie cmdlet Get-ADSyncScheduler teraz zwraca nową właściwość typu Boolean o nazwie SyncCycleInProgress. Zwrócona wartość to true, oznacza, że istnieje cykl zaplanowanej synchronizacji w toku.
* Folder docelowy, przechowywania, dzienniki konfiguracji i instalacji programu Azure AD Connect została przeniesiona z %localappdata%\AADConnect do %programdata%\AADConnect poprawić dostępność w plikach dziennika.

Zarządzanie usługami AD FS
* Dodano obsługę aktualizacji certyfikat SSL farmy usług AD FS.
* Dodano obsługę zarządzania usługą AD FS 2016.
* Teraz można określić istniejący gMSA (konto usługi zarządzane przez grupę), podczas instalacji usług AD FS.
* Teraz można skonfigurować algorytmu SHA-256 jako algorytm wyznaczania wartości skrótu podpisu dla zaufania jednostki uzależnionej usługi Azure AD.

Resetowanie hasła
* Wprowadzono ulepszenia, aby umożliwić produktu do funkcji w środowiskach z bardziej rygorystyczne reguły zapory.
* Niezawodność połączenia ulepszona do usługi Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Wydanie: Grudnia 2016 r.

**Rozwiązano problem:**

* Rozwiązano problem, w których reguły oświadczenie issuerid dla usługi Active Directory Federation Services (AD FS) brakuje w tej kompilacji.

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect automatyczne uaktualnianie.

## <a name="113710"></a>1.1.371.0
Wydanie: Grudnia 2016 r.

**Znany problem:**

* Reguła oświadczenia issuerid dla usług AD FS jest brak w tej kompilacji. Reguła oświadczenia issuerid jest wymagany, jeśli są federowanie wielu domen w usłudze Azure Active Directory (Azure AD). Jeśli używasz usługi Azure AD Connect do zarządzania lokalnymi wdrożenia usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczenie issuerid z konfiguracji usług AD FS. Problem można obejść przez dodanie reguły oświadczenie issuerid po instalacji/uaktualnienia. Aby uzyskać szczegółowe informacje na temat dodawania issuerid reguł oświadczeń, zapoznaj się z tym artykułem na [Obsługa wielu domen do federowania w usłudze Azure AD](how-to-connect-install-multiple-domains.md).

**Rozwiązano problem:**

* Port 9090 nie jest otwarty dla połączeń wychodzących, Azure AD Connect instalacja lub uaktualnienie nie powiedzie się.

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect automatyczne uaktualnianie.

## <a name="113700"></a>1.1.370.0
Wydanie: Grudnia 2016 r.

**Znane problemy:**

* Reguła oświadczenia issuerid dla usług AD FS jest brak w tej kompilacji. Reguła oświadczenia issuerid jest wymagany, jeśli są federowanie wielu domen z usługą Azure AD. Jeśli używasz usługi Azure AD Connect do zarządzania lokalnymi wdrożenia usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczenie issuerid z konfiguracji usług AD FS. Ten problem można obejść przez dodanie reguły oświadczenie issuerid po instalacji/uaktualnienia. Aby uzyskać szczegółowe informacje na temat dodawania issuerid reguł oświadczeń, zapoznaj się z tym artykułem na [Obsługa wielu domen do federowania w usłudze Azure AD](how-to-connect-install-multiple-domains.md).
* Port 9090 należy otworzyć, ruch wychodzący do ukończenia instalacji.

**Nowe funkcje:**

* Uwierzytelnianie przekazywane (wersja zapoznawcza).

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pomocą funkcji Azure AD Connect automatyczne uaktualnianie.

## <a name="113430"></a>1.1.343.0
Wydanie: Listopada 2016 r.

**Znany problem:**

* Reguła oświadczenia issuerid dla usług AD FS jest brak w tej kompilacji. Reguła oświadczenia issuerid jest wymagany, jeśli są federowanie wielu domen z usługą Azure AD. Jeśli używasz usługi Azure AD Connect do zarządzania lokalnymi wdrożenia usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczenie issuerid z konfiguracji usług AD FS. Ten problem można obejść przez dodanie reguły oświadczenie issuerid po instalacji/uaktualnienia. Aby uzyskać szczegółowe informacje na temat dodawania issuerid reguł oświadczeń, zapoznaj się z tym artykułem na [Obsługa wielu domen do federowania w usłudze Azure AD](how-to-connect-install-multiple-domains.md).

**Rozwiązano problemy:**

* Czasami instalowanie program Azure AD Connect nie działa, ponieważ nie można utworzyć konta usługi lokalnej, którego hasło spełnia stopień złożoności określone przez zasady haseł w organizacji.
* Rozwiązano problem, w której reguły dołączania są nie ponownie oceniane podczas obiektu w przestrzeni łącznika jednocześnie staje się poza zakresem dla jednego dołączyć regułę i stają się w zakresie dla innego. Może to nastąpić, jeśli istnieje co najmniej dwóch reguł sprzężenia, których warunki sprzężenia wykluczają się wzajemnie.
* Rozwiązano problem, w której reguły synchronizacji ruchu przychodzącego (z usługi Azure AD), które nie zawierają reguły dołączania, nie są przetwarzane jeśli mają niższe wartości pierwszeństwa niż tych, które zawierają reguły dołączania.

**Ulepszenia:**

* Dodano obsługę instalowania usługi Azure AD Connect w systemie Windows Server 2016 Standard lub nowszym.
* Dodano obsługę przy użyciu programu SQL Server 2016 jako zdalnej bazy danych programu Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Wydanie: Sierpnia 2016 r.

**Rozwiązano problemy:**

* Zmiany można zsynchronizować interwał nie nastąpić aż po zakończeniu następnego cyklu synchronizacji.
* Kreator Azure AD Connect nie akceptuje konto usługi Azure AD, którego nazwa użytkownika, który rozpoczyna się od znaku podkreślenia (\_).
* Kreator Azure AD Connect nie może uwierzytelnić konta usługi Azure AD, jeśli hasło konta zawiera zbyt wiele znaków specjalnych. Komunikat o błędzie "nie można zweryfikować poświadczeń. Wystąpił nieoczekiwany błąd." jest zwracany.
* Odinstalowywanie serwera przejściowego powoduje wyłączenie synchronizacji haseł w dzierżawie usługi Azure AD i powoduje, że synchronizacja haseł zakończona niepowodzeniem z aktywnego serwera.
* Synchronizacja haseł nie powiedzie się w przypadku rzadko po nie skrót hasła użytkownika w udziale.
* Po włączeniu trybu przejściowego serwer usługi Azure AD Connect funkcji zapisywania zwrotnego haseł nie jest tymczasowo wyłączone.
* Kreator Azure AD Connect nie są wyświetlane synchronizacja haseł rzeczywiste i konfiguracji funkcji zapisywania zwrotnego haseł, gdy serwer jest w trybie przejściowym. Zawsze pokazuje je jako wyłączone.
* Zmiany w konfiguracji synchronizacji haseł i zapisywania zwrotnego haseł nie są zachowywane przez kreatora Azure AD Connect, jeśli serwer jest w trybie przejściowym.

**Ulepszenia:**

* Zaktualizowano polecenia cmdlet Start-ADSyncSyncCycle, aby wskazać, czy można pomyślnie uruchomić nowego cyklu synchronizacji lub nie.
* Dodano polecenie cmdlet Stop-ADSyncSyncCycle, aby zakończyć cykl synchronizacji i działania, które są obecnie w toku.
* Zaktualizowano polecenie cmdlet Stop-ADSyncScheduler, aby zakończyć cykl synchronizacji i działania, które są obecnie w toku.
* Podczas konfigurowania [rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) w kreatorze program Azure AD Connect, teraz można wybrać atrybut usługi Azure AD typu "Teletex string".

## <a name="111890"></a>1.1.189.0
Wydanie: Czerwca 2016 r.

**Rozwiązano problemy i ulepszenia:**

* Teraz można zainstalować program Azure AD Connect na serwerze zgodne ze standardem FIPS.
  * Aby synchronizować hasła, zobacz [synchronizacji skrótów haseł i ze standardem FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Rozwiązano problem, w których nazwa NetBIOS nie można rozpoznać nazwę FQDN w łącznika usługi Active Directory.

## <a name="111800"></a>1.1.180.0
Wydanie: Maja 2016 r.

**Nowe funkcje:**

* Ostrzeżenie i pomaga zweryfikować domen, jeśli nie przed programem Azure AD Connect.
* Dodano obsługę [Microsoft Cloud w Niemczech](reference-connect-instances.md#microsoft-cloud-germany).
* Dodano obsługę najnowsze [chmury Microsoft Azure dla instytucji rządowych](reference-connect-instances.md#microsoft-azure-government) infrastruktury za pomocą nowego wymagania dotyczące adresu URL.

**Rozwiązano problemy i ulepszenia:**

* Dodać filtrowanie do Edytor reguł synchronizacji ułatwia znajdowanie reguły synchronizacji.
* Zwiększono wydajność podczas usuwania obszaru łącznika.
* Rozwiązano problem, gdy ten sam obiekt został usunięty i dodany w tym samym uruchomieniu (o nazwie usuwania/Dodaj).
* Wyłączone synchronizowanie reguły nie jest już ponownie włączy funkcje uwzględnione obiekty i Odśwież atrybuty dotyczące schematu katalogu lub uaktualniania.

## <a name="111300"></a>1.1.130.0
Wydanie: Kwiecień 2016

**Nowe funkcje:**

* Dodano obsługę atrybuty wielowartościowe [rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md).
* Dodano obsługę więcej odmiany konfiguracji [automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md) wziąć pod uwagę kwalifikuje się do uaktualnienia.
* Dodano niektóre polecenia cmdlet dla [Niestandardowy harmonogram](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Wydanie: Marzec 2016

**Rozwiązano problemy:**

* Wprowadzone się, że instalacja ekspresowa nie można użyć w systemie Windows Server 2008 (sprzed wersji R2), ponieważ synchronizacja haseł nie jest obsługiwana w tym systemie operacyjnym.
* Uaktualnianie z narzędzia DirSync z konfiguracją niestandardowego filtru nie działa zgodnie z oczekiwaniami.
* Podczas uaktualniania do nowszych wersji i nie ma zmian w konfiguracji, pełny import/synchronizacji nie powinna zostać zaplanowana.

## <a name="111100"></a>1.1.110.0
Wydanie: Luty 2016

**Rozwiązano problemy:**

* Uaktualnienia z wcześniejszych wersji nie działa, jeśli instalacja nie jest w folderze C:\Program Files domyślnym.
* Po zainstalowaniu i wyczyść **proces synchronizacji rozpocznie się** na końcu kreatora instalacji, działania Kreatora instalacji po raz drugi nie włączy harmonogramu.
* Harmonogram nie działa zgodnie z oczekiwaniami na serwerach, na którym nie jest używany format daty/godziny US en. Spowoduje również zablokowanie `Get-ADSyncScheduler` zwracać prawidłowy czas.
* Po zainstalowaniu starszej wersji programu Azure AD Connect z usługami AD FS jako opcji logowania i uaktualniania, nie można ponownie uruchomić Kreatora instalacji.

## <a name="111050"></a>1.1.105.0
Wydanie: Luty 2016

**Nowe funkcje:**

* [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md) funkcji Express ustawienia klientów.
* Pomoc techniczna dla administratora globalnego przy użyciu usługi Azure Multi-Factor Authentication i Privileged Identity Management w Kreatorze instalacji.
  * Musisz zezwolić na użycie na serwerze proxy również zezwolić na ruch https://secure.aadcdn.microsoftonline-p.com Jeśli uwierzytelnianie wieloskładnikowe.
  * Musisz dodać https://secure.aadcdn.microsoftonline-p.com do listy zaufanych witryn, uwierzytelnianie wieloskładnikowe działać prawidłowo.
* Zezwalaj na zmienianie metodę logowania użytkownika po wstępnej instalacji.
* Zezwalaj na [domenę i jednostkę Organizacyjną filtrowanie](how-to-connect-install-custom.md#domain-and-ou-filtering) w Kreatorze instalacji. Dzięki temu, nawiązywania połączenia z lasów, w którym nie wszystkie domeny są dostępne.
* [Harmonogram](how-to-connect-sync-feature-scheduler.md) jest wbudowana do aparatu synchronizacji.

**Funkcje promowane z wersji preview do wersji ogólnie dostępnej:**

* [Zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md).
* [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md).

**Nowe funkcje w wersji zapoznawczej:**

* Nowe rozwiązanie domyślne są synchronizowane cyklu interwał wynosi 30 minut. Używane do trzech godzin dla wszystkich wcześniejszych wersji. Dodaje obsługę, aby zmienić [harmonogramu](how-to-connect-sync-feature-scheduler.md) zachowanie.

**Rozwiązano problemy:**

* Sprawdź, czy strona domen DNS zawsze nie można rozpoznać domeny.
* Monity o podanie poświadczeń administratora domeny, podczas konfigurowania usług AD FS.
* Lokalne konta usługi AD nie są rozpoznawane przez Kreatora instalacji, jeśli znajduje się w domenie z innego drzewa DNS niż domeny katalogu głównego.

## <a name="1091310"></a>1.0.9131.0
Wydanie: Grudnia 2015 r.

**Rozwiązano problemy:**

* Synchronizacja haseł może nie działać po zmianie hasła w Active Directory Domain Services (AD DS), ale działa po ustawieniu hasła.
* Jeśli masz serwer proxy, może się nie powieść uwierzytelniania usługi Azure AD podczas instalacji, czy uaktualnienie zostało anulowane na stronie konfiguracji.
* Aktualizowanie z poprzedniej wersji programu Azure AD Connect przy użyciu pełnego wystąpienia programu SQL Server kończy się niepowodzeniem, jeśli nie jesteś administratorem systemu (SA) programu SQL Server.
* Aktualizowanie z poprzedniej wersji programu Azure AD Connect przy użyciu zdalnego programu SQL Server pokazuje błąd "Nie można uzyskać dostęp ADSync SQL bazy danych".

## <a name="1091250"></a>1.0.9125.0
Wydanie: Listopad 2015

**Nowe funkcje:**

* Można ponownie skonfigurować usług AD FS do zaufania usługi Azure AD.
* Można odświeżyć schematu usługi Active Directory, a następnie ponownie wygenerować reguły synchronizacji.
* Można wyłączyć reguły synchronizacji.
* Można zdefiniować "AuthoritativeNull" jako literał nowe reguły synchronizacji.

**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health do celów synchronizacji](how-to-connect-health-sync.md).
* Obsługa [usług domenowych Azure AD](../user-help/active-directory-passwords-update-your-own-password.md) synchronizacji haseł.

**Nowy scenariusz obsługiwane:**

* Obsługuje wiele organizacji programu Exchange w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [hybrydowych wdrożeń z wieloma lasami usługi Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Rozwiązano problemy:**

* Problemy z synchronizacją hasła:
  * Obiekt, który został przeniesiony z poza zakresem w zakres nie będzie jej synchronizowanymi hasłami. Obejmuje to jednostki Organizacyjnej i filtrowanie atrybutów.
  * Wybranie nowej jednostki Organizacyjnej do uwzględnienia w synchronizacji nie wymaga synchronizacji pełnej hasła.
  * Po włączeniu wyłączony użytkownik nie synchronizuje hasła.
  * Kolejka ponownych prób hasła jest nieskończona i poprzedniej limit 5000 obiektów, które wkrótce zostaną wycofane został usunięty.
* Nie można nawiązać połączenia z usługi Active Directory z poziomem funkcjonalności lasu systemu Windows Server 2016.
* Nie można zmienić grupy, która jest używana do filtrowania grupy po początkowej instalacji.
* Już nie tworzy nowy profil użytkownika na serwerze usługi Azure AD Connect dla każdego użytkownika podczas zmiany hasła z zapisywaniem zwrotnym haseł włączone.
* Liczba całkowita typu Long, nie można używać wartości zakresów reguły synchronizacji.
* Pole wyboru "Zapisywanie zwrotne urządzeń" pozostanie wyłączony, jeśli ma kontrolerów domeny jest nieosiągalny.

## <a name="1086670"></a>1.0.8667.0
Wydanie: Sierpień 2015

**Nowe funkcje:**

* Kreatora instalacji usługi Azure AD Connect jest teraz zlokalizowane dla wszystkich języków systemu Windows Server.
* Dodano obsługę dla konta odblokować za pomocą zarządzania hasłami w usłudze Azure AD.

**Rozwiązano problemy:**

* Kreator instalacji programu Azure AD Connect ulega awarii, jeśli inny użytkownik będzie nadal występował, instalacji, a nie osoba, która pierwszego uruchomienia instalacji.
* Jeśli poprzednie dezinstalacji programu Azure AD Connect nie czysta synchronizacji programu Azure AD Connect, nie jest możliwe ponowne zainstalowanie.
* Nie można zainstalować program Azure AD Connect przy użyciu instalacji ekspresowej, jeśli użytkownik nie jest w domenie głównej lasu lub innej niż angielska wersja usługi Active Directory jest używana.
* Jeśli nie można rozpoznać nazwę FQDN konta użytkownika usługi Active Directory, wyświetlany jest mylący komunikat o błędzie "Nie można zatwierdzić schemat".
* Konto używane na łącznika usługi Active Directory jest zmieniany poza kreatora, Kreator nie powiedzie się w kolejnych uruchomień.
* Program Azure AD Connect jest czasami nie można zainstalować na kontrolerze domeny.
* Nie można włączyć i wyłączyć "Tryb przejściowy", jeśli zostały dodane atrybuty rozszerzenia.
* Zapisywanie zwrotne haseł nie powiedzie się w niektórych konfiguracjach z powodu nieprawidłowego hasła na łącznika usługi Active Directory.
* Nie można uaktualnić narzędzia DirSync, jeśli nazwa wyróżniająca (DN) jest używany w filtrowanie atrybutów.
* Nadmiernego użycia procesora CPU podczas korzystania z resetowania hasła.

**Funkcje usunięte w wersji zapoznawczej:**

* Funkcja w wersji zapoznawczej [zapis zwrotny użytkowników](how-to-connect-preview.md#user-writeback) tymczasowo usunięto w oparciu o opinie od naszych klientów w wersji zapoznawczej. Go zostanie dodany później po rozwiązaliśmy podana opinii.

## <a name="1086410"></a>1.0.8641.0
Wydanie: Czerwiec 2015

**Początkowa wersja programu Azure AD Connect.**

Zmieniono nazwę z usługi Azure AD Sync do programu Azure AD Connect.

**Nowe funkcje:**

* [Ustawienia ekspresowe](how-to-connect-install-express.md) instalacji
* Można [konfiguracji usług AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Można [uaktualnienie z narzędzia DirSync](how-to-dirsync-upgrade-get-started.md)
* [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Wprowadzono [Tryb przejściowy](how-to-connect-sync-staging-server.md)

**Nowe funkcje w wersji zapoznawczej:**

* [Zapis zwrotny użytkowników](how-to-connect-preview.md#user-writeback)
* [Zapisywanie zwrotne grup](how-to-connect-preview.md#group-writeback)
* [Zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md)
* [Rozszerzenia katalogów](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Wydanie: Maja 2015 r.

**Nowe wymaganie:**

* Synchronizacja programu Azure AD wymaga teraz .NET Framework w wersji 4.5.1 do zainstalowania.

**Rozwiązano problemy:**

* Zapisywanie zwrotne haseł z usługi Azure AD kończy się niepowodzeniem z powodu błędu połączenia usługi Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Wydanie: Kwietnia 2015 r.

**Rozwiązano problemy i ulepszenia:**

* Łącznik usługi Active Directory nie przetwarzać usuwa poprawnie, jeśli włączono Kosza wiąże się z wielu domen w lesie.
* Dla platformy Azure łącznika usługi Active Directory została ulepszona wydajność operacji importowania.
* Gdy grupa przekroczyła limit członkostwa (domyślnie limit jest ustawiony do 50 000 obiektów), grupa została usunięta w usłudze Azure Active Directory. Nowe zachowanie grupie nie zostanie usunięta, zostanie zgłoszony błąd i nowych zmian członkostwa nie są eksportowane.
* Nowy obiekt nie może zostać zainicjowany, jeśli przygotowanych usunięcia z tej samej nazwy domeny już znajduje się w przestrzeni łącznika.
* Niektóre obiekty są oznaczone do synchronizacji podczas synchronizacji różnicowej mimo, że nastąpiła żadna zmiana przygotowane do obiektu.
* Wymuszanie synchronizacji haseł powoduje również usunięcie preferowanych listy kontrolerów domeny.
* CSExportAnalyzer ma problemy z niektórych stanów obiektów.

**Nowe funkcje:**

* Typ obiektu "ANY" w MV teraz nawiązać sprzężenia.

## <a name="104850222"></a>1.0.485.0222
Wydanie: Luty 2015

**Ulepszenia:**

* Ulepszone importowanie wydajność.

**Rozwiązano problemy:**

* Synchronizacja haseł honoruje atrybutu cloudFiltered, który jest używany przez filtrowanie atrybutów. Obiekty filtrowane nie są już w zakresie do synchronizacji haseł.
* W rzadkich sytuacjach, gdy topologii miały wiele kontrolerów domen synchronizacja haseł nie działa.
* Włączono "Zatrzymane server" podczas importowania z łącznika usługi Azure AD po zarządzania urządzeniami w usłudze Azure AD/usługi Intune.
* Łączenie obcych podmiotów zabezpieczeń (FSP) z wielu domen w tym samym lesie powoduje błąd niejednoznaczne sprzężenia.

## <a name="104751202"></a>1.0.475.1202
Wydanie: Grudzień 2014 roku

**Nowe funkcje:**

* Synchronizacja haseł za pomocą filtrowania na podstawie atrybutu jest teraz obsługiwane. Aby uzyskać więcej informacji, zobacz [synchronizacji haseł z filtrowaniem](how-to-connect-sync-configure-filtering.md).
* Ten atrybut ms-DS-ExternalDirectoryObjectID jest zapisywane z powrotem do usługi Active Directory. Ta funkcja dodaje obsługę aplikacji usługi Office 365. Dostęp Online i lokalnych skrzynek pocztowych w wdrożenia hybrydowego programu Exchange do używa protokołu OAuth2.

**Rozwiązano problemy uaktualnienia:**

* Asystent logowania w nowszej wersji jest dostępna na serwerze.
* Ścieżka instalacji niestandardowej zostało użyte do zainstalowania Azure AD Sync.
* Nieprawidłowy niestandardowy sprzężenia kryterium blokuje uaktualnienia.

**Inne poprawki:**

* Naprawiono szablonów dla pakietu Office Pro Plus.
* Problemy z instalacją stały spowodowane nazwy użytkowników, które zaczyna się kreska.
* Naprawiono utraty ustawienie sourceAnchor podczas uruchamiania Kreatora instalacji po raz drugi.
* Naprawiono śledzenia zdarzeń systemu Windows do synchronizacji haseł.

## <a name="104701023"></a>1.0.470.1023
Wydanie: Października 2014 r.

**Nowe funkcje:**

* Synchronizacja haseł z wielu lokalnej usługi Active Directory do usługi Azure AD.
* Zlokalizowany interfejs użytkownika instalacji, wszystkie języki systemu Windows Server.

**Uaktualnienie z wersji ogólnie dostępnej aplikacji AADSync 1.0**

Jeśli masz już zainstalowany program Azure AD Sync, istnieje jeden dodatkowy krok, który należy wykonać w przypadku, gdy zmieniono dowolne reguły synchronizacji out-of-box. Po uaktualnieniu do 1.0.470.1023 wersji, synchronizacja, które są zduplikowane reguły, które zostały zmodyfikowane. Dla każdej reguły synchronizacji zmodyfikowane wykonaj następujące czynności:

1. Znajdź reguły synchronizacji zostały zmodyfikowane i Zapisz zmiany.
1. Usuń regułę synchronizacji.
1. Znajdź nowe reguły synchronizacji, który jest tworzony przez usługę Azure AD Sync, a następnie ponownie zastosuj zmiany.

**Uprawnienia dla konta usługi Active Directory**

Konto usługi Active Directory musi mieć dodatkowe uprawnienia, aby można było odczytać wartości skrótów haseł z usługi Active Directory. Uprawnień udzielanych są nazywane "Replikowanie zmiany w katalogu" i "Directory replikacji zmian wszystkich". Zarówno uprawnienia są wymagane, aby można było odczytać wartości skrótów haseł.

## <a name="104190911"></a>1.0.419.0911
Wydanie: Wrzesień 2014

**Początkowa wersja programu Azure AD Sync.**

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
