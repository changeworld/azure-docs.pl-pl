---
title: Konfigurowanie usługi Azure AD B2C jako identyfikatora SAML dla aplikacji
title-suffix: Azure AD B2C
description: Jak skonfigurować usługę Azure AD B2C, aby zapewnić potwierdzenia protokołu SAML do aplikacji (dostawców usług). Usługa Azure AD B2C będzie działać jako dostawca tożsamości (IdP) do aplikacji SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051624"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Rejestrowanie aplikacji SAML w usłudze Azure AD B2C

W tym artykule dowiesz się, jak skonfigurować usługę Azure Active Directory B2C (Azure AD B2C) do działania jako dostawca tożsamości (SAML) (Security Assertion Markup Language, SAML) dla aplikacji.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Organizacje korzystające z usługi Azure AD B2C jako rozwiązania do zarządzania tożsamością i dostępem klientów mogą wymagać interakcji z dostawcami tożsamości lub aplikacjami skonfigurowanym do uwierzytelniania przy użyciu protokołu SAML.

Usługa Azure AD B2C osiąga współdziałanie SAML na jeden z dwóch sposobów:

* Działając jako *dostawca tożsamości* (IdP) i osiągając logowanie jednokrotne (Logowanie jednokrotne) u dostawców usług opartych na SAML (aplikacje)
* Działając jako *dostawca usług* (SP) i współpracując z dostawcami tożsamości opartymi na SAML, takimi jak Salesforce i ADFS

