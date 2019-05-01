---
title: Interfejs API REST oświadczeń wymiany, ponieważ krok aranżacji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat usługi Azure Active Directory B2C zasady niestandardowe, które integrują się z interfejsem API.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c0a29bcbd3142be577d4cf1f64ff8c9921010bba
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688009"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Przewodnik: Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C w kroku aranżacji

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tożsamość środowisko Framework (IEF) źródłową Azure Active Directory B2C (Azure AD B2C) umożliwia dewelopera tożsamości zintegrować interakcję z interfejsu API RESTful w podróży użytkownika.  

Na końcu tego instruktażu można utworzyć podróży użytkownika usługi Azure AD B2C, który współdziała z usług RESTful.

IEF wysyła dane jako oświadczenia i odbiera dane z powrotem w oświadczeniach. Wymiana oświadczeń interfejsu API REST:

- Może być zaprojektowane jako kroku aranżacji.
- Można wyzwolić akcję zewnętrznych. Na przykład zarejestrować zdarzenie w zewnętrznej bazie danych.
- Może służyć do pobierania wartości, a następnie zapisać ją w bazie danych użytkownika.

Oświadczenia odebrane można użyć później, aby zmienić przepływem wykonania.

Istnieje również możliwość projektowania interakcji jako profil sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [instruktażu: Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika](active-directory-b2c-rest-api-validation-custom.md).

Scenariusz polega na tym, że po użytkownik wykona edytowania profilu, chcemy, aby:

