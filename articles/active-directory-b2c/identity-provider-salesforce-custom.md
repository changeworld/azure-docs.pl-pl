---
title: Konfigurowanie logowania się u dostawcy saml salesforce przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Skonfiguruj rejestrację u dostawcy SAML salesforce przy użyciu niestandardowych zasad w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67fe9ef4ad2b025d11f88976973658c9cd8ae693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187954"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania się u dostawcy saml salesforce przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie dla użytkowników z organizacji Salesforce przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C). Zaloguj się, dodając [profil techniczny SAML](saml-technical-profile.md) do zasad niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w [wprowadzenie do niestandardowych zasad w usłudze Azure Active Directory B2C](custom-policy-get-started.md).
- Jeśli jeszcze tego nie zrobiłeś, zarejestruj się, aby korzystać z [bezpłatnego konta Developer Edition.](https://developer.salesforce.com/signup) W tym artykule użyto [programu Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Skonfiguruj moją domenę](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) dla swojej organizacji Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurowanie Salesforce jako dostawcy tożsamości

1. [Zaloguj się do Salesforce](https://login.salesforce.com/).
2. W menu po lewej stronie w obszarze **Ustawienia**rozwiń węzeł **Tożsamość**, a następnie wybierz pozycję **Dostawca tożsamości**.
3. Wybierz **pozycję Włącz dostawcę tożsamości**.
4. W obszarze **Wybierz certyfikat**wybierz certyfikat, którego program Salesforce ma używać do komunikowania się z usługą Azure AD B2C. Można użyć certyfikatu domyślnego.
5. Kliknij przycisk **Zapisz**.

### <a name="create-a-connected-app-in-salesforce"></a>Tworzenie połączonej aplikacji w Salesforce

1. Na stronie **Dostawca tożsamości** wybierz dostawców usług są teraz tworzone za **pośrednictwem połączonych aplikacji. Kliknij tutaj.**
2. W obszarze **Podstawowe informacje**wprowadź wymagane wartości dla połączonej aplikacji.
3. W obszarze **Ustawienia aplikacji sieci Web**zaznacz pole wyboru Włącz **SAML.**
4. W polu **Identyfikator jednostki** wprowadź następujący adres URL. Upewnij się, że wartość `your-tenant` została zastąpiona nazwą dzierżawy usługi Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. W polu **Adres URL acs** wprowadź następujący adres URL. Upewnij się, że wartość `your-tenant` została zastąpiona nazwą dzierżawy usługi Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Przewiń do dołu listy, a następnie kliknij przycisk **Zapisz**.

### <a name="get-the-metadata-url"></a>Pobierz adres URL metadanych

1. Na stronie przeglądu połączonej aplikacji kliknij pozycję **Zarządzaj**.
2. Skopiuj wartość **punktu końcowego odnajdywania metadanych,** a następnie zapisz ją. Użyjesz go w dalszej części tego artykułu.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurowanie użytkowników Salesforce do federacji

1. Na stronie **Zarządzanie** połączoną aplikacją kliknij pozycję **Zarządzaj profilami**.
2. Wybierz profile (lub grupy użytkowników), które chcesz sfektorować z usługą Azure AD B2C. Jako administrator systemu zaznacz pole wyboru **Administrator systemu,** aby można było je sfedować przy użyciu konta Salesforce.

## <a name="generate-a-signing-certificate"></a>Generowanie certyfikatu podpisywania

Żądania wysyłane do salesforce muszą być podpisane przez usługę Azure AD B2C. Aby wygenerować certyfikat podpisywania, otwórz program Azure PowerShell, a następnie uruchom następujące polecenia.

> [!NOTE]
> Upewnij się, że zaktualizowano nazwę dzierżawy i hasło w dwóch pierwszych wierszach.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać certyfikat utworzony w dzierżawie usługi Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
5. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
6. W **Options**przypadku `Upload`opcji wybierz opcję .
7. Wprowadź wartość **Nazwa** dla zasad. Na przykład SAMLSigningCert. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. Przejdź do utworzonego certyfikatu B2CSigningCert.pfx i wybierz go.
9. Wprowadź **hasło** certyfikatu.
3. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy logowali się przy użyciu konta Salesforce, musisz zdefiniować konto jako dostawcę oświadczeń, z którego usługa Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik uwierzytelnił.

Konto Salesforce można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad. Aby uzyskać więcej informacji, zobacz [definiowanie profilu technicznego SAML](saml-technical-profile.md).

1. Otwórz *plik TrustFrameworkExtensions.xml*.
1. Znajdź **ClaimsProviders** element. Jeśli nie istnieje, dodaj go w elemencie głównym.
1. Dodaj nowy **ClaimsProvider** w następujący sposób:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zaktualizuj wartość **programu PartnerEntity** za pomocą adresu URL metadanych Salesforce skopiowanego wcześniej.
1. Zaktualizuj wartość obu wystąpień **StorageReferenceId** do nazwy klucza certyfikatu podpisywania. Na przykład B2C_1A_SAMLSigningCert.
1. Znajdź `<ClaimsProviders>` sekcję i dodaj następujący fragment kodu XML. Jeśli zasady zawierają `SM-Saml-idp` już profil techniczny, przejdź do następnego kroku. Aby uzyskać więcej informacji, zobacz [zarządzanie sesjami logowania jednokrotnego](custom-policy-reference-sso.md).

    ```XML
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Prześlij plik rozszerzenia w celu weryfikacji

Do tej pory skonfigurowano zasady, dzięki czemu usługa Azure AD B2C wie, jak komunikować się z kontem Salesforce. Spróbuj przesłać plik rozszerzenia zasad, aby potwierdzić, że do tej pory nie ma żadnych problemów.

1. Na stronie **Zasady niestandardowe** w dzierżawie usługi Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **zastępowanie zasad, jeśli istnieje**, a następnie przejdź do pliku *TrustFrameworkExtensions.xml.*
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji lub logowania. Aby go udostępnić, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał również dostawcę tożsamości Salesforce.

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu startowego.
2. Znajdź i skopiuj całą zawartość elementu `Id="SignUpOrSignIn"` **UserJourney,** który zawiera .
3. Otwórz *trustFrameworkExtensions.xml* i znajdź **UserJourneys** element. Jeśli element nie istnieje, dodaj jeden.
4. Wklej całą zawartość **elementu UserJourney,** który został skopiowany jako element podrzędny elementu **UserJourneys.**
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Wyświetl przycisk

**Element ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz **ClaimsProviderSelection** element dla konta LinkedIn, nowy przycisk pojawia się, gdy użytkownik ląduje na stronie.

1. Znajdź **OrchestrationStep** element, `Order="1"` który zawiera w podróży użytkownika, który został właśnie utworzony.
2. W obszarze **ClaimsProviderSelects**dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, `SalesforceExchange`na przykład:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Łączenie przycisku z akcją

Teraz, gdy masz przycisk w miejscu, musisz połączyć go z działaniem. Akcja, w tym przypadku, jest dla usługi Azure AD B2C do komunikowania się z kontem Salesforce, aby otrzymać token.

1. Znajdź **OrchestrationStep,** `Order="2"` który zawiera w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** upewniając się, że używasz tej samej wartości dla **identyfikatora,** która została użyta dla **obiektu TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** do **identyfikatora** profilu technicznego utworzonego wcześniej. Na przykład `LinkedIn-OAUTH`.

3. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Komunikacja z usługą Azure AD B2C odbywa się za pośrednictwem aplikacji, która rejestrujesz się w dzierżawie B2C. W tej sekcji wymieniono opcjonalne kroki, które można wykonać, aby utworzyć aplikację testową, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnianej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został właśnie utworzony:

1. Zrób kopię *pliku SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład zmień jego nazwę na *SignUpSignInSalesforce.xml*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** z unikatową wartością. Na przykład `SignUpSignInSalesforce`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład,`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **defaultuserJourney,** aby dopasować identyfikator nowego środowiska podróży użytkownika, który został utworzony (SignUpSignInSalesforce).
5. Zapisz zmiany, przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że utworzona aplikacja usługi Azure AD B2C jest zaznaczona w polu **Wybierz aplikację,** a następnie przetestuj ją, klikając przycisk **Uruchom teraz**.
