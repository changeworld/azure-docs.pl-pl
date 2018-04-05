---
title: 'Usługa Azure Active Directory B2C: Dodać własne atrybutów niestandardowych zasad i używać w edycji profilu | Dokumentacja firmy Microsoft'
description: Przewodnik dotyczący przy użyciu właściwości rozszerzenia, atrybuty niestandardowe, w tym ich z interfejsu użytkownika
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: e0595a67b90e1be7bb992ef7bda4343e692d8957
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Usługa Azure Active Directory B2C: Tworzenie i używanie niestandardowych atrybutów w profilu niestandardowego edytowanie zasad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule Tworzenie niestandardowego atrybutu w katalogu usługi Azure AD B2C i jako oświadczenia niestandardowego w podróży użytkownika edycji profilu za pomocą tego nowego atrybutu.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki w artykule [wprowadzenie zasady niestandardowe](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Wykorzystaj niestandardowe atrybuty do zbierania informacji o klientach w usłudze Azure Active Directory B2C za pomocą zasad niestandardowych
Katalogu usługi Azure Active Directory (Azure AD) B2C jest dostarczany z wbudowanego zestawu atrybutów: podana imię, nazwisko, Miasto, kod pocztowy, userPrincipalName, itp.  Często muszą utworzyć własne atrybuty.  Na przykład:
* Aplikacja klienta uwzględniającym musi być zachowany atrybut, taki jak "LoyaltyNumber."
* Dostawca tożsamości ma identyfikator unikatowy użytkownika, który musi zostać zapisany, takie jak "uniqueUserGUID"."
* Przebieg użytkownika niestandardowego musi być zachowany stan użytkownika, takie jak "migrationStatus."

Z usługi Azure AD B2C można rozszerzyć zestaw atrybutów przechowywanych dla każdego konta użytkownika. Może także odczytywać i zapisywać te atrybuty za pomocą [interfejsu API usługi Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

Właściwości rozszerzenia rozszerzenie schematu obiektu użytkownika w katalogu.  Właściwość rozszerzenia warunki, atrybutu niestandardowego i oświadczenia niestandardowe zapoznaj się samo w kontekście tego artykułu, a nazwa może być różna w zależności od kontekstu (aplikacji, obiekt zasad).

Właściwości rozszerzenia mogą być rejestrowane tylko dla obiektu aplikacji, nawet jeśli dane mogą zawierać dla użytkownika. Właściwość jest dołączony do aplikacji. Obiekt aplikacji muszą mieć uprawnienie zapisu do rejestru właściwość rozszerzenia. 100 właściwości rozszerzenia (za pośrednictwem wszystkich typów i wszystkie aplikacje) mogą być zapisywane na żadnym pojedynczym obiektem. Właściwości rozszerzenia są dodawane na typ docelowy katalogu i stanie się dostępny natychmiast w dzierżawie usługi Azure AD B2C w katalogu.
Jeśli aplikacja zostanie usunięty, są również usuwane te właściwości rozszerzenia wraz z danymi znajdującymi się w nich dla wszystkich użytkowników. Jeśli właściwość rozszerzenia zostanie usunięty przez aplikację, zostanie ono usunięte obiekty katalogu docelowego i wartości usunięte.

Właściwości rozszerzenia istnieje tylko w kontekście zarejestrowaną aplikację w dzierżawie. Identyfikator obiektu tej aplikacji muszą być zawarte w TechnicalProfile, który go używać.

>[!NOTE]
>Katalog usługi Azure AD B2C zwykle obejmuje aplikację sieci Web o nazwie `b2c-extensions-app`.  Ta aplikacja jest używany głównie za pomocą zasad wbudowany b2c dla oświadczenia niestandardowe utworzone za pośrednictwem portalu Azure.  Za pomocą tej aplikacji do zarejestrowania rozszerzenia zasad niestandardowych b2c jest zalecane tylko dla użytkowników zaawansowanych.  Odpowiednie instrukcje znajdują się w sekcji kolejne kroki w tym artykule.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Tworzenie nowej aplikacji do zapisania właściwości rozszerzenia

1. Otwórz sesję przeglądania i przejdź do [portalu Azure](https://portal.azure.com) i zaloguj się przy użyciu poświadczeń administracyjnych katalogu B2C, chcesz skonfigurować.
1. Kliknij przycisk **usługi Azure Active Directory** w menu nawigacji po lewej stronie. Konieczne może być go znaleźć, wybierając więcej usług >.
1. Wybierz **rejestracji aplikacji** i kliknij przycisk **nowej rejestracji aplikacji**
1. Podaj następujące wpisy zalecane:
  * Określ nazwę dla aplikacji sieci web: **aplikacji sieci Web-GraphAPI-DirectoryExtensions**
  * Typ aplikacji: aplikacja/interfejs API sieci Web
  * Sign-on URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Wybierz ** utworzyć. Pomyślne zakończenie pojawia się w **powiadomienia**
1. Wybierz aplikację sieci web nowo utworzony: **aplikacji sieci Web-GraphAPI-DirectoryExtensions**
1. Wybierz ustawienia: **wymagane uprawnienia**
1. Wybierz interfejs API **systemu Windows Azure Active Directory**
1. Należy zaznaczyć uprawnienia aplikacji: **Odczyt i zapis danych katalogu**, i **Zapisz**
1. Wybierz **udzielić uprawnień** i Potwierdź **tak**.
1. Skopiuj do Schowka i Zapisz następujące identyfikatory z aplikacji sieci Web-GraphAPI-DirectoryExtensions > Ustawienia > Właściwości >
*  **Identyfikator aplikacji** . Przykład: `103ee0e6-f92d-4183-b576-8c3739027780`
* **Obiekt o identyfikatorze**. Przykład: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modyfikowanie zasad niestandardowych do dodania ApplicationObjectId

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
><TechnicalProfile Id="AAD-Common"> Jest określana jako "wspólne", ponieważ jego elementy są uwzględnione w i użyć ponownie w wszystkie usługi Azure Active Directory TechnicalProfiles przy użyciu elementu: `<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>Podczas TechnicalProfile zapisuje po raz pierwszy właściwość nowo utworzonego rozszerzenia, może wystąpić błąd jednorazowego.  Właściwość rozszerzenia jest tworzony podczas pierwszego, który jest używany.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Przy użyciu nowego rozszerzenia właściwości / atrybutu niestandardowego w podróży użytkownika


1. Otwieranie pliku Party(RP) jednostki uzależnionej, który opisuje zasady edytować przebieg użytkownika.  Jest uruchamiany,, może być wskazane w celu pobrania z już skonfigurowany wersji pliku RP PolicyEdit bezpośrednio z sekcji zasady niestandardowe Azure B2C w portalu Azure.  Alternatywnie można otworzyć pliku XML z folderu magazynu.
2. Dodawanie oświadczenia niestandardowego `loyaltyId`.  Dodając niestandardowe oświadczenia w `<RelyingParty>` elementu, jest przekazywany jako parametr do UserJourney TechnicalProfiles i uwzględnione w tokenie dla aplikacji.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Dodawanie definicji oświadczenia do pliku rozszerzenie zasad `TrustFrameworkExtensions.xml` wewnątrz `<ClaimsSchema>` element, jak pokazano.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Dodaj takie same oświadczeń definicji do pliku podstawowego zasad `TrustFrameworkBase.xml`.  
>Dodawanie `ClaimType` definicji w zarówno dla typu podstawowego, jak i plik rozszerzenia zwykle nie jest konieczne, jednak ponieważ następnych krokach zostaną dodane extension_loyaltyId do TechnicalProfiles w pliku podstawowego, modułu sprawdzania poprawności zasad spowoduje odrzucenie przekazywania pliku podstawowego bez niego.
>Może to być przydatne do śledzenia realizacji przebieg użytkownika o nazwie "ProfileEdit" w pliku TrustFrameworkBase.xml.  Wyszukaj przebieg użytkownika o tej samej nazwie w edytorze i sprawdź, czy aranżacji krok 5 wywołuje TechnicalProfileReferenceID = "SelfAsserted ProfileUpdate".  Wyszukiwanie i sprawdzić to TechnicalProfile, aby zapoznać się z przepływem.
5. Dodaj loyaltyId zgodnie z oświadczeń przychodzących i wychodzących w TechnicalProfile "SelfAsserted ProfileUpdate"
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

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Dodaj oświadczenie w TechnicalProfile "AAD-UserWriteProfileUsingObjectId", aby zachować wartości oświadczenia we właściwości rozszerzenia dla bieżącego użytkownika w katalogu.
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
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Dodaj oświadczenie w TechnicalProfile "AAD-UserReadUsingObjectId" można odczytać wartości atrybutu rozszerzenia za każdym razem, gdy użytkownik loguje. Dotychczas TechnicalProfiles zostały zmienione w strumieniu tylko kont lokalnych.  Razie nowy atrybut przepływu konto społecznego/federacyjnych inny zestaw TechnicalProfiles musi zostać zmienione. Zobacz następne kroki.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
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
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>IncludeTechnicalProfile element dodaje wszystkie elementy wspólnych AAD tego TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Testowanie zasad niestandardowych za pomocą "Uruchom teraz"
1. Otwórz **bloku usługi Azure AD B2C** i przejdź do **tożsamości środowiska Framework > zasady niestandardowe**.
1. Wybierz zasady niestandardowe przekazywane i kliknij przycisk **Uruchom teraz** przycisku.
1. Należy zalogowanie przy użyciu adresu e-mail.

Token identyfikatora wysyłane powrót do aplikacji zawiera nową właściwość rozszerzenia jako oświadczenia niestandardowego poprzedzony extension_loyaltyId. Zobacz przykład.

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

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Dodaj nowe oświadczenie do przepływów dla logowania do kont społecznościowych, zmieniając TechnicalProfiles wymienionych poniżej. Te dwie TechnicalProfiles są używane przez federacyjne/społecznego konto logowania do zapisu i odczytu danych użytkownika za pomocą alternativeSecurityId jako lokalizacji obiektu użytkownika.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Przy użyciu tego samego atrybuty rozszerzenia między zasadami wbudowanych i niestandardowych.
Po dodaniu rozszerzenia atrybutów (alias niestandardowych atrybutów) za pośrednictwem portalu środowisko te atrybuty są rejestrowane przy użyciu ** b2c rozszerzeń aplikacji w każdej dzierżawy b2c.  Aby użyć te atrybuty rozszerzenia w zasadach niestandardowych:
1. W ramach dzierżawy usługi b2c w portal.azure.com, przejdź do **usługi Azure Active Directory** i wybierz **rejestracji aplikacji**
2. Znajdź użytkownika **b2c rozszerzeń aplikacji** i wybierz ją
3. W obszarze "Essentials" rekordu **identyfikator aplikacji** i **identyfikator obiektu:**
4. Należy uwzględnić je w metadanych programu AAD typowe techniczne profilu podobnie jak w następujący sposób:

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

Aby zachować spójność z obsługi portalu, Utwórz te atrybuty przy użyciu interfejsu użytkownika portalu *przed* używane w niestandardowych zasad.  Podczas tworzenia atrybutu "ActivationStatus" w portalu, użytkownik musi odwoływać się do niego w następujący sposób:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Informacje ogólne

* A **techniczne profilu (TP)** jest typem elementu, który można traktować jako *funkcja* definiuje nazwę punktu końcowego, jego metadanych, protokół, a szczegóły programu exchange oświadczeń który tożsamości Należy wykonać czynności Framework.  Gdy to *funkcja* jest wywoływana w kroku aranżacji lub z innego TechnicalProfile, InputClaims i OutputClaims są przekazywane jako parametry przez obiekt wywołujący.


* Pełna oczyszczania we właściwościach rozszerzenia, zobacz artykuł [rozszerzenia SCHEMATU katalogu | POJĘCIA DOTYCZĄCE INTERFEJSU API PROGRAMU GRAPH](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Atrybuty rozszerzenia interfejsu API programu Graph są nazywane zgodnie z Konwencją `extension_ApplicationObjectID_attributename`. Zasady niestandardowe nazywać atrybuty rozszerzenia extension_attributename, w związku z tym pominięcie ApplicationObjectId w pliku XML
