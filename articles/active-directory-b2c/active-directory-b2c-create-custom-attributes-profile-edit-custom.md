---
title: Dodawanie własnego atrybutów do zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Wskazówki na temat używania właściwości rozszerzenia, atrybuty niestandardowe i uwzględniając je w interfejsie użytkownika.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ecde4d8cd8ee454290b16b640ba05d310cf348fe
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450243"
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Usługa Azure Active Directory B2C: Tworzenie i używanie atrybutów niestandardowych, które w niestandardowym profilu edytowanie zasad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule Tworzenie niestandardowego atrybutu w katalogu usługi Azure AD B2C i jako oświadczenia niestandardowego w podróży użytkownika edycji profilu za pomocą tego nowego atrybutu.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w artykule [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Wykorzystaj niestandardowe atrybuty do zbierania informacji o klientach w usłudze Azure Active Directory B2C za pomocą zasad niestandardowych
Katalogu usługi Azure Active Directory (Azure AD) B2C zawiera zestaw wbudowanych atrybutów: podana imię, nazwisko, Miasto, kod pocztowy, userPrincipalName, itp.  Często muszą utworzyć własne atrybuty.  Na przykład:
* Aplikacji przeznaczonych dla klientów ma zostać zachowany atrybutu, takiego jak "LoyaltyNumber."
* Dostawcy tożsamości ma identyfikator unikatowy użytkownika, który musi zostać zapisany, takie jak "uniqueUserGUID"."
* Podróż użytkownika niestandardowego ma zostać zachowany stan użytkownika, takie jak "migrationStatus."

Za pomocą usługi Azure AD B2C można rozszerzyć zbiór atrybutów przechowywanych dla każdego konta użytkownika. Można odczytać i zapisać te atrybuty przy użyciu [interfejsu API usługi Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

Właściwości rozszerzenia rozszerzenia schematu obiektów użytkownika w katalogu.  Właściwość rozszerzenia warunki, atrybut niestandardowy i oświadczenia niestandardowego zapoznaj się tak samo w kontekście tego artykułu i nazwa różni się w zależności od kontekstu (aplikacji, obiekt zasad).

Właściwości rozszerzenia tylko można zarejestrować w obiekcie aplikacji, nawet jeśli mogą one zawierać dane dla użytkownika. Właściwość jest dołączony do aplikacji. Obiekt aplikacji musi otrzymać dostęp do zapisu, można zarejestrować właściwości rozszerzenia. Do każdego pojedynczego obiektu można zapisać 100 właściwości rozszerzenia (we wszystkich typach i wszystkich aplikacji). Właściwości rozszerzenia są dodawane do typu katalogu docelowego i staje się natychmiast dostępne w dzierżawie katalogu usługi Azure AD B2C.
Jeśli aplikacja zostanie usunięta, te właściwości rozszerzenia, wraz z żadnych danych zawartych w nich dla wszystkich użytkowników, również zostaną usunięte. Jeśli właściwości rozszerzenia zostanie usunięty przez aplikację, jest ona usuwana obiektów katalogu docelowego i wartości, który został usunięty.

Właściwości rozszerzenia istnieje tylko w kontekście zarejestrowaną aplikację w dzierżawie. Identyfikator obiektu aplikacji muszą być uwzględnione w profilu TechnicalProfile, który go używać.

>[!NOTE]
>Katalog usługi Azure AD B2C zwykle obejmują aplikację sieci Web o nazwie `b2c-extensions-app`.  Ta aplikacja jest używany głównie przez wbudowane zasady b2c dla oświadczenia niestandardowe utworzone w witrynie Azure portal.  Za pomocą tej aplikacji do zarejestrowania rozszerzenia niestandardowe zasady usługi b2c jest zalecane tylko dla zaawansowanych użytkowników.  Odpowiednie instrukcje znajdują się w sekcji kolejne kroki, w tym artykule.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Tworzenie nowej aplikacji do zapisania właściwości rozszerzenia

1. Otwórz sesję przeglądania i przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się przy użyciu poświadczeń administracyjnych danego katalogu B2C ma zostać skonfigurowany.
2. Kliknij przycisk **usługi Azure Active Directory** w menu nawigacji po lewej stronie. Konieczne może być ją znaleźć, wybierając pozycję więcej usług >.
3. Wybierz **rejestracje aplikacji** i kliknij przycisk **rejestrowanie nowej aplikacji**
4. Podaj następujące wpisy zalecane:
    * Określ nazwę dla aplikacji sieci web: **aplikacji sieci Web-GraphAPI-DirectoryExtensions**
    * Typ aplikacji: aplikacja/interfejs API sieci Web
    * Logowanie jednokrotne URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
5. Wybierz pozycję **Utwórz**.
6. Wybierz aplikację sieci web nowo utworzony.
7. Wybierz **ustawienia** > **wymagane uprawnienia**.
8. Wybierz interfejs API **Windows Azure Active Directory**.
9. Umieść znacznik wyboru w uprawnieniach aplikacji: **odczytu i zapisu danych katalogu**, a następnie wybierz pozycję **Zapisz**.
10. Wybierz **udzielić uprawnień** i upewnij się, **tak**.
11. Skopiuj do Schowka, a następnie zapisz następujące identyfikatory:
    * **Identyfikator aplikacji** . Przykład: `103ee0e6-f92d-4183-b576-8c3739027780`
    * **Obiekt o identyfikatorze**. Przykład: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modyfikowanie zasad niestandardowych, aby dodać ApplicationObjectId

Jeśli ukończono procedurę [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md), pobranych i zmodyfikowanych [pliki](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o nazwie *TrustFrameworkBase.xml*,  *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, i *PasswordReset.xml*. W poniższych krokach można nadal wprowadzać modyfikacje do tych plików.

1. Otwórz *TrustFrameworkBase.xml* pliku i Dodaj `Metadata` jak pokazano w poniższym przykładzie. Wstaw identyfikator obiektu, zarejestrowane dla `ApplicationObjectId` wartość i identyfikator aplikacji, która rejestrowane dla `ClientId` wartość: 

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

>[!NOTE]
>Gdy profilu technicznego zapisuje po raz pierwszy do właściwości nowo utworzonej rozszerzenia, może wystąpić błąd jednorazowe. Właściwość rozszerzenia jest tworzona przy pierwszym, który jest używany.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Korzystając z nowej właściwości rozszerzenia / niestandardowego atrybutu w podróży użytkownika

1. Otwórz *ProfileEdit.xml* pliku.
2. Dodawanie oświadczenia niestandardowego `loyaltyId`.  Umieszczając niestandardowe oświadczenia w `<RelyingParty>` elementu, jest on zawarty w tokenu dla aplikacji.
    
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

3. Otwórz *TrustFrameworkExtensions.xml* pliku i Dodaj`<ClaimsSchema>` elementu i jego elementów podrzędnych do `BuildingBlocks` elementu:

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

4. Dodania tego samego `ClaimType` definicji *TrustFrameworkBase.xml*. Dodawanie `ClaimType` definicji zarówno podstawowy, jak i plik rozszerzenia zwykle nie jest jednak konieczne, ponieważ spowoduje to dodanie następne kroki `extension_loyaltyId` do profili Technicalprofile w pliku podstawowego, moduł sprawdzania zasad spowoduje odrzucenie przekazywania pliku podstawowego bez go. Może być przydatne do śledzenia realizacji podróży użytkownika o nazwie "ProfileEdit" w *TrustFrameworkBase.xml* pliku.  Wyszukaj podróży użytkownika o takiej samej nazwie w edytorze, a następnie sprawdź, czy aranżacji kroku 5 wywołuje TechnicalProfileReferenceID = "SelfAsserted ProfileUpdate".  Wyszukiwanie i sprawdzić tego profilu technicznego, aby zapoznać się z przepływem.

5. Otwórz *TrustFrameworkBase.xml* pliku i Dodaj `loyaltyId` jako dane wejściowe i wyjściowe oświadczenia w profilu TechnicalProfile "SelfAsserted ProfileUpdate":

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

6. W *TrustFrameworkBase.xml* Dodaj `loyaltyId` oświadczenie do profilu technicznego "AAD-UserWriteProfileUsingObjectId", aby zachować wartość oświadczenia we właściwości rozszerzenia dla bieżącego użytkownika w katalogu:

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

7. W *TrustFrameworkBase.xml* Dodaj `loyaltyId` oświadczenie do profilu technicznego "AAD-UserReadUsingObjectId" ma zostać odczytana wartość atrybutu rozszerzenia, za każdym razem, gdy użytkownik loguje się. Dotychczas profili Technicalprofile zostały zmienione w usłudze flow tylko w przypadku kont lokalnych.  W razie nowy atrybut jest w usłudze flow ubezpieczenia/niepowodzenia federacyjnych konta inny zbiór profili Technicalprofile musi zostać zmienione. Zobacz następne kroki.

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

1. Otwórz **bloku usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości > zasady niestandardowe**.
1. Wybierz zasady niestandardowe, które przekazane i kliknij przycisk **Uruchom teraz** przycisku.
1. Powinien móc zarejestrować się przy użyciu adresu e-mail.

Identyfikator tokenu wysyłane z powrotem w aplikacji zawiera nową właściwość rozszerzenia jako oświadczenia niestandardowego poprzedzone extension_loyaltyId. Zobacz przykład.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Dodaj nowe oświadczenie do przepływów w dotyczące logowania się do konta w sieci społecznościowej, zmieniając profili Technicalprofile wymienione poniżej. Tych dwóch profili Technicalprofile są używane przez konto ubezpieczenia/niepowodzenia federacyjnych logowań do zapisu i odczytu danych użytkownika za pomocą alternativeSecurityId jako locator obiektu użytkownika.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Przy użyciu tego samego atrybuty rozszerzenia między zasadami wbudowanych i niestandardowych.
Podczas dodawania atrybutów rozszerzenia (zwane również niestandardowych atrybutów) za pośrednictwem portalu środowisko te atrybuty są rejestrowane przy użyciu ** b2c-extensions-app, znajdującą się w każdej dzierżawy b2c.  Aby użyć tych atrybutów rozszerzenia w zasadach niestandardowych:
1. W ramach dzierżawy b2c w witrynie portal.azure.com, przejdź do **usługi Azure Active Directory** i wybierz **rejestracje aplikacji**
2. Znajdź swoje **b2c-extensions-app** i wybierz ją
3. W obszarze "Podstawowe" rekord **identyfikator aplikacji** i **identyfikator obiektu:**
4. Należy uwzględnić je w swojej Technical Preview, które są typowe dla usługi AAD metadanych profilu, takich jak w następujący sposób:

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

Aby zachować spójność z środowisko portalu, utworzyć te atrybuty przy użyciu interfejsu użytkownika portalu *przed* będziesz ich używać w niestandardowych zasad.  Po utworzeniu atrybutu "ActivationStatus" w portalu możesz musi odwoływać się do niego w następujący sposób:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Informacje ogólne

* A **profilu Technical Preview (TP)** to typ elementu, który może być uważane za *funkcja* definiuje nazwę punktu końcowego, jego metadane, protokół i szczegóły wymiana oświadczeń, tożsamości Struktura środowiska, należy wykonać.  Gdy to *funkcja* jest wywoływana w kroku aranżacji lub z innego profilu technicznego, InputClaims i OutputClaims są dostarczane jako parametry przez obiekt wywołujący.


* Pełne traktowanie właściwości rozszerzenia, zobacz artykuł [rozszerzenia SCHEMATU katalogu | POJĘCIA DOTYCZĄCE INTERFEJSU API PROGRAMU GRAPH](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Atrybuty rozszerzenia w interfejsie API programu Graph noszą przy użyciu konwencji `extension_ApplicationObjectID_attributename`. Zasady niestandardowe nazywać rozszerzeń atrybuty extension_attributename, w związku z tym pomijanie ApplicationObjectId w pliku XML
