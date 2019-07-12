---
title: Konfigurowanie bezpośredniego Federacji przy użyciu dostawcy tożsamości dla modelu B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Bezpośrednio utworzyć Federację za pomocą dostawcy tożsamości SAML lub WS-Fed, dzięki czemu goście zalogować się do aplikacji w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bc3c1325e8379082134e2cbec1586f7d338ee61
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797937"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Bezpośrednie federacji z usług AD FS i dostawców innych firm dla użytkowników-gości (wersja zapoznawcza)
|     |
| --- |
| Bezpośrednie federacyjnej jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

W tym artykule opisano sposób konfigurowania bezpośrednich federacji z innej organizacji, współpracy B2B. Można skonfigurować bezpośrednie federacji z każdej organizacji, w których dostawca tożsamości (IdP) obsługuje protokół SAML 2.0 i WS-Fed.
Po skonfigurowaniu bezpośredniego Federacji przy użyciu dostawcy tożsamości partnerów nowych użytkowników-gości z tej domeny umożliwia własne zarządzane przez dostawcę tożsamości konta organizacji Zaloguj się do dzierżawy usługi Azure AD i rozpocząć współpracę z Tobą. Nie ma potrzeby użytkownika gościa, Utwórz osobne konta usługi Azure AD.
> [!NOTE]
> Bezpośrednie użytkowników-gości federacyjnej musi Zaloguj się przy użyciu łącza, które obejmuje kontekstu dzierżawy (na przykład `https://myapps.microsoft.com/?tenantid=<tenant id>` lub `https://portal.azure.com/<tenant id>`, lub w przypadku zweryfikowanej domeny `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). Bezpośrednie linki do aplikacji i zasobów również działać tak długo, jak należą do kontekstu dzierżawy. Użytkowników federacyjnych bezpośrednich są obecnie nie można zalogować się przy użyciu typowych punktów końcowych, które mają nie kontekstu dzierżawy. Na przykład za pomocą `https://myapps.microsoft.com`, `https://portal.azure.com`, lub `https://teams.microsoft.com` spowoduje wystąpienie błędu.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Po uwierzytelnieniu użytkownika-gościa z Federacją bezpośrednie
Po skonfigurowaniu bezpośredniego federacji z organizacji żadnych nowych użytkowników-gości, zapraszane osoby będą uwierzytelniane przy użyciu bezpośredniego federacji. Należy pamiętać, że konfigurowania federacyjnych bezpośrednie nie zmienić metodę uwierzytelniania dla użytkowników-gości, którzy już wykorzystana zaproszenie ze strony użytkownika. Oto kilka przykładów:
 - Jeśli użytkownicy-goście już wykorzystana zaproszeń z firmy która następnie skonfigurować bezpośrednie federacji w swojej organizacji, tych użytkowników z gościa będą w dalszym używać tej samej metody uwierzytelniania, którego używały przed rozpoczęciem konfigurowania Federacji bezpośrednich.
 - Jeśli Konfigurowanie bezpośrednich federacji z organizacji partnerskiej i zapraszać użytkowników-gości, a następnie organizacji partnerskiej później przechodzi do usługi Azure AD, użytkowników-gości, którzy już wykorzystana zaproszenia będą w dalszym ciągu używać bezpośrednich Federacji, tak długo, jak bezpośrednio istnieje zasada federacyjnej w Twojej dzierżawie.
 - Jeśli usuniesz bezpośrednie federacji z organizacji partnerskiej, żadnych użytkowników-gości obecnie przy użyciu Federacji bezpośrednie będzie nie można się zalogować.

W dowolnym z tych scenariuszy należy zaktualizować metodę uwierzytelniania użytkownika-gościa przez usunięcie konta gościa z katalogu i reinviting je.

Bezpośrednie federacyjnej jest powiązany do przestrzeni nazw domen, np. contoso.com i fabrikam.com. Podczas ustanawiania konfiguracji bezpośrednie federacyjnych, za pomocą usług AD FS lub dostawcy tożsamości innej firmy, organizacje skojarzyć jedną lub więcej przestrzeni nazw domeny do tych dostawców tożsamości. 

## <a name="end-user-experience"></a>Środowisko użytkownika końcowego 
Z Federacją bezpośrednie użytkownicy-goście, zaloguj się do dzierżawy usługi Azure AD przy użyciu własnego konta organizacji. Gdy uzyskujesz dostęp do zasobów udostępnionych i wyświetlenia monitu o logowanie, bezpośrednie Federacji użytkownicy są przekierowywani do ich tożsamości. Po pomyślnym zalogowaniu są zwracane do usługi Azure AD, aby uzyskiwać dostęp do zasobów. Bezpośrednie, tokeny odświeżania użytkowników federacyjnych są ważne przez 12 godzin, [domyślna długość przekazywanie tokenu odświeżania](../develop/active-directory-configurable-token-lifetimes.md#exceptions) w usłudze Azure AD. Jeśli dostawca tożsamości federacyjnych ma włączone logowanie Jednokrotne, użytkownik będzie środowisko logowania jednokrotnego i nie będą widzieć żadnych monit logowania po uwierzytelnieniu początkowej.

## <a name="limitations"></a>Ograniczenia

### <a name="dns-verified-domains-in-azure-ad"></a>Zweryfikowano DNS domeny w usłudze Azure AD
Bezpośrednie federacyjnej jest dozwolony tylko dla domen będących ***nie*** DNS zweryfikowane w usłudze Azure AD. Bezpośrednie federacji jest dozwolona dla dzierżawy niezarządzane (weryfikowany pocztą e-mail lub "wirusowego") usługi Azure AD, ponieważ nie są one zweryfikowane DNS.
### <a name="authentication-url"></a>Adres URL uwierzytelniania
Bezpośrednie federacyjnej jest dozwolona tylko dla zasad, których adres URL uwierzytelniania domeny pasuje do domeny docelowej lub której adres URL uwierzytelniania jest jednym z tych dostawców tożsamości dozwolonych (Ta lista może ulec zmianie):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Na przykład podczas konfigurowania bezpośrednich Federację dla **fabrikam.com**, adres URL uwierzytelniania `https://fabrikam.com/adfs` przekazują sprawdzania poprawności. Hosta w tej samej domeny również będzie przekazywać, na przykład `https://sts.fabrikam.com/adfs`. Jednak adres URL uwierzytelniania `https://fabrikamconglomerate.com/adfs` lub `https://fabrikam.com.uk/adfs` dla tej samej domenie nie przekaże.

### <a name="signing-certificate-renewal"></a>Odnawianie certyfikatu podpisywania
Jeśli określisz adres URL metadanych w ustawieniach dostawcy tożsamości usługi Azure AD będzie automatycznie odnawiana certyfikat podpisywania, po jego wygaśnięciu. Jednakże jeśli certyfikat jest obracana jakiegokolwiek powodu przed upływem czasu wygaśnięcia lub jeśli nie podasz adresu URL metadanych, usługi Azure AD będzie mógł go odnowić. W takim przypadku należy ręcznie zaktualizować certyfikat podpisywania.
## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Można skonfigurować bezpośrednie federacji z domeny, dla której istnieje niezarządzanej dzierżawy (weryfikowany pocztą e-mail)? 
Tak. Jeśli nie zostało zweryfikowane domeny, a nie zostało poddane dzierżawy [przejęcia przez administratora](../users-groups-roles/domains-admin-takeover.md), można skonfigurować bezpośrednie federacji. Dzierżaw niezarządzanego lub weryfikowany pocztą e-mail są tworzone, gdy użytkownik umarza zaproszenie B2B lub wykonuje samoobsługowego tworzenia nowego konta usługi Azure AD przy użyciu domeny, która obecnie nie istnieje. Można skonfigurować bezpośrednie federacji z tych domen. Jeśli próbujesz skonfigurować bezpośrednie federacji z weryfikacji DNS domeny, w witrynie Azure portal lub za pośrednictwem programu PowerShell, zobaczysz błąd.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Jeśli bezpośrednie federacyjnych i uwierzytelniania jednorazowy kod dostępu poczty e-mail są włączone, jakiej metody ma pierwszeństwo przed?
W przypadku Federacji bezpośrednie jest nawiązywane z organizacji partnerskiej, ma pierwszeństwo przed uwierzytelniania jednorazowy kod dostępu poczty e-mail dla nowych użytkowników-gości z tej organizacji. Jeśli użytkownik-Gość zrealizować zaproszenia przy użyciu uwierzytelniania jednorazowy kod dostępu, przed rozpoczęciem konfigurowania Federacji bezpośrednich, będą one nadal korzystać z uwierzytelniania jednorazowy kod dostępu. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Bezpośredni Federacji adres problemy dotyczące logowania z powodu częściowo zsynchronizowane dzierżawy?
Nie, [e-mail jednorazowy kod dostępu](one-time-passcode.md) funkcja powinna być używana w tym scenariuszu. "Częściowo zsynchronizowane dzierżawy" odnosi się do dzierżawy usługi Azure AD partner, gdzie tożsamości użytkowników w środowisku lokalnym nie są w pełni zsynchronizowane z chmurą. Gościa, którego tożsamość jeszcze nie istnieje w chmurze, ale kto próbuje zrealizować zaproszenia B2B nie będzie można się zalogować. Umożliwia to gościa zarejestrować się w funkcji jednorazowy kod dostępu. Funkcja federacyjnego bezpośrednie jest przeznaczona dla scenariuszy, gdzie gość ma swoje własne konta organizacyjnego zarządzane przez dostawcę tożsamości, ale organizacja nie ma w ogóle nie obecności usługi Azure AD.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Krok 1: Konfigurowanie organizacji partnera dostawcy tożsamości
Po pierwsze organizacji partnera musi skonfigurować jego dostawcy tożsamości za pomocą wymaganych oświadczeń i jednostek uzależnionych. 

> [!NOTE]
> Aby zilustrować, jak skonfigurować dostawcę tożsamości dla Federacji bezpośrednie, użyjemy usługi Active Directory Federation Services (AD FS) jako przykład. Zapoznaj się z artykułem [konfigurowania bezpośrednich federacji z usługami AD FS](direct-federation-adfs.md), co daje przykłady sposobu konfiguracji usług AD FS jako dostawca tożsamości SAML 2.0 i WS-Fed w ramach przygotowania do bezpośredniego federacji.

### <a name="saml-20-configuration"></a>Konfiguracja SAML 2.0

Usługa Azure AD B2B można skonfigurować federowania z dostawcami tożsamości, które używają protokołu SAML przy użyciu określonych wymagań wymienionych poniżej. Aby uzyskać więcej informacji na temat konfigurowania relacji zaufania między Twojego dostawcy tożsamości SAML i usługa Azure AD, zobacz [Użyj protokołu SAML 2.0 dostawcy tożsamości (IdP) dla logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Uwaga domeny docelowej do bezpośredniego federacji nie może być zweryfikowany DNS w usłudze Azure AD. Domena adresu URL uwierzytelniania musi być zgodny w domenie docelowej, lub musi być domeny przez dostawcę tożsamości dozwolone. Zobacz [ograniczenia](#limitations) sekcji, aby uzyskać szczegółowe informacje. 

#### <a name="required-saml-20-attributes-and-claims"></a>Wymaganych atrybutów SAML 2.0 i oświadczenia
W poniższej tabeli przedstawiono wymagania dotyczące określonych atrybutów i oświadczenia, które muszą zostać skonfigurowane u dostawcy tożsamości innej firmy. Aby skonfigurować bezpośrednie Federacji, następujące atrybuty musi otrzymać odpowiedzi SAML 2.0, od dostawcy tożsamości. Te atrybuty można skonfigurować, tworząc usługę tokenu zabezpieczającego online pliku XML lub wprowadzając je ręcznie.

Wymaganych atrybutów dla odpowiedzi SAML 2.0, od dostawcy tożsamości:

|Atrybut  |Wartość  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Wystawcy identyfikatora URI partnera dostawcy tożsamości, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |


Wymagane oświadczenia dla tokenu języka SAML 2.0, wystawiony przez dostawców tożsamości:

|Atrybut  |Value  |
|---------|---------|
|Format identyfikatora NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Konfiguracja usługi WS-Fed 
Usługa Azure AD B2B można skonfigurować federowania z dostawcami tożsamości, które za pomocą protokołu WS-Fed niektóre szczególne wymagania wymienione poniżej. Obecnie dwóch dostawców usługi WS-Fed zostały przetestowane na zgodność z usługą Azure AD usług AD FS i sieci Shibboleth. Aby uzyskać więcej informacji na temat ustanawiania zaufania jednostki uzależnionej między usługi WS-Fed dostawcy zgodne z usługą Azure AD, zobacz "Integracja z usługą STS dokument za pomocą protokołów WS" dostępne w [dokumentacja zgodności dostawca tożsamości platformy Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Domeny docelowej do bezpośredniego federacji nie może być zweryfikowany DNS w usłudze Azure AD. Domena adresu URL uwierzytelniania muszą być zgodne, domeny docelowej lub domeny przez dostawcę tożsamości dozwolone. Zobacz [ograniczenia](#limitations) sekcji, aby uzyskać szczegółowe informacje. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Wymagane usługi WS-Fed atrybutach i oświadczeniach

W poniższej tabeli przedstawiono wymagania dotyczące określonych atrybutów i oświadczenia, które muszą być skonfigurowane na dostawcy tożsamości usługi WS-Fed innych firm. Aby skonfigurować bezpośrednie Federacji, następujące atrybuty musi zostać odebrana w wiadomości WS-Fed od dostawcy tożsamości. Te atrybuty można skonfigurować, tworząc usługę tokenu zabezpieczającego online pliku XML lub wprowadzając je ręcznie.

Wymaganych atrybutów w wiadomości WS-Fed od dostawcy tożsamości:
 
|Atrybut  |Wartość  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Wystawcy identyfikatora URI partnera dostawcy tożsamości, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |

Wymagane oświadczenia dla tokenu usługi WS-Fed, wystawiony przez dostawców tożsamości:

|Atrybut  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Krok 2: Konfigurowanie Federacji bezpośrednie w usłudze Azure AD 
Następnie należy skonfigurować Federacji za pomocą dostawcy tożsamości, skonfigurowane w kroku 1 w usłudze Azure AD. Można użyć portalu usługi Azure AD lub programu PowerShell. Może zająć 5 – 10 minut, zanim zasad bezpośrednie Federacji staje się skuteczny. W tym czasie nie próbuj zrealizować zaproszenia do domeny federacyjnej bezpośrednich. Wymagane są następujące atrybuty:
- Wystawcy identyfikatora URI partnera dostawcy tożsamości
- Punkt końcowy uwierzytelniania pasywnym partnera dostawcy tożsamości (obsługiwane tylko https)
- Certyfikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>W celu skonfigurowania Federacji bezpośrednie w portalu usługi Azure AD

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **relacje w organizacji**.
3. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **dostawcy tożsamości SAML nowy/WS-Fed**.

    ![Zrzut ekranu przedstawiający przycisk dodawania nowych SAML lub tożsamości usługi WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Na **dostawcy tożsamości SAML nowy/WS-Fed** w obszarze **protokół dostawcy tożsamości**, wybierz opcję **SAML** lub **WS-FED**.

    ![Zrzut ekranu przedstawiający analizy przycisk na stronie SAML lub tożsamości usługi WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Wprowadź nazwę domeny w organizacji partnera, którego będzie nazwa docelowej domeny dla Federacji bezpośrednie
6. Możesz przekazać plik metadanych, aby wypełnić metadanych szczegóły. Jeśli użytkownik chce ręcznie wejściowych metadanych, wprowadź następujące informacje:
   - Nazwa domeny partnera dostawcy tożsamości
   - Identyfikator jednostki partnera dostawcy tożsamości
   - Punktu końcowego pasywnego obiektu żądającego partnera dostawcy tożsamości
   - Certyfikat
   > [!NOTE]
   > Adres URL metadanych jest opcjonalny, ale firma Microsoft zdecydowanie zaleca. Jeśli podasz adres URL metadanych, usługi Azure AD może automatycznie odnowić certyfikat podpisywania, po jego wygaśnięciu. Jeśli certyfikat jest obracana jakiegokolwiek powodu przed upływem czasu wygaśnięcia lub adres URL metadanych nie są oferowane usługi Azure AD będzie mógł go odnowić. W takim przypadku należy ręcznie zaktualizować certyfikat podpisywania.

7. Wybierz pozycję **Zapisz**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>W celu skonfigurowania Federacji bezpośrednie w usłudze Azure AD przy użyciu programu PowerShell

1. Zainstaluj najnowszą wersję programu Azure AD PowerShell dla modułu programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Aby uzyskać szczegółowy opis kroków, szybkiego startu, aby dodać użytkownika-gościa zawiera sekcję [zainstalować najnowszy moduł AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Uruchom następujące polecenie: 
   ```powershell
   Connect-AzureAD
   ```
1. W wierszu logowania Zaloguj się przy użyciu zarządzanego konta administratora globalnego. 
2. Uruchom następujące polecenia, zastępując wartości z pliku metadanych federacji. Dla serwera usług AD FS i usługi Okta pliku federacji jest federationmetadata.xml, na przykład: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Krok 3: Testowanie bezpośrednich federacji w usłudze Azure AD
Teraz przetestować konfigurację bezpośrednie Federacji zapraszając nowy użytkownik-Gość B2B. Aby uzyskać więcej informacji, zobacz [użytkowników we współpracy Dodaj B2B w usłudze Azure AD w witrynie Azure portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Jak edytować relację bezpośrednie Federacji?

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **relacje w organizacji**.
3. Wybierz **dostawców tożsamości**
4. W obszarze **dostawcy tożsamości SAML/WS-Fed**, wybierz dostawcę.
5. W okienku szczegółów dostawcy tożsamości zaktualizuj wartości.
6. Wybierz pozycję **Zapisz**.


## <a name="how-do-i-remove-direct-federation"></a>Jak usunąć bezpośredniego federacyjnej?
Możesz usunąć konfigurację bezpośrednie federacji. Jeśli to zrobisz, bezpośrednie Federacji gościa użytkowników, którzy już wykorzystana zaproszenia nie będzie mógł zalogować. Jednak możesz udzielić im dostępu do zasobów ponownie przez usunięcie ich z katalogu i reinviting je. Aby usunąć bezpośredniego Federacji za pomocą dostawcy tożsamości w portalu usługi Azure AD:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **relacje w organizacji**.
3. Wybierz **dostawców tożsamości**.
4. Wybierz dostawcę tożsamości, a następnie wybierz **Usuń**. 
5. Wybierz **tak** aby potwierdzić usunięcie. 

Aby usunąć bezpośredniego Federacji za pomocą dostawcy tożsamości za pomocą programu PowerShell:
1. Zainstaluj najnowszą wersję programu Azure AD PowerShell dla modułu programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Uruchom następujące polecenie: 
   ```powershell
   Connect-AzureAD
   ```
3. W wierszu logowania Zaloguj się przy użyciu zarządzanego konta administratora globalnego. 
4. Wprowadź następujące polecenie:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
