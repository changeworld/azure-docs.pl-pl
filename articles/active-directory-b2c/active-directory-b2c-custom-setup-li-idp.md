---
title: Dodaj LinkedIn jako dostawca tożsamości protokołu OAuth2 za pomocą niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Artykule o konfigurowaniu aplikacji LinkedIn przy użyciu protokołu OAuth2 i zasady niestandardowe.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 889264dc06d1a6f10713ff5fcd7ff644c113bb45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752320"
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie LinkedIn funkcję dostawcy tożsamości za pomocą zasad niestandardowych
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono sposób włączenia logowania dla użytkowników, konta LinkedIn przy użyciu [niestandardowych zasad](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

## <a name="step-1-create-a-linkedin-account-application"></a>Krok 1: Tworzenie aplikacji LinkedIn konta
Aby użyć LinkedIn jako dostawca tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację LinkedIn i dostarczyć prawo parametrów. Można zarejestrować aplikacji LinkedIn, przechodząc do [stronę tworzenia konta LinkedIn](https://www.linkedin.com/start/join).

1. Przejdź do [Zarządzanie aplikacjami LinkedIn](https://www.linkedin.com/secure/developer?newapp=) witryny sieci Web, zaloguj się przy użyciu poświadczeń konta LinkedIn, a następnie wybierz **tworzenie aplikacji**.

    ![LinkedIn konta — tworzenie aplikacji](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. Na **Utwórz nową aplikację** strony, wykonaj następujące czynności:

    a. Typ użytkownika **nazwa firmy**, opisową **nazwa** firmy i **opis** nowej aplikacji.

    b. Przekazywanie z **Logo aplikacji**.

    c. Wybierz **stosowania**.

    d. W **adres URL witryny sieci Web** Wklej **https://login.microsoftonline.com**.

    e. Typ użytkownika **służbowy adres E-mail** adres i **Telefon służbowy** numer.

    f. W dolnej części strony, przeczytaj i zaakceptuj warunki użytkowania, a następnie wybierz **przesyłania**.

    ![LinkedIn konta — Konfigurowanie właściwości aplikacji](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Wybierz **uwierzytelniania**, a następnie zanotuj **identyfikator klienta** i **klucz tajny klienta** wartości.

4. W **autoryzacji adresów URL przekierowań** Wklej **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Zastąp {*dzierżawy*} nazwą Twojej dzierżawy (na przykład contosob2c.onmicrosoft.com). Upewnij się, że używasz schematu HTTPS. 

    ![Konto LinkedIn — zestaw uprawnień przekierowania adresów URL](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >Klucz tajny klienta jest ważne poświadczenie zabezpieczeń. Nie udostępniaj nikomu ten klucz tajny i rozpowszechnienie go z aplikacją.

5. Wybierz pozycję **Dodaj**.

6. Wybierz **ustawienia**, zmień **stanu aplikacji** do **Live**, a następnie wybierz **aktualizacji**.

    ![Konto LinkedIn — Ustaw stan aplikacji](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Krok 2: Dodaj klucz LinkedIn aplikacji do usługi Azure AD B2C
Federacja z kontami LinkedIn wymaga klucz tajny klienta konta LinkedIn do relacji zaufania usługi Azure AD B2C w imieniu aplikacji. Aby przechowywać klucz tajny aplikacji LinkedIn w dzierżawie usługi Azure AD B2C, wykonaj następujące czynności:  

1. W dzierżawie usługi Azure AD B2C, wybierz **ustawieniami B2C** > **Framework obsługi tożsamości**.

2. Zaznacz, aby wyświetlić klucze, które są dostępne w Twojej dzierżawie **klucze zasad**.

3. Wybierz pozycję **Dodaj**.

4. W **opcje** wybierz opcję **przekazać**.

5. W **nazwa** wpisz **B2cRestClientCertificate**.  
    Prefiks *B2C_1A_* mogą być dodawane automatycznie.

6. W **klucz tajny** wprowadź klucz tajny aplikacji LinkedIn z [portalu rejestracji aplikacji](https://apps.dev.microsoft.com).

7. Aby uzyskać **użycie klucza**, wybierz pozycję **szyfrowania**.

8. Wybierz pozycję **Utwórz**. 

9. Upewnij się, że utworzono `B2C_1A_LinkedInSecret`klucza.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Krok 3: Dodawanie dostawcy oświadczeń w zasadach rozszerzenia
Jeśli chcesz, aby użytkownikom na logowanie się przy użyciu swojego konta LinkedIn, należy zdefiniować LinkedIn jako dostawcy oświadczeń. Innymi słowy należy określić punkty końcowe, które komunikuje usługi Azure AD B2C. Punkty końcowe udostępniają zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj LinkedIn jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzeł rozszerzenia pliku zasad:

1. W katalogu roboczym, otwórz *TrustFrameworkExtensions.xml* rozszerzenia pliku zasad. 

2. Wyszukaj `<ClaimsProviders>` elementu.

3. W `<ClaimsProviders>` elementu, Dodaj następujący fragment kodu XML: 

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4. Zastąp *client_id* wartości z identyfikatorem LinkedIn aplikacji klienta.

5. Zapisz plik.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Krok 4: Zarejestruj dostawcę oświadczeń LinkedIn konta
Po skonfigurowaniu dostawcy tożsamości. Jednak nie jest jeszcze dostępna w żadnym rejestracji i logowania systemu Windows. Teraz należy dodać LinkedIn dostawcy tożsamości konta do użytkownika `SignUpOrSignIn` podróży użytkownika.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1: Wykonaj kopię przebieg użytkownika
Aby udostępnić przebieg użytkownika, Utwórz kopię istniejącego szablonu przebieg użytkownika, a następnie dodaj LinkedIn dostawcy tożsamości:

>[!NOTE]
>Jeśli został skopiowany `<UserJourneys>` element z plikiem podstawowym zasad do *TrustFrameworkExtensions.xml* rozszerzenia plików, można pominąć tę sekcję.

1. Otwórz plik bazowy tej zasady (na przykład TrustFrameworkBase.xml).

2. Wyszukaj `<UserJourneys>` element, wybierz całą zawartość `<UserJourney>` węzeł, a następnie wybierz **Wytnij** można przenieść do zaznaczonego tekstu do Schowka.

3. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i wyszukaj `<UserJourneys>` elementu. Jeśli element nie istnieje, należy go dodać.

4. Wklej całą zawartość `<UserJourney>` węzła, który przenoszony do Schowka w kroku 2, do `<UserJourneys>` elementu.

### <a name="step-42-display-the-button"></a>Krok 4.2. "Button" Wyświetl
`<ClaimsProviderSelections>` Element definiuje listę opcje wyboru dostawcy oświadczeń i ich kolejność. `<ClaimsProviderSelection>` Węzeł jest odpowiednikiem przycisk dostawcy tożsamości na stronie tworzenia konta lub logowania. Jeśli dodasz `<ClaimsProviderSelection>` konto LinkedIn przycisk Nowy węzeł jest wyświetlane, gdy użytkownik wyładowuje na stronie. Aby dodać ten element, wykonaj następujące czynności:

1. Wyszukaj `<UserJourney>` węzła, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, które zostały skopiowane.

2. Zlokalizuj `<OrchestrationStep>` węzła, który zawiera `Order="1"`.

3. W `<ClaimsProviderSelections>` elementu, Dodaj następujący fragment kodu XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3: Połącz przycisku akcji
Teraz, gdy masz przycisku w miejscu, należy go powiązać akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z kontem LinkedIn otrzymujących token. Połączyć przycisku akcji przez łączenie techniczne profilu dla dostawcy oświadczeń LinkedIn konta:

1. Wyszukaj `<OrchestrationStep>` węzła, który zawiera `Order="2"` w `<UserJourney>` węzła.

2. W `<ClaimsExchanges>` elementu, Dodaj następujący fragment kodu XML:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Upewnij się, że `Id` ma taką samą wartość jak `TargetClaimsExchangeId` w poprzedniej sekcji.
    >* Upewnij się, że `TechnicalProfileReferenceId` identyfikator jest ustawiony na techniczne profilu utworzonego wcześniej (LinkedIn-OAuth).

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
    Teraz można się zalogować przy użyciu konta LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Krok 7: Rejestr (opcjonalnie) konta LinkedIn oświadczeń dostawcy podróży edycji profilu użytkownika
Można także dodać dostawcy tożsamości konta LinkedIn do Twojej `ProfileEdit` podróży użytkownika. Aby użytkownik podróży dostępne Powtórz "krok 4." Teraz, wybierz opcję `<UserJourney>` węzła, który zawiera `Id="ProfileEdit"`. Zapisz, przekazywanie i przetestowania zasad.

## <a name="optional-download-the-complete-policy-files"></a>(Opcjonalnie) Pobierz pliki pełną zasad
Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, zaleca się tworzenia scenariusz przy użyciu plików zasady niestandardowe. Użytkownikowi, firma Microsoft umieściła [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
