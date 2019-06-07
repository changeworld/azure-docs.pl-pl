---
title: Interfejs API REST oświadczeń wymiany jako sprawdzania poprawności w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat w zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0779e4a93230a90b8eee76f1898154c1a5b82661
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508732"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Przewodnik: Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tożsamość środowisko Framework (IEF) źródłową Azure Active Directory B2C (Azure AD B2C) umożliwia dewelopera tożsamości zintegrować interakcję z interfejsu API RESTful w podróży użytkownika.  

Na końcu tego instruktażu można utworzyć podróży użytkownika usługi Azure AD B2C, który współdziała z usług RESTful.

IEF wysyła dane jako oświadczenia i odbiera dane z powrotem w oświadczeniach. Interakcja z interfejsem API:

- Może być zaprojektowane jako wymiana oświadczeń interfejsu API REST lub profil sprawdzania poprawności, które odbywa się wewnątrz kroku aranżacji.
- Zwykle sprawdza poprawność danych wejściowych od użytkownika. W przypadku odrzucenia wartości przez użytkownika użytkownik może ponownie spróbować Wprowadź prawidłową wartość z szansą sprzedaży, aby zwrócić komunikat o błędzie.

Istnieje również możliwość projektowania interakcji w kroku aranżacji. Aby uzyskać więcej informacji, zobacz [instruktażu: Integracja interfejsu API REST wymianą oświadczeń swoją podróż po użytkownik usługi Azure AD B2C w kroku aranżacji](active-directory-b2c-rest-api-step-custom.md).

Na przykład profil sprawdzania poprawności użyjemy podróży użytkownika Edycja profilu w pliku pakietu startowego ProfileEdit.xml.

Firma Microsoft może zweryfikować, czy nazwa podana przez użytkownika w trakcie edycji profilu nie jest częścią listy wykluczeń.

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawy usługi Azure AD B2C skonfigurowany tak, aby ukończyć konta lokalnego konta-dokonywania/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).
- Punkt końcowy interfejsu API REST do interakcji z. W tym przewodniku skonfigurowaliśmy pokaz lokacji o nazwie [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) za pomocą usługi interfejsu API REST.

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Przygotowywanie funkcji interfejsu API REST

> [!NOTE]
> Ustawienia funkcji interfejsu API REST znajduje się poza zakres tego artykułu. [Usługa Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) zapewnia doskonałą zestaw narzędzi do tworzenia RESTful usług w chmurze.

Utworzono funkcję platformy Azure, która otrzymuje oświadczenia, że klient oczekuje jako `playerTag`. Funkcja sprawdza, czy istnieje tego oświadczenia. Możesz uzyskać dostęp kodu pełne funkcji platformy Azure w [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

Oczekuje IEF `userMessage` oświadczenia, które zwraca funkcję platformy Azure. To oświadczenie zostanie wyświetlone jako ciąg znaków do użytkownika, jeśli weryfikacja zakończy się niepowodzeniem, np. gdy zwracany jest stan 409 konflikt w poprzednim przykładzie.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Krok 2: Konfigurowanie interfejsu API RESTful wymiana oświadczeń jako profil techniczny w pliku TrustFrameworkExtensions.xml

Profil techniczny jest pełną konfigurację programu exchange żądanego przy użyciu usługi RESTful. Otwórz plik TrustFrameworkExtensions.xml i Dodaj następujący fragment kodu XML wewnątrz `<ClaimsProviders>` elementu.

> [!NOTE]
> Następujący kod XML dostawcy typu RESTful `Version=1.0.0.0` jest określana jako protokołu. Należy wziąć pod uwagę jej jako funkcja, która wchodzi w interakcje z zewnętrznej usługi. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims` Element definiuje oświadczenia, które będą wysyłane z IEF usługi REST. W tym przykładzie zawartość oświadczenie `givenName` będą wysyłane do usługi REST jako `playerTag`. W tym przykładzie IEF nie oczekuje oświadczeń z powrotem. Zamiast tego oczekuje na odpowiedź z usługi REST i działa na podstawie kodów stanu, które otrzymuje.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Krok 3: Wymiana oświadczeń typu RESTful usługi objęte się samodzielnie profilu technicznego, które chcesz sprawdzić poprawność danych wejściowych użytkownika

Jest najbardziej popularnym zastosowaniem kroku sprawdzania poprawności w interakcji z użytkownikiem. Wszystkie interakcje, gdzie użytkownik powinien zapewniać dane wejściowe są *własnym potwierdzone profile techniczne*. W tym przykładzie dodamy sprawdzania poprawności do profilu technicznego samoobsługowego Asserted ProfileUpdate. Jest to techniczne profilu, który pliku jednostki uzależnionej zasad firmy (RP) `Profile Edit` używa.

Aby dodać wymiana oświadczeń do samodzielnie profilu technicznego:

1. Otwórz plik TrustFrameworkBase.xml i wyszukaj `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Sprawdź konfigurację tego profilu technicznego. Sprawdź, jak program exchange z użytkownikiem jest zdefiniowany jako oświadczenia, które pojawi się prośba o użytkownika (oświadczeń wejściowych) i oświadczenia, które będzie można oczekiwać od dostawcy samodzielnie (oświadczeń danych wyjściowych).
3. Wyszukaj `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`i zwróć uwagę, że ten profil jest wywoływana jako aranżacji krok 5 z `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Krok 4: Przekazywanie i przetestować plik zasad profilu edycji planu odzyskiwania

1. Przekaż nową wersję pliku TrustFrameworkExtensions.xml.
2. Użyj **Uruchom teraz** do testowania pliku zasad profilu edycji planu odzyskiwania.
3. Test weryfikacji, podając jeden z istniejących nazw (na przykład mcvinny) w **imię** pola. Jeśli wszystko jest prawidłowo skonfigurowane, otrzymasz komunikat, który powoduje powiadomienie użytkownika o player tag jest już używana.

## <a name="next-steps"></a>Kolejne kroki

[Modyfikowanie profilu rejestracji edycji i użytkownika do zbierania dodatkowych informacji od użytkowników](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Wskazówki: Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C w kroku aranżacji](active-directory-b2c-rest-api-step-custom.md)
