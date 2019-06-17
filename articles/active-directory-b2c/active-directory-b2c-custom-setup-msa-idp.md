---
title: Dodaj konto Microsoft (MSA) jako dostawcy tożsamości za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przykład za pomocą programu Microsoft jako dostawcy tożsamości za pomocą protokołu OpenID Connect (OIDC).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d4b1f65daf271de88ce7167da6866e1890602fd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510327"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Ustawienia logowania za pomocą konta Microsoft, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowania dla użytkowników z konta Microsoft, za pomocą [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki [wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Jeśli nie masz jeszcze konta Microsoft, zrób to w [ https://www.live.com/ ](https://www.live.com/).

## <a name="add-an-application"></a>Dodawanie aplikacji

Aby użyć konta Microsoft jako dostawcy tożsamości w usłudze Azure AD B2C, musisz dodać aplikację konta Microsoft.

1. Zaloguj się do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przy użyciu poświadczeń konta Microsoft.
2. W prawym górnym rogu wybierz **Dodaj aplikację**.
3. Wprowadź **Nazwa aplikacji**, a następnie kliknij przycisk **Create** 
4. Wybierz **wygenerować nowe hasło** i upewnij się, skopiuj hasło do użycia podczas konfigurowania dostawcy tożsamości. Także skopiować identyfikator aplikacji. 
5. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **przekierowania adresów URL**. Zastąp `your-tenant-name` nazwą Twojej dzierżawy.
6. Wybierz pozycję **Zapisz**.

## <a name="create-a-policy-key"></a>Utwórz klucz zasad

Musisz zapisać hasło, która została wygenerowana i wcześniej zapisaną w dzierżawie usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz **struktura środowiska tożsamości — wersja ZAPOZNAWCZA**.
5. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
6. Aby uzyskać **opcje**, wybierz `Manual`.
7. Wprowadź **nazwa** klucza zasad. Na przykład `MSASecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **klucz tajny**, wprowadź hasło, które wcześniej zapisane.
9. Aby uzyskać **użycie klucza**, wybierz opcję `Signature`.
10. Kliknij pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Użytkownikom na logowanie przy użyciu konta Microsoft, należy zdefiniować konto jako dostawcy oświadczeń, które usługi Azure AD B2C mogą się komunikować za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. 

Można zdefiniować usługi Azure AD jako dostawcy oświadczeń, dodając **ClaimsProvider** elementu w pliku rozszerzenie zasad.

1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź **ClaimsProviders** elementu. Jeśli nie istnieje, należy dodać go pod elementem głównym.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client id</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

4.  Zastąp wartość **client_id** nagrane wcześniej identyfikator aplikacji.
5.  Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia o weryfikację

W razie skonfigurowano zasady tak, aby usługa Azure AD B2C wie, jak komunikować się z Twoim kontem Microsoft. Spróbuj przekazać plik rozszerzenia swoich zasad, aby potwierdzić, że wszystkie problemy nie ma do tej pory.

1. Na **zasady niestandardowe** strony w swojej dzierżawie usługi Azure AD B2C, wybierz opcję **zasady przekazywania**.
2. Włącz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustFrameworkExtensions.xml* pliku.
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępny w wszystkich ekranów rejestracji lub logowania. Aby udostępnić ją, utworzenie duplikatu istniejącego podróży użytkownika szablonu, a następnie zmodyfikuj go tak, aby w nim również dostawcy tożsamości konta Microsoft.

1. Otwórz *TrustFrameworkBase.xml* plik z pakietu startowego.
2. Znajdź i skopiuj cała zawartość **UserJourney** element, który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *TrustFrameworkExtensions.xml* i Znajdź **podróży użytkowników** elementu. Jeśli element nie istnieje, dodać.
4. Wklej całą zawartość **UserJourney** element, który został skopiowany jako element podrzędny elementu **podróży użytkowników** elementu.
5. Zmień nazwę identyfikator podróży użytkownika. Na przykład `SignUpSignInMSA`.

### <a name="display-the-button"></a>Wyświetlanie przycisku

**Elemencie ClaimsProviderSelection** element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz **elemencie ClaimsProviderSelection** elementu dla konta Microsoft, nowy przycisk pojawia się po użytkownik wyładowuje na stronie.

1. W *TrustFrameworkExtensions.xml* plików, Znajdź **OrchestrationStep** element, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelects**, Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** do odpowiedniej wartości, na przykład `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z konta w serwisie Twitter otrzymujących token.

1. Znajdź **OrchestrationStep** zawierającej `Order="2"` w podróży użytkownika.
2. Dodaj następujący kod **elementu ClaimsExchange** upewniając się, użyj tej samej wartości dla Identyfikatora, który był używany przez element **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```
    
    Zaktualizuj wartość **TechnicalProfileReferenceId** identyfikator profilu technicznego została utworzona wcześniej. Na przykład `MSA-OIDC`.

3. Zapisz *TrustFrameworkExtensions.xml* plik i ponownie przekazać go do weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Za pomocą usługi Azure AD B2c odbywa się przy użyciu aplikacji utworzonej w dzierżawie. W tej sekcji przedstawiono kroki opcjonalne, które możesz wykonać, aby utworzyć aplikację testu, jeśli jeszcze tego nie zrobiłeś.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji, na przykład *testapp1*.
6. Dla **aplikacji sieci Web / interfejs API sieci Web**, wybierz opcję `Yes`, a następnie wprowadź `https://jwt.ms` dla **adres URL odpowiedzi**.
7. Kliknij pozycję **Utwórz**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i przetestować go jednostki uzależnionej ze stron

Należy zaktualizować plik innych firm (RP) jednostki uzależnionej, która inicjuje podróży użytkownika, który został utworzony.

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład, zmień jej nazwę na *SignUpSignInMSA.xml*.
2. Otwórz nowy plik i zaktualizuj wartość **PolicyId** atrybutu dla **elementu TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInMSA`.
3. Zaktualizuj wartość **PublicPolicyUri** o identyfikatorze URI zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_msa`
4. Zaktualizuj wartość **ReferenceId** atrybutu w **DefaultUserJourney** aby dopasować identyfikator nowego podróży użytkownika utworzony (SignUpSignInMSA).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że zaznaczone jest aplikacja usługi Azure AD B2C, który został utworzony w **wybierz aplikację** pola, a następnie przetestuj go, klikając **Uruchom teraz**.
