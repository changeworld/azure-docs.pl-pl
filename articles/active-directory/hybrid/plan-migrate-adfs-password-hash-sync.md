---
title: 'Program Azure AD Connect: Migrowanie z Federacji na synchronizację skrótów haseł w usłudze Azure AD | Dokumentacja firmy Microsoft'
description: Informacje na temat przenoszenia środowiska tożsamości hybrydowej z Federacji na synchronizację skrótów haseł.
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
ms.openlocfilehash: c226eb19dbd2049c486acfb1ffb9423fdb1dad43
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410265"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>Migrowanie z Federacji na synchronizację skrótów haseł w usłudze Azure AD
Ten dokument zawiera wskazówki dotyczące przenoszenia z usług AD FS do synchronizacji skrótów haseł.

>[!NOTE]
>Pobrania kopię tego dokumentu jest dostępna [tutaj](https://aka.ms/ADFSTOPHSDPDownload).


## <a name="prerequisites-for-the-migration"></a>Wymagania wstępne dotyczące migracji 
Następujące wymagania wstępne są wymagane, można było migrować.
### <a name="update-azure-ad-connect"></a>Aktualizacja usługi Azure AD Connect

Co najmniej do pomyślnego przeprowadzenia kroków migracji do uwierzytelniania przekazywanego, powinny mieć [programu Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Ta wersja zawiera istotne zmiany sposobu logowania konwersji odbywa się i zmniejsza całkowity czas migracji z Federacji do uwierzytelniania w chmurze z potencjalnie godziny do minuty.

> [!IMPORTANT]
> Nieaktualne dokumentację, narzędzia i blogi wskazywać konwersji użytkownika krok wymagany podczas konwertowania domeny federacyjne na zarządzane. Należy pamiętać, że konwertowanie użytkowników nie jest wymagane już i firma Microsoft pracuje na temat aktualizowania dokumentacji i narzędzi, aby to odzwierciedlić.

Aby zaktualizować program Azure AD Connect do najnowszej wersji, zgodnie z dokumentem [instrukcje aktualizacji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Uprawnienia wymagana jest synchronizacja skrótów haseł

Program Azure AD Connect można skonfigurować przy użyciu ustawień ekspresowych lub instalacji niestandardowej. Jeśli użyto opcji instalacji niestandardowej [wymagane uprawnienia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) dla synchronizacji skrótów haseł nie może być w miejscu.

Usługi Azure AD Connect AD DS konto usługi musi mieć następujące uprawnienia, aby można było synchronizowanie skrótów haseł.

* Replikuj zmiany katalogu

* Replikacja katalogu zmienia wszystkie

Teraz jest odpowiedni moment, aby sprawdzić te uprawnienia są stosowane dla wszystkich domen w lesie.

### <a name="plan-migration-method"></a>Planowanie migracji, metoda

Istnieją dwie metody przeprowadzić migrację z uwierzytelniania federacyjnego do synchronizacji skrótów haseł i bezproblemowe logowanie Jednokrotne. Metody, których używasz będzie zależeć od tego, jak usługi AD FS zostały pierwotnie skonfigurowane. 



- **Opcja A: Używanie programu Azure AD Connect**. Jeśli usługi AD FS zostały pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect, zmiana synchronizacji skrótów haseł jako metodę logowania użytkownika muszą być wykonywane za pomocą Kreatora programu Azure AD Connect.   
Korzystając z usługi Azure AD Connect, uruchomieniu polecenia cmdlet Set-MsolDomainAuthentication dla Ciebie automatycznie po zmianie metody logowania użytkownika i dlatego masz żadnej kontroli nad tym go unfederating wszystkie zweryfikowanych domen federacyjnych w dzierżawie usługi Azure AD.

> [!NOTE]
> W tej chwili nie można uniknąć unfederating wszystkich domen w Twojej dzierżawie, po zmianie logowania użytkownika na synchronizację skrótów haseł podczas AAD Connect pierwotnie użyty do skonfigurowania usług AD FS dla Ciebie.  



- **Opcja B: Używanie programu Azure AD Connect przy użyciu programu PowerShell**. Ta metoda może być używana tylko wtedy, gdy usługi AD FS nie zostało skonfigurowane za pomocą usługi Azure AD Connect. Nadal musisz zmienić metodę logowania użytkownika za pomocą Kreatora programu Azure AD Connect, ale podstawowe różnica polega na to, że nie będzie ona automatycznie działać polecenia cmdlet Set-MsolDomainAuthentication dla Ciebie, ponieważ ma ona nie świadomości farmy usług AD FS i dlatego mają pełną kontrolę nad które domeny są konwertowane, w jakiej kolejności.

Aby zrozumieć, jakie metody należy użyć, należy wykonać kroki opisane w poniższej sekcji.

#### <a name="verify-current-user-sign-in-settings"></a>Sprawdź bieżące ustawienia logowania użytkownika

Sprawdź bieżące logowania ustawień użytkownika, logując się do portalu usługi Azure AD [ https://aad.portal.azure.com ](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego.

W sekcji użytkownika logowania Sprawdź włączenie Federacji i bezproblemowego logowania jednokrotnego i uwierzytelniania przekazywanego są wyłączone. Sprawdź także, ten stan synchronizacji haseł, który powinien być wyświetlony jako wyłączony, chyba że to wcześniej został włączony.

![Obraz 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>Zweryfikuj konfigurację usługi Azure AD Connect

   1. Przejdź do serwera usługi Azure AD Connect i uruchom program Azure AD Connect, a następnie wybierz pozycję Konfiguruj. 
   2. Na ekranie dodatkowych zadań wybrać wyświetlanie bieżącej konfiguracji, a następnie wybierz dalej.</br>
   ![Obraz 31](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. Na ekranie Przegląd rozwiązania Zwróć uwagę na stan synchronizacji haseł.</br> 

   Synchronizacja skrótów haseł jest obecnie ustawiony na wyłączony, należy wykonać czynności opisane w tym przewodniku, aby ją włączyć. Jeśli synchronizacja skrótów haseł jest obecnie ustawiony na włączone, można bezpiecznie pominąć sekcji [krok 1 — Włączanie synchronizacji skrótów haseł](#step-1--enable-password-hash-synchronization) w tym przewodniku.
   4. Na ekranie Przegląd rozwiązania przewiń w dół do usługi Active Directory Federation Services (AD FS).</br>
 
   Jeśli widzisz, że konfiguracja usług AD FS znajduje się w tej sekcji, a następnie można bezpiecznie przyjąć usług AD FS zostały pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect i dlatego konwersji użytkownika domeny z federacyjnego zarządzane mogą opierać się za pośrednictwem programu Azure AD Connect "Zmiana użytkownika logowania -w "opcji ten proces opisano szczegółowo w sekcji **opcja A — przełącznik z Federacji na synchronizację skrótów haseł za pomocą usługi Azure AD Connect**.
   5. Jeśli nie widzisz Active Directory Federation Services na liście bieżące ustawienia, a następnie konieczne będzie ręczne przekonwertowanie domen z federacyjnego na zarządzanego za pośrednictwem programu PowerShell, które zostanie szczegółowo opisany w sekcji **opcja B — przełącznik z Federacji na Synchronizacja skrótów haseł przy użyciu programu PowerShell i Azure AD Connect**.

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
Sprawdź wszystkie ustawienia, które może być dostosowane do Federacji projektowania i wdrażania dokumentacji, w szczególności PreferredAuthenticationProtocol SupportsMfa i PromptLoginBehavior.

Więcej informacji na temat co te ustawienia można znaleźć poniżej.

[Monituj usług federacyjnych Active Directory = Obsługa parametrów logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Zestaw MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Jeśli wartość SupportsMfa jest aktualnie ustawiona na wartość "True" następnie oznacza to, że używasz rozwiązania MFA On-Premises iniekcję wezwanie 2nd factor do przepływu uwierzytelniania użytkownika. To nie będzie już działać dla scenariuszy uwierzytelniania usługi Azure AD i zamiast tego trzeba będzie korzystać z usługi Azure MFA (oparte na chmurze) do wykonania tej samej funkcji. Ostrożnie oceny wymagań dotyczących uwierzytelniania Wieloskładnikowego przed skierowaniem ich do przodu i upewnij się, że wiesz, jak korzystać z usługi Azure MFA, skutki licencjonowania i procesu rejestracji użytkowników końcowych przed przekonwertowaniem Twoich domen. Nasz przewodnik wdrażania dla usługi Azure MFA, który zawiera bardziej szczegółowe, można znaleźć w folderze [ https://aka.ms/deploymentplans ](https://aka.ms/deploymentplans).

#### <a name="backup-federation-settings"></a>Ustawienia tworzenia kopii zapasowej Federacji

Mimo że nie będzie zmieniana na inne jednostki uzależnionej strony na farmie usług AD FS w trakcie tego procesu, zalecane jest aby upewnić się, że masz bieżącego prawidłowej kopii zapasowej można przywrócić farmy usług AD FS. Można to zrobić za pomocą bezpłatne Microsoft [usługi AD FS szybkiego przywrócenia narzędzie](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). To narzędzie może służyć do kopii zapasowej i przywracanie usług AD FS, albo do istniejącej farmy lub nowej farmy.

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
| Blokują starsze uwierzytelnianie klientów za pośrednictwem usług AD FS.| Rozważ zastąpienie kontrolki do blokowania uwierzytelnianie starszych klientów aktualnie dostępne w usługach AD FS przy użyciu kombinacji [dostępu warunkowego kontroluje dla starszych uwierzytelniania](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) i [dostępu klienta programu Exchange Online Reguły](http://aka.ms/EXOCAR).|
| Możesz wymagać od użytkowników wykonać uwierzytelnianie wieloskładnikowe dla rozwiązania z serwerem usługi MFA w środowisku lokalnym, podczas uwierzytelniania usług AD FS.| Nie można iniekcję żądanie usługi MFA za pomocą lokalnego rozwiązania MFA do przepływ uwierzytelniania dla domeny zarządzanej, jednak można użyć usługi Azure MFA, w tym celu przyszłości raz domeny jest konwertowany. Jeśli użytkownicy nie korzystają już dzisiaj usługi Azure MFA, to będzie obejmować kroku rejestracji jednorazowe przez użytkownika końcowego, który trzeba będzie przygotowania i komunikują się użytkownikom końcowym.|
| Używasz zasad kontroli dostępu (reguł autoryzacji) już dziś w usługach AD FS do kontrolowania dostępu do usługi Office 365.| Należy wziąć pod uwagę, zastępując je z równoważne usługi Azure AD [zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) i [reguł dostępu klienta Exchange Online](http://aka.ms/EXOCAR).|

### <a name="considerations-for-common-ad-fs-customizations"></a>Zagadnienia dotyczące typowych dostosowania usług AD FS

#### <a name="inside-corporate-network-claim"></a>Oświadczenia wewnątrz sieci firmowej

Oświadczenie InsideCorporateNetwork jest wystawione przez usługi AD FS, jeśli uwierzytelnianie użytkownika znajduje się wewnątrz sieci firmowej. To oświadczenie można następnie przekazywane do usługi Azure AD i umożliwia obejście uwierzytelniania wieloskładnikowego na podstawie lokalizacji sieciowej użytkownika. Zobacz [zaufane adresy IP dla użytkowników federacyjnych](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) informacji na temat sposobu określenia, czy masz to obecnie włączone w usługach AD FS.

Oświadczenie InsideCorporateNetwork nie będzie dostępna już po domen są konwertowane na synchronizację skrótów haseł. [Lokalizacje z nazwą w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) może być używany do zastępowania tej funkcji.

Po skonfigurowaniu lokalizacji o nazwie wszystkie zasady dostępu warunkowego skonfigurowane do dołączania lub wykluczania lokalizacje sieciowe "Wszystkie zaufane lokalizacje" lub "Zaufane adresy IP usługi MFA" muszą zostać zaktualizowane w celu uwzględnienia nowo utworzonej lokalizacji o nazwie.

Zobacz [Active Directory warunkowego lokalizacje dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations) więcej informacji na temat warunek lokalizacji w funkcji dostępu warunkowego.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrydowe usługi Azure AD urządzeń w miejscu pracy

Dołączania urządzenia do usługi Azure AD umożliwia tworzenie reguł dostępu warunkowego, które wymuszają urządzenia spełniające standardy zabezpieczeń i zgodności dostępu i umożliwia użytkownikom zalogować się do urządzenia przy użyciu pracy organizacji lub konta służbowego, a nie osobistego konto. Urządzeń dołączonych do usługi Azure AD hybrydowe umożliwia dołączanie urządzeń przyłączonych do domeny usługi AD do usługi Azure AD. Środowisko federacyjnego mogły zostać skonfigurowane za pomocą tej funkcji.

Aby upewnić się, że hybrydowego przyłączenia kontynuuje pracę dla nowych urządzeń przyłączone do domeny po domen zostały przekonwertowane na synchronizację skrótów haseł, należy skonfigurować program Azure AD Connect do synchronizowania kont komputerów usługi Active Directory dla klientów z systemem Windows 10 do Usługa Azure AD. Dla kont komputerów Windows 7 i Windows 8 Dołącz do hybrydowego użyje bezproblemowe logowanie Jednokrotne do zarejestrowania komputera w usłudze Azure AD, a nie trzeba synchronizować je, podobnie jak w przypadku urządzeń z systemem Windows 10. Jednak trzeba będzie wdrożyć plik workplacejoin.exe zaktualizowane (za pośrednictwem pliku .msi) dla tych klientów niskiego poziomu, dzięki czemu mogą rejestrować się za pomocą bezproblemowego logowania jednokrotnego. [Pobierz plik MSI](https://www.microsoft.com/download/details.aspx?id=53554). 

Aby uzyskać więcej informacji, zobacz [w jaki sposób skonfigurować hybrydowych urządzeń w przyłączonych do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Znakowanie

Twoja organizacja może mieć [dostosowanej strony logowania usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) do wyświetlania informacji bardziej odpowiednie do organizacji. Jeśli tak, należy wziąć pod uwagę co podobne [dostosowania strony logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Podobne dostosowania są dostępne, należy się spodziewać, niektóre zmiany wizualne. Można uwzględnić oczekiwane zmiany w komunikacji dla użytkowników końcowych.

> [!NOTE]
> Logo firmy jest dostępna tylko wtedy, gdy masz zakupionych licencja podstawowa lub Premium dla usługi Azure AD lub licencji usługi Office 365.

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

Po wdrożeniu synchronizacji skrótów haseł i bezproblemowe logowanie Jednokrotne logowanie użytkownika końcowego ulegnie zmianie podczas uzyskiwania dostępu do usługi Office 365 i inne skojarzone zasoby uwierzytelnienia przy użyciu usługi Azure AD. Użytkownicy spoza sieci zobaczą teraz tylko strony logowania usługi Azure AD, a nie nastąpi przekierowanie do strony oparte na formularzach, przedstawiony przez zewnętrzne serwery Proxy aplikacji sieci Web.

Istnieje wiele elementów do planowania strategii komunikacji. Należą do nich:

* Powiadamianie użytkowników o nadchodzących i wydania funkcji za pośrednictwem
  * Wiadomości e-mail i innych wewnętrznych kanałów komunikacji
  * Elementy wizualne, takie jak plakaty
  * Executive łączności na żywo lub innych
* Określanie, kto będzie dostosowywać i który będzie wysyłać komunikatów i kiedy.

## <a name="implementing-your-solution"></a>Wdrażanie rozwiązania

Po zaplanowaniu rozwiązania, można przystąpić do jej wdrożenia. Implementacja obejmuje następujące składniki:

1. Włączanie synchronizacji skrótów haseł

2. Przygotowywanie do bezproblemowego logowania jednokrotnego na

3. Zmiana metody logowania do synchronizacji skrótów haseł i włączanie bezproblemowego logowania jednokrotnego

### <a name="step-1--enable-password-hash-synchronization"></a>Krok 1 — Włączanie synchronizacji skrótów haseł

Pierwszym krokiem do implementacji tego rozwiązania jest włączenie synchronizacji skrótów haseł na Kreatora programu Azure AD Connect. Synchronizacja skrótów haseł jest opcjonalna funkcja, która może zostać włączona w środowiskach, nie wpływając na przebieg uwierzytelniania przy użyciu federacji. W takim przypadku program Azure AD Connect rozpocznie synchronizację skrótów haseł, nie wpływając na użytkowników logowanie się przy użyciu federacji.

Z tego powodu zaleca się wykonanie tego kroku jako zadanie podrzędne przygotowania dobrze, zanim zmienisz to metoda logowania w domenach. Zapewni to wystarczającą ilość czasu, aby sprawdzić synchronizacji skrótów haseł jest poprawnie działać.

Aby włączyć synchronizację skrótów haseł:

   1. Serwerze programu Azure AD Connect Otwórz kreatora, a następnie wybierz pozycję Konfiguruj.
   2. Wybierz pozycję Dostosuj opcje synchronizacji, a następnie wybierz pozycję dalej.
   3. W ekranie łączenie z usługą Azure AD Podaj nazwę użytkownika i hasło administratora globalnego.
   4. W oknie łączenie katalogów ekranu, kliknij przycisk Dalej.
   5. Na ekranie filtrowania domenę i jednostkę Organizacyjną, kliknij przycisk Dalej.
   6. Na ekranie funkcji opcjonalnych wybierz synchronizacji haseł, a następnie wybierz przycisk Dalej.
   ![Obraz 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. Wybierz dalej na wszystkich pozostałych ekranów i Konfiguruj na ostatnim ekranie.
   8. Program Azure AD Connect rozpocznie się synchronizowanie skrótów haseł podczas następnej synchronizacji.

Po włączeniu synchronizacji skrótów haseł dla wszystkich użytkowników w zakresie synchronizacji zostanie ponownie utworzono skrót i zapisywane w usłudze Azure AD program Azure AD Connect tworzy skrót hasła. W zależności od liczby użytkowników ta operacja może zająć od minut do kilku godzin.

Do celów planowania, należy oszacować, że około 20 000 użytkowników mogą być przetwarzane w ciągu 1 godziny.

Aby sprawdzić synchronizacji skrótów haseł poprawnie działa, należy użyć zadania rozwiązywania problemów w Kreatora programu Azure AD Connect.

   1. Otwieranie nowej sesji programu Windows PowerShell na serwerze programu Azure AD Connect przy użyciu opcji Uruchom jako administrator.
   2. Uruchom Set-ExecutionPolicy RemoteSigned lub Set-ExecutionPolicy Unrestricted.
   3. Uruchom Kreatora programu Azure AD Connect.
   4. Przejdź do strony dodatkowych zadań, wybrać Rozwiązywanie problemów i kliknij przycisk Dalej.
   5. Na stronie Rozwiązywanie problemów kliknij przycisk Uruchom, aby uruchomić menu rozwiązywania problemów w programie PowerShell.
   6. W menu głównym wybierz synchronizację skrótów haseł rozwiązywania problemów.
   7. W podmenu wybierz haseł, synchronizacji skrótów nie działa w ogóle.

Jeśli znajdziesz problemy, skorzystaj z informacji w [w tym artykule](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization) rozwiązywać problemy.

### <a name="step-2--prepare-for-seamless-sso"></a>Krok 2 — przygotowanie bezproblemowego logowania jednokrotnego

Na urządzeniach za pomocą bezproblemowego logowania jednokrotnego należy dodać adres URL usług Azure AD do ustawienia strefy Intranet użytkowników za pomocą zasad grupy w usłudze Active Directory.

Domyślnie przeglądarka automatycznie oblicza poprawnej strefy Internet lub Intranet, z określonym adresem URL. Na przykład "http://contoso/"mapuje do strefy Intranet, podczas gdy"http://intranet.contoso.com/" mapuje do strefy Internet (ponieważ jest to adres URL zawiera kropkę). Przeglądarki będzie wysyłał bilety protokołu Kerberos do punktu końcowego w chmurze, takich jak usługa Azure AD adres URL, chyba że dodasz do strefy Intranet w przeglądarce adres URL.

Postępuj zgodnie z [kroki, aby wdrożyć](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) zmiany wymagane w celu urządzeń.

> [!IMPORTANT]
> Wprowadzenie tej zmiany nie wpłynie sposób użytkownicy logują się do usługi Azure AD. Jest ważne, że ta konfiguracja jest stosowany do wszystkich urządzeń przed rozpoczęciem pracy z kroku 3. Ponadto należy pamiętać, że użytkownikom logowanie się na urządzeniach, które nie odebrały tej konfiguracji należy po prostu wprowadź nazwę użytkownika i hasło do logowania do usługi Azure AD.

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>Krok 3 — Zmień metodę logowania do wersji i Włącz bezproblemowe logowanie Jednokrotne

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>Opcja A — przełącznik z Federacji wersji przy użyciu usługi Azure AD Connect

Użyj tej metody, gdy usługi AD FS początkowo został skonfigurowany za pomocą usługi Azure AD Connect. Nie można użyć tej metody, jeśli usługi AD FS nie zostało pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect. Pierwszy **zmienić metodę użytkownika logowania**

   1. Serwerze programu Azure AD Connect należy otworzyć kreatora.
   2. Wybierz pozycję Zaloguj zmiany użytkownika, a następnie wybierz pozycję dalej.
   ![Obraz 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. W **nawiązywanie połączenia z usługi Azure AD** ekranu Podaj nazwę użytkownika i hasło **administratora globalnego**.
   4. W **logowania użytkownika** ekranu, zmień przycisk radiowy z federacji z usługami AD FS do przekazania synchronizacji skrótów i upewnij się, że pole wyboru nie Konwertuj kont użytkowników, jak to jest krok przestarzały i zostanie usunięte z przyszłych wersji z usługi AAD Connect. Również wybrać Włącz logowanie jednokrotne, a następnie wybierz **dalej**.
   ![Obraz 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > Począwszy od usługi Azure AD Connect w wersji 1.1.880.0 bezproblemowe pojedynczego logowania pole wyboru jest domyślnie włączona.
   
   > [!IMPORTANT]
   > Możesz bezpiecznie zignorować ostrzeżenia wskazująca, że konwersji użytkownika i synchronizacji skrótów haseł pełne są kroki wymagane do konwertowania z Federacji na uwierzytelnianie w chmurze. Należy pamiętać, że te kroki nie są już wymagane, w przyszłych wersjach programu Azure AD Connect nie będzie istniała opcja do przekonwertowania użytkowników. Jeśli nadal widzisz te ostrzeżenia, sprawdź, że używasz najnowszej wersji programu Azure AD Connect i korzystają z najnowszej wersji tego przewodnika. Aby uzyskać więcej informacji, zobacz [sekcji aktualizacji programu Azure AD Connect](#_Update_Azure_AD).
   
   5. Włącz logowanie jednokrotne na ekranie wprowadź poświadczenia konta administratora domeny, a następnie wybierz przycisk Dalej.
   ![Obraz 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > Poświadczenia administratora domeny są wymagane do włączenie bezproblemowego logowania jednokrotnego, ponieważ proces wykonuje następujące czynności, które wymagają tych podwyższonym poziomem uprawnień. Poświadczenia administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Są one używane tylko w celu włączenia tej funkcji i następnie zostaje odrzucone po pomyślnym ukończeniu
   >  * Konto komputera o nazwie AZUREADSSOACC (który reprezentuje usługę Azure AD) jest tworzony w sieci lokalnej usługi Active Directory (AD).
   >  * Klucz odszyfrowywania protokołu Kerberos konta komputera jest udostępniony w bezpieczny sposób za pomocą usługi Azure AD.
   >  * Ponadto dwa Kerberos głównych nazw usług (SPN) są tworzone do reprezentowania dwa adresy URL, które są używane podczas logowania w usłudze Azure AD.
   >  * Poświadczenia administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Są one używane tylko w celu włączenia tej funkcji i następnie zostaje odrzucone po pomyślnym ukończeniu
   
   6. W oknie gotowy do ekranu konfiguracja, upewnij się, "Procesu rozpoczęcia synchronizacji po ukończeniu konfiguracji" pole wyboru jest zaznaczone. Następnie wybierz pozycję Konfiguruj.
   ![Obraz 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > W tym momencie wszystkich domen federacyjnych zostanie zmieniony na uwierzytelnianie zarządzane, które będą teraz korzystać z synchronizacji skrótów haseł jako metodę uwierzytelniania.
       
   7. Otwórz portal usługi Azure AD, wybierz usługę Azure Active Directory, a następnie wybierz program Azure AD Connect.
   8. Sprawdź, czy federacji jest wyłączona podczas bezproblemowe logowanie jednokrotne, a synchronizacja haseł są włączone.  
  ![Obraz 37](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. Przejdź do [testowania i następne kroki](#testing-and-next-steps).
   
   > [!IMPORTANT]
   > Pomiń sekcji opcja B — przełącznik z Federacji na synchronizację skrótów haseł przy użyciu programu PowerShell i Azure AD Connect, tak jak zaprezentowano w tej sekcji nie mają zastosowania.  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>Opcja B — przełącznik z Federacji wersji przy użyciu programu PowerShell i Azure AD Connect

Użyj tej opcji, gdy federacyjnym nie zostało wstępnie skonfigurowane przy użyciu usługi Azure AD Connect.

W ramach tego procesu możesz Włącz bezproblemowe logowanie Jednokrotne i Przełącz domen z Sfederowane do zarządzanego.

   1. Serwerze programu Azure AD Connect należy otworzyć kreatora.
   2. Wybierz pozycję Zaloguj zmiany użytkownika, a następnie wybierz pozycję dalej. 
   3. W ekranie łączenie z usługą Azure AD Podaj nazwę użytkownika i hasło administratora globalnego.
   4. Na ekranie logowania użytkownika Zmień przycisk radiowy z nie jest skonfigurowana do synchronizacji skrótów haseł, zaznacz opcję Włącz pojedynczego logowania jednokrotnego, a następnie wybierz pozycję dalej.
   
   Przed wprowadzeniem zmiany: ![Obraz 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   Po zmianie:  
   ![Obraz 22](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > Począwszy od usługi Azure AD Connect w wersji 1.1.880.0 bezproblemowe pojedynczego logowania pole wyboru jest domyślnie włączona.
   
   5. Włącz logowanie jednokrotne na ekranie wprowadź poświadczenia konta administratora domeny, a następnie wybierz przycisk Dalej.
   
   > [!NOTE]
   > Poświadczenia administratora domeny są wymagane do włączenie bezproblemowego logowania jednokrotnego, ponieważ proces wykonuje następujące czynności, które wymagają tych podwyższonym poziomem uprawnień. Poświadczenia administratora domeny nie są przechowywane w programie Azure AD Connect lub w usłudze Azure AD. Są one używane tylko w celu włączenia tej funkcji i następnie zostaje odrzucone po pomyślnym ukończeniu.
   > * Konto komputera o nazwie AZUREADSSOACC (który reprezentuje usługę Azure AD) jest tworzony w sieci lokalnej usługi Active Directory (AD).
   > * Klucz odszyfrowywania protokołu Kerberos konta komputera jest udostępniony w bezpieczny sposób za pomocą usługi Azure AD.
   > * Ponadto dwa Kerberos głównych nazw usług (SPN) są tworzone do reprezentowania dwa adresy URL, które są używane podczas logowania w usłudze Azure AD.
   
   6. W oknie gotowy do ekranu konfiguracja, upewnij się, "Procesu rozpoczęcia synchronizacji po ukończeniu konfiguracji" pole wyboru jest zaznaczone. Następnie wybierz pozycję Konfiguruj.
   ![Obraz 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   Wybieranie konfigurowania bezproblemowe logowanie Jednokrotne zostaną skonfigurowane zgodnie z kroku wersje zapoznawcze. Nie można zmodyfikować konfiguracji synchronizacji skrótów haseł zostało wcześniej włączone.
   
   > [!IMPORTANT]
   > Jak użytkownicy logują się na tym etapie zostaną wprowadzone żadne zmiany.  
   
   7. Sprawdź, czy federacji w dalszym ciągu być włączone i teraz bezproblemowe logowanie jednokrotne jest włączona na portalu usługi Azure AD.
   ![Obraz 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Konwertuj domen z federacyjnego na zarządzanego

W tym momencie federacyjnej jest nadal włączony i działa dla Twoich domen. Aby kontynuować wdrożenie, każdej domeny, musi zostać skonwertowany z Sfederowane do zarządzanego, aby wymusić uwierzytelnianie użytkowników za pośrednictwem synchronizacji skrótów haseł.

> [!IMPORTANT]
> Nie wszystkie domeny wymagają można przekonwertować na tym samym czasie, możesz wybrać na początek z domeny testu dla dzierżawy produkcyjnej lub domeny o najmniejszej liczby użytkowników.

Konwersja odbywa się przy użyciu modułu Azure AD PowerShell.

   1. Otwórz program PowerShell i zaloguj się do usługi Azure AD przy użyciu konta administratora globalnego.  
   2. Aby przekonwertować pierwszej domeny, uruchom następujące polecenie:  
   
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   
   3. Otwórz portal usługi Azure AD, wybierz usługę Azure Active Directory, a następnie wybierz program Azure AD Connect.
   4. Sprawdź, czy domena została przekonwertowana na zarządzane, uruchamiając następujące polecenie:
   
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>Testowanie i następne kroki

### <a name="test-authentication-with-phs"></a>Testuj uwierzytelnienie za pomocą wersji

Korzystając z Twojej dzierżawy został Federacji, użytkownicy zostały przekierowaniu ze strony logowania usługi Azure AD do środowiska usług AD FS. Teraz, gdy dzierżawy jest skonfigurowany do używania synchronizacji skrótów haseł zamiast Federacji, użytkownicy nie będą Pobierz przekierowane do usług AD FS i zamiast tego będzie zalogować się bezpośrednio za pomocą strony logowania usługi Azure AD.

Otwórz program Internet Explorer w trybie InPrivate w celu uniknięcia bezproblemowe logowanie Jednokrotne logowanie automatyczne, a następnie przejdź do strony logowania usługi Office 365 ([http://portal.office.com](http://portal.office.com/)). Wpisz nazwę UPN użytkownika, a następnie kliknij przycisk Dalej. Upewnij się, że wpisz nazwę UPN użytkownika hybrydowych, które zostało synchronizowane z lokalnej usługi Active Directory i który został wcześniej federacyjnych. Użytkownik zostanie wyświetlony ekran do wpisywania nazwy użytkownika i hasła.

![Obraz 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![Obraz 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

Po wpisaniu hasła, należy uzyskać przekierowanie do portalu usługi Office 365.

![Obraz 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>Testowanie bezproblemowego logowania jednokrotnego

Zaloguj się na komputerze dołączonym do domeny, która jest połączona z siecią firmową. Otwórz program Internet Explorer i przejdź do jednej z następujących adresów URL:  
  
[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (Zamień Contoso domenę).

Użytkownik chwilę nastąpi przekierowanie do strony logowania usługi Azure AD i zostanie wyświetlony komunikat "Próby zarejestrowania Cię" i nie powinien wyświetlony monit o podanie nazwy użytkownika lub hasło.

![Obraz 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

Następnie użytkownik będzie pobrać przekierowanie i pomyślnie zalogował się do panelu dostępu:

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

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Z zespołem pomocy technicznej należy dowiedzieć się, jak rozwiązywać problemy z uwierzytelnianiem, powstałe podczas lub po zmianie z Federacji zarządzane. Korzystając z poniższej dokumentacji rozwiązywania problemów, aby pomóc zespołowi pomocy technicznej zapoznania się z wspólne kroki rozwiązywania problemów i odpowiednie działania, które mogą pomóc wyizolować i rozwiązać problem.

[Rozwiązywanie problemów z synchronizacją skrótów haseł usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Rozwiązywanie problemów z usługi Azure Active Directory bezproblemowego logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>Przeskocz do odszyfrowywania bezproblemowego logowania jednokrotnego protokołu Kerberos.

Ważne jest, aby często przerzucić klucz odszyfrowywania protokołu Kerberos konta komputera AZUREADSSOACC (który reprezentuje usługę Azure AD) utworzone w lokalnym lasem usługi AD. Zdecydowanie zalecamy przedłużenie klucz odszyfrowywania protokołu Kerberos co najmniej co 30 dni wyrównać jak członkowie domeny usługi Active Directory Prześlij zmiany hasła. Ponieważ nie jest skojarzona urządzenie dołączone do obiektu konta komputera AZUREADSSOACC przerzucania musi być wykonywane ręcznie.

Wykonaj następujące kroki na serwerze lokalnym, na którym uruchomiony jest program Azure AD Connect, aby zainicjować przerzucania klucza odszyfrowywania protokołu Kerberos.

[Jak je czy przerzucić klucz odszyfrowywania protokołu Kerberos konta komputera AZUREADSSOACC](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="next-steps"></a>Kolejne kroki

- [Zagadnienia dotyczące projektowania przy korzystaniu z programu Azure AD Connect](plan-connect-design-concepts.md)
- [Wybierz odpowiednie uwierzytelnianie](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Obsługiwane topologie](plan-connect-design-concepts.md)
