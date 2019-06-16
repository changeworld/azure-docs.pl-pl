---
title: Konfigurowanie polecenie Zaloguj się przy użyciu konta w serwisie Twitter przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Skonfiguruj Zaloguj się przy użyciu konta w serwisie Twitter przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cc657e079949b8217031906efeb84049217d6493
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510246"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie polecenie Zaloguj się przy użyciu konta w serwisie Twitter przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowania dla użytkowników konta w serwisie Twitter przy użyciu [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki [wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Jeśli nie masz jeszcze konta w serwisie Twitter, utwórz je na [stronę rejestracji w usłudze Twitter](https://twitter.com/signup).

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby korzystać z usługi Twitter jako dostawcy tożsamości w usłudze Azure AD B2C, musisz utworzyć aplikację usługi Twitter.

1. Zaloguj się do [deweloperów w usłudze Twitter](https://developer.twitter.com/en/apps) witryny sieci Web przy użyciu poświadczeń konta usługi Twitter.
2. Wybierz **tworzenie aplikacji**.
3. Wprowadź **nazwy aplikacji** i **opis aplikacji**.
4. W **adres URL witryny internetowej**, wprowadź `https://your-tenant.b2clogin.com`. Zastąp `your-tenant` nazwą Twojej dzierżawy. Na przykład https://contosob2c.b2clogin.com.
5. Aby uzyskać **adresów URL wywołania zwrotnego**, wprowadź `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`. Zastąp `your-tenant` o nazwie Nazwa dzierżawy i `your-policy-Id` o identyfikatorze zasady. Na przykład `b2c_1A_signup_signin_twitter`. Należy używać małych liter, podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C.
6. W dolnej części strony, przeczytaj i zaakceptuj warunki, a następnie wybierz **Utwórz**.
7. Na **szczegóły aplikacji** wybierz opcję **Edytuj > Edytuj szczegóły**, pole wyboru dla **Włącz Zaloguj się przy użyciu usługi Twitter**, a następnie wybierz pozycję **Zapisz**.
8. Wybierz **klucze i tokeny** i Zarejestruj **klucz interfejsu API klienta** i **klucz tajny konsumenta interfejsu API** wartości do użycia później.

## <a name="create-a-policy-key"></a>Utwórz klucz zasad

Chcesz przechować klucz tajny, który wcześniej zapisaną w dzierżawie usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz **struktura środowiska tożsamości — wersja ZAPOZNAWCZA**.
5. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
6. Aby uzyskać **opcje**, wybierz `Manual`.
7. Wprowadź **nazwa** klucza zasad. Na przykład `TwitterSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **klucz tajny**, wprowadź klucz tajny klienta, który wcześniej zarejestrowane.
9. Aby uzyskać **użycie klucza**, wybierz opcję `Encryption`.
10. Kliknij pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Użytkownikom na logowanie przy użyciu konta w serwisie Twitter, należy zdefiniować konto jako dostawcy oświadczeń, które usługi Azure AD B2C mogą się komunikować za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. 

Można zdefiniować konta w serwisie Twitter jako dostawcy oświadczeń, przez dodanie jej do **ClaimsProviders** elementu w pliku rozszerzenie zasad.

1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź **ClaimsProviders** elementu. Jeśli nie istnieje, należy dodać go pod elementem głównym.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
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

4. Zastąp wartość **client_id** z kluczem konsumenta, który wcześniej zapisane.
5. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia o weryfikację

W razie skonfigurowano zasady tak, aby usługa Azure AD B2C wie, jak komunikować się z kontem LinkedIn. Spróbuj przekazać plik rozszerzenia swoich zasad, aby potwierdzić, że wszystkie problemy nie ma do tej pory.

1. Na **zasady niestandardowe** strony w swojej dzierżawie usługi Azure AD B2C, wybierz opcję **zasady przekazywania**.
2. Włącz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustFrameworkExtensions.xml* pliku.
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępny w wszystkich ekranów rejestracji lub logowania. Aby udostępnić ją, utworzenie duplikatu istniejącego podróży użytkownika szablonu, a następnie zmodyfikuj go tak, aby w nim również dostawcy tożsamości w usłudze Twitter.

1. Otwórz *TrustFrameworkBase.xml* plik z pakietu startowego.
2. Znajdź i skopiuj cała zawartość **UserJourney** element, który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *TrustFrameworkExtensions.xml* i Znajdź **podróży użytkowników** elementu. Jeśli element nie istnieje, dodać.
4. Wklej całą zawartość **UserJourney** element, który został skopiowany jako element podrzędny elementu **podróży użytkowników** elementu.
5. Zmień nazwę identyfikator podróży użytkownika. Na przykład `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Wyświetlanie przycisku

**Elemencie ClaimsProviderSelection** element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz **elemencie ClaimsProviderSelection** element dla konta w serwisie Twitter, nowy przycisk pojawia się po użytkownik wyładowuje na stronie.

1. Znajdź **OrchestrationStep** element, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelects**, Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** do odpowiedniej wartości, na przykład `TwitterExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z konta w serwisie Twitter otrzymujących token.

1. Znajdź **OrchestrationStep** zawierającej `Order="2"` w podróży użytkownika.
2. Dodaj następujący kod **elementu ClaimsExchange** upewniając się, użyj tej samej wartości dla Identyfikatora, który był używany przez element **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```
    
    Zaktualizuj wartość **TechnicalProfileReferenceId** identyfikator profilu technicznego została utworzona wcześniej. Na przykład `Twitter-OAUTH1`.

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

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład, zmień jej nazwę na *SignUpSignInTwitter.xml*.
2. Otwórz nowy plik i zaktualizuj wartość **PolicyId** atrybutu dla **elementu TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInTwitter`.
3. Zaktualizuj wartość **PublicPolicyUri** o identyfikatorze URI zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Zaktualizuj wartość **ReferenceId** atrybutu w **DefaultUserJourney** aby dopasować identyfikator nowego podróży użytkownika utworzony (SignUpSignTwitter).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że zaznaczone jest aplikacja usługi Azure AD B2C, który został utworzony w **wybierz aplikację** pola, a następnie przetestuj go, klikając **Uruchom teraz**.