![Diagram z B2C jako dostawcą tożsamości po lewej stronie i B2C jako dostawcą usług po prawej stronie](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Podsumowanie dwóch niewyłącznych scenariuszy podstawowych za pomocą SAML:

| Scenariusz | Rola usługi Azure AD B2C | Porady |
| -------- | ----------------- | ------- |
| Moja aplikacja oczekuje potwierdzenia SAML, aby zakończyć uwierzytelnianie. | **Usługa Azure AD B2C działa jako dostawca tożsamości (IdP)**<br />Usługa Azure AD B2C działa jako identyfikator SAML do aplikacji. | Ten artykuł. |
| Moi użytkownicy potrzebują logowania jednokrotnego za pomocą dostawcy tożsamości zgodnego ze standardem SAML, takiego jak ADFS, Salesforce lub Shibboleth.  | **Usługa Azure AD B2C działa jako dostawca usług (SP)**<br />Usługa Azure AD B2C działa jako dostawca usług podczas łączenia się z dostawcą tożsamości SAML. Jest to serwer proxy federacji między aplikacją a dostawcą tożsamości SAML.  | <ul><li>[Konfigurowanie logowania się za pomocą usługi ADFS jako identyfikatora SAML przy użyciu zasad niestandardowych](identity-provider-adfs2016-custom.md)</li><li>[Konfigurowanie logowania się u dostawcy saml salesforce przy użyciu zasad niestandardowych](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki opisane w [wprowadzenie do niestandardowych zasad w usłudze Azure AD B2C.](custom-policy-get-started.md) Potrzebujesz *SocialAndLocalAccounts* zasady niestandardowe z pakietu startowego zasad niestandardowych omówione w artykule.
* Podstawowa znajomość protokołu SAML (Security Assertion Markup Language).
* Aplikacja sieci web skonfigurowana jako dostawca usług SAML (SP). W tym samouczku można użyć [aplikacji testowej SAML,][samltest] która zapewniamy.

## <a name="components-of-the-solution"></a>Elementy rozwiązania

Istnieją trzy główne składniki wymagane dla tego scenariusza:

* Dostawca **usług** SAML z możliwością wysyłania żądań SAML i odbierania, dekodowania i odpowiadania na potwierdzenia SAML z usługi Azure AD B2C. Jest to również znane jako jednostka uzależniająca.
* Publicznie dostępny **punkt końcowy metadanych** SAML dla usługodawcy.
* [Dzierżawa usługi Azure AD B2C](tutorial-create-tenant.md)

Jeśli nie masz jeszcze dostawcy usług SAML i skojarzonego punktu końcowego metadanych, możesz użyć tej przykładowej aplikacji SAML, która została udostępniona do testowania:

[Aplikacja testowa SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Konfigurowanie certyfikatów

Aby utworzyć relację zaufania między dostawcą usług a usługą Azure AD B2C, należy dostarczyć certyfikaty X509 aplikacji sieci web.

* **Certyfikaty usługodawcy**
  * Certyfikat z kluczem prywatnym przechowywanym w aplikacji sieci Web. Ten certyfikat jest używany przez usługodawcę do podpisywania żądania SAML wysłanego do usługi Azure AD B2C. Usługa Azure AD B2C odczytuje klucz publiczny z metadanych dostawcy usług, aby sprawdzić poprawność podpisu.
  * (Opcjonalnie) Certyfikat z kluczem prywatnym przechowywanym w aplikacji sieci Web. Usługa Azure AD B2C odczytuje klucz publiczny z metadanych dostawcy usług do szyfrowania potwierdzenia SAML. Dostawca usług następnie używa klucza prywatnego do odszyfrowania potwierdzenia.
* **Certyfikaty usługi Azure AD B2C**
  * Certyfikat z kluczem prywatnym w usłudze Azure AD B2C. Ten certyfikat jest używany przez usługę Azure AD B2C do podpisywania odpowiedzi SAML wysłanej do dostawcy usług. Usługodawca odczytuje klucz publiczny metadanych usługi Azure AD B2C w celu zweryfikowanie podpisu odpowiedzi SAML.

Można użyć certyfikatu wystawionego przez urząd certyfikacji publicznej lub, w tym samouczku, certyfikatu z podpisem własnym.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Przygotowanie certyfikatu z podpisem własnym

Jeśli nie masz jeszcze certyfikatu, możesz użyć certyfikatu z podpisem własnym dla tego samouczka. W systemie Windows można użyć polecenia cmdlet [New-SelfSignedCertificate programu](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) PowerShell do wygenerowania certyfikatu.

1. Wykonaj to polecenie programu PowerShell, aby wygenerować certyfikat z podpisem własnym. Zmodyfikuj `-Subject` argument odpowiednio dla aplikacji i nazwy dzierżawy usługi Azure AD B2C. Można również dostosować `-NotAfter` datę, aby określić inny wygaśnięcie certyfikatu.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Otwórz **zarządzanie certyfikatami** > użytkowników**Bieżące** > **certyfikaty** > **osobiste** > użytkowników*yourappname.yourtenant.onmicrosoft.com*
1. Wybierz certyfikat > **akcja** > **eksportu** **wszystkich zadań** > 
1. Wybierz **pozycję Tak** > **Dalej** > **Tak, wyeksportuj klucz** > prywatny**Dalej**
1. Akceptowanie ustawień domyślnych **formatu pliku eksportu**
1. Podaj hasło certyfikatu

### <a name="12-upload-the-certificate"></a>1.2 Prześlij certyfikat

Następnie przekaż certyfikat potwierdzenia SAML i podpisywania odpowiedzi do usługi Azure AD B2C.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do dzierżawy usługi Azure AD B2C.
1. W obszarze **Zasady**wybierz pozycję **Struktura środowiska tożsamości,** a następnie **klawisze zasad**.
1. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Opcje** > **przekazywania**.
1. Wprowadź **nazwę**, na przykład *SamlIdpCert*. Prefiks *B2C_1A_* jest automatycznie dodawany do nazwy klucza.
1. Przekaż certyfikat za pomocą formantu przekazywania pliku.
1. Wprowadź hasło certyfikatu.
1. Wybierz **pozycję Utwórz**.
1. Sprawdź, czy klucz jest wyświetlany zgodnie z oczekiwaniami. Na przykład *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Przygotuj swoją politykę

### <a name="21-create-the-saml-token-issuer"></a>2.1 Tworzenie wystawcy tokenu SAML

Teraz dodaj możliwość wystawiania tokenów SAML przez dzierżawę przy użyciu [identyfikatora wystawcy tokenów SAML](saml-issuer-technical-profile.md) i profilów technicznych [dostawcy sesji SAML.](custom-policy-reference-sso.md#samlssosessionprovider)

Otwórz `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** w pakiecie startowym niestandardowych zasad.

Znajdź `<ClaimsProviders>` sekcję i dodaj następujący fragment kodu XML.

Można zmienić wartość `IssuerUri` metadanych. Jest to identyfikator URI wystawcy, który jest zwracany w odpowiedzi SAML z usługi Azure AD B2C. Aplikacja jednostki uzależniona powinna być skonfigurowana do akceptowania identyfikatora URI wystawcy podczas sprawdzania poprawności potwierdzenia SAML.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Dodaj zasady jednostki uzależniającej SAML

Teraz, gdy dzierżawa może wystawiać potwierdzenia SAML, należy utworzyć zasady jednostki uzależniającej SAML i zmodyfikować proces użytkownika, tak aby wystawiał asercja SAML zamiast JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Tworzenie zasad rejestracji lub logowania

1. Utwórz kopię pliku *SignUpOrSignin.xml* w katalogu roboczym pakietu startowego i zapisz go pod nową nazwą. Na przykład *signuporsigninsaml.xml*. Jest to plik zasad jednostki uzależniającej.

1. Otwórz plik *SignUpOrSigninSAML.xml* w preferowanym edytorze.

1. Zmień `PolicyId` zasady `PublicPolicyUri` na _B2C_1A_signup_signin_saml_ i `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` jak widać poniżej.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Dodaj następujący fragment kodu XML `<RelyingParty>` tuż przed elementem. Ten xml zastępuje krok aranżacji numer 7 _rejestracji rejestracji._ Jeśli uruchomiono z innego folderu w pakiecie startowym lub dostosowano podróż użytkownika przez dodanie lub `order` usunięcie kroków aranżacji, upewnij się, że liczba (w elemencie) jest wyrównana z numerem określonym w podróży użytkownika dla kroku wystawcy tokenu (na przykład w innych folderach pakietu startowego jest to krok numer 4 dla `LocalAccounts`, 6 for `SocialAccounts` i 9 for `SocialAndLocalAccountsWithMfa`).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Zastąp `<TechnicalProfile>` cały `<RelyingParty>` element w elemencie następującym profilem technicznym XML.

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Zaktualizuj `tenant-name` nazwę dzierżawy usługi Azure AD B2C.

Ostateczny plik zasad jednostki uzależniającej powinien wyglądać następująco:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Przesyłanie i testowanie metadanych zasad

Zapisz zmiany i przekaż nowy plik zasad. Po przekazaniu obu zasad (rozszerzenia i plików jednostki uzależnionej) otwórz przeglądarkę internetową i przejdź do metadanych zasad.

Metadane dpd zasad usługi Azure AD B2C to informacje używane w protokole SAML do udostępnienia konfiguracji dostawcy tożsamości SAML. Metadane definiują lokalizację usług, takich jak logowanie i wylogowywanie, certyfikaty, metoda logowania i inne. Metadane zasad usługi Azure AD B2C są dostępne pod następującym adresem URL. Zamień `tenant-name` na nazwę dzierżawy usługi Azure `policy-name` AD B2C i nazwę (ID) zasad:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Twoje zasady niestandardowe i dzierżawa usługi Azure AD B2C są teraz gotowe. Następnie utwórz rejestrację aplikacji w usłudze Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Aplikacja instalacji w katalogu usługi Azure AD B2C

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Rejestrowanie aplikacji w usłudze Azure Active Directory

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *SAMLApp1*.
1. W obszarze **Obsługiwane typy kont**wybierz pozycję **Konta tylko w tym katalogu organizacyjnym**
1. W obszarze **Przekierowanie identyfikatora URI**wybierz pozycję **Sieć Web**, a następnie wprowadź . `https://localhost` Zmodyfikujesz tę wartość w dalszej części manifestu rejestracji aplikacji.
1. Wybierz pozycję **Zarejestruj**.

### <a name="42-update-the-app-manifest"></a>4.2 Aktualizacja manifestu aplikacji

W przypadku aplikacji SAML istnieje kilka właściwości, które należy skonfigurować w manifeście rejestracji aplikacji.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do rejestracji aplikacji utworzonej w poprzedniej sekcji.
1. W obszarze **Zarządzaj**wybierz **pozycję Manifest,** aby otworzyć edytor manifestów. Zmodyfikuj kilka właściwości w poniższych sekcjach.

#### <a name="identifieruris"></a>identyfikatorUris

Jest `identifierUris` to kolekcja ciągów zawierająca zdefiniowane przez użytkownika identyfikatory URI, które jednoznacznie identyfikują aplikację sieci Web w dzierżawie usługi Azure AD B2C. Usługodawca musi ustawić tę `Issuer` wartość w elemencie żądania SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Ta właściwość reprezentuje publicznie dostępny adres URL metadanych dostawcy usług. Adres URL metadanych może wskazywać plik metadanych przekazany do dowolnego anonimowo dostępnego punktu końcowego, na przykład magazynu obiektów blob.

Metadane są informacjami używanymi w protokole SAML do udostępnienia konfiguracji strony SAML, takiej jak dostawca usług. Metadane definiują lokalizację usług, takich jak logowanie i wylogowywanie, certyfikaty, metoda logowania i inne. Usługa Azure AD B2C odczytuje metadane dostawcy usług i działa odpowiednio. Metadane nie są wymagane. Można również określić niektóre atrybuty, takie jak identyfikator URI odpowiedzi i identyfikator URI wylogowania bezpośrednio w manifeście aplikacji.

Jeśli istnieją właściwości określone zarówno w adresie URL metadanych SAML, *jak* i w manifeście rejestracji aplikacji, są one **scalane**. Właściwości określone w adresie URL metadanych są przetwarzane jako pierwsze i mają pierwszeństwo.

W tym samouczku, który używa aplikacji testowej SAML, użyj następującej wartości dla: `samlMetadataUrl`

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (opcjonalnie)

Jeśli nie podasz identyfikatora URI metadanych, możesz jawnie określić adres URL odpowiedzi. Ta opcjonalna `AssertionConsumerServiceUrl` właściwość reprezentuje (`SingleSignOnService` adres URL `BindingType` w metadanych dostawcy usług) i zakłada `HTTP POST`się, że jest .

Jeśli zdecydujesz się skonfigurować adres URL odpowiedzi i adres URL wylogowania w manifeście aplikacji bez użycia metadanych dostawcy usług, usługa Azure AD B2C nie będzie sprawdzać poprawności podpisu żądania SAML ani szyfrować odpowiedzi SAML.

W tym samouczku, w którym używasz aplikacji `url` testowej SAML, ustaw właściwość `replyUrlsWithType` wartości wyświetlanej w poniższym urywek JSON.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (opcjonalnie)

Ta opcjonalna `Logout` właściwość`SingleLogoutService` reprezentuje adres URL (URL w `BindingType` metadanych jednostki uzależniającej), a dla tego zakłada `Http-Redirect`się.

W tym samouczku, który używa aplikacji `logoutUrl` testowej `https://samltestapp2.azurewebsites.net/logout`SAML, pozostaw ustawioną na:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Zaktualizuj kod aplikacji

Ostatnim krokiem jest włączenie usługi Azure AD B2C jako identyfikator SAML w aplikacji jednostki uzależniającej SAML. Każda aplikacja jest inna, a kroki, aby to zrobić, różnią się. Szczegółowe informacje można znaleźć w dokumentacji aplikacji.

Zazwyczaj wymagane są niektóre lub wszystkie z poniższych:

* **Metadane**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Wystawca**: Użyj identyfikatora jednostki w pliku metadanych
* **Adres URL/SAML logowania/adres URL SAML:** Sprawdź wartość w pliku metadanych
* **Certyfikat:** Jest to *B2C_1A_SamlIdpCert*, ale bez klucza prywatnego. Aby uzyskać klucz publiczny certyfikatu:

    1. Przejdź do adresu URL metadanych określonego powyżej.
    1. Skopiuj `<X509Certificate>` wartość w elemencie.
    1. Wklej go do pliku tekstowego.
    1. Zapisz plik tekstowy jako plik *cer.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Test z aplikacją testowej SAML (opcjonalnie)

Aby ukończyć ten samouczek za pomocą naszej [aplikacji testowej SAML:][samltest]

* Aktualizowanie nazwy dzierżawy
* Aktualizuj nazwę zasad, na przykład *B2C_1A_signup_signin_saml*
* Określ ten identyfikator URI wystawcy:`https://contoso.onmicrosoft.com/app-name`

Wybierz **zaloguj się** i powinien zostać wyświetlony ekran logowania użytkownika. Po zalogowaniu potwierdzenia SAML jest wydawane z powrotem do przykładowej aplikacji.

## <a name="sample-policy"></a>Przykładowe zasady

Zapewniamy pełną przykładową zasadę, której można użyć do testowania za pomocą aplikacji testowej SAML.

1. Pobieranie [przykładowych zasad logowania inicjowanych przez SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Aktualizuj, `TenantId` aby dopasować nazwę dzierżawy, na przykład *contoso.b2clogin.com*
1. Zachowaj nazwę zasad *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Obsługiwane i nieobsługiwały modalności SAML

Następujące scenariusze jednostki uzależniającej SAML (RP) są obsługiwane za pośrednictwem własnego punktu końcowego metadanych:

* Wiele adresów URL wylogowania lub powiązania POST dla adresu URL wylogowania w obiekcie jednostki aplikacji/usługi.
* Określ klucz podpisywania, aby zweryfikować żądania RP w obiekcie jednostkowym aplikacji/usługi.
* Określ klucz szyfrowania tokenu w obiekcie jednostkowym aplikacji/usługi.
* Logowania inicjowane przez dostawcę tożsamości nie są obecnie obsługiwane w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat [protokołu SAML](https://www.oasis-open.org/)można znaleźć na stronie internetowej OASIS .
- Pobierz aplikację internetową testu SAML z [repozytorium społeczności Usługi Ad AD B2C GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
