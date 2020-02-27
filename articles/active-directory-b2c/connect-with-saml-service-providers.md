---
title: Skonfiguruj Azure AD B2C jako element SAML dostawcy tożsamości dla aplikacji
title-suffix: Azure AD B2C
description: Jak skonfigurować Azure AD B2C, aby zapewnić zatwierdzeń protokołu SAML dla aplikacji (usługodawców). Azure AD B2C będzie pełnić funkcję dostawcy pojedynczej tożsamości (dostawcy tożsamości) do aplikacji SAML.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5ec83857ebabc92bf86f9f84a43746a0e561218a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647602"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Rejestrowanie aplikacji SAML w Azure AD B2C

W tym artykule dowiesz się, jak skonfigurować Azure Active Directory B2C (Azure AD B2C) do działania jako SAML (SAML) dostawcy tożsamości (dostawcy tożsamości) w aplikacjach.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Organizacje używające Azure AD B2C jako rozwiązania do zarządzania tożsamościami i dostępem klientów mogą wymagać interakcji z dostawcami tożsamości lub aplikacjami skonfigurowanymi do uwierzytelniania przy użyciu protokołu SAML.

Azure AD B2C realizuje współdziałanie SAML w jeden z dwóch sposobów:

* Działając jako *dostawca tożsamości* (dostawcy tożsamości) i uzyskując Logowanie jednokrotne (SSO) z dostawcami usług opartymi na protokole SAML (aplikacje)
* Działając jako *dostawca usług* (SP) i współpracując z dostawcami tożsamości opartymi na protokole SAML, takimi jak SALESFORCE i ADFS

![Diagram z B2C jako dostawca tożsamości po lewej i B2C jako dostawca usługi po prawej](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Podsumowanie dwóch wyłącznych scenariuszy podstawowych przy użyciu protokołu SAML:

| Scenariusz | Rola Azure AD B2C | Porady |
| -------- | ----------------- | ------- |
| Moja aplikacja oczekuje potwierdzenia SAML do ukończenia uwierzytelniania. | **Azure AD B2C działa jako dostawca tożsamości (dostawcy tożsamości)**<br />Azure AD B2C działa jako dostawcy tożsamości SAML dla aplikacji. | Ten artykuł. |
| Moi użytkownicy potrzebują logowania jednokrotnego za pomocą dostawcy tożsamości zgodnego z protokołem SAML, takiego jak ADFS, Salesforce lub Shibboleth.  | **Azure AD B2C działa jako dostawca usług (SP)**<br />Azure AD B2C działa jako dostawca usługi podczas nawiązywania połączenia z dostawcą tożsamości SAML. Jest to federacyjny serwer proxy między aplikacją i dostawcą tożsamości SAML.  | <ul><li>[Konfigurowanie logowania za pomocą usług AD FS jako dostawcy tożsamości języka SAML przy użyciu zasad niestandardowych](identity-provider-adfs2016-custom.md)</li><li>[Konfigurowanie logowania za pomocą dostawcy protokołu SAML usługi Salesforce przy użyciu zasad niestandardowych](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Azure AD B2C](custom-policy-get-started.md). Zasady niestandardowe *SocialAndLocalAccounts* są potrzebne w ramach pakietu zasad niestandardowych, który został omówiony w artykule.
* Podstawowe informacje o protokole SAML (SAML).
* Aplikacja sieci Web skonfigurowana jako dostawca usługi SAML (SP). Na potrzeby tego samouczka możesz użyć [aplikacji testowej SAML][samltest] , którą udostępniamy.

## <a name="components-of-the-solution"></a>Składniki rozwiązania

W tym scenariuszu są wymagane trzy główne składniki:

* **Dostawca usług** SAML umożliwiający wysyłanie żądań SAML oraz odbieranie, dekodowanie i odpowiadanie na potwierdzenia saml z Azure AD B2C. Jest to również nazywane jednostką uzależnioną.
* Publicznie dostępny **punkt końcowy metadanych** SAML dla dostawcy usług.
* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)

Jeśli nie masz jeszcze dostawcy usługi SAML i skojarzonego z nim punktu końcowego metadanych, możesz użyć tej przykładowej aplikacji SAML, która została udostępniona do testowania:

