---
title: Interfejs API REST oświadczeń wymiany jako aranżacji krok w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat dotyczący usługi Azure Active Directory B2C niestandardowych zasad integracji z interfejsem API.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/24/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0b8fff2e7a47ad84c146a02fb09b64931398b208
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34710784"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Wskazówki: Integrowanie wymiany oświadczenia interfejsu API REST w podróży użytkownika usługi Azure AD B2C krok aranżacji

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tożsamość środowiska Framework (IEF) źródłową Azure Active Directory B2C (Azure AD B2C) umożliwia deweloperowi tożsamości integracji interakcji z interfejsu API RESTful w podróży użytkownika.  

Na końcu tego przewodnika można utworzyć przebieg użytkownika usługi Azure AD B2C, która współdziała z usług RESTful.

IEF wysyła dane w oświadczeniach i odbiera dane z powrotem w oświadczeniach. Wymiana oświadczenia interfejsu API REST:

- Można zaprojektować krok aranżacji.
- Może wyzwolić zewnętrznego działania. Na przykład on rejestrować zdarzenie w zewnętrznej bazy danych.
- Umożliwia pobieranie wartości, a następnie zapisać ją w bazie danych użytkownika.

Odebrano oświadczeń można użyć później, aby zmienić sposób wykonywania.

Można również projektować interakcji jako profil sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [wskazówki: integrowanie interfejsu API REST oświadczeń wymiany w podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika](active-directory-b2c-rest-api-validation-custom.md).

Scenariusz jest, gdy użytkownik wykona edycji profilu, chęć:

1. Wyszukiwanie użytkownika w systemie zewnętrznym.
2. Pobierz miasto, w którym użytkownik jest zarejestrowany.
3. Zwróć tego atrybutu w aplikacji jako oświadczenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawy usługi Azure AD B2C, skonfigurowany tak, aby ukończyć konta lokalnego konta-konta/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).
- Punkt końcowy interfejsu API REST do interakcji z. W tym przewodniku zastosowano elementu webhook aplikacji prostych funkcji platformy Azure jako przykład.
- *Zalecane*: zakończenie [interfejsu API REST oświadczeń wskazówki programu exchange na potrzeby sprawdzania poprawności](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Przygotowanie funkcji interfejsu API REST

> [!NOTE]
> Instalator funkcji interfejsu API REST jest poza zakres tego artykułu. [Środowisko Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) zapewnia doskonałą zestawu narzędzi do tworzenia usługi RESTful w chmurze.

Firma Microsoft Konfigurowanie funkcji Azure, które otrzymuje oświadczenie o nazwie `email`, a następnie zwraca oświadczenia `city` z przypisaną wartością `Redmond`. Przykład funkcji platformy Azure jest na [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

`userMessage` Oświadczenie, które zwraca funkcja Azure jest opcjonalna w tym kontekście i IEF zignoruje. Może potencjalnie używać go jako wiadomości do aplikacji i użytkownik widzi później.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Aplikacja Azure funkcji można łatwo uzyskać adres URL funkcji, która zawiera identyfikator określoną funkcję. W tym przypadku jest adres URL: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Służy on do testowania.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Krok 2: Konfigurowanie programu exchange oświadczenia interfejsu API RESTful jako profil techniczne w pliku TrustFrameworExtensions.xml

Techniczne profil jest pełną konfigurację programu exchange potrzeby z usługą RESTful. Otwórz plik TrustFrameworkExtensions.xml i Dodaj następujący fragment kodu XML wewnątrz `<ClaimsProvider>` elementu.

> [!NOTE]
> W poniższych XML dostawcy RESTful `Version=1.0.0.0` jest określane jako protokół. Należy wziąć pod uwagę ją jako funkcję, która będzie współpracować z zewnętrznej usługi. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`<InputClaims>` Element definiuje oświadczenia, które będą wysyłane do usługi REST IEF. W tym przykładzie zawartość oświadczenia `givenName` będą wysyłane do usługi REST jako oświadczenie `email`.  

`<OutputClaims>` Element definiuje oświadczenia, które IEF będą oczekiwać od usługi REST. Niezależnie od liczby oświadczenia, które są odbierane IEF będzie używać tylko te określone w tym miejscu. W tym przykładzie oświadczenie odebrana jako `city` zostaną zmapowane do IEF oświadczeń o nazwie `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 3: Dodaj nowe oświadczenie `city` do schematu pliku TrustFrameworkExtensions.xml

Oświadczenie `city` nie jest jeszcze zdefiniowana dowolne miejsce w naszym schematu. Tak, Dodaj definicję wewnątrz elementu `<BuildingBlocks>`. Ten element na początku pliku TrustFrameworkExtensions.xml można znaleźć.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Krok 4: Obejmują wymiana oświadczeń usługi REST aranżacji krok w podróży użytkownika edycji profilu w TrustFrameworkExtensions.xml

Dodaj krok do podróży profilu użytkownika edycji, po użytkownik został uwierzytelniony (procedura aranżacji 1 – 4 w następujących XML) i użytkownik udostępnił informacje zaktualizowany profil (krok 5).

> [!NOTE]
> Istnieje wiele przypadków użycia, gdy wywołanie interfejsu API REST może służyć jako etap aranżacji. Krok aranżacji może służyć jako aktualizacji do systemu zewnętrznego po pomyślnym zakończeniu zadania, takie jak rejestracji po raz pierwszy lub aktualizacji profilu, aby zachować synchronizację informacji. W takim przypadku służy rozszerzyć informacje podane w aplikacji po edycji profilu.

Kopia profilu Edytuj przebieg XML kod użytkownika z pliku TrustFrameworkBase.xml do pliku TrustFrameworkExtensions.xml wewnątrz `<UserJourneys>` elementu. Następnie wprowadź zmiany w kroku 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Jeśli kolejność nie jest zgodna z wersją, upewnij się, Wstaw kod jako kroku przed `ClaimsExchange` typu `SendClaims`.

Końcowe XML podróży użytkownika powinien wyglądać następująco:

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the JWT token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 5: Dodaj oświadczenie `city` Twojego jednostki uzależnionej zasady plików, oświadczenia są wysyłane do aplikacji

Edytuj plik ProfileEdit.xml jednostki uzależnionej strony (RP) i zmodyfikuj `<TechnicalProfile Id="PolicyProfile">` elementu do dodania następujących: `<OutputClaim ClaimTypeReferenceId="city" />`.

Po dodaniu nowego oświadczenia profilu techniczna wygląda następująco:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6-upload-your-changes-and-test"></a>Krok 6: Przekazać zmiany i testowanie

Zastąp istniejące wersje zasad.

1.  (Opcjonalne:) Zapisz (pobierając) istniejącą wersję pliku rozszerzenia przed kontynuowaniem. Aby zachować początkowej złożoności niski, firma Microsoft zaleca, nie przekazuj wielu wersji pliku rozszerzenia.
2.  (Opcjonalne:) Zmień nazwę nowej wersji identyfikator zasad dla pliku edycji zasad, zmieniając `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Przekaż plik rozszerzenia.
4.  Przekaż plik RP edycji zasady.
5.  Użyj **Uruchom teraz** do testowania zasad. Przejrzyj token, który IEF zwraca do aplikacji.

Jeśli wszystko jest poprawnie skonfigurowane, token uwzględni nowe oświadczenie `city`, z wartością `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Kolejne kroki

[Za pośrednictwem interfejsu API REST na potrzeby sprawdzania poprawności](active-directory-b2c-rest-api-validation-custom.md)

[Modyfikowanie edycji profilu uzyskanie dodatkowych informacji od użytkowników](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
