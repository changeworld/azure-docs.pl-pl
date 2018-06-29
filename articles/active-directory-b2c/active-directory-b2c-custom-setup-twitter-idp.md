---
title: Dodaj usługi Twitter jako dostawca tożsamości OAuth1 za pomocą niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Użyj usługi Twitter jako dostawca tożsamości za pomocą protokołu OAuth1.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6b09bb295d889255dada0cebbb9ded2379d95d23
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34710240"
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie usługi Twitter jako dostawca tożsamości OAuth1 za pomocą zasad niestandardowych
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono sposób włączyć logowanie użytkowników korzystających z konta w usłudze Twitter, za pomocą [niestandardowych zasad](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

## <a name="step-1-create-a-twitter-account-application"></a>Krok 1: Tworzenie aplikacji konta usługi Twitter
Aby użyć usługi Twitter jako dostawca tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację usługi Twitter i dostarczyć prawo parametrów. Można zarejestrować aplikacji Twitter, przechodząc do [stronę tworzenia konta w serwisie Twitter](https://twitter.com/signup).

1. Przejdź do [Twitter deweloperzy](https://apps.twitter.com/) witryny sieci Web, zaloguj się przy użyciu poświadczeń konta usługi Twitter, a następnie wybierz **Utwórz nową aplikację**.

    ![Konto w usłudze Twitter — Utwórz nową aplikację](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. W **tworzenie aplikacji** okna, wykonaj następujące czynności:
 
    a. Typ **nazwa** i **opis** dla nowej aplikacji. 

    b. W **witryny sieci Web** Wklej **https://login.microsoftonline.com**. 

    c. W **wywołania zwrotnego adresu URL** Wklej **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Zastąp {*dzierżawy*} nazwą Twojej dzierżawy (na przykład contosob2c.onmicrosoft.com). Upewnij się, że używasz schematu HTTPS. 

    d. W dolnej części strony, przeczytaj i zaakceptuj postanowienia, a następnie wybierz **tworzenie aplikacji Twitter**.

    ![Konto w usłudze Twitter — Dodawanie nowej aplikacji](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. W **pokaz B2C** wybierz **ustawienia**, wybierz pozycję **zezwolić tej aplikacji należy się zalogować za pomocą usługi Twitter** pole wyboru, a następnie wybierz **aktualizacji Ustawienia**.

4. Wybierz **kluczy i tokenów dostępu**i zanotuj **konsumenta (klucz interfejsu API)** i **klucz tajny klienta (klucz tajny interfejsu API)** wartości.

    ![Konta w usłudze Twitter — Ustawianie właściwości aplikacji](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >Klucz tajny klienta jest ważne poświadczenie zabezpieczeń. Nie udostępniaj nikomu ten klucz tajny i rozpowszechnienie go z aplikacją.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Krok 2: Dodaj klucz Twitter konta aplikacji do usługi Azure AD B2C
Federacja z kontami usługi Twitter wymaga klucz tajny klienta konta w usłudze Twitter do relacji zaufania usługi Azure AD B2C w imieniu aplikacji. Aby przechowywać klucz tajny klienta aplikacji Twitter w dzierżawie usługi Azure AD B2C, wykonaj następujące czynności: 

1. W dzierżawie usługi Azure AD B2C, wybierz **ustawieniami B2C** > **Framework obsługi tożsamości**.

2. Zaznacz, aby wyświetlić klucze, które są dostępne w Twojej dzierżawie **klucze zasad**.

3. Wybierz pozycję **Dodaj**.

4. W **opcje** wybierz opcję **ręcznego**.

5. W **nazwa** wybierz opcję **TwitterSecret**.  
    Prefiks *B2C_1A_* mogą być dodawane automatycznie.

6. W **klucz tajny** wprowadź klucz tajny aplikacji firmy Microsoft z [portalu rejestracji aplikacji](https://apps.dev.microsoft.com).

7. Aby uzyskać **użycie klucza**, użyj **szyfrowania**.

8. Wybierz pozycję **Utwórz**.

9. Upewnij się, że utworzono `B2C_1A_TwitterSecret` klucza.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Krok 3: Dodawanie dostawcy oświadczeń w zasadach rozszerzenia

Jeśli chcesz, aby użytkownikom na logowanie się przy użyciu konta w usłudze Twitter, należy zdefiniować Twitter jako dostawcy oświadczeń. Innymi słowy należy określić punkty końcowe, które komunikuje usługi Azure AD B2C. Punkty końcowe udostępniają zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj Twitter jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzeł rozszerzenia pliku zasad:

1. W katalogu roboczym, otwórz *TrustFrameworkExtensions.xml* rozszerzenia pliku zasad. 

2. Wyszukaj `<ClaimsProviders>` sekcji.

3. W `<ClaimsProviders>` węzła, Dodaj następujący fragment kodu XML:  

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
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
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

4. Zastąp *client_id*"wartości o klucz klienta usługi Twitter konto aplikacji.

5. Zapisz plik.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Krok 4: Zarejestruj podróży rejestracji i logowania użytkownika przez dostawcę oświadczeń konta usługi Twitter
Po skonfigurowaniu dostawcy tożsamości. Jednak nie jest jeszcze dostępna w żadnym rejestracji i logowania systemu Windows. Teraz należy dodać dostawcy tożsamości konta usługi Twitter do użytkownika `SignUpOrSignIn` podróży użytkownika.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1: Wykonaj kopię przebieg użytkownika
Aby udostępnić przebieg użytkownika, Utwórz kopię istniejącego szablonu przebieg użytkownika, a następnie dodaj dostawcy tożsamości usługi Twitter:

>[!NOTE]
>Jeśli został skopiowany `<UserJourneys>` element z plikiem podstawowym zasad do *TrustFrameworkExtensions.xml* rozszerzenie pliku, możesz przejść do następnej sekcji.

1. Otwórz plik bazowy tej zasady (na przykład TrustFrameworkBase.xml).

2. Wyszukaj `<UserJourneys>` element, wybierz całą zawartość `<UserJourney>` węzeł, a następnie wybierz **Wytnij** można przenieść do zaznaczonego tekstu do Schowka.

3. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml), a następnie wyszukaj `<UserJourneys>` elementu. Jeśli element nie istnieje, należy go dodać.

4. Wklej całą zawartość `<UserJourney>` węzła, który przenoszony do Schowka w kroku 2, do `<UserJourneys>` elementu.

### <a name="step-42-display-the-button"></a>Krok 4.2. "Button" Wyświetl
`<ClaimsProviderSelections>` Element definiuje listę opcje wyboru dostawcy oświadczeń i ich kolejność. `<ClaimsProviderSelection>` Węzeł jest odpowiednikiem przycisk dostawcy tożsamości na stronie tworzenia konta lub logowania. Jeśli dodasz `<ClaimsProviderSelection>` konta w usłudze Twitter, przycisk Nowy węzeł jest wyświetlane, gdy użytkownik wyładowuje na stronie. Aby dodać ten element, wykonaj następujące czynności:

1. Wyszukaj `<UserJourney>` węzła, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, które zostały skopiowane.

2. Zlokalizuj `<OrchestrationStep>` węzła, który zawiera `Order="1"`.

3. W `<ClaimsProviderSelections>` elementu, Dodaj następujący fragment kodu XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3: Połącz przycisku akcji
Teraz, gdy masz przycisku w miejscu, należy go powiązać akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z konta w usłudze Twitter otrzymujących token. Połączyć przycisku akcji przez łączenie techniczne profilu dla dostawcy oświadczeń usługi Twitter konta:

1. Wyszukaj `<OrchestrationStep>` węzła, który zawiera `Order="2"` w `<UserJourney>` węzła.
2. W `<ClaimsExchanges>` elementu, Dodaj następujący fragment kodu XML:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Upewnij się, że `Id` ma taką samą wartość jak `TargetClaimsExchangeId` w poprzedniej sekcji.
    >* Upewnij się, że `TechnicalProfileReferenceId` identyfikator jest ustawiony na techniczne profilu utworzonego wcześniej (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5: Przekaż zasady dla Twojej dzierżawy
1. W [portalu Azure](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz **usługi Azure AD B2C**.

2. Wybierz **Framework obsługi tożsamości**.

3. Wybierz **wszystkich zasad**.

4. Wybierz **przekazywać zasady**.

5. Wybierz **zastąpić zasady, jeśli istnieje** pole wyboru.

6. Przekaż *TrustFrameworkBase.xml* i *TrustFrameworkExtensions.xml* plików i upewnij się, że przeszedł pomyślnie weryfikacji.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6: Test zasady niestandardowe przy użyciu Uruchom teraz

1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie wybierz **Framework obsługi tożsamości**.

    >[!NOTE]
    >Uruchom teraz wymaga co najmniej jednej aplikacji można preregistered dla dzierżawcy. Aby dowiedzieć się, jak zarejestrować aplikacji, zapoznaj się z usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [Rejestracja aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strony (RP) zasad niestandardowych, które można przekazać, a następnie wybierz **Uruchom teraz**.  
    Teraz można się zalogować przy użyciu konta usługi Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Krok 7: Dostawcy do edycji profilu użytkownika podróży oświadczeń rejestru (opcjonalnie) konta w usłudze Twitter
Można także dodać dostawcy tożsamości konta usługi Twitter do Twojej `ProfileEdit` podróży użytkownika. Aby użytkownik podróży dostępne Powtórz "krok 4." Teraz, wybierz opcję `<UserJourney>` węzła, który zawiera `Id="ProfileEdit"`. Zapisz, przekazywanie i przetestowania zasad.


## <a name="optional-download-the-complete-policy-files"></a>(Opcjonalnie) Pobierz pliki pełną zasad
Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, zaleca się tworzenia scenariusz przy użyciu plików zasady niestandardowe. Użytkownikowi, firma Microsoft umieściła [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
