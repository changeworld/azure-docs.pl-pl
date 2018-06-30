---
title: Azure Active Directory Connect — często zadawane pytania — | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 7edabc99da5e1466e848336c647a33213c9edd8b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132993"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect — często zadawane pytania

## <a name="general-installation"></a>Ogólne instalacji
**Pytanie: czy będzie instalacji działać, jeśli administrator globalny usługi Azure Active Directory (Azure AD) ma uwierzytelnianie dwuskładnikowe (2FA) włączone?**  
Począwszy od kompilacje lutym 2016 r. Ten scenariusz jest obsługiwany.

**Pytanie: czy istnieje sposób, aby zainstalować program Azure AD Connect nienadzorowanej?**  
Instalacja usługi Azure AD Connect jest obsługiwana tylko podczas korzystania z Kreatora instalacji. Instalacja nienadzorowana, dyskretnej nie jest obsługiwana.

**Pytanie: czy masz lasu, gdy nie można skontaktować się z jednej domeny. Jak zainstalować usługi Azure AD Connect?**  
Począwszy od kompilacje lutym 2016 r. Ten scenariusz jest obsługiwany.

**Pytanie: czy usług Azure do domeny Active Directory (Azure AD DS), agent kondycji działają w instalacji server core?**  
Tak. Po zainstalowaniu agenta można ukończyć procesu rejestracji za pomocą następującego polecenia cmdlet programu PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Pytanie: czy Azure AD Connect, obsługa synchronizacji z dwiema domenami do na usługi Azure AD?**  
Tak, ten scenariusz jest obsługiwany. Zapoznaj się [wielu domen](active-directory-aadconnect-multiple-domains.md).
 
**Pytanie: czy masz wiele łączników dla tej samej domeny usługi Active Directory w programie Azure AD Connect?**  
Nie, wiele łączników dla tej samej domeny usługi AD nie są obsługiwane. 

**Pytanie: czy można przenieść bazę danych usługi Azure AD Connect z lokalnej bazy danych do zdalnego wystąpienia programu SQL Server?**   
Tak, w poniższych krokach podano ogólne wskazówki, jak to zrobić. Obecnie pracujemy nad dokumentem bardziej szczegółowych.
1. Utwórz kopię zapasową bazy danych LocalDB ADSync.
Jest najprostszym sposobem, w tym celu można użyć programu SQL Server Management Studio zainstalowany na tym samym komputerze co usługa Azure AD Connect. Połączyć się z *(localdb)\.\ADSync*, a następnie wykonaj kopię zapasową bazy danych programu ADSync.

2. Przywróć bazę danych ADSync do zdalnego wystąpienia programu SQL Server.

3. Azure AD Connect z istniejącej instalacji [zdalnej bazy danych SQL](active-directory-aadconnect-existing-database.md).
   Artykuł pokazuje, jak przeprowadzić migrację do przy użyciu lokalnej bazy danych SQL. W przypadku migracji do korzystania ze zdalną bazą danych SQL w kroku 5 procesu należy również wprowadzić istniejącego konta usługi, który będzie Uruchom jako dla usługi synchronizacji usługi Windows. To konto usługi aparatu synchronizacji jest opisane tutaj:
   
      **Użyj istniejącego konta usługi**: Domyślnie program Azure AD Connect używa konta usług wirtualnych dla usługi synchronizacji do użycia. Jeśli należy użyć zdalnego wystąpienia programu SQL Server lub serwer proxy, który wymaga uwierzytelniania, użyj zarządzane konto usługi lub konto usługi w domenie, a znać hasło. W takich przypadkach wprowadź konto do użycia. Upewnij się, że użytkowników, którzy uruchomili instalację są administratorów systemu w języku SQL, dzięki czemu można utworzyć poświadczenia logowania dla konta usługi. Aby uzyskać więcej informacji, zobacz [konta Azure AD Connect i uprawnienia](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). 
   
      Od ostatniej kompilacji aprowizowanie bazy danych może wykonać poza pasmem administrator usługi SQL, a jej instalację może następnie przeprowadzić administrator programu Azure AD Connect z uprawnieniami właściciela bazy danych. Aby uzyskać więcej informacji, zobacz [instalowania programu Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](active-directory-aadconnect-sql-delegation.md).

