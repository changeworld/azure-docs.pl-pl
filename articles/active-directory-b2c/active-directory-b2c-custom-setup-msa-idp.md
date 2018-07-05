---
title: Dodaj konto Microsoft (MSA) jako dostawcy tożsamości za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przykład za pomocą programu Microsoft jako dostawcy tożsamości za pomocą protokołu OpenID Connect (OIDC).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7a83ace83176d75abdac03b354c4c4ac71eb4238
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450142"
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie konta Microsoft (MSA) jako dostawcy tożsamości za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule dowiesz się, jak włączyć logowania dla użytkowników z konta Microsoft (MSA) za pośrednictwem [zasady niestandardowe](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

Kroki te obejmują:

1.  Tworzenie aplikacji konta Microsoft.
2.  Dodawanie klucza aplikacji konta Microsoft do usługi Azure AD B2C
3.  Dodawanie dostawcy oświadczeń do zasad
4.  Rejestrowanie dostawcy oświadczeń Account Microsoft do podróży użytkownika
5.  Przekazywanie zasad do usługi Azure AD B2C dzierżawy i przetestować go

## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konta Microsoft
Aby użyć konta Microsoft jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację konta Microsoft i dostarczyć odpowiednie parametry. Musisz mieć konto Microsoft. Jeśli nie masz, odwiedź stronę [ https://www.live.com/ ](https://www.live.com/).

1.  Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) i zaloguj się przy użyciu poświadczeń konta Microsoft.
2.  Kliknij przycisk **Dodaj aplikację**.

    ![Microsoft konta — Dodaj aplikację](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Podaj **nazwa** dla aplikacji, **kontaktowy adres e-mail**, usuń zaznaczenie pola wyboru **Pozwól nam pomóc Ci zacząć** i kliknij przycisk **Utwórz**.

    ![Konto Microsoft — rejestrowanie aplikacji](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Skopiuj wartość **identyfikator aplikacji**. Możesz go potrzebować, aby skonfigurować konto Microsoft jako dostawcy tożsamości w dzierżawie.

    ![Konto Microsoft — Kopiowanie wartości Identyfikator aplikacji](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Kliknij pozycję **Dodaj platformy**

    ![Microsoft konta — Dodaj platformę](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Wybierz z listy platform **Web**.

    ![Konto Microsoft — z listy platform wybierz sieci Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Wprowadź `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` w **identyfikatory URI przekierowań** pola. Zastąp **{dzierżawa}** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com).

    ![Konto Microsoft — zestaw przekierowania adresów URL](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Kliknij pozycję **wygenerować nowe hasło** w obszarze **wpisów tajnych aplikacji** sekcji. Skopiuj nowe hasło, które są wyświetlane na ekranie. Możesz go potrzebować, aby skonfigurować konto Microsoft jako dostawcy tożsamości w dzierżawie. To hasło jest ważnym poświadczeniem zabezpieczeń.

    ![Microsoft konta — Generowanie nowego hasła](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Konto Microsoft — kopiowanie nowe hasło](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Zaznacz pole wyboru, które mówi **Obsługa zestawu Live SDK** w obszarze **zaawansowane opcje** sekcji. Kliknij pozycję **Zapisz**.

    ![Konto Microsoft — Obsługa zestawu Live SDK](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Dodaj klucz aplikacji konta Microsoft do usługi Azure AD B2C
Federacja z kontami Microsoft wymaga klucz tajny klienta konta Microsoft w celu zaufania usługi Azure AD B2C w imieniu aplikacji. Musisz zapisać swoje secert aplikacji konta Microsoft w dzierżawie usługi Azure AD B2C:   

1.  Przejdź do dzierżawy usługi Azure AD B2C i wybierz **ustawieniami B2C** > **struktura środowiska tożsamości**
2.  Wybierz **klucze zasad** Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie.
3.  Kliknij przycisk **+ Dodaj**.
4.  Aby uzyskać **opcje**, użyj **ręczne**.
5.  Aby uzyskać **nazwa**, użyj `MSASecret`.  
    Prefiks `B2C_1A_` mogą być dodawane automatycznie.
6.  W **klucz tajny** wprowadź klucz tajny aplikacji firmy Microsoft z https://apps.dev.microsoft.com
7.  Aby uzyskać **użycie klucza**, użyj **podpisu**.
8.  Kliknij przycisk **Utwórz**
9.  Upewnij się, że utworzono klucz `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Dodawanie dostawcy oświadczeń w zasadach rozszerzenia
Użytkownikom na logowanie przy użyciu Account firmy Microsoft, należy zdefiniować Account Microsoft jako dostawcy oświadczeń. Innymi słowy należy określić punkt końcowy, który komunikuje się usługi Azure AD B2C. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj Account Microsoft jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzła w pliku zasad rozszerzenia:

1.  Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego. Jeśli potrzebujesz edytora XML [spróbuj programu Visual Studio Code](https://code.visualstudio.com/download), lekki edytor dla wielu platform.
2.  Znajdź `<ClaimsProviders>` sekcji
3.  Dodaj następujący fragment kodu XML w obszarze `ClaimsProviders` elementu:

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
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
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

4.  Zastąp `client_id` wartość identyfikatora klienta aplikacji Account Microsoft

5.  Zapisz plik.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Rejestrowanie, Account Microsoft oświadczenia dostawcy do logowania się lub zaloguj w podróży użytkownika

W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępny w wszystkich ekranów konta-dokonywania/logowania. Teraz należy dodać dostawcę tożsamości Account Microsoft do użytkownika `SignUpOrSignIn` podróży użytkownika. Aby udostępnić ją, możemy utworzyć duplikatem istniejącej podróży użytkownika szablonu.  Następnie dodamy Account Microsoft dostawcy tożsamości:

> [!NOTE]
>
>Jeśli został wcześniej skopiowany `<UserJourneys>` elementu z pliku podstawowego zasad do pliku rozszerzenie `TrustFrameworkExtensions.xml`, możesz przejść do tej sekcji.

1.  Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).
2.  Znajdź `<UserJourneys>` elementu i skopiować całą zawartość `<UserJourneys>` węzła.
3.  Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodać.
4.  Wklej całą zawartość `<UserJourneys>` węzeł, który został skopiowany jako element podrzędny elementu `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Wyświetlanie przycisku
`<ClaimsProviderSelections>` Element definiuje listę opcji do wyboru dostawcy oświadczeń i ich kolejność.  `<ClaimsProviderSelection>` element jest odpowiednikiem przycisk dostawcy tożsamości, na stronie konta-dokonywania/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla konta Microsoft, nowy przycisk pojawia się po użytkownik wyładowuje na stronie. Aby dodać ten element:

1.  Znajdź `<UserJourney>` węzeł, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, który został skopiowany.
2.  Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"`
3.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji
Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z Account Microsoft, aby odebrać token. Łącze przycisku do akcji, łącząc profilu technicznego dla dostawcy oświadczeń Account Microsoft:

1.  Znajdź `<OrchestrationStep>` zawierającej `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Upewnij się, `Id` ma taką samą wartość jak w przypadku `TargetClaimsExchangeId` w poprzedniej sekcji
>   * Upewnij się, `TechnicalProfileReferenceId` ustawiony identyfikator profilu technicznego utworzonego wcześniej (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Przekazywanie zasad dla Twojej dzierżawy
1.  W [witryny Azure portal](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2.  Wybierz **struktura środowiska tożsamości**.
3.  Otwórz **wszystkie zasady** bloku.
4.  Wybierz **przekazywać zasady**.
5.  Sprawdź **Zastąp zasady Jeśli istnieje** pole.
6.  **Przekaż** TrustFrameworkExtensions.xml i upewnij się, że nie wystąpi niepowodzenie weryfikacji

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz

1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości**.
> [!NOTE]
>
>**Uruchom teraz** wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [rejestracji aplikacji](active-directory-b2c-app-registration.md) artykułu.
2.  Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany. Wybierz **Uruchom teraz**.
3.  Powinien móc logować się za pomocą konta Microsoft.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Opcjonalnie] Zarejestruj dostawcę oświadczeń Account Microsoft do edytowania profilu podróży użytkownika
Warto również dodać Account Microsoft dostawcy tożsamości do użytkownika `ProfileEdit` podróży użytkownika. Aby udostępnić ją, firma Microsoft Powtórz ostatnie dwa kroki:

### <a name="display-the-button"></a>Wyświetlanie przycisku
1.  Otwórz plik rozszerzenia zasad (na przykład TrustFrameworkExtensions.xml).
2.  Znajdź `<UserJourney>` węzeł, który zawiera `Id="ProfileEdit"` w podróży użytkownika, który został skopiowany.
3.  Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"`
4.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji
1.  Znajdź `<OrchestrationStep>` zawierającej `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testowanie niestandardowe zasady edytowania profilu za pomocą polecenia Uruchom teraz
1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości**.
2.  Otwórz **B2C_1A_ProfileEdit**, jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany. Wybierz **Uruchom teraz**.
3.  Powinien móc logować się za pomocą konta Microsoft.

## <a name="download-the-complete-policy-files"></a>Pobierz pliki pełną zasad
Opcjonalnie: Zalecamy tworzenie scenariusza za pomocą własne zasady niestandardowe, które omówimy pliki po ukończeniu, wprowadzenie do zasad niestandardowych zamiast przy użyciu tych przykładowych plików.  [Przykładowe pliki zasad dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
