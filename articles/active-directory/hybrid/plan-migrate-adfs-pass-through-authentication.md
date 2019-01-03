---
title: 'Program Azure AD Connect: Migrowanie z Federacji do uwierzytelniania przekazywanego usługi Azure AD | Dokumentacja firmy Microsoft'
description: Informacje na temat przenoszenia środowiska tożsamości hybrydowej z Federacji do uwierzytelniania przekazywanego.
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: dcb2d1741a8e62bd317881d3f224d3358cad8778
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557210"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>Migrowanie z Federacji do uwierzytelniania przekazywanego usługi Azure AD
Ten dokument zawiera wskazówki dotyczące przenoszenia z usług AD FS do uwierzytelniania przekazywanego.

>[!NOTE]
>Pobrania kopię tego dokumentu jest dostępna [tutaj](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-pass-through-authentication"></a>Wymagania wstępne dotyczące uwierzytelniania przekazywanego
Następujące wymagania wstępne są wymagane, można było migrować.
### <a name="update-azure-ad-connect"></a>Aktualizacja usługi Azure AD Connect

Co najmniej do pomyślnego przeprowadzenia kroków migracji do uwierzytelniania przekazywanego, powinny mieć [programu Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Ta wersja zawiera istotne zmiany sposobu logowania konwersji odbywa się i zmniejsza całkowity czas migracji z Federacji do uwierzytelniania w chmurze z potencjalnie godziny do minuty.

> [!IMPORTANT]
> Nieaktualne dokumentację, narzędzia i blogi wskazywać konwersji użytkownika krok wymagany podczas konwertowania domeny federacyjne na zarządzane. **Konwertowanie użytkowników** jest nie wymaga już i firma Microsoft pracuje na temat aktualizowania dokumentacji i narzędzi, aby to odzwierciedlić.

Aby zaktualizować program Azure AD Connect do najnowszej wersji, zgodnie z dokumentem [instrukcje aktualizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Planowanie uwierzytelniania agenta liczbę i rozmieszczenie

Uwierzytelnianie przekazywane jest realizowane przez lekki wdrażani serwerze programu platformy Azure AD Connect i na serwerach Windows w środowisku lokalnym. Zainstaluj agentów, jak najbliżej Twojego kontrolery domen Active Directory w celu zmniejszenia opóźnień.

Dla większości uwierzytelniania klientów, dwa lub trzy agenci są wystarczająco dużo, aby uzyskać wysoką dostępność i wydajność i dzierżawcy mogą mieć nie więcej niż 12 agentów zarejestrowany. Pierwszy agent zawsze jest instalowany na samym serwerze AAD Connect. Zapoznaj się [informacji na temat szacunki ruchu sieciowego i wskazówki dotyczące wydajności](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations) , aby zrozumieć ograniczenia agenta i opcji wdrażania agenta.

### <a name="plan-migration-method"></a>Planowanie migracji, metoda

Istnieją dwie metody, aby przeprowadzić migrację z uwierzytelniania federacyjnego PTA i bezproblemowe logowanie Jednokrotne. Metody, których używasz będzie zależeć od tego, jak usługi AD FS zostały pierwotnie skonfigurowane.

- **Używanie programu Azure AD Connect**. Jeśli usługi AD FS zostały pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect, a następnie zmień uwierzytelnianie przekazujących *musi* można wykonać za pomocą Kreatora programu Azure AD Connect.

Korzystając z usługi Azure AD Connect, uruchomieniu polecenia cmdlet Set-MsolDomainAuthentication dla Ciebie automatycznie po zmianie metody logowania użytkownika i dlatego masz żadnej kontroli nad tym go unfederating wszystkie zweryfikowanych domen federacyjnych w dzierżawie usługi Azure AD.  
‎  
> [!NOTE]
> W tej chwili nie można uniknąć niezaznaczone federating wszystkich domenach w Twojej dzierżawie, gdy zmienią się użytkownika logowania do uwierzytelniania przekazywanego po AAD Connect pierwotnie użyty do skonfigurowania usług AD FS dla Ciebie.  
‎
- **Używanie programu Azure AD Connect przy użyciu programu PowerShell**. Ta metoda może być używana tylko wtedy, gdy usługi AD FS nie zostało skonfigurowane za pomocą usługi Azure AD Connect. Nadal musisz zmienić metodę logowania użytkownika za pomocą Kreatora programu Azure AD Connect, ale podstawowe różnica polega na to, że nie będzie ona automatycznie działać polecenia cmdlet Set-MsolDomainAuthentication dla Ciebie, ponieważ ma ona nie świadomości farmy usług AD FS i dlatego mają pełną kontrolę nad które domeny są konwertowane, w jakiej kolejności.

Aby zrozumieć, jakie metody należy użyć, należy wykonać kroki opisane w poniższej sekcji.

#### <a name="verify-current-user-sign-in-settings"></a>Sprawdź bieżące ustawienia logowania użytkownika

Sprawdź bieżące logowania ustawień użytkownika, logując się do portalu usługi Azure AD [ https://aad.portal.azure.com ](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego. 

W **użytkownika logowania** sekcji, upewnij się, że **Federacji** jest **włączone** i **bezproblemowego logowania jednokrotnego** i  **Uwierzytelnianie przekazywane** są **wyłączone**. 

![Obraz 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Sprawdź, jak został skonfigurowany Federacji

   1. Przejdź do serwera Azure AD Connect i uruchom program Azure AD Connect, a następnie wybierz **Konfiguruj**.
   2. Na **dodatkowe zadania** ekranu, wybierz opcję **wyświetlanie bieżącej konfiguracji** , a następnie wybierz **dalej**.</br>
   ![Obraz 31](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. W **Przegląd rozwiązania** ekranu, przewiń w dół do **Active Directory Federation Services (AD FS)**.</br>
   Jeśli widzisz, że konfiguracja usług AD FS znajduje się w tej sekcji, a następnie można bezpiecznie przyjąć usług AD FS zostały pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect i dlatego konwersji użytkownika domeny z federacyjnego zarządzane mogą opierać się za pośrednictwem programu Azure AD Connect  **Zmiany logowania użytkownika** opcji ten proces opisano szczegółowo w sekcji **opcja 1: Konfigurowanie uwierzytelniania przekazywanego za pomocą usługi Azure AD Connect**.  
‎
   4. Jeśli nie widzisz Active Directory Federation Services na liście bieżące ustawienia, a następnie konieczne będzie ręczne przekonwertowanie domen z federacyjnego na zarządzanego za pośrednictwem programu PowerShell, która została szczegółowo opisana w sekcji **opcja 2 — przełącznik z Federacji PTA przy użyciu programu PowerShell i Azure AD Connect**.

### <a name="document-current-federation-settings"></a>Bieżące ustawienia Federacji dokumentu

Możesz znaleźć bieżące ustawienia Federacji, uruchamiając polecenie cmdlet polecenia Get-MsolDomainFederationSettings.

Polecenie to:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Na przykład:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```


Zweryfikuj ustawienia, które może zostały dostosowane do Federacji projektowania i wdrażania dokumentacji, w szczególności **PreferredAuthenticationProtocol**, **SupportsMfa**, i  **PromptLoginBehavior**.

Więcej informacji na temat co te ustawienia można znaleźć poniżej.

[Monituj usług federacyjnych Active Directory = Obsługa parametrów logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Zestaw MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Jeśli wartość SupportsMfa jest aktualnie ustawiona na wartość "True" następnie oznacza to, że używasz rozwiązania MFA On-Premises iniekcję wezwanie 2nd factor do przepływu uwierzytelniania użytkownika. To nie będzie już działać dla scenariuszy uwierzytelniania usługi Azure AD i zamiast tego trzeba będzie korzystać z usługi Azure MFA (oparte na chmurze) do wykonania tej samej funkcji. Ostrożnie oceny wymagań dotyczących uwierzytelniania Wieloskładnikowego przed skierowaniem ich do przodu i upewnij się, że wiesz, jak korzystać z usługi Azure MFA, skutki licencjonowania i procesu rejestracji użytkowników końcowych przed przekonwertowaniem Twoich domen.

#### <a name="backup-federation-settings"></a>Ustawienia tworzenia kopii zapasowej Federacji

Mimo że nie będzie zmieniana na inne jednostki uzależnionej strony na farmie usług AD FS w trakcie tego procesu, zalecane jest aby upewnić się, że masz bieżącego prawidłowej kopii zapasowej można przywrócić farmy usług AD FS. Można to zrobić za pomocą bezpłatne Microsoft [usługi AD FS szybkiego przywrócenia narzędzie](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). To narzędzie może służyć do tworzenia kopii zapasowych i przywracanie usług AD FS, albo do istniejącej farmy lub nowej farmy.

Jeśli wybierzesz nie korzystała z usługi AD FS szybkiego przywrócenia narzędzie, następnie co najmniej należy wyeksportować "Microsoft Office 365 platforma tożsamości" jednostki uzależnionej relacja zaufania i wszystkie skojarzone niestandardowych reguł oświadczeń, które mogły zostać dodane. Można to zrobić za pomocą w poniższym przykładzie programu PowerShell

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>Zagadnienia dotyczące wdrażania i użycia usług AD FS

### <a name="validate-your-current-ad-fs-usage"></a>Sprawdź poprawność bieżące użycie usług AD FS

Przed przekonwertowaniem z Federacji na zarządzane, należy rozważyć ściśle jak używasz usług AD FS dzisiaj dla usługi Azure AD/Office 365 i innych aplikacji (jednostki uzależnionej relacja zaufania jednostek uzależnionych). W szczególności należy wziąć pod uwagę poniższej tabeli:

| Jeśli| Następnie |
|-|-|
| Chcesz zachować usług AD FS w przypadku innych aplikacji.| Będą używać usługi Azure AD i usług AD FS i, należy wziąć pod uwagę doświadczenia użytkownika końcowego w wyniku. Użytkownicy mogą muszą uwierzytelnić się dwa razy w niektórych scenariuszach, gdy do usługi Azure AD (gdzie otrzymają one logowania jednokrotnego i nowszych wersjach do innych aplikacji, takich jak Office 365) i ponownie dla wszystkich aplikacji, nadal powiązane z usług AD FS jako zaufanie jednostki uzależnionej. |
| Usługi AD FS są silnie dostosowanego i architekturze w ustawieniach dostosowania określone w pliku onload.js, którego nie można zduplikować w usłudze Azure AD (na przykład zmienisz środowisko logowania, aby użytkownicy tylko wprowadź formacie SamAccountName swoją nazwę użytkownika w przeciwieństwie nazwy UPN, lub silnie marki środowisko logowania)| Należy sprawdzić, czy bieżący wymagań dostosowania użytkownika mogą zostać spełnione przez usługę Azure AD przed kontynuowaniem. Sekcje znakowania programu AD FS i opcji dostosowania usług AD FS, aby uzyskać dodatkowe informacje i wskazówki można znaleźć.|
| Blokują starsze uwierzytelnianie klientów za pośrednictwem usług AD FS.| Rozważ zastąpienie kontrolki do blokowania uwierzytelnianie starszych klientów aktualnie dostępne w usługach AD FS przy użyciu kombinacji [dostępu warunkowego kontroluje dla starszych uwierzytelniania](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) i [dostępu klienta programu Exchange Online Reguły](http://aka.ms/EXOCAR). |
| Możesz wymagać od użytkowników wykonać uwierzytelnianie wieloskładnikowe dla rozwiązania z serwerem usługi MFA w środowisku lokalnym, podczas uwierzytelniania usług AD FS.| Nie można iniekcję żądanie usługi MFA za pomocą lokalnego rozwiązania MFA do przepływ uwierzytelniania dla domeny zarządzanej, jednak można użyć usługi Azure MFA, w tym celu przyszłości raz domeny jest konwertowany. Jeśli użytkownicy nie korzystają już dzisiaj usługi Azure MFA, to będzie obejmować kroku rejestracji jednorazowej przez użytkownika końcowego, który trzeba będzie przygotowania i komunikują się użytkownikom końcowym. |
| Używasz zasad kontroli dostępu (reguł autoryzacji) już dziś w usługach AD FS do kontrolowania dostępu do usługi Office 365.| Należy wziąć pod uwagę, zastępując je z równoważne usługi Azure AD [zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) i [reguł dostępu klienta Exchange Online](http://aka.ms/EXOCAR).|

### <a name="considerations-for-common-ad-fs-customizations"></a>Zagadnienia dotyczące typowych dostosowania usług AD FS

#### <a name="inside-corporate-network-claim"></a>Oświadczenia wewnątrz sieci firmowej

Oświadczenie InsideCorporateNetwork jest wystawione przez usługi AD FS, jeśli uwierzytelnianie użytkownika znajduje się wewnątrz sieci firmowej. To oświadczenie można następnie przekazywane do usługi Azure AD i umożliwia obejście uwierzytelniania wieloskładnikowego na podstawie lokalizacji sieciowej użytkownika. Zobacz [zaufane adresy IP dla użytkowników federacyjnych](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) informacji na temat sposobu określenia, czy masz to obecnie włączone w usługach AD FS.

Oświadczenie InsideCorporateNetwork nie będzie dostępna już po domen są konwertowane do uwierzytelniania przekazywanego. [Lokalizacje z nazwą w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) może być używany do zastępowania tej funkcji.

Po skonfigurowaniu lokalizacji o nazwie wszystkie zasady dostępu warunkowego skonfigurowane do dołączania lub wykluczania lokalizacje sieciowe "Wszystkie zaufane lokalizacje" lub "Zaufane adresy IP usługi MFA" muszą zostać zaktualizowane w celu uwzględnienia nowo utworzonej lokalizacji o nazwie.

Zobacz [Active Directory warunkowego lokalizacje dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations) więcej informacji na temat warunek lokalizacji w funkcji dostępu warunkowego.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrydowe usługi Azure AD urządzeń w miejscu pracy

Dołączenie urządzenia do usługi Azure AD umożliwia tworzenie reguł dostępu warunkowego, które wymuszają urządzenia spełniające standardy dostępu zabezpieczeń i zgodności. Umożliwia ono użytkownikom logować się do urządzenia przy użyciu pracy organizacji lub konta służbowego, a nie osobistego konto. Urządzeń dołączonych do usługi Azure AD hybrydowe umożliwia dołączanie urządzeń przyłączonych do domeny usługi AD do usługi Azure AD. Środowisko federacyjnego mogły zostać skonfigurowane za pomocą tej funkcji.

Aby upewnić się, że hybrydowego przyłączenia kontynuuje pracę dla nowych urządzeń przyłączone do domeny po domen zostały przekonwertowane na uwierzytelniania przekazywanego, należy skonfigurować program Azure AD Connect do synchronizowania kont komputerów usługi Active Directory dla klientów z systemem Windows 10 do Usługa Azure AD. Dla kont komputerów Windows 7 i Windows 8 Dołącz do hybrydowego użyje bezproblemowe logowanie Jednokrotne do zarejestrowania komputera w usłudze Azure AD, a nie trzeba synchronizować je, podobnie jak w przypadku urządzeń z systemem Windows 10. Jednak trzeba będzie wdrożyć plik workplacejoin.exe zaktualizowane (za pośrednictwem pliku .msi) dla tych klientów niskiego poziomu, dzięki czemu mogą rejestrować się za pomocą bezproblemowego logowania jednokrotnego. [Pobierz plik MSI](https://www.microsoft.com/download/details.aspx?id=53554).

Aby uzyskać więcej informacji na temat tego wymagania, zobacz [w jaki sposób skonfigurować hybrydowych urządzeń w przyłączonych do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)

#### <a name="branding"></a>Znakowanie

Twoja organizacja może mieć [dostosowanej strony logowania usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) do wyświetlania informacji bardziej odpowiednie do organizacji. Jeśli tak, należy wziąć pod uwagę co podobne [dostosowania strony logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Podobne dostosowania są dostępne, należy się spodziewać, niektóre zmiany wizualne. Można uwzględnić oczekiwane zmiany w komunikacji dla użytkowników końcowych.

> [!NOTE]
> Logo firmy jest dostępna tylko wtedy, gdy masz zakupionych licencja podstawowa lub Premium dla usługi Azure AD lub licencji usługi Office 365.

## <a name="planning-for-smart-lockout"></a>Planowanie inteligentnej blokady

Usługa Azure AD, inteligentnej blokady chroni przed atakami siłowymi hasła i uniemożliwia konta usługi Active Directory w środowisku lokalnym jest zablokowane, gdy jest używane uwierzytelnianie przekazujących ustawiono zasady grupy blokady konta w usłudze Active Directory. 

Aby uzyskać więcej informacji na temat [funkcji inteligentnej blokady i zmień jego konfigurację](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="planning-deployment-and-support"></a>Planowanie wdrażania i obsługi

### <a name="plan-the-maintenance-window"></a>Planowanie okna obsługi

Gdy sam proces konwersji domeny odbywa się relatywnie szybko, usługi Azure AD może nadal wysyłać niektóre żądania uwierzytelnienia do serwerów usług AD FS na okres maksymalnie 4 godziny po zakończeniu konwersji domeny. W tym oknie cztery godziny, a także w zależności od różnych pamięci podręcznych po stronie usługi, te uwierzytelnienia nie mogą być akceptowane przez usługę Azure AD, a użytkownicy otrzymają błąd, będą mogli pomyślnie uwierzytelnić nadal usług AD FS, ale nie będzie akceptował usługi Azure AD użytkownika wystawiony token, zgodnie z tym relacjami zaufania federacji zostało usunięte.

> [!NOTE]
> Spowoduje to tylko wpływ na użytkowników, którzy uzyskiwać dostęp do usług za pośrednictwem przeglądarki przedziale konwersji post do momentu wyczyszczenia pamięci podręcznej po stronie usługi. Starszych klientów (program Exchange ActiveSync, Outlook 2010 i 2013) nie powinna mieć wpływ, jak usługi Exchange Online utrzymuje pamięci podręcznej poświadczeń na okres czasu, który jest używany konieczność ponownego uwierzytelnienia użytkownika w trybie dyskretnym bez konieczności przejść z powrotem do usług AD FS. Poświadczenia przechowywane na urządzeniu dla tych klientów są używane do ponownego uwierzytelniania się dyskretnie po to pamięci podręcznej jest czyszczona i dlatego użytkownicy nie powinni odbierać żadnych monitów o hasło, w wyniku procesu konwersji domeny. Z drugiej strony, klientów nowoczesnego uwierzytelniania (Office 2013 grudnia 2016 r., IOS i aplikacje dla systemu Android) tych użycia nieprawidłowy Token odświeżania w celu uzyskania nowych tokenów dostępu opinię dotyczącą przedłużenia dostępu do zasobów, a nie usług AD FS będzie i dlatego jest odporny na żadnych monitów o hasło jako wynik konwersji domeny przetwarzania i będą nadal działać bez wymaga dodatkowej konfiguracji.
> [!IMPORTANT]
> Nie zamknięcia środowiska usług AD FS lub usunąć zaufanie jednostki uzależnionej, dopóki upewnieniu się, że wszyscy użytkownicy są pomyślnym uwierzytelnieniu przy użyciu uwierzytelniania w chmurze usługi Office 365.

### <a name="plan-for-rollback"></a>Plan wycofania

Jeśli poważnym problemem znajduje się i nie można rozpoznać szybko, można zdecydować wycofać rozwiązanie z powrotem do Federacji. Należy zaplanować, co należy zrobić, jeśli wdrożenie nie jest akceptowana zgodnie z planem. Jeśli konwersja domeny lub użytkownicy nie powiedzie się podczas wdrażania lub musisz wycofania do Federacji, trzeba zrozumieć sposób ograniczyć ewentualnej awarii i ograniczyć wpływ na użytkowników.

#### <a name="rolling-back"></a>Wycofywanie

Aby uzyskać informacje dotyczące określonego wdrożenia można znaleźć w dokumentacji projektu i wdrożenia federacyjnego. Proces ten powinien obejmować:

* Konwertuj domen zarządzanych do federacyjnych, za pomocą konwersji MSOLDomainToFederated 

* Jeśli to konieczne, konfigurowanie dodatkowych oświadczeń reguły.

### <a name="plan-change-communications"></a>Planowanie komunikacji zmiany

Ważnym elementem planowania, wdrażania i obsługi zapewnienie, że użytkownicy końcowi są aktywnie informacje na temat zmiany i jakie mogą wystąpić lub należy wykonać. 

Po wdrożeniu uwierzytelniania przekazywanego i bezproblemowe logowanie Jednokrotne logowanie użytkownika końcowego ulegnie zmianie podczas uzyskiwania dostępu do usługi Office 365 i inne skojarzone zasoby uwierzytelnienia przy użyciu usługi Azure AD. Użytkownicy spoza sieci zobaczą teraz usługi Azure AD strony logowania, a nie nastąpi przekierowanie do strony oparte na formularzach, przedstawiony przez zewnętrzne serwery Proxy aplikacji sieci Web.

Istnieje wiele elementów do planowania strategii komunikacji. Należą do nich:

* Powiadamianie użytkowników o nadchodzących i wydania funkcji za pośrednictwem
  * Wiadomości e-mail i innych wewnętrznych kanałów komunikacji
  * Elementy wizualne, takie jak plakaty
  * Executive łączności na żywo lub innych
* Określanie, kto będzie dostosowywać i który będzie wysyłać komunikatów i kiedy.

## <a name="implementing-your-solution"></a>Wdrażanie rozwiązania

Po zaplanowaniu rozwiązania, można przystąpić do jej wdrożenia. Implementacja obejmuje następujące składniki:

   1. Przygotowywanie do bezproblemowego logowania jednokrotnego na
   2. Zmiana metody logowania uwierzytelniania przekazywanego i włączanie bezproblemowego logowania jednokrotnego

## <a name="step-1--prepare-for-seamless-sso"></a>Krok 1 — Przygotowanie bezproblemowego logowania jednokrotnego

Na urządzeniach za pomocą bezproblemowego logowania jednokrotnego należy dodać adres URL usług Azure AD do ustawienia strefy Intranet użytkowników za pomocą zasad grupy w usłudze Active Directory.

Domyślnie przeglądarka automatycznie oblicza poprawnej strefy Internet lub Intranet, z określonym adresem URL. Na przykład "http://contoso/"mapuje do strefy Intranet, podczas gdy"http://intranet.contoso.com/" mapuje do strefy Internet (ponieważ jest to adres URL zawiera kropkę). Przeglądarki będzie wysyłał bilety protokołu Kerberos do punktu końcowego w chmurze, takich jak usługa Azure AD adres URL, chyba że dodasz do strefy Intranet w przeglądarce adres URL.

Postępuj zgodnie z [kroki, aby wdrożyć](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) zmiany wymagane w celu urządzeń.

> [!IMPORTANT]
> Wprowadzenie tej zmiany nie wpłynie sposób użytkownicy logują się do usługi Azure AD. Jest ważne, że ta konfiguracja jest stosowany do wszystkich urządzeń przed rozpoczęciem pracy z kroku 3 też pamiętać, że użytkownikom logowanie się na urządzeniach, które nie odebrały tej konfiguracji należy po prostu wprowadź nazwę użytkownika i hasło do logowania do usługi Azure AD.

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>Krok 2 — Zmień metodę logowania PTA i Włącz bezproblemowe logowanie Jednokrotne

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>Opcja A: Konfigurowanie PTA za pomocą usługi Azure AD Connect

Użyj tej metody, gdy usługi AD FS początkowo został skonfigurowany za pomocą usługi Azure AD Connect. Nie można użyć tej metody, jeśli usługi AD FS nie zostało pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect.

> [!IMPORTANT]
> Należy pamiętać, wykonując kroki opisane poniżej swoje domeny zostanie przekonwertowana z Sfederowane do zarządzanego. Zapoznaj się z sekcją metody Plan migracji, aby uzyskać więcej informacji.[](#_Plan_Migration_Method)

Najpierw trzeba zmienić metodę logowania jednokrotnego:

   1. Serwerze programu Azure AD Connect należy otworzyć kreatora.
   2. Wybierz **zmiana użytkownika logowania** , a następnie wybierz **dalej**. 
   3. W **nawiązywanie połączenia z usługi Azure AD** ekranu Podaj nazwę użytkownika i hasło administratora globalnego.
   4. **Logowania użytkownika** ekranu, zmień przycisk radiowy z **Federacja z usługami AD FS** do **uwierzytelniania przekazywanego**, wybierz opcję **włączyć logowanie jednokrotne**  polecenie **dalej**.
   5. Włącz logowanie jednokrotne na ekranie wprowadź poświadczenia konta administratora domeny, a następnie wybierz przycisk Dalej.  

   > [!NOTE]
   > Poświadczenia administratora domeny są wymagane do włączenie bezproblemowego logowania jednokrotnego, ponieważ proces wykonuje następujące czynności, które wymagają tych podwyższonym poziomem uprawnień. Poświadczenia administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Są one używane tylko w celu włączenia tej funkcji i następnie zostaje odrzucone po pomyślnym ukończeniu
   >
   > * Konto komputera o nazwie AZUREADSSOACC (który reprezentuje usługę Azure AD) jest tworzony w sieci lokalnej usługi Active Directory (AD).
   > * Klucz odszyfrowywania protokołu Kerberos konta komputera jest udostępniony w bezpieczny sposób za pomocą usługi Azure AD.
   > * Ponadto dwa Kerberos głównych nazw usług (SPN) są tworzone do reprezentowania dwa adresy URL, które są używane podczas logowania w usłudze Azure AD.
   > * Poświadczenia administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Są one używane tylko w celu włączenia tej funkcji i następnie zostaje odrzucone po pomyślnym ukończeniu

   6. W **gotowe do konfiguracji** ekranu, upewnij się, że **w procesie synchronizacji uruchomić po zakończeniu konfiguracji** pole wyboru jest zaznaczone. Następnie wybierz pozycję **Konfiguruj**.</br>
   ![Obraz](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. Otwórz **portalu usługi Azure AD**, wybierz opcję **usługi Azure Active Directory**, a następnie wybierz pozycję **program Azure AD Connect**.
   8. Upewnij się, że **federacyjnej jest wyłączona** podczas **bezproblemowego logowania jednokrotnego** i **— dostęp próbny dokładnego uwierzytelniania** są **włączone**.</br>
   ![Obraz](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

Następnie należy wdrożyć dodatkowe metody uwierzytelniania. Otwórz **witryny Azure portal**, przejdź do **usługi Azure Active Directory, Azure AD Connect** i kliknij przycisk **uwierzytelniania przekazywanego**.

Z **uwierzytelniania przekazywanego** stronie, kliknij przycisk Pobierz. Z **Pobierz** agenta ekranu, kliknij pozycję **Zaakceptuj warunki i Pobierz**.

Rozpocznie się pobieranie agentów dodatkowego uwierzytelniania. Instalowanie dodatkowych agenta uwierzytelniania na serwerze przyłączonym do domeny. 

> [!NOTE]
> Pierwszy agent zawsze jest instalowany na serwerze programu Azure AD Connect, sama jako część zmian konfiguracji w sekcji logowania użytkownika w narzędziu Azure AD Connect. Żadnych dodatkowych agentów uwierzytelniania należy zainstalować na osobnym serwerze. Zalecane jest między 2 – 3 dodatkowe uwierzytelnianie dostępne agenty. 

Uruchom instalację agenta uwierzytelniania. Podczas instalacji, musisz podać poświadczenia **administratora globalnego** konta.

![Obraz 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![Obraz 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

Po zainstalowaniu agenta uwierzytelniania możesz przejść wstecz do strony kondycji agenta uwierzytelniania przekazywanego, aby sprawdzić stan dodatkowych agentów.


Przejdź do testowania i następne kroki.

> [!IMPORTANT]
> Pomiń sekcję opcja B: Przełącz się z Federacji PTA przy użyciu usługi Azure AD Connect i programu PowerShell tak jak zaprezentowano w tej sekcji nie mają zastosowania.  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>Opcja B — przełącznik z Federacji PTA przy użyciu programu PowerShell i Azure AD Connect

Użyj tej opcji, gdy federacyjnym nie zostało wstępnie skonfigurowane przy użyciu usługi Azure AD Connect. Najpierw należy włączyć uwierzytelnianie przekazujących:

   1. Serwerze programu Azure AD Connect należy otworzyć kreatora.
   2. Wybierz **zmiana użytkownika logowania** , a następnie wybierz **dalej**.
   3. W **nawiązywanie połączenia z usługi Azure AD** ekranu Podaj nazwę użytkownika i hasło administratora globalnego.
   4. Na **logowania użytkownika** ekranu, zmień przycisk radiowy z **nie należy konfigurować** do **uwierzytelniania przekazywanego**, wybierz opcję **włączyć logowanie jednokrotne** polecenie **dalej**.
   5. W **włączyć logowanie jednokrotne** ekranu, wprowadź poświadczenia konta administratora domeny, a następnie wybierz **dalej**.

   > [!NOTE]
   > Poświadczenia administratora domeny są wymagane do włączenie bezproblemowego logowania jednokrotnego, ponieważ proces wykonuje następujące czynności, które wymagają tych podwyższonym poziomem uprawnień. Poświadczenia administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Są one używane tylko w celu włączenia tej funkcji i następnie zostaje odrzucone po pomyślnym ukończeniu.
   >
   > * Konto komputera o nazwie AZUREADSSOACC (który reprezentuje usługę Azure AD) jest tworzony w sieci lokalnej usługi Active Directory (AD).
   > * Klucz odszyfrowywania protokołu Kerberos konta komputera jest udostępniony w bezpieczny sposób za pomocą usługi Azure AD.
   > * Ponadto dwa Kerberos głównych nazw usług (SPN) są tworzone do reprezentowania dwa adresy URL, które są używane podczas logowania w usłudze Azure AD.

   6. W **gotowe do konfiguracji** ekranu, upewnij się, że **w procesie synchronizacji uruchomić po zakończeniu konfiguracji** pole wyboru jest zaznaczone. Następnie wybierz pozycję **Konfiguruj**.</br>
   ‎![Obraz](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   Podczas wybierania Konfiguruj, wykonywane są następujące kroki:
   * Pierwszy Agent uwierzytelniania przekazywanego jest zainstalowany
   * Funkcja przekazywania jest włączona
   * Bezproblemowe logowanie jednokrotne jest włączona.  
   7. Upewnij się, że **Federacji** jest nadal **włączone** i **bezproblemowego logowania jednokrotnego** i **— dostęp próbny dokładnego uwierzytelniania** są teraz włączone.
   ![Obraz 2](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. Wybierz **uwierzytelniania przekazywanego** i sprawdź, że jest w stanie **Active**.</br>
   
   Jeśli Agent uwierzytelniania nie jest aktywne, postępuj zgodnie z [te kroki rozwiązywania problemów](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) przed kontynuowaniem procesu konwersacji domeny w następnym kroku. Istnieje ryzyko, co powoduje awarię uwierzytelniania po skonwertowaniu domen przed sprawdzania poprawności agentów PTA zainstalowano pomyślnie i czy ich stan jest wyświetlany jako "Aktywny" w witrynie Azure portal.  
   9. Następnie należy wdrożyć agentów dodatkowego uwierzytelniania. Otwórz **witryny Azure portal**, przejdź do **usługi Azure Active Directory**, **program Azure AD Connect** i kliknij przycisk **uwierzytelniania przekazywanego**.
   10. Z **uwierzytelniania przekazywanego** kliknij na **Pobierz** przycisku. Z **Pobierz agenta** ekranu, kliknij przycisk na **Zaakceptuj warunki i Pobierz**.
   
   Rozpocznie się pobieranie agentów dodatkowego uwierzytelniania. Instalowanie dodatkowych agenta uwierzytelniania na serwerze przyłączonym do domeny.

  > [!NOTE]
  > Pierwszy agent zawsze jest instalowany na serwerze programu Azure AD Connect, sama jako część zmian konfiguracji w sekcji logowania użytkownika w narzędziu Azure AD Connect. Żadnych dodatkowych agentów uwierzytelniania należy zainstalować na osobnym serwerze. Zalecane jest między 2 – 3 dodatkowe uwierzytelnianie dostępne agenty.
  
   11. Uruchom instalację agenta uwierzytelniania. Podczas instalacji, musisz podać poświadczenia **administratora globalnego** konta.</br>
   ![Obraz 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![Obraz 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. Po zainstalowaniu agenta uwierzytelniania możesz przejść wstecz do strony kondycji agenta uwierzytelniania przekazywanego, aby sprawdzić stan dodatkowych agentów.

W tym momencie federacyjnej jest nadal włączony i działa dla Twoich domen. Aby kontynuować wdrożenie, każdej domeny musi zostać skonwertowany z Sfederowane do zarządzanego, więc uwierzytelniania przekazywanego, który rozpoczyna się obsługiwać żądania uwierzytelniania dla domeny.

Nie wszystkie domeny wymagają można przekonwertować na tym samym czasie, możesz wybrać na początek z domeny testu dla dzierżawy produkcyjnej lub domeny o najmniejszej liczby użytkowników.

Konwersja odbywa się przy użyciu modułu Azure AD PowerShell.

   1. Otwórz **PowerShell** i zaloguj się do usługi Azure AD przy użyciu **administratora globalnego** konta.
   2. Aby przekonwertować pierwszej domeny, uruchom następujące polecenie:
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. Otwórz **portalu usługi Azure AD**, wybierz opcję **usługi Azure Active Directory**, a następnie wybierz pozycję **program Azure AD Connect**.  
   4. Po przekonwertowaniu wszystkich domen federacyjnych, upewnij się, że **federacyjnej jest wyłączona** podczas **bezproblemowego logowania jednokrotnego** i **uwierzytelniania przekazywanego** są  **Włączone**.</br>
   ![Obraz](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>Testowanie i następne kroki

### <a name="test-pass-through-authentication"></a>Test uwierzytelniania przekazywanego 

Korzystając z Twojej dzierżawy został Federacji, użytkownicy zostały przekierowaniu ze strony logowania usługi Azure AD do środowiska usług AD FS. Teraz, gdy dzierżawy jest skonfigurowany do używania uwierzytelniania przekazywanego zamiast Federacji, użytkownicy nie będą Pobierz przekierowane do usług AD FS i zamiast tego będzie zalogować się bezpośrednio za pomocą strony logowania usługi Azure AD.

Otwórz program Internet Explorer w trybie InPrivate w celu uniknięcia bezproblemowe logowanie Jednokrotne logowanie automatyczne, a następnie przejdź do strony logowania usługi Office 365 ([http://portal.office.com](http://portal.office.com/)). Typ **UPN** użytkownika i kliknij przycisk **dalej**. Upewnij się, że wpisz nazwę UPN użytkownika hybrydowych, które zostało synchronizowane z lokalnej usługi Active Directory i który został wcześniej federacyjnych. Użytkownik zostanie wyświetlony ekran do wpisywania nazwy użytkownika i hasła.

![Obraz 18](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![Obraz 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

Po wpisaniu hasła, należy uzyskać przekierowanie do portalu usługi Office 365.

![Obraz 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>Testowanie bezproblemowego logowania jednokrotnego

   1. Zaloguj się do domeny przyłączyć maszyny, która jest połączona z siecią firmową. 
   2. Otwórz **programu Internet Explorer** lub **Chrome** i przejdź do jednej z następujących adresów URL:   
      ‎  
      ‎[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (Zamień Contoso domenę).

      Użytkownik chwilę nastąpi przekierowanie do strony logowania usługi Azure AD i zostanie wyświetlony komunikat "Próby zarejestrowania Cię" i nie powinien wyświetlony monit o podanie nazwy użytkownika lub hasło.</br>
   ![Obraz 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. Następnie użytkownik będzie pobrać przekierowanie i pomyślnie zalogował się do panelu dostępu:

> [!NOTE]
> Bezproblemowe logowanie jednokrotne działa w przypadku usługi Office 365, które obsługuje wskazówkę dotyczącą domeny (na przykład myapps.microsoft.com/contoso.com). Portalu usługi Office 365 (portal.office.com) aktualnie nie obsługuje wskazówkę dotyczącą domeny i w związku z tym oczekuje się, że użytkownicy będą musieli wpisać ich nazwy UPN. Gdy nazwy UPN jest podana, bezproblemowego logowania jednokrotnego na można pobrać biletu protokołu Kerberos w imieniu użytkownika i rejestrowanie ich w bez wpisywania hasła.
> [!TIP]
> Zaleca się wdrożenie [dołączenie do hybrydowej usługi AD platformy Azure w systemie Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) dla ulepszone funkcji logowania jednokrotnego.

### <a name="removal-of-the-relying-party-trust"></a>Usuwanie zaufania jednostki uzależnionej

Po zweryfikowaniu wszystkich użytkowników i klientów są pomyślnym uwierzytelnieniu za pomocą usługi Azure AD, można uważać, można bezpiecznie usunąć zaufanie jednostki uzależnionej w usłudze Office 365.

Jeśli usługi AD FS nie jest używany do innych celów (inne zaufania jednostek uzależnionych zostały skonfigurowane), jest bezpieczne teraz likwidowanie wdrożenia usług AD FS.

### <a name="rollback"></a>Wycofywanie

Jeśli poważnym problemem znajduje się i nie można rozpoznać szybko, można zdecydować wycofać rozwiązanie z powrotem do Federacji.

Aby uzyskać informacje dotyczące określonego wdrożenia można znaleźć w dokumentacji projektu i wdrożenia federacyjnego. Proces ten powinien obejmować:

* Konwertuj domen zarządzanych do federacyjnych, za pomocą konwersji MSOLDomainToFederated
* Jeśli to konieczne, konfigurowanie dodatkowych oświadczeń reguły.

### <a name="enable-synchronization-of-userprincipalname-updates"></a>Włącz synchronizację aktualizacji userPrincipalName

W przeszłości aktualizacje atrybut UserPrincipalName przy użyciu usługi synchronizacji ze środowiska lokalnego zostało zablokowane, chyba że oba te warunki są spełnione:

* Użytkownik jest zarządzany (inne niż federacyjne).
* Użytkownikowi nie przypisano licencji.

Aby uzyskać instrukcje, jak sprawdzić, lub włączyć tę funkcję zapoznaj się z następującym artykułem:

[Synchronizowanie aktualizacji userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Przerzucić bezproblemowe klucz odszyfrowywania protokołu Kerberos Usługa rejestracji Jednokrotnej

Ważne jest, aby często przerzucić klucz odszyfrowywania protokołu Kerberos konta komputera AZUREADSSOACC (który reprezentuje usługę Azure AD) utworzone w lokalnym lasem usługi AD. Zdecydowanie zalecamy przedłużenie klucz odszyfrowywania protokołu Kerberos co najmniej co 30 dni wyrównać jak członkowie domeny usługi Active Directory Prześlij zmiany hasła. Ponieważ nie ma żadnego skojarzonego urządzenia dołączone do obiektu konta komputera AZUREADSSOACC przerzucania musi być wykonywane ręcznie.

Wykonaj następujące kroki na serwerze lokalnym, na którym uruchomiony jest program Azure AD Connect, aby zainicjować przerzucania klucza odszyfrowywania protokołu Kerberos.

[Jak je czy przerzucić klucz odszyfrowywania protokołu Kerberos konta komputera AZUREADSSOACC](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

Serwery z uruchomioną agentów uwierzytelniania powinny być monitorowane w celu zapewnienia dostępności rozwiązania. Oprócz liczników wydajności serwera ogólne agentów uwierzytelniania uwidocznić obiektów wydajności, które mogą służyć do zrozumienia statystyki uwierzytelniania i błędy.

Agentów uwierzytelniania Rejestruj operacje dziennikach zdarzeń Windows w obszarze "Aplikacji i usług Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin".

Można ją włączyć dzienniki do rozwiązywania problemów, jeśli to konieczne.

Zobacz więcej informacji na temat [monitorowanie i rejestrowanie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Kolejne kroki

- [Zagadnienia dotyczące projektowania przy korzystaniu z programu Azure AD Connect](plan-connect-design-concepts.md)
- [Wybierz odpowiednie uwierzytelnianie](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Obsługiwane topologie](plan-connect-design-concepts.md)
