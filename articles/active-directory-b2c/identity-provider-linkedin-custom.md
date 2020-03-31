---
title: Konfigurowanie logowania się za pomocą konta LinkedIn przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Konfigurowanie logowania za pomocą konta LinkedIn w usłudze Azure Active Directory B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188175"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania za pomocą konta LinkedIn przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie dla użytkowników z konta LinkedIn przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w [wprowadzenie do niestandardowych zasad w usłudze Azure Active Directory B2C](custom-policy-get-started.md).
- Konto LinkedIn — jeśli jeszcze go nie masz, [utwórz konto](https://www.linkedin.com/start/join).
- Strona LinkedIn — do skojarzenia z aplikacją LinkedIn, którą tworzysz w następnej sekcji, musisz utworzyć [stronę LinkedIn.](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page)

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby używać usługi LinkedIn jako dostawcy tożsamości w usłudze Azure AD B2C, należy utworzyć aplikację LinkedIn.

### <a name="create-app"></a>Tworzenie aplikacji

1. Zaloguj się w witrynie [zarządzania aplikacjami LinkedIn](https://www.linkedin.com/secure/developer?newapp=) przy użyciu poświadczeń konta LinkedIn.
1. Wybierz pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji**.
1. Wprowadź nazwę **firmy** odpowiadającą nazwie strony LinkedIn. Utwórz stronę linkedin, jeśli jeszcze jej nie masz.
1. (Opcjonalnie) Wprowadź **adres URL polityki prywatności**. Musi to być prawidłowy adres URL, ale nie musi być osiągalnym punktem końcowym.
1. Wprowadź **firmową wiadomość e-mail**.
1. Prześlij obraz **logo aplikacji.** Obraz logo musi być kwadratowy, a jego wymiary muszą wynosić co najmniej 100x100 pikseli.
1. Pozostaw ustawienia domyślne w sekcji **Produkty.**
1. Zapoznaj się z informacjami przedstawionymi w **kategoriach prawnych**. Jeśli zgadzasz się na warunki, zaznacz to pole wyboru.
1. Wybierz pozycję **Utwórz aplikację**.

### <a name="configure-auth"></a>Konfigurowanie uwierzytelniania

1. Wybierz kartę **Urywek.**
1. Zarejestruj **identyfikator klienta**.
1. Ujawnij i nagraj **klucz tajny klienta**.
1. W **ustawieniach OAuth 2.0**dodaj następujący **adres URL przekierowania**. Zamień `your-tenant` na nazwę dzierżawy. Użyj **wszystkich małych liter** dla nazwy dzierżawy, nawet jeśli jest zdefiniowana za pomocą wielkich liter w usłudze Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny klienta, który został wcześniej zarejestrowany w dzierżawie usługi Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **Katalog + subskrypcja** w górnym menu i wybierz katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
5. Wybierz **pozycję Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
6. W **Options**przypadku `Manual`opcji wybierz opcję .
7. Wprowadź **nazwę** klucza zasad. Na przykład `LinkedInSecret`. Prefiks *B2C_1A_* jest automatycznie dodawany do nazwy klucza.
8. W **pliku Secret**wprowadź klucz tajny klienta, który został wcześniej zarejestrowany.
9. Dla **użycia klucza**wybierz opcję `Signature`.
10. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy logowali się przy użyciu konta LinkedIn, musisz zdefiniować konto jako dostawcę oświadczeń, z którego usługa Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik uwierzytelnił.

Zdefiniuj konto LinkedIn jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz plik *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** w edytorze. Ten plik znajduje się w [pakiecie startowym zasad niestandardowych][starter-pack] pobranych jako część jednego z wymagań wstępnych.
1. Znajdź **ClaimsProviders** element. Jeśli nie istnieje, dodaj go w elemencie głównym.
1. Dodaj nowy **ClaimsProvider** w następujący sposób:

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
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

1. Zastąp wartość **client_id** identyfikatorem klienta aplikacji LinkedIn, który został wcześniej zarejestrowany.
1. Zapisz plik.

### <a name="add-the-claims-transformations"></a>Dodawanie przekształceń oświadczeń

Profil techniczny LinkedIn wymaga **extractGivenNameFromLinkedInResponse** i **ExtractSurNameFromLinkedInResponse** roszczeń przekształceń, które mają zostać dodane do listy ClaimsTransformations. Jeśli nie masz **claimstransformations** element zdefiniowany w pliku, dodać nadrzędne elementy XML, jak pokazano poniżej. Przekształcenia oświadczeń również potrzebują nowego typu oświadczenia zdefiniowanego o nazwie **nullStringClaim**.

Dodaj **element BuildingBlocks** w górnej części pliku *TrustFrameworkExtensions.xml.* Zobacz *TrustFrameworkBase.xml* na przykład.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Prześlij plik rozszerzenia w celu weryfikacji

Masz teraz skonfigurowane zasady, dzięki czemu usługa Azure AD B2C wie, jak komunikować się z kontem LinkedIn. Spróbuj przesłać plik rozszerzenia zasad, aby potwierdzić, że do tej pory nie ma żadnych problemów.

1. Na stronie **Zasady niestandardowe** w dzierżawie usługi Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **zastępowanie zasad, jeśli istnieje**, a następnie przejdź do pliku *TrustFrameworkExtensions.xml.*
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji lub logowania. Aby go udostępnić, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał również dostawcę tożsamości LinkedIn.

1. Otwórz plik *TrustFrameworkBase.xml* w pakiecie startowym.
2. Znajdź i skopiuj całą zawartość elementu `Id="SignUpOrSignIn"` **UserJourney,** który zawiera .
3. Otwórz *trustFrameworkExtensions.xml* i znajdź **UserJourneys** element. Jeśli element nie istnieje, dodaj jeden.
4. Wklej całą zawartość **elementu UserJourney,** który został skopiowany jako element podrzędny elementu **UserJourneys.**
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Wyświetl przycisk

**Element ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz **ClaimsProviderSelection** element dla konta LinkedIn, nowy przycisk pojawia się, gdy użytkownik ląduje na stronie.

1. Znajdź **OrchestrationStep** element, `Order="1"` który zawiera w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelections**dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, `LinkedInExchange`na przykład:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Łączenie przycisku z akcją

Teraz, gdy masz przycisk w miejscu, musisz połączyć go z działaniem. Akcja, w tym przypadku, jest dla usługi Azure AD B2C do komunikowania się z kontem LinkedIn, aby otrzymać token.

1. Znajdź **OrchestrationStep,** `Order="2"` który zawiera w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** upewniając się, że używasz tej samej wartości dla identyfikatora, który był używany dla **obiektu TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** do identyfikatora profilu technicznego utworzonego wcześniej. Na przykład `LinkedIn-OAUTH`.

3. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Komunikacja z usługą Azure AD B2C odbywa się za pośrednictwem aplikacji, która rejestrujesz się w dzierżawie B2C. W tej sekcji wymieniono opcjonalne kroki, które można wykonać, aby utworzyć aplikację testową, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnianej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został utworzony.

1. Zrób kopię *pliku SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład zmień jego nazwę na *SignUpSignInLinkedIn.xml*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** z unikatową wartością. Na przykład `SignUpSignInLinkedIn`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład,`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **defaultuserJourney,** aby dopasować identyfikator nowego środowiska podróży użytkownika, który został utworzony (SignUpSignLinkedIn).
5. Zapisz zmiany, przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że utworzona aplikacja usługi Azure AD B2C jest zaznaczona w polu **Wybierz aplikację,** a następnie przetestuj ją, klikając przycisk **Uruchom teraz**.

## <a name="migration-from-v10-to-v20"></a>Migracja z wersji 1.0 do wersji 2.0

LinkedIn niedawno [zaktualizował swoje interfejsy API z wersji 1.0 do wersji 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Aby przeprowadzić migrację istniejącej konfiguracji do nowej konfiguracji, użyj informacji w poniższych sekcjach, aby zaktualizować elementy profilu technicznego.

### <a name="replace-items-in-the-metadata"></a>Zastępowanie elementów w metadanych

W istniejącym **elemencie Metadanych** **Profilu Technicznego**zaktualizuj następujące elementy **elementu** z:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Do:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Dodawanie elementów do metadanych

W **metadanych** **TechnicalProfile**dodaj następujące elementy **elementu:**

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Aktualizowanie outputclaims

W istniejących **OutputClaims** **TechnicalProfile**, zaktualizować następujące **OutputClaim** elementów z:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Do:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Dodawanie nowych elementów OutputClaimsTransformation

W **OutputClaimsTransformations** of the **TechnicalProfile**dodaj następujące **outputclaimsTransformation** elementy:

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definiowanie nowych przekształceń oświadczeń i typu oświadczenia

W ostatnim kroku dodano nowe przekształcenia oświadczeń, które muszą zostać zdefiniowane. Aby zdefiniować przekształcenia oświadczeń, dodaj je do listy **ClaimsTransformations**. Jeśli nie masz **claimstransformations** element zdefiniowany w pliku, dodać nadrzędne elementy XML, jak pokazano poniżej. Przekształcenia oświadczeń również potrzebują nowego typu oświadczenia zdefiniowanego o nazwie **nullStringClaim**.

**BuildingBlocks** element powinien zostać dodany w górnej części pliku. Zobacz *TrustframeworkBase.xml* jako przykład.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Uzyskiwanie adresu e-mail

W ramach migracji linkedin z wersji 1.0 do wersji 2.0 wymagane jest dodatkowe wywołanie innego interfejsu API w celu uzyskania adresu e-mail. Jeśli musisz uzyskać adres e-mail podczas rejestracji, wykonaj następujące czynności:

1. Wykonaj powyższe kroki, aby umożliwić usługi Azure AD B2C do federacji z LinkedIn, aby umożliwić użytkownikowi zalogować się. Jako część federacji usługa Azure AD B2C odbiera token dostępu dla usługi LinkedIn.
2. Zapisz token dostępu LinkedIn w oświadczeniu. [Zapoznaj się z instrukcjami tutaj](idp-pass-through-custom.md).
3. Dodaj następującego dostawcę oświadczeń, który sprawia, że żądanie do `/emailAddress` interfejsu API LinkedIn. Aby autoryzować to żądanie, potrzebny jest token dostępu LinkedIn.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Dodaj następujący krok aranżacji do podróży użytkownika, tak aby dostawca oświadczeń interfejsu API jest wyzwalany, gdy użytkownik loguje się przy użyciu usługi LinkedIn. Upewnij się, `Order` że numer jest odpowiednio aktualizowany. Dodaj ten krok natychmiast po kroku aranżacji, który wyzwala profil techniczny linkedin.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Uzyskanie adresu e-mail z LinkedIn podczas rejestracji jest opcjonalne. Jeśli nie chcesz uzyskiwać wiadomości e-mail z LinkedIn, ale wymagasz jej podczas rejestracji, użytkownik musi ręcznie wprowadzić adres e-mail i zweryfikować go.

Aby uzyskać pełną próbkę zasad korzystających z dostawcy tożsamości LinkedIn, zobacz [pakiet startowy zasad niestandardowych](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
