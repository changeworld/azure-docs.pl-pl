---
title: Konfigurowanie logowania się za pomocą konta Google przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Skonfiguruj logowanie za pomocą konta Google w usłudze Azure Active Directory B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d02c86a1ff330aa4003299e1494a164089d8470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188226"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania się za pomocą konta Google przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie użytkowników z kontem Google przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w usłudze [Wprowadzenie do zasad niestandardowych w usłudze Active Directory B2C](custom-policy-get-started.md).
- Jeśli nie masz jeszcze konta Google, utwórz je w [witrynie Utwórz konto Google.](https://accounts.google.com/SignUp)

## <a name="register-the-application"></a>Zarejestruj zgłoszenie

Aby włączyć logowanie użytkowników z konta Google, musisz utworzyć projekt aplikacji Google.

1. Zaloguj się w [Konsoli Programistów Google](https://console.developers.google.com/) przy użyciu danych logowania do konta.
2. Wprowadź **nazwę projektu**, kliknij przycisk **Utwórz**, a następnie upewnij się, że używasz nowego projektu.
3. Wybierz **polecenie Poświadczenia** w menu po lewej stronie, a następnie wybierz pozycję **Utwórz poświadczenia > identyfikator klienta Oauth**.
4. Wybierz **pozycję Konfiguruj ekran zgody**.
5. Wybierz lub określ prawidłowy **adres e-mail**, `b2clogin.com` podaj nazwę **produktu** wyświetlaną użytkownikom, wprowadź w obszarze **Autoryzowane domeny**, a następnie kliknij przycisk **Zapisz**.
6. W obszarze **Typ aplikacji**wybierz pozycję Aplikacja **sieci Web**.
7. Wprowadź **nazwę** aplikacji.
8. W **polu Źródła autoryzowanego javascriptu** `https://your-tenant-name.b2clogin.com` wprowadź i w polu Autoryzowane **przekierowanie identyfikatorów URI**wprowadź . `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` Zastąp nazwę dzierżawy nazwą dzierżawy. Należy użyć wszystkich małych liter podczas wprowadzania nazwy dzierżawy, nawet jeśli dzierżawca jest zdefiniowany za pomocą wielkich liter w usłudze Azure AD B2C.
8. Kliknij przycisk **Utwórz**.
9. Skopiuj wartości **identyfikatora klienta** i **klucza tajnego klienta**. Oba jednych i drugich będą potrzebne do skonfigurowania Google jako dostawcy tożsamości w dzierżawie. Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny klienta, który został wcześniej zarejestrowany w dzierżawie usługi Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **Katalog + subskrypcja** w górnym menu i wybierz katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
5. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
6. W **Options**przypadku `Manual`opcji wybierz opcję .
7. Wprowadź **nazwę** klucza zasad. Na przykład `GoogleSecret`. Prefiks `B2C_1A_` zostanie automatycznie dodany do nazwy klucza.
8. W **pliku Secret**wprowadź wcześniej zarejestrowany klucz tajny klienta.
9. Dla **użycia klucza**wybierz opcję `Signature`.
10. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy logowali się przy użyciu konta Google, musisz zdefiniować konto jako dostawcę oświadczeń, z którego usługa Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik uwierzytelnił.

Konto Google można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz *plik TrustFrameworkExtensions.xml*.
2. Znajdź **ClaimsProviders** element. Jeśli nie istnieje, dodaj go w elemencie głównym.
3. Dodaj nowy **ClaimsProvider** w następujący sposób:

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
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
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
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ustaw **client_id** identyfikatorem aplikacji z rejestracji aplikacji.
5. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Prześlij plik rozszerzenia w celu weryfikacji

Do tej pory skonfigurowano zasady, dzięki czemu usługa Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przesłać plik rozszerzenia zasad, aby potwierdzić, że do tej pory nie ma żadnych problemów.

1. Na stronie **Zasady niestandardowe** w dzierżawie usługi Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **zastępowanie zasad, jeśli istnieje**, a następnie przejdź do pliku *TrustFrameworkExtensions.xml.*
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny w żadnym z ekranów rejestracji/logowania. Aby udostępnić go, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał również dostawcę tożsamości usługi Azure AD.

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu startowego.
2. Znajdź i skopiuj całą zawartość elementu `Id="SignUpOrSignIn"` **UserJourney,** który zawiera .
3. Otwórz *trustFrameworkExtensions.xml* i znajdź **UserJourneys** element. Jeśli element nie istnieje, dodaj jeden.
4. Wklej całą zawartość **elementu UserJourney,** który został skopiowany jako element podrzędny elementu **UserJourneys.**
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInGoogle`.

### <a name="display-the-button"></a>Wyświetl przycisk

**Element ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji/logowania. Jeśli dodasz element **ClaimsProviderSelection** dla konta Google, pojawi się nowy przycisk, gdy użytkownik wyląduje na stronie.

1. Znajdź **OrchestrationStep** element, `Order="1"` który zawiera w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelects**dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, `GoogleExchange`na przykład:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Łączenie przycisku z akcją

Teraz, gdy masz przycisk w miejscu, musisz połączyć go z działaniem. Akcja, w tym przypadku, jest dla usługi Azure AD B2C do komunikowania się z kontem Google, aby otrzymać token.

1. Znajdź **OrchestrationStep,** `Order="2"` który zawiera w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** upewniając się, że używasz tej samej wartości dla identyfikatora, który został użyty dla **obiektu TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** do identyfikatora profilu technicznego utworzonego wcześniej. Na przykład `Google-OAuth`.

3. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Komunikacja z usługą Azure AD B2C odbywa się za pośrednictwem aplikacji, która rejestrujesz się w dzierżawie B2C. W tej sekcji wymieniono opcjonalne kroki, które można wykonać, aby utworzyć aplikację testową, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnianej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został utworzony.

1. Zrób kopię *pliku SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład zmień jego nazwę na *SignUpSignInGoogle.xml*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** z unikatową wartością. Na przykład `SignUpSignInGoogle`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład,`http://contoso.com/B2C_1A_signup_signin_google`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney,** aby dopasować identyfikator nowego środowiska podróży użytkownika, który został utworzony (SignUpSignGoogle).
5. Zapisz zmiany, przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że utworzona aplikacja usługi Azure AD B2C jest zaznaczona w polu **Wybierz aplikację,** a następnie przetestuj ją, klikając przycisk **Uruchom teraz**.
