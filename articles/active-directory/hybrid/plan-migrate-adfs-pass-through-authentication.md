---
title: 'Program Azure AD Connect: Migrowanie z Federacji na PTA dla usługi Azure AD'
description: Ten artykuł zawiera informacje o przejściu do środowiska tożsamości hybrydowej z Federacji uwierzytelniania przekazywanego.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb421442a7b45f3cd5925fd1475a0a69053c3113
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473385"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrowanie z Federacji na uwierzytelnianie przekazywane usługi Azure Active Directory

W tym artykule opisano sposób przenoszenia domen organizacji z usługi Active Directory Federation Services (AD FS) do uwierzytelniania przekazywanego.

Możesz [pobierania w tym artykule](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Wymagania wstępne dotyczące migracji do uwierzytelniania przekazywanego

Następujące wymagania wstępne są wymagane do migracji z za pomocą usług AD FS do uwierzytelniania przekazywanego.

### <a name="update-azure-ad-connect"></a>Aktualizacja usługi Azure AD Connect

Aby pomyślnie wykonać kroki trwania migracji za pomocą uwierzytelniania przekazywanego, konieczne jest posiadanie [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 lub nowszej. W programie Azure AD Connect 1.1.819.0 sposób rejestrowania konwersja jest wykonywane znacznie zmiany. Całkowity czas migracji z usług AD FS do uwierzytelniania w chmurze w tej wersji zmniejszono z potencjalnie godziny do minuty.

> [!IMPORTANT]
> Można mogą odczytać w nieaktualnych dokumentację, narzędzia i blogi konwersji użytkownika jest wymagana podczas konwertowania domen z tożsamości federacyjnej tożsamość zarządzaną. *Konwertowanie użytkowników* nie jest już wymagane. Firma Microsoft pracuje się do aktualizacji, dokumentacji i narzędzi w celu odzwierciedlenia tej zmiany.

Aby uaktualnić program Azure AD Connect, wykonaj kroki opisane w [program Azure AD Connect: Uaktualnianie do najnowszej wersji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Planowanie uwierzytelniania agenta liczbę i rozmieszczenie

Uwierzytelnianie przekazywane wymaga wdrażania uproszczone agentów na serwer programu Azure AD Connect i na komputerze z systemem Windows Server w środowisku lokalnym. Aby zmniejszyć czas oczekiwania, należy zainstalować agentów, jak najbliżej kontrolerów domeny usługi Active Directory.

W przypadku większości klientów dwóch lub trzech agentów uwierzytelniania są wystarczające, aby zapewnić wysoką dostępność i wymaganą pojemność. Dzierżawca może mieć maksymalnie 12 agentów zarejestrowany. Pierwszy agent zawsze jest instalowany na serwerze usługi Azure AD Connect. Aby dowiedzieć się więcej o ograniczeniach agenta i opcji wdrażania agenta, zobacz [uwierzytelnianie przekazywane usługi Azure AD: Bieżące ograniczenia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations).

### <a name="plan-the-migration-method"></a>Planowanie metody migracji

Dwie metody mogą wybierać, aby przeprowadzić migrację z funkcji zarządzania tożsamościami federacyjnymi uwierzytelniania przekazywanego i bezproblemowe logowanie jednokrotne (SSO). Metody, których używasz, zależy od tego, jak wystąpienia usług AD FS zostały pierwotnie skonfigurowane.

* **Azure AD Connect**. Jeśli usługi AD FS pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect, możesz *musi* zmiana uwierzytelniania przekazywanego za pomocą Kreatora programu Azure AD Connect.

   Automatycznie uruchamia program Azure AD Connect **MsolDomainAuthentication zestaw** polecenia cmdlet, po zmianie metody logowania użytkownika. Program Azure AD Connect unfederates automatycznie wszystkie domeny federacyjne zweryfikowanej w dzierżawie usługi Azure AD.

   > [!NOTE]
   > Obecnie Jeśli początkowo używano programu Azure AD Connect skonfigurować usługi AD FS, nie można uniknąć unfederating wszystkich domen w Twojej dzierżawie, gdy zmienią się logowania użytkownika do uwierzytelniania przekazywanego.
‎
* **Azure AD Connect przy użyciu programu PowerShell**. Tej metody można użyć tylko wtedy, gdy nie został pierwotnie skonfigurować usługi AD FS za pomocą usługi Azure AD Connect. Dla tej opcji nadal należy zmienić metodę logowania użytkownika za pomocą Kreatora programu Azure AD Connect. Różnica core przy użyciu tej opcji jest, że Kreator nie zostanie automatycznie uruchomiona **MsolDomainAuthentication zestaw** polecenia cmdlet. Po wybraniu tej opcji masz pełną kontrolę nad domen są konwertowane oraz w jakiej kolejności.

Aby dowiedzieć się, której metody należy użyć, wykonaj kroki opisane w poniższych sekcjach.

#### <a name="verify-current-user-sign-in-settings"></a>Sprawdź bieżące ustawienia logowania użytkownika

1. Zaloguj się do [portalu usługi Azure AD](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego.
2. W **logowania użytkownika** sekcji, sprawdź następujące ustawienia:
   * **Federacyjna** ustawiono **włączone**.
   * **Bezproblemowe logowanie jednokrotne** ustawiono **wyłączone**.
   * **Uwierzytelnianie przekazywane** ustawiono **wyłączone**.

   ![Zrzut ekranu przedstawiający ustawienia w sekcji Łączenie użytkownika usługi Azure AD logowania](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Sprawdź, jak został skonfigurowany Federacji

1. Na serwerze usługi Azure AD Connect Otwórz program Azure AD Connect. Wybierz pozycję **Konfiguruj**.
2. Na **dodatkowe zadania** wybierz opcję **wyświetlanie bieżącej konfiguracji**, a następnie wybierz pozycję **dalej**.<br />
 
   ![Zrzut ekranu przedstawiający widok opcji konfiguracji bieżącego na stronie dodatkowe zadania](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Na **Przejrzyj swoje rozwiązanie** przewiń do **Active Directory Federation Services (AD FS)** .<br />

   * Jeśli Konfiguracja usług AD FS znajduje się w tej sekcji, można bezpiecznie przyjąć, że usług AD FS zostały pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect. Można konwertować domen z tożsamości federacyjnej do tożsamości zarządzanej przy użyciu programu Azure AD Connect **zmiana użytkownika logowania** opcji. Aby uzyskać więcej informacji na temat procesu, zobacz sekcję **opcja 1: Konfigurowanie uwierzytelniania przekazywanego za pomocą usługi Azure AD Connect**.
   * Jeśli usługi AD FS nie jest wymieniony w bieżące ustawienia, należy ręcznie przekonwertować domen z tożsamości federacyjnej tożsamości zarządzanej przy użyciu programu PowerShell. Aby uzyskać więcej informacji na temat tego procesu, zobacz sekcję **opcja 2: Przełączyć się z federacyjnego uwierzytelniania przekazywanego za pomocą programu PowerShell i Azure AD Connect**.

### <a name="document-current-federation-settings"></a>Bieżące ustawienia Federacji dokumentu

Aby znaleźć bieżące ustawienia Federacji, uruchom **polecenia Get-MsolDomainFederationSettings** polecenia cmdlet:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Przykład:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Sprawdź wszystkie ustawienia, które może być dostosowane dla Federacji dokumentacji projektu i wdrożenia. W szczególności poszukaj dostosowań w **PreferredAuthenticationProtocol**, **SupportsMfa**, i **PromptLoginBehavior**.

Więcej informacji można znaleźć w tych artykułach:

* [Wiersz usług AD FS = Obsługa parametrów logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Jeśli **SupportsMfa** ustawiono **True**, za pomocą lokalnego rozwiązania do uwierzytelniania wieloskładnikowego wstrzyknąć wyzwanie drugiego czynnika do przepływu uwierzytelniania użytkownika. Ta konfiguracja nie jest już działa w przypadku scenariuszy uwierzytelniania usługi Azure AD. 
>
> Użyj usługi opartej na chmurze usługi Azure Multi-Factor Authentication do wykonania tej samej funkcji. Należy wnikliwie zastanowić wymagań dotyczących uwierzytelniania wieloskładnikowego, przed kontynuowaniem. Przed rozpoczęciem konwertowania domen, upewnij się, że rozumiesz, jak używać usługi Azure Multi-Factor Authentication, skutki licencjonowania i procesu rejestracji użytkownika.

#### <a name="back-up-federation-settings"></a>Tworzenie kopii zapasowej ustawień federacyjnych

Mimo że nie zmian do innych uzależnionych w farmie usług AD FS w procesach opisanych w tym artykule, zaleca się, że masz bieżącego prawidłowej kopii zapasowej farmy usług AD FS, którą można przywrócić z. Bieżący prawidłowej kopii zapasowej można utworzyć przy użyciu bezpłatne Microsoft [usługi AD FS szybkiego przywrócenia narzędzie](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Umożliwia to narzędzie do tworzenia kopii zapasowej usług AD FS i przywrócić istniejącej farmy lub utworzyć nową farmę.

Jeśli wybierzesz nie korzystała z usługi AD FS szybkiego przywrócenia narzędzia do co najmniej, należy wyeksportować jednostki uzależnionej relacja zaufania i wszystkie skojarzone niestandardowych reguł oświadczeń dodanych platforma tożsamości firmy Microsoft Office 365. Możesz wyeksportować zaufania jednostki uzależnionej i reguł oświadczeń skojarzony, korzystając z następującego przykładu programu PowerShell:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Zagadnienia dotyczące wdrażania i za pomocą usług AD FS

W tej sekcji opisano zagadnienia dotyczące wdrażania i szczegółowe informacje dotyczące korzystania z usług AD FS.

### <a name="current-ad-fs-use"></a>Użyj bieżącego usług AD FS

Przed dokonaniem konwersji z tożsamości federacyjnej do tożsamości zarządzanej poznaniem jak obecnie używasz usługi AD FS dla usługi Azure AD, Office 365 i innych aplikacji (jednostki uzależnionej relacja zaufania jednostek uzależnionych). W szczególności należy wziąć pod uwagę scenariusze, które są opisane w poniższej tabeli:

| Jeśli użytkownik | Następnie |
|-|-|
| Zamierzasz nadal korzystać z usług AD FS z innymi aplikacjami (innych niż Usługa Azure AD i Office 365). | Po przekonwertowaniu domen, użyjesz usługi Azure AD i usług AD FS. Należy wziąć pod uwagę doświadczenia użytkownika. W niektórych przypadkach użytkownikom może wymagać dwukrotnego uwierzytelnienia: raz do usługi Azure AD (w którym użytkownik otrzyma rejestracji Jednokrotnej dostęp do innych aplikacji, takich jak Office 365) oraz ponownie wszystkie aplikacje, które są nadal powiązane z usług AD FS jako zaufanie jednostki uzależnionej. |
| Wystąpienia usług AD FS jest w dużym stopniu dostosowany i zależy od ustawienia dostosowania określone w pliku onload.js (na przykład, jeśli zmienisz środowisko logowania, aby użytkownicy używana będzie tylko **SamAccountName** format swoją nazwę użytkownika zamiast użytkownika głównej nazwy (UPN) lub Twoja organizacja ma intensywnie marki środowisko logowania). Nie można zduplikować pliku onload.js w usłudze Azure AD. | Przed kontynuowaniem należy sprawdzić, czy usługa Azure AD można spełniają wymagań dostosowania użytkownika bieżącego. Aby uzyskać więcej informacji i wskazówki zobacz sekcje na marki usług AD FS i dostosowania usług AD FS.|
| Aby zablokować wcześniejszych wersjach klientów uwierzytelniania jest używanie usług AD FS.| Rozważ zastąpienie kontrolek usług AD FS, które blokują wcześniejszych wersjach klientów uwierzytelniania przy użyciu kombinacji [kontroli dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) i [reguł dostępu klienta Exchange Online](https://aka.ms/EXOCAR). |
| Możesz wymagać od użytkowników wykonywać uwierzytelnianie wieloskładnikowe, względem rozwiązania z serwerem usługi Multi-Factor authentication w środowisku lokalnym, gdy użytkownicy są uwierzytelniani do usług AD FS.| W domenie zarządzanej tożsamości nie można wstrzyknąć uwierzytelnienia Multi-Factor Authentication za pośrednictwem lokalnego rozwiązania usługi Multi-Factor authentication do przepływu uwierzytelniania. Jednak można użyć usługi Azure Multi-Factor Authentication do uwierzytelniania wieloskładnikowego, po przekonwertowaniu domeny.<br /><br /> Jeśli użytkownicy obecnie nie używasz usługi Azure Multi-Factor Authentication, etapu rejestracji jednorazowej użytkownika jest wymagana. Należy przygotować się do i komunikacji planowane rejestracji dla użytkowników. |
| Obecnie używają zasad kontroli dostępu (reguł autoryzacji) w usługach AD FS do kontrolowania dostępu do usługi Office 365.| Należy wziąć pod uwagę, zastępując zasady równoważne usługi Azure AD [zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) i [reguł dostępu klienta Exchange Online](https://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Typowe dostosowania usług AD FS

W tej sekcji opisano typowe dostosowania usług AD FS.

#### <a name="insidecorporatenetwork-claim"></a>Oświadczenie InsideCorporateNetwork

Problemy z usług AD FS **InsideCorporateNetwork** oświadczenia, jeśli użytkownik, który jest uwierzytelniany znajduje się wewnątrz sieci firmowej. To oświadczenie, może być następnie przekazywany usłudze Azure AD. Oświadczenie jest używany do obejścia uwierzytelnianie wieloskładnikowe na podstawie lokalizacji sieciowej użytkownika. Aby dowiedzieć się, jak ustalić, czy ta funkcja obecnie jest dostępna w usługach AD FS, zobacz [zaufane adresy IP dla użytkowników federacyjnych](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

**InsideCorporateNetwork** oświadczenia nie jest dostępne po domen są konwertowane na uwierzytelnianie przekazywane. Możesz użyć [lokalizacje z nazwą w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) zastąpić tę funkcję.

Po skonfigurowaniu nazwane lokalizacje, należy zaktualizować wszystkie zasady dostępu warunkowego, które zostały skonfigurowane, aby uwzględnić lub wykluczyć sieci **wszystkie zaufane lokalizacje** lub **zaufane adresy IP usługi MFA** wartości odzwierciedlały nowe lokalizacje z nazwą.

Aby uzyskać więcej informacji na temat **lokalizacji** warunku dostępu warunkowego, zobacz [lokalizacji dostępu warunkowego usługi Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrydowe przyłączone do usługi AD urządzenia w usłudze Azure

Podczas przyłączania urządzenia do usługi Azure AD, można utworzyć zasady dostępu warunkowego, które wymuszają, że urządzenia spełniają Twoje standardy dostępu dotyczące zabezpieczeń i zgodności. Ponadto użytkownicy mogą zarejestrować na urządzeniu przy użyciu pracy organizacji lub konta służbowego zamiast konta osobistego. Kiedy używać hybrydowej platformy Azure w przypadku urządzeń przyłączonych do usługi AD urządzeń przyłączonych do domeny usługi Active Directory mogą dołączyć do usługi Azure AD. Środowisko federacyjnego może została skonfigurowana do użycia tej funkcji.

Upewnij się, że dołączenie do hybrydowej w dalszym ciągu działać w przypadku wszelkich urządzeń, które są przyłączone do domeny po domen są konwertowane na uwierzytelniania przekazywanego, dla klientów z systemem Windows 10, możesz korzystać program Azure AD Connect do synchronizowania kont komputerów usługi Active Directory do usługi Azure AD.

Dla kont komputerów Windows 8 i Windows 7 dołączenie do hybrydowej używa bezproblemowe logowanie Jednokrotne, aby zarejestrować komputer w usłudze Azure AD. Nie trzeba synchronizacji systemu Windows 8 i Windows 7 komputerów, tak jak w przypadku urządzeń z systemem Windows 10. Jednak należy wdrożyć plik workplacejoin.exe zaktualizowane (za pośrednictwem pliku .msi) dla klientów systemu Windows 8 i Windows 7, dzięki czemu można zarejestrować się przy użyciu bezproblemowe logowanie Jednokrotne. [Pobierz plik MSI](https://www.microsoft.com/download/details.aspx?id=53554).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie hybrydowej platformy Azure urządzeń dołączonych do usługi AD](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Znakowanie

Jeśli Twoja organizacja [dostosowanej strony logowania usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) Aby wyświetlić informacje, które są bardziej odpowiednie dla organizacji, należy wziąć pod uwagę co podobne [dostosowania strony logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Mimo że dostępne są podobne dostosowań, niektóre zmiany wizualne na stronach logowania można się spodziewać po konwersji. Można udostępnić użytkownikom informacje o zmianach oczekiwanego w komunikacji.

> [!NOTE]
> Znakowanie organizacji jest dostępna tylko wtedy, gdy zakupu licencja podstawowa lub Premium usługi Azure Active Directory, lub jeśli masz licencję usługi Office 365.

## <a name="plan-for-smart-lockout"></a>Planowanie inteligentnej blokady

Usługa Azure AD, inteligentnej blokady chroni przed atakami siłowymi hasła. Inteligentnej blokady zapobiega konta usługi Active Directory w środowisku lokalnym jest zablokowane, gdy jest używane uwierzytelnianie przekazywane ustawiono zasady grupy blokady konta w usłudze Active Directory.

Aby uzyskać więcej informacji, zobacz [inteligentnej blokady w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="plan-deployment-and-support"></a>Planowanie wdrożenia i pomocy technicznej

Wykonaj zadania, które są opisane w tej sekcji ułatwią planowanie dotyczące wdrażania i obsługi.

### <a name="plan-the-maintenance-window"></a>Planowanie okna obsługi

Mimo że proces konwersji domeny odbywa się relatywnie szybko, usługi Azure AD mogą kontynuować wysyłanie niektórych żądań uwierzytelniania na serwerach usług AD FS przez kilka godzin, po zakończeniu konwersji domeny. Podczas tego 4 godzinnego przedziału czasu, a także w zależności od różnych pamięci podręcznych po stronie usługi usługi Azure AD nie może zaakceptować te uwierzytelnienia. Użytkownicy mogą wystąpić błąd. Użytkownik może nadal pomyślnie uwierzytelnić usług AD FS, ale usługa Azure AD nie będzie akceptował użytkownika wystawionego tokenu, ponieważ tej relacji zaufania federacji zostało usunięte.

Dotyczy tylko użytkowników, którzy dostęp do usług za pośrednictwem przeglądarki sieci web w tym oknie po konwersji, zanim wyczyszczenia pamięci podręcznej po stronie usługi. Starszych klientów (program Exchange ActiveSync, Outlook 2010 i 2013) nie są oczekiwane których to dotyczy, ponieważ usługi Exchange Online utrzymuje pamięci podręcznej poświadczeń na pewien okres czasu. Pamięci podręcznej jest używana w trybie dyskretnym ponownego uwierzytelnienia użytkownika. Użytkownik nie musi powrócić do usług AD FS. Poświadczenia przechowywane na urządzeniu dla tych klientów są używane do dyskretnie ponownie się uwierzytelniać po to pamięci podręcznej jest wyczyszczone. Użytkownicy nie powinien otrzymywać żadnych monitów o hasło, w wyniku procesu konwersji domeny.

Nowocześni Klienci uwierzytelniania (pakiety Office 2016 i Office 2013, iOS i aplikacje dla systemu Android) używają tokenu odświeżania prawidłowy w celu uzyskania nowych tokenów dostępu opinię dotyczącą przedłużenia dostępu do zasobów, zamiast zwracać do usług AD FS. Ci klienci są błędy wynikające z procesu konwersji domeny żadnych monitów o hasło. Klienci będą nadal działać bez dodatkowej konfiguracji.

> [!IMPORTANT]
> Nie zamknięcia środowiska usług AD FS lub usunąć usługi Office 365 zaufania jednostki uzależnionej, dopóki upewnieniu się, że wszyscy użytkownicy pomyślnie przeprowadzić uwierzytelnienie przy użyciu uwierzytelniania w chmurze.

### <a name="plan-for-rollback"></a>Plan wycofania

Jeśli napotkasz poważnym problemem, który nie może zostać szybko rozwiązać, można zdecydować wycofywanie rozwiązania do federacyjnego. Należy zaplanować, co należy zrobić, jeśli wdrożenie nie wprowadzane zgodnie z oczekiwaniami. Jeśli konwersja domeny lub użytkownicy nie powiedzie się podczas wdrażania lub jeśli potrzebujesz wrócić do Federacji, należy zrozumieć, jak rozwiązać ewentualnej awarii i zmniejszenia wpływu na użytkowników.

#### <a name="to-roll-back"></a>Aby wycofać

Aby zaplanować wycofywania, sprawdź w dokumentacji projektowania i wdrażania federacyjnego informacje dotyczące określonego wdrożenia. Proces powinien zawierać następujące zadania:

* Konwertowania domen zarządzanych do domen federacyjnych, za pomocą **Convert MSOLDomainToFederated** polecenia cmdlet.
* Jeśli to konieczne, konfigurowanie dodatkowych oświadczeń reguły.

### <a name="plan-communications"></a>Planowanie komunikacji

Ważnym elementem planowania, wdrażania i obsługi jest zapewnienie, że użytkownicy są aktywnie aktualne informacje o nadchodzących zmianach. Użytkownicy należy wiedzieć z wyprzedzeniem, co może wystąpić, i co jest wymagane z nich.

Po wdrożeniu bezproblemowego logowania jednokrotnego i uwierzytelniania przekazywanego zmian środowiska logowania użytkowników do uzyskiwania dostępu do usługi Office 365 i innych zasobów, które są uwierzytelniani za pomocą usługi Azure AD. Użytkownicy, którzy znajdują się poza siecią Zobacz tylko na usłudze Azure AD — stronę logowania. Ci użytkownicy nie są przekierowywane do strony oparte na formularzach, który jest przedstawiony przez serwery proxy aplikacji sieci web dołączonej do Internetu.

W strategii komunikacji, obejmują następujące elementy:

* Powiadom użytkowników o nadchodzących i wydania funkcji przy użyciu:
   * Adres e-mail i innych kanałów komunikacji wewnętrznej.
   * Elementy wizualne, takie jak plakaty.
   * Komunikacja wykonawczego na żywo i inne.
* Określić, który umożliwia dostosowywanie komunikacji i który będzie wysyłać komunikatów i kiedy.

## <a name="implement-your-solution"></a>Implementowania rozwiązania

Planowane jest rozwiązanie. Teraz możesz teraz wdrożyć ją. Implementacja obejmuje następujące składniki:

* Przygotowywanie do bezproblemowego logowania jednokrotnego.
* Zmiana metody logowania i uwierzytelniania przekazywanego, umożliwiając bezproblemowe logowanie Jednokrotne.

### <a name="step-1-prepare-for-seamless-sso"></a>Krok 1: Przygotowanie do bezproblemowego logowania jednokrotnego

Dla urządzeń umożliwia bezproblemowe logowanie Jednokrotne należy dodać adres URL usług Azure AD do ustawienia strefy intranet użytkowników za pomocą zasad grupy w usłudze Active Directory.

Domyślnie, przeglądarek internetowych automatycznie obliczyć poprawnej strefy internet lub intranet, z adresu URL. Na przykład **http:\/\/contoso /** mapuje do strefy intranet i **http:\/\/intranet.contoso.com** mapuje do strefy internet (ponieważ adres URL zawiera kropkę). Przeglądarki wysyłać do punktu końcowego w chmurze, takich jak usługa Azure AD adres URL, bilety protokołu Kerberos, tylko wtedy, gdy jawnie dodać adres URL do strefy intranet w przeglądarce.

Wykonaj kroki, aby [wprowadzane](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) zmiany wymagane w celu urządzeń.

> [!IMPORTANT]
> Wprowadzenie tej zmiany nie modyfikuje sposób użytkownicy logują się do usługi Azure AD. Jednak ważne jest zastosowanie tej konfiguracji do wszystkich urządzeń przed kontynuowaniem. Użytkownicy logujący się na urządzeniach, które nie zostały odebrane tej konfiguracji po prostu jest konieczne wprowadzenie nazwy użytkownika i hasło, aby zalogować się do usługi Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Krok 2: Zmień metodę logowania do uwierzytelniania przekazywanego i Włącz bezproblemowe logowanie Jednokrotne

Masz dwie opcje zmiana metody logowania do uwierzytelniania przekazywanego i włączanie bezproblemowego logowania jednokrotnego.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Option A: Konfigurowanie uwierzytelniania przekazywanego za pomocą usługi Azure AD Connect

Ta metoda wstępnie skonfigurowane środowiska usług AD FS za pomocą usługi Azure AD Connect. Nie można użyć tej metody, jeśli użytkownik *nie* początkowego konfigurowania środowiska usług AD FS za pomocą usługi Azure AD Connect.

> [!IMPORTANT]
> Po wykonaniu następujących czynności, wszystkie domeny są przekształcane z tożsamości federacyjnej tożsamość zarządzaną. Aby uzyskać więcej informacji, zobacz [planowanie metody migracji](#plan-the-migration-method).

Najpierw należy zmienić metodę logowania:

1. Na serwerze programu Azure AD Connect Otwórz kreatora programu Azure AD Connect.
2. Wybierz **zmiana użytkownika logowania**, a następnie wybierz pozycję **dalej**. 
3. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź nazwę użytkownika i hasło konta administratora globalnego.
4. Na **logowania użytkownika** wybierz opcję **uwierzytelniania przekazywanego** przycisku Wybierz **włączyć rejestrację jednokrotną**, a następnie wybierz pozycję **dalej**.
5. Na **włączyć rejestrację jednokrotną** strony, wprowadź poświadczenia konta administratora domeny, a następnie wybierz pozycję **dalej**.

   > [!NOTE]
   > Poświadczenia konta administratora domeny są wymagane, aby umożliwić bezproblemowe logowanie Jednokrotne. Proces wykonuje następujące czynności, które wymagają tych podwyższonym poziomem uprawnień. Poświadczenia konta administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Poświadczenia konta administratora domeny są używane tylko po to, aby włączyć tę funkcję. Poświadczenia są odrzucane, gdy proces zakończy się pomyślnie.
   >
   > 1. Konto komputera o nazwie AZUREADSSOACC (który reprezentuje usługę Azure AD) jest tworzony w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym.
   > 2. Bezpiecznie udostępniono klucz odszyfrowywania protokołu Kerberos konta komputera z usługą Azure AD.
   > 3. Dwa Kerberos głównych nazw usług (SPN) są tworzone do reprezentowania dwa adresy URL, które są używane podczas logowania w usłudze Azure AD.

6. Na **wszystko gotowe do skonfigurowania** strony, upewnij się, że **proces synchronizacji rozpocznie się po ukończeniu konfiguracji** pole wyboru jest zaznaczone. Następnie wybierz **Konfiguruj**.<br />

   ![Zrzut ekranu przedstawiający wszystko gotowe do skonfigurowania strony](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. W portalu usługi Azure AD wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **program Azure AD Connect**.
8. Sprawdź następujące ustawienia:
   * **Federacyjna** ustawiono **wyłączone**.
   * **Bezproblemowe logowanie jednokrotne** ustawiono **włączone**.
   * **Uwierzytelnianie przekazywane** ustawiono **włączone**.<br />

   ![Zrzut ekranu pokazujący ustawienia w sekcji logowania użytkownika](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Dalej. Wdrażanie dodatkowych metod uwierzytelniania:

1. W witrynie Azure portal przejdź do **usługi Azure Active Directory** > **program Azure AD Connect**, a następnie wybierz pozycję **uwierzytelniania przekazywanego**.
2. Na **uwierzytelniania przekazywanego** wybierz opcję **Pobierz** przycisku.
3. Na **Pobierz agenta** wybierz opcję **Zaakceptuj warunki i Pobierz**.

   Dodatkowe uwierzytelnianie agentów rozpocznie pobieranie. Zainstaluj agenta uwierzytelniania pomocniczego na serwerze przyłączonym do domeny. 

   > [!NOTE]
   > Pierwszy agent jest zawsze instalowany na serwerze programu Azure AD Connect sama w ramach zmian konfiguracji wprowadzonych w **logowania użytkownika** sekcji narzędzia Azure AD Connect. Wszelkich agentów dodatkowego uwierzytelniania należy zainstalować na osobnym serwerze. Zaleca się, że masz dwa lub trzy dodatkowe uwierzytelnianie dostępne agenty. 

4. Uruchom instalację agenta uwierzytelniania. Podczas instalacji należy wprowadzić poświadczenia konta administratora globalnego.

   ![Zrzut ekranu przedstawiający przycisk Zainstaluj w witrynie Microsoft Azure AD Connect uwierzytelniania agenta pakietu](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Zrzut ekranu przedstawiający stronę logowania](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Po zainstalowaniu agenta uwierzytelniania, można zwrócić się do strony kondycji agenta uwierzytelniania przekazywanego, aby sprawdzić stan dodatkowych agentów.

Przejdź do [testowania i następne kroki](#testing-and-next-steps).

> [!IMPORTANT]
> Przejdź do sekcji **opcja B: Przełączyć się z federacyjnego uwierzytelniania przekazywanego za pomocą programu PowerShell i Azure AD Connect**. W krokach w tej sekcji nie mają zastosowania, jeśli wybrano opcję A, aby zmienić metodę logowania do uwierzytelniania przekazywanego i Włącz bezproblemowe logowanie Jednokrotne. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Opcja B: Przełączyć się z federacyjnego uwierzytelniania przekazywanego za pomocą programu PowerShell i Azure AD Connect

Użyj tej opcji, które nie zostały wstępnie skonfigurowane domen federacyjnych przy użyciu usługi Azure AD Connect.

Najpierw należy włączyć uwierzytelnianie przekazujących:

1. Serwerze programu Azure AD Connect Otwórz kreatora programu Azure AD Connect.
2. Wybierz **zmiana użytkownika logowania**, a następnie wybierz pozycję **dalej**.
3. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź nazwę użytkownika i hasło konta administratora globalnego.
4. Na **logowania użytkownika** wybierz opcję **uwierzytelniania przekazywanego** przycisku. Wybierz **włączyć rejestrację jednokrotną**, a następnie wybierz pozycję **dalej**.
5. Na **włączyć rejestrację jednokrotną** strony, wprowadź poświadczenia konta administratora domeny, a następnie wybierz pozycję **dalej**.

   > [!NOTE]
   > Poświadczenia konta administratora domeny są wymagane, aby umożliwić bezproblemowe logowanie Jednokrotne. Proces wykonuje następujące czynności, które wymagają tych podwyższonym poziomem uprawnień. Poświadczenia konta administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Poświadczenia konta administratora domeny są używane tylko po to, aby włączyć tę funkcję. Poświadczenia są odrzucane, gdy proces zakończy się pomyślnie.
   > 
   > 1. Konto komputera o nazwie AZUREADSSOACC (który reprezentuje usługę Azure AD) jest tworzony w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym.
   > 2. Bezpiecznie udostępniono klucz odszyfrowywania protokołu Kerberos konta komputera z usługą Azure AD.
   > 3. Dwa Kerberos głównych nazw usług (SPN) są tworzone do reprezentowania dwa adresy URL, które są używane podczas logowania w usłudze Azure AD.

6. Na **wszystko gotowe do skonfigurowania** strony, upewnij się, że **proces synchronizacji rozpocznie się po ukończeniu konfiguracji** pole wyboru jest zaznaczone. Następnie wybierz **Konfiguruj**.<br />

   ‎![Zrzut ekranu pokazujący gotowe, aby skonfigurować stronę i przycisk Konfiguruj](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Wykonane następujące kroki, po wybraniu **Konfiguruj**:

   1. Pierwszy agent uwierzytelnianie przekazywane jest zainstalowany.
   2. Funkcja przekazywania jest włączona.
   3. Bezproblemowe logowanie Jednokrotne jest włączona.

7. Sprawdź następujące ustawienia:
   * **Federacyjna** ustawiono **włączone**.
   * **Bezproblemowe logowanie jednokrotne** ustawiono **włączone**.
   * **Uwierzytelnianie przekazywane** ustawiono **włączone**.
   
   ![Zrzut ekranu pokazujący ustawienia w sekcji logowania użytkownika](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Wybierz **uwierzytelniania przekazywanego** i sprawdź, że jest w stanie **Active**.<br />
   
   Jeśli agent uwierzytelniania nie jest aktywne, należy wykonać niektóre [kroki rozwiązywania problemów](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) przed kontynuowaniem procesu konwersji domeny w następnym kroku. Istnieje ryzyko, co powoduje awarię uwierzytelniania po skonwertowaniu Twoich domen, aby sprawdzić poprawność agentów uwierzytelniania przekazywanego zostali pomyślnie zainstalowani i że ich stan **Active** w witrynie Azure portal.

Następnie można wdrożyć agentów dodatkowego uwierzytelniania:

1. W witrynie Azure portal przejdź do **usługi Azure Active Directory** > **program Azure AD Connect**, a następnie wybierz pozycję **uwierzytelniania przekazywanego**.
2. Na **uwierzytelniania przekazywanego** wybierz opcję **Pobierz** przycisku. 
3. Na **Pobierz agenta** wybierz opcję **Zaakceptuj warunki i Pobierz**.
 
   Agent uwierzytelniania rozpocznie się pobieranie. Zainstaluj agenta uwierzytelniania pomocniczego na serwerze przyłączonym do domeny.

   > [!NOTE]
   > Pierwszy agent jest zawsze instalowany na serwerze programu Azure AD Connect sama w ramach zmian konfiguracji wprowadzonych w **logowania użytkownika** sekcji narzędzia Azure AD Connect. Wszelkich agentów dodatkowego uwierzytelniania należy zainstalować na osobnym serwerze. Zaleca się, że masz dwa lub trzy dodatkowe uwierzytelnianie dostępne agenty.
 
4. Uruchom instalację agenta uwierzytelniania. Podczas instalacji należy wprowadzić poświadczenia konta administratora globalnego.<br />

   ![Zrzut ekranu przedstawiający przycisk Zainstaluj w witrynie Microsoft Azure AD Connect uwierzytelniania agenta pakietu](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Zrzut ekranu przedstawiający stronę logowania](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Po zainstalowaniu agenta uwierzytelniania, można powrócić do strony kondycji agenta uwierzytelniania przekazywanego, aby sprawdzić stan dodatkowych agentów.

W tym momencie uwierzytelniania federacyjnego jest nadal aktywne i operacyjne dla Twoich domen. Aby kontynuować wdrożenia, należy przekonwertować każdej domeny z tożsamości federacyjnej na tożsamość zarządzaną, że uwierzytelnianie przekazujących rozpoczyna się obsługi żądań uwierzytelniania dla domeny.

Nie trzeba przekonwertować wszystkich domen w tym samym czasie. Można rozpocząć domeny testu na dzierżawy produkcyjnej lub rozpoczynać się domenę, która ma najmniejsza liczba użytkowników.

Wykonaj konwersję przy użyciu modułu Azure AD PowerShell:

1. W programie PowerShell Zaloguj się w usłudze Azure AD przy użyciu konta administratora globalnego.
2. Aby przekonwertować pierwszej domeny, uruchom następujące polecenie:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. W portalu usługi Azure AD wybierz **usługi Azure Active Directory** > **program Azure AD Connect**.
4. Po przekonwertowaniu wszystkich domen federacyjnych, należy sprawdzić te ustawienia:
   * **Federacyjna** ustawiono **wyłączone**.
   * **Bezproblemowe logowanie jednokrotne** ustawiono **włączone**.
   * **Uwierzytelnianie przekazywane** ustawiono **włączone**.<br />

   ![Zrzut ekranu pokazujący ustawienia w sekcji logowania użytkownika](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testowanie i następne kroki

Wykonaj poniższe zadania, aby zweryfikować uwierzytelniania przekazywanego, Zakończ proces konwersji.

### <a name="test-pass-through-authentication"></a>Test uwierzytelniania przekazywanego 

W przypadku dzierżawy tożsamości federacyjnych użytkowników zostały przekierowanie ze strony logowania usługi Azure AD do środowiska usług AD FS. Teraz, gdy dzierżawy jest skonfigurowany do używania uwierzytelniania przekazywanego zamiast uwierzytelnianie federacyjne, użytkownicy nie są przekierowywani do usług AD FS. Zamiast tego użytkownicy logować bezpośrednio na stronie logowania w usłudze Azure AD.

Aby przetestować uwierzytelnianie przekazujących:

1. Otwórz program Internet Explorer w trybie InPrivate, umożliwiając bezproblemowe logowanie Jednokrotne nie automatyczne logowanie.
2. Przejdź do strony logowania usługi Office 365 ([https://portal.office.com](https://portal.office.com/)).
3. Wprowadź nazwę UPN użytkownika, a następnie wybierz pozycję **dalej**. Upewnij się, że wprowadzasz nazwę UPN użytkownika hybrydowego, który zostało zsynchronizowane z wystąpienia usługi Active Directory w środowisku lokalnym, a, którzy wcześniej korzystali uwierzytelniania federacyjnego. Zostanie wyświetlona strona, na którym możesz wprowadzić nazwę użytkownika i hasło:

   ![Zrzut ekranu przedstawiający stronę logowania, w którym należy wprowadzić nazwę użytkownika](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Zrzut ekranu przedstawiający stronę logowania, w którym należy wprowadzić hasło](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Po wprowadzeniu hasła i wybierz **Zaloguj**, użytkownik jest przekierowany do portalu usługi Office 365.

   ![Zrzut ekranu przedstawiający portal usługi Office 365](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Bezproblemowe logowanie Jednokrotne testu

Aby przetestować bezproblemowe logowanie Jednokrotne:

1. Zaloguj się do maszyny przyłączonych do domeny, która jest połączona z siecią firmową.
2. W programie Internet Explorer lub Chrome przejdź do jednej z następujących adresów URL (Zastąp "contoso" z Twoją domeną):

   * https:\/\/myapps.microsoft.com/contoso.com
   * protokół https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Użytkownik krótko jest przekierowywany do usługi Azure AD strony logowania, który pokazuje komunikat "Podczas próby zalogowania Cię." Użytkownik nie jest monitowany o nazwę użytkownika lub hasło.<br />

   ![Zrzut ekranu przedstawiający stronę logowania w usłudze Azure AD i wiadomości](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. Użytkownik jest przekierowywany i jest pomyślnie zalogowano się do panelu dostępu:

   > [!NOTE]
   > Bezproblemowe logowanie Jednokrotne działa z usługami Office 365, które obsługują wskazówkę dotyczącą domeny (na przykład myapps.microsoft.com/contoso.com). Wskazówki dotyczące domeny nie obsługuje obecnie, portalu usługi Office 365 (portal.office.com). Użytkownicy muszą wprowadzić nazwę UPN. Po wprowadzeniu nazwy UPN bezproblemowe logowanie Jednokrotne pobiera biletu protokołu Kerberos w imieniu użytkownika. Użytkownik jest zalogowany bez wprowadzania hasła.

   > [!TIP]
   > Zaleca się wdrożenie [hybrydowe usługi Azure AD join w systemie Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) ulepszone środowisko logowania jednokrotnego.

### <a name="remove-the-relying-party-trust"></a>Usuń relację zaufania jednostki uzależnionej

Po sprawdzeniu wszystkich użytkowników i klientów są pomyślnym uwierzytelnieniu za pomocą usługi Azure AD, jest bezpiecznie usunąć usługi Office 365 zaufania jednostki uzależnionej.

Jeśli nie używasz usług AD FS do innych celów (czyli dla innych jednostek uzależnionych), można bezpiecznie likwidowanie wdrożenia usług AD FS na tym etapie.

### <a name="rollback"></a>Wycofywanie

Jeśli odnajdywanie poważnym problemem i nie można rozpoznać je szybko, możesz wybrać wycofywanie rozwiązania do federacyjnego.

Zajrzyj do dokumentacji projektowania i wdrażania federacyjnego, aby uzyskać informacje dotyczące określonego wdrożenia. Proces ten powinien obejmować następujące zadania:

* Konwertowanie domen zarządzanych do uwierzytelniania federacyjnego przy użyciu **Convert MSOLDomainToFederated** polecenia cmdlet.
* Jeśli to konieczne, należy skonfigurować reguły dodatkowe oświadczenia.

### <a name="sync-userprincipalname-updates"></a>Aktualizacje userPrincipalName synchronizacji

W przeszłości aktualizacje **UserPrincipalName** atrybut, który używa usługi synchronizacji z lokalnym środowiskiem, są blokowane, chyba, że oba te warunki są spełnione:

* Użytkownik znajduje się w domenie zarządzanej tożsamości (inne niż federacyjne).
* Użytkownikowi nie przypisano licencji.

Aby dowiedzieć się, jak sprawdzić lub włączyć tę funkcję, zobacz [synchronizować aktualizacje userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Przerzucić bezproblemowe klucz odszyfrowywania protokołu Kerberos Usługa rejestracji Jednokrotnej

Należy często przerzucić klucz odszyfrowywania protokołu Kerberos konta komputera AZUREADSSOACC (który reprezentuje usługę Azure AD). Konto komputera AZUREADSSOACC jest tworzone w lesie usługi Active Directory w środowisku lokalnym. Firma Microsoft zdecydowanie zaleca się przerzucaniu klucz odszyfrowywania protokołu Kerberos co najmniej co 30 dni, aby było zgodne z sposób, że elementy członkowskie domeny usługi Active Directory Prześlij zmiany hasła. Nie istnieje żadne skojarzone urządzenie dołączone do obiektu konta komputera dla AZUREADSSOACC, dlatego należy ręcznie wykonać przerzucania.

Zainicjuj przerzucania bezproblemowe klucz odszyfrowywania protokołu Kerberos Usługa rejestracji Jednokrotnej na serwerze w środowisku lokalnym, który jest uruchomiony program Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [jak mogę przerzucić klucz odszyfrowywania protokołu Kerberos konta komputera AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

Monitorowanie serwerów z uruchomioną agentów uwierzytelniania w celu zapewnienia dostępności rozwiązania. Oprócz liczników wydajności serwera ogólne agentów uwierzytelniania uwidocznić obiektów wydajności, które mogą pomóc Ci zrozumieć statystyki uwierzytelniania i błędy.

Agentów uwierzytelniania rejestrowanie operacji, dzienniki zdarzeń Windows, które znajdują się w aplikacji i Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin usługi.

Można również włączyć rejestrowanie na potrzeby rozwiązywania problemów.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure Active Directory uwierzytelnianie przekazywane](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [pojęć dotyczących projektowania usługi Azure AD Connect](plan-connect-design-concepts.md).
* Wybierz [odpowiednie uwierzytelnianie](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).
* Dowiedz się więcej o [obsługiwane topologie](plan-connect-design-concepts.md).
