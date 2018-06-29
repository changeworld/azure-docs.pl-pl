---
title: Wylogowuj mnie w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat pokazująca, jak skonfigurować "zachowuje wylogowuj mnie".
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a32aaa5d91426199c29765d2d9645e8a4ddb03b4
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709159"
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Usługa Azure Active Directory B2C: Włącz "Wylogowuj mnie (KMSI)"  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure AD B2C umożliwia teraz sieci web i natywnych aplikacji, aby włączyć funkcję "Wylogowuj mnie (KMSI)". Ta funkcja udziela dostępu do zwracania użytkowników do aplikacji bez wyświetlania monitu o ponowne wprowadzenie nazwy użytkownika i hasła. Dostęp jest odwoływane po wylogowaniu się użytkownika. 

Zaleca się zaznaczenie tej opcji na komputery publiczne użytkowników. 

![img](images/kmsi.PNG)


## <a name="prerequisites"></a>Wymagania wstępne

Dzierżawy usługi Azure AD B2C skonfigurowane i umożliwiają lokalnego konta konta-konta/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Jak włączyć KMSI

Wprowadź następujące zmiany w zasadach rozszerzenia framework zaufania.

## <a name="adding-a-content-definition-element"></a>Dodawanie elementu definicję zawartości 

`BuildingBlocks` Węzła rozszerzenie pliku musi zawierać `ContentDefinitions` elementu. 

1. W `ContentDefinitions` sekcji, zdefiniuj nowy `ContentDefinition` o identyfikatorze `api.signuporsigninwithkmsi`.
2. Nowej `ContentDefinition` musi zawierać `LoadUri`, `RecoveryUri` i `DataUri` w następujący sposób.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` jest zrozumiały dla identyfikatora urządzenia i powoduje wyświetlenie KMSI pola wyboru na stronach logowania. Upewnij się, że nie możesz zmienić tę wartość. 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>Dodawanie dostawcy oświadczeń logowania konta lokalnego 

Możesz zdefiniować Rejestrowanie konta lokalnego dostawcy oświadczeń, aby `<ClaimsProvider>` węzeł w pliku rozszerzenie zasad:

1. Otwórz plik rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego. 
2. Znajdź `<ClaimsProviders>` sekcji. Jeśli nie istnieje, dodaj ją w obszarze węzła głównego.
3. Pakiet początkowy z [wprowadzenie](active-directory-b2c-get-started-custom.md) jest dostarczany z dostawcy oświadczeń "Lokalne konto logowania". 
4. Jeśli nie, Dodaj nowy `<ClaimsProvider>` węzła w następujący sposób:

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>Dodaj identyfikatory aplikacji do zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku rozszerzenia (`TrustFrameworkExtensions.xml`):

1. W pliku rozszerzenia (TrustFrameworkExtensions.xml), Znajdź element `<TechnicalProfile Id="login-NonInteractive">` i `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Zastąp wszystkie wystąpienia `IdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji Framework obsługi tożsamości aplikacji zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md). Oto przykład:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Zastąp wszystkie wystąpienia `ProxyIdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji Framework obsługi tożsamości serwera Proxy aplikacji zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

4. Zapisz plik rozszerzenia.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Utwórz KMSI w podróży włączonego użytkownika

Teraz należy dodać do podróży użytkownika logowania konta lokalnego dostawcy oświadczeń. 

1. Otwórz plik bazowy tej zasady (na przykład TrustFrameworkBase.xml).
2. Znajdź `<UserJourneys>` element i skopiuj cały `<UserJourney>` węzła, który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodaj je.
4. Wklej całą `<UserJourney>` węzła, który został skopiowany jako element podrzędny `<UserJourneys>` elementu.
5. Zmień nazwę Identyfikatora nowy przebieg użytkownika (na przykład zmienić nazwę jako `SignUpOrSignInWithKmsi`).
6. Ponadto w `OrchestrationStep 1` zmienić `ContentDefinitionReferenceId` do `api.signuporsigninwithkmsi` , zdefiniowanego w poprzednich krokach. Umożliwia to pole wyboru w podróży użytkownika. 
7. Po zakończeniu modyfikowania pliku rozszerzenia. Zapisz i przekazywanie tego pliku. Upewnij się, że wszystkie operacje sprawdzania poprawności powiodło się.

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

## <a name="create-a-relying-party-rp-file"></a>Utwórz plik jednostki uzależnionej strony (RP)

Następnie zaktualizuj jednostki uzależnionej pliku strony (RP), który inicjuje przebieg użytkownika, który został utworzony:

1. Utwórz kopię SignUpOrSignIn.xml w katalogu roboczym. Następnie należy zmienić jego nazwę (na przykład SignUpOrSignInWithKmsi.xml).

2. Otwórz nowy plik i aktualizacji `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` z unikatową wartość. Jest to nazwa zasady (na przykład SignUpOrSignInWithKmsi).

3. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` odpowiadające `Id` nowe podróży użytkownika utworzony (na przykład SignUpOrSignInWithKmsi).

4. KMSI jest skonfigurowany w `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** Określa, jak długo użytkownik pozostanie zalogowany. W poniższym przykładzie sesji KMSI automatycznie wygaśnie po 14 dniach niezależnie od tego, jak często użytkownik wykonuje uwierzytelnianie w trybie dyskretnym.

   Ustawienie `KeepAliveInDays` wartość na 0 spowoduje wyłączenie funkcji KMSI. Domyślnie ta wartość wynosi 0

6. Jeśli **`SessionExpiryType`** jest *stopniowych*, a następnie sesji KMSI zostanie rozszerzony przez 14 dni za każdym razem, gdy użytkownik wykonuje uwierzytelnianie w trybie dyskretnym.  Jeśli *stopniowych* jest zaznaczone, zaleca się zachować do minimum liczbę dni. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
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

7. Zapisz zmiany, a następnie przekazać plik.

8. Testowanie zasad niestandardowych, który został przekazany, w portalu Azure, przejdź do bloku zasady, a następnie kliknij przycisk **Uruchom teraz**.


## <a name="link-to-sample-policy"></a>Link do zasad próbki

Można znaleźć zasady próbki [tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








