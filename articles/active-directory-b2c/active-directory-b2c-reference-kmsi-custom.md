---
title: Informuj mnie zalogowany w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat pokazująca, jak skonfigurować "nie wylogowuj mnie".
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6bad6e1f2b204f76b075652a9d3f27367a8de49f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441321"
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Usługa Azure Active Directory B2C: Włącz "Chcę zalogowany (KMSI)"  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure AD B2C umożliwia teraz aplikacji sieci web i aplikacji natywnych, aby włączyć funkcję "Chcę zalogowany (KMSI)". Ta funkcja daje dostęp do zwracania użytkowników do aplikacji bez monitowania użytkownika o ponowne wprowadzenie nazwy użytkownika i hasła. Ten dostęp jest odwoływane, gdy użytkownik loguje. 

Firma Microsoft zaleca się zaznaczenie tej opcji na komputery publiczne użytkowników. 

![img](images/kmsi.PNG)


## <a name="prerequisites"></a>Wymagania wstępne

Dzierżawę usługi Azure AD B2C, skonfigurowane i umożliwiają lokalnego konta konta-dokonywania/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Jak włączyć KMSI

Wprowadź następujące zmiany w zasadach zaufania framework rozszerzenia.

## <a name="adding-a-content-definition-element"></a>Dodawanie elementu definicji zawartości 

`BuildingBlocks` Węzła pliku rozszerzenie musi zawierać `ContentDefinitions` elementu. 

1. W `ContentDefinitions` sekcji, zdefiniuj nowy `ContentDefinition` o identyfikatorze `api.signuporsigninwithkmsi`.
2. Nowy `ContentDefinition` musi zawierać `LoadUri`, `RecoveryUri` i `DataUri` w następujący sposób.
3. Identyfikator URI`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` jest zrozumiały identyfikatora urządzenia i wyświetlenie KMSI pole wyboru na stronach logowania. Upewnij się, że nie możesz zmienić tę wartość. 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>Dodaj dostawcę oświadczeń logowania konta lokalnego 

Logowanie za pomocą konta lokalnego można zdefiniować jako dostawcy oświadczeń, aby `<ClaimsProvider>` węzeł w pliku rozszerzenie zasad:

1. Otwórz plik rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego. 
2. Znajdź `<ClaimsProviders>` sekcji. Jeśli nie istnieje, należy go dodać w węźle głównym.
3. Pakiet startowy z [wprowadzenie](active-directory-b2c-get-started-custom.md) jest dostarczany z dostawcy oświadczeń "Lokalne logowanie za pomocą konta". 
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

### <a name="add-the-application-ids-to-your-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zdefiniowania zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku rozszerzenia (`TrustFrameworkExtensions.xml`):

1. W pliku rozszerzeń (TrustFrameworkExtensions.xml) można znaleźć elementu `<TechnicalProfile Id="login-NonInteractive">` i `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Zastąp wszystkie wystąpienia zmiennej `IdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md). Oto przykład:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Zastąp wszystkie wystąpienia zmiennej `ProxyIdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości serwera Proxy, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

4. Zapisz plik rozszerzenia.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Utwórz KMSI w podróży użytkownika włączone

Teraz należy dodać logowanie za pomocą konta lokalnego dostawcy oświadczeń do swoją podróż po użytkownik. 

1. Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).
2. Znajdź `<UserJourneys>` elementu i skopiować całą `<UserJourney>` węzeł, który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodać.
4. Wklej całą `<UserJourney>` węzeł, który został skopiowany jako element podrzędny elementu `<UserJourneys>` elementu.
5. Zmień nazwę identyfikator nowego podróży użytkownika (na przykład zmień nazwę `SignUpOrSignInWithKmsi`).
6. Na koniec w `OrchestrationStep 1` zmienić `ContentDefinitionReferenceId` do `api.signuporsigninwithkmsi` , zdefiniowanego w poprzednich krokach. Umożliwia to pole wyboru w podróży użytkownika. 
7. Po zakończeniu modyfikowania pliku rozszerzenia. Zapisz, a następnie przekaż ten plik. Upewnij się, że wszystkie sprawdzenia powiodło się.

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

## <a name="create-a-relying-party-rp-file"></a>Utwórz plik jednostki uzależnionej strona (RP)

Następnie zaktualizuj plik innych firm (RP) jednostki uzależnionej, który inicjuje podróży użytkownika, który został utworzony:

1. Utwórz kopię SignUpOrSignIn.xml w katalogu roboczym. Następnie zmień jego nazwę (na przykład SignUpOrSignInWithKmsi.xml).

2. Otwórz nowy plik i zaktualizuj `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` przy użyciu unikatowej wartości. To jest nazwa zasady (na przykład SignUpOrSignInWithKmsi).

3. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` do dopasowania `Id` nowe podróży użytkownika, który został utworzony (na przykład SignUpOrSignInWithKmsi).

4. KMSI jest skonfigurowana w `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** Określa, jak długo użytkownik pozostaje zalogowany. W poniższym przykładzie sesji KMSI automatycznie wygaśnie po 14 dniach od niezależnie od tego, jak często użytkownik wykonuje dyskretne uwierzytelnianie.

   Ustawienie `KeepAliveInDays` wartości 0 powoduje wyłączenie funkcji KMSI. Domyślnie ta wartość wynosi 0

6. Jeśli **`SessionExpiryType`** jest *stopniowe*, a następnie sesji KMSI zostanie rozszerzony przez 14 dni, za każdym razem, gdy użytkownik wykona dyskretne uwierzytelnianie.  Jeśli *stopniowe* jest zaznaczone, zaleca się zachować liczba dni do minimum. 

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

8. Aby przetestować zasad niestandardowych, który został przekazany, w witrynie Azure portal, przejdź do bloku zasady, a następnie kliknij **Uruchom teraz**.


## <a name="link-to-sample-policy"></a>Link do zasad próbki

Można znaleźć zasad przykładowe [tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








