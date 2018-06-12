---
title: 'Azure AD Connect: Historia wersji | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera listę wszystkich wersji programu Azure AD Connect i Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c2a830fa873f7277440d1baa9049a9c8eafcbf55
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261784"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Historia wersji
Zespół usługi Azure Active Directory (Azure AD) regularnie aktualizuje Azure AD Connect z nowych funkcji. Nie wszystkie dodatki mają zastosowanie do wszystkich grup odbiorców.


W tym artykule jest przeznaczona do można korzystać ze śledzenia wersji, które zostały wydane i zrozumieć, jakie zmiany są w najnowszej wersji.

Ta tabela znajduje się lista Tematy pokrewne:

Temat |  Szczegóły
--------- | --------- |
Kroki do uaktualnienia programu Azure AD Connect | Różnych metod na [uaktualnianie z poprzedniej wersji do najnowszej wersji](active-directory-aadconnect-upgrade-previous-version.md) wersji Azure AD Connect.
Wymagane uprawnienia | Uprawnienia wymagane do zastosowania aktualizacji, zobacz [konta i uprawnienia](./active-directory-aadconnect-accounts-permissions.md#upgrade).

Pobierz | [Pobieranie programu Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Stan zlecenia

2018-5/14: wydane dla automatycznego uaktualnienia i pobierania.

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

Nowe funkcje i ulepszenia

- Ta wersja zawiera publicznej wersji zapoznawczej integracji PingFederate w programie Azure AD Connect. W tej wersji klienci mogą łatwo i niezawodny sposób konfigurowania ich środowiska usługi Azure Active Directory wykorzystać PingFederate jako ich dostawcy federacyjnego. Aby dowiedzieć się więcej na temat sposobu korzystania z tej nowej funkcji, przejdź na stronę naszych [dokumentacji online](active-directory-aadconnect-user-signin.md#federation-with-pingfederate). 
- Zaktualizowano Azure AD Connect kreatora Rozwiązywanie problemów z narzędziu, którym teraz analizuje więcej scenariusz błąd, takich jak połączonego skrzynki pocztowe i grupami dynamicznymi AD. Dowiedz się więcej o narzędziu rozwiązywania problemów [tutaj](active-directory-aadconnect-troubleshoot-objectsync.md).
- Konfiguracja funkcji zapisywania zwrotnego urządzeń będzie teraz zarządzana wyłącznie w obrębie Azure AD Connect kreatora.
- Nowy moduł programu PowerShell o nazwie ADSyncTools.psm1 zostanie dodany, który może służyć do rozwiązywania problemów z łącznością SQL i różnych innych narzędzi rozwiązywania problemów. Dowiedz się więcej o ADSyncTools module [tutaj](active-directory-aadconnect-tshoot-sql-connectivity.md). 
- Dodano nowe zadanie dodatkowe "Konfiguruj opcje urządzenia". Zadania umożliwiają skonfigurowanie dwie następujące czynności: 
    -   **Hybrydowe usługi Azure AD join**: Jeśli środowiska pod kątem lokalnej wpływ AD a również skorzystać z możliwości oferowane przez usługi Azure Active Directory, można zaimplementować hybrydowe usługi Azure AD przyłączone do urządzeń. Są to urządzenia, które są zarówno do lokalnej usługi Active Directory i Azure Active Directory.
    -   **Zapisywanie zwrotne urządzeń**: zapisywanie zwrotne urządzeń służy do włączania dostępu warunkowego, w oparciu o urządzenia do usług AD FS (2012 R2 lub nowszej) chronione urządzeń

   >[!NOTE] 
   > - Opcję włączania zapisu zwrotnego urządzeń Dostosuj opcje synchronizacji będzie nieaktywna. 
   > -  Moduł programu PowerShell dla programu ADPrep jest przestarzałe w tej wersji.



### <a name="fixed-issues"></a>Rozwiązane problemy 

- Ta wersja aktualizuje instalacji programu SQL Server Express do programu SQL Server 2012 z dodatkiem SP4, określający, między innymi poprawki kilka luk w zabezpieczeniach.  Zobacz [tutaj](https://support.microsoft.com/en-ca/help/4018073/sql-server-2012-service-pack-4-release-information) Aby uzyskać więcej informacji na temat programu SQL Server 2012 z dodatkiem SP4.
- Przetwarzanie reguł synchronizacji: reguły synchronizacji ruchu wychodzącego sprzężenia z warunkiem nie dołączyć powinna być cofnąć zastosowane, jeśli reguły synchronizacji nadrzędny nie ma już zastosowania
- Kilka poprawek ułatwień dostępu zostały zastosowane do interfejsu użytkownika Menedżera usługi synchronizacji i Edytor reguł synchronizacji
- Azure AD kreatora Połącz: Wystąpił błąd podczas tworzenia konta łącznika usługi AD podczas programu Azure AD Connect jest w grupie roboczej
- Azure AD kreatora Connect: W usłudze Azure AD strony logowania Wyświetla pole wyboru weryfikacji, gdy istnieje niezgodność AD domen i domen usługi Azure AD zweryfikowano
- Poprawka programu PowerShell automatycznego uaktualnienia można ustawić stan uaktualnienia automatycznie prawidłowo w niektórych przypadkach po próba automatycznego uaktualnienia.
- Azure AD kreatora Połącz: Zaktualizowano telemetrię, aby przechwycić wcześniej brakujące informacje
- Azure AD kreatora Połącz: Następujące zmiany zostały wprowadzone korzystając z **zmienić logowania użytkownika** zadanie, aby przełączyć się z usługami AD FS do uwierzytelniania przekazywanego:
    - Agent uwierzytelniania przekazywanego jest zainstalowany na serwerze programu Azure AD Connect, a funkcja przekazywanego uwierzytelniania jest włączona, zanim nie możemy przekonwertować domenach z federacyjnych do zarządzanych.
    - Użytkownicy są już skonwertowane z federacyjnych do zarządzanych. Konwertowane są tylko domeny.
- Azure AD kreatora Połącz: Wyrażenia regularnego programu AD FS Multi domeny jest nieprawidłowy, gdy ma nazwę UPN użytkownika "znak specjalny wyrażeń regularnych aktualizacji do obsługi znaków specjalnych
- Azure AD kreatora Połącz: Usuń fałszywe komunikat "Konfiguruj atrybutu zakotwiczenia źródła" podczas bez zmian 
- Azure AD kreatora Połącz: Usługi AD FS obsługują scenariusza podwójną federacyjnego
- Azure AD kreatora Connect: Usługi AD FS oświadczenia nie zostaną zaktualizowane dla domeny dodane podczas konwertowania domeny zarządzanej federacyjnego
- Azure AD kreatora Połącz: Podczas wykrywania zainstalowane pakiety, znaleźliśmy starych Dirsync/usługi Azure AD Sync/usługi Azure AD Connect pokrewnych produktów. Firma Microsoft podejmie teraz próbę odinstalowania starych produktów.
- Azure AD kreatora Connect: Poprawne błąd komunikat mapowania podczas instalacji agenta uwierzytelniania przekazywanie zakończy się niepowodzeniem.
- Azure AD kreatora Połącz: Usunąć kontener "Konfiguracja" ze strony filtrowania jednostki Organizacyjnej domeny
- Zainstaluj aparat synchronizacji: Usuń niepotrzebne logiki starszej wersji, która czasami nie powiodło się z aparatu synchronizacji instalacja msi
- Azure AD kreatora Połącz: Napraw podręcznego tekst pomocy na stronie funkcje opcjonalne dla synchronizacji skrótów haseł
- Synchronizowanie aparat środowiska wykonawczego: Usuń scenariusz, w którym obiekt CS ma importowanych delete i spróbować ponownie zainicjować obsługę administracyjną obiektu reguły synchronizacji.
- Synchronizowanie aparat środowiska wykonawczego: Dodaj pomocy link dla łączności dotyczące rozwiązywania problemów w przewodniku do dziennika zdarzeń wystąpił błąd importu
- Synchronizowanie aparat środowiska wykonawczego: zmniejszyć użycie pamięci harmonogramu synchronizacji podczas wyliczania łączników
- Azure AD kreatora Połącz: Rozwiązania problemu rozpoznawania niestandardowe konto usługi synchronizacji z brakiem uprawnień odczytu AD
- Azure AD kreatora Connect: Rejestrowanie domenę i jednostkę Organizacyjną opcje filtrowania poprawy
- Azure AD kreatora Połącz: Dodaj usługi AD FS domyślne oświadczenia do relacji zaufania federacji, utworzony w scenariuszu dla usługi MFA
- Azure AD kreatora Połącz: Usługi AD FS wdrażanie WAP: Dodawanie awarii serwera do użycia nowego certyfikatu
- Azure AD kreatora Połącz: DSSO wyjątku, gdy onPremCredentials nie jest zainicjowana dla domeny 
- Przepływ preferencyjnie atrybut distinguishedName AD z obiektu aktywnego użytkownika.
- Usunięte usterki bardzo drobny zostały pierwszeństwo reguły synchronizacji pierwszego OOB ustawiono 99 zamiast 100



## <a name="117510"></a>1.1.751.0
Stan 4/12/2018: wydane dla pobierania tylko

>[!NOTE]
>Ta wersja jest poprawki programu Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Synchronizacja programu Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
Rozwiązany problem zostały odnajdywanie automatyczne wystąpienia platformy Azure dla czasami sprawdzano Chin dzierżaw.  

### <a name="ad-fs-management"></a>Zarządzanie usługami AD FS
#### <a name="fixed-issues"></a>Rozwiązane problemy

Wystąpił problem w Logika ponawiania konfiguracji, który spowoduje ArgumentException, podając "element z tym samym kluczem już został dodany."  To spowoduje, że wszystkie operacje ponawiania się niepowodzeniem.

## <a name="117500"></a>1.1.750.0
Stan 2018-3/22: wydane dla automatycznego uaktualnienia i pobierania.
>[!NOTE]
>Po zakończeniu uaktualniania do tej nowej wersji automatycznie spowodują uruchomienie pełnej synchronizacji i pełny import dla łącznika usługi Azure AD i pełnej synchronizacji dla łącznika usługi AD. Ponieważ to może zająć trochę czasu, w zależności od wielkości środowiska Azure AD Connect upewnij się, czy wykonano czynności niezbędnych do obsługi to lub Wstrzymaj się na temat uaktualniania, aby znaleźć dogodnym momencie Aby to zrobić.

>[!NOTE]
>"Funkcji AutoUpgrade niepoprawnie zostało wyłączone dla niektórych dzierżawców, którzy później niż 1.1.524.0 wdrożone kompilacji. Aby upewnić się, że Twoje wystąpienie usługi Azure AD Connect jest nadal uprawnia do skorzystania z AutoUpgrade, uruchom następujące polecenie cmdlet programu PowerShell: "Set ADSyncAutoUpgrade - AutoupGradeState Enabled"


### <a name="azure-ad-connect"></a>Program Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy

* Polecenia cmdlet Set-ADSyncAutoUpgrade wcześniej uniemożliwiają Autoupgrade, jeśli ustawiono stan automatycznej aktualizacji na zawieszone. Ta funkcja zostanie zmieniona, więc nie blokuje AutoUpgrade kompilacji w przyszłości.
* Zmienione **logowania użytkownika** strony opcji "Synchronizację haseł" do "Synchronizacji skrótu hasła".  Azure AD Connect synchronizuje skrótów haseł, nie hasła, tak że to rzeczywiście występuje wyrównane.  Aby uzyskać więcej informacji, zobacz [Implementowanie synchronizacji skrótów haseł z synchronizacji Azure AD Connect](active-directory-aadconnectsync-implement-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Stan: Wydane do wybranych klientów

>[!NOTE]
>Po zakończeniu uaktualniania do tej nowej wersji automatycznie spowodują uruchomienie pełnej synchronizacji i pełny import dla łącznika usługi Azure AD i pełnej synchronizacji dla łącznika usługi AD. Ponieważ może to potrwać pewien czas w zależności od wielkości środowiska Azure AD Connect upewnij się, że wykonaniu czynności niezbędnych do obsługi to lub Wstrzymaj się na temat uaktualniania, aby znaleźć dogodnym momencie w tym celu.

### <a name="azure-ad-connect"></a>Program Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Usuń okna czasowego na zadania w tle dla strony filtrowania partycji podczas przełączania do następnej strony.

* Stałe błędu, który spowodował naruszenie zasad dostępu podczas ConfigDB akcji niestandardowej.

* Stałe błędów pozwoli na odzyskanie limit czasu połączenia SQL.

* Stałe usterki, w których certyfikatów z symbolami wieloznacznymi sieci SAN nie powiodło się sprawdzanie wymagań wstępnych.

* Stałe błędów, co powoduje, że miiserver.exe do awarii podczas eksportowania łącznika usługi Azure AD.

* Stała usterki rejestrowane próba nieprawidłowe hasło, które na kontrolerze domeny, podczas uruchamiania kreatora Azure AD Connect, aby zmienić konfigurację.


#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Dodawanie ustawienia prywatności rozporządzenia ochrony danych ogólne (GDPR).  Aby uzyskać więcej informacji, zobacz artykuł [tutaj](active-directory-aadconnect-gdpr.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* dane telemetryczne aplikacji - administratora można przełączać tej klasy danych wł. / wył w momencie

* Azure AD kondycji dane — administrator musi odwiedź portal kondycji do kontrolowania ustawień kondycji.
   Po zmianie zasad usługi agentów odczytu i wymuszania go.

* Dodane urządzenie zapisu czynności konfiguracyjnych i pasek postępu inicjowania strony

* Ulepszone ogólne diagnostyki za pomocą raportu w formacie HTML i zbierania danych pełnego tekstu ZIP / raport HTML

* Poprawia niezawodność automatycznego uaktualnienia i dodany dodatkowe telemetrię, aby upewnić się, że można ustalić kondycję serwera

* Ogranicz uprawnienia dostępne do kont uprzywilejowanych na koncie w łączniku AD

  * W przypadku nowych instalacji Kreatora ogranicza uprawnienia, które konta uprzywilejowane ma konta MSOL po utworzeniu konta MSOL.

Zmiany zajmie się poniżej:
1. Instalacji ekspresowej
2. Instalacje niestandardowe automatyczne tworzenie konta
3. Zmieniony Instalator, więc nie wymaga uprawnień administratora systemu w czystej instalacji programu Azure AD Connect

* Dodano nowe narzędzia do rozwiązywania problemów z synchronizacją dla konkretnego obiektu. Jest on dostępny w obszarze opcji "Rozwiązywanie problemów z synchronizacji obiektu" Azure AD Connect kreatora Rozwiązywanie problemów z dodatkowe zadania. Obecnie to narzędzie sprawdza, czy:

  * UserPrincipalName niezgodność między obiektów synchronizowanych użytkowników i konta użytkowników w dzierżawie programu Azure AD.
  * Jeśli obiekt jest filtrowana z synchronizacji z powodu filtrowania domeny
  * Jeśli obiekt jest filtrowana z synchronizacji z powodu jednostki organizacyjnej (OU) filtrowania

* Dodano nowe narzędzia do synchronizacji bieżącego skrót hasła przechowywane w lokalnej usłudze Active Directory dla określonego konta użytkownika.

Narzędzie nie wymaga zmiany hasła. Jest on dostępny w obszarze opcji "Rozwiązywanie problemów z synchronizacji haseł w wyznaczania wartości skrótu" Azure AD Connect kreatora Rozwiązywanie problemów z dodatkowe zadania.






## <a name="116540"></a>1.1.654.0
Stan:, 12 grudnia 2017

>[!NOTE]
>Ta wersja jest zabezpieczenie powiązanej poprawki programu Azure AD Connect

### <a name="azure-ad-connect"></a>Program Azure AD Connect
Poprawa został dodany do programu Azure AD Connect wersji 1.1.654.0 (i po) aby upewnić się, że zalecane uprawnienia zmiany opisane w sekcji [blokowania dostępu do konta usług AD DS](#lock) są automatycznie stosowane podczas usługi Azure AD Połącz tworzy konto usług AD DS. 

- Podczas konfigurowania usługi Azure AD Connect, instalowanie administratora można podać istniejące konto usług AD DS, lub pozwól automatycznie utworzyć konto usługi Azure AD Connect. Zmiany uprawnień są automatycznie stosowane do konta usług AD DS, które jest tworzona przy użyciu usługi Azure AD Connect podczas instalacji. Nie są stosowane do istniejącego konta usług AD DS dostarczonego przez administratora instalowania.
- Dla klientów, którzy korzystają ze starszej wersji programu Azure AD Connect do 1.1.654.0 (lub po) uprawnienie zmiany będą nie wstecz zastosowane do istniejących kont usługi AD DS utworzone przed uaktualnieniem. Tylko zostaną one zastosowane do nowych kont usług AD DS, utworzonych po uaktualnieniu. Dzieje się tak podczas dodawania nowych lasów usługi AD do synchronizacji usługi Azure AD.

>[!NOTE]
>Ta wersja usuwa tylko luki w zabezpieczeniach dla nowych instalacji programu Azure AD Connect, gdy konto usługi jest tworzone przez proces instalacji. W przypadku istniejących instalacji lub w przypadku, gdy samodzielnie konto należy upewnić się, tę lukę w zabezpieczeniach nie istnieje.

#### <a name="lock"></a> Zablokowanie dostępu do konta usług AD DS
Blokowanie dostępu do konta usług AD DS, implementując następujące zmiany uprawnień w lokalnej usługi AD:  

*   Wyłączyć funkcję dziedziczenia w określonym obiekcie
*   Usuń wszystkie wpisy kontroli dostępu dla określonego obiektu, z wyjątkiem ACE określonych do siebie. Chcemy zachować uprawnienia domyślne po przejściu do siebie.
*   Przypisz te określone uprawnienia:

Typ     | Name (Nazwa)                          | Dostęp               | Dotyczy
---------|-------------------------------|----------------------|--------------|
Zezwalaj    | SYSTEM                        | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy przedsiębiorstwa             | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy domeny                 | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Administratorzy                | Pełna kontrola         | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Wyświetlanie zawartości        | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Odczyt wszystkich właściwości  | Ten obiekt  |
Zezwalaj    | Kontrolery domeny przedsiębiorstwa | Uprawnienia do odczytu     | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Wyświetlanie zawartości        | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Odczyt wszystkich właściwości  | Ten obiekt  |
Zezwalaj    | Uwierzytelnieni użytkownicy           | Uprawnienia do odczytu     | Ten obiekt  |

Aby zwiększyć ustawienia dla konta usługi AD DS, należy uruchomić [ten skrypt programu PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). Skrypt programu PowerShell przypisze uprawnienia wymienionych powyżej konta usług AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Skrypt programu PowerShell, aby zwiększyć istniejącego konta usługi

Aby użyć skryptu programu PowerShell, aby zastosować te ustawienia do istniejącego konta usług AD DS, (ether udostępniane przez organizację lub utworzone przez wcześniejszą instalację programu Azure AD Connect, Pobierz skrypt z linku podanego powyżej.

##### <a name="usage"></a>Użycie:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Gdzie 

**$ObjectDN** = konto usługi Active Directory, w której uprawnienia należy zwiększyć.

**$Credential** = poświadczeniami administratora, które ma niezbędne uprawnienia, aby ograniczyć uprawnienia do konta $ObjectDN. Te uprawnienia są zwykle przechowywane przez administratora przedsiębiorstwa lub domeny. Użyj w pełni kwalifikowaną nazwę konta administratora, aby uniknąć błędów wyszukiwania konta. Przykład: contoso.com\admin.

>[!NOTE] 
>$credential. Nazwa użytkownika powinna mieć format FQDN\username. Przykład: contoso.com\admin 

##### <a name="example"></a>Przykład:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Ta luka w zabezpieczeniach zostało użyte do uzyskania nieautoryzowanego dostępu?

Aby zobaczyć, czy tę lukę w zabezpieczeniach został użyty do naruszenia bezpieczeństwa usługi Azure AD Connect konfiguracji należy sprawdzić hasło ostatnio zresetować daty konta usługi.  Jeśli sygnaturą czasową w nieoczekiwany, dalszych badań, za pomocą dziennika zdarzeń dla zdarzenia resetowania tego hasła, należy podjąć.

Aby uzyskać więcej informacji, zobacz [4056318 poradnik zabezpieczeń firmy Microsoft](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Stanu: 2017 27 października

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji Azure AD Connect automatycznego uaktualnienia.

### <a name="azure-ad-connect"></a>Program Azure AD Connect
#### <a name="fixed-issue"></a>Rozwiązany problem
* Rozwiązany problem ze zgodnością wersji między Azure AD Connect i Azure AD Connect Agent kondycji (na potrzeby synchronizacji). Ten problem ma wpływ na klientów, którzy są Wykonywanie uaktualnienia w miejscu Azure AD Connect do wersji 1.1.647.0, ale obecna wersja 3.0.127.0 agenta kondycji. Po uaktualnieniu agenta programu Health nie może wysyłać dane kondycji dotyczące usługi Azure AD Connect usługi synchronizacji z usługą Azure AD Health. Z tej poprawki wersja 3.0.129.0 agenta kondycji jest instalowany podczas uaktualnienia w miejscu Azure AD Connect. Agent kondycji wersji 3.0.129.0 nie ma problem ze zgodnością z programem Azure AD Connect wersji 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Stanu: 2017 19 października

> [!IMPORTANT]
> Istnieje znany problem ze zgodnością między Azure AD Connect 1.1.647.0 i wersja agenta Azure AD Connect Health (dla synchronizacji) 3.0.127.0. Ten problem uniemożliwia wysyłanie danych kondycji o Azure AD Connect usługi synchronizacji (w tym błędów synchronizacji obiektów i Historia uruchomień danych) do usługi Azure AD Health Agent kondycji. Przed uaktualnieniem ręcznie wdrożenia usługi Azure AD Connect do wersji 1.1.647.0 Sprawdź bieżącą wersję agenta Azure AD Connect Health zainstalowanego na serwerze programu Azure AD Connect. Możesz to zrobić, przechodząc do *Panelu sterowania → aplecie Dodaj Usuń programy* i Wyszukaj aplikację *Microsoft Azure agenta AD Connect Health do celów synchronizacji*. Jeśli jego wersja jest 3.0.127.0, zalecane jest, zaczekaj na następną wersję Azure AD Connect ma być dostępny przed uaktualnieniem. Jeśli wersja kondycji agenta nie jest 3.0.127.0, jest poprawnie kontynuować uaktualnianie ręczne, w miejscu. Należy pamiętać, że ten problem nie wpływa na uaktualnienia zasięg lub klienci, którzy przeprowadzania nowej instalacji programu Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Program Azure AD Connect
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem z *zmienić logowania użytkownika* zadań w Kreatorze Azure AD Connect:

  * Problem występuje, jeśli masz istniejące wdrożenie usługi Azure AD Connect z synchronizacją haseł **włączone**, i chcesz ustawić metodę logowania użytkownika jako *uwierzytelniania przekazywanego*. Aby zmiana została zastosowana, Kreator niepoprawnie pokazuje "*wyłączyć synchronizację haseł*" wiersza. Jednak synchronizacja haseł pozostaje włączona po zastosowaniu zmiany. Z tej poprawki Kreator nie będzie wyświetlany monit.

  * Zgodnie z projektem, Kreator nie wyłączyć synchronizację haseł po zaktualizowaniu metoda logowania użytkownika za pomocą *zmienić logowania użytkownika* zadań. Pozwoli to uniknąć zakłócenia dla klientów, którzy mają być zachowane synchronizacji haseł, nawet jeśli są one włączania uwierzytelniania przekazywanego lub federacyjnego, jako jego podstawowy użytkownik logowania — metoda.
  
  * Jeśli chcesz wyłączyć synchronizację haseł po zaktualizowaniu metody logowania użytkownika, należy wykonać *dostosowanie konfiguracji synchronizacji* zadań w kreatorze. Po przejściu do *funkcje opcjonalne* strony, usuń zaznaczenie pola wyboru *synchronizacji haseł* opcji.
  
  * Należy pamiętać, że ten sam problem występuje także próbie włączenia/wyłączenia bezproblemowe logowanie jednokrotne. W szczególności masz istniejące wdrożenie usługi Azure AD Connect z włączoną synchronizacją hasła i metody logowania użytkownika jest już skonfigurowany jako *uwierzytelniania przekazywanego*. Przy użyciu *zmienić logowania użytkownika* zadania, spróbuj do wyboru/Usuń zaznaczenie pola wyboru *włączyć bezproblemowe logowanie jednokrotne* opcji, gdy metoda logowania użytkownika jest skonfigurowany jako "Uwierzytelniania przekazywanego". Aby zmiana została zastosowana, Kreator niepoprawnie pokazuje "*wyłączyć synchronizację haseł*" wiersza. Jednak synchronizacja haseł pozostaje włączona po zastosowaniu zmiany. Z tej poprawki Kreator nie będzie wyświetlany monit.

* Rozwiązano problem z *zmienić logowania użytkownika* zadań w Kreatorze Azure AD Connect:

   * Problem występuje, jeśli masz istniejące wdrożenie usługi Azure AD Connect z synchronizacją haseł **wyłączone**, i chcesz ustawić metodę logowania użytkownika jako *uwierzytelniania przekazywanego*. Po zastosowaniu zmiany Kreator włącza zarówno uwierzytelniania przekazywanego i synchronizacja haseł. Z tej poprawki Kreator włącza już synchronizacji haseł.

  * Synchronizacja haseł był wcześniej, jako warunek wstępny do włączania uwierzytelniania przekazywanego. Podczas ustawiania metody logowania użytkownika jako *uwierzytelniania przekazywanego*, Kreator będzie włączenia uwierzytelniania przekazywanego i synchronizacji haseł. Ostatnio synchronizacji haseł został usunięty jako warunek wstępny. W ramach programu Azure AD Connect wersji 1.1.557.0, zmiany do programu Azure AD Connect nie Włącz synchronizację haseł po ustawieniu metody logowania użytkownika jako *uwierzytelniania przekazywanego*. Jednak zmiany tylko została zastosowana do instalacji Azure AD Connect. W tej poprawki te same zmiany, również są stosowane do *zmienić logowania użytkownika* zadań.
  
  * Należy pamiętać, że ten sam problem występuje także próbie włączenia/wyłączenia bezproblemowe logowanie jednokrotne. W szczególności masz istniejące wdrożenie usługi Azure AD Connect z synchronizacją haseł wyłączone i metody logowania użytkownika jest już skonfigurowany jako *uwierzytelniania przekazywanego*. Przy użyciu *zmienić logowania użytkownika* zadania, spróbuj do wyboru/Usuń zaznaczenie pola wyboru *włączyć bezproblemowe logowanie jednokrotne* opcji, gdy metoda logowania użytkownika jest skonfigurowany jako "Uwierzytelniania przekazywanego". Po zastosowaniu zmiany Kreator włącza synchronizacji haseł. Z tej poprawki Kreator włącza już synchronizacji haseł. 

* Rozwiązano problem powodujący Azure AD Connect uaktualnienie kończy się niepowodzeniem z powodu błędu "*nie można uaktualnić usługę synchronizacji*". Ponadto usługa synchronizacji nie można uruchomić z powodu błędu zdarzenia "*usługa nie może uruchomić, ponieważ wersja bazy danych jest nowsza niż wersja plików binarnych zainstalowany*". Problem występuje, gdy administrator uaktualniania nie ma uprawnień administratora systemu do programu SQL server, który jest używany przez program Azure AD Connect. Ta poprawka Azure AD Connect wymaga tylko administratorowi uprawnień db_owner w bazie danych ADSync podczas uaktualniania.

* Rozwiązano problem Azure AD Connect uaktualnienia, który dotyczy klientów, którzy mają włączone [bezproblemowe logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Po uaktualnieniu Azure AD Connect bezproblemowe logowanie jednokrotne niepoprawnie pojawia się jako wyłączone w Kreator Azure AD Connect, mimo że funkcja pozostanie włączony i w pełni funkcjonalne. Z tej poprawki funkcja jest teraz wyświetlany jako prawidłowo włączone w kreatorze.

* Rozwiązano problem powodujący Kreator Azure AD Connect, aby zawsze pokazuj "*Konfiguruj zakotwiczenia źródła*" Monituj na *gotowe do konfiguracji* strony, nawet jeśli nie dokonano żadnych zmian związanych z zakotwiczenie źródła.

* Po Ręczne uaktualnianie w miejscu programu Azure AD Connect klient musi podać poświadczenia administratora globalnego odpowiedniego dzierżawcy usługi Azure AD. Wcześniej, uaktualnienia można kontynuować mimo, że poświadczenia administratora globalnego należące do innej usługi Azure AD dzierżawy. Podczas uaktualniania pojawi się pomyślnie, niektóre konfiguracje nie są poprawnie zachowywane po uaktualnieniu. Dzięki tej zmianie kreatora zapobiega uaktualnienia kontynuowanie, jeśli podane poświadczenia nie są zgodne z dzierżawy usługi Azure AD.

* Usunięto nadmiarowe logiki niepotrzebnie uruchomiono ponownie usługę Azure AD Connect Health na początku ręcznego uaktualnienia.


#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano logikę uprościć czynności wymagane do skonfigurowania usługi Azure AD Connect z chmurą Niemcy firmy Microsoft. Wcześniej konieczne jest aktualizacja określonych kluczy rejestru na serwerze Azure AD Connect, aby działać poprawnie w Niemczech firmy Microsoft w chmurze, zgodnie z opisem w tym artykule. Teraz Azure AD Connect można automatycznie wykryć, czy dzierżawy jest w Niemczech firmy Microsoft w chmurze na podstawie podane podczas instalacji poświadczenia administratora globalnego.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
>[!NOTE]
> Uwaga: Usługę synchronizacji ma interfejs WMI, który umożliwia tworzenie własnych niestandardowych harmonogramu. Ten interfejs jest teraz przestarzałe i zostanie usunięte z przyszłych wersji programu Azure AD Connect zostały wysłane po 30 czerwca 2018. Klienci, którzy chcą dostosować harmonogram synchronizacji, należy użyć [wbudowanych harmonogram (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Rozwiązane problemy
* Gdy Kreator Azure AD Connect tworzy konto łącznika AD wymagane, aby zsynchronizować zmiany z lokalnej usługi Active Directory, go nie poprawnie przypisuje konto uprawnień wymaganych do odczytania PublicFolder obiektów. Dotyczy to zarówno instalacja ekspresowa i Instalacja niestandardowa. Ta zmiana rozwiązuje ten problem.

* Rozwiązano problem powodujący Kreatora usługi Azure AD Connect rozwiązywania problemów nie renderowanej strony prawidłowo dla Administratorzy korzystający z systemu Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Podczas rozwiązywania problemów z synchronizacji haseł za pomocą Kreatora Azure AD Connect, rozwiązywanie problemów z strony, strony rozwiązywania problemów teraz zwraca stan specyficznego dla domeny.

* Wcześniej, jeśli próba włączenia synchronizacji skrótu hasła, Azure AD Connect nie sprawdza, czy konta łącznika AD ma wymagane uprawnienia do synchronizowania skrótów haseł z lokalnej usługi AD. Teraz Kreator Azure AD Connect Sprawdź i ostrzeżenia, jeśli konta łącznika usługi AD nie ma wystarczających uprawnień.

### <a name="ad-fs-management"></a>Zarządzanie usługami AD FS
#### <a name="fixed-issue"></a>Rozwiązany problem
* Rozwiązano problem związane z wykorzystaniem [msDS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkcji. Dotyczy to klientów, którzy skonfigurowali *Federacja z usługami AD FS* jako metodę logowania użytkownika. Podczas wykonywania *Konfiguruj zakotwiczenia źródła* zadań w kreatorze, Azure AD Connect zmienia się przy użyciu * ms-DS-ConsistencyGuid jako atrybut źródłowy dla nazwę immutableId. W ramach tej zmiany Azure AD Connect podejmuje próbę aktualizacji reguł oświadczeń dla nazwę ImmutableId w usługach AD FS. Jednak w tym kroku nie powiodło się ponieważ Azure AD Connect nie miało poświadczeń administratora, wymagane do skonfigurowania usług AD FS. Z tej poprawki, Azure AD Connect teraz wyświetli monit o wprowadzenie poświadczeń administratora dla usług AD FS podczas wykonywania *Konfiguruj zakotwiczenia źródła* zadań.



## <a name="116140"></a>1.1.614.0
Stanu: 2017 05 września

### <a name="azure-ad-connect"></a>Program Azure AD Connect

#### <a name="known-issues"></a>Znane problemy
* Jest to znany problem, który powoduje uaktualnienie Azure AD Connect kończy się niepowodzeniem z powodu błędu "*nie można uaktualnić usługę synchronizacji*". Ponadto usługa synchronizacji nie można uruchomić z powodu błędu zdarzenia "*usługa nie może uruchomić, ponieważ wersja bazy danych jest nowsza niż wersja plików binarnych zainstalowany*". Problem występuje, gdy administrator uaktualniania nie ma uprawnień administratora systemu do programu SQL server, który jest używany przez program Azure AD Connect. Uprawnienia dbo nie są wystarczające.

* Istnieje znany problem dotyczący usługi Azure AD Connect uaktualnić ją ma wpływ na klientów, którzy mają włączone [bezproblemowe logowanie jednokrotne](active-directory-aadconnect-sso.md). Po uaktualnieniu Azure AD Connect funkcję pojawia się jako wyłączone w kreatorze, mimo że funkcja pozostanie włączony. Poprawka dla tego problemu będą udostępniane w przyszłych wersji. Klienci, którzy są dane o tym problemie wyświetlania ręcznie rozwiązać go przez włączenie bezproblemowe logowanie jednokrotne w kreatorze.

#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem, który uniemożliwił Azure AD Connect aktualizowanie reguł oświadczeń w lokalnej instalacji usług AD FS podczas włączania [msDS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkcji. Problem występuje, jeśli spróbuj włączyć funkcję istniejącego wdrożenia usługi Azure AD Connect, które ma skonfigurowany jako metodę logowania usług AD FS. Problem występuje, ponieważ Kreator nie monit o podanie poświadczeń usług AD FS przed próbą aktualizacji reguł oświadczeń w usługach AD FS.
* Rozwiązano problem powodujący Azure AD Connect niepowodzenie instalacji, jeśli w lokalnym lesie usługi Active Directory została wyłączona NTLM. Problem jest następstwem Kreator Azure AD Connect nie dostarcza poświadczenia w pełni kwalifikowana, podczas tworzenia kontekstów zabezpieczeń wymagane do uwierzytelniania protokołu Kerberos. Powoduje to niepowodzenie uwierzytelnienia Kerberos i Kreator Azure AD Connect, aby wrócić korzystania z uwierzytelniania NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem, na których nie można utworzyć nową regułę synchronizacji Jeśli atrybut Tag nie jest wypełnione.
* Rozwiązano problem powodujący Azure AD Connect połączyć się z lokalnymi AD synchronizacji haseł przy użyciu protokołu NTLM, nawet jeśli protokół Kerberos jest dostępne. Ten problem występuje, gdy lokalną topologii AD ma co najmniej jeden kontroler domeny, które zostały przywrócone z kopii zapasowej.
* Rozwiązano problem powodujący kroki pełną synchronizację, aby niepotrzebnie wystąpić po uaktualnieniu. Ogólnie rzecz biorąc uruchomienie pełnej synchronizacji czynności jest wymagane po uaktualnieniu w przypadku zmiany reguły synchronizacji out-of-box. Problem został z powodu błędu Zmień logikę wykrywania, który niepoprawnie wykryty zmiany w przypadku napotkania wyrażenie reguły synchronizacji znakami nowego wiersza. Znaki nowego wiersza są wstawiane do wyrażenia reguły synchronizacji, aby poprawić czytelność.
* Rozwiązano problem, który może spowodować, że serwer Azure AD Connect nie działa prawidłowo po automatyczne uaktualnienie. Ten problem ma wpływ na serwerach Azure AD Connect z wersją 1.1.443.0 (lub starszym). Aby uzyskać szczegółowe informacje o problemie, można znaleźć w artykule [Azure AD Connect nie działa prawidłowo po automatyczne uaktualnianie](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Rozwiązano problem, która powoduje automatyczne uaktualnienie ponowienia co 5 minut, jeśli wystąpią błędy. Z poprawkę automatyczne uaktualnienie ponownych prób z wykładniczej wycofania gdy wystąpią błędy.
* Rozwiązano problem, w którym zdarzenia synchronizacji haseł 611 nieprawidłowo jest wyświetlany w dziennikach zdarzeń aplikacji systemu Windows jako **informacyjny** zamiast **błąd**. Zawsze, gdy synchronizacji haseł napotka jakiś problem, zostanie wygenerowane zdarzenie 611. 
* Rozwiązano problem w Kreator Azure AD Connect, który umożliwia funkcji zapisywania zwrotnego grupy włączenia bez zaznaczania jednostki Organizacyjnej wymagane dla zapisu zwrotnego grup.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Rozwiązywanie problemów dotyczących zadań należy dodać do Kreator Azure AD Connect w obszarze dodatkowych zadań. Klienci mogą korzystać z tego zadania, aby rozwiązać problemy związane z synchronizacji haseł i zbieranie danych diagnostycznych ogólne. W przyszłości Rozwiązywanie problemów dotyczących zadań zostanie rozszerzony, aby uwzględnić inne problemy dotyczące synchronizacji katalogu.
* Azure AD Connect obsługuje teraz nowy tryb instalacji o nazwie **Użyj istniejącej bazy danych**. Ten tryb instalacji umożliwiają zainstalowanie usługi Azure AD Connect, która określa istniejąca baza danych ADSync. Aby uzyskać więcej informacji na temat tej funkcji można znaleźć w artykule [Użyj istniejącej bazy danych](active-directory-aadconnect-existing-database.md).
* Ze względów bezpieczeństwa Azure AD Connect teraz domyślnie używany TLS1.2 do nawiązywania połączenia z usługą Azure AD do synchronizacji katalogów. Wartość domyślna została wcześniej TLS1.0.
* Po uruchomieniu usługi Azure AD Connect Agent synchronizacji haseł, próbuje nawiązać połączenia z dobrze znanego punktu końcowego usługi Azure AD dla synchronizacji haseł. Po pomyślnym połączeniu nastąpi przekierowanie do punktu końcowego określonego regionu. Wcześniej Agent synchronizacji haseł buforuje punktu końcowego określonego regionu, dopóki nie zostanie uruchomiona ponownie. Teraz agenta spowoduje wyczyszczenie pamięci podręcznej i ponawia próbę z dobrze znanego punktu końcowego, jeśli wykryje problem z połączeniem z punktem końcowym określonego regionu. Ta zmiana zapewnia, że synchronizacji haseł w pamięci podręcznej punktu końcowego określonego regionu nie jest już dostępny, można trybu failover w innym elemencie EndPoint określonego regionu.
* Aby zsynchronizować zmiany z lokalnego lasu usługi AD, konta usług AD DS jest wymagana. Możesz i utworzyć usług AD DS konta użytkownika i podać jego poświadczenia do usługi Azure AD Connect lub (ii), podaj poświadczenia administratora przedsiębiorstwa i pozwól Azure AD Connect tworzenia konta usług AD DS. Wcześniej (i) jest domyślną opcją w Kreatora programu Azure AD Connect. Teraz jest domyślną opcją w (ii).

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Dodano obsługę programu Microsoft Azure dla instytucji rządowych Cloud i Niemczech Microsoft Cloud.

### <a name="ad-fs-management"></a>Zarządzanie usługami AD FS
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Polecenia cmdlet Initialize ADSyncNGCKeysWriteBack w module przygotowywania środowiska powershell AD został niepoprawnie stosowanie listy ACL do kontenera rejestracji urządzenia i w związku z tym dziedziczy tylko istniejące uprawnienia.  Zaktualizowano, aby konto usługi synchronizacji ma odpowiednie uprawnienia.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Zadania usługi AAD Connect Sprawdź usługi AD FS Login został zaktualizowany tak, aby sprawdza operacje logowania względem Online firmy Microsoft i nie tylko token pobierania z usług AD FS.
* Podczas konfigurowania nowej farmy usług AD FS przy użyciu AAD Connect, strona monitowanie o podanie poświadczeń usług AD FS został przeniesiony, aby teraz występuje przed użytkownik jest monitowany o podanie serwerów usług AD FS i WAP.  Dzięki temu AAD Connect sprawdzić, czy określone konto ma odpowiednie uprawnienia.
* Podczas uaktualniania AAD Connect firma Microsoft nie powiedzie się uaktualnienie Jeśli zaufania usług AD FS usługi AAD nie może zaktualizować.  W takim przypadku użytkownik pojawi się odpowiedni komunikat ostrzegawczy i powinno być kontynuowane, aby zresetować zaufania za pomocą zadania dodatkowe AAD Connect.

### <a name="seamless-single-sign-on"></a>Bezproblemowe logowanie jednokrotne
#### <a name="fixed-issues"></a>Rozwiązane problemy
* Rozwiązano problem powodujący Kreator Azure AD Connect zwracać błąd przy próbie włączenia [bezproblemowe logowanie jednokrotne](active-directory-aadconnect-sso.md). Komunikat o błędzie *"Konfiguracja Microsoft Azure AD Connect Agent uwierzytelniania nie powiodło się."* Ten problem dotyczy istniejących klientów, którzy przeprowadzili uaktualnienie ręcznie wersję zapoznawczą agentów uwierzytelniania dla [uwierzytelniania przekazywanego](active-directory-aadconnect-sso.md) oparte na kroki opisane w tym [artykułu](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stanu: 2017 23 lipca

### <a name="azure-ad-connect"></a>Program Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązany problem

* Rozwiązano problem powodujący reguły synchronizacji out-of-box "Out do AD - użytkownika nazwę ImmutableId" do usunięcia:

  * Problem występuje, gdy jest uaktualnienie programu Azure AD Connect lub gdy opcja zadań *aktualizacji konfiguracji synchronizacji* w programie Azure AD Connect jest używany Kreator aktualizacji konfiguracji synchronizacji Azure AD Connect.
  
  * Ta reguła synchronizacji ma zastosowanie do klientów, którzy mają włączone [msDS-ConsistencyGuid jako funkcja zakotwiczenie źródła](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Ta funkcja została wprowadzona w wersji 1.1.524.0 i po. Po usunięciu reguły synchronizacji Azure AD Connect nie może wypełnić lokalnymi AD ConsistencyGuid-ms-DS atrybut o wartości atrybut ObjectGuid. Nie zapobiega nowi użytkownicy z aprowizowany do usługi Azure AD.
  
  * Poprawka gwarantuje, że reguły synchronizacji zostanie już usunięte podczas uaktualniania, lub zmianę konfiguracji, tak długo, jak ta funkcja jest włączona. Dla istniejących klientów, które zostały objęte tym problemem poprawkę gwarantuje również, że reguła synchronizacji jest dodawany po uaktualnieniu do tej wersji programu Azure AD Connect.

* Rozwiązano problem powodujący reguły synchronizacji out-of-box mają pierwszeństwo przed wartość, która jest mniejsza niż 100:

  * Ogólnie rzecz biorąc pierwszeństwo wartości 0 - 99 są zarezerwowane dla reguły synchronizacji niestandardowych. Podczas uaktualniania wartości pierwszeństwo reguły synchronizacji out-of-box są aktualizowane w celu uwzględnienia zmian reguły synchronizacji. Z powodu tego problemu reguły synchronizacji out-of-box można przypisać priorytet wartość, która jest mniejsza niż 100.
  
  * Poprawka zapobiega problem podczas uaktualniania. Jednak nie są przywracane wartości priorytet dla istniejących klientów, którzy zostały dotyczy problem. Poprawka oddzielne zostaną przekazane w przyszłości pomoc przywracania.

* Rozwiązano problem gdzie [ekran domeny i jednostki Organizacyjnej filtrowania](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) w programie Azure AD Connect jest wyświetlany Kreator *synchronizowanie wszystkich domen i jednostek organizacyjnych* opcję jako wybrany, nawet jeśli filtrowanie na podstawie jednostki Organizacyjnej jest włączona.

*   Rozwiązano problem powodujący [ekranu Konfigurowanie partycji katalogu](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) w Menedżera usługi synchronizacji do zwracania błąd Jeśli *Odśwież* kliknięciu przycisku. Komunikat o błędzie *"Napotkano błąd podczas odświeżania domeny: nie można rzutować obiektu typu"System.Collections.ArrayList"na typ" Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* Ten błąd występuje, gdy nowej domeny AD został dodany do istniejącego lasu usługi AD i próbujesz zaktualizować Azure AD Connect przy użyciu przycisku Odśwież.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* [Funkcja aktualizacji automatycznych](active-directory-aadconnect-feature-automatic-upgrade.md) został rozszerzony w celu obsługi klientów z następujących konfiguracji:
  * Włączono funkcję zapisywania zwrotnego urządzeń.
  * Włączono funkcję zapisywania zwrotnego grup.
  * Instalacja nie jest ustawień ekspresowych lub uaktualnienie narzędzia DirSync.
  * Masz więcej niż 100 000 obiektów w magazynie metaverse.
  * Łączysz się z więcej niż jednego lasu. Instalacja ekspresowa tylko łączy się z jednego lasu.
  * Konta łącznika usługi AD nie jest już domyślnym kontem MSOL_.
  * Serwer jest ustawiona na tryb przejściowy.
  * Włączono funkcję zapisywania zwrotnego użytkowników.
  
  >[!NOTE]
  >Rozszerzenia zakresu funkcji automatyczne uaktualnienie ma wpływ na klientów z programem Azure AD Connect kompilacji 1.1.105.0 i po. Jeśli chcesz, aby serwer Azure AD Connect, aby automatycznie uaktualnione, należy uruchomić następujące polecenia cmdlet na serwerze programu Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Aby uzyskać więcej informacji o włączenie/wyłączenie funkcji automatycznego uaktualniania można znaleźć w artykule [Azure AD Connect: automatyczne uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Stan: Nie będzie publikowana. Zmiany w tej kompilacji są uwzględnione w wersji 1.1.561.0.

### <a name="azure-ad-connect"></a>Program Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązany problem

* Rozwiązano problem powodujący reguły synchronizacji out-of-box "Out do AD - użytkownika nazwę ImmutableId" do usunięcia podczas aktualizowania konfiguracji filtrowania opartego na jednostce Organizacyjnej. Ta reguła synchronizacji jest wymagany dla [msDS-ConsistencyGuid jako funkcja zakotwiczenie źródła](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Rozwiązano problem gdzie [ekran domeny i jednostki Organizacyjnej filtrowania](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) w programie Azure AD Connect jest wyświetlany Kreator *synchronizowanie wszystkich domen i jednostek organizacyjnych* opcję jako wybrany, nawet jeśli filtrowanie na podstawie jednostki Organizacyjnej jest włączona.

*   Rozwiązano problem powodujący [ekranu Konfigurowanie partycji katalogu](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) w Menedżera usługi synchronizacji do zwracania błąd Jeśli *Odśwież* kliknięciu przycisku. Komunikat o błędzie *"Napotkano błąd podczas odświeżania domeny: nie można rzutować obiektu typu"System.Collections.ArrayList"na typ" Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* Ten błąd występuje, gdy nowej domeny AD został dodany do istniejącego lasu usługi AD i próbujesz zaktualizować Azure AD Connect przy użyciu przycisku Odśwież.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* [Funkcja aktualizacji automatycznych](active-directory-aadconnect-feature-automatic-upgrade.md) został rozszerzony w celu obsługi klientów z następujących konfiguracji:
  * Włączono funkcję zapisywania zwrotnego urządzeń.
  * Włączono funkcję zapisywania zwrotnego grup.
  * Instalacja nie jest ustawień ekspresowych lub uaktualnienie narzędzia DirSync.
  * Masz więcej niż 100 000 obiektów w magazynie metaverse.
  * Łączysz się z więcej niż jednego lasu. Instalacja ekspresowa tylko łączy się z jednego lasu.
  * Konta łącznika usługi AD nie jest już domyślnym kontem MSOL_.
  * Serwer jest ustawiona na tryb przejściowy.
  * Włączono funkcję zapisywania zwrotnego użytkowników.
  
  >[!NOTE]
  >Rozszerzenia zakresu funkcji automatyczne uaktualnienie ma wpływ na klientów z programem Azure AD Connect kompilacji 1.1.105.0 i po. Jeśli chcesz, aby serwer Azure AD Connect, aby automatycznie uaktualnione, należy uruchomić następujące polecenia cmdlet na serwerze programu Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Aby uzyskać więcej informacji o włączenie/wyłączenie funkcji automatycznego uaktualniania można znaleźć w artykule [Azure AD Connect: automatyczne uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Stanu: 2017 lipca

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji Azure AD Connect automatycznego uaktualnienia.

### <a name="azure-ad-connect"></a>Program Azure AD Connect

#### <a name="fixed-issue"></a>Rozwiązany problem
* Rozwiązano problem za pomocą polecenia cmdlet Initialize-ADSyncDomainJoinedComputerSync, który spowodował zweryfikowanej domeny skonfigurowane na istniejący obiekt punktu połączenia usługi zostanie zmieniony, nawet jeśli jest nadal prawidłowa domena. Ten problem występuje, gdy dzierżawy usługi Azure AD ma więcej niż jeden zweryfikowanych domen, które mogą służyć do konfigurowania punktu połączenia usługi.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Zapisywanie zwrotne haseł jest teraz dostępna do podglądu z chmury Microsoft Azure dla instytucji rządowych i Niemczech Microsoft Cloud. Aby uzyskać więcej informacji na temat obsługi wystąpienia różnych usług Azure AD Connect można znaleźć w artykule [Azure AD Connect: uwagi dotyczące wystąpienia](active-directory-aadconnect-instances.md).

* Polecenia cmdlet Initialize-ADSyncDomainJoinedComputerSync ma teraz nowe opcjonalny parametr o nazwie AzureADDomain. Ten parametr umożliwia określenie, które zweryfikować domeny służący do konfigurowania punktu połączenia usługi.

### <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Nazwa agenta wymagany na potrzeby uwierzytelniania przekazywanego została zmieniona z *łącznika serwera Proxy w aplikacji pakietu Microsoft Azure AD* do *agenta programu Microsoft Azure AD Connect uwierzytelniania*.

* Włączanie uwierzytelniania przekazywanego już domyślnie włączone synchronizacji skrótu hasła.


## <a name="115530"></a>1.1.553.0
Stan: Czerwiec 2017

> [!IMPORTANT]
> Brak schematu i synchronizacji reguły wprowadzone w tej kompilacji. Azure AD Connect usługi synchronizacji wyzwoli pełny Import i pełną synchronizację czynności po uaktualnieniu. Poniżej zamieszczono szczegółowe informacje o zmianach. Aby tymczasowo wstrzymuje pełny Import i pełną synchronizację czynności po uaktualnieniu, można znaleźć w artykule [jak mają być odroczone pełną synchronizację po uaktualnieniu](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Znany problem
* Istnieje problem, który ma wpływ na klientów, którzy korzystają z [filtrowanie na podstawie jednostki Organizacyjnej](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) z synchronizacji Azure AD Connect. Po przejściu do [strony domeny i jednostki Organizacyjnej filtrowania](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) w Kreatorze Azure AD Connect oczekuje się następujące działania:
  * Jeśli włączono filtrowanie na podstawie jednostki Organizacyjnej **synchronizacji wybranych domen i jednostek organizacyjnych** opcja jest zaznaczona.
  * W przeciwnym razie **synchronizowanie wszystkich domen i jednostek organizacyjnych** opcja jest zaznaczona.

Jest to problem, który pojawia się, że **synchronizowanie wszystkich domen i jednostek organizacyjnych opcja** jest zawsze zaznaczone, po uruchomieniu kreatora.  Dzieje się tak nawet, jeśli wcześniej skonfigurowano filtrowanie oparte na jednostce Organizacyjnej. Przed zapisaniem zmian konfiguracji AAD Connect, upewnij się, **synchronizować wybranych domen i jednostek organizacyjnych opcja** i Potwierdź, że wszystkie jednostki organizacyjne, które należy zsynchronizować są ponownie włączona. W przeciwnym razie filtrowanie na podstawie jednostki Organizacyjnej zostanie wyłączone.

#### <a name="fixed-issues"></a>Rozwiązane problemy

* Rozwiązano problem z zapisywaniem zwrotnym haseł, które umożliwiają administratorowi usługi Azure AD można zresetować hasła lokalnego AD uprzywilejowane konto użytkownika. Problem występuje, gdy Azure AD Connect jest udzielenie uprawnienia do resetowania hasła za pośrednictwem konta uprzywilejowanego. Problem został rozwiązany w tej wersji programu Azure AD Connect, nie zezwalając Administrator usługi Azure AD można zresetować hasła dowolnego lokalnymi AD uprzywilejowane konto użytkownika, chyba że administrator jest właścicielem tego konta. Aby uzyskać więcej informacji, zapoznaj się [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453).

* Rozwiązany problem związany z [msDS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkcji, w którym Azure AD Connect jest nie do zapisu do atrybutu msDS-ConsistencyGuid AD lokalnymi. Problem występuje w przypadku wielu lokalnymi lasami usługi AD dodany do programu Azure AD Connect i *tożsamości użytkowników istnieją w wielu opcji katalogów* jest zaznaczone. W przypadku takiej konfiguracji reguły synchronizacji wynikowe nie należy wypełniać atrybutu sourceAnchorBinary w magazynie Metaverse. Atrybut sourceAnchorBinary jest używany jako atrybut źródłowy dla atrybutu msDS-ConsistencyGuid. W związku z tym zapisywania zwrotnego z atrybutem ms DSConsistencyGuid nie występuje. Aby rozwiązać ten problem, aby upewnić się, że atrybut sourceAnchorBinary w magazynie Metaverse zawsze jest wypełniana zostały zaktualizowane następujące reguły synchronizacji:
  * W z usługi AD - InetOrgPerson AccountEnabled.xml
  * W z usługi AD - wstawić InetOrgPerson
  * W z usługi AD - AccountEnabled.xml użytkownika
  * W z usługi AD - wstawić użytkownika
  * W z usługi AD - użytkownika dołączyć SOAInAAD.xml

* Wcześniej nawet jeśli [msDS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkcja nie jest włączona, reguła synchronizacji "Out do AD-użytkownika nazwę ImmutableId" wciąż jest dodawany do programu Azure AD Connect. Efekt jest niegroźne i nie powoduje zapisywanie zwrotne atrybutu msDS-ConsistencyGuid występuje. Aby uniknąć pomyłek, dodano logikę upewnij się, że reguła synchronizacji został dodany tylko po włączeniu funkcji.

* Rozwiązano problem, który spowodował niepowodzenie synchronizacji skrótów haseł z zdarzenie błędu 611. Ten problem występuje, gdy jedna lub więcej domen, kontrolerów zostały usunięte z lokalnej usługi AD. Na koniec cyklu synchronizacji haseł, pliku cookie synchronizacji wystawione przez lokalny AD zawiera identyfikatory wywołania usuniętych kontrolerów domeny z numerów USN (Update Sequence Number) o wartości 0. Menedżera synchronizacji haseł nie może pozostać synchronizacji plik cookie zawierający USN wartość 0 i kończy się niepowodzeniem z zdarzenie błędu 611. Podczas następnego cyklu synchronizacji Menedżer synchronizacji haseł ponownie używa ostatniego pliku cookie utrwalonego synchronizacji, która nie zawiera numeru USN równą 0. Powoduje to, że te same zmiany hasła, należy ponownie zsynchronizować. Z tej poprawki Menedżera synchronizacji haseł są utrwalane w pliku cookie synchronizacji poprawnie.

* Wcześniej nawet jeśli automatyczne uaktualnienie zostało wyłączone za pomocą polecenia cmdlet Set-ADSyncAutoUpgrade, automatyczne uaktualnienie proces jest kontynuowany do sprawdzenia okresowo uaktualniania, a zależy od pobrany Instalator uwzględnić niepełnosprawność. Z tej poprawki proces automatycznego uaktualniania nie sprawdza, czy uaktualnienie okresowo. Poprawka jest automatycznie stosowana podczas uaktualniania Instalatora dla tej wersji Azure AD Connect jest wykonywana raz.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Wcześniej [msDS-ConsistencyGuid jako zakotwiczenie źródła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) była dostępna tylko dla nowych funkcji. Teraz jest ona dostępna do istniejącego wdrożenia. Więcej szczegółów:
  * Aby uzyskać dostęp do funkcji, uruchom Kreatora programu Azure AD Connect, a następnie wybierz *zakotwiczenie źródła aktualizacji* opcji.
  * Ta opcja jest widoczna tylko do istniejącego wdrożenia, które używają objectGuid jako atrybut sourceAnchor.
  * Podczas konfigurowania opcji, Kreator sprawdza, czy stan atrybutu msDS-ConsistencyGuid w lokalnej usługi Active Directory. Jeśli ten atrybut nie jest skonfigurowana dla dowolnego obiektu użytkownika w katalogu, kreator użyje msDS-ConsistencyGuid jako atrybut sourceAnchor. Jeśli ten atrybut został skonfigurowany w co najmniej jeden obiekt użytkownika w katalogu, Kreator zawiera atrybut jest używany przez inne aplikacje i nie nadaje się jako atrybut sourceAnchor i nie zezwala na zmianę zakotwiczenie źródła, aby kontynuować. Jeśli masz pewność, że ten atrybut nie jest używane przez istniejące aplikacje, należy skontaktować się z obsługą informacji dotyczących Pomiń błąd.

* Specyficzne dla **userCertificate** atrybutu na obiekty urządzeń, Azure teraz AD Connect szuka wartości certyfikaty wymagane dla [łączenie urządzeń przyłączonych do domeny z usługą Azure AD środowisko systemu Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) i odfiltrowuje rest przed synchronizacją z usługą Azure AD. Aby włączyć to zachowanie, reguła synchronizacji out-of-box "Się do usługi AAD — urządzenia Join SOAInAD" została zaktualizowana.

* Azure AD Connect teraz obsługuje funkcję zapisywania zwrotnego z usługą Exchange Online **cloudPublicDelegates** atrybutu lokalnej usłudze AD **publicDelegates** atrybutu. Dzięki temu scenariusz, w którym skrzynki pocztowej programu Exchange Online może zostać przydzielony SendOnBehalfTo uprawnień użytkowników z lokalnej skrzynek pocztowych programu Exchange. Do obsługi tej funkcji, Nowa reguła synchronizacji out-of-box "Out do AD — PublicDelegates hybrydowego programu Exchange użytkownika funkcji zapisywania zwrotnego" został dodany. Ta reguła synchronizacji jest dodana tylko do programu Azure AD Connect po włączeniu funkcji hybrydowym programu Exchange.

*   Azure AD Connect obsługuje teraz Synchronizowanie **altRecipient** atrybutów z usługi Azure AD. Aby obsługiwać tę zmianę, następujące reguły synchronizacji out-of-box zostały zaktualizowane do uwzględnienia przepływu wymaganego atrybutu:
  * W z usługi Active Directory — Exchange użytkownika
  * Out do usługi AAD — ExchangeOnline użytkownika
  
* **CloudSOAExchMailbox** atrybut w magazynie Metaverse wskazuje, czy dany użytkownik ma pocztowa usługi Exchange Online. Jego definicja została zaktualizowana uwzględnienie dodatkowych Exchange Online RecipientDisplayTypes jako takie urządzenia i sali konferencyjnej skrzynek pocztowych. Aby włączyć tę zmianę, definicja atrybutu cloudSOAExchMailbox, który znajduje się w obszarze reguły synchronizacji out-of-box "W z usługi AAD — użytkownika hybrydowym programu Exchange", został zaktualizowany od:

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

* Dodano następujący zestaw funkcji X509Certificate2 zgodnego do tworzenia wyrażenia reguł do obsługi certyfikatów wartości w atrybucie certyfikatu użytkownika synchronizacji:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|certThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Wybierz pozycję|
    |CertKeyAlgorithmParams|CertHashString|Gdzie|
    |||Zawiera|

* Aby umożliwić klientom tworzenie reguły synchronizacji niestandardowych przepływ sAMAccountName, domainNetBios i domainFQDN dla grupy obiektów, a także distinguishedName obiektów użytkownika zostały wprowadzone następujące zmiany schematu:

  * Następujące atrybuty zostały dodane do schematu MV:
    * Grupa: Nazwa konta
    * Grupa: domainNetBios
    * Grupa: domainFQDN
    * Osoba: distinguishedName

  * Następujące atrybuty zostały dodane do schematu łącznika usługi Azure AD:
    * Group: OnPremisesSamAccountName
    * Group: NetBiosName
    * Grupa: NazwaDomenyDNS
    * Użytkownik: OnPremisesDistinguishedName

* Skrypt polecenia cmdlet ADSyncDomainJoinedComputerSync ma teraz nowe opcjonalny parametr o nazwie AzureEnvironment. Parametr jest używany do określenia którym odpowiedniego dzierżawcy usługi Azure Active Directory znajduje się w regionie. Prawidłowe wartości to:
  * AzureCloud (default)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Zaktualizowane Edytor reguł synchronizacji do dołączenia (zamiast udostępniania) jako wartość domyślną typu łącza podczas tworzenia reguły synchronizacji.

### <a name="ad-fs-management"></a>Zarządzanie usługami AD FS

#### <a name="issues-fixed"></a>Problemy z stałej

* Następujące adresy URL są nowe WS-Federation punkty końcowe wprowadzone przez usługę Azure AD, aby zwiększyć odporność awarii uwierzytelniania i zostaną dodane do lokalnej konfiguracji relacji zaufania usług AD FS odpowiedzieć strony:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Rozwiązano problem powodujący usług AD FS do generowania wartości oświadczenia niepoprawne IssuerID. Problem występuje, gdy istnieje wielu zweryfikowanych domen w dzierżawie usługi Azure AD i sufiks domeny atrybutu userPrincipalName służący do generowania oświadczeń IssuerID jest co najmniej 3 poziomy głębokości (na przykład johndoe@us.contoso.com). Problem zostanie rozwiązany, aktualizując regex używane przez reguły oświadczeń.

#### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
* Wcześniej funkcja Zarządzanie certyfikatami programu AD FS udostępniane przez usługi Azure AD Connect można używać tylko z farmy usług AD FS zarządzanych za pomocą usługi Azure AD Connect. Teraz używając funkcji z farmy usług AD FS, które nie są zarządzane za pomocą usługi Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Zwolnione: 2017 maja

> [!IMPORTANT]
> Brak schematu i synchronizacji reguły wprowadzone w tej kompilacji. Azure AD Connect usługi synchronizacji wyzwoli pełny Import i pełną synchronizację czynności po uaktualnieniu. Poniżej zamieszczono szczegółowe informacje o zmianach.
>
>

**Rozwiązane problemy:**

Synchronizacja programu Azure AD Connect

* Rozwiązano problem powodujący automatyczne uaktualnienie występuje na serwerze programu Azure AD Connect, nawet jeśli klient ma wyłączone funkcji przy użyciu polecenia cmdlet Set-ADSyncAutoUpgrade. Ta poprawka proces automatyczne uaktualnienie na serwerze nadal sprawdza, czy uaktualnienie okresowo, ale pobrany Instalator będzie honorować konfiguracja automatycznego uaktualnienia.
* Podczas uaktualniania w miejscu narzędzia DirSync Azure AD Connect tworzy konto usługi Azure AD do użycia przez łącznik usługi Azure AD dla synchronizacji z usługą Azure AD. Po utworzeniu konta usługi Azure AD Connect jest uwierzytelniany w usłudze Azure AD przy użyciu konta. Czasami, uwierzytelnianie nie powiedzie się z powodu przejściowych problemów, co z kolei powoduje, że uaktualnienie w miejscu DirSync kończy się niepowodzeniem z powodu błędu *"Wystąpił błąd podczas wykonywania zadań Konfiguruj AAD Sync: AADSTS50034: Aby zalogować się do tej aplikacji, musi to być konto dodane do katalogu xxx.onmicrosoft.com."* Aby zwiększyć odporność uaktualnienie narzędzia DirSync, Azure AD Connect teraz ponawia etap uwierzytelniania.
* Wystąpił problem z kompilacją 443, który powoduje uaktualnienie w miejscu DirSync zakończyło się powodzeniem, ale nie zostaną utworzone profile uruchamiania wymagane do synchronizacji katalogów. Naprawianie logiki znajduje się w tej kompilacji programu Azure AD Connect. Podczas uaktualniania klienta do tej kompilacji, Azure AD Connect wykrywa brak profilów uruchamiania i tworzy je.
* Rozwiązano problem powodujący procesu synchronizacji haseł uruchomienie z 6900 identyfikator zdarzenia i błąd *"element o takim samym kluczu dodano już"*. Ten problem występuje, gdy aktualizacja jednostki Organizacyjnej konfiguracji filtrowania obejmują partycji konfiguracji usługi AD. Aby rozwiązać ten problem, proces synchronizacji haseł synchronizuje zmiany hasła z tylko partycji domeny AD. Partycje niezwiązanego z domeną, takie jak partycja konfiguracji są pomijane.
* Podczas instalacji ekspresowej, Azure AD Connect tworzy lokalne konto usługi AD DS, które będzie używane przez łącznik AD do komunikowania się z lokalnej usługi AD. Poprzednio utworzone konto jest z flagą PASSWD_NOTREQD ustawioną dla atrybutu Kontrola konta użytkownika i losowe hasło jest ustawiony na koncie. Teraz Azure AD Connect jawnie usuwa flagę PASSWD_NOTREQD, po ustawieniu hasła dla konta.
* Rozwiązano problem powodujący uaktualnienie narzędzia DirSync kończy się niepowodzeniem z powodu błędu *"do kontroli nastąpiło zakleszczenie w programie sql server próby uzyskania blokady aplikacji"* po atrybucie mailNickname znajduje się w lokalnej schemat AD, ale nie jest ograniczone do klasy obiektów użytkowników usługi AD.
* Rozwiązano problem powodujący funkcję zapisywania zwrotnego urządzenia tak, aby automatycznie wyłączone podczas administratora jest aktualizowania konfiguracji synchronizacji usługi Azure AD Connect przy użyciu Kreatora Azure AD Connect. Przyczyną tego problemu jest Kreator sprawdzanie wymaganego wstępnie istniejącej konfiguracji funkcji zapisywania zwrotnego urządzeń w lokalnej usłudze AD i sprawdzenie nie powiedzie się. Poprawka jest pomijania sprawdzania, jeśli zapisu zwrotnego urządzeń jest już włączone wcześniej.
* Aby skonfigurować filtrowanie jednostki Organizacyjnej, możesz użyć Kreatora programu Azure AD Connect lub Menedżera usługi synchronizacji. Wcześniej, jeśli używasz kreatora programu Azure AD Connect do konfigurowania filtrowania jednostki Organizacyjnej, nowe jednostki organizacyjne utworzone później są dołączone do synchronizacji katalogów. Jeśli nie chcesz, aby nowe jednostki organizacyjne zostanie uwzględniona, należy skonfigurować jednostkę Organizacyjną filtrowanie przy użyciu Menedżera usługi synchronizacji. Teraz można uzyskać takie samo zachowanie Kreator Azure AD Connect.
* Rozwiązano problem powodujący procedur składowanych wymagane przez program Azure AD Connect, należy utworzyć w schemacie instalowania administratora, a nie w schemacie dbo.
* Rozwiązano problem powodujący atrybutu TrackingId zwrócony przez usługę Azure AD można pominąć w usłudze AAD Connect dzienników zdarzeń serwera. Problem występuje, jeśli usługa Azure AD Connect odbiera komunikat przekierowania z usługi Azure AD i Azure AD Connect nie może połączyć się z punktem końcowym podane. TrackingId jest używany przez pracowników działu pomocy technicznej, aby skorelować z dziennikami po stronie usługi podczas rozwiązywania problemów.
* Azure AD Connect odbierze błąd LargeObject z usługi Azure AD, Azure AD Connect generuje zdarzenia EventID 6941 i komunikat *"elastycznie obiekt jest zbyt duży. Ogranicz liczbę wartości atrybutów dla tego obiektu."* W tym samym czasie, Azure AD Connect generuje również mylących zdarzenia EventID 6900 i komunikat *"Microsoft.Online.Coexistence.ProvisionRetryException: nie można nawiązać połączenia z systemu Windows usługi Azure Active Directory."* Aby zminimalizować ryzyko pomyłek, Azure AD Connect nie generuje już drugim przypadku, gdy zostanie odebrany błąd LargeObject.
* Rozwiązano problem powodujący Synchronization Service Manager przestaje odpowiadać podczas próby zaktualizowania konfiguracji ogólny łącznik LDAP.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Synchronizacja zmian reguły — następujące zmiany reguły synchronizacji zostały wdrożone:
  * Zestaw reguł synchronizacji zaktualizowany domyślny nie eksportowania atrybuty **userCertificate** i **userSMIMECertificate** Jeśli atrybutów ma więcej niż 15 wartości.
  * Atrybuty AD **identyfikator pracownika** i **msExchBypassModerationLink** znajdują się teraz w domyślny zestaw reguł synchronizacji.
  * Atrybut AD **fotografii** została usunięta z domyślnego zestawu reguł synchronizacji.
  * Dodaje **preferredDataLocation** schematu Metaverse i schematu łącznika usługi AAD. Klienci, którzy do zaktualizowania obu atrybutów w usłudze Azure AD można zaimplementować zasady niestandardowe synchronizacji w tym celu. 
  * Dodaje **userType** schematu Metaverse i schematu łącznika usługi AAD. Klienci, którzy do zaktualizowania obu atrybutów w usłudze Azure AD można zaimplementować zasady niestandardowe synchronizacji w tym celu.

* Azure AD Connect teraz automatycznie umożliwia korzystanie z atrybutu ConsistencyGuid jako atrybut zakotwiczenia źródła dla lokalnych obiektów usługi AD. Ponadto, Azure AD Connect wypełnia atrybutu ConsistencyGuid o wartości atrybut objectGuid, jeśli jest pusty. Ta funkcja ma zastosowanie do tylko nowe wdrożenie. Aby dowiedzieć się więcej o tej funkcji, zapoznaj się z rozdziałem artykułu [Azure AD Connect: zagadnienia dotyczące — przy użyciu msDS-ConsistencyGuid jako sourceAnchor projektowania](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* Nowe Rozwiązywanie problemów z polecenia cmdlet Invoke-ADSyncDiagnostics został dodany do zdiagnozowania synchronizacji skrótu hasła problemy związane z. Aby dowiedzieć się, jak za pomocą polecenia cmdlet, można znaleźć w artykule [Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacji Azure AD Connect](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).
* Azure AD Connect teraz obsługuje Synchronizowanie folderu publicznego Mail-Enabled obiektów z lokalnej usługi AD do usługi Azure AD. Można włączyć funkcję Kreator Azure AD Connect w funkcji opcjonalnych. Aby dowiedzieć się więcej o tej funkcji, można znaleźć w artykule [Office 365 katalogu na podstawie krawędzi blokuje obsługę lokalnej poczty włączone folderów publicznych](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect wymaga przy użyciu usług AD DS konta do synchronizacji z lokalnej usługi AD. Wcześniej Jeśli zainstalowano w trybie Express Azure AD Connect, musisz podać poświadczenia konta administratora przedsiębiorstwa i Azure AD Connect będzie utworzyć wymagane konto usługi AD DS produktów. Jednak do niestandardowej instalacji i dodawania lasów do istniejącego wdrożenia, konieczne jest zamiast tego Podaj konto usług AD DS. Teraz masz również opcję, aby podać poświadczenia konta administratora przedsiębiorstwa podczas instalacji niestandardowej i pozwól Azure AD Connect, utworzyć wymagane konta usług AD DS.
* Azure AD Connect obsługuje teraz SQL AOA. Przed zainstalowaniem usługi Azure AD Connect, należy włączyć SQL AOA. Podczas instalacji Azure AD Connect wykrywa, czy podane wystąpienie programu SQL jest włączone dla SQL AOA. Po włączeniu SQL AOA Azure dalsze AD Connect danych liczbowych Jeśli SQL AOA jest skonfigurowana do używania synchronicznego lub asynchronicznego replikacji. Podczas ustawiania odbiornika grupy dostępności, zalecane jest, ustaw właściwość RegisterAllProvidersIP na 0. To zalecenie wynika Azure AD Connect aktualnie używa programu SQL Native Client do połączenia z serwerem SQL i programu SQL Native Client nie obsługuje użycie właściwości MultiSubNetFailover.
* Jeśli używasz LocalDB jako bazy danych dla serwera programu Azure AD Connect i osiągnęła swój limit rozmiaru 10 GB, usługa synchronizacji nie można uruchomić. Wcześniej należy wykonać operacji ShrinkDatabase na LocalDB odzyskiwanie miejsca bazy danych usługi synchronizacji rozpocząć. Po upływie którego można użyć Menedżera usługi synchronizacji do usunięcia historii wykonywania odzyskiwanie miejsca bazy danych. Teraz służy polecenie cmdlet Start-ADSyncPurgeRunHistory do przeczyszczenia Uruchom historyczne dane z bazy danych LocalDB Aby odzyskać miejsce bazy danych. Ponadto to polecenie cmdlet obsługuje tryb offline (określając parametr - offline) która może zostać użyta, gdy usługa synchronizacji nie jest uruchomiona. Uwaga: W trybie offline można tylko jeśli usługa synchronizacji nie jest uruchomiona i baza danych używana jest LocalDB.
* Aby zmniejszyć ilość miejsca do magazynowania wymaganego, usługi Azure AD Connect teraz kompresuje szczegóły błędu synchronizacji przed ich zapisaniem w LocalDB/baz danych. Podczas uaktualniania ze starszej wersji programu Azure AD Connect do tej wersji, Azure AD Connect przeprowadza jednorazowe kompresji na istniejące informacje o błędzie synchronizacji.
* Wcześniej po zaktualizowaniu jednostki Organizacyjnej konfiguracji filtrowania, należy ręcznie uruchomić pełny import, aby upewnić się, że istniejące obiekty są poprawnie uwzględniony/wykluczony z synchronizacji katalogów. Teraz, Azure AD Connect automatycznie wyzwala pełny import podczas następnej synchronizacji cyklu. Dalsze, pełny import jest stosowana tylko do łączników AD aktualizacja dotyczy. Uwaga: To ulepszenie ma zastosowanie do jednostki Organizacyjnej filtrowania aktualizacji, które zostało nawiązane przy użyciu tylko Kreator Azure AD Connect. Nie ma zastosowania do jednostki Organizacyjnej filtrowania aktualizacji, które zostało nawiązane przy użyciu Menedżera usługi synchronizacji.
* Poprzednio filtrowanie na podstawie grupy obsługuje użytkowników, grup i skontaktuj się z tylko obiekty. Teraz filtrowanie na podstawie grupy obsługuje również obiektów komputerów.
* Wcześniej można usunąć przestrzeni łącznika danych bez konieczności wyłączania harmonogramu synchronizacji Azure AD Connect. Teraz Menedżera usługi synchronizacji blokuje usunięcie danych przestrzeni łącznika, jeśli wykryje, czy włączono harmonogramu. Co więcej ostrzeżenie jest zwracana do powiadamia klientów o utracie danych, usunięcie danych przestrzeni łącznika.
* Wcześniej musisz wyłączyć przekształcania programu PowerShell do poprawnego działania Kreator Azure AD Connect. Ten problem zostanie rozwiązany częściowo. Można włączyć przekształcania programu PowerShell, jeśli do zarządzania konfiguracji synchronizacji jest używany Kreator Azure AD Connect. Jeśli do zarządzania konfiguracji usług AD FS jest używany Kreator Azure AD Connect, należy wyłączyć przekształcania programu PowerShell.



## <a name="114860"></a>1.1.486.0
Wydanie: Kwietnia 2017

**Rozwiązane problemy:**
* Rozwiązanie problemu, w którym Azure AD Connect nie zostaną zainstalowane pomyślnie w zlokalizowanej wersji systemu Windows Server.

## <a name="114840"></a>1.1.484.0
Wydanie: Kwietnia 2017

**Znane problemy:**

* Tej wersji programu Azure AD Connect nie zostaną pomyślnie zainstalowane, jeśli są spełnione wszystkie następujące warunki:
   1. Wykonywana albo instalacji narzędzia DirSync w miejscu świeże lub uaktualniania programu Azure AD Connect.
   2. Używasz zlokalizowanej wersji systemu Windows Server, w którym nazwa wbudowanej grupy administratorów na serwerze nie jest "Administratorzy".
   3. Czy przy użyciu domyślnego programu SQL Server 2012 Express LocalDB, zainstalowane z programem Azure AD Connect zamiast podając własne pełne SQL.

**Rozwiązane problemy:**

Synchronizacja programu Azure AD Connect
* Rozwiązano problem, na którym harmonogramu synchronizacji pomija krok całego synchronizacji co najmniej jeden łączniki braku profilu uruchamiania dla tego kroku synchronizacji. Na przykład można ręcznie dodać łącznik bez tworzenia Import zmian profilu uruchamiania go za pomocą Menedżera usługi synchronizacji. Ta poprawka gwarantuje, że harmonogram synchronizacji będzie kontynuował działanie Import zmian dla innych łączników.
* Rozwiązano problem, na którym usługa synchronizacji natychmiastowe zatrzymanie przetwarzania profilu uruchamiania, gdy jest napotka jakiś problem z wykonywania czynności. Ta poprawka gwarantuje, że usługa synchronizacji uruchomionymi krok pomija i kontynuuje przetwarzanie reszta. Na przykład masz Import zmian, uruchom profilu dla użytkownika łącznika usługi AD z wielu kroków wykonywania (po jednej dla każdego lokalnej domeny usługi AD). Usługa synchronizacji zostanie uruchomiona Import zmian z innymi domenami usługi AD, nawet wtedy, gdy jeden z nich ma problemy z połączeniem sieciowym.
* Rozwiązano problem powodujący aktualizacji łącznika usługi Azure AD można pominąć podczas automatycznego uaktualnienia.
* Rozwiązano problem powodujący Azure AD Connect do niepoprawnie ustalić, czy serwer jest kontrolerem domeny podczas instalacji, z kolei powoduje uaktualnienie narzędzia DirSync kończy się niepowodzeniem.
* Rozwiązano problem powodujący DirSync uaktualnienia w miejscu nie utworzyć dowolnego profilu uruchamiania dla łącznika usługi Azure AD.
* Rozwiązano problem, w którym interfejs użytkownika Menedżera usługi synchronizacji przestaje odpowiadać podczas próby skonfigurowania ogólny łącznik LDAP.

Zarządzanie usługami AD FS
* Rozwiązano problem, w której Kreator Azure AD Connect nie powiedzie się, jeśli węzeł podstawowy usług AD FS został przeniesiony na inny serwer.

Usługa rejestracji Jednokrotnej pulpitu
* Rozwiązano problem Kreatora programu Azure AD Connect, w którym ekranu logowania nie umożliwia włączenia funkcji logowania jednokrotnego pulpitu, jeśli została wybrana opcja synchronizacji haseł możliwość logowania podczas instalacji nowego.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Azure AD Sync Połącz teraz obsługuje wirtualnego konta usługi, zarządzane konta usługi i konto usługi zarządzane przez grupę jako swojego konta usługi. Dotyczy to nowa instalacja programu Azure tylko AD Connect. Po zainstalowaniu programu Azure AD Connect:
    * Domyślnie Kreator Azure AD Connect spowoduje utworzenie wirtualnego konta usługi i używa go jako swojego konta usługi.
    * Jeśli instalujesz na kontrolerze domeny, Azure AD Connect nastąpi powrót do poprzedniej zachowanie, w której zostanie utworzone konto użytkownika domeny i używa go jako swojego konta usługi.
    * Domyślne zachowanie można zastąpić, zapewniając jedną z następujących czynności:
      * Konto usługi zarządzane przez grupę
      * Zarządzane konto usługi
      * Konto użytkownika domeny
      * Konto użytkownika lokalnego
* Wcześniej po uaktualnieniu do nowej kompilacji programu Azure AD Connect zawierający łączniki aktualizacji lub zmian reguły synchronizacji, Azure AD Connect wyzwoli cyklu pełnej synchronizacji. Teraz Azure AD Connect selektywnie wyzwala pełny Import krok tylko dla łączników o aktualizacji i pełną synchronizację tylko dla łączników o zmian reguły synchronizacji.
* Wcześniej próg usuwania eksportu ma zastosowanie tylko do eksportu, które są wyzwalane za pomocą harmonogramu synchronizacji. Funkcja jest rozszerzona obejmują eksportuje ręcznie wyzwalane przez klienta za pomocą Menedżera usługi synchronizacji.
* Na dzierżawy usługi Azure AD jest konfiguracji usługi, która wskazuje, czy funkcja synchronizacji haseł jest włączone dla dzierżawy. Wcześniej jest łatwy w konfiguracji usługi być niepoprawnie skonfigurowana przy użyciu usługi Azure AD Connect, jeśli masz aktywnego i serwerze tymczasowym. Teraz, Azure AD Connect będzie próbował zachować konfigurację usługi zgodne z aktywnych tylko serwera Azure AD Connect.
* Azure AD Connect, Kreator teraz wykrywa i zwraca ostrzeżenie, jeśli lokalnej usługi AD nie ma Kosz usługi AD włączone.
* Wcześniej wyeksportować do czasu usługi Azure AD, out i kończy się niepowodzeniem, jeśli łączny rozmiar obiektów w partii przekroczy określony próg. Teraz usługi synchronizacji spróbuje ponownie wysłać obiektów w oddzielnych, krótszych partie, jeśli Napotkano problem.
* Usunięciu aplikacji Zarządzanie kluczami usługi synchronizacji z Menu Start systemu Windows. Zarządzanie klucza szyfrowania będą obsługiwane za pośrednictwem interfejsu wiersza polecenia przy użyciu miiskmu.exe. Informacje o zarządzaniu klucz szyfrowania, można znaleźć w artykule [porzucanie klucz szyfrowania synchronizacji Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Wcześniej Jeśli zmienisz hasło konta usługi synchronizacji Azure AD Connect, usługi synchronizacji nie będzie możliwe start poprawnie dopiero po klucz szyfrowania porzucone i ponownie zainicjować hasło konta usługi synchronizacji Azure AD Connect. Teraz ten proces nie jest już wymagane.

Usługa rejestracji Jednokrotnej pulpitu

* Kreator Azure AD Connect nie wymaga już portu 9090 ma zostać otwarty w sieci, podczas konfigurowania uwierzytelniania przekazywanego i logowania jednokrotnego pulpitu. Wyłącznie port 443 jest wymagana. 

## <a name="114430"></a>1.1.443.0
Wydanie: 2017 marca

**Rozwiązane problemy:**

Synchronizacja programu Azure AD Connect
* Rozwiązano problem, co powoduje, że Kreator Azure AD Connect, jeśli wyświetlana nazwa łącznika usługi Azure AD nie zawiera początkowej domenie onmicrosoft.com przypisane do dzierżawy usługi Azure AD.
* Rozwiązano problem, co powoduje, że Kreator Azure AD Connect zakończyć się niepowodzeniem podczas nawiązywania połączenia z bazą danych SQL, gdy hasło konta usługi synchronizacji zawiera znaki specjalne, takie jak apostrof, dwukropek i miejsca.
* Rozwiązano problem, który powoduje błąd "dimage ma kotwicy, który jest inny niż obraz" występuje na serwerze programu Azure AD Connect w trybie przejściowym, po tymczasowo wyłączyć lokalnego AD obiektu synchronizowanie i włączone go ponownie do synchronizowania.
* Rozwiązano problem, który powoduje błąd "fantom jest zlokalizowana nazwa Wyróżniająca obiektu" występuje na serwerze programu Azure AD Connect w trybie przejściowym, po tymczasowo wyłączyć lokalnego AD obiektu synchronizowanie i włączone go ponownie do synchronizowania.

Zarządzanie usługami AD FS
* Rozwiązano problem której Kreator Azure AD Connect nie Zaktualizuj konfigurację usług AD FS i ustaw prawy oświadczeń w zaufania jednostki uzależnionej, po skonfigurowaniu alternatywnego Identyfikatora logowania.
* Rozwiązano problem, w której Kreator Azure AD Connect nie może poprawnie obsłużyć serwerów usług AD FS, których konta usługi są skonfigurowane przy użyciu formatu userPrincipalName zamiast sAMAccountName formatu.

Uwierzytelnianie przekazywane
* Rozwiązano problem, co powoduje, że Kreator Azure AD Connect, jeśli wybrano opcję przekazywane za pośrednictwem uwierzytelniania, ale rejestracji jego łącznika nie powiedzie się.
* Rozwiązano problem co powoduje, że Kreator Azure AD Connect, aby pominąć weryfikacji sprawdza na wybrany, gdy jest włączona funkcja logowania jednokrotnego pulpitu metoda logowania.

Resetowanie hasła
* Rozwiązano problem, który może spowodować, że serwer Azure AAD Connect nie próbować połączyć się ponownie, jeśli połączenie został przerwany przez zapory lub serwera proxy.

**Nowe funkcje/ulepszenia:**

Synchronizacja programu Azure AD Connect
* Polecenie cmdlet Get-ADSyncScheduler teraz zwraca nową właściwość typu Boolean o nazwie SyncCycleInProgress. Zwrócona wartość ma wartość true, oznacza, że występuje cykl zaplanowanej synchronizacji w toku.
* Folderu docelowego dla instalacji usługi Azure AD Connect i dzienniki instalacji został przeniesiony z %localappdata%\AADConnect do %programdata%\AADConnect zwiększenia dostępności w plikach dziennika.

Zarządzanie usługami AD FS
* Dodano obsługę aktualizacji certyfikat SSL usług AD FS farmy.
* Dodano obsługę zarządzania usługą AD FS 2016.
* Możesz teraz określić istniejącą grupę (grupy zarządzanych kont usług), podczas instalacji usług AD FS.
* Można teraz skonfigurować algorytmu SHA-256, jak siła algorytmu wyznaczania wartości skrótu podpisu dla zaufania jednostki uzależnionej usługi Azure AD.

Resetowanie hasła
* Wprowadzono ulepszenia umożliwiające produktu funkcji w środowiskach z bardziej rygorystyczne reguły zapory.
* Połączenie lepszą niezawodność do usługi Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Wydanie: Grudnia 2016

**Rozwiązany problem:**

* Rozwiązanie problemu, w których issuerid reguł oświadczeń dla usługi Active Directory Federation Services (AD FS) brakuje w tej kompilacji.

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji Azure AD Connect automatycznego uaktualnienia.

## <a name="113710"></a>1.1.371.0
Wydanie: Grudnia 2016

**Znany problem:**

* W tej kompilacji brakuje reguły oświadczeń issuerid dla usług AD FS. Reguły oświadczeń issuerid jest wymagany, jeśli są federowanie wielu domen z usługi Azure Active Directory (Azure AD). Jeśli używasz usługi Azure AD Connect do zarządzania lokalnymi wdrożenia usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczeń issuerid z konfiguracji programu AD FS. Można obejść ten problem, dodając reguły oświadczeń issuerid po instalacji/aktualizacja. Aby uzyskać więcej informacji na temat dodawania issuerid reguł oświadczeń, znajduje się w tym artykule w [Obsługa wielu domen do federowania w usłudze Azure AD](active-directory-aadconnect-multiple-domains.md).

**Rozwiązany problem:**

* Jeśli nie jest otwarty Port 9090 połączenia wychodzące, Azure AD Connect instalacji lub uaktualniania nie powiedzie się.

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji Azure AD Connect automatycznego uaktualnienia.

## <a name="113700"></a>1.1.370.0
Wydanie: Grudnia 2016

**Znane problemy:**

* W tej kompilacji brakuje reguły oświadczeń issuerid dla usług AD FS. Reguły oświadczeń issuerid jest wymagany, jeśli są federowanie wielu domen z usługą Azure AD. Jeśli używasz usługi Azure AD Connect do zarządzania lokalnymi wdrożenia usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczeń issuerid z konfiguracji programu AD FS. Można obejść ten problem, dodając reguły oświadczeń issuerid po instalacji/aktualizacja. Aby uzyskać więcej informacji na temat dodawania issuerid reguł oświadczeń, znajduje się w tym artykule w [Obsługa wielu domen do federowania w usłudze Azure AD](active-directory-aadconnect-multiple-domains.md).
* Port 9090 należy otworzyć, ruch wychodzący do ukończenia instalacji.

**Nowe funkcje:**

* Uwierzytelniania przekazywanego (wersja zapoznawcza).

>[!NOTE]
>Ta kompilacja nie jest dostępna dla klientów za pośrednictwem funkcji Azure AD Connect automatycznego uaktualnienia.

## <a name="113430"></a>1.1.343.0
Wydanie: Listopada 2016

**Znany problem:**

* W tej kompilacji brakuje reguły oświadczeń issuerid dla usług AD FS. Reguły oświadczeń issuerid jest wymagany, jeśli są federowanie wielu domen z usługą Azure AD. Jeśli używasz usługi Azure AD Connect do zarządzania lokalnymi wdrożenia usług AD FS, uaktualnienie do tej kompilacji usuwa istniejącą regułę oświadczeń issuerid z konfiguracji programu AD FS. Można obejść ten problem, dodając reguły oświadczeń issuerid po instalacji/aktualizacja. Aby uzyskać więcej informacji na temat dodawania issuerid reguł oświadczeń, znajduje się w tym artykule w [Obsługa wielu domen do federowania w usłudze Azure AD](active-directory-aadconnect-multiple-domains.md).

**Rozwiązane problemy:**

* Czasami zainstalowaniu programu Azure AD Connect nie działa, ponieważ nie można utworzyć konto Usługa lokalna, którego hasło spełnia poziom złożoności określone przez zasady haseł w Twojej organizacji.
* Rozwiązano problem, w którym sprzężenia reguły są nie ponownie oceniane po obiektu w przestrzeni łącznika jednocześnie staje się poza zakresem dla jednego dołączyć regułę i stać się w zakresie dla innej. Może to nastąpić, jeśli istnieje co najmniej dwa reguł sprzężenia, których warunki sprzężenia wzajemnie się wykluczają.
* Rozwiązano problem, w której reguły synchronizacji ruchu przychodzącego (z usługi Azure AD), które nie zawierają zasady sprzężenia, nie są przetwarzane, gdy mają niższe wartości pierwszeństwa niż zawierających zasady sprzężenia.

**Ulepszenia:**

* Dodano obsługę zainstalowaniu programu Azure AD Connect w systemie Windows Server 2016 Standard lub nowszego.
* Dodano obsługę programu Azure AD Connect przy użyciu programu SQL Server 2016 jako ze zdalną bazą danych.

## <a name="112810"></a>1.1.281.0
Wydanie: Sierpnia 2016

**Rozwiązane problemy:**

* Zmiany, aby zsynchronizować interwał nie nastąpić dopiero po zakończeniu następnego cyklu synchronizacji.
* Kreator Azure AD Connect nie akceptuje konto usługi Azure AD, którego nazwa użytkownika, który rozpoczyna się od znaku podkreślenia (\_).
* Kreator Azure AD Connect nie może uwierzytelnić konta usługi Azure AD, jeśli hasło konta zawiera zbyt wiele znaków specjalnych. Komunikat o błędzie "nie można zweryfikować poświadczeń. Wystąpił nieoczekiwany błąd." jest zwracany.
* Odinstalowywanie serwera na potrzeby przemieszczania powoduje wyłączenie synchronizacji haseł w dzierżawie usługi Azure AD i powoduje, że synchronizacja haseł z aktywnego serwera.
* Synchronizacja haseł nie powiedzie się w przypadkach zdarza po nie skrót hasła przechowywane na użytkownika.
* Po włączeniu trybu przejściowego serwera Azure AD Connect funkcji zapisywania zwrotnego haseł nie jest tymczasowo wyłączone.
* Kreator Azure AD Connect nie są wyświetlane, synchronizacja haseł rzeczywiste i konfiguracji funkcji zapisywania zwrotnego haseł, gdy serwer jest w trybie przejściowym. Zawsze przedstawia on je jako wyłączone.
* Zmiany konfiguracji synchronizacji haseł i zapisywania zwrotnego haseł nie są zachowywane przez Kreator Azure AD Connect, gdy serwer jest w trybie przejściowym.

**Ulepszenia:**

* Zaktualizowane polecenia cmdlet Start-ADSyncSyncCycle aby wskazać, czy można pomyślnie uruchomić nowego cyklu synchronizacji lub nie.
* Dodane polecenia cmdlet Stop-ADSyncSyncCycle zakończenie cyklu synchronizacji i działania, które są obecnie w toku.
* Zaktualizowane polecenia cmdlet Stop-ADSyncScheduler zakończenie cyklu synchronizacji i działania, które są obecnie w toku.
* Podczas konfigurowania [rozszerzenia katalogów](active-directory-aadconnectsync-feature-directory-extensions.md) w Kreator Azure AD Connect można teraz wybrać atrybut usługi Azure AD typu "Teletex string".

## <a name="111890"></a>1.1.189.0
Wydanie: Czerwca 2016 r.

**Rozwiązane problemy i ulepszenia:**

* Teraz można zainstalować na serwerze zgodne ze standardem FIPS Azure AD Connect.
  * Aby synchronizować hasła, zobacz [synchronizacji skrótów haseł i FIPS](active-directory-aadconnectsync-implement-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Rozwiązano problem, w którym nazwa NetBIOS nie można rozpoznać do nazwy FQDN w łącznika usługi Active Directory.

## <a name="111800"></a>1.1.180.0
Wydanie: Maj 2016

**Nowe funkcje:**

* Ostrzeżenie i pomaga zweryfikować domeny, jeśli nie zostało to zrobić przez uruchomienie programu Azure AD Connect.
* Dodano obsługę [Niemcy Microsoft Cloud](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Dodano obsługę najnowszych [chmury Microsoft Azure dla instytucji rządowych](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) infrastruktury z nowymi wymaganiami adresu URL.

**Rozwiązane problemy i ulepszenia:**

* Dodano filtrowanie do Edytor reguł synchronizacji ułatwia znajdowanie reguły synchronizacji.
* Zwiększona wydajność podczas usuwania przestrzeni łącznika.
* Rozwiązano problem, gdy ten sam obiekt został usunięty i dodany w tym samym Uruchom (zwane delete/add).
* Wyłączone synchronizacji już reguły włączające zawierają obiekty i Odśwież atrybuty schematu katalogu lub uaktualniania.

## <a name="111300"></a>1.1.130.0
Wydanie: Kwietnia 2016

**Nowe funkcje:**

* Dodano obsługę atrybuty wielowartościowe [rozszerzenia katalogów](active-directory-aadconnectsync-feature-directory-extensions.md).
* Dodano obsługę więcej zmian konfiguracji dla [automatyczne uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md) wziąć pod uwagę kwalifikuje się do uaktualnienia.
* Dodane niektóre polecenia cmdlet dla [harmonogramu niestandardowego](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Wydanie: Marca 2016

**Rozwiązane problemy:**

* Wprowadzone się, że instalacja ekspresowa nie można użyć w systemie Windows Server 2008 (pre-R2), ponieważ synchronizacja hasła nie jest obsługiwana w tym systemie operacyjnym.
* Uaktualnienie z narzędzia DirSync z konfiguracją niestandardowego filtru nie działają zgodnie z oczekiwaniami.
* Podczas uaktualniania do nowszych wersji i nie wprowadzono żadnych zmian w konfiguracji, pełny import/synchronizacji nie powinny zostać zaplanowane.

## <a name="111100"></a>1.1.110.0
Wydanie: Lutego 2016

**Rozwiązane problemy:**

* Uaktualnienie z wcześniejszych wersjach nie działa, jeśli instalacja nie jest domyślny folder C:\Program Files.
* Jeśli po zainstalowaniu wyczyść **Uruchom proces synchronizacji** na końcu kreatora instalacji, uruchomienie Kreatora instalacji po raz drugi nie włączy harmonogramu.
* Planista nie działa zgodnie z oczekiwaniami na serwerach, na których nie jest używany format daty i godziny US en. Zostanie również zablokować `Get-ADSyncScheduler` do zwrócenia prawidłowego czasu.
* Jeśli zainstalowano jako opcji logowania i uaktualnianie wcześniejszych wersji programu Azure AD Connect z usługami AD FS, Kreator instalacji nie można uruchomić ponownie.

## <a name="111050"></a>1.1.105.0
Wydanie: Lutego 2016

**Nowe funkcje:**

* [Automatyczne uaktualnianie](active-directory-aadconnect-feature-automatic-upgrade.md) funkcji Express ustawienia klientów.
* Obsługa administratora globalnego przy użyciu usługi Azure Multi-Factor Authentication i Privileged Identity Management w Kreatorze instalacji.
  * Musisz zezwolić proxy również zezwalająca na ruch do https://secure.aadcdn.microsoftonline-p.com Jeśli używane jest uwierzytelnianie wieloskładnikowe.
  * Konieczne jest dodanie https://secure.aadcdn.microsoftonline-p.com do listy zaufanych witryn, uwierzytelnianie wieloskładnikowe działać poprawnie.
* Zezwalaj na zmienianie metoda logowania użytkownika po wstępnej instalacji.
* Zezwalaj na [domenę i jednostkę Organizacyjną filtrowania](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) w Kreatorze instalacji. Dzięki temu, nawiązywanie połączeń z lasów, w którym nie wszystkie domeny są dostępne.
* [Harmonogram](active-directory-aadconnectsync-feature-scheduler.md) korzysta z wbudowanej w aparatu synchronizacji.

**Funkcje podwyższenia poziomu z wersji zapoznawczej do GA:**

* [Zapisywanie zwrotne urządzeń](active-directory-aadconnect-feature-device-writeback.md).
* [Rozszerzenia katalogów](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nowe funkcje w wersji zapoznawczej:**

* Nowym domyślnym synchronizacji cyklu interwał wynosi 30 minut. Używany do trzech godzin dla wszystkich wcześniejszych wersjach. Dodaje obsługę, aby zmienić [harmonogramu](active-directory-aadconnectsync-feature-scheduler.md) zachowanie.

**Rozwiązane problemy:**

* Sprawdź, czy strony domen DNS zawsze nie rozpoznał domen.
* Wyświetla monit o poświadczenia administratora domeny, podczas konfigurowania usług AD FS.
* Lokalnych kont usługi AD nie są rozpoznawane przez Kreatora instalacji, jeśli znajduje się w domenie z innego niż domeny katalogu głównego drzewa DNS.

## <a name="1091310"></a>1.0.9131.0
Wydanie: Grudnia 2015 r.

**Rozwiązane problemy:**

* Synchronizacja haseł może nie działać po zmianie hasła w usługach domenowych w usłudze Active Directory (AD DS), ale działa w przypadku ustawienia hasła.
* Jeśli masz serwer proxy może zakończyć się niepowodzeniem uwierzytelniania do usługi Azure AD podczas instalacji, lub Jeśli uaktualnienie zostało anulowane na stronie konfiguracji.
* Uaktualnianie z poprzedniej wersji programu Azure AD Connect przy użyciu pełnego wystąpienia programu SQL Server kończy się niepowodzeniem, jeśli nie jesteś administratorem systemu (SA) programu SQL Server.
* Uaktualnianie z poprzedniej wersji programu Azure AD Connect ze zdalnym serwerem SQL pokazuje błąd "Nie można uzyskać dostępu do programu SQL ADSync bazy danych".

## <a name="1091250"></a>1.0.9125.0
Wydanie: Listopad 2015

**Nowe funkcje:**

* Można ponownie skonfigurować usługi AD FS do zaufania usługi Azure AD.
* Można Odśwież schemat usługi Active Directory, a następnie ponownie wygenerować reguły synchronizacji.
* Można wyłączyć reguły synchronizacji.
* Można zdefiniować "AuthoritativeNull" jako literału nowej reguły synchronizacji.

**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health dla synchronizacji](../connect-health/active-directory-aadconnect-health-sync.md).
* Obsługa [usług domenowych Azure AD](../active-directory-passwords-update-your-own-password.md) synchronizacji haseł.

**Nowy scenariusz obsługiwane:**

* Obsługuje wiele lokalnej organizacji programu Exchange. Aby uzyskać więcej informacji, zobacz [hybrydowych wdrożeń z wieloma lasami usługi Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Rozwiązane problemy:**

* Problemy z synchronizacją hasła:
  * Obiekt przeniesiony z poza zakresem w zakres nie będzie miał jego hasło zsynchronizowane. W tym jednostki Organizacyjnej i filtrowanie atrybutów.
  * Wybranie nowej jednostki Organizacyjnej do uwzględnienia w synchronizacji nie wymaga synchronizacji pełnej hasła.
  * Po włączeniu wyłączony użytkownik nie synchronizuje hasła.
  * Kolejka ponownych prób hasło to nieskończoność i poprzednich limit 5000 obiekty zostaną wycofane został usunięty.
* Nie można nawiązać połączenia z usługi Active Directory z poziomem funkcjonalności lasu systemu Windows Server 2016.
* Nie można zmienić grupy, który służy do filtrowania grupy po początkowej instalacji.
* Nie tworzy nowy profil użytkownika na serwerze usługi Azure AD Connect dla każdego użytkownika podczas zmiany hasła z włączoną zapisywania zwrotnego haseł.
* Nie można używać długich liczb całkowitych wartości zakresy reguły synchronizacji.
* Pole wyboru "zapisu zwrotnego urządzeń" był wyłączony, jeśli ma kontrolerów domeny jest nieosiągalny.

## <a name="1086670"></a>1.0.8667.0
Wydanie: Sierpień 2015

**Nowe funkcje:**

* Kreatora instalacji usługi Azure AD Connect jest teraz zlokalizowane dla wszystkich języków systemu Windows Server.
* Dodano obsługę konta odblokowanie przy użyciu usługi Azure AD zarządzania hasłami.

**Rozwiązane problemy:**

* Kreator instalacji usługi Azure AD Connect ulegnie awarii, jeśli inny użytkownik będzie nadal występować, instalacji, a nie osoby, która najpierw uruchomić instalację.
* Jeśli poprzednie dezinstalacji programu Azure AD Connect nie może prawidłowo odinstalować synchronizacja programu Azure AD Connect, nie jest możliwe ponowne zainstalowanie.
* Nie można zainstalować usługi Azure AD Connect przy użyciu instalacji ekspresowej, jeśli użytkownik nie ma w domenie głównej lasu lub innej niż angielska wersji usługi Active Directory jest używana.
* Jeśli nie można rozpoznać nazwę FQDN konta użytkownika usługi Active Directory, jest wyświetlany błąd komunikat o błędzie "Nie można zatwierdzić schematu".
* Jeśli konto używane na łącznika usługi Active Directory została zmieniona poza kreatora, Kreator nie powiedzie się w kolejnych uruchomieniach.
* Azure AD Connect czasami nie można zainstalować na kontrolerze domeny.
* Nie można włączać i wyłączać "Tryb przejściowy", jeśli zostały dodane atrybuty rozszerzenia.
* Zapisywanie zwrotne haseł kończy się niepowodzeniem w niektórych konfiguracjach ze względu na nieprawidłowe hasło na łącznika usługi Active Directory.
* Nie można uaktualnić narzędzia DirSync, jeśli nazwa wyróżniająca (DN) jest używana w filtrowanie atrybutów.
* Nadmiernego użycia procesora CPU przy użyciu resetowania hasła.

**Funkcje usunięte w wersji zapoznawczej:**

* Funkcja w wersji zapoznawczej [zapisu zwrotnego użytkowników](active-directory-aadconnect-feature-preview.md#user-writeback) tymczasowo usunięto oparte na opinie klientów w wersji zapoznawczej. Zostanie ona dodana ponownie później po rozwiązaliśmy podana opinii.

## <a name="1086410"></a>1.0.8641.0
Wydanie: Czerwiec 2015

**Początkowa wersja programu Azure AD Connect.**

Zmienione nazwę z usługi Azure AD Sync do programu Azure AD Connect.

**Nowe funkcje:**

* [Ustawienia ekspresowe](active-directory-aadconnect-get-started-express.md) instalacji
* Można [skonfigurować usługi AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* Można [uaktualnianie z narzędzia DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Wprowadzono [Tryb przejściowy](active-directory-aadconnectsync-operations.md#staging-mode)

**Nowe funkcje w wersji zapoznawczej:**

* [Zapisywanie zwrotne użytkowników](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Zapisywanie zwrotne grup](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Zapisywanie zwrotne urządzeń](active-directory-aadconnect-feature-device-writeback.md)
* [Rozszerzenia katalogów](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Wydanie: Maj 2015

**Nowe wymaganie:**

* Synchronizacja programu Azure AD teraz wymaga programu .NET Framework w wersji 4.5.1 do zainstalowania.

**Rozwiązane problemy:**

* Zapisywanie zwrotne haseł z usługi Azure AD kończy się niepowodzeniem z powodu błędu połączenia usługi Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Wydanie: Kwietnia 2015 r.

**Rozwiązane problemy i ulepszenia:**

* Łącznik usługi Active Directory nie usuwa poprawnie przetworzyć Jeśli włączono Kosza i jest wiele domen w lesie.
* Dla łącznika usługi Azure Active Directory została zwiększona wydajność operacji importowania.
* Gdy grupa przekroczyła limit członkostwa (domyślnie limit jest ustawiony do 50 000 obiektów), grupa została usunięta w usłudze Azure Active Directory. O nowe zachowania grupie nie zostanie usunięta, zostanie zgłoszony błąd i nowe zmiany członkostwa nie są eksportowane.
* Nie można zainicjować obsługi nowego obiektu, jeśli przemieszczanego delete o tej samej nazwie Wyróżniającej już znajduje się w przestrzeni łącznika.
* Niektóre obiekty zostały oznaczone synchronizacji podczas synchronizacji różnicowej, nawet jeśli nie została zmieniona na obiekt przemieszczane.
* Wymuszanie synchronizacji haseł spowoduje również usunięcie liście preferowanych kontrolera domeny.
* CSExportAnalyzer ma problemy z niektórych stanów obiektów.

**Nowe funkcje:**

* Sprzężenia teraz mogą łączyć się typ obiektu MV "ANY".

## <a name="104850222"></a>1.0.485.0222
Wydanie: Luty 2015

**Ulepszenia:**

* Ulepszone importowanie wydajności.

**Rozwiązane problemy:**

* Synchronizacja haseł honoruje atrybut cloudFiltered, który jest używany przez filtrowanie atrybutów. Obiekty filtrowane nie są już w zakresie synchronizacji haseł.
* W rzadkich sytuacji, gdy topologia miały dla wielu kontrolerów domeny synchronizacja haseł nie działa.
* Włączono "Zatrzymana server" podczas importowania z łącznika usługi Azure AD po zarządzania urządzeniami w usłudze Azure AD/usługi Intune.
* Dołączenie obce podmioty zabezpieczeń (FSP) z wielu domen w tym samym lesie, spowoduje wystąpienie błędu niejednoznaczne sprzężenia.

## <a name="104751202"></a>1.0.475.1202
Wydanie: Z grudnia 2014

**Nowe funkcje:**

* Synchronizacja haseł z filtrowanie na podstawie atrybutu jest teraz obsługiwane. Aby uzyskać więcej informacji, zobacz [synchronizacji haseł za pomocą filtrowania](active-directory-aadconnectsync-configure-filtering.md).
* Atrybut msDS-identyfikatora ExternalDirectoryObjectID jest zapisywane z powrotem do usługi Active Directory. Ta funkcja dodaje obsługę aplikacji usługi Office 365. Używa protokołu OAuth2 na dostęp do skrzynek pocztowych Online i lokalnego w ramach wdrożenia hybrydowego programu Exchange.

**Rozwiązane problemy uaktualnienia:**

* Nowsza wersja Asystenta logowania jest dostępna na serwerze.
* Ścieżka instalacji niestandardowej został użyty do zainstalowania programu Azure AD Sync.
* Nieprawidłowy niestandardowy sprzężenia kryterium blokuje uaktualnienia.

**Inne poprawki:**

* Stała szablonów dla pakietu Office Pro Plus.
* Problemy z instalacją stałym spowodowane przez użytkownika o nazwach zaczynających łącznikiem.
* Stałe utraty ustawienie sourceAnchor podczas uruchamiania Kreatora instalacji po raz drugi.
* Stały śledzenia zdarzeń systemu Windows, aby synchronizować hasła.

## <a name="104701023"></a>1.0.470.1023
Wydanie: Października 2014 r.

**Nowe funkcje:**

* Synchronizację haseł z wieloma lokalnymi usługi Active Directory do usługi Azure AD.
* Instalacja zlokalizowanego interfejsu użytkownika na wszystkie języki systemu Windows Server.

**Uaktualnianie z GA aplikacja AADSync 1.0**

Jeśli masz już zainstalowany program Azure AD Sync, ma jednego dodatkowego kroku, które należy wykonać w przypadku, gdy zmieniono dowolne reguły synchronizacji out-of-box. Po uaktualnieniu do 1.0.470.1023 Zwolnij, synchronizacji, które są zduplikowane reguły, które zostały zmodyfikowane. Dla każdej reguły synchronizacji zmodyfikowane wykonaj następujące czynności:

1.  Znajdź reguły synchronizacji zostały zmodyfikowane i Zapisz zmiany.
* Usuń regułę synchronizacji.
* Znajdź nową regułę synchronizacji, która jest tworzona przez program Azure AD Sync, a następnie ponownie zastosuj zmiany.

**Uprawnienia dla konta usługi Active Directory**

Konto usługi Active Directory musi mieć przyznane dodatkowe uprawnienia, aby można było odczytać skrótów haseł z usługi Active Directory. Uprawnienia można przyznać są nazywane "Replikowanie zmiany katalogu" i "Directory replikacji zmian wszystkie". Aby można było odczytać skrótów haseł są wymagane zarówno uprawnienia.

## <a name="104190911"></a>1.0.419.0911
Wydanie: Września 2014

**Początkowa wersja programu Azure AD Sync.**

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
