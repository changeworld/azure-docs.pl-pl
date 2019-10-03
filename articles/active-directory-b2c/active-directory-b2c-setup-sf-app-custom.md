---
title: Konfigurowanie logowania za pomocą dostawcy protokołu SAML usługi Salesforce przy użyciu zasad niestandardowych w programie Azure Active Directory B2C
description: Konfigurowanie logowania za pomocą dostawcy protokołu SAML usługi Salesforce przy użyciu zasad niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2922aa32bab2d9d7146a03757850d4b724ad7570
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71822265"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania za pomocą dostawcy protokołu SAML usługi Salesforce przy użyciu zasad niestandardowych w programie Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania dla użytkowników z organizacji usługi Salesforce przy użyciu [zasad niestandardowych](active-directory-b2c-overview-custom.md) w programie Azure Active Directory B2C (Azure AD B2C). Aby włączyć logowanie, Dodaj [profil techniczny SAML](saml-technical-profile.md) do zasad niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Jeśli jeszcze tego nie zrobiono, zarejestruj się w celu uzyskania [bezpłatnego konta dewelopera](https://developer.salesforce.com/signup). W tym artykule jest wykorzystywane [środowisko usługi Salesforce](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Skonfiguruj moją domenę](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) dla organizacji usługi Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurowanie usługi Salesforce jako dostawcy tożsamości

1. [Zaloguj się do usługi Salesforce](https://login.salesforce.com/).
2. W menu po lewej stronie w obszarze **Ustawienia**rozwiń węzeł **tożsamość**, a następnie wybierz pozycję **dostawca tożsamości**.
3. Wybierz pozycję **Włącz dostawcę tożsamości**.
4. W obszarze **Wybierz certyfikat**wybierz certyfikat, który ma być używany przez usługi Salesforce do komunikowania się z Azure AD B2C. Możesz użyć domyślnego certyfikatu.
5. Kliknij przycisk **Save** (Zapisz).

### <a name="create-a-connected-app-in-salesforce"></a>Tworzenie aplikacji połączonej w usłudze Salesforce

1. Na stronie **dostawca tożsamości** wybierz opcję **dostawcy usług są teraz tworzone przez połączone aplikacje. Kliknij tutaj.**
2. W obszarze **podstawowe informacje**wprowadź wymagane wartości dla połączonej aplikacji.
3. W obszarze **Ustawienia aplikacji sieci Web**zaznacz pole wyboru **Włącz protokół SAML** .
4. W polu **Identyfikator jednostki** wprowadź następujący adres URL. Upewnij się, że wartość `your-tenant` jest zastępowana nazwą dzierżawy Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. W polu **adres URL usługi ACS** wprowadź następujący adres URL. Upewnij się, że wartość `your-tenant` jest zastępowana nazwą dzierżawy Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Przewiń w dół listy, a następnie kliknij przycisk **Zapisz**.

### <a name="get-the-metadata-url"></a>Pobieranie adresu URL metadanych

1. Na stronie Przegląd połączonej aplikacji kliknij przycisk **Zarządzaj**.
2. Skopiuj wartość **punktu końcowego odnajdywania metadanych**, a następnie zapisz go. Zostanie ona użyta w dalszej części tego artykułu.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurowanie użytkowników usługi Salesforce do sfederować

1. Na stronie **Zarządzanie** połączonej aplikacji kliknij pozycję **Zarządzaj profilami**.
2. Wybierz Profile (lub grupy użytkowników), które chcesz sfederować z Azure AD B2C. Jako administrator systemu zaznacz pole wyboru **administrator systemu** , aby można było sfederować przy użyciu konta usługi Salesforce.

## <a name="generate-a-signing-certificate"></a>Generuj certyfikat podpisywania

Żądania wysyłane do usługi Salesforce muszą być podpisane przez Azure AD B2C. Aby wygenerować certyfikat podpisywania, Otwórz Azure PowerShell, a następnie uruchom następujące polecenia.

> [!NOTE]
> Upewnij się, że nazwa dzierżawy i hasło zostały zaktualizowane w dwóch pierwszych wierszach.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać certyfikat utworzony w dzierżawie Azure AD B2C.

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje**wybierz pozycję `Upload`.
7. Wprowadź wartość **Nazwa** dla zasad. Na przykład SAMLSigningCert. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. Przejdź do i wybierz utworzony certyfikat B2CSigningCert. pfx.
9. Wprowadź **hasło** dla certyfikatu.
3. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu konta usługi Salesforce, musisz zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto usługi Salesforce można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz *plik TrustFrameworkExtensions. XML*.
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Zaktualizuj wartość **PartnerEntity** za pomocą wcześniej SKOPIOWANEGO adresu URL metadanych usługi Salesforce.
5. Zaktualizuj wartość obu wystąpień elementu **identyfikatorze storagereferenceid** na nazwę klucza certyfikatu podpisywania. Na przykład B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Teraz zasady zostały skonfigurowane tak, aby Azure AD B2C wie, jak komunikować się z kontem usługi Salesforce. Spróbuj przekazać plik rozszerzenia zasad tylko, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions. XML* .
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji lub logowania. Aby można było go udostępnić, należy utworzyć duplikat istniejącej przejazdu użytkownika szablonu, a następnie zmodyfikować go tak, aby miał także dostawcę tożsamości usługi Salesforce.

1. Otwórz plik *TrustFrameworkBase. XML* z pakietu początkowego.
2. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , która zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *plik TrustFrameworkExtensions. XML* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
4. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz element **ClaimsProviderSelection** dla konta LinkedIn, nowy przycisk będzie wyświetlany, gdy użytkownik zostanie wystawiony na stronie.

1. Znajdź element **OrchestrationStep** , który zawiera `Order="1"` w podróży użytkownika, który właśnie został utworzony.
2. W obszarze **ClaimsProviderSelects**Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikowania się z kontem usługi Salesforce w celu uzyskania tokenu.

1. Znajdź **OrchestrationStep** , który obejmuje `Order="2"` w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla **identyfikatora** , który został użyty dla **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** na **Identyfikator** utworzonego wcześniej profilu technicznego. Na przykład `LinkedIn-OAUTH`.

3. Zapisz plik *TrustFrameworkExtensions. XML* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji Azure AD B2C

Komunikacja z Azure AD B2C odbywa się za pomocą aplikacji zarejestrowanej w dzierżawie B2C. W tej sekcji przedstawiono kroki opcjonalne, które można wykonać, aby utworzyć aplikację testową, jeśli nie została jeszcze wykonana.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje właśnie utworzoną drogę użytkownika:

1. Utwórz kopię *pliku SignUpOrSignIn. XML* w katalogu roboczym i zmień jego nazwę. Na przykład zmień nazwę na *SignUpSignInSalesforce. XML*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInSalesforce`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład `http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi nowej podróży użytkownika, która została utworzona (SignUpSignInSalesforce).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady z listy.
6. Upewnij się, że utworzona aplikacja Azure AD B2C została wybrana w polu **Wybierz aplikację** , a następnie przetestuj ją, klikając polecenie **Uruchom teraz**.
