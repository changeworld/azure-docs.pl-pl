---
title: Dodaj Google + jako dostawcy tożsamości OAuth2 za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przykładowe użycie Google + jako dostawcy tożsamości przy użyciu protokołu OAuth2.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f076a906ba38e6c8e8c9530baba1607553b41ea6
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338332"
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie Google + jako dostawcy tożsamości OAuth2 za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten przewodnik pokazuje, jak włączyć logowanie dla użytkowników z konta Google + za pośrednictwem [zasady niestandardowe](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

Kroki te obejmują:

1.  Tworzenie aplikacji konto Google +.
2.  Dodawanie klucza aplikacji konta Google + do usługi Azure AD B2C
3.  Dodawanie dostawcy oświadczeń do zasad
4.  Rejestrowanie Google + konto dostawcy oświadczeń wobec podróży użytkownika
5.  Przekazywanie zasad do usługi Azure AD B2C dzierżawy i przetestować go

## <a name="create-a-google-account-application"></a>Tworzenie aplikacji konto Google +
Aby użyć Google + jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację Google + i dostarczyć odpowiednie parametry. Możesz zarejestrować Google + aplikacją tutaj: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Przejdź do [konsoli deweloperów Google](https://console.developers.google.com/) i zaloguj się przy użyciu poświadczeń konta Google +.
2.  Kliknij przycisk **Tworzenie projektu**, wprowadź **Nazwa projektu**, a następnie kliknij przycisk **Utwórz**.

3.  Kliknij pozycję **menu projekty**.

    ![Google + konto — wybierz projekt](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Kliknij pozycję **+** przycisku.

    ![Google + konto — Utwórz nowy projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Wprowadź **Nazwa projektu**, a następnie kliknij przycisk **Utwórz**.

    ![Google + konto — nowy projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Poczekaj, aż projektu jest gotowy i kliknąć **menu projekty**.

    ![Google + konto — Zaczekaj, aż nowego projektu jest gotowe do użycia.](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Kliknij nazwę projektu.

    ![Google + konto — wybierz pozycję Nowy projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Kliknij przycisk **Menedżer interfejsu API** a następnie kliknij przycisk **poświadczenia** w nawigacji po lewej stronie.
9.  Kliknij przycisk **ekran zgody OAuth** kartę u góry.

    ![Google + konto — ekran zgody OAuth zestawu](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Wybierz lub Określ prawidłową **adres E-mail**, podaj **nazwa produktu**i kliknij przycisk **Zapisz**.

    ![Google + - poświadczenia aplikacji](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Kliknij przycisk **nowe poświadczenia** , a następnie wybierz **identyfikator klienta OAuth**.

    ![Google + — Tworzenie nowego poświadczenia aplikacji](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  W obszarze **typ aplikacji**, wybierz opcję **aplikacji sieci Web**.

    ![Google + — Wybieranie typu aplikacji](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Podaj **nazwa** dla aplikacji, wprowadź `https://{tenant}.b2clogin.com` w **JavaScript autoryzowanych źródeł** pola i `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` w **identyfikatory URI przekierowania autoryzowanych** pole. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c). **{Dzierżawa}** wartości jest uwzględniana wielkość liter. Kliknij pozycję **Utwórz**.

    ![Google + - zapewniają JavaScript autoryzowanych źródeł i identyfikatory URI przekierowania](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Skopiuj wartości z **identyfikator klienta** i **klucz tajny klienta**. Potrzebne do skonfigurowania Google + jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.

    ![Google + - skopiuj wartości klienta identyfikator i klucz tajny klienta](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Dodaj klucz aplikacji konto Google + do usługi Azure AD B2C
Federacja z konta Google + wymaga klucz tajny klienta konta Google + zaufania usługi Azure AD B2C w imieniu aplikacji. Chcesz przechować klucz tajny aplikacji Google + w dzierżawie usługi Azure AD B2C:  

1.  Przejdź do dzierżawy usługi Azure AD B2C i wybierz **ustawieniami B2C** > **struktura środowiska tożsamości**
2.  Wybierz **klucze zasad** Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie.
3.  Kliknij przycisk **+ Dodaj**.
4.  Aby uzyskać **opcje**, użyj **ręczne**.
5.  Aby uzyskać **nazwa**, użyj `GoogleSecret`.  
    Prefiks `B2C_1A_` mogą być dodawane automatycznie.
6.  W **klucz tajny** wprowadź klucz tajny aplikacji Google z [konsoli deweloperów Google](https://console.developers.google.com/) skopiowane powyżej.
7.  Aby uzyskać **użycie klucza**, użyj **podpisu**.
8.  Kliknij przycisk **Utwórz**
9.  Upewnij się, że utworzono klucz `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Dodawanie dostawcy oświadczeń w zasadach rozszerzenia

Użytkownikom na logowanie przy użyciu konta Google +, należy zdefiniować konto Google + jako dostawcy oświadczeń. Innymi słowy należy określić punkt końcowy, który komunikuje się usługi Azure AD B2C. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj konto Google + jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzła w pliku zasad rozszerzenia:

1.  Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego. Jeśli potrzebujesz edytora XML [spróbuj programu Visual Studio Code](https://code.visualstudio.com/download), lekki edytor dla wielu platform.
2.  Znajdź `<ClaimsProviders>` sekcji
3.  Dodaj następujący fragment kodu XML w obszarze `ClaimsProviders` i Zastąp ciąg `client_id` wartość za pomocą usługi Google + konto identyfikator klienta aplikacji przed zapisaniem pliku.  

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

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Zarejestruj Google + konto dostawcy oświadczeń zarejestrować się lub Zarejestruj w podróży użytkownika

Dostawca tożsamości została skonfigurowana.  Jednak nie jest dostępna we wszystkich ekranów konta-dokonywania/logowania. Dodaj dostawcę tożsamości konta Google + do użytkownika `SignUpOrSignIn` podróży użytkownika. Aby udostępnić ją, możemy utworzyć duplikatem istniejącej podróży użytkownika szablonu.  Następnie dodamy dostawcy tożsamości konta Google +:

>[!NOTE]
>
>Jeśli został skopiowany `<UserJourneys>` elementu z pliku podstawowego zasad do pliku rozszerzenie (TrustFrameworkExtensions.xml), możesz przejść do tej sekcji.

1.  Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).
2.  Znajdź `<UserJourneys>` elementu i skopiować całą zawartość `<UserJourneys>` węzła.
3.  Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodać.
4.  Wklej całą zawartość `<UserJourney>` węzeł, który został skopiowany jako element podrzędny elementu `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Wyświetlanie przycisku
`<ClaimsProviderSelections>` Element definiuje listę opcji do wyboru dostawcy oświadczeń i ich kolejność.  `<ClaimsProviderSelection>` element jest odpowiednikiem przycisk dostawcy tożsamości, na stronie konta-dokonywania/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla konta Google +, nowy przycisk pojawia się po użytkownik wyładowuje na stronie. Aby dodać ten element:

1.  Znajdź `<UserJourney>` węzeł, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, który został skopiowany.
2.  Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"`
3.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji
Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się przy użyciu konta Google +, aby odebrać token.

1.  Znajdź `<OrchestrationStep>` zawierającej `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Upewnij się, `Id` ma taką samą wartość jak w przypadku `TargetClaimsExchangeId` w poprzedniej sekcji
> * Upewnij się, `TechnicalProfileReferenceId` ustawiony identyfikator profilu technicznego utworzonego wcześniej (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Przekazywanie zasad dla Twojej dzierżawy
1.  W [witryny Azure portal](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2.  Wybierz **struktura środowiska tożsamości**.
3.  Otwórz **wszystkie zasady** bloku.
4.  Wybierz **przekazywać zasady**.
5.  Sprawdź **Zastąp zasady Jeśli istnieje** pole.
6.  **Przekaż** TrustFrameworkExtensions.xml i upewnij się, że nie wystąpi niepowodzenie weryfikacji

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz
1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości**.

    >[!NOTE]
    >
    >    **Uruchom teraz** wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. 
    >    Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [rejestracji aplikacji](active-directory-b2c-app-registration.md) artykułu.


2.  Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany. Wybierz **Uruchom teraz**.
3.  Powinien móc logować się za pomocą konta Google +.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Opcjonalnie] Zarejestruj Google + konto dostawcy oświadczeń wobec podróży użytkownika edytowania profilu
Warto również dodać dostawcę tożsamości konta Google + do użytkownika `ProfileEdit` podróży użytkownika. Aby udostępnić ją, firma Microsoft Powtórz ostatnie dwa kroki:

### <a name="display-the-button"></a>Wyświetlanie przycisku
1.  Otwórz plik rozszerzenia zasad (na przykład TrustFrameworkExtensions.xml).
2.  Znajdź `<UserJourney>` węzeł, który zawiera `Id="ProfileEdit"` w podróży użytkownika, który został skopiowany.
3.  Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"`
4.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji
1.  Znajdź `<OrchestrationStep>` zawierającej `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="upload-the-policy-to-your-tenant"></a>Przekazywanie zasad dla Twojej dzierżawy
1.  W [witryny Azure portal](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2.  Wybierz **struktura środowiska tożsamości**.
3.  Otwórz **wszystkie zasady** bloku.
4.  Wybierz **przekazywać zasady**.
5.  Sprawdź **Zastąp zasady Jeśli istnieje** pole.
6.  **Przekaż** TrustFrameworkExtensions.xml i upewnij się, że nie wystąpi niepowodzenie weryfikacji.

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testowanie niestandardowe zasady edytowania profilu za pomocą polecenia Uruchom teraz

1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości**.
2.  Otwórz **B2C_1A_ProfileEdit**, jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany. Wybierz **Uruchom teraz**.
3.  Powinien móc logować się za pomocą konta Google +.

## <a name="download-the-complete-policy-files"></a>Pobierz pliki pełną zasad
Opcjonalnie: Zalecamy tworzenie scenariusza za pomocą własne zasady niestandardowe, które omówimy pliki po ukończeniu, wprowadzenie do zasad niestandardowych zamiast przy użyciu tych przykładowych plików.  [Przykładowe pliki zasad dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
