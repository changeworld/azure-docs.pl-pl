---
title: Dodawanie własnego atrybutów do zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący przy użyciu właściwości rozszerzenia oraz atrybuty niestandardowe i uwzględniając je w interfejsie użytkownika.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 41c3db1c9a7295d939aa34a36f86c0dfa9fecd91
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797128"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Użyj niestandardowych atrybutów w niestandardowym profilu edytowanie zasad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule utworzysz niestandardowego atrybutu w katalogu usługi Azure Active Directory (Azure AD) B2C. Użyjesz tego nowego atrybutu jako oświadczenia niestandardowego w podróży użytkownika edycji profilu.

## <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie z instrukcjami w artykule [usługi Azure Active Directory B2C: Wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Wykorzystaj niestandardowe atrybuty do zbierania informacji o klientach w usłudze Azure AD B2C przy użyciu zasad niestandardowych
Katalog usługi Azure AD B2C jest powiązana z wbudowanego zestawu atrybutów. Należą do nich **imię**, **nazwisko**, **Miasto**, **kod pocztowy**, i **userPrincipalName**. Często zachodzi potrzeba tworzenia własnych atrybutów, takich jak te przykłady:
* Aplikacji przeznaczonych dla klientów ma zostać zachowany dla atrybutu, takich jak **LoyaltyNumber.**
* Dostawcy tożsamości ma identyfikator unikatowych użytkowników, takich jak **uniqueUserGUID** musi zostać zapisany.
* Podróż użytkownika niestandardowego ma zostać zachowany stan użytkownika, takich jak **migrationStatus**.

