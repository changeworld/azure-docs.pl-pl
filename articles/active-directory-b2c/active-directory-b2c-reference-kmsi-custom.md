---
title: Informuj mnie zalogowany w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić się zachować mnie podpisane w (KMSI) w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0ae6f19ea9a04aa6b2547fa031dbb09d03b887c3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509426"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Włączanie opcji nie wylogowuj mnie (KMSI) w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Można włączyć funkcji zachować mnie podpisane w (KMSI) dla aplikacji sieci web i aplikacji natywnych w usłudze Azure Active Directory (Azure AD) B2C. Ta funkcja daje dostęp do zwracania użytkowników do aplikacji bez monitowania użytkownika o ponowne wprowadzenie nazwy użytkownika i hasła. Ten dostęp jest odwoływane, gdy użytkownik wylogowuje się. 

Użytkownicy nie należy włączać tej opcji na komputery publiczne. 

![Włączanie opcji Nie wylogowuj mnie](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Wymagania wstępne

Dzierżawy usługi Azure AD B2C, który jest skonfigurowany do zezwalania konta lokalnego, zarejestruj się i zaloguj się. Jeśli nie masz dzierżawę, można utworzyć jeden wykonując kroki w [samouczka: tworzenie dzierżawy usługi Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Dodaj element definicji zawartości 

W obszarze **BuildingBlocks** elementu z pliku rozszerzenia, Dodaj **ContentDefinitions** elementu. 

1. W obszarze **ContentDefinitions** elementu Dodawanie **ContentDefinition** elementu z identyfikatorem `api.signuporsigninwithkmsi`.
2. W obszarze **ContentDefinition** elementu Dodawanie **parametr LoadUri**, **RecoveryUri**, i **identyfikator URI** elementów. `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` Wartość **identyfikator URI** element jest zrozumiały identyfikatora urządzenia i wyświetlenie KMSI pole wyboru na stronach logowania. Nie można zmienić tę wartość. 

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>                       
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Dodawanie dostawcy oświadczeń w logowania dla konta lokalnego  

Można zdefiniować logowania konta lokalnego jako dostawcę oświadczeń za pomocą **ClaimsProvider** elementu w pliku rozszerzenie zasad:

1. Otwórz *TrustFrameworkExtensions.xml* plików z katalogu roboczego. 
2. Znajdź **ClaimsProviders** elementu. Jeśli nie istnieje, należy dodać go pod elementem głównym. [Pakiet startowy](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) obejmuje konta lokalnego dostawcy oświadczeń w logowania. 
3. Dodaj **ClaimsProvider** element z **DisplayName** i **profilu technicznego** jak pokazano w poniższym przykładzie:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zdefiniowania zasad niestandardowych

Dodawanie identyfikatorów aplikacji do *TrustFrameworkExtensions.xml* pliku.

1. W *TrustFrameworkExtensions.xml* plików, Znajdź **profilu technicznego** elementu z identyfikatorem programu `login-NonInteractive` i **profilu technicznego** elementu z Identyfikator typu `login-NonInteractive-PasswordChange` i Zamień wszystkie wartości `IdentityExperienceFrameworkAppId` z identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Zamień wszystkie wartości `ProxyIdentityExperienceFrameworkAppId` z identyfikatorem aplikacji platformy środowiska tożsamości serwera Proxy aplikacji, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).
3. Zapisz plik rozszerzenia.

## <a name="create-a-kmsi-enabled-user-journey"></a>Utwórz podróż włączonego użytkownika KMSI

Dodaj dostawcę oświadczeń logowania dla lokalnego konta do swoją podróż po użytkownik. 

1. Otwórz plik podstawowy zasad. Na przykład *TrustFrameworkBase.xml*.
2. Znajdź **podróży użytkowników** elementu i skopiować całą zawartość **UserJourney** element, który używa identyfikatora elementu `SignUpOrSignIn`.
3. Otwórz plik rozszerzenia. Na przykład *TrustFrameworkExtensions.xml* i Znajdź **podróży użytkowników** elementu. Jeśli element nie istnieje, dodać.
4. Wklej całą **UserJourney** element, który został skopiowany jako element podrzędny elementu **podróży użytkowników** elementu.
5. Zmień wartość na identyfikator nowego podróży użytkownika. Na przykład `SignUpOrSignInWithKmsi`.
6. Na koniec w pierwszym kroku aranżacji, zmień wartość **ContentDefinitionReferenceId** do `api.signuporsigninwithkmsi`. Ustawienie tej wartości umożliwia pola wyboru w podróży użytkownika. 
7. Zapisz i przekazać rozszerzenie pliku i upewnij się, że wszystkie sprawdzenia powiodło się.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Utwórz plik strony jednostki uzależnionej

Należy zaktualizować plik innych firm (RP) jednostki uzależnionej, która inicjuje podróży użytkownika, który został utworzony.

1. Utwórz kopię *SignUpOrSignIn.xml* plików w katalogu roboczym, a następnie zmień jego nazwę. Na przykład *SignUpOrSignInWithKmsi.xml*.
2. Otwórz nowy plik i zaktualizuj **PolicyId** atrybutu dla **elementu TrustFrameworkPolicy** przy użyciu unikatowej wartości. Jest to nazwa zasad. Na przykład `SignUpOrSignInWithKmsi`.
3. Zmiana **ReferenceId** atrybutu dla **DefaultUserJourney** element, aby dopasować identyfikator nowego podróży użytkownika, który został utworzony. Na przykład `SignUpOrSignInWithKmsi`.

    KMSI jest konfigurowana przy użyciu **UserJourneyBehaviors** element z **SingleSignOn**, **wartość SessionExpiryType**, i **SessionExpiryInSeconds** jako jego pierwszym elementy podrzędne. **KeepAliveInDays** atrybut kontroluje, jak długo użytkownik pozostaje zalogowany. W poniższym przykładzie, sesja KMSI automatycznie wygasa po upływie `7` dni, niezależnie od tego, jak często użytkownik wykonuje dyskretne uwierzytelnianie. Ustawienie **KeepAliveInDays** wartość `0` powoduje wyłączenie funkcji KMSI. Domyślnie ta wartość jest `0`. Jeśli wartość **wartość SessionExpiryType** jest `Rolling`, rozszerzoną sesji KMSI `7` dni za każdym razem, gdy użytkownik wykona dyskretne uwierzytelnianie.  Jeśli `Rolling` jest zaznaczone, należy zachować liczba dni do minimum. 

    Wartość **SessionExpiryInSeconds** reprezentuje czas wygaśnięcia sesji logowania jednokrotnego. Jest to używane wewnętrznie przez usługę Azure AD B2C do sprawdzenia, czy sesja dla KMSI wygasł lub nie. Wartość **KeepAliveInDays** określa wartość Expires/Max-Age plik cookie logowania jednokrotnego w przeglądarce sieci web. W odróżnieniu od **SessionExpiryInSeconds**, **KeepAliveInDays** służy do przeglądarki uniemożliwiają wyczyszczenie plików cookie, jest ono zamknięte. Użytkownik może dyskretnie zalogować się tylko wtedy, gdy istnieje plik cookie sesji logowania jednokrotnego, które są kontrolowane przez **KeepAliveInDays**i nie jest uznawane za wygasłe, którymi steruje **SessionExpiryInSeconds**. 
    
    Jeśli użytkownik nie włączysz **nie wylogowuj mnie** na stronie rejestracji i logowania sesja wygasa po upływie czasu wskazywanym przez **SessionExpiryInSeconds** został przekazany lub przeglądarka jest zamknięta. Jeśli użytkownik włączy **nie wylogowuj mnie**, wartość **KeepAliveInDays** zastępuje wartość **SessionExpiryInSeconds** i decyduje o czas wygaśnięcia sesji. Nawet jeśli użytkownicy Zamknij przeglądarkę i otwórz go ponownie, może nadal w trybie dyskretnym logowania tak długo, jak znajduje się on w czasie **KeepAliveInDays**. Zalecane jest, aby ustawić wartość **SessionExpiryInSeconds** krótki okres (1200 sekund), podczas wartość **KeepAliveInDays** można ustawić na względnie długiego okresu (7 dni), jak pokazano w Poniższy przykład:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Zapisz zmiany, a następnie przekaż plik.
5. Aby przetestować zasad niestandardowych, który został przekazany, w witrynie Azure portal, przejdź do strony zasad, a następnie wybierz **Uruchom teraz**.

Można znaleźć zasad przykładowe [tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








