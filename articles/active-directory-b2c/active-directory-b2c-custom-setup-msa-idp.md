---
title: Dodaj konto Microsoft (MSA) jako dostawcy tożsamości za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C
description: Przykład za pomocą programu Microsoft jako dostawcy tożsamości za pomocą protokołu OpenID Connect (OIDC).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654150"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Ustawienia logowania za pomocą konta Microsoft, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowania dla użytkowników z konta Microsoft, za pomocą [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki [wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Jeśli nie masz jeszcze konta Microsoft, zrób to w [ https://www.live.com/ ](https://www.live.com/).

## <a name="add-an-application"></a>Dodawanie aplikacji

Aby włączyć logowania dla użytkowników za pomocą konta Microsoft, musisz zarejestrować aplikację w dzierżawie usługi Azure AD. Dzierżawy usługi Azure AD nie jest taka sama jak dzierżawy usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy usługi Azure AD.
1. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **rejestracje aplikacji**.
1. Wybierz **nowej rejestracji**.
1. Wprowadź **nazwa** dla aplikacji. Na przykład *MSAapp1*.
1. W obszarze **obsługiwane typy kont**, wybierz opcję **kont w dowolnym katalogu organizacji i osobistych kont Microsoft (np. Skype, Xbox, Outlook.com)** .
1. W obszarze **identyfikator URI przekierowania (opcjonalnie)** , wybierz opcję **Web** i wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w polu tekstowym. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C.
1. Wybierz **zarejestrować**
1. Rekord **identyfikator aplikacji (klienta)** wyświetlane na stronie przeglądu aplikacji. Jest to potrzebne podczas konfigurowania dostawcy oświadczeń w dalszej części tego tematu.
1. Wybierz **certyfikaty i klucze tajne**
1. Kliknij przycisk **nowy wpis tajny klienta**
1. Wprowadź **opis** dla wpisu tajnego, na przykład *klucz tajny klienta aplikacji MSA*, a następnie kliknij przycisk **Dodaj**.
1. Zapisz hasło aplikacji wyświetlone w **wartość** kolumny. Będzie ona używana w następnej sekcji.

## <a name="create-a-policy-key"></a>Utwórz klucz zasad

Teraz, po utworzeniu aplikacji w dzierżawie usługi Azure AD, należy przechowywać klucz tajny klienta tej aplikacji w dzierżawie usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz **struktura środowiska tożsamości**.
1. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
1. Aby uzyskać **opcje**, wybierz `Manual`.
1. Wprowadź **nazwa** klucza zasad. Na przykład `MSASecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. W **klucz tajny**, wprowadź klucz tajny klienta, zapisane w poprzedniej sekcji.
1. Aby uzyskać **użycie klucza**, wybierz opcję `Signature`.
1. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Aby umożliwić użytkownikom na logowanie przy użyciu konta Microsoft, należy zdefiniować konto jako dostawcy oświadczeń, które usługi Azure AD B2C mogą się komunikować za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Można zdefiniować usługi Azure AD jako dostawcy oświadczeń, dodając **ClaimsProvider** elementu w pliku rozszerzenie zasad.

1. Otwórz *TrustFrameworkExtensions.xml* plik zasad.
1. Znajdź **ClaimsProviders** elementu. Jeśli nie istnieje, należy dodać go pod elementem głównym.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

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
            <Item Key="client_id">Your Microsoft application client ID</Item>
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

1. Zastąp wartość **client_id** za pomocą aplikacji usługi Azure AD *identyfikator aplikacji (klienta)* zarejestrowany wcześniej.
1. Zapisz plik.

Twoje zasady zostały skonfigurowane teraz, tak aby usługi Azure AD B2C wie, jak komunikować się z aplikacji konta Microsoft w usłudze Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia o weryfikację

Przed kontynuowaniem należy przekazać zmodyfikowane zasady, aby upewnić się, że wszystkie problemy nie ma do tej pory.

1. Przejdź do swojej dzierżawy usługi Azure AD B2C w witrynie Azure portal i wybierz pozycję **struktura środowiska tożsamości**.
1. Na **zasady niestandardowe** wybierz opcję **przekazywanie niestandardowych zasad**.
1. Włącz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustFrameworkExtensions.xml* pliku.
1. Kliknij pozycję **Przekaż**.

Jeśli żadne błędy są wyświetlane w portalu, przejdź do następnej sekcji.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest jeszcze dostępna we wszystkich ekranów rejestracji lub logowania. Aby udostępnić ją, utworzenie duplikatu istniejącego podróży użytkownika szablonu, a następnie zmodyfikuj go tak, aby w nim również dostawcy tożsamości konta Microsoft.

1. Otwórz *TrustFrameworkBase.xml* plik z pakietu startowego.
1. Znajdź i skopiuj cała zawartość **UserJourney** element, który zawiera `Id="SignUpOrSignIn"`.
1. Otwórz *TrustFrameworkExtensions.xml* i Znajdź **podróży użytkowników** elementu. Jeśli element nie istnieje, dodać.
1. Wklej całą zawartość **UserJourney** element, który został skopiowany jako element podrzędny elementu **podróży użytkowników** elementu.
1. Zmień nazwę identyfikator podróży użytkownika. Na przykład `SignUpSignInMSA`.

### <a name="display-the-button"></a>Wyświetlanie przycisku

**Elemencie ClaimsProviderSelection** element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz **elemencie ClaimsProviderSelection** element dla konta Microsoft, nowy przycisk jest wyświetlane, gdy użytkownik wyładowuje na stronie.

1. W *TrustFrameworkExtensions.xml* plików, Znajdź **OrchestrationStep** element, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
1. W obszarze **ClaimsProviderSelects**, Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** do odpowiedniej wartości, na przykład `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z kontem Microsoft, aby otrzymać token.

