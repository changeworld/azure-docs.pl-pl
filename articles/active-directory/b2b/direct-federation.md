---
title: Bezpośrednia federacja z dostawcą tożsamości dla B2B — Azure AD
description: Bezpośrednio sfeuj z dostawcą tożsamości SAML lub WS-Fed, aby goście mogli logować się do aplikacji usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050876"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Bezpośrednia federacja z usługami AD FS i zewnętrznymi dostawcami dla użytkowników-gości (wersja zapoznawcza)
|     |
| --- |
| Bezpośrednia federacja jest publiczną funkcją podglądu usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

W tym artykule opisano sposób konfigurowania bezpośredniej federacji z inną organizacją do współpracy B2B. Można skonfigurować bezpośrednią federację z dowolną organizacją, której dostawca tożsamości (IdP) obsługuje protokół SAML 2.0 lub WS-Fed.
Po skonfigurowaniu bezpośredniej federacji przy użyciu dostawcy tożsamości partnera nowi użytkownicy-goście z tej domeny mogą używać własnego konta organizacyjnego zarządzanego przez IdP, aby zalogować się do dzierżawy usługi Azure AD i rozpocząć współpracę z Tobą. Nie ma potrzeby, aby użytkownik-gość utworzył oddzielne konto usługi Azure AD.
> [!NOTE]
> Bezpośredni użytkownicy-goście-goście muszą zalogować się przy użyciu `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>`łącza zawierającego kontekst dzierżawy (na przykład lub , lub w przypadku zweryfikowanej domeny). `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com` Bezpośrednie łącza do aplikacji i zasobów również działają tak długo, jak zawierają kontekst dzierżawy. Bezpośredni użytkownicy federacyjne nie mogą obecnie zalogować się przy użyciu typowych punktów końcowych, które nie mają kontekstu dzierżawy. Na przykład `https://myapps.microsoft.com`użycie `https://portal.azure.com`, `https://teams.microsoft.com` lub spowoduje błąd.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Kiedy użytkownik-gość jest uwierzytelniony przy użyciu bezpośredniej federacji?
Po skonfigurowaniu bezpośredniej federacji w organizacji, każdy nowy zaproszony użytkownik zostanie uwierzytelniony przy użyciu bezpośredniej federacji. Należy pamiętać, że konfigurowanie federacji bezpośredniej nie zmienia metody uwierzytelniania dla użytkowników-gości, którzy już zrealizowali zaproszenie od Ciebie. Oto kilka przykładów:
 - Jeśli użytkownicy-goście już zrealizowali zaproszenia od Ciebie, a następnie skonfigurujesz bezpośrednią federację w swojej organizacji, ci użytkownicy-goście będą nadal używać tej samej metody uwierzytelniania, której używali przed skonfigurowaniem bezpośredniej federacji.
 - Jeśli skonfigurujesz bezpośrednią federację z organizacją partnerską i zaprosisz użytkowników-gości, a następnie organizacja partnerska przejdzie później do usługi Azure AD, użytkownicy-goście, którzy już zrealizowali zaproszenia, będą nadal korzystać z bezpośredniej federacji, o ile zasady federacyjne w dzierżawie istnieje.
 - Jeśli usuniesz bezpośrednią federację w organizacji partnerskiej, użytkownicy-goście korzystający obecnie z federacji bezpośredniej nie będą mogli się zalogować.

W każdym z tych scenariuszy można zaktualizować metodę uwierzytelniania użytkownika gościa, usuwając konto użytkownika gościa z katalogu i ponownie je korzystając.

Federacja bezpośrednia jest powiązana z obszarami nazw domen, takimi jak contoso.com i fabrikam.com. Podczas ustanawiania bezpośredniej konfiguracji federacji z usługą AD FS lub z usługodawcą domeny innej firmy organizacje kojarzą co najmniej jedną przestrzeń nazw domen z tymi adresami ip. 

