---
title: Konfigurowanie logowania za pomocą konta na Twitterze przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Skonfiguruj logowanie za pomocą konta twitterowego przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5804ded875ef03d7ade4414eb8f08885634748dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051594"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania za pomocą konta twitterowego przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie dla użytkowników konta twitter przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w [wprowadzenie do niestandardowych zasad w usłudze Azure Active Directory B2C](custom-policy-get-started.md).
- Jeśli nie masz jeszcze konta na Twitterze, utwórz je na stronie rejestracji na [Twitterze](https://twitter.com/signup).

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby używać twittera jako dostawcy tożsamości w usłudze Azure AD B2C, należy utworzyć aplikację Twitter.

1. Zaloguj się w witrynie [Twitter Developers](https://developer.twitter.com/en/apps) za pomocą poświadczeń konta na Twitterze.
2. Wybierz **pozycję Utwórz aplikację**.
3. Wprowadź **nazwę aplikacji** i **opis aplikacji**.
4. W **adresie URL witryny**wprowadź `https://your-tenant.b2clogin.com`. Zamień `your-tenant` na nazwę dzierżawy. Na przykład `https://contosob2c.b2clogin.com`.
5. Aby uzyskać adres URL `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp` **wywołania zwrotnego,** wprowadź . Zastąp `your-tenant` nazwą nazwy `your-policy-Id` dzierżawy i identyfikatorem zasad. Na przykład `b2c_1A_signup_signin_twitter`. Należy użyć wszystkich małych liter podczas wprowadzania nazwy dzierżawy, nawet jeśli dzierżawca jest zdefiniowany za pomocą wielkich liter w usłudze Azure AD B2C.
6. U dołu strony przeczytaj i zaakceptuj warunki, a następnie wybierz pozycję **Utwórz**.
7. Na stronie **Szczegóły aplikacji** wybierz pozycję Edytuj > **Edytuj szczegóły**, zaznacz pole wyboru Włącz zaloguj się za **pomocą twittera,** a następnie wybierz pozycję **Zapisz**.
8. Wybierz **klucze i tokeny** i zarejestruj **klucz interfejsu API konsumenta** i tajne wartości **klucza interfejsu API konsumenta, które** mają być używane później.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny, który został wcześniej nagrany w dzierżawie usługi Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **Katalog + subskrypcja** w górnym menu i wybierz katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
5. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
6. W **Options**przypadku `Manual`opcji wybierz opcję .
7. Wprowadź **nazwę** klucza zasad. Na przykład `TwitterSecret`. Prefiks `B2C_1A_` zostanie automatycznie dodany do nazwy klucza.
8. W **pliku Secret**wprowadź wcześniej zarejestrowany klucz tajny klienta.
9. Dla **użycia klucza**wybierz opcję `Encryption`.
10. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy logowali się przy użyciu konta twitterowego, musisz zdefiniować konto jako dostawcę oświadczeń, z którego usługa Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik uwierzytelnił.

Konto twitterowe można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz *plik TrustFrameworkExtensions.xml*.
2. Znajdź **ClaimsProviders** element. Jeśli nie istnieje, dodaj go w elemencie głównym.
3. Dodaj nowy **ClaimsProvider** w następujący sposób:

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

4. Zastąp wartość **client_id** kluczem konsumenta, który został wcześniej nagrany.
5. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Prześlij plik rozszerzenia w celu weryfikacji

Do tej pory skonfigurowano zasady, dzięki czemu usługa Azure AD B2C wie, jak komunikować się z kontem Twitter. Spróbuj przesłać plik rozszerzenia zasad, aby potwierdzić, że do tej pory nie ma żadnych problemów.

1. Na stronie **Zasady niestandardowe** w dzierżawie usługi Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **zastępowanie zasad, jeśli istnieje**, a następnie przejdź do pliku *TrustFrameworkExtensions.xml.*
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji lub logowania. Aby go udostępnić, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał również dostawcę tożsamości Twitter.

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu startowego.
2. Znajdź i skopiuj całą zawartość elementu `Id="SignUpOrSignIn"` **UserJourney,** który zawiera .
3. Otwórz *trustFrameworkExtensions.xml* i znajdź **UserJourneys** element. Jeśli element nie istnieje, dodaj jeden.
4. Wklej całą zawartość **elementu UserJourney,** który został skopiowany jako element podrzędny elementu **UserJourneys.**
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Wyświetl przycisk

**Element ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz **claimsproviderSelection** element dla konta Twitter, nowy przycisk pojawia się, gdy użytkownik ląduje na stronie.

1. Znajdź **OrchestrationStep** element, `Order="1"` który zawiera w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelects**dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, `TwitterExchange`na przykład:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Łączenie przycisku z akcją

Teraz, gdy masz przycisk w miejscu, musisz połączyć go z działaniem. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z kontem Twitter, aby otrzymać token.

1. Znajdź **OrchestrationStep,** `Order="2"` który zawiera w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** upewniając się, że używasz tej samej wartości dla identyfikatora, który był używany dla **obiektu TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** do identyfikatora profilu technicznego utworzonego wcześniej. Na przykład `Twitter-OAUTH1`.

3. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Komunikacja z usługą Azure AD B2C odbywa się za pośrednictwem aplikacji, która rejestrujesz się w dzierżawie B2C. W tej sekcji wymieniono opcjonalne kroki, które można wykonać, aby utworzyć aplikację testową, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnianej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został utworzony.

1. Zrób kopię *pliku SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład zmień jego nazwę na *SignUpSignInTwitter.xml*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** z unikatową wartością. Na przykład `SignUpSignInTwitter`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład,`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney,** aby dopasować identyfikator nowego środowiska podróży użytkownika, który został utworzony (SignUpSignTwitter).
5. Zapisz zmiany, przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że utworzona aplikacja usługi Azure AD B2C jest zaznaczona w polu **Wybierz aplikację,** a następnie przetestuj ją, klikając przycisk **Uruchom teraz**.
