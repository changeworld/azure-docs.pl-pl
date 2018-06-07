---
title: 'Azure Active Directory Connect: Często zadawane pytania dotyczące - | Dokumentacja firmy Microsoft'
description: Ta strona zawiera często zadawane pytania dotyczące usługi Azure AD Connect.
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
ms.openlocfilehash: 4cef685d71a64f8a6681a3449e4fe0b67899c67c
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808608"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Często zadawane pytania dotyczące usługi Azure Active Directory Connect

## <a name="general-installation"></a>Ogólne instalacji
**Pytanie: będzie instalacji działać, jeśli administrator globalny usługi Azure AD ma 2FA włączone?**  
Ten scenariusz jest obsługiwany z kompilacji od lutego 2016 r.

**Pytanie: czy istnieje sposób, aby zainstalować program Azure AD Connect nienadzorowanej?**  
Możliwe jest tylko zainstalować program Azure AD Connect przy użyciu Kreatora instalacji. Instalacja nienadzorowana i dyskretnej nie są obsługiwane.

**Pytanie: czy masz lasu, gdy nie można skontaktować się z jednej domeny. Jak zainstalować usługi Azure AD Connect?**  
Ten scenariusz jest obsługiwany z kompilacji od lutego 2016 r.

**Pytanie: agenta programu health usług AD DS działa w instalacji server core?**  
Tak. Po zainstalowaniu agenta, można ukończyć procesu rejestracji, za pomocą następującego polecenia cmdlet programu PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Pytanie: czy programu AADConnect obsługuje synchronizowane z obiema domenami do na usługi Azure AD?**</br>
Tak, ten scenariusz jest obsługiwany. Zapoznaj się [wielu domen](active-directory-aadconnect-multiple-domains.md)
 
**Pytanie: czy masz wiele łączników dla tej samej domeny usługi Active Directory w usłudze Azure AD connect?**</br> Nie, wiele łączników dla tej samej domeny usługi AD nie są obsługiwane. 

**Pytanie: czy można przenieść bazę danych usługi Azure AD Connect z lokalnej bazy danych na zdalnym serwerze SQL?**</br> Tak, poniższe kroki zawierają ogólne wytyczne dotyczące jak to zrobić.  Obecnie pracujemy nad bardziej szczegółowe dokumentu, która będzie dostępna wkrótce.


   1. Kopia zapasowa bazy danych "ADSync" LocalDB Najprostszym sposobem, aby to zrobić polega na użyciu programu SQL Server Management Studio zainstalowany na tym samym komputerze co usługa Azure AD Connect. Połączyć się z "(localdb)\.\ADSync" — następnie utwórz kopię zapasową bazy danych ADSync
   2. Przywróć bazę danych "ADSync" do Twojego wystąpienia zdalnego SQL
   3. Azure AD Connect z istniejącej instalacji [zdalnej bazy danych SQL](active-directory-aadconnect-existing-database.md) łącze przedstawiono czynności wymagane podczas migracji przy użyciu lokalnej bazy danych SQL. W przypadku migracji do korzystania ze zdalną bazą danych SQL w kroku 5 tego procesu zostanie również należy wprowadzić istniejącego konta usługi synchronizacji usługi Windows będzie uruchamiana jako. To konto usługi aparatu synchronizacji jest opisane tutaj:</br></br>
   **Użyj istniejącego konta usługi**— domyślnie Azure AD Connect używa konta usług wirtualnych dla usługi synchronizacji do użycia. Jeśli zdalnego programu SQL Server lub serwer proxy, który wymaga uwierzytelniania, należy użyć konta usługi zarządzanego lub użyć konta usługi w domenie i znać hasło. W takich przypadkach wprowadź konto do użycia. Upewnij się, że użytkownik wykonujący instalację jest administratorem systemu na serwerze SQL, aby można było utworzyć identyfikator logowania dla konta usługi. Zobacz temat [Azure AD Connect accounts and permissions (Konta i uprawnienia w programie Azure AD Connect)](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account).</br></br> Od ostatniej kompilacji aprowizowanie bazy danych może wykonać poza pasmem administrator usługi SQL, a jej instalację może następnie przeprowadzić administrator programu Azure AD Connect z uprawnieniami właściciela bazy danych. Aby uzyskać więcej informacji, zobacz temat [Install Azure AD Connect using SQL delegated administrator permissions (Instalowanie programu Azure AD Connect za pomocą delegowanych uprawnień administratora usługi SQL)](active-directory-aadconnect-sql-delegation.md).