1. Znajdź **OrchestrationStep** zawierającej `Order="2"` w podróży użytkownika.
1. Dodaj następujący kod **elementu ClaimsExchange** upewniając się, użyj tej samej wartości dla Identyfikatora, który był używany przez element **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** odpowiadać językowi `Id` wartość w **profilu technicznego** elementu dostawcy oświadczeń, dodano wcześniej. Na przykład `MSA-OIDC`.

1. Zapisz *TrustFrameworkExtensions.xml* plik i ponownie przekazać go do weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Za pomocą usługi Azure AD B2C odbywa się za pośrednictwem aplikacji, który zostanie utworzony w dzierżawie usługi Azure AD B2C. W tej sekcji przedstawiono kroki opcjonalne, które możesz wykonać, aby utworzyć aplikację testu, jeśli jeszcze tego nie zrobiłeś.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
1. Wprowadź nazwę aplikacji, na przykład *testapp1*.
1. Dla **aplikacji sieci Web / interfejs API sieci Web**, wybierz opcję `Yes`, a następnie wprowadź `https://jwt.ms` dla **adres URL odpowiedzi**.
1. Kliknij przycisk **Utwórz**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i przetestować go jednostki uzależnionej ze stron

Należy zaktualizować plik innych firm (RP) jednostki uzależnionej, która inicjuje podróży użytkownika, który został utworzony.

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład, zmień jej nazwę na *SignUpSignInMSA.xml*.
1. Otwórz nowy plik i zaktualizuj wartość **PolicyId** atrybutu dla **elementu TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInMSA`.
1. Zaktualizuj wartość **PublicPolicyUri** o identyfikatorze URI zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_msa`
1. Zaktualizuj wartość **ReferenceId** atrybutu w **DefaultUserJourney** aby jest zgodny z Identyfikatorem podróży użytkownika utworzonego wcześniej (SignUpSignInMSA).
1. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady na liście.
1. Upewnij się, że tej aplikacji usługi Azure AD B2C, który został utworzony w poprzedniej sekcji (lub wykonując wymagań wstępnych, na przykład *webapp1* lub *testapp1*) jest zaznaczona w **wybierz Aplikacja** pola, a następnie przetestuj go, klikając **Uruchom teraz**.
1. Wybierz **Account Microsoft** znajdujący się i zaloguj się.

    Jeśli operacji logowania zakończy się pomyślnie, użytkownik jest przekierowany do `jwt.ms` powoduje wyświetlenie zdekodowany tokenu, podobnie do:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
