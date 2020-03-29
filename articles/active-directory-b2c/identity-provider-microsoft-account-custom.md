---
title: Konfigurowanie logowania się za pomocą konta Microsoft przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Jak używać zasad niestandardowych, aby włączyć konto Microsoft (MSA) jako dostawcę tożsamości przy użyciu protokołu OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188124"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania się za pomocą konta Microsoft przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie użytkowników z konta Microsoft przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w [wprowadzenie do niestandardowych zasad w usłudze Azure Active Directory B2C](custom-policy-get-started.md).
- Jeśli nie masz jeszcze konta Microsoft, utwórz je w programie [https://www.live.com/](https://www.live.com/).

## <a name="register-an-application"></a>Rejestrowanie aplikacji

Aby włączyć logowanie dla użytkowników z kontem Microsoft, musisz zarejestrować aplikację w dzierżawie usługi Azure AD. Dzierżawa usługi Azure AD nie jest taka sama jak dzierżawa usługi Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **subskrypcja Katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
1. Wybierz **pozycję Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *MSAapp1*.
1. W obszarze **Obsługiwane typy kont**wybierz **pozycję Konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft (np Outlook.com.**
1. W obszarze **Przekierowanie identyfikatora URI (opcjonalnie)** wybierz pozycję **Web** i wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w polu tekstowym. Zamień `your-tenant-name` nazwę dzierżawy usługi Azure AD B2C.
1. Wybierz **zarejestruj się**
1. Zarejestruj **identyfikator aplikacji (klienta)** wyświetlany na stronie Przegląd aplikacji. Jest to konieczne podczas konfigurowania dostawcy oświadczeń w dalszej sekcji.
1. Wybieranie **wpisów tajnych certyfikatów &**
1. Kliknij **pozycję Nowy klucz tajny klienta**
1. Wprowadź **opis klucza** tajnego, na przykład *klucz tajny klienta aplikacji MSA,* a następnie kliknij przycisk **Dodaj**.
1. Zapisz hasło aplikacji wyświetlane w kolumnie **Wartość.** Ta wartość jest używana w następnej sekcji.

## <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

Jeśli chcesz uzyskać `family_name` i `given_name` oświadczeń z usługi Azure AD, można skonfigurować opcjonalne oświadczenia dla aplikacji w interfejsie użytkownika witryny azure portalu lub manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [Jak podać opcjonalne oświadczenia do aplikacji usługi Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W sekcji **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście.
1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu (wersja zapoznawcza)**.
1. Wybierz **dodaj oświadczenie opcjonalne**.
1. Wybierz typ tokenu, który chcesz skonfigurować.
1. Wybierz opcjonalne oświadczenia do dodania.
1. Kliknij przycisk **Dodaj**.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Teraz, gdy utworzono aplikację w dzierżawie usługi Azure AD, należy przechowywać klucz tajny klienta tej aplikacji w dzierżawie usługi Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **Katalog + subskrypcja** w górnym menu i wybierz katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
1. W **Options**przypadku `Manual`opcji wybierz opcję .
1. Wprowadź **nazwę** klucza zasad. Na przykład `MSASecret`. Prefiks `B2C_1A_` zostanie automatycznie dodany do nazwy klucza.
1. W **sekcji Tajne**wprowadź klucz tajny klienta zarejestrowany w poprzedniej sekcji.
1. Dla **użycia klucza**wybierz opcję `Signature`.
1. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Aby umożliwić użytkownikom zalogowanie się przy użyciu konta Microsoft, należy zdefiniować konto jako dostawcę oświadczeń, z którego usługa Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik uwierzytelnił.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń, dodając element **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik zasad *TrustFrameworkExtensions.xml.*
1. Znajdź **ClaimsProviders** element. Jeśli nie istnieje, dodaj go w elemencie głównym.
1. Dodaj nowy **ClaimsProvider** w następujący sposób:

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
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

1. Zastąp wartość **client_id** *identyfikatorem aplikacji (klienta)* aplikacji usługi Azure AD, który został zarejestrowany wcześniej.
1. Zapisz plik.

Teraz skonfigurowano zasady, dzięki czemu usługa Azure AD B2C wie, jak komunikować się z aplikacją konta Microsoft w usłudze Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Prześlij plik rozszerzenia w celu weryfikacji

Przed kontynuowaniem prześlij zmodyfikowane zasady, aby potwierdzić, że do tej pory nie ma żadnych problemów.

1. Przejdź do dzierżawy usługi Azure AD B2C w portalu Azure i wybierz **platformę środowiska tożsamości.**
1. Na stronie **Zasady niestandardowe** wybierz pozycję **Przekaż zasady niestandardowe**.
1. Włącz **zastępowanie zasad, jeśli istnieje**, a następnie przejdź do pliku *TrustFrameworkExtensions.xml.*
1. Kliknij pozycję **Przekaż**.

Jeśli w portalu nie są wyświetlane żadne błędy, przejdź do następnej sekcji.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie skonfigurowano dostawcę tożsamości, ale nie jest on jeszcze dostępny na żadnym z ekranów rejestracji lub logowania. Aby udostępnić go, utwórz duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikuj go tak, aby miał również dostawcę tożsamości konta Microsoft.

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu startowego.
1. Znajdź i skopiuj całą zawartość elementu `Id="SignUpOrSignIn"` **UserJourney,** który zawiera .
1. Otwórz *trustFrameworkExtensions.xml* i znajdź **UserJourneys** element. Jeśli element nie istnieje, dodaj jeden.
1. Wklej całą zawartość **elementu UserJourney,** który został skopiowany jako element podrzędny elementu **UserJourneys.**
1. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInMSA`.

### <a name="display-the-button"></a>Wyświetl przycisk

**Element ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz **ClaimsProviderSelection** element dla konta Microsoft, nowy przycisk jest wyświetlany, gdy użytkownik ląduje na stronie.

1. W pliku *TrustFrameworkExtensions.xml* znajdź element **OrchestrationStep,** który zawiera `Order="1"` w utworzonej podróży użytkownika.
1. W obszarze **ClaimsProviderSelects**dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, `MicrosoftAccountExchange`na przykład:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Łączenie przycisku z akcją

Teraz, gdy masz przycisk w miejscu, musisz połączyć go z działaniem. Akcja, w tym przypadku, jest dla usługi Azure AD B2C do komunikowania się z kontem Microsoft, aby otrzymać token.

1. Znajdź **OrchestrationStep,** `Order="2"` który zawiera w podróży użytkownika.
1. Dodaj następujący element **ClaimsExchange** upewniając się, że używasz tej samej wartości dla identyfikatora, który był używany dla **obiektu TargetClaimsExchangeId:**

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId,** aby dopasować wartość `Id` wartości w **TechnicalProfile** elementu dostawcy oświadczeń, który został dodany wcześniej. Na przykład `MSA-OIDC`.

1. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Komunikacja z usługą Azure AD B2C odbywa się za pośrednictwem aplikacji, która rejestrujesz się w dzierżawie B2C. W tej sekcji wymieniono opcjonalne kroki, które można wykonać, aby utworzyć aplikację testową, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnianej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został utworzony.

1. Zrób kopię *pliku SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład zmień jego nazwę na *SignUpSignInMSA.xml*.
1. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** z unikatową wartością. Na przykład `SignUpSignInMSA`.
1. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład,`http://contoso.com/B2C_1A_signup_signin_msa`
1. Zaktualizuj wartość atrybutu **ReferenceId** w **defaultuserJourney,** aby dopasować identyfikator podróży użytkownika, który został utworzony wcześniej (SignUpSignInMSA).
1. Zapisz zmiany, przekaż plik, a następnie wybierz nowe zasady na liście.
1. Upewnij się, że aplikacja usługi Azure AD B2C utworzona w poprzedniej sekcji (lub wypełniając wymagania wstępne, na przykład *webapp1* lub *testapp1)* jest zaznaczona w polu **Wybierz aplikację,** a następnie przetestuj ją, klikając przycisk **Uruchom teraz**.
1. Wybierz przycisk **Konto Microsoft** i zaloguj się.

    Jeśli operacja logowania zakończy się pomyślnie, zostanie `jwt.ms` przekierowana do której jest wyświetlana zdekodowana token, podobnie jak:

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
