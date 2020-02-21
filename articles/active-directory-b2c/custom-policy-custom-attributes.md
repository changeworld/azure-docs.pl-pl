---
title: Dodawanie własnych atrybutów do zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przewodnik dotyczący używania właściwości rozszerzenia i atrybutów niestandardowych i dołączania ich do interfejsu użytkownika.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c2361192a612cfd92003b1e3c36e85dbbd9090bb
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482772"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Użyj atrybutów niestandardowych w niestandardowych zasadach edytowania profilu

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano tworzenie niestandardowego atrybutu w katalogu Azure Active Directory B2C (Azure AD B2C). Ten nowy atrybut będzie używany jako niestandardowa w profilu Edycja podróży użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w artykule [Azure Active Directory B2C: wprowadzenie do zasad niestandardowych](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Używanie atrybutów niestandardowych do zbierania informacji o klientach w Azure AD B2C przy użyciu zasad niestandardowych
Katalog Azure AD B2C zawiera wbudowany zestaw atrybutów. Przykłady to **imię**i nazwisko, **imię**, **miasto**, **Kod pocztowy**i **userPrincipalName**. Często trzeba utworzyć własne atrybuty, takie jak następujące przykłady:
* Aplikacja mająca dostęp do klienta musi zachować dostęp do atrybutu, takiego jak **LoyaltyNumber.**
* Dostawca tożsamości ma unikatowy identyfikator użytkownika, taki jak **uniqueUserGUID** , który musi zostać zapisany.
* Niestandardowa podróż użytkownika wymaga utrzymania stanu użytkownika, takiego jak **migrationStatus**.

Azure AD B2C rozszerza zestaw atrybutów przechowywanych na poszczególnych kontach użytkowników. Można również odczytywać i zapisywać te atrybuty przy użyciu [interfejsu API Microsoft Graph](manage-user-accounts-graph-api.md).

Właściwości rozszerzenia rozszerzają schemat obiektów użytkownika w katalogu. *Właściwość rozszerzenia*terminów, *atrybut niestandardowy*i *niestandardowa* , odwołują się do tego samego elementu w kontekście tego artykułu. Nazwa różni się w zależności od kontekstu, takiego jak aplikacja, obiekt lub zasady.

Właściwości rozszerzenia można zarejestrować tylko w obiekcie aplikacji, chociaż mogą zawierać dane dla użytkownika. Właściwość jest dołączona do aplikacji. Obiekt aplikacji musi mieć dostęp do zapisu w celu zarejestrowania właściwości rozszerzenia. Setki właściwości rozszerzenia, między wszystkimi typami i wszystkimi aplikacjami, można zapisywać do dowolnego pojedynczego obiektu. Właściwości rozszerzenia są dodawane do typu katalogu docelowego i stają się natychmiast dostępne w dzierżawie usługi Azure AD B2C Directory.
Jeśli aplikacja zostanie usunięta, te właściwości rozszerzenia wraz z wszelkimi zawartymi w nich danymi dla wszystkich użytkowników również zostaną usunięte. Jeśli właściwość rozszerzenia zostanie usunięta przez aplikację, zostanie usunięta z obiektów katalogu docelowego i wartości są usuwane.

Właściwości rozszerzenia istnieją tylko w kontekście zarejestrowanej aplikacji w dzierżawie. Identyfikator obiektu aplikacji musi być uwzględniony w **profilu technicznym** , który go używa.

>[!NOTE]
>Katalog Azure AD B2C zwykle zawiera aplikację sieci Web o nazwie `b2c-extensions-app`. Ta aplikacja jest używana głównie przez wbudowane zasady B2C dla niestandardowych oświadczeń utworzonych za pośrednictwem Azure Portal. Zalecamy, aby tylko zaawansowani użytkownicy rejestrowali rozszerzenia dla zasad niestandardowych B2C za pomocą tej aplikacji.
Instrukcje znajdują się w sekcji **następne kroki** w tym artykule.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Utwórz nową aplikację do przechowywania właściwości rozszerzenia

1. Otwórz sesję przeglądania i przejdź do [Azure Portal](https://portal.azure.com). Zaloguj się przy użyciu poświadczeń administracyjnych katalogu B2C, który chcesz skonfigurować.
2. Wybierz pozycję **Azure Active Directory** w menu nawigacji po lewej stronie. Może być konieczne jego znalezienie, wybierając pozycję **więcej usług**.
3. Wybierz pozycję **Rejestracje aplikacji**. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
4. Podaj następujące wpisy:
    * Nazwa aplikacji sieci Web: **webapp-GraphAPI-DirectoryExtensions**.
    * Typ aplikacji: **aplikacja sieci Web/interfejs API**.
    * Adres URL logowania: **https://{Dzierżawca}. onmicrosoft. com/webapp-GraphAPI-DirectoryExtensions**.
5. Wybierz pozycję **Utwórz**.
6. Wybierz nowo utworzoną aplikację sieci Web.
7. Wybierz pozycję **Ustawienia** , > **wymagane uprawnienia**.
8. Wybierz Azure Active Directory interfejsu API **systemu Windows**.
9. Wprowadź znacznik wyboru w obszarze uprawnienia aplikacji: **Odczytuj i zapisuj dane katalogu**. Następnie wybierz pozycję **Zapisz**.
10. Wybierz pozycję **Udziel uprawnień** i Potwierdź opcję **tak**.
11. Skopiuj następujące identyfikatory do schowka i Zapisz je:
    * **Identyfikator aplikacji**. Przykład: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Identyfikator obiektu**. Przykład: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Modyfikowanie zasad niestandardowych w celu dodania **ApplicationObjectId**

Po wykonaniu czynności opisanych w [Azure Active Directory B2C: wprowadzenie do zasad niestandardowych](custom-policy-get-started.md), pobrane i zmodyfikowane [przykładowe pliki](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o nazwie **TrustFrameworkBase. XML**, **TrustFrameworkExtensions. XML**, **SignUpOrSignin. XML**, **ProfileEdit. XML**i **PasswordReset. XML**. W tym kroku wprowadzisz dalsze modyfikacje tych plików.

* Otwórz plik **TrustFrameworkBase. XML** i dodaj sekcję `Metadata`, jak pokazano w poniższym przykładzie. Wstaw identyfikator obiektu, który został wcześniej zarejestrowany dla wartości `ApplicationObjectId` i identyfikator aplikacji zarejestrowanej dla wartości `ClientId`:

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
> Gdy **profilu technicznym** zapisuje po raz pierwszy do nowo utworzonej właściwości rozszerzenia, może wystąpić jednorazowy błąd. Właściwość rozszerzenia jest tworzona przy pierwszym użyciu.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Korzystanie z nowej właściwości rozszerzenia lub atrybutu niestandardowego w podróży użytkownika

1. Otwórz plik **ProfileEdit. XML** .
2. Dodaj `loyaltyId`niestandardowego żądania. Poprzez dołączenie niestandardowego żądania do elementu `<RelyingParty>`, zostanie on uwzględniony w tokenie dla aplikacji.

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

3. Otwórz plik **TrustFrameworkExtensions. XML** i dodaj element`<ClaimsSchema>` i jego elementy podrzędne do elementu `BuildingBlocks`:

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

4. Dodaj tę samą definicję `ClaimType` do **TrustFrameworkBase. XML**. Nie trzeba dodawać definicji `ClaimType` zarówno w pliku podstawowym, jak i w plikach rozszerzeń. Jednak następne kroki umożliwiają dodanie `extension_loyaltyId` do **TechnicalProfiles** w pliku podstawowym. Dlatego moduł walidacji zasad odrzuca przekazywanie pliku podstawowego bez niego. Przydatne może być śledzenie wykonywania podróży użytkownika o nazwie **ProfileEdit** w pliku **TrustFrameworkBase. XML** . Wyszukaj podróż użytkownika o tej samej nazwie w edytorze. Należy zauważyć, że aranżacja krok 5 wywołuje **TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate**. Wyszukaj i zbadaj ten **profilu technicznym** , aby zapoznać się z przepływem.

5. Otwórz plik **TrustFrameworkBase. XML** i Dodaj `loyaltyId` jako wystąpienie danych wejściowych i wyjściowych w **profilu technicznym SelfAsserted-ProfileUpdate**:

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

6. W pliku **TrustFrameworkBase. XML** dodaj zastrzeżenie `loyaltyId` do **profilu technicznym AAD-UserWriteProfileUsingObjectId**. To dodanie spowoduje zachowanie wartości w polu właściwości rozszerzenia dla bieżącego użytkownika w katalogu:

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

7. W pliku **TrustFrameworkBase. XML** dodaj zgłoszenie `loyaltyId` do **profilu technicznym AAD-UserReadUsingObjectId** , aby odczytać wartość atrybutu rozszerzenia za każdym razem, gdy użytkownik zaloguje się. Do tej pory **TechnicalProfiles** został zmieniony tylko w przepływie kont lokalnych. Jeśli chcesz utworzyć nowy atrybut w przepływie konta społecznościowego lub federacyjnego, należy zmienić inny zestaw **TechnicalProfiles** . Zobacz sekcję **następne kroki** .

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

1. Otwórz blok Azure AD B2C i przejdź do **struktury programu Identity Experience** > **zasad niestandardowych**.
1. Wybierz przekazane zasady niestandardowe. Wybierz pozycję **Uruchom teraz**.
1. Utwórz konto przy użyciu adresu e-mail.

Token identyfikatora wysłany z powrotem do aplikacji zawiera nową właściwość rozszerzenia jako niestandardową rolę, poprzedzoną **extension_loyaltyId**. Zobacz poniższy przykład:

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

## <a name="next-steps"></a>Następne kroki

1. Dodaj nowe zastrzeżenie do przepływów, aby zalogować się do kont społecznościowych, zmieniając następujące **TechnicalProfiles**. Konta społecznościowe i federacyjne używają tych dwóch **TechnicalProfiles** do logowania. Zapisują i odczytują dane użytkowników przy użyciu **alternativeSecurityId** jako lokalizatora obiektu użytkownika.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Użyj tych samych atrybutów rozszerzenia między wbudowanymi i niestandardowymi zasadami. W przypadku dodawania rozszerzenia lub niestandardowych atrybutów za pośrednictwem środowiska portalu te atrybuty są rejestrowane przy użyciu **B2C-Extensions-App** , która istnieje w każdej dzierżawie B2C. Wykonaj następujące kroki, aby użyć atrybutów rozszerzenia w niestandardowych zasadach:

   a. W dzierżawie B2C w portal.azure.com przejdź do **Azure Active Directory** i wybierz pozycję **rejestracje aplikacji**.
   b. Znajdź **aplikację B2C-Extensions-App** i wybierz ją.
   c. W obszarze **Essentials**wprowadź **Identyfikator aplikacji** i **Identyfikator obiektu**.
   d. Uwzględnij je w metadanych usługi **AAD-Common** profilu technicznym:

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

3. Zadbaj o to, aby zachować zgodność z portalem. Te atrybuty można utworzyć przy użyciu interfejsu użytkownika portalu przed użyciem ich w niestandardowych zasadach. Podczas tworzenia atrybutu **ActivationStatus** w portalu należy odwołać się do niego w następujący sposób:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Dokumentacja

Aby uzyskać więcej informacji na temat właściwości rozszerzenia, zobacz artykuł [Dodawanie niestandardowych danych do zasobów przy użyciu rozszerzeń](https://docs.microsoft.com/graph/extensibility-overview).

> [!NOTE]
> * **Profilu technicznym** to typ elementu lub funkcja, która definiuje nazwę, metadane i protokół punktu końcowego. **Profilu technicznym** szczegóły wymiany oświadczeń wykonywanych przez strukturę środowiska tożsamości. Gdy ta funkcja jest wywoływana w kroku aranżacji lub z innego **profilu technicznym**, **InputClaims** i **OutputClaims** są udostępniane jako parametry przez obiekt wywołujący.
> * Atrybuty rozszerzenia w interfejs API programu Graph są nazwane przy użyciu konwencji `extension_ApplicationObjectID_attributename`.
> * Zasady niestandardowe odwołują się do atrybutów rozszerzenia jako **extension_attributename**. To odwołanie pomija **ApplicationObjectId** w XML.
> * Musisz określić identyfikator atrybutu w następującym formacie, **extension_attributename** wszędzie tam, gdzie jest przywoływany.
