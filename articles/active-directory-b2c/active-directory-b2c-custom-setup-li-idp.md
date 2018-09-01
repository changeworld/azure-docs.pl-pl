---
title: Dodawanie usługi LinkedIn jako dostawcy tożsamości OAuth2 za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Instrukcje artykuł o konfigurowaniu aplikacji LinkedIn przy użyciu protokołu OAuth2 i zasady niestandardowe.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 58a595c697b6e1a70089a6683493835e0d3a9780
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344322"
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie usługi LinkedIn jako dostawcy tożsamości za pomocą zasad niestandardowych
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowania dla użytkowników konta LinkedIn, używając [zasady niestandardowe](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

## <a name="step-1-create-a-linkedin-account-application"></a>Krok 1: Tworzenie aplikacji konto usługi LinkedIn
Aby użyć usługi LinkedIn jako dostawcy tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację usługi LinkedIn i dostarczyć odpowiednie parametry. Aplikacja usługi LinkedIn można zarejestrować, przechodząc do [stronie tworzenia konta usługi LinkedIn](https://www.linkedin.com/start/join).

1. Przejdź do [zarządzania aplikacjami usługi LinkedIn](https://www.linkedin.com/secure/developer?newapp=) witryny sieci Web, zaloguj się przy użyciu poświadczeń konta usługi LinkedIn, a następnie wybierz **tworzenia aplikacji**.

    ![LinkedIn konta — tworzenie aplikacji](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. Na **Utwórz nową aplikację** wykonaj następujące czynności:

    a. Typ usługi **nazwa firmy**, opisowe **nazwa** dla firmy i **opis** nowej aplikacji.

    b. Przekaż swoje **Logo aplikacji**.

    c. Wybierz **użycia aplikacji**.

    d. W **adres URL witryny internetowej** pole, Wklej **https://{tenant}.b2clogin.com**.  Gdzie {*dzierżawy*} jest nazwa dzierżawy (na przykład contoso.b2clogin.com).

    e. Typ usługi **firmowa Poczta E-mail** adres i **Telefon służbowy** numer.

    f. W dolnej części strony, przeczytaj i zaakceptuj warunki użytkowania, a następnie wybierz **przesyłania**.

    ![LinkedIn konta — Konfigurowanie właściwości aplikacji](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Wybierz **uwierzytelniania**, a następnie zanotuj **identyfikator klienta** i **klucz tajny klienta** wartości.

4. W **autoryzacji adresów URL przekierowania** pole, Wklej **https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Zastąp {*dzierżawy*} nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com). Upewnij się, że będą używać schematu HTTPS. 

    ![Konto usługi LinkedIn — zestaw uprawnień przekierowania adresów URL](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń. Udostępnij ten wpis tajny z dowolnymi osobami lub nie rozpowszechnienie go z aplikacją.

5. Wybierz pozycję **Dodaj**.

6. Wybierz **ustawienia**, zmienić **stan aplikacji** do **Live**, a następnie wybierz pozycję **aktualizacji**.

    ![Konto usługi LinkedIn — Ustaw stan aplikacji](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Krok 2: Dodaj klucz usługi LinkedIn aplikacji do usługi Azure AD B2C
Federacja z konta usługi LinkedIn wymaga klucz tajny klienta konta LinkedIn relacji zaufania usługi Azure AD B2C w imieniu aplikacji. Aby przechowywać klucz tajny aplikacji usługi LinkedIn w ramach dzierżawy usługi Azure AD B2C, wykonaj następujące czynności:  

1. W ramach dzierżawy usługi Azure AD B2C wybierz **ustawieniami B2C** > **struktura środowiska tożsamości**.

2. Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie, wybierz **klucze zasad**.

3. Wybierz pozycję **Dodaj**.

4. W **opcje** wybierz opcję **przekazywanie**.

5. W **nazwa** wpisz **B2cRestClientCertificate**.  
    Prefiks *B2C_1A_* mogą być dodawane automatycznie.

6. W **klucz tajny** wprowadź klucz tajny aplikacji usługi LinkedIn z [portalu rejestracji aplikacji](https://apps.dev.microsoft.com).

7. Aby uzyskać **użycie klucza**, wybierz opcję **szyfrowania**.

8. Wybierz pozycję **Utwórz**. 

9. Upewnij się, że utworzono `B2C_1A_LinkedInSecret`klucza.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Krok 3: Dodawanie dostawcy oświadczeń w zasadach rozszerzenia
Jeśli chcesz, aby użytkownikom na logowanie przy użyciu swojego konta usługi LinkedIn, należy zdefiniować LinkedIn jako dostawcy oświadczeń. Innymi słowy należy określić punkty końcowe, które komunikuje się usługi Azure AD B2C. Punktów końcowych, które zawierają zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj LinkedIn jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzła w pliku zasad rozszerzenia:

1. W katalogu roboczym otwórz *TrustFrameworkExtensions.xml* rozszerzenie pliku zasad. 

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

4. Zastąp *client_id* wartość przy użyciu swojego identyfikatora klienta aplikacji usługi LinkedIn.

5. Zapisz plik.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Krok 4: Zarejestruj dostawcę oświadczeń konta LinkedIn
Po skonfigurowaniu dostawcy tożsamości. Jednak nie jest jeszcze dostępna w żadnym z tworzenia konta lub logowania systemu windows. Teraz należy dodać dostawcę tożsamości konta usługi LinkedIn do użytkownika `SignUpOrSignIn` podróży użytkownika.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1: Utwórz kopię podróży użytkownika
Aby udostępnić podróży użytkownika, możesz utworzenie duplikatu istniejącego szablonu podróży użytkownika, a następnie dodaj LinkedIn dostawcy tożsamości:

>[!NOTE]
>Jeśli został skopiowany `<UserJourneys>` elementu z pliku podstawowego zasad do *TrustFrameworkExtensions.xml* plik rozszerzenia, możesz pominąć tę sekcję.

1. Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).

2. Wyszukaj `<UserJourneys>` elementu, zaznacz całą zawartość `<UserJourney>` węzeł, a następnie wybierz **Wytnij** Aby przenieść zaznaczony tekst do Schowka.

3. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i wyszukaj `<UserJourneys>` elementu. Jeśli element nie istnieje, należy go dodać.

4. Wklej całą zawartość `<UserJourney>` węzła, który jest przenoszony do Schowka w kroku 2, do `<UserJourneys>` elementu.

### <a name="step-42-display-the-button"></a>Krok 4.2: Wyświetlenie "button"
`<ClaimsProviderSelections>` Element definiuje listę opcji do wyboru dostawcy oświadczeń i ich kolejność. `<ClaimsProviderSelection>` Węzeł jest odpowiednikiem przycisk dostawcy tożsamości, na stronie tworzenia konta lub logowania. Jeśli dodasz `<ClaimsProviderSelection>` węzła dla konta usługi LinkedIn, nowy przycisk jest wyświetlane, gdy użytkownik wyładowuje na stronie. Aby dodać ten element, wykonaj następujące czynności:

1. Wyszukaj `<UserJourney>` węzeł, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, który został skopiowany.

2. Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"`.

3. W `<ClaimsProviderSelections>` elementu, Dodaj następujący fragment kodu XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3: Link przycisk, aby akcję
Teraz, gdy przycisk w miejscu, należy go połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się za pomocą konta LinkedIn i odebrać token. Łącze przycisku do akcji, łącząc profilu technicznego dla dostawcy oświadczeń konta LinkedIn:

1. Wyszukaj `<OrchestrationStep>` węzeł, który zawiera `Order="2"` w `<UserJourney>` węzła.

2. W `<ClaimsExchanges>` elementu, Dodaj następujący fragment kodu XML:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Upewnij się, że `Id` ma taką samą wartość jak w przypadku `TargetClaimsExchangeId` w poprzedniej sekcji.
    >* Upewnij się, że `TechnicalProfileReferenceId` identyfikator ustawiono profil techniczny utworzonego wcześniej (LinkedIn-OAuth).

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
    Teraz można się zalogować przy użyciu konta LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Krok 7: (Opcjonalnie) Zarejestruj konto usługi LinkedIn oświadczeń dostawcy podróży użytkownika edytowania profilu
Można także dodać dostawcy tożsamości konta usługi LinkedIn w celu usługi `ProfileEdit` podróży użytkownika. Aby ułatwić podróży dostępne, powtórz "krok 4." Tym razem wybierz pozycję `<UserJourney>` węzeł, który zawiera `Id="ProfileEdit"`. Zapisz, przekazywanie i testowanie zasad.

## <a name="optional-download-the-complete-policy-files"></a>(Opcjonalnie) Pobierz pliki pełną zasad
Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, firma Microsoft zaleca tworzenie scenariusza za pomocą plików zasad niestandardowych. Dla Twojej informacji udostępniliśmy [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
