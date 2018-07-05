---
title: Dodawanie usługi Twitter jako dostawcy tożsamości OAuth1 za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Użyj usługi Twitter jako dostawcy tożsamości za pomocą protokołu OAuth1.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 786f0dfd0cf3cf2e9ab0d16e26811fabd6bfc17c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440958"
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie usługi Twitter jako dostawcy tożsamości OAuth1 za pomocą zasad niestandardowych
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowania dla użytkowników konta w serwisie Twitter przy użyciu [zasady niestandardowe](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

## <a name="step-1-create-a-twitter-account-application"></a>Krok 1: Tworzenie aplikacji konta usługi Twitter
Aby użyć usługi Twitter jako dostawcy tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację usługi Twitter i dostarczyć odpowiednie parametry. Możesz zarejestrować aplikację usługi Twitter, przechodząc do [stronę rejestracji w usłudze Twitter](https://twitter.com/signup).

1. Przejdź do [deweloperów w usłudze Twitter](https://apps.twitter.com/) witryny sieci Web, zaloguj się przy użyciu poświadczeń konta usługi Twitter, a następnie wybierz **Utwórz nową aplikację**.

    ![Konto w usłudze Twitter — Tworzenie nowej aplikacji](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. W **tworzenia aplikacji** okna, wykonaj następujące czynności:
 
    a. Typ **nazwa** i **opis** dla nowej aplikacji. 

    b. W **witryny sieci Web** pole, Wklej **https://login.microsoftonline.com**. 

    c. 4. Aby uzyskać **adresów URL wywołania zwrotnego**, wprowadź `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`. Upewnij się zastąpić **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com) i **{policyId}** za pomocą identyfikatora zasad (na przykład b2c_1_policy).  **Wywołanie zwrotne adres URL musi być zapisana w same małe litery.** Należy dodać adres URL wywołania zwrotnego dla wszystkich zasad, które używają logowania usługi Twitter. Upewnij się, że używasz `b2clogin.com` zamiast ` login.microsoftonline.com` Jeśli używasz go w aplikacji.

    d. W dolnej części strony, przeczytaj i zaakceptuj warunki, a następnie wybierz **tworzenie aplikacji usługi Twitter**.

    ![Konto w usłudze Twitter — Dodawanie nowej aplikacji](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. W **pokaz B2C** wybierz **ustawienia**, wybierz opcję **zezwalasz tej aplikacji, które ma być używany do logowania się przy użyciu usługi Twitter** pole wyboru, a następnie wybierz pozycję **aktualizacji Ustawienia**.

4. Wybierz **klucze i tokeny dostępu**i zanotuj **konsumenta (klucz interfejsu API)** i **klucz tajny klienta (klucz tajny interfejsu API)** wartości.

    ![Konta w serwisie Twitter — Ustawianie właściwości aplikacji](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń. Udostępnij ten wpis tajny z dowolnymi osobami lub nie rozpowszechnienie go z aplikacją.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Krok 2: Dodaj klucz aplikacji konta usługi Twitter do usługi Azure AD B2C
Federacja z konta usługi Twitter wymaga klucz tajny klienta konta w serwisie Twitter zaufania usługi Azure AD B2C w imieniu aplikacji. Aby przechowywać klucz tajny klienta aplikacji usługi Twitter w dzierżawie usługi Azure AD B2C, wykonaj następujące czynności: 

1. W ramach dzierżawy usługi Azure AD B2C wybierz **ustawieniami B2C** > **struktura środowiska tożsamości**.

2. Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie, wybierz **klucze zasad**.

3. Wybierz pozycję **Dodaj**.

4. W **opcje** wybierz opcję **ręczne**.

5. W **nazwa** wybierz opcję **TwitterSecret**.  
    Prefiks *B2C_1A_* mogą być dodawane automatycznie.

6. W **klucz tajny** wprowadź klucz tajny aplikacji firmy Microsoft z [portalu rejestracji aplikacji](https://apps.dev.microsoft.com).

7. Aby uzyskać **użycie klucza**, użyj **szyfrowania**.

8. Wybierz pozycję **Utwórz**.

9. Upewnij się, że utworzono `B2C_1A_TwitterSecret` klucza.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Krok 3: Dodawanie dostawcy oświadczeń w zasadach rozszerzenia

Jeśli chcesz, aby użytkownikom na logowanie za pomocą konta w serwisie Twitter, należy zdefiniować Twitter jako dostawcy oświadczeń. Innymi słowy należy określić punkty końcowe, które komunikuje się usługi Azure AD B2C. Punktów końcowych, które zawierają zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj Twitter jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzła w pliku zasad rozszerzenia:

1. W katalogu roboczym otwórz *TrustFrameworkExtensions.xml* rozszerzenie pliku zasad. 

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

4. Zastąp *client_id*"wartością klucz klienta usługi Twitter konta aplikacji.

5. Zapisz plik.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Krok 4: Zarejestruj dostawcę oświadczeń konta serwisu Twitter do Twojej podróży użytkownika rejestracji lub logowania
Po skonfigurowaniu dostawcy tożsamości. Jednak nie jest jeszcze dostępna w żadnym z tworzenia konta lub logowania systemu windows. Teraz należy dodać dostawcę tożsamości konta usługi Twitter do użytkownika `SignUpOrSignIn` podróży użytkownika.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1: Utwórz kopię podróży użytkownika
Aby udostępnić podróży użytkownika, możesz utworzenie duplikatu istniejącego szablonu podróży użytkownika, a następnie dodaj dostawcy tożsamości w usłudze Twitter:

>[!NOTE]
>Jeśli został skopiowany `<UserJourneys>` elementu z pliku podstawowego zasad do *TrustFrameworkExtensions.xml* plik rozszerzenia, możesz przejść do następnej sekcji.

1. Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).

2. Wyszukaj `<UserJourneys>` elementu, zaznacz całą zawartość `<UserJourney>` węzeł, a następnie wybierz **Wytnij** Aby przenieść zaznaczony tekst do Schowka.

3. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml), a następnie wyszukaj `<UserJourneys>` elementu. Jeśli element nie istnieje, należy go dodać.

4. Wklej całą zawartość `<UserJourney>` węzła, który jest przenoszony do Schowka w kroku 2, do `<UserJourneys>` elementu.

### <a name="step-42-display-the-button"></a>Krok 4.2: Wyświetlenie "button"
`<ClaimsProviderSelections>` Element definiuje listę opcji do wyboru dostawcy oświadczeń i ich kolejność. `<ClaimsProviderSelection>` Węzeł jest odpowiednikiem przycisk dostawcy tożsamości, na stronie tworzenia konta lub logowania. Jeśli dodasz `<ClaimsProviderSelection>` węzła dla konta w serwisie Twitter, nowy przycisk jest wyświetlane, gdy użytkownik wyładowuje na stronie. Aby dodać ten element, wykonaj następujące czynności:

1. Wyszukaj `<UserJourney>` węzeł, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, który został skopiowany.

2. Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"`.

3. W `<ClaimsProviderSelections>` elementu, Dodaj następujący fragment kodu XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3: Link przycisk, aby akcję
Teraz, gdy przycisk w miejscu, należy go połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z konta w serwisie Twitter otrzymujących token. Łącze przycisku do akcji, łącząc profilu technicznego dla dostawcy oświadczeń konta Twitter:

1. Wyszukaj `<OrchestrationStep>` węzeł, który zawiera `Order="2"` w `<UserJourney>` węzła.
2. W `<ClaimsExchanges>` elementu, Dodaj następujący fragment kodu XML:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Upewnij się, że `Id` ma taką samą wartość jak w przypadku `TargetClaimsExchangeId` w poprzedniej sekcji.
    >* Upewnij się, że `TechnicalProfileReferenceId` identyfikator ustawiono profil techniczny utworzonego wcześniej (Twitter — OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5: Przekaż zasady dla Twojej dzierżawy
1. W [witryny Azure portal](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz pozycję **usługi Azure AD B2C**.

2. Wybierz **struktura środowiska tożsamości**.

3. Wybierz **wszystkie zasady**.

4. Wybierz **przekazywać zasady**.

5. Wybierz **Zastąp zasady Jeśli istnieje** pole wyboru.

6. Przekaż *TrustFrameworkBase.xml* i *TrustFrameworkExtensions.xml* plików i upewnij się, że przekazują sprawdzania poprawności.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6: Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz

1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie wybierz pozycję **struktura środowiska tożsamości**.

    >[!NOTE]
    >Uruchom teraz wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [rejestracji aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strona (RP) zasad niestandardowych, które przekazane, a następnie wybierz **Uruchom teraz**.  
    Teraz można się zalogować za pomocą konta w serwisie Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Krok 7: Rejestr (opcjonalnie) konta w serwisie Twitter oświadczeń dostawcy podróży użytkownika edytowania profilu
Można także dodać dostawcę tożsamości konta usługi Twitter do usługi `ProfileEdit` podróży użytkownika. Aby ułatwić podróży dostępne, powtórz "krok 4." Tym razem wybierz pozycję `<UserJourney>` węzeł, który zawiera `Id="ProfileEdit"`. Zapisz, przekazywanie i testowanie zasad.


## <a name="optional-download-the-complete-policy-files"></a>(Opcjonalnie) Pobierz pliki pełną zasad
Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, firma Microsoft zaleca tworzenie scenariusza za pomocą plików zasad niestandardowych. Dla Twojej informacji udostępniliśmy [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
