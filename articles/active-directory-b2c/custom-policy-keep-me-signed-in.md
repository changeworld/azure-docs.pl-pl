---
title: Nie wylogowuj mnie w Azure Active Directory B2C
description: Dowiedz się, jak skonfigurować rejestrowanie nadal zalogowane (KMSI) w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 052e1bc4e9a14b34e21b0bfeb4193fbd0b2b1a22
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848904"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Włącz opcję Nie wylogowuj mnie (KMSI) w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Możesz włączyć funkcje Zachowaj zalogowany (KMSI) dla użytkowników sieci Web i aplikacji natywnych, które mają konta lokalne w katalogu Azure Active Directory B2C (Azure AD B2C). Ta funkcja udziela dostępu do użytkowników, którzy powracają do aplikacji bez monitowania o wprowadzenie nazwy użytkownika i hasła. Ten dostęp jest odwoływany po wylogowaniu się użytkownika.

Użytkownicy nie powinni włączać tej opcji na komputerach publicznych.

![Przykładowa strona logowania z zalogowaniem, która wyświetla pole wyboru nie wylogowuj mnie](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Wymagania wstępne

Dzierżawa Azure AD B2C skonfigurowana do zezwalania na logowanie przy użyciu konta lokalnego. KMSI nie jest obsługiwane dla kont zewnętrznych dostawców tożsamości.

Jeśli nie masz dzierżawy, możesz ją utworzyć, wykonując czynności opisane w [samouczku: Tworzenie dzierżawy Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Dodaj element definicji zawartości

W elemencie **BuildingBlocks** pliku rozszerzenia Dodaj element **ContentDefinitions** .

1. W elemencie **ContentDefinitions** Dodaj element **ContentDefinition** o identyfikatorze `api.signuporsigninwithkmsi`.
2. W elemencie **ContentDefinition** Dodaj elementy **LoadUri**, **RecoveryUri**i **DataUri** . Wartość `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` elementu **DataUri** to zrozumiały dla komputera identyfikator, który wywołuje KMSI pole wyboru na stronach logowania. Ta wartość nie może być zmieniana.

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

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Dodawanie dostawcy oświadczeń logowania dla konta lokalnego

Możesz zdefiniować logowanie do konta lokalnego jako dostawca oświadczeń przy użyciu elementu **ClaimsProvider** w pliku rozszerzenia zasad:

1. Otwórz plik *TrustFrameworkExtensions. XML* z katalogu roboczego.
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego. [Pakiet początkowy](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) zawiera dostawcę oświadczeń logowania do konta lokalnego.
3. Dodaj element **ClaimsProvider** z parametrami **DisplayName** i **profilu technicznym** , jak pokazano w następującym przykładzie:

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

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku *TrustFrameworkExtensions. XML* .

1. W pliku *TrustFrameworkExtensions. XML* Znajdź element **profilu technicznym** o identyfikatorze `login-NonInteractive` i element **profilu technicznym** z identyfikatorem `login-NonInteractive-PasswordChange` i Zastąp wszystkie wartości `IdentityExperienceFrameworkAppId` identyfikatorem aplikacji programu Identity Experience Framework, zgodnie z opisem w temacie [wprowadzenie](custom-policy-get-started.md).

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Zastąp wszystkie wartości `ProxyIdentityExperienceFrameworkAppId` identyfikatorem aplikacji w programie Application Platform Experience platformy, zgodnie z opisem w temacie [wprowadzenie](custom-policy-get-started.md).
3. Zapisz plik rozszerzeń.

## <a name="create-a-kmsi-enabled-user-journey"></a>Utwórz podróż użytkownika z włączonym KMSI

Dodaj dostawcę oświadczeń logowania dla konta lokalnego do podróży użytkownika.

1. Otwórz podstawowy plik zasad. Na przykład *TrustFrameworkBase. XML*.
2. Znajdź element **UserJourneys** i Skopiuj całą zawartość elementu **UserJourney** , który używa identyfikatora `SignUpOrSignIn`.
3. Otwórz plik rozszerzenia. Na przykład *TrustFrameworkExtensions. XML* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
4. Wklej cały **UserJourney** element, który został skopiowany jako element podrzędny elementu **UserJourneys** .
5. Zmień wartość identyfikatora dla nowej podróży użytkownika. Na przykład `SignUpOrSignInWithKmsi`.
6. Na koniec w pierwszym kroku aranżacji Zmień wartość **ContentDefinitionReferenceId** na `api.signuporsigninwithkmsi`. Ustawienie tej wartości umożliwia korzystanie z pola wyboru w podróży użytkownika.
7. Zapisz i Przekaż plik rozszerzenia i upewnij się, że wszystkie walidacje zostały wykonane pomyślnie.

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

## <a name="create-a-relying-party-file"></a>Utwórz plik jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Utwórz kopię pliku *SignUpOrSignIn. XML* w katalogu roboczym, a następnie zmień jego nazwę. Na przykład *SignUpOrSignInWithKmsi. XML*.
2. Otwórz nowy plik i zaktualizuj atrybut **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. To jest nazwa zasad. Na przykład `SignUpOrSignInWithKmsi`.
3. Zmień atrybut **ReferenceId** elementu **DefaultUserJourney** , tak aby był zgodny z identyfikatorem utworzonej nowej podróży użytkownika. Na przykład `SignUpOrSignInWithKmsi`.

    KMSI konfiguruje się za pomocą elementu **UserJourneyBehaviors** z **SingleSignon**, **SessionExpiryType**i **SessionExpiryInSeconds** jako jego pierwszego elementu podrzędnego. Atrybut **KeepAliveInDays** określa, jak długo użytkownik pozostanie zalogowany. W poniższym przykładzie sesja KMSI automatycznie wygasa po `7` dniach, niezależnie od tego, jak często użytkownik wykonuje uwierzytelnianie dyskretne. Ustawienie wartości **KeepAliveInDays** na `0` powoduje wyłączenie funkcji KMSI. Domyślnie ta wartość jest `0`. Jeśli wartość **SessionExpiryType** jest `Rolling`, sesja KMSI zostanie rozszerzona o `7` dni za każdym razem, gdy użytkownik wykonuje uwierzytelnianie dyskretne.  W przypadku wybrania `Rolling` należy zachować minimalną liczbę dni.

    Wartość **SessionExpiryInSeconds** reprezentuje czas wygaśnięcia sesji logowania jednokrotnego. Jest on używany wewnętrznie przez Azure AD B2C, aby sprawdzić, czy sesja KMSI wygasła. Wartość **KeepAliveInDays** określa wartość wygaśnięcia/maks. wiek pliku cookie logowania jednokrotnego w przeglądarce sieci Web. W przeciwieństwie do **SessionExpiryInSeconds**, **KeepAliveInDays** jest używany, aby zapobiec wyczyszczeniu pliku cookie przez przeglądarkę. Użytkownik może zalogować się w trybie dyskretnym tylko wtedy, gdy istnieje plik cookie sesji logowania jednokrotnego, który jest kontrolowany przez **KeepAliveInDays**i nie wygasł, który jest kontrolowany przez **SessionExpiryInSeconds**.

    Jeśli użytkownik nie zostanie włączony **na stronie Rejestracja i** logowanie, sesja wygasa po upływie czasu wskazywanego przez **SessionExpiryInSeconds** lub gdy przeglądarka zostanie zamknięta. Jeśli użytkownik włączy **Logowanie**, wartość **KeepAliveInDays** zastępuje wartość **SessionExpiryInSeconds** i określa czas wygaśnięcia sesji. Nawet jeśli użytkownicy zamkną przeglądarkę i otworzą ją ponownie, mogą w dalszym ciągu zalogować się, tak długo, jak w chwili **KeepAliveInDays**. Zaleca się, aby wartość **SessionExpiryInSeconds** była krótszym okresem (1200 sekund), podczas gdy wartość **KeepAliveInDays** można ustawić na stosunkowo długi okres (7 dni), jak pokazano w następującym przykładzie:

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

4. Zapisz zmiany, a następnie Przekaż plik.
5. Aby przetestować przekazane zasady niestandardowe, w Azure Portal przejdź do strony zasady, a następnie wybierz pozycję **Uruchom teraz**.

Przykładowe zasady można znaleźć [tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
