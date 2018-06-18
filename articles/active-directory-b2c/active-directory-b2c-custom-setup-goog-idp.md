---
title: Dodaj Google + jako dostawca tożsamości protokołu OAuth2 za pomocą niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przykład przy użyciu usługi Google + jako dostawca tożsamości za pomocą protokołu OAuth2.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3be17ca815910b7472a8760529971bd6c2016792
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709278"
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie Google + funkcję dostawcy tożsamości OAuth2 przy użyciu zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym przewodniku opisano sposób włączenia logowania użytkowników z konta Google + za pośrednictwem [niestandardowych zasad](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

Kroki te obejmują:

1.  Tworzenie aplikacji konto Google +.
2.  Dodawanie klucza aplikacji konto Google + do usługi Azure AD B2C
3.  Dodawanie dostawcy oświadczeń dla zasad
4.  Rejestrowanie Google + konto dostawcy oświadczeń wobec przebieg użytkownika
5.  Przesyłanie zasad do usługi Azure AD B2C dzierżawy i przetestować go

## <a name="create-a-google-account-application"></a>Tworzenie aplikacji konto Google +
Aby użyć Google + jako dostawca tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy do utworzenia aplikacji Google + i dostarczyć prawo parametrów. Możesz zarejestrować Google + aplikacją w tym miejscu: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Przejdź do [konsoli deweloperów Google](https://console.developers.google.com/) i zaloguj się przy użyciu poświadczeń konta Google +.
2.  Kliknij przycisk **Tworzenie projektu**, wprowadź **Nazwa projektu**, a następnie kliknij przycisk **Utwórz**.

3.  Polecenie **menu projekty**.

    ![Google + konto — wybierz projekt](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Polecenie **+** przycisku.

    ![Google + konto — Utwórz nowy projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Wprowadź **Nazwa projektu**, a następnie kliknij przycisk **Utwórz**.

    ![Google + konto — nowy projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Poczekaj, aż projekt jest gotowy i kliknij na **menu projekty**.

    ![Google + konto - Zaczekaj, aż nowego projektu jest gotowe do użycia.](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Kliknij nazwę projektu.

    ![Google + konto — wybierz nowy projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Kliknij przycisk **Menedżer interfejsu API** , a następnie kliknij przycisk **poświadczenia** na lewym pasku nawigacyjnym.
9.  Kliknij przycisk **ekranu zgoda OAuth** u góry.

    ![Google + konto — Ustaw OAuth zgody ekranu](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Wybierz lub określ prawidłowe **adres E-mail**, podaj **nazwa produktu**i kliknij przycisk **zapisać**.

    ![Google + - poświadczeń aplikacji](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Kliknij przycisk **nowe poświadczenia** , a następnie wybierz **identyfikator klienta OAuth**.

    ![Google + - Utwórz nowe poświadczenia aplikacji](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  W obszarze **typu aplikacji**, wybierz pozycję **aplikacji sieci Web**.

    ![Google + — Wybieranie typu aplikacji](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Podaj **nazwa** dla aplikacji, wprowadź `https://login.microsoftonline.com` w **źródeł autoryzowany JavaScript** pola i `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **autoryzowanych przekierowania URI** pola. Zastąp **{dzierżawa}** nazwą Twojej dzierżawy (na przykład contosob2c.onmicrosoft.com). **{Dzierżawa}** wartość jest rozróżniana wielkość liter. Kliknij przycisk **Utwórz**.

    ![Google + - zapewniają autoryzację JavaScript źródeł i Przekierowanie URI](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Potrzebne do skonfigurowania Google + funkcję dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważne poświadczenie zabezpieczeń.

    ![Google + - skopiowanie wartości klucza tajnego identyfikator i klienta klienta](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Dodaj klucz aplikacji konto Google + do usługi Azure AD B2C
Federacja z konta Google + wymaga klucz tajny klienta konta Google + do relacji zaufania usługi Azure AD B2C w imieniu aplikacji. Należy przechowywać klucz tajny aplikacji Google + w dzierżawie usługi Azure AD B2C:  

1.  Przejdź do dzierżawy usługi Azure AD B2C i wybierz **ustawieniami B2C** > **Framework obsługi tożsamości**
2.  Wybierz **zasad kluczy** Aby wyświetlić dostępne w Twojej dzierżawie kluczy.
3.  Kliknij przycisk **+ Dodaj**.
4.  Aby uzyskać **opcje**, użyj **ręcznego**.
5.  Aby uzyskać **nazwa**, użyj `GoogleSecret`.  
    Prefiks `B2C_1A_` mogą być dodawane automatycznie.
6.  W **klucz tajny** wprowadź klucz tajny aplikacji Google z [konsoli deweloperów Google](https://console.developers.google.com/) skopiowany powyżej.
7.  Aby uzyskać **użycie klucza**, użyj **podpisu**.
8.  Kliknij przycisk **Utwórz**
9.  Upewnij się, że utworzono klucz `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Dodawanie dostawcy oświadczeń w zasadach rozszerzenia

Użytkownikom na logowanie się przy użyciu konta Google +, należy określić konto Google + jako dostawcy oświadczeń. Innymi słowy należy określić punkt końcowy, który komunikuje się usługi Azure AD B2C. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj konto Google + jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzeł rozszerzenia pliku zasad:

1.  Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego. Jeśli potrzebujesz edytora XML [spróbuj Visual Studio Code](https://code.visualstudio.com/download), lekkie edytora i platform.
2.  Znajdź `<ClaimsProviders>` sekcji
3.  Dodaj następujący fragment kodu XML w obszarze `ClaimsProviders` elementu i zastępowanie `client_id` wartość przy użyciu usługi Google + konto aplikacji Identyfikatora klienta przed zapisaniem pliku.  

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
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
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
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Zarejestruj Google + konto dostawcy oświadczeń wobec Zarejestruj się lub zaloguj w podróży użytkownika

Dostawca tożsamości nie został skonfigurowany.  Jednak nie jest dostępna w żadnym ekrany konta-konta/logowania. Dodawanie dostawcy tożsamości konta Google + do użytkownika `SignUpOrSignIn` podróży użytkownika. Aby było to możliwe, utworzymy duplikatem istniejącej przebieg użytkownika szablonu.  Następnie dodaj dostawcy tożsamości konta Google +:

>[!NOTE]
>
>Jeśli został skopiowany `<UserJourneys>` elementu z pliku podstawowego zasad do pliku rozszerzenie (TrustFrameworkExtensions.xml), możesz przejść do tej sekcji.

1.  Otwórz plik bazowy tej zasady (na przykład TrustFrameworkBase.xml).
2.  Znajdź `<UserJourneys>` element i skopiuj cała zawartość `<UserJourneys>` węzła.
3.  Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodaj je.
4.  Wklej całą zawartość `<UserJourney>` węzła, który został skopiowany jako element podrzędny `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Wyświetlany przycisk
`<ClaimsProviderSelections>` Element definiuje listę opcje wyboru dostawcy oświadczeń i ich kolejność.  `<ClaimsProviderSelection>` element jest odpowiednikiem przycisku dostawcy tożsamości na stronie konta-konta/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla konta Google + nowy przycisk zostaną wyświetlone po wyładowuje użytkownika na stronie. Aby dodać ten element:

1.  Znajdź `<UserJourney>` węzła, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, które zostały skopiowane.
2.  Zlokalizuj `<OrchestrationStep>` węzła, który zawiera `Order="1"`
3.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Połącz przycisku akcji
Teraz, gdy masz przycisku w miejscu, należy połączyć je z akcją. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z konta Google + otrzymujących token.

1.  Znajdź `<OrchestrationStep>` zawierającą `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Upewnij się, `Id` ma taką samą wartość jak `TargetClaimsExchangeId` w poprzedniej sekcji
> * Upewnij się, `TechnicalProfileReferenceId` ustawiony identyfikator techniczne profilu utworzonego wcześniej (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Przekaż zasady dla Twojej dzierżawy
1.  W [portalu Azure](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2.  Wybierz **Framework obsługi tożsamości**.
3.  Otwórz **wszystkie zasady** bloku.
4.  Wybierz **przekazywać zasady**.
5.  Sprawdź **zastąpić zasady, jeśli istnieje** pole.
6.  **Przekaż** TrustFrameworkExtensions.xml i upewnij się, że nie wystąpi niepowodzenie weryfikacji

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych przy użyciu Uruchom teraz
1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **Framework obsługi tożsamości**.

    >[!NOTE]
    >
    >    **Uruchom teraz** wymaga co najmniej jednej aplikacji można preregistered dla dzierżawcy. 
    >    Aby dowiedzieć się, jak zarejestrować aplikacji, zapoznaj się z usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [Rejestracja aplikacji](active-directory-b2c-app-registration.md) artykułu.


2.  Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej zasady niestandardowe strony (RP), który został przekazany. Wybierz **Uruchom teraz**.
3.  Można będzie zalogować się przy użyciu konta Google +.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Opcjonalnie] Zarejestruj Google + konto dostawcy oświadczeń do edycji profilu użytkownika podróży
Można również dodać dostawcy tożsamości konta Google + do użytkownika `ProfileEdit` podróży użytkownika. Aby było to możliwe, możemy Powtórz ostatnie dwa kroki:

### <a name="display-the-button"></a>Wyświetlany przycisk
1.  Otwórz plik rozszerzenia zasad (na przykład TrustFrameworkExtensions.xml).
2.  Znajdź `<UserJourney>` węzła, który zawiera `Id="ProfileEdit"` w podróży użytkownika, które zostały skopiowane.
3.  Zlokalizuj `<OrchestrationStep>` węzła, który zawiera `Order="1"`
4.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Połącz przycisku akcji
1.  Znajdź `<OrchestrationStep>` zawierającą `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="upload-the-policy-to-your-tenant"></a>Przekaż zasady dla Twojej dzierżawy
1.  W [portalu Azure](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2.  Wybierz **Framework obsługi tożsamości**.
3.  Otwórz **wszystkie zasady** bloku.
4.  Wybierz **przekazywać zasady**.
5.  Sprawdź **zastąpić zasady, jeśli istnieje** pole.
6.  **Przekaż** TrustFrameworkExtensions.xml i upewnij się, że nie wystąpi niepowodzenie weryfikacji.

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testowanie zasad niestandardowych edycji profilu przy użyciu Uruchom teraz

1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **Framework obsługi tożsamości**.
2.  Otwórz **B2C_1A_ProfileEdit**, jednostki uzależnionej zasady niestandardowe strony (RP), który został przekazany. Wybierz **Uruchom teraz**.
3.  Można będzie zalogować się przy użyciu konta Google +.

## <a name="download-the-complete-policy-files"></a>Pobierz pliki pełną zasad
Opcjonalnie: Zaleca się tworzenie scenariusz przy użyciu własnych zasad niestandardowych, które przeprowadzenie pliki po zakończeniu wprowadzenie do zasad niestandardowych, zamiast korzystać z tych przykładowych plików.  [Przykładowe pliki zasad dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