1. Wyszukaj użytkownika w systemie zewnętrznym.
2. Uzyskaj miasta, w którym użytkownik jest zarejestrowany.
3. Ten atrybut należy powrócić do aplikacji jako oświadczenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawy usługi Azure AD B2C skonfigurowany tak, aby ukończyć konta lokalnego konta-dokonywania/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).
- Punkt końcowy interfejsu API REST do interakcji z. W tym instruktażu wykorzystano element webhook aplikacji prostej funkcji platformy Azure jako przykład.
- *Zalecane*: Wykonaj [interfejsu API REST oświadczeń Przewodnik programu exchange jako kroku weryfikacji](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Przygotowywanie funkcji interfejsu API REST

> [!NOTE]
> Ustawienia funkcji interfejsu API REST znajduje się poza zakres tego artykułu. [Usługa Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) zapewnia doskonałą zestaw narzędzi do tworzenia RESTful usług w chmurze.

Skonfigurowaliśmy funkcji platformy Azure, która otrzymuje oświadczenie o nazwie `email`, a następnie zwraca oświadczenia `city` z przypisaną wartością elementu `Redmond`. Przykład funkcji platformy Azure znajduje się na [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

`userMessage` Oświadczenia, które zwraca funkcję platformy Azure jest opcjonalny w tym kontekście, a IEF zignoruje go. Potencjalnie służy jako wiadomości do aplikacji i przedstawić użytkownikowi później.

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

Aplikację funkcji platformy Azure można łatwo uzyskać adres URL funkcji, która zawiera identyfikator określoną funkcję. W tym przypadku jest adres URL: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Można go używać do testowania.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Krok 2: Konfigurowanie interfejsu API RESTful wymiana oświadczeń jako profil techniczny w pliku TrustFrameworExtensions.xml

Profil techniczny jest pełną konfigurację programu exchange żądanego przy użyciu usługi RESTful. Otwórz plik TrustFrameworkExtensions.xml i Dodaj następujący fragment kodu XML wewnątrz `<ClaimsProvider>` elementu.

> [!NOTE]
> Następujący kod XML dostawcy typu RESTful `Version=1.0.0.0` jest określana jako protokołu. Należy wziąć pod uwagę jej jako funkcja, która wchodzi w interakcje z zewnętrznej usługi. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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
                <Item Key="AllowInsecureAuthInProduction">true</Item>
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

`<InputClaims>` Element definiuje oświadczenia, które będą wysyłane z IEF usługi REST. W tym przykładzie zawartość oświadczenie `givenName` będą wysyłane do usługi REST jako oświadczenie `email`.  

`<OutputClaims>` Element definiuje oświadczenia, które IEF będzie oczekiwać od usługi REST. Bez względu na liczbę oświadczeń, które są odbierane IEF użyje tylko te określone w tym miejscu. W tym przykładzie oświadczenie odebrana jako `city` zostaną zmapowane do IEF oświadczeń o nazwie `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 3: Dodaj nowe oświadczenie `city` do schematu pliku TrustFrameworkExtensions.xml

Oświadczenie `city` nie jest jeszcze zdefiniowana dowolne miejsce w naszym schematu. Tak, Dodaj definicję wewnątrz elementu `<BuildingBlocks>`. Możesz znaleźć tego elementu na początku pliku TrustFrameworkExtensions.xml.

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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Krok 4: Krok aranżacji w podróży użytkownika Edycja profilu w TrustFrameworkExtensions.xml one obejmować wymiana oświadczeń usługi REST

Dodaj krok do profilu edycji podróży użytkownika, po użytkownik został uwierzytelniony (aranżacji: kroki 1 – 4 następujący kod XML) i użytkownik udostępnił informacje zaktualizowany profil (krok 5).

> [!NOTE]
> Istnieje wiele przypadków użycia, w którym wywołania interfejsu API REST może służyć jako kroku aranżacji. Krok aranżacji może służyć jako aktualizację do systemu zewnętrznego po pomyślnym zakończeniu zadania, takie jak rejestracji po raz pierwszy lub aktualizacja profilu Aby zachować synchronizację informacji. W tym przypadku jest używany rozszerzyć informacje do aplikacji po edycji profilu.

Kopia profilu edycji podróż XML kod użytkownika z pliku TrustFrameworkBase.xml do pliku TrustFrameworkExtensions.xml wewnątrz `<UserJourneys>` elementu. Następnie wprowadzić zmiany w kroku 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Jeśli kolejność nie jest zgodna z wersją, upewnij się, Wstaw kod jako etap przed `ClaimsExchange` typu `SendClaims`.

Końcowe XML dla podróży użytkownika powinien wyglądać następująco:

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

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 5. Dodaj oświadczenie `city` do swoje jednostki uzależnionej zasad pliku, dzięki czemu oświadczenia są wysyłane do aplikacji

Edytuj plik ProfileEdit.xml jednostki uzależnionej strona (RP) i modyfikować `<TechnicalProfile Id="PolicyProfile">` element dodać następujący kod: `<OutputClaim ClaimTypeReferenceId="city" />`.

Po dodaniu nowego oświadczenia, profilu technicznego wygląda następująco:

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

## <a name="step-6-upload-your-changes-and-test"></a>Krok 6: Przekazywanie zmian i testowanie

Zastąp istniejące wersje zasad.

1.  (Opcjonalne:) Zapisz (pobierając) istniejącą wersję pliku rozszerzenia przed kontynuowaniem. Aby niskich początkowej złożoności, zaleca się, nie nastąpi przekazanie wielu wersji pliku rozszerzenia.
2.  (Opcjonalne:) Zmień nazwę nowej wersji identyfikator zasad edycji pliku zasad, zmieniając `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Przekaż plik rozszerzenia.
4.  Przekaż plik RP edycji zasady.
5.  Użyj **Uruchom teraz** do testowania zasad. Przejrzyj token, który IEF zwraca do aplikacji.

Jeśli wszystko jest prawidłowo skonfigurowane, token będzie zawierać nowe oświadczenie `city`, z wartością `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

[Użycie interfejsu API REST jako kroku weryfikacji](active-directory-b2c-rest-api-validation-custom.md)

[Modyfikowanie edytowania profilu do zbierania dodatkowych informacji od użytkowników](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