## <a name="end-user-experience"></a>Środowisko użytkownika końcowego 
Dzięki bezpośredniej federacji użytkownicy-goście logują się do dzierżawy usługi Azure AD przy użyciu własnego konta organizacji. Gdy uzyskują dostęp do zasobów udostępnionych i są monitowani o zalogowanie, bezpośredni użytkownicy federacji są przekierowywani do ich IdP. Po pomyślnym zalogowaniu są one zwracane do usługi Azure AD, aby uzyskać dostęp do zasobów. Tokeny odświeżania bezpośrednich użytkowników federacji są ważne przez 12 godzin, [czyli domyślną długość tokenu odświeżania przez](../develop/active-directory-configurable-token-lifetimes.md#exceptions) usługę Azure AD. Jeśli federowany protokół IdP ma włączone logowanie, użytkownik będzie miał doświadczenie logowania sytego i nie zobaczy żadnego monitu logowania po początkowym uwierzytelnieniu.

## <a name="limitations"></a>Ograniczenia

### <a name="dns-verified-domains-in-azure-ad"></a>Domeny zweryfikowane przez dns w usłudze Azure AD
Domena, z którą chcesz się skonserwować, ***nie*** może być zweryfikowana przez system DNS w usłudze Azure AD. Możesz skonfigurować bezpośrednią federację z niezarządzanymi (zweryfikowanymi pocztą e-mail lub "wirusową") dzierżawcami usługi Azure AD, ponieważ nie są one zweryfikowane przez system DNS.

### <a name="authentication-url"></a>Adres URL uwierzytelniania
Bezpośrednia federacja jest dozwolona tylko dla zasad, w których domena adresu URL uwierzytelniania jest zgodna z domeną docelową lub gdy adres URL uwierzytelniania jest jednym z tych dozwolonych dostawców tożsamości (ta lista może ulec zmianie):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Na przykład podczas konfigurowania **fabrikam.com**bezpośredniej federacji dla `https://fabrikam.com/adfs` fabrikam.com adres URL uwierzytelniania przejdzie weryfikację. Host w tej samej domenie `https://sts.fabrikam.com/adfs`również przejdzie, na przykład . Jednak adres URL `https://fabrikamconglomerate.com/adfs` `https://fabrikam.com.uk/adfs` uwierzytelniania lub dla tej samej domeny nie przejdzie.

### <a name="signing-certificate-renewal"></a>Odnawianie certyfikatów podpisywania
Jeśli określisz adres URL metadanych w ustawieniach dostawcy tożsamości, usługa Azure AD automatycznie odnowi certyfikat podpisywania po jego wygaśnięciu. Jeśli jednak certyfikat zostanie obrócony z jakiegokolwiek powodu przed upływem czasu lub jeśli nie podasz adresu URL metadanych, usługa Azure AD nie będzie mogła go odnowić. W takim przypadku należy ręcznie zaktualizować certyfikat podpisywania.

### <a name="limit-on-federation-relationships"></a>Ograniczenie relacji federacyjnej
Obecnie obsługiwanych jest maksymalnie 1000 relacji federacyjnej. Limit ten obejmuje zarówno [federacje wewnętrzne,](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) jak i federacje bezpośrednie.

### <a name="limit-on-multiple-domains"></a>Ograniczanie w wielu domenach
Obecnie nie obsługujemy bezpośredniej federacji z wieloma domenami z tej samej dzierżawy.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Czy mogę skonfigurować bezpośrednią federację z domeną, dla której istnieje dzierżawa niezarządzana (zweryfikowana pocztą e-mail)? 
Tak. Jeśli domena nie została zweryfikowana, a dzierżawca nie została poddana [przejęciu administratora,](../users-groups-roles/domains-admin-takeover.md)można skonfigurować bezpośrednią federację w tej domenie. Niezarządzane lub zweryfikowane pocztą e-mail dzierżawy są tworzone, gdy użytkownik realizuje zaproszenie B2B lub wykonuje samoobsługowe rejestracje dla usługi Azure AD przy użyciu domeny, która obecnie nie istnieje. Można skonfigurować bezpośrednią federację z tymi domenami. Jeśli spróbujesz skonfigurować bezpośrednią federację z domeną zweryfikowaną przez DNS w witrynie Azure portal lub za pośrednictwem programu PowerShell, zostanie wyświetlony błąd.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Jeśli bezpośrednie uwierzytelnianie federacyjnym i e-mail jednorazowym kodem dostępu jest włączone, która metoda ma pierwszeństwo?
Po ustanowieniu federacji bezpośredniej z organizacją partnerską ma pierwszeństwo przed jednorazowym uwierzytelnianiem kodu dostępu poczty e-mail dla nowych użytkowników-gości z tej organizacji. Jeśli użytkownik-gość zrealizował zaproszenie przy użyciu jednorazowego uwierzytelniania kodem dostępu przed skonfigurowaniem bezpośredniej federacji, będzie nadal używać jednorazowego uwierzytelniania kodem dostępu. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Czy bezpośrednia federacja rozwiązuje problemy z logowaniem z powodu częściowo zsynchronizowanego najmu?
Nie, funkcja [jednorazowego kodu dostępu poczty e-mail](one-time-passcode.md) powinna być używana w tym scenariuszu. "Częściowo zsynchronizowany najem" odnosi się do partnerskiej dzierżawy usługi Azure AD, w której lokalne tożsamości użytkowników nie są w pełni synchronizowane z chmurą. Gość, którego tożsamość jeszcze nie istnieje w chmurze, ale który próbuje zrealizować zaproszenie B2B, nie będzie mógł się zalogować. Funkcja jednorazowego kodu dostępu umożliwiłaby temu gościowi zalogowanie się. Funkcja bezpośredniej federacji dotyczy scenariuszy, w których gość ma własne konto organizacyjne zarządzane przez IdP, ale organizacja nie ma żadnej obecności usługi Azure AD.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Krok 1: Konfigurowanie dostawcy tożsamości organizacji partnerskiej
Po pierwsze organizacja partnerska musi skonfigurować swojego dostawcę tożsamości przy pomocy wymaganych oświadczeń i relacji zaufania jednostki uzależniającej. 

> [!NOTE]
> Aby zilustrować sposób konfigurowania dostawcy tożsamości dla federacji bezpośredniej, jako przykład użyjemy usług federacyjnych Active Directory (AD FS). Zobacz artykuł [Konfigurowanie bezpośredniej federacji z usługami AD FS](direct-federation-adfs.md), w którym przedstawiono przykłady konfigurowania usług AD FS jako dostawcy tożsamości SAML 2.0 lub WS-Fed w ramach przygotowań do bezpośredniej federacji.

### <a name="saml-20-configuration"></a>Konfiguracja SAML 2.0

Usługi Azure AD B2B można skonfigurować do ujednolici z dostawcami tożsamości, którzy używają protokołu SAML z określonymi wymaganiami wymienionymi poniżej. Aby uzyskać więcej informacji na temat konfigurowania zaufania między dostawcą tożsamości SAML a usługą Azure AD, zobacz [Używanie dostawcy tożsamości SAML 2.0 (IdP) do logowania jednokrotnego.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)  

> [!NOTE]
> Domena docelowa dla bezpośredniej federacji nie może być zweryfikowana przez system DNS w usłudze Azure AD. Domena adresu URL uwierzytelniania musi być zgodna z domeną docelową lub musi być domeną dostawcy dozwolonej tożsamości. Szczegółowe informacje można znaleźć w sekcji [Ograniczenia.](#limitations) 

#### <a name="required-saml-20-attributes-and-claims"></a>Wymagane atrybuty i oświadczenia SAML 2.0
W poniższych tabelach przedstawiono wymagania dotyczące określonych atrybutów i oświadczeń, które muszą być skonfigurowane u dostawcy tożsamości innej firmy. Aby skonfigurować federację bezpośrednią, należy otrzymać następujące atrybuty w odpowiedzi SAML 2.0 od dostawcy tożsamości. Atrybuty te można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego online lub wprowadzając je ręcznie.

Wymagane atrybuty odpowiedzi SAML 2.0 z IdP:

|Atrybut  |Wartość  |
|---------|---------|
|TwierdzenieConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator wystawcy identyfikatora partnera, na przykład`http://www.example.com/exk10l6w90DHM0yi...`         |


Wymagane oświadczenia dotyczące tokenu SAML 2.0 wystawionego przez IdP:

|Atrybut  |Wartość  |
|---------|---------|
|NameID Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Konfiguracja WS-Fed 
Usługi Azure AD B2B można skonfigurować do ujednolici z dostawcami tożsamości, którzy używają protokołu WS-Fed z pewnymi określonymi wymaganiami, jak podano poniżej. Obecnie dwóch dostawców WS-Fed zostały przetestowane pod kątem zgodności z usługą Azure AD obejmują usługi AD FS i Shibboleth. Aby uzyskać więcej informacji na temat ustanawiania zaufania jednostki uzależnionej między dostawcą zgodnym z usługą Azure AD, zobacz "Papier integracji STS przy użyciu protokołów WS" dostępny w [Dokumentach zgodności dostawcy tożsamości usługi Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Domena docelowa dla bezpośredniej federacji nie może być zweryfikowana przez system DNS w usłudze Azure AD. Domena adresu URL uwierzytelniania musi być zgodna z domeną docelową lub domeną dostawcy dozwolonej tożsamości. Szczegółowe informacje można znaleźć w sekcji [Ograniczenia.](#limitations) 

#### <a name="required-ws-fed-attributes-and-claims"></a>Wymagane atrybuty i roszczenia WS-Fed

W poniższych tabelach przedstawiono wymagania dotyczące określonych atrybutów i oświadczeń, które muszą być skonfigurowane u zewnętrznego dostawcy tożsamości WS-Fed. Aby skonfigurować federację bezpośrednią, w wiadomości WS-Fed od dostawcy tożsamości należy odbierać następujące atrybuty. Atrybuty te można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego online lub wprowadzając je ręcznie.

Wymagane atrybuty w wiadomości WS-Fed z IdP:
 
|Atrybut  |Wartość  |
|---------|---------|
|Pasywny PunktEndor     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator wystawcy identyfikatora partnera, na przykład`http://www.example.com/exk10l6w90DHM0yi...`         |

Wymagane roszczenia dotyczące tokenu WS-Fed wystawionego przez IdP:

|Atrybut  |Wartość  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Krok 2: Konfigurowanie bezpośredniej federacji w usłudze Azure AD 
Następnie skonfigurujesz federację z dostawcą tożsamości skonfigurowanym w kroku 1 w usłudze Azure AD. Można użyć portalu usługi Azure AD lub programu PowerShell. Może upłynąć 5-10 minut, zanim zasady bezpośredniej federacji wejdą w życie. W tym czasie nie próbuj realizować zaproszenia dla domeny bezpośredniej federacji. Wymagane są następujące atrybuty:
- Identyfikator wystawcy IdP partnera
- Punkt końcowy uwierzytelniania pasywnego partnera IdP (obsługiwane jest tylko https)
- Certyfikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Aby skonfigurować bezpośrednią federację w portalu usługi Azure AD

1. Przejdź do [witryny Azure portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **pozycję Relacje organizacyjne**.
3. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję Nowy identyfikator **SAML/WS-Fed**.

    ![Zrzut ekranu przedstawiający przycisk dodawania nowego identyfikatora SAML lub WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Na stronie **Nowy identyfikator SAML/WS-Fed** w obszarze **Protokół dostawcy tożsamości**wybierz pozycję **SAML** lub **WS-FED**.

    ![Zrzut ekranu przedstawiający przycisk analizy na stronie SAML lub WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Wprowadź nazwę domeny organizacji partnerskiej, która będzie nazwą domeny docelowej dla federacji bezpośredniej
6. Możesz przekazać plik metadanych, aby wypełnić szczegóły metadanych. Jeśli wybierzesz opcję ręcznego wprowadzania metadanych, wprowadź następujące informacje:
   - Nazwa domeny idP partnera
   - Identyfikator jednostki identyfikatora partnera
   - Pasywny punkt końcowy żądania idP partnera
   - Certyfikat
   > [!NOTE]
   > Adres URL metadanych jest opcjonalny, jednak zdecydowanie go zalecamy. Jeśli podasz adres URL metadanych, usługa Azure AD może automatycznie odnowić certyfikat podpisywania po jego wygaśnięciu. Jeśli certyfikat zostanie obrócony z jakiegokolwiek powodu przed upływem czasu lub jeśli nie podasz adresu URL metadanych, usługa Azure AD nie będzie mogła go odnowić. W takim przypadku należy ręcznie zaktualizować certyfikat podpisywania.

7. Wybierz **pozycję Zapisz**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Aby skonfigurować bezpośrednią federację w usłudze Azure AD przy użyciu programu PowerShell

1. Zainstaluj najnowszą wersję modułu programu Azure AD PowerShell for Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview) (Jeśli potrzebujesz szczegółowych kroków, szybki start dodawania użytkownika-gościa zawiera sekcję [Instalowanie najnowszego modułu AzureADPreview.)](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module) 
2. Uruchom następujące polecenie: 
   ```powershell
   Connect-AzureAD
   ```
1. W wierszu logowania zaloguj się za pomocą zarządzanego konta administratora globalnego. 
2. Uruchom następujące polecenia, zastępując wartości z pliku metadanych federacji. W przypadku serwera usług AD FS Server i Okta plik federacyjny to federationmetadata.xml, na przykład: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Krok 3: Testowanie bezpośredniej federacji w usłudze Azure AD
Teraz przetestuj bezpośrednią konfigurację federacji, zapraszając nowego użytkownika gościa B2B. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie użytkowników współpracy usługi Azure AD B2B w witrynie Azure portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Jak edytować bezpośrednią relację federacjową?

1. Przejdź do [witryny Azure portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **pozycję Relacje organizacyjne**.
3. Wybierz **dostawców tożsamości**
4. W obszarze **DOSTAWCY tożsamości SAML/WS-Fed**wybierz dostawcę tożsamości.
5. W okienku szczegółów dostawcy tożsamości zaktualizuj wartości.
6. Wybierz **pozycję Zapisz**.


## <a name="how-do-i-remove-direct-federation"></a>Jak usunąć federację bezpośrednią?
Możesz usunąć bezpośrednią konfigurację federacji. Jeśli to zrobisz, bezpośredni użytkownicy-goście-goście federacji, którzy już zrealizowali zaproszenia, nie będą mogli się zalogować. Ale można dać im dostęp do zasobów ponownie, usuwając je z katalogu i ponownie je. Aby usunąć bezpośrednią federację z dostawcą tożsamości w portalu usługi Azure AD:

1. Przejdź do [witryny Azure portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **pozycję Relacje organizacyjne**.
3. Wybierz **dostawców tożsamości**.
4. Wybierz dostawcę tożsamości, a następnie wybierz pozycję **Usuń**. 
5. Wybierz **pozycję Tak,** aby potwierdzić usunięcie. 

Aby usunąć bezpośrednią federację z dostawcą tożsamości przy użyciu programu PowerShell:
1. Zainstaluj najnowszą wersję modułu programu Azure AD PowerShell for Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Uruchom następujące polecenie: 
   ```powershell
   Connect-AzureAD
   ```
3. W wierszu logowania zaloguj się za pomocą zarządzanego konta administratora globalnego. 
4. Wprowadź następujące polecenie:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