Aby zapewnić proste zaleca się, że użytkownik instalujący Azure AD Connect jest Skojarzenie w programie SQL. (Jednak o najnowszych wersjach można teraz używać delegowanego administratora SQL, zgodnie z opisem [tutaj](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Sieć
**Pytanie: czy mam zapory, urządzenia sieciowego lub czegoś innego, która ogranicza maksymalny czas połączenia pozostają otwarte w sieci. Jak długo Moje próg limitu czasu po stronie klienta należy przy użyciu usługi Azure AD Connect?**  
Całe oprogramowanie sieciowe, urządzenia fizyczne lub czymkolwiek innym, która ogranicza maksymalny czas połączenia może pozostawać otwarte, powinna używać progu co najmniej 5 minut (300 sekund) dla połączenia między serwerami, na którym jest zainstalowany klient programu Azure AD Connect i Usługa Azure Active Directory. To zalecenie ma również zastosowanie do wszystkich wydanych narzędzi synchronizacji pakietu Microsoft Identity.

**Pytanie: czy domeny drugiego poziomu (jednej etykiety domen) obsługiwane?**  
Nie, usługi Azure AD Connect nie obsługuje lokalnymi lasami/domenami przy użyciu drugiego poziomu.

**Pyt.: czy lasów domenom AD rozłącznego obsługiwane?**  
Nie, usługi Azure AD Connect nie obsługuje lokalnymi lasami zawierający rozłączne przestrzenie nazw.

**Pytanie: czy "kropkami" o nazwie NetBios obsługiwane?**  
Nie, usługi Azure AD Connect nie obsługuje lokalnymi lasami/domenami, których nazwa NetBios zawiera kropkę "." w nazwie.

**Pytanie: jest czysty środowisko IPv6 obsługiwane?**  
Nie, usługi Azure AD Connect nie obsługuje czystego środowiska protokołu IPv6.

## <a name="federation"></a>Federacja
**Pytanie: co należy zrobić, jeśli otrzymuję wiadomość e-mail, który monit o odnawiania certyfikatu usługi Office 365**  
Użyj wskazówek opisaną w [odnawiania certyfikatów](active-directory-aadconnect-o365-certs.md) dokumentu dotyczące sposobu odnawiania certyfikatu.

**Pytanie: czy masz "Automatycznie Aktualizuj jednostki uzależnionej" dla jednostki uzależnionej usługi Office 365. Czy muszę wykonywać żadnych czynności w przypadku, gdy mój token certyfikatu podpisywania automatycznie najedzie na?**  
Użyj wskazówek opisaną w artykule [odnawiania certyfikatów](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Środowisko
**Q: Zmień nazwę serwera, po zainstalowaniu usługi Azure AD Connect jest obsługiwane?**  
Nie. Zmiana nazwy serwera spowoduje, że aparat synchronizacji nie będą mogli nawiązywać połączeń z bazą danych SQL i usługa nie będzie możliwe jej uruchomienie.

## <a name="identity-data"></a>Danych tożsamości
**Pyt.: atrybut nazwy UPN (userPrincipalName) w usłudze Azure AD nie odpowiada UPN lokalnego — Dlaczego?**  
Zobacz następujące artykuły:

* [Niezgodne nazwy użytkowników w usłudze Office 365, Azure lub Intune lokalną nazwą UPN lub alternatywnym Identyfikatorem logowania](https://support.microsoft.com/en-us/kb/2523192)
* [Zmiany nie są synchronizowane przez narzędzie do synchronizacji usługi Azure Active Directory po zmianie nazwy UPN konta użytkownika, aby użyć innej domeny federacyjnej](https://support.microsoft.com/en-us/kb/2669550)

Można również skonfigurować usługi Azure AD, aby umożliwić aparatu synchronizacji można zaktualizować właściwości userPrincipalName zgodnie z opisem w [funkcji Usługa synchronizacji Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

**P: jest obsługiwane nietrwałego dopasowania grupy AD skontaktuj się z obiekty z istniejących obiektów usługi Azure AD grupy/skontaktuj się z lokalnymi?**  
Tak, proxyAddress oparciu tego dopasowania nietrwałego.  Elastyczne dopasowywanie nie jest obsługiwana dla grup, które nie są włączone do wiadomości.

**Pytanie: czy jest ona obsługiwana na ręczne ustawienie atrybutu nazwę ImmutableId w istniejących obiektach Azure AD grupy/skontaktuj się z twardego dopasowanie do obiektów grupy AD skontaktuj się z lokalnymi?**  
Nie, ręcznie Ustawianie nazwę ImmutableId atrybut na istniejący obiekt Azure AD grupy/skontaktuj się z do dopasowania twardych go aktualnie nie jest obsługiwane.

## <a name="custom-configuration"></a>Konfiguracja niestandardowa
**Pytanie: gdzie są udokumentowane poleceń cmdlet programu PowerShell dla usługi Azure AD Connect**  
Z wyjątkiem poleceń cmdlet opisane w tej lokacji innych poleceń cmdlet programu PowerShell znaleziono w programie Azure AD Connect nie są obsługiwane przez klienta.

**Pytanie: czy można użyć "Serwera serwer eksportu/importu" znaleziony w *Menedżera usługi synchronizacji* na przenoszenie konfiguracji między serwerami?**  
Nie. Ta opcja nie pobierze wszystkie ustawienia konfiguracji i nie powinna być używana. Zamiast tego należy użyć Kreatora tworzenia konfiguracji podstawowej na drugim serwerze i generowania skryptów programu PowerShell, aby przenieść wszystkie reguły niestandardowej między serwerami za pomocą edytora reguły synchronizacji. Zobacz [migracji w kierunku](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**Pytanie: hasła można buforować Azure strony logowania i może to być zablokowana, ponieważ zawiera on element input hasła z automatycznego uzupełniania = "false" atrybutu?**</br>
Obecnie modyfikowanie atrybutów HTML pole wprowadzania hasła, tym autocomplete tag nie jest obsługiwane. Obecnie trwa praca funkcja, która pozwoli niestandardowych skryptów Javascript, umożliwiające dodanie atrybutu do pola hasła.

**Pytanie: czy w witrynie Azure logowania są wyświetlane nazwy użytkowników dla użytkowników, którzy wcześniej zalogowali się pomyślnie.  Można to zachowanie wyłączyć?**</br>
Obecnie modyfikowanie atrybutów HTML pole wprowadzania hasła, tym autocomplete tag nie jest obsługiwane. Obecnie trwa praca funkcja, która pozwoli niestandardowych skryptów Javascript, umożliwiające dodanie atrybutu do pola hasła.

**Pytanie: czy istnieje sposób, aby zapobiec równoczesnych sesji?**</br>
Nie.

## <a name="auto-upgrade"></a>Automatycznego uaktualnienia

**Pytanie: jakie są zalety i skutków przy użyciu automatycznego uaktualnienia?**</br>
Wszyscy klienci są zalecane jest włączenie automatycznego uaktualniania dla ich instalacji Azure AD Connect. Korzyści są zawsze otrzymają najnowszych poprawek, w tym aktualizacje zabezpieczeń luk w zabezpieczeniach, które zostały znalezione w programie Azure AD Connect. Proces uaktualniania jest bezproblemowego i nastąpi automatycznie natychmiast dostępna jest nowa wersja. Tysiące klientów Azure AD Connect za pomocą automatycznego uaktualnienia każdej nowej wersji.

Proces uaktualniania automatycznie zawsze najpierw ustalają, czy instalacja kwalifikuje się do automatycznego uaktualnienia (proces ten obejmuje wyszukiwanie niestandardowe zmiany zasad, szczególnych czynników środowiska itp.), a jeśli tak, uaktualnianie jest przeprowadzane i przetestowane. Jeśli testy wykazują uaktualnienia zakończyła się niepowodzeniem, Pobierz zostanie automatycznie przywrócona poprzednia wersja.

W zależności od wielkości środowiska proces może zająć kilka godzin, a a odbywa się uaktualnienie, nastąpi Brak synchronizacji między systemu Windows Server AD i Azure AD.

**Pytanie: czy mogę odebrał wiadomość e-mail z informacją, Moje automatycznego uaktualnienia przestanie działać, którą należy zainstalować nową wersję. Dlaczego trzeba to zrobić?**</br>
Została wydana w zeszłym roku wersji programu Azure AD Connect, które w pewnych okolicznościach, być może został wyłączony funkcja automatycznego uaktualniania na serwerze. Ten problem został rozwiązany w programie Azure AD Connect wersji 1.1.750.0. Klienci, którzy mogą dotyczy ten problem należy ręcznie uaktualnić do najnowszej wersji programu Azure AD Connect złagodzić problem. Należy ręcznie uaktualnić należy pobrać i uruchomić najnowszą wersję pliku AADConnect.msi.
 
-  Jeśli bieżąca wersja jest starsza niż 1.1.750.0, należy uaktualnić do najnowszej wersji [który można pobrać tutaj](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Jeśli wersja programu Azure AD Connect jest 1.1.750.0 lub nowszego, nie trzeba wykonywać żadnych czynności w celu złagodzenia problem uaktualniania automatycznie, jako użytkownik jest już w wersji, która ma poprawkę dla tego. 

**Pytanie: czy mogę odebrał wiadomość e-mail z informacją, aby uaktualnić do najnowszej wersji, aby ponownie włączyć automatycznego uaktualnienia. Jestem na 1.1.654.0, należy uaktualnić?** </br>    
Tak, należy uaktualnić do 1.1.750 lub nowszej ponowne włączenie automatycznego uaktualnienia. Oto link, który objaśnia, jak uaktualnić do nowszej wersji

**Pytanie: czy mogę odebrał wiadomość e-mail z informacją, aby uaktualnić do najnowszej wersji, aby ponownie włączyć automatycznego uaktualnienia. Używam programu PowerShell włączenie automatycznego uaktualniania, nadal należy zainstalować najnowszą wersję?**</br>    
Tak, nadal należy uaktualnić do wersji 1.1.750.0 lub nowszej. Włączanie automatycznego uaktualniania usługi przy użyciu programu PowerShell nie chroni przed automatycznego uaktualniania problem w wersji sprzed 1.1.750

**Pytanie: czy chcesz uaktualnić do nowszej wersji, ale nie wiem, którzy zainstalowali Azure AD Connect i nie ma nazwy użytkownika i hasła.  Jest to potrzebne?**</br>
Nie trzeba znać nazwę użytkownika i hasło, który początkowo został użyty do uaktualnienia Azure AD Connect — konto usługi Azure AD, które ma rolę administratora globalnego może być używane.

**Pytanie: jak znaleźć, którą wersję programu Azure AD Connect jestem na?**</br>   
Aby sprawdzić, która wersja programu Azure AD Connect jest zainstalowana na serwerze, przejdź do panelu sterowania i wyszukać zainstalowana wersja programu Microsoft Azure AD Connect w "Programy > programy i funkcje":

![wersja](media/active-directory-aadconnect-faq/faq1.png)

**Pytanie: jak wykonać uaktualnienie do najnowszej wersji programu AADConnect?**</br>    
To [artykułu](active-directory-aadconnect-upgrade-previous-version.md) opisano sposób uaktualnienia do nowszej wersji. 

**Pytanie: czy mamy jest już uaktualniona do najnowszej wersji programu AADConnect ostatni rok, potrzebujemy próbę uaktualnienia?**</br> Zespoły Azure AD Connect powoduje częste aktualizacji do usługi i jest ważne, czy serwer jest aktualny i najnowszą wersję, aby korzystać z poprawki i aktualizacje zabezpieczeń, a także nowe funkcje. Włączenie automatycznego uaktualnienia używanej wersji oprogramowania zostaną automatycznie zaktualizowane. Aby znaleźć Historia wersji programu Azure AD Connect, wykonaj to [łącze](active-directory-aadconnect-version-history.md).

**Pytanie: jak długo trwa do przeprowadzenia uaktualnienia oraz jaki jest wpływ na Moje użytkowników?**</br>    
Czas potrzebny do uaktualniania zależy od rozmiaru sieci dzierżawcy, a w przypadku większych organizacji może być najlepszym w tym celu w wieczorem lub w weekendy. Podczas uaktualniania nie działania synchronizacji ma miejsce.

**Pytanie: czy mogę podejrzeń uaktualnienia do programu AADConnect, ale w portalu usługi Office nadal zawiera informacje o narzędzia DirSync.  Dlaczego jest to?**</br>    
Zespół pakietu Office działa, aby pobrać aktualizacje portalu pakietu Office w celu odzwierciedlenia bieżącej nazwy produktu — nie odzwierciedlają narzędzie synchronizacji, które są używane.

**Pytanie: czy mogę sprawdzić Mój stan automatycznej aktualizacji i wyświetlany jest tekst "Wstrzymano". Dlaczego jest wstrzymana? Należy go włączyć?**</br>     
Błąd został wprowadzony w poprzedniej wersji, która w pewnych okolicznościach, spowoduje automatyczne uaktualnienia stan "zawieszone". Ręczne włączenie jej jest technicznie możliwe, ale wymaga wykonania kilku czynności złożone, dlatego najlepiej, należy zainstalować najnowszą wersję programu Azure AD Connect

**Pytanie: czy Moja firma ma wymagania dotyczące zarządzania ograniczeniami zmiany i warto kontrolować, gdy jest on umieszczony. Czy można kontrolować, po uruchomieniu automatycznego uaktualnienia?**</br> Nie istnieje obecnie ma takich funkcji, ta funkcja jest coś, co jest obliczenia w przyszłości.

**Q: wiadomość e-mail zostanie uzyskać Jeśli automatycznej aktualizacji nie powiodło się? Jak wiedzą, że nie powiodło się?**</br>     
Nie otrzymasz powiadomienie dotyczące wyniku uaktualnienia, ta funkcja jest coś, co jest w trakcie oceny w przyszłości.

**Q:do publikować osi czasu, aby podczas planowania Przekładanie automatycznego uaktualnienia?**</br>    
Automatycznej aktualizacji jest pierwszym krokiem w procesie wersji w nowszej wersji, więc ilekroć jest nowa wersja automatycznego uaktualnienia wypychanie zostanie wykonane. Są wstępnie ogłoszone w nowszej wersji programu Azure AD Connect [planu usługi Azure AD](../../active-directory/whats-new.md).

**Pytanie: automatycznej aktualizacji uaktualnienia AAD Connect Health?**</br>   Tak, automatycznego uaktualnienia uaktualnia AAD Connect Health

**Pytanie: czy też automatycznej aktualizacji serwerów AAD Connect w tryb przejściowy?**</br>   
Nie, możesz nie automatycznej aktualizacji z serwera usługi Azure AD Connect, który znajduje się w trybie przejściowym.

**Pytanie: czy automatycznej aktualizacji nie powiedzie się i serwer AAD Connect nie można uruchomić, co należy zrobić?**</br>   
W rzadkich przypadkach usługa Azure AD Connect nie zostanie uruchomiona po wykonaniu uaktualnienia. W takich przypadkach ponowny rozruch serwera, który zazwyczaj rozwiązuje problem. Jeśli usługa Azure AD Connect nadal nie zostanie uruchomiony, otwórz bilet pomocy technicznej. Oto [łącze](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) który wyjaśnia, jak to zrobić. 

**Pytanie: nie mam pewności co ryzyko jest podczas uaktualniania do nowszej wersji programu Azure AD Connect. Można wywołać, aby pomóc uaktualnienia?**</br>
Jeśli potrzebujesz pomocy, uaktualnienie do nowszej wersji programu Azure AD Connect, otwórz bilet pomocy technicznej, w tym miejscu jest [łącze](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) który pokazuje, jak to zrobić.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
**Pytanie: jak uzyskać pomoc dotyczącą usługi Azure AD Connect?**

[Wyszukaj w bazie wiedzy Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Wyszukiwanie Microsoft Knowledge Base (KB) dla techniczne rozwiązania typowych problemów naprawa w razie awarii o obsłudze programu Azure AD Connect.

[Fora dotyczące usługi Active Directory platformy Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Można wyszukiwać i wyszukać technicznych pytania i odpowiedzi od społeczności lub zadać pytanie własne klikając [tutaj](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Jak uzyskać pomoc techniczną dotyczącą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)