Usługa Azure AD B2C rozszerza zestaw atrybutów, przechowywane na każdym koncie użytkownika. Można odczytać i zapisać te atrybuty przy użyciu [interfejsu API usługi Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

Właściwości rozszerzenia rozszerzenia schematu obiektów użytkownika w katalogu. Warunki *właściwość rozszerzenia*, *atrybutu niestandardowego*, i *oświadczenia niestandardowego* się tak samo w kontekście tego artykułu. Nazwa różni się w zależności od kontekstu, takich jak zasad aplikacji lub obiektu.

Właściwości rozszerzenia tylko można zarejestrować w obiekcie aplikacji, nawet jeśli mogą one zawierać dane dla użytkownika. Właściwość jest dołączony do aplikacji. Obiekt aplikacji musi mieć dostęp do zapisu, można zarejestrować właściwości rozszerzenia. Właściwości rozszerzenia sto, we wszystkich typach i wszystkich aplikacji, można zapisać w żadnym konkretnym obiektem. Właściwości rozszerzenia są dodawane do typu katalogu docelowego i stają się natychmiast dostępne w dzierżawie katalogu usługi Azure AD B2C.
Jeśli aplikacja zostanie usunięta, te właściwości rozszerzenia, wraz z żadnych danych zawartych w nich dla wszystkich użytkowników, również zostaną usunięte. Jeśli właściwości rozszerzenia zostanie usunięty przez aplikację, zostanie ono usunięte obiektów katalogu docelowego, a wartości są usuwane.

Właściwości rozszerzenia istnieje tylko w kontekście zarejestrowaną aplikację w dzierżawie. Obiekt identyfikator tej aplikacji muszą być zawarte w **profilu technicznego** używa go.

>[!NOTE]
>Katalog usługi Azure AD B2C zwykle obejmują aplikację sieci web o nazwie `b2c-extensions-app`. Ta aplikacja jest używany głównie przez wbudowane zasady B2C dla oświadczenia niestandardowe utworzone w witrynie Azure portal. Firma Microsoft zaleca, tylko użytkownicy zaawansowani zarejestrować rozszerzenia usługi B2C niestandardowych zasad za pomocą tej aplikacji.  
Instrukcje znajdują się w **następne kroki** sekcję w tym artykule.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Utwórz nową aplikację do zapisania właściwości rozszerzenia

1. Otwórz sesję przeglądania i przejdź do [witryny Azure portal](https://portal.azure.com). Zaloguj się przy użyciu poświadczeń administracyjnych katalogu B2C, który chcesz skonfigurować.
2. Wybierz **usługi Azure Active Directory** w menu nawigacji po lewej stronie. Może być konieczne ją znaleźć, wybierając **więcej usług**.
3. Wybierz pozycję **Rejestracje aplikacji**. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
4. Podaj następujące wpisy:
    * Nazwa aplikacji sieci web: **WebApp-GraphAPI-DirectoryExtensions**.
    * Typ aplikacji: **Aplikacja/interfejs API sieci Web**.
    * Adres URL logowania: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Wybierz pozycję **Utwórz**.
6. Wybierz aplikację sieci web nowo utworzony.
7. Wybierz **ustawienia** > **wymagane uprawnienia**.
8. Wybierz interfejs API **Windows Azure Active Directory**.
9. Wprowadź znacznik wyboru w uprawnieniach aplikacji: **Odczytuj i zapisuj dane katalogu**. Następnie wybierz pozycję **Zapisz**.
10. Wybierz **udzielić uprawnień** i upewnij się, **tak**.
11. Skopiuj następujące identyfikatory do Schowka, a następnie zapisz je:
    * **Identyfikator aplikacji**. Przykład: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Obiekt o identyfikatorze**. Przykład: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Modyfikowanie zasad niestandardowych, aby dodać **ApplicationObjectId**

Po wykonaniu kroków w [usługi Azure Active Directory B2C: Wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md), pobranych i zmodyfikowanych [przykładowe pliki](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o nazwie **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml**, i **PasswordReset.xml**. W tym kroku możesz wprowadzić więcej modyfikacje tych plików.

* Otwórz **TrustFrameworkBase.xml** pliku i Dodaj `Metadata` jak pokazano w poniższym przykładzie. Wstaw identyfikator obiektu, zarejestrowane dla `ApplicationObjectId` wartość i identyfikator aplikacji, która rejestrowane dla `ClientId` wartość: 

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> Gdy **profilu technicznego** zapisuje po raz pierwszy właściwości nowo utworzonej rozszerzenia, może wystąpić błąd jednorazowego. Właściwość rozszerzenia jest tworzona przy pierwszym, który jest używany.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Używanie nowej właściwości rozszerzenia lub niestandardowego atrybutu w podróży użytkownika

1. Otwórz **ProfileEdit.xml** pliku.
2. Dodawanie oświadczenia niestandardowego `loyaltyId`. Umieszczając niestandardowe oświadczenia w `<RelyingParty>` elementu, jest on zawarty w tokenie dla aplikacji.
    
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Otwórz **TrustFrameworkExtensions.xml** pliku i Dodaj`<ClaimsSchema>` elementu i jego elementów podrzędnych do `BuildingBlocks` elementu:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Dodania tego samego `ClaimType` definicji **TrustFrameworkBase.xml**. Nie jest konieczne dodanie `ClaimType` definicji zarówno podstawowy, jak i plików rozszerzeń. Jednak dodać kolejne kroki `extension_loyaltyId` do **profili Technicalprofile** w pliku podstawowego. Dlatego weryfikacji zasad odrzuca przekazywania pliku podstawowego bez niego. Może być przydatne do śledzenia realizacji podróży użytkownika o nazwie **ProfileEdit** w **TrustFrameworkBase.xml** pliku. Wyszukaj podróży użytkownika o tej samej nazwie w edytorze. Sprawdź, czy kroku aranżacji 5 wywołuje **TechnicalProfileReferenceID = "SelfAsserted ProfileUpdate**. Wyszukiwanie i sprawdzić to **profilu technicznego** możliwość zapoznania się z przepływem.

5. Otwórz **TrustFrameworkBase.xml** pliku i Dodaj `loyaltyId` jako dane wejściowe i wyjściowe oświadczenia w **profilu technicznego SelfAsserted-ProfileUpdate**:

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. W **TrustFrameworkBase.xml** Dodaj `loyaltyId` oświadczenie do **profilu technicznego usługi AAD — UserWriteProfileUsingObjectId**. To dodawanie będzie się powtarzał wartość oświadczenia we właściwości rozszerzenia dla bieżącego użytkownika w katalogu:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. W **TrustFrameworkBase.xml** Dodaj `loyaltyId` oświadczenie do **profilu technicznego usługi AAD — UserReadUsingObjectId** można odczytać wartości atrybutu rozszerzenia, za każdym razem, gdy użytkownik loguje się. Do tej pory **profili Technicalprofile** zostały zmienione w usłudze flow tylko w przypadku kont lokalnych. Jeśli chcesz, aby nowy atrybut w usłudze flow społecznościowych lub federacyjnego konta inny zbiór **profili Technicalprofile** musi zostać zmienione. Zobacz **następne kroki** sekcji.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Otwórz blok usługi Azure AD B2C i przejdź do **struktura środowiska tożsamości** > **zasady niestandardowe**.
1. Wybierz zasady niestandardowe, który został przekazany. Wybierz **Uruchom teraz**.
1. Zarejestruj się przy użyciu adresu e-mail.

Identyfikator tokenu wysyłane z powrotem w aplikacji zawiera nową właściwość rozszerzenia jako oświadczenia niestandardowego poprzedzone **extension_loyaltyId**. Zobacz poniższy przykład:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Kolejne kroki

1. Dodaj nowe oświadczenie do przepływów zalogować się do kont społecznościowych, zmieniając następujące **profili Technicalprofile**. Kont społecznościowych i federacyjnego służą dwa **profili Technicalprofile** do logowania. Zapisują i odczytują dane użytkownika przy użyciu **alternativeSecurityId** jako locator obiektu użytkownika.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Użyj tego samego atrybuty rozszerzenia między zasadami wbudowanych i niestandardowych. Po dodaniu rozszerzenia lub niestandardowego, atrybuty za pośrednictwem portalu środowisko, te atrybuty są zarejestrowane przy użyciu **b2c-extensions-app** znajdujące się w każdej dzierżawy B2C. Wykonaj poniższe kroki, aby użyć rozszerzeń atrybuty w zasadach niestandardowych:

   a. W ramach dzierżawy B2C w witrynie portal.azure.com, przejdź do **usługi Azure Active Directory** i wybierz **rejestracje aplikacji**.  
   b. Znajdź swoje **b2c-extensions-app** i wybierz ją.  
   c. W obszarze **Essentials**, wprowadź **identyfikator aplikacji** i **obiektu o identyfikatorze**.  
   d. Należy je uwzględnić w swojej **typowe usługi AAD** metadane profilu technicznego:  

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Pozostają spójne za pomocą portalu. Korzystanie z interfejsu użytkownika portalu, zanim będziesz ich używać w niestandardowych zasad, aby utworzyć te atrybuty. Po utworzeniu atrybutu **ActivationStatus** w portalu możesz musi odwoływać się do niego w następujący sposób:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Informacje ogólne

Aby uzyskać więcej informacji na temat właściwości rozszerzenia, zobacz artykuł [rozszerzenia schematu katalogu | Pojęcia dotyczące interfejsu API Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * A **profilu technicznego** jest typ elementu lub funkcji, który definiuje nazwę punktu końcowego, metadane i protokołu. **Profilu technicznego** szczegóły wymiana oświadczeń, które wykonuje struktura środowiska tożsamości. Gdy ta funkcja jest wywoływana w kroku aranżacji lub z innego **profilu technicznego**, **InputClaims** i **OutputClaims** są dostarczane jako parametry przez obiekt wywołujący .  
> * Noszą rozszerzeń atrybuty w interfejsie API programu Graph przy użyciu konwencji `extension_ApplicationObjectID_attributename`.  
> * Zasady niestandardowe odnoszą się do rozszerzeń atrybuty jak **extension_attributename**. Ta dokumentacja umożliwia pominięcie **ApplicationObjectId** w formacie XML.
