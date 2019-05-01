---
title: Konfigurowanie logowania za pomocą konta Google w usłudze Azure Active Directory B2C, za pomocą zasad niestandardowych | Dokumentacja firmy Microsoft
description: Konfigurowanie logowania za pomocą konta Google w usłudze Azure Active Directory B2C, za pomocą zasad niestandardowych.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 94b71dc8a355b818af6789022eb297100b569e73
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712021"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Skonfiguruj polecenie logowania za pomocą konta Google, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie dla użytkowników z konta Google przy użyciu [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki [wprowadzenie do zasad niestandardowych w Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Jeśli nie masz już konto Google, utworzyć w [utworzyć konto Google](https://accounts.google.com/SignUp).

## <a name="register-the-application"></a>Rejestrowanie aplikacji

Aby włączyć logowania dla użytkowników z konta Google, musisz utworzyć projekt aplikacji Google. 

1. Zaloguj się do [konsoli deweloperów Google](https://console.developers.google.com/) przy użyciu poświadczeń konta.
2. Wprowadź **Nazwa projektu**, kliknij przycisk **Utwórz**, a następnie upewnij się, czy przy użyciu nowego projektu.
3. Wybierz **poświadczenia** w menu po lewej stronie, a następnie wybierz **Utwórz poświadczenia > Identyfikator klienta Oauth**.
4. Wybierz **ekranie wyrażania zgody Konfiguruj**.
5. Wybierz lub Określ prawidłową **adres E-mail**, podaj **nazwa produktu** widocznym dla użytkowników, wprowadź `b2clogin.com` w **autoryzacji domen**, a następnie kliknij przycisk  **Zapisz**.
6. W obszarze **typ aplikacji**, wybierz opcję **aplikacji sieci Web**.
7. Wprowadź **nazwa** dla aplikacji.
8. W **JavaScript autoryzowanych źródeł**, wprowadź `https://your-tenant-name.b2clogin.com` i **identyfikatory URI przekierowania autoryzowanych**, wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nazwa Twojej dzierżawy należy zastąpić nazwę dzierżawy. Należy używać małych liter, podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C.
8. Kliknij pozycję **Utwórz**.
9. Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Konieczne będzie, obie z nich, aby skonfigurować Google jako dostawcy tożsamości w dzierżawie. Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń.

## <a name="create-a-policy-key"></a>Utwórz klucz zasad

Chcesz przechować klucz tajny klienta, który wcześniej zapisaną w dzierżawie usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz **struktura środowiska tożsamości — wersja ZAPOZNAWCZA**.
5. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
6. Aby uzyskać **opcje**, wybierz `Manual`.
7. Wprowadź **nazwa** klucza zasad. Na przykład `GoogleSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **klucz tajny**, wprowadź klucz tajny klienta, który wcześniej zarejestrowane.
9. Aby uzyskać **użycie klucza**, wybierz opcję `Signature`.
10. Kliknij pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Użytkownikom na logowanie przy użyciu konta Google, należy zdefiniować konto jako dostawcy oświadczeń, które usługi Azure AD B2C mogą się komunikować za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. 

Konto Google można zdefiniować jako dostawcy oświadczeń, przez dodanie jej do **ClaimsProviders** elementu w pliku rozszerzenie zasad.

1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź **ClaimsProviders** elementu. Jeśli nie istnieje, należy dodać go pod elementem głównym.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Ustaw **client_id** do Identyfikatora aplikacji z rejestracji aplikacji.
5. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia o weryfikację

W razie skonfigurowano zasady tak, aby usługa Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przekazać plik rozszerzenia swoich zasad, aby potwierdzić, że wszystkie problemy nie ma do tej pory.

1. Na **zasady niestandardowe** strony w swojej dzierżawie usługi Azure AD B2C, wybierz opcję **zasady przekazywania**.
2. Włącz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustFrameworkExtensions.xml* pliku.
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępny w wszystkich ekranów konta-dokonywania/logowania. Aby udostępnić ją, utworzenie duplikatu istniejącego podróży użytkownika szablonu, a następnie zmodyfikuj go tak, aby w nim również dostawcy tożsamości usługi Azure AD.

1. Otwórz *TrustFrameworkBase.xml* plik z pakietu startowego.
2. Znajdź i skopiuj cała zawartość **UserJourney** element, który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *TrustFrameworkExtensions.xml* i Znajdź **podróży użytkowników** elementu. Jeśli element nie istnieje, dodać.
4. Wklej całą zawartość **UserJourney** element, który został skopiowany jako element podrzędny elementu **podróży użytkowników** elementu.
5. Zmień nazwę identyfikator podróży użytkownika. Na przykład `SignUpSignInGoogle`.

### <a name="display-the-button"></a>Wyświetlanie przycisku

**Elemencie ClaimsProviderSelection** element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie konta-dokonywania/logowania. Jeśli dodasz **elemencie ClaimsProviderSelection** elementu dla konta Google, nowy przycisk pojawia się po użytkownik wyładowuje na stronie.

1. Znajdź **OrchestrationStep** element, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelects**, Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** do odpowiedniej wartości, na przykład `GoogleExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z kontem Google, aby otrzymać token.

1. Znajdź **OrchestrationStep** zawierającej `Order="2"` w podróży użytkownika.
2. Dodaj następujący kod **elementu ClaimsExchange** upewniając się, użyj tej samej wartości dla Identyfikatora, który był używany przez element **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```
    
    Zaktualizuj wartość **TechnicalProfileReferenceId** identyfikator profilu technicznego została utworzona wcześniej. Na przykład `Google-OAuth`.

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

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład, zmień jej nazwę na *SignUpSignInGoogle.xml*.
2. Otwórz nowy plik i zaktualizuj wartość **PolicyId** atrybutu dla **elementu TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInGoogle`.
3. Zaktualizuj wartość **PublicPolicyUri** o identyfikatorze URI zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_google`
4. Zaktualizuj wartość **ReferenceId** atrybutu w **DefaultUserJourney** aby dopasować identyfikator nowego podróży użytkownika utworzony (SignUpSignGoogle).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że zaznaczone jest aplikacja usługi Azure AD B2C, który został utworzony w **wybierz aplikację** pola, a następnie przetestuj go, klikając **Uruchom teraz**.