[Aplikacja testowa SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Skonfiguruj certyfikaty

Aby utworzyć relację zaufania między dostawcą usług i Azure AD B2C, należy podać certyfikaty x509 i ich klucze prywatne.

* **Certyfikaty dostawcy usług**
  * Certyfikat z kluczem prywatnym przechowywanym w aplikacji sieci Web. Ten certyfikat jest używany przez dostawcę usługi do podpisywania żądania SAML wysłanego do Azure AD B2C. Azure AD B2C odczytuje klucz publiczny z metadanych dostawcy usług w celu zweryfikowania podpisu.
  * Obowiązkowe Certyfikat z kluczem prywatnym przechowywanym w aplikacji sieci Web. Azure AD B2C odczytuje klucz publiczny z metadanych dostawcy usług w celu zaszyfrowania potwierdzenia SAML. Następnie dostawca usług używa klucza prywatnego do odszyfrowania potwierdzenia.
* **Azure AD B2C certyfikaty**
  * Certyfikat z kluczem prywatnym w Azure AD B2C. Ten certyfikat jest używany przez Azure AD B2C do podpisania odpowiedzi SAML wysyłanej do dostawcy usług. Dostawca usług odczytuje klucz publiczny metadanych Azure AD B2C, aby zweryfikować podpis odpowiedzi SAML.

Możesz użyć certyfikatu wystawionego przez publiczny urząd certyfikacji lub, w tym samouczku, certyfikatu z podpisem własnym.

### <a name="11-prepare-a-self-signed-certificate"></a>1,1 Przygotowanie certyfikatu z podpisem własnym

Jeśli nie masz jeszcze certyfikatu, możesz użyć certyfikatu z podpisem własnym dla tego samouczka. W systemie Windows można użyć polecenia cmdlet [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) programu PowerShell do wygenerowania certyfikatu.

1. Wykonaj to polecenie programu PowerShell, aby wygenerować certyfikat z podpisem własnym. Zmodyfikuj `-Subject` argument odpowiedni dla aplikacji i Azure AD B2C nazwę dzierżawy. Możesz również dostosować `-NotAfter` datę, aby określić inne wygaśnięcie certyfikatu.

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

1. Otwórz przystawkę **Zarządzanie certyfikatami użytkowników** > **bieżący użytkownik** > **osobistych** > **certyfikatów** > *yourappname.yourtenant.onmicrosoft.com*
1. Wybierz **akcję** >ą > **wszystkie zadania** > **Eksportuj**
1. Wybierz pozycję **tak** , > **dalej** > **tak, eksportuj klucz prywatny** > **dalej**
1. Zaakceptuj wartości domyślne w **formacie pliku eksportu**
1. Podaj hasło do certyfikatu

### <a name="12-upload-the-certificate"></a>1,2. Przekaż certyfikat

Następnie Przekaż potwierdzenie SAML i certyfikat podpisywania odpowiedzi do Azure AD B2C.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do dzierżawy Azure AD B2C.
1. W obszarze **zasady**wybierz opcję **Struktura środowiska tożsamości** , a następnie pozycję **klucze zasad**.
1. Wybierz pozycję **Dodaj**, a następnie wybierz **Opcje** > **Przekaż**.
1. Wprowadź **nazwę**, na przykład *SamlIdpCert*. Prefiks *B2C_1A_* jest automatycznie dodawany do nazwy klucza.
1. Przekaż certyfikat przy użyciu kontrolki przekazywania pliku.
1. Wprowadź hasło certyfikatu.
1. Wybierz pozycję **Utwórz**.
1. Sprawdź, czy klucz jest wyświetlany zgodnie z oczekiwaniami. Na przykład *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Przygotuj zasady

### <a name="21-create-the-saml-token-issuer"></a>2,1 Tworzenie wystawcy tokenów SAML

Teraz Dodaj możliwość dla dzierżawy w celu wystawiania tokenów SAML.

Otwórz **`TrustFrameworkExtensions.xml`** `SocialAndLocalAccounts\`w ramach pakietu zasad niestandardowych.

Znajdź sekcję `<ClaimsProviders>` i Dodaj następujący fragment kodu XML.

Można zmienić wartość metadanych `IssuerUri`. Jest to identyfikator URI wystawcy, który jest zwracany w odpowiedzi SAML z Azure AD B2C. Aplikacja jednostki uzależnionej powinna być skonfigurowana w taki sposób, aby akceptowała identyfikator URI wystawcy podczas walidacji potwierdzenia SAML.

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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Dodaj zasady jednostek uzależnionych SAML

Teraz, gdy Dzierżawca może wystawiać potwierdzenia SAML, należy utworzyć zasady jednostek uzależnionych SAML i zmodyfikować drogę użytkownika, tak aby wystawić potwierdzenie SAML zamiast tokenu JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1 Tworzenie zasad tworzenia konta lub logowania

1. Utwórz kopię pliku *SignUpOrSignin. XML* w katalogu roboczym pakietu startowego i Zapisz go przy użyciu nowej nazwy. Na przykład *SignUpOrSigninSAML. XML*. Jest to plik zasad jednostki uzależnionej.

1. Otwórz plik *SignUpOrSigninSAML. XML* w preferowanym edytorze.

1. Zmień `PolicyId` i `PublicPolicyUri` zasad, aby _B2C_1A_signup_signin_saml_ i `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` jak pokazano poniżej.

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

1. Dodaj następujący fragment kodu XML tuż przed elementem `<RelyingParty>`. Ten plik XML zastępuje krok aranżacji nr 7 podróży użytkownika _SignUpOrSignIn_ . Jeśli rozpoczęto od innego folderu w pakiecie startowym lub dostosowałeś drogę użytkownika przez dodanie lub usunięcie kroków aranżacji, upewnij się, że liczba (w `order` elemencie) jest wyrównana z tą, którą określono w podróży użytkownika dla kroku wystawcy token (na przykład w innych folderach z pakietem startowym zostanie uruchomiony numer 4 dla `LocalAccounts`, 6 dla `SocialAccounts` i 9 dla `SocialAndLocalAccountsWithMfa`).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Zastąp cały element `<TechnicalProfile>` w elemencie `<RelyingParty>` następującym kodem XML profilu technicznego.

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

1. Zaktualizuj `tenant-name` przy użyciu nazwy dzierżawy Azure AD B2Cowej.

Końcowy plik zasad jednostki uzależnionej powinien wyglądać następująco:

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

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2 przekazywanie i testowanie metadanych zasad

Zapisz zmiany i przekaż nowy plik zasad. Po przekazaniu obu zasad (rozszerzenia i plików jednostek uzależnionych) Otwórz przeglądarkę internetową i przejdź do metadanych zasad.

Metadane dostawcy tożsamości zasad Azure AD B2C są informacje używane w protokole SAML do ujawnienia konfiguracji dostawcy tożsamości SAML. Metadane określają lokalizację usług, takich jak logowanie i wylogowywanie, certyfikaty, metoda logowania itd. Metadane zasad Azure AD B2C są dostępne pod następującym adresem URL. Zastąp `tenant-name` nazwą dzierżawy Azure AD B2C i `policy-name` z nazwą (IDENTYFIKATORem) zasad:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Twoje zasady niestandardowe i dzierżawa Azure AD B2C są teraz gotowe. Następnie utwórz rejestrację aplikacji w Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Skonfiguruj aplikację w katalogu Azure AD B2C

### <a name="41-register-your-application-in-azure-active-directory"></a>4,1 Zarejestruj swoją aplikację w Azure Active Directory

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)** , a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *SAMLApp1*.
1. W obszarze **obsługiwane typy kont**wybierz opcję **konta tylko w tym katalogu organizacji**
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Sieć Web**, a następnie wprowadź `https://localhost`. Tę wartość należy zmodyfikować później w manifeście rejestracji aplikacji.
1. Wybierz pozycję **Zarejestruj**.

### <a name="42-update-the-app-manifest"></a>4,2. Zaktualizuj manifest aplikacji

W przypadku aplikacji SAML istnieje kilka właściwości, które należy skonfigurować w manifeście rejestracji aplikacji.

1. W [Azure Portal](https://portal.azure.com)przejdź do rejestracji aplikacji utworzonej w poprzedniej sekcji.
1. W obszarze **Zarządzaj**wybierz pozycję **manifest** , aby otworzyć Edytor manifestu. Modyfikujesz kilka właściwości w poniższych sekcjach.

#### <a name="identifieruris"></a>identifierUris

`identifierUris` to kolekcja ciągów zawierająca zdefiniowane przez użytkownika identyfikatory URI, które w unikatowy sposób identyfikują aplikację sieci Web w ramach dzierżawy Azure AD B2C. Dostawca usług musi ustawić tę wartość w `Issuer` elemencie żądania SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Ta właściwość reprezentuje publicznie dostępny adres URL metadanych dostawcy usług. Adres URL metadanych może wskazywać na plik metadanych przekazany do dowolnego anonimowo dostępnego punktu końcowego, na przykład magazynu obiektów BLOB.

Metadane są informacje używane w protokole SAML do ujawnienia konfiguracji strony SAML, takiej jak dostawca usług. Metadane określają lokalizację usług, takich jak logowanie i wylogowywanie, certyfikaty, metoda logowania itd. Azure AD B2C odczytuje metadane dostawcy usługi i odpowiednio działa. Metadane nie są wymagane. Możesz również określić niektóre atrybuty, takie jak identyfikator URI odpowiedzi i identyfikator URI wylogowania, bezpośrednio w manifeście aplikacji.

Jeśli istnieją właściwości określone *zarówno* w adresie URL metadanych SAML, jak i w manifeście rejestracji aplikacji, zostaną one **scalone**. Właściwości określone w adresie URL metadanych są przetwarzane jako pierwsze i mają pierwszeństwo.

Dla tego samouczka korzystającego z aplikacji testowej SAML należy użyć następującej wartości dla `samlMetadataUrl`:

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (opcjonalnie)

Jeśli nie podasz identyfikatora URI metadanych, można jawnie określić adres URL odpowiedzi. Ta opcjonalna właściwość reprezentuje `AssertionConsumerServiceUrl` (`SingleSignOnService` adres URL w metadanych dostawcy usługi), a `BindingType` założono, że `HTTP POST`.

Jeśli zdecydujesz się skonfigurować adres URL odpowiedzi i adres URL wylogowywania w manifeście aplikacji bez użycia metadanych dostawcy usług, Azure AD B2C nie zweryfikuje podpisu żądania SAML ani nie szyfruje odpowiedzi SAML.

W tym samouczku, w którym używasz aplikacji testowej SAML, ustaw właściwość `url` `replyUrlsWithType` na wartość pokazaną w poniższym fragmencie kodu JSON.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (opcjonalnie)

Ta opcjonalna właściwość reprezentuje adres URL `Logout` (`SingleLogoutService` adres URL w metadanych jednostki uzależnionej), a `BindingType` dla tego celu przyjmuje się `Http-Redirect`.

W tym samouczku, który używa aplikacji testowej SAML, pozostaw `logoutUrl` ustawione na `https://samltestapp2.azurewebsites.net/logout`:

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Zaktualizuj kod aplikacji

Ostatnim krokiem jest włączenie Azure AD B2C jako elementu SAML dostawcy tożsamości w aplikacji jednostki uzależnionej SAML. Każda aplikacja jest różna i czynności, które należy wykonać, są różne. Aby uzyskać szczegółowe informacje, zajrzyj do dokumentacji aplikacji.

Niektóre lub wszystkie następujące elementy są zwykle wymagane:

* **Metadane**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Wystawca**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name`
* **Adres URL logowania/punkt końcowy SAML/adres URL SAML**: Sprawdź wartość w pliku metadanych
* **Certyfikat**: jest to *B2C_1A_SamlIdpCert*, ale bez klucza prywatnego. Aby uzyskać klucz publiczny certyfikatu:

    1. Przejdź do adresu URL metadanych określonego powyżej.
    1. Skopiuj wartość z elementu `<X509Certificate>`.
    1. Wklej ją do pliku tekstowego.
    1. Zapisz plik tekstowy jako plik *CER* .

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1 test z aplikacją testową SAML (opcjonalnie)

Aby ukończyć ten samouczek przy użyciu [aplikacji testowej SAML][samltest]:

* Zaktualizuj nazwę dzierżawy
* Nazwa zasad aktualizacji, na przykład *B2C_1A_signup_signin_saml*
* Określ identyfikator URI wystawcy: `https://contoso.onmicrosoft.com/app-name`

Wybierz pozycję **Zaloguj** , a następnie Wyświetl ekran logowania użytkownika końcowego. Po zalogowaniu potwierdzenie SAML jest wydawane z powrotem do przykładowej aplikacji.

## <a name="sample-policy"></a>Przykładowe zasady

Oferujemy kompletne przykładowe zasady, których można użyć do testowania z aplikacją testową SAML.

1. Pobieranie [przykładowych zasad logowania zainicjowanych przez protokół SAML-Sp](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Zaktualizuj `TenantId` tak, aby odpowiadała nazwie dzierżawy, na przykład *contoso.b2clogin.com*
1. Zachowaj nazwę zasad *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Obsługiwane i nieobsługiwane procedury SAML

Następujące scenariusze jednostki uzależnionej SAML (RP) są obsługiwane za pośrednictwem własnego punktu końcowego metadanych:

* Wiele adresów URL wylogowywania lub po wprowadzeniu powiązania dla adresu URL wylogowania w obiekcie głównym aplikacji/usługi.
* Określ klucz podpisywania, aby zweryfikować żądania RP w obiekcie głównym aplikacji/usługi.
* Określ klucz szyfrowania tokenu w obiekcie aplikacji/jednostki usługi.
* Logowania inicjowane przez dostawcę tożsamości nie są obecnie obsługiwane w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat [protokołu SAML można znaleźć w witrynie sieci Web języka Oasis](https://www.oasis-open.org/).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
