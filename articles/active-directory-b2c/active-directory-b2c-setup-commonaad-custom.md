---
title: Konfigurowanie logowania dla dostawcy tożsamości usługi Azure AD z wieloma dzierżawcami przy użyciu zasad niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Dodawanie dostawcy tożsamości usługi Azure AD z wieloma dzierżawami przy użyciu zasad niestandardowych — Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8524eb8f9a8d220964e5dd1f6f8dc6d1aaf94a6d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980722"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania do Azure Active Directory z wieloma dzierżawcami przy użyciu zasad niestandardowych w programie Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania dla użytkowników przy użyciu wielodostępnego punktu końcowego dla Azure Active Directory (Azure AD) przy użyciu [zasad niestandardowych](active-directory-b2c-overview-custom.md) w programie Azure AD B2C. Dzięki temu użytkownicy z wielu dzierżawców usługi Azure AD mogą logować się do Azure AD B2C bez konfigurowania dostawcy technicznego dla każdej dzierżawy. Jednak członkowie gościa w żadnej z tych dzierżawców **nie będą** mogli się zalogować. W tym celu należy [indywidualnie skonfigurować każdą dzierżawę](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com`jest używany w organizacji dzierżawy usługi Azure AD `fabrikamb2c.onmicrosoft.com` i jest używany jako dzierżawca Azure AD B2C w poniższych instrukcjach.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Zarejestruj aplikację

Aby włączyć Logowanie użytkowników z określonej organizacji usługi Azure AD, musisz zarejestrować aplikację w ramach organizacji dzierżawy usługi Azure AD.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD (contoso.com), klikając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
4. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
5. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
6. W polu **Typ aplikacji**wybierz opcję `Web app / API`.
7. W polu **adres URL logowania**wprowadź następujący adres URL w postaci małych liter, gdzie `your-tenant` jest zastępowana nazwą dzierżawy Azure AD B2C (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Kliknij przycisk **Utwórz**. Skopiuj **Identyfikator aplikacji** do użycia później.
9. Wybierz aplikację, a następnie wybierz pozycję **Ustawienia**.
10. Wybierz pozycję **klucze**, wprowadź opis klucza, wybierz czas trwania, a następnie kliknij przycisk **Zapisz**. Skopiuj wartość klucza, który zostanie wyświetlony później.
11. W obszarze **Ustawienia**wybierz pozycję **Właściwości**, ustaw opcję **wiele** dzierżawców `Yes`, a następnie kliknij przycisk **Zapisz** .

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz aplikacji utworzony w dzierżawie Azure AD B2C.

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
2. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
3. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
4. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
5. W obszarze **Opcje**wybierz `Manual`opcję.
6. Wprowadź **nazwę** klucza zasad. Na przykład `ContosoAppSecret`.  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
7. W **kluczu tajnym**wprowadź wcześniej zarejestrowany klucz aplikacji.
8. W obszarze **użycie klucza**wybierz `Signature`opcję.
9. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu usługi Azure AD, musisz zdefiniować usługę Azure AD jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń, dodając usługę Azure AD do elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz *plik TrustFrameworkExtensions. XML*.
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>

            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="unique_name" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. W elemencie **ClaimsProvider** należy zaktualizować wartość dla **domeny** do unikatowej wartości, która może być używana do odróżnienia od innych dostawców tożsamości.
5. W elemencie **profilu technicznym** zaktualizuj wartość parametru **DisplayName**. Ta wartość jest wyświetlana na przycisku logowania na ekranie logowania.
6. Ustaw **client_id** na identyfikator aplikacji z rejestracji aplikacji dzierżawy usługi Azure AD mulity.

### <a name="restrict-access"></a>Ogranicz dostęp

> [!NOTE]
> Użycie `https://sts.windows.net` jako wartości parametru **ValidTokenIssuerPrefixes** umożliwia wszystkim użytkownikom usługi Azure AD logowanie się do aplikacji.

Należy zaktualizować listę prawidłowych wystawców tokenów i ograniczyć dostęp do określonej listy użytkowników dzierżawy usługi Azure AD, którzy mogą się zalogować. Aby uzyskać wartości, należy zapoznać się z metadanymi dla każdej z określonych dzierżawców usługi Azure AD, z których użytkownik chce się zalogować. Format danych wygląda następująco: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, gdzie `your-tenant` jest nazwą dzierżawy usługi Azure AD (contoso.com lub inną dzierżawę usługi Azure AD).

1. Otwórz przeglądarkę i przejdź do adresu URL **metadanych** i Wyszukaj obiekt **Issuer** i skopiuj jego wartość. Powinien wyglądać następująco: `https://sts.windows.net/tenant-id/`.
2. Skopiuj i wklej wartość klucza **ValidTokenIssuerPrefixes** . Można dodać wiele, rozdzielając je przecinkami. Przykładem jest komentarz w przykładowym kodzie XML powyżej.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Teraz zasady zostały skonfigurowane tak, aby Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przekazać plik rozszerzenia zasad tylko, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions. XML* .
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji/logowania. Aby można było go udostępnić, należy utworzyć duplikat istniejącej przejazdu użytkownika szablonu, a następnie zmodyfikować go tak, aby miał także dostawcę tożsamości usługi Azure AD.

1. Otwórz plik *TrustFrameworkBase. XML* z pakietu początkowego.
2. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *plik TrustFrameworkExtensions. XML* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
4. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInContoso`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji/logowania. Jeśli dodasz element **ClaimsProviderSelection** dla usługi Azure AD, zostanie wyświetlony nowy przycisk, gdy użytkownik zostanie wystawiony na stronie.

1. Znajdź element **OrchestrationStep** , który obejmuje `Order="1"` w podróży użytkownika.
2. W obszarze **ClaimsProviderSelects**Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikacji z usługą Azure AD w celu uzyskania tokenu. Połącz przycisk z akcją, łącząc profil techniczny dostawcy oświadczeń usługi Azure AD.

1. Znajdź **OrchestrationStep** obejmujący `Order="2"` w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla **identyfikatora** , który został użyty dla **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** na **Identyfikator** utworzonego wcześniej profilu technicznego. Na przykład `Common-AAD`.

3. Zapisz plik *TrustFrameworkExtensions. XML* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji Azure AD B2C

Komunikacja z Azure AD B2C odbywa się za pomocą aplikacji utworzonej w dzierżawie. W tej sekcji przedstawiono kroki opcjonalne, które można wykonać, aby utworzyć aplikację testową, jeśli nie została jeszcze wykonana.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji, na przykład *testapp1*.
6. W polu **aplikacja sieci Web/interfejs API sieci Web**wybierz `Yes`pozycję `https://jwt.ms` , a następnie wprowadź **adres URL odpowiedzi**.
7. Kliknij przycisk **Utwórz**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Utwórz kopię *pliku SignUpOrSignIn. XML* w katalogu roboczym i zmień jego nazwę. Na przykład zmień nazwę na *SignUpSignContoso. XML*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInContoso`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi nowej podróży użytkownika, która została utworzona (SignUpSignContoso).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady z listy.
6. Upewnij się, że utworzona aplikacja Azure AD B2C została wybrana w polu **Wybierz aplikację** , a następnie przetestuj ją, klikając polecenie **Uruchom teraz**.
