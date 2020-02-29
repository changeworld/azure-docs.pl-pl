---
title: Wymiana oświadczeń interfejsu API REST jako Walidacja
titleSuffix: Azure AD B2C
description: Przewodnik dotyczący tworzenia Azure AD B2Cych podróży użytkowników, które współdziałają z usługami RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7100498d99068941bcd7ca48b6cbcaa271fbb095
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189076"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Wskazówki: integruje wymianę oświadczeń interfejsu API REST w trakcie Azure AD B2C użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Platforma Identity Experience Framework (IEF), która jest zależna od Azure Active Directory B2C (Azure AD B2C) umożliwia deweloperom tożsamości integrację interakcji z interfejsem API RESTful w podróży użytkownika.

Po zakończeniu tego instruktażu będziesz mieć możliwość utworzenia Azure AD B2Cej podróży użytkowników, która współdziała z usługami RESTful.

IEF wysyła dane w oświadczeniach i odbiera dane z powrotem w oświadczeniach. Interakcja z interfejsem API:

- Może być zaprojektowana jako wymiana oświadczeń interfejsu API REST lub jako profil weryfikacji, który odbywa się w ramach kroku aranżacji.
- Zazwyczaj sprawdza poprawność danych wejściowych użytkownika. Jeśli wartość użytkownika zostanie odrzucona, użytkownik może spróbować ponownie wprowadzić prawidłową wartość, aby otrzymać komunikat o błędzie.

Interakcję można także zaprojektować jako krok aranżacji. Aby uzyskać więcej informacji, zobacz [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w ramach kursu Azure AD B2C użytkownika jako kroku aranżacji](custom-policy-rest-api-claims-exchange.md).

W przypadku przykładowego profilu weryfikacji będziemy używać profilu Edytuj podróż użytkownika w pliku Start Pack ProfileEdit. XML.

Możemy sprawdzić, czy nazwa podana przez użytkownika w profilu Edytuj nie jest częścią listy wykluczeń.

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawa Azure AD B2C skonfigurowana do ukończenia rejestracji/logowania do konta lokalnego, zgodnie z opisem w temacie [wprowadzenie](custom-policy-get-started.md).
- Punkt końcowy interfejsu API REST do współpracy z usługą. W tym instruktażu skonfigurujemy witrynę demonstracyjną o nazwie [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) z usługą interfejsu API REST.

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1. Przygotowanie funkcji interfejsu API REST

> [!NOTE]
> Konfiguracja funkcji interfejsu API REST jest poza zakresem tego artykułu. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) oferuje doskonały zestaw narzędzi do tworzenia usług RESTful w chmurze.

Utworzyliśmy funkcję platformy Azure, która otrzymuje rolę, której oczekuje jako `playerTag`. Funkcja sprawdza poprawność tego, czy to stwierdzenie istnieje. Możesz uzyskać dostęp do kompletnego kodu funkcji platformy Azure w serwisie [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

IEF oczekuje `userMessage`, że funkcja platformy Azure zwraca wartość. To zgłoszenie będzie prezentowane jako ciąg dla użytkownika, Jeśli weryfikacja nie powiedzie się, na przykład w przypadku zwrócenia w poprzednim przykładzie stanu konfliktu 409.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Krok 2. Konfigurowanie protokołu API RESTful jako profilu technicznego w pliku TrustFrameworkExtensions. XML

Profil techniczny to pełna konfiguracja programu Exchange wymaganego przez usługę RESTful. Otwórz plik TrustFrameworkExtensions. XML i Dodaj następujący fragment kodu XML wewnątrz elementu `<ClaimsProviders>`.

> [!NOTE]
> W poniższym kodzie XML dostawca RESTful `Version=1.0.0.0` został opisany jako protokół. Rozważmy ją jako funkcję, która będzie współdziałać z usługą zewnętrzną. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
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

Element `InputClaims` definiuje oświadczenia, które będą wysyłane z IEF do usługi REST. W tym przykładzie zawartość `givenName` żądania zostanie wysłana do usługi REST jako `playerTag`. W tym przykładzie IEF nie oczekuje z powrotem oświadczeń. Zamiast tego czeka na odpowiedź z usługi REST i działa na podstawie otrzymanych kodów stanu.

Komentarze powyżej `AuthenticationType` i `AllowInsecureAuthInProduction` określają zmiany, które należy wykonać po przejściu do środowiska produkcyjnego. Aby dowiedzieć się, jak zabezpieczyć interfejsy API usługi RESTful w środowisku produkcyjnym, zobacz [Secure RESTful interfejsy API z uwierzytelnianiem Basic](secure-rest-api-dotnet-basic-auth.md) i [Secure RESTful API z uwierzytelnianiem certyfikatu](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Krok 3. uwzględnienie wymiany oświadczeń usługi RESTful w profilu technicznym, w którym chcesz sprawdzić poprawność danych wejściowych użytkownika

Typowym zastosowaniem kroku walidacji jest interakcja z użytkownikiem. Wszystkie interakcje, w których użytkownik powinien podać dane wejściowe, są *profilami technicznymi z własnym potwierdzeń*. W tym przykładzie dodamy weryfikację do profilu technicznego z własnymi potwierdzeń ProfileUpdate. Jest to profil techniczny, którego używa plik zasad jednostki uzależnionej (RP) `Profile Edit`.

Aby dodać wymianę oświadczeń do profilu technicznego z własnym potwierdzeniem:

1. Otwórz plik TrustFrameworkBase. XML i Wyszukaj `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Przejrzyj konfigurację tego profilu technicznego. Zwróć uwagę na to, jak program Exchange z użytkownikiem jest definiowany jako oświadczenia, które zostanie poproszony o użytkownika (oświadczenia wejściowe), i oświadczenia, które będą oczekiwane z powrotem od dostawcy z własnym potwierdzeniem (oświadczeń wyjściowych).
3. Wyszukaj `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`i zwróć uwagę, że ten profil jest wywoływany w ramach aranżacji krok 5 `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Krok 4. przekazanie i przetestowanie profilu Edytuj plik zasad RP

1. Przekaż nową wersję pliku TrustFrameworkExtensions. XML.
2. Użyj **teraz uruchomienia** , aby przetestować plik zasad RP Edytuj profil.
3. Przetestuj sprawdzanie poprawności, podając jedną z istniejących nazw (na przykład mcvinny) w polu **Nazwa** . Jeśli wszystko jest prawidłowo skonfigurowane, powinien zostać wyświetlony komunikat z informacją o tym, że tag odtwarzacza jest już używany.

## <a name="next-steps"></a>Następne kroki

[Modyfikowanie profilu Edycja i Rejestracja użytkownika w celu zebrania dodatkowych informacji od użytkowników](custom-policy-custom-attributes.md)

[Przewodnik: integruje wymianę oświadczeń interfejsu API REST w ramach przejazdu Azure AD B2C użytkownika w ramach kroku aranżacji](custom-policy-rest-api-claims-exchange.md)

[Dokumentacja: profil techniczny RESTful](restful-technical-profile.md)

Aby dowiedzieć się, jak zabezpieczyć interfejsy API, zobacz następujące artykuły:

* [Zabezpieczanie interfejsu API usługi RESTful przy użyciu uwierzytelniania podstawowego (nazwa użytkownika i hasło)](secure-rest-api-dotnet-basic-auth.md)
* [Zabezpieczanie interfejsu API usługi RESTful przy użyciu certyfikatów klienta](secure-rest-api-dotnet-certificate-auth.md)
