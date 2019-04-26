---
title: Azure Active Directory Connect — często zadawane pytania — | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/02/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a392fd03016f83f86364d8f92e8bb4da0aa3364a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381481"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect — często zadawane pytania

## <a name="general-installation"></a>Ogólne instalacji

**Pyt.: Jak można zabezpieczyć mój serwer usługi Azure AD Connect, aby zmniejszyć obszar narażony na ataki zabezpieczeń?**

Firma Microsoft zaleca, ograniczenie funkcjonalności serwera Azure AD Connect do zmniejszenia bezpieczeństwa obszar narażony na atak to kluczowy składnik środowiska informatycznego.  Zgodnie z zaleceniami poniżej zostaną obniżone zagrożenia dla bezpieczeństwa organizacji.

* Wdrażanie usługi Azure AD Connect na serwerze przyłączonym do domeny i Ogranicz dostęp administracyjny do administratorów domeny lub inne grupy zabezpieczeń ściśle kontrolowany

Aby dowiedzieć się więcej, zobacz: 

* [Zabezpieczanie grupy Administratorzy](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpieczanie konta wbudowanego konta administratora](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Ulepszenia zabezpieczeń i sustainment, zmniejszając powierzchni ataku](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Zmniejszenie podatności usługi Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**Pyt.: Instalacja będzie działać, jeśli administrator globalny usługi Azure Active Directory (Azure AD) ma uwierzytelniania dwuskładnikowego (2FA) włączone?**  
Począwszy od lutego 2016 kompilacje ten scenariusz jest obsługiwany.

**Pyt.: Czy istnieje sposób, aby zainstalować program Azure AD Connect nienadzorowanej?**  
Instalacja programu Azure AD Connect jest obsługiwana tylko w przypadku korzystania z Kreatora instalacji. Instalacji nienadzorowanej instalacji dyskretnej, nie jest obsługiwana.

**Pyt.: Masz lasu, w których nie można skontaktować się z jednej domeny. Jak zainstalować program Azure AD Connect?**  
Począwszy od lutego 2016 kompilacje ten scenariusz jest obsługiwany.

**Pyt.: Agent kondycji usługi Azure Active Directory Domain Services (Azure AD DS) działa w instalacji server core?**  
Tak. Po zainstalowaniu agenta, możesz przejść proces rejestracji, za pomocą następującego polecenia cmdlet programu PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Pyt.: Program Azure AD Connect obsługuje synchronizowanie z dwiema domenami, aby w usłudze Azure AD?**  
Tak, ten scenariusz jest obsługiwany. Zapoznaj się [wielu domen](how-to-connect-install-multiple-domains.md).
 
**Pyt.: Można mieć wiele łączników w tej samej domeny usługi Active Directory w programie Azure AD Connect?**  
Nie, wiele łączników dla tej samej domeny usługi AD nie są obsługiwane. 

**Pyt.: Czy mogę przenieść bazę danych usługi Azure AD Connect z lokalnej bazy danych do zdalnego wystąpienia programu SQL Server?**   
Tak, w poniższych krokach przedstawiono ogólne wskazówki, jak to zrobić. Obecnie pracujemy nad bardziej szczegółowym dokumencie.
1. Utwórz kopię zapasową bazy danych LocalDB ADSync.
Jest najprostszym sposobem, w tym celu można użyć programu SQL Server Management Studio zainstalowany na tym samym komputerze co program Azure AD Connect. Połączyć się z *(LocalDb). \ADSync*, a następnie tworzy kopię zapasową bazy danych ADSync.

2. Przywracanie bazy danych ADSync do zdalnego wystąpienia programu SQL Server.

3. Zainstaluj program Azure AD Connect przed istniejące [zdalną bazą danych SQL](how-to-connect-install-existing-database.md).
   Artykuł pokazuje, jak przeprowadzić migrację do korzystania z lokalnej bazy danych SQL. Migrowania do korzystania ze zdalną bazą danych SQL w kroku 5 procesu należy również wprowadzić istniejącego konta usługi, których będzie używać usługa synchronizacji Windows. To konto usługi aparatu synchronizacji opisano tutaj:
   
      **Użyj istniejącego konta usługi**: Domyślnie program Azure AD Connect korzysta z wirtualnego konta usługi dla usługi synchronizacji do użycia. Jeśli używane zdalne wystąpienie programu SQL Server lub serwer proxy, który wymaga uwierzytelniania, użyj zarządzane konto usługi lub konto usługi w domenie i znać hasło. W takich przypadkach wprowadź konto do użycia. Upewnij się, że użytkownicy, którzy uruchomili instalację administratorów systemu SQL tak, aby tworzyć poświadczenia logowania dla konta usługi. Aby uzyskać więcej informacji, zobacz [program Azure AD Connect konta i uprawnienia](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Od ostatniej kompilacji aprowizowanie bazy danych może wykonać poza pasmem administrator usługi SQL, a jej instalację może następnie przeprowadzić administrator programu Azure AD Connect z uprawnieniami właściciela bazy danych. Aby uzyskać więcej informacji, zobacz [zainstalować program Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md).

Aby zachować ich prostotę, zaleca się użytkownicy, którzy instalują program Azure AD Connect były administratorów systemu SQL. Jednak przy użyciu najnowszych wersji możesz teraz Użyj delegowane Administratorzy SQL, zgodnie z opisem w [zainstalować program Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md).

## <a name="network"></a>Sieć
**Pyt.: Mam zapory, urządzenie sieciowe albo coś innego, który ogranicza czas, który połączenia mogą pozostać otwarte w sieci. Co Moje próg limitu czasu po stronie klienta należy podczas korzystania z usługi Azure AD Connect?**  
Całe oprogramowanie sieciowe, urządzenia fizyczne lub jakichkolwiek innych czynności, która ogranicza maksymalny czas, w którym połączenia mogą pozostawać otwarte, należy używać równy co najmniej pięć minut (300 sekund) dla łączności między serwerem, na którym jest zainstalowany klient programu Azure AD Connect i Azure Active Directory. To zalecenie dotyczy także wszystkie wcześniej wydanych narzędzia synchronizacji Microsoft Identity.

**Pyt.: Nazwą domeny (domeny drugiego poziomu) są obsługiwane?**  
Chociaż zaleca się względem tej konfiguracji sieci ([artykuł](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), za pomocą synchronizacji programu Azure AD Connect z nazwą domeny jest obsługiwany, tak długo, jak działa konfiguracja sieci dla jednej domeny poziomu poprawnie.

**Pyt.: Są lasy z rozłączną domeny usługi AD obsługiwane?**  
Nie, usługa Azure AD Connect nie obsługuje lokalnymi lasami, które zawierają rozłączne przestrzenie nazw.

**Pyt.: Są "kropkowana" nazwy NetBIOS, obsługiwane?**  
Nie, usługa Azure AD Connect nie obsługuje lokalnymi lasami lub domenami, których nazwa NetBIOS zawiera kropkę (.).

**Pyt.: Czystego środowiska protokołu IPv6 jest obsługiwana?**  
Nie, usługa Azure AD Connect nie obsługuje czystego środowiska protokołu IPv6.

**Pytanie: Mam środowisku wielu lasów i sieć między dwoma lasami używa translatora adresów Sieciowych (NAT). Jest między tymi dwoma lasami obsługiwane przy użyciu usługi Azure AD Connect?**</br>
 Nie, za pomocą usługi Azure AD Connect za pośrednictwem translatora adresów Sieciowych nie jest obsługiwane. 

## <a name="federation"></a>Federacja
**Pyt.: Co należy zrobić, jeśli pojawia się wiadomość e-mail, który żąda do odnowienia certyfikatu usługi Office 365?**  
Aby uzyskać wskazówki dotyczące odnawiania certyfikatu, zobacz [odnawiania certyfikatów](how-to-connect-fed-o365-certs.md).

**Pyt.: Masz "Automatycznie Aktualizuj jednostki uzależnionej" dla jednostki zależnej usługi Office 365. Czy muszę podejmować żadnych działań, gdy mój token podpisywania certyfikatu automatycznie najedzie na?**  
Użyj wskazówek, które jest opisane w artykule [odnawiania certyfikatów](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Środowisko
**Pyt.: Zmień nazwę serwera, po zainstalowaniu usługi Azure AD Connect jest obsługiwana?**  
Nie. Zmiana nazwy serwera renderuje aparatem synchronizacji nie może połączyć się z wystąpieniem bazy danych SQL i nie można uruchomić usługi.

## <a name="identity-data"></a>Dane o tożsamości
**Pyt.: Dlaczego nie atrybut userPrincipalName (UPN) w usłudze Azure AD są zgodne lokalnymi nazwami UPN?**  
Aby uzyskać informacje zobacz następujące artykuły:

* [Nazwy użytkowników w usłudze Office 365, Azure lub Intune nie są zgodne lokalnymi nazwami UPN lub alternatywnym Identyfikatorem logowania](https://support.microsoft.com/kb/2523192)
* [Zmiany nie są synchronizowane przez narzędzie do synchronizacji usługi Azure Active Directory, po zmianie nazwy UPN konta użytkownika do użycia z innej domeny federacyjnej](https://support.microsoft.com/kb/2669550)

Można również skonfigurować usługi Azure AD, aby umożliwić aparatu synchronizacji można zaktualizować nazwy UPN, zgodnie z opisem w [funkcji Usługa synchronizacji Azure AD Connect](how-to-connect-syncservice-features.md).

**Pyt.: Czy obsługiwane soft-match grupy usługi Azure AD w środowisku lokalnym lub obiektem kontaktu z istniejącej grupy usługi Azure AD lub skontaktuj się z obiektu?**  
Tak, to dopasowanie słabe opiera się na proxyAddress. Dopasowanie słabe nie jest obsługiwane dla grup, które nie są włączone do wiadomości e-mail.

**Pyt.: Czy obsługiwane ręcznie ustawić atrybut ImmutableId dla istniejącej grupy usługi Azure AD, lub skontaktuj się z obiektów twardym dopasowania go do grupy usługi Azure AD w środowisku lokalnym lub skontaktuj się z obiektu?**  
Nie, Ręczne ustawianie atrybutu ImmutableId na istniejącej grupy usługi Azure AD lub obiektem kontaktu do twarde dopasowania, jego aktualnie nie jest obsługiwane.

## <a name="custom-configuration"></a>Konfiguracja niestandardowa
**Pyt.: Gdzie są udokumentowane poleceń cmdlet programu PowerShell dla usługi Azure AD Connect**  
Z wyjątkiem poleceń cmdlet, które zostały opisane w tej witrynie innych poleceń cmdlet programu PowerShell w usłudze Azure AD Connect nie są obsługiwane do użytku klienta.

**Pyt.: Czy można użyć opcji "Serwer serwer eksportu/importu", która znajduje się w Menedżerze usługi synchronizacji, aby przenieść konfigurację między serwerami?**  
Nie. Ta opcja nie pobiera pożądanych wszystkie ustawienia konfiguracji, a nie powinny być używane. Zamiast tego użyj kreatora, aby utworzyć konfigurację podstawową na drugim serwerze i generowanie skryptów programu PowerShell, aby przenieść wszystkie reguły niestandardowej między serwerami za pomocą edytora reguł synchronizacji. Aby uzyskać więcej informacji, zobacz [migracja typu Swing](how-to-upgrade-previous-version.md#swing-migration).

**Pyt.: Hasła można buforować Azure strony logowania i może tej buforowanie blokowane, ponieważ zawiera on element input hasła przy użyciu *autouzupełniania = "false"* atrybut?**  
Obecnie modyfikowanie atrybutów HTML **hasło** pole, w tym tagu autouzupełniania, nie jest obsługiwane. Obecnie pracujemy nad funkcja, która umożliwia niestandardowych skryptów JavaScript, który pozwala dodać dowolnego atrybutu, aby **hasło** pola.

**Pyt.: Strona logowania jest Azure wyświetla nazw użytkowników, którzy wcześniej zalogowali się pomyślnie. Można to zachowanie można wyłączyć?**  
Obecnie modyfikowanie atrybutów HTML **hasło** pola wejściowego, w tym tagu autouzupełniania, nie jest obsługiwana. Obecnie pracujemy nad funkcja, która umożliwia niestandardowych skryptów JavaScript, który pozwala dodać dowolnego atrybutu, aby **hasło** pola.

**Pyt.: Czy istnieje sposób, aby zapobiec równoczesnych sesji?**  
Nie.

## <a name="auto-upgrade"></a>Automatyczne uaktualnianie

**Pyt.: Jakie są zalety i konsekwencje korzystania z automatycznego uaktualnienia?**  
Firma Microsoft jest wniosku wszystkich klientów, aby włączyć automatyczne uaktualnianie ich instalacji program Azure AD Connect. Korzyścią jest zawsze otrzymywać najnowsze poprawki, w tym aktualizacje zabezpieczeń dla luki w zabezpieczeniach, które zostały znalezione w programie Azure AD Connect. Proces uaktualniania jest łatwe i odbywa się automatycznie, gdy tylko jest dostępna nowa wersja. Tysiące klientów usługi Azure AD Connect za pomocą automatyczne uaktualnianie każdej nowej wersji.

Proces automatycznego uaktualniania ustanawia zawsze najpierw, czy instalacja kwalifikują się do automatycznego uaktualniania. Jeśli jest ona uprawniona, uaktualnienie jest wykonywane i testowane. Proces obejmuje również wyszukiwanie niestandardowe zmiany zasad i szczególnych czynników środowiskowych. Jeśli testy wykazują, że uaktualnienie zakończy się niepowodzeniem, poprzednia wersja zostanie automatycznie przywrócony.

W zależności od wielkości środowiska może potrwać kilka godzin. Podczas uaktualniania jest w toku, odbywa się nie synchronizacji między Windows Server Active Directory a usługą Azure AD.

**Pyt.: Otrzymuję wiadomość e-mail z informacją, Moje automatyczne uaktualnianie przestaje działać, należy zainstalować nową wersję. Dlaczego trzeba to zrobić?**  
W zeszłym roku firma Microsoft opublikowała wersję programu Azure AD Connect, że w pewnych okolicznościach może być wyłączone funkcji automatycznej aktualizacji na serwerze. Problem został rozwiązany w usłudze Azure AD Connect w wersji 1.1.750.0. Jeśli masz problem została dotknięta, należy go unikać, uruchamiając skrypt programu PowerShell w celu jego naprawy lub przez ręczne uaktualnienie do najnowszej wersji programu Azure AD Connect. 

Aby uruchomić skrypt programu PowerShell [Pobierz skrypt](https://aka.ms/repairaadconnect) i uruchomienie go na serwerze usługi Azure AD Connect w ramach administracyjne okno programu PowerShell. Aby dowiedzieć się, jak uruchomić skrypt, [Obejrzyj ten krótki film](https://aka.ms/repairaadcau).

Aby ręcznie przeprowadzić uaktualnienie, musisz pobrać i uruchomić najnowszą wersję pliku AADConnect.msi.
 
-  Jeśli Twoja bieżąca wersja jest starsza niż 1.1.750.0, [pobierania i przeprowadzić uaktualnienie do najnowszej wersji](https://www.microsoft.com/download/details.aspx?id=47594).
- Jeśli wersja usługi Azure AD Connect jest 1.1.750.0 lub później, są wymagane nie dalsze działania. Już używasz wersji, który zawiera poprawkę automatyczną aktualizację. 

**Pyt.: Otrzymuję wiadomość e-mail z informacją, aby uaktualnić do najnowszej wersji, aby ponownie włączyć automatyczne uaktualnianie. Używam wersji 1.1.654.0. Należy uaktualnić?**  
Tak, musisz uaktualnić do wersji 1.1.750.0 lub nowszej, aby ponownie włączyć automatyczne uaktualnianie. [Pobierz i przeprowadzić uaktualnienie do najnowszej wersji](https://www.microsoft.com/download/details.aspx?id=47594).

**Pyt.: Otrzymuję wiadomość e-mail z informacją, aby uaktualnić do najnowszej wersji, aby ponownie włączyć automatyczne uaktualnianie. Jeśli masz używany programu PowerShell umożliwia automatyczne uaktualnianie, nadal należy zainstalować najnowszą wersję?**  
Tak, nadal należy do uaktualnienia do wersji 1.1.750.0 lub nowszej. Włączanie usługi automatyczną aktualizację przy użyciu programu PowerShell nie chroni przed znalezione w wersjach starszych niż 1.1.750.0 problemy automatycznej aktualizacji.

**Pyt.: Chcę przeprowadzić uaktualnienie do nowszej wersji, ale nie mam pewności, których zainstalowano program Azure AD Connect, a nie ma nazwy użytkownika i hasła. Potrzebujemy to?**
Nie trzeba znać nazwę użytkownika i hasło, który pierwotnie został użyty do uaktualnić program Azure AD Connect. Użyj dowolnego konta usługi Azure AD, która ma rolę administratora globalnego.

**Pyt.: Jak znaleźć, używam wersji programu Azure AD Connect?**  
Aby sprawdzić, która wersja programu Azure AD Connect jest zainstalowany na serwerze, przejdź do panelu sterowania i wyszukać zainstalowana wersja programu Microsoft Azure AD Connect, wybierając **programy** > **programy i funkcje** , jak pokazano poniżej:

![Wersja usługi Azure AD Connect w Panelu sterowania](./media/reference-connect-faq/faq1.png)

**Pyt.: Jak uaktualnić do najnowszej wersji programu Azure AD Connect?**  
Aby dowiedzieć się, jak uaktualnić do najnowszej wersji, zobacz [program Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](how-to-upgrade-previous-version.md). 

**Pyt.: Firma Microsoft już uaktualniony do najnowszej wersji programu Azure AD Connect ostatniego roku. Potrzebujemy próbę uaktualnienia?**  
Zespół usługi Azure AD Connect sprawia, że częste aktualizacje do usługi. Korzystać z poprawek i aktualizacji zabezpieczeń, a także nowe funkcje, jest aktualne serwer do najnowszej wersji. Jeśli zostanie włączone automatyczne uaktualnianie, jest automatycznie aktualizowany używanej wersji oprogramowania. Historia wersji programu Azure AD Connect, możesz znaleźć [program Azure AD Connect: Historia wersji](reference-connect-version-history.md).

**Pyt.: Jak długo trwa Aby przeprowadzić uaktualnienie i jaki jest wpływ na użytkowników?**  
Czas potrzebny do uaktualnienia, zależy od rozmiaru Twojej dzierżawy. W przypadku większych organizacji może być najlepiej wykonać uaktualnienie w wieczorami lub w weekendy. Podczas uaktualniania Brak działania synchronizacji ma miejsce.

**Pyt.: Sądzę, że uaktualnienia do usługi Azure AD Connect, ale urząd portalu wzmianki nadal narzędzia DirSync. Dlaczego jest to?**  
Zespół usługi Office działa tak, aby pobrać aktualizacje portalu pakietu Office, aby odzwierciedlić nazwę bieżącego produktu. Narzędzie synchronizacji, które są używane, nie są wyświetlane.

**Pyt.: Mój stan automatyczną aktualizację mówi "Suspended". Dlaczego jest zawieszony? Należy go włączyć?**  
Błąd został wprowadzony w poprzedniej wersji, która w pewnych okolicznościach pozostawia stan automatyczną aktualizację "Suspended". Ręczne włączanie go jest technicznie możliwe, ale wymaga podjęcia kilku czynności złożone. Najlepiej, możesz zrobić to, zainstaluj najnowszą wersję programu Azure AD Connect.

**Pyt.: Moja firma ma wymagania strict zarządzania zmianami i chcę, aby kontrolować, gdy jest przekazywane. Czy mogę kontrolować to po uruchomieniu automatycznego uaktualniania?**  
Nie ma żadnych takich funkcji już dziś. Ta funkcja jest oceniana w przyszłej wersji.

**Pyt.: Wiadomość e-mail będzie uzyskać Jeśli automatyczne uaktualnianie nie powiodło się? Skąd mam wiedzieć, czy zakończyła się pomyślnie?**  
Użytkownik nie będzie powiadamiany o wyniku uaktualnienia. Ta funkcja jest oceniana w przyszłej wersji.

**Pyt.: Oś czasu opublikować Planując gary'ego vaynerchucka automatycznych aktualizacji?**  
Automatyczne uaktualnianie jest pierwszym krokiem w procesie tworzenia wersji nowszej wersji. Zawsze, gdy nowa wersja, aktualizacje są automatycznie przekazywane. Nowsze wersje programu Azure AD Connect są wstępnie ogłoszone w [harmonogram działania dla usługi Azure AD](../fundamentals/whats-new.md).

**Pyt.: Automatyczne uaktualnianie również uaktualnić program Azure AD Connect Health?**  
Tak, automatyczne uaktualnianie powoduje także uaktualnienie programu Azure AD Connect Health.

**Pyt.: Czy można również automatyczną aktualizację serwerów Azure AD Connect w trybie przejściowym?**  
Tak, użytkownik może automatycznego uaktualniania serwera Azure AD Connect, która jest w trybie przejściowym.

**Pyt.: Jeśli serwer usługi Azure AD Connect nie rozpoczyna się automatyczne uaktualnienie nie powiedzie się, co należy zrobić?**  
W rzadkich przypadkach usługa Azure AD Connect nie uruchamia się po wykonaniu uaktualnienia. W takich przypadkach ponownego uruchomienia serwera zwykle rozwiązuje problem. Jeśli usługa Azure AD Connect nadal nie zostanie uruchomiona, otwórz bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Utwórz żądanie obsługi, aby się z pomocą techniczną usługi Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**Pyt.: Nie wiem, jakie zagrożenia są uaktualnienie do nowszej wersji programu Azure AD Connect. Można wywołać, mi pomóc uaktualnienia?**  
Jeśli potrzebujesz pomocy, uaktualnienie do nowszej wersji programu Azure AD Connect, otwórz bilet pomocy technicznej w [Utwórz żądanie obsługi, aby się z pomocą techniczną usługi Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
**Pyt.: Jak uzyskać pomoc dotyczącą usługi Azure AD Connect?**

[Wyszukiwanie w bazie wiedzy Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Wyszukaj KB dla rozwiązań technicznych typowych problemów wymagających usunięcia awarii dotyczące pomocy technicznej dla usługi Azure AD Connect.

[Azure Active Directory Forums](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Wyszukaj pytania i odpowiedzi lub Zadaj własne pytania, przechodząc do [społeczności usługi Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Uzyskaj pomoc techniczną dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