Aby zachować proste rzeczy, zaleca się użytkowników, którzy instalują Azure AD Connect administratorów systemu w języku SQL. Jednak z kompilacjami ostatnie można teraz Użyj delegowanych administratorów SQL, zgodnie z opisem w [instalowania programu Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Sieć
**Pytanie: czy masz zapory, urządzenia sieciowego lub czegoś innego, która ogranicza podczas połączenia można pozostają otwarte w sieci. Co Moja próg limitu czasu po stronie klienta należy podczas korzystania z usługi Azure AD Connect?**  
Całe oprogramowanie sieciowe, urządzenia fizyczne lub czymkolwiek innym, która ogranicza maksymalny czas, który połączenia może pozostawać otwarte, powinna używać progu co najmniej pięć minut (300 sekund) dla połączenia między serwerami, na którym jest zainstalowany klient programu Azure AD Connect i Azure Active Directory. To zalecenie ma również zastosowanie do wszystkich wydanych narzędzi synchronizacji pakietu Microsoft Identity.

**Pytanie: czy domen (przez) obsługiwane?**  
Nie, usługi Azure AD Connect nie obsługuje lokalnego lasu lub domeny, które Użyj domeny drugiego poziomu.

**Pyt.: czy lasów domenom AD rozłącznego obsługiwane?**  
Nie, usługi Azure AD Connect nie obsługuje lokalnymi lasami, zawierające rozłączne przestrzenie nazw.

**Pytanie: czy "kropkami" nazwy NetBIOS obsługiwane?**  
Nie, usługi Azure AD Connect nie obsługuje lokalnego lasu lub domeny, której nazwa NetBIOS zawiera kropkę (.).

**Pytanie: jest czysty środowisko IPv6 obsługiwane?**  
Nie, usługi Azure AD Connect nie obsługuje czystego środowiska protokołu IPv6.

## <a name="federation"></a>Federacja
**Pytanie: co należy zrobić, jeśli I otrzymasz wiadomość e-mail, który żąda odnawiania certyfikatu usługi Office 365?**  
Aby uzyskać wskazówki dotyczące odnawiania certyfikatu, zobacz [odnawiania certyfikatów](active-directory-aadconnect-o365-certs.md).

**Pytanie: czy masz "Automatycznie Aktualizuj jednostki uzależnionej" dla jednostki zależnej usługi Office 365. Czy muszę wykonywać żadnych czynności w przypadku, gdy mój token certyfikatu podpisywania automatycznie najedzie na?**  
Użyj wskazówek opisaną w artykule [odnawiania certyfikatów](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Środowisko
**Q: Zmień nazwę serwera, po zainstalowaniu usługi Azure AD Connect jest obsługiwane?**  
Nie. Zmiana nazwy serwera renderuje aparatu synchronizacji nie można nawiązać połączenia z wystąpieniem bazy danych SQL i nie można uruchomić usługi.

## <a name="identity-data"></a>Danych tożsamości
**Pytanie: Dlaczego atrybutu userPrincipalName (UPN) w usłudze Azure AD nie odpowiada lokalną nazwą UPN**  
Aby uzyskać informacje zobacz następujące artykuły:

* [Nazwy użytkowników w usłudze Office 365, Azure lub Intune nie są zgodne lokalną nazwą UPN lub alternatywnym Identyfikatorem logowania](https://support.microsoft.com/en-us/kb/2523192)
* [Zmiany nie są synchronizowane przez narzędzie do synchronizacji usługi Azure Active Directory po zmianie nazwy UPN konta użytkownika, aby użyć innej domeny federacyjnej](https://support.microsoft.com/en-us/kb/2669550)

Można również skonfigurować usługi Azure AD, aby umożliwić aparatu synchronizacji można zaktualizować nazwy UPN, zgodnie z opisem w [funkcji Usługa synchronizacji Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

**Pytanie: czy jest obsługiwany soft-match lokalnej usługi Azure AD grupę lub obiekt kontaktu z istniejącą grupą usługi Azure AD, lub skontaktuj się z obiektu?**  
Tak, to elastyczne dopasowanie jest oparta na proxyAddress. Elastyczne dopasowywanie nie jest obsługiwana dla grup, które nie są włączone do wiadomości.

**Pytanie: czy jest obsługiwany ręcznie ustawić atrybut nazwę ImmutableId dla istniejącej grupy usługi Azure AD, lub skontaktuj się z obiektu twardych dopasowania go do lokalnej grupy usługi Azure AD, lub skontaktuj się z obiektu?**  
Nie, Ręczne ustawianie atrybutu nazwę ImmutableId na istniejącej grupy usługi Azure AD lub skontaktuj się z pomocą obiektów do twardych dopasowania, jego aktualnie nie jest obsługiwane.

## <a name="custom-configuration"></a>Konfiguracja niestandardowa
**Pytanie: gdzie są udokumentowane poleceń cmdlet programu PowerShell dla usługi Azure AD Connect**  
Z wyjątkiem poleceń cmdlet są opisane w tej witrynie innych poleceń cmdlet programu PowerShell znaleziono w programie Azure AD Connect nie są obsługiwane przez klienta.

**Pytanie: czy można użyć opcji "Serwer serwer eksportu/importu" został znaleziony w Synchronization Service Manager na przenoszenie konfiguracji między serwerami?**  
Nie. Ta opcja nie pobiera wszystkie ustawienia konfiguracji i nie powinna być używana. Zamiast tego użyj kreatora, aby utworzyć konfigurację podstawową na drugim serwerze i generowania skryptów programu PowerShell, aby przenieść wszystkie reguły niestandardowej między serwerami za pomocą edytora reguły synchronizacji. Aby uzyskać więcej informacji, zobacz [migracji w kierunku](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**Pytanie: hasła można buforować Azure strony logowania i może to buforowanie blokowane, ponieważ zawiera on element input hasła z *autouzupełniania = "false"* atrybut?**  
Obecnie modyfikowanie atrybutów HTML **hasło** pole, w tym autocomplete tag nie jest obsługiwane. Obecnie pracujemy nad funkcją, która służy do niestandardowych skryptów JavaScript, które umożliwia dodanie atrybutu do **hasło** pola.

**Pytanie: Azure logowania zostanie wyświetlona nazw użytkowników, użytkowników, którzy wcześniej zalogowali się pomyślnie. Można to zachowanie wyłączyć?**  
Obecnie modyfikowanie atrybutów HTML **hasło** pola wejściowego, w tym autocomplete tag nie jest obsługiwane. Obecnie pracujemy nad funkcją, która służy do niestandardowych skryptów JavaScript, które umożliwia dodanie atrybutu do **hasło** pola.

**Pytanie: czy istnieje sposób, aby zapobiec równoczesnych sesji?**  
Nie.

## <a name="auto-upgrade"></a>Automatycznego uaktualnienia

**Pytanie: jakie są zalety i skutków przy użyciu automatycznego uaktualnienia?**  
Firma Microsoft udzielanie porad wszystkich klientów, aby włączyć automatycznego uaktualnienia ich instalacji Azure AD Connect. Korzyścią jest zawsze wyświetlany najnowszych poprawek, w tym aktualizacje zabezpieczeń luk w zabezpieczeniach, które zostały znalezione w programie Azure AD Connect. Proces uaktualniania jest bezproblemowego i odbywa się automatycznie, gdy dostępna jest nowa wersja. Tysiące klientów Azure AD Connect za pomocą automatycznego uaktualnienia każdej nowej wersji.

Proces automatycznej aktualizacji zawsze najpierw określi, czy instalacja uprawnia do skorzystania z automatycznego uaktualnienia. Jeśli jest ona uprawniona, uaktualnianie jest przeprowadzane i przetestowane. Proces obejmuje również wyszukiwanie niestandardowe zmiany zasad i szczególnych czynników środowiska. Jeśli testy wykazują, że uaktualnienie zakończy się niepowodzeniem, zostanie automatycznie przywrócony poprzedniej wersji.

W zależności od wielkości środowiska proces może zająć kilka godzin. Podczas uaktualniania jest w toku, brak synchronizacji między Windows Server Active Directory i Azure AD się nie dzieje.

**Pytanie: czy mogę odebrał wiadomość e-mail z informacją, Moje automatycznego uaktualnienia przestanie działać, którą należy zainstalować nową wersję. Dlaczego trzeba to zrobić?**  
W zeszłym roku firma Microsoft opublikowała wersję programu Azure AD Connect, które w pewnych okolicznościach może wyłączyć funkcję automatycznej aktualizacji na serwerze. W programie Azure AD Connect wersji 1.1.750.0 został rozwiązany problem. Jeśli użytkownik wpłynęła problem, można ograniczyć, uruchamiając skrypt programu PowerShell w celu jego naprawy lub przez ręczne uaktualnienie do najnowszej wersji programu Azure AD Connect. 

Aby uruchomić skrypt programu PowerShell [pobrać skryptu](https://aka.ms/repairaadconnect) i uruchom go na serwerze usługi Azure AD Connect administracyjne okno programu PowerShell. Aby dowiedzieć się, jak uruchomić skrypt, [wyświetlić ten krótki film](https://aka.ms/repairaadcau).

Należy ręcznie uaktualnić należy pobrać i uruchomić najnowszą wersję pliku AADConnect.msi.
 
-  Jeśli bieżąca wersja jest starsza niż 1.1.750.0, [pobierania i uaktualnić do najnowszej wersji](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Jeśli wersja Azure AD Connect jest 1.1.750.0 lub później, są wymagane żadne dalsze akcje. Już korzystasz z wersji, zawierający poprawkę automatycznej aktualizacji. 

**Pytanie: czy mogę odebrał wiadomość e-mail z informacją, aby uaktualnić do najnowszej wersji, aby ponownie włączyć automatycznego uaktualnienia. Używana wersja 1.1.654.0. Należy uaktualnić?**  
Tak, należy uaktualnić do wersji 1.1.750.0 lub nowszej, ponowne włączenie automatycznego uaktualnienia. [Pobierz i Uaktualnij do najnowszej wersji](https://www.microsoft.com/en-us/download/details.aspx?id=47594).

**Pytanie: czy mogę odebrał wiadomość e-mail z informacją, aby uaktualnić do najnowszej wersji, aby ponownie włączyć automatycznego uaktualnienia. Włączenie automatycznego uaktualniania I użycia programu PowerShell, nadal należy zainstalować najnowszą wersję?**  
Tak, nadal należy uaktualnić do wersji 1.1.750.0 lub nowszej. Włączanie automatycznej aktualizacji usługi przy użyciu programu PowerShell nie chroni przed problem automatycznej aktualizacji w wersji sprzed 1.1.750.0.

**Pytanie: czy chcesz uaktualnić do nowszej wersji, ale nie wiem, którzy zainstalowali Azure AD Connect, a nie ma nazwy użytkownika i hasła. Jest to potrzebne?**
Nie trzeba znać nazwy użytkownika i hasła, który początkowo został użyty do uaktualnienia Azure AD Connect. Użyj dowolnego konta usługi Azure AD mającego rolę administratora globalnego.

**Pytanie: jak znaleźć używam wersji programu Azure AD Connect?**  
Aby sprawdzić, która wersja programu Azure AD Connect jest zainstalowana na serwerze, przejdź do panelu sterowania i wyszukać zainstalowana wersja programu Microsoft Azure AD Connect, wybierając **programy** > **programy i funkcje** , jak pokazano poniżej:

![Wersja usługi Azure AD Connect w Panelu sterowania](media/active-directory-aadconnect-faq/faq1.png)

**Pytanie: jak uaktualnić do najnowszej wersji programu Azure AD Connect?**  
Aby dowiedzieć się, jak uaktualnić do najnowszej wersji, zobacz [Azure AD Connect: uaktualnianie z poprzedniej wersji do najnowszej wersji](active-directory-aadconnect-upgrade-previous-version.md). 

**Pytanie: czy mamy już uaktualniony do najnowszej wersji programu Azure AD Connect ostatni rok. Potrzebujemy próbę uaktualnienia?**  
Zespół usługi Azure AD Connect sprawia, że częste aktualizacji do usługi. Korzystać z poprawki i aktualizacje zabezpieczeń, a także nowe funkcje, jest aktualne serwer do najnowszej wersji. Włączenie automatycznego uaktualnienia wersji oprogramowania jest aktualizowany automatycznie. Historia wersji programu Azure AD Connect znaleźć [Azure AD Connect: Historia wersji](active-directory-aadconnect-version-history.md).

**Pytanie: jak długo trwa do uaktualnienia, a co jest wpływ na Moje użytkowników?**  
Czas potrzebny do uaktualnienia, zależy od rozmiaru sieci dzierżawcy. W przypadku większych organizacji może być najlepiej wykonać uaktualnienie w wieczorem lub w weekendy. Podczas uaktualniania nie działania synchronizacji ma miejsce.

**Pytanie:, który I uważają, że uaktualnienia do usługi Azure AD Connect, ale portalu usługi Office nadal wymienia narzędzia DirSync. Dlaczego jest to?**  
Zespół pakietu Office pracuje nad Pobierz aktualizacje portalu Office w celu odzwierciedlenia bieżącej nazwy produktu. Narzędzie synchronizacji, które są używane, nie są widoczne.

**P: Mój stan automatycznego uaktualnienia jest wyświetlany komunikat "Zawieszone." Dlaczego jest wstrzymana? Należy go włączyć?**  
Wprowadzono usterki w poprzedniej wersji, która w pewnych okolicznościach pozostawia stan automatycznego uaktualnienia "Zawieszone." Ręczne włączenie jej jest technicznie możliwe, ale wymaga wykonania kilku czynności złożonych. Najlepiej, które może wykonywać jest, zainstaluj najnowszą wersję programu Azure AD Connect.

**Pytanie: mojej firmy mają wymagania strict zarządzania zmianami i warto kontrolować, kiedy ma wypchnięty. Czy można kontrolować, po uruchomieniu automatycznego uaktualnienia?**  
Nie, obecnie jest nie tych funkcji. Funkcja jest oceniane w przyszłości.

**Q: wiadomość e-mail będzie uzyskać Jeśli automatycznego uaktualnienia nie powiodło się? Jak wiedzą, że nie powiodło się?**  
Nie dowiesz się wyniku uaktualnienia. Funkcja jest oceniane w przyszłości.

**Pytanie: czy opublikować oś czasu Planując Przekładanie automatycznego uaktualnienia?**  
Automatyczne uaktualnianie jest pierwszym krokiem w procesie wersji nowszej wersji. Gdy istnieje nowej wersji, są automatycznie przekazywane uaktualnienia. Są wstępnie ogłoszone w nowszej wersji programu Azure AD Connect [planu usługi Azure AD](../fundamentals/whats-new.md).

**Pytanie: czy usługa Azure AD Connect Health jest również uaktualnić automatycznego uaktualnienia?**  
Tak, automatycznie upgrade uaktualnia także Azure AD Connect Health.

**Pytanie: czy można również automatycznej aktualizacji serwerów Azure AD Connect w tryb przejściowy?**  
Tak, użytkownik może automatycznej aktualizacji z serwera usługi Azure AD Connect, który znajduje się w trybie przejściowym.

**Pytanie: czy automatyczne uaktualnienie nie powiedzie się i serwer Azure AD Connect nie uruchamia się, co należy zrobić?**  
W rzadkich przypadkach usługi Azure AD Connect nie uruchamia się po wykonaniu uaktualnienia. W takich przypadkach zwykle ponownego uruchomienia serwera rozwiązuje problem. Jeśli usługa Azure AD Connect nadal nie zostanie uruchomiony, otwórz bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Utwórz żądanie obsługi, aby się z pomocą techniczną usługi Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**Pytanie: nie mam pewności co ryzyko jest uaktualnienie do nowszej wersji programu Azure AD Connect. Można wywołać, aby pomóc uaktualnienia?**  
Jeśli potrzebujesz pomocy, uaktualnienie do nowszej wersji programu Azure AD Connect, otwórz bilet pomocy technicznej w [Utwórz żądanie obsługi, aby się z pomocą techniczną usługi Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
**Pytanie: jak uzyskać pomoc dotyczącą usługi Azure AD Connect?**

[Wyszukaj w bazie wiedzy Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Wyszukaj bazy wiedzy technicznej rozwiązania typowych problemów naprawa w razie awarii o obsłudze programu Azure AD Connect.

[Fora dotyczące usługi Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Wyszukaj pytania i odpowiedzi lub zadać pytania własnych, przechodząc do [społeczności usługi Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Uzyskać pomoc techniczną dotyczącą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
