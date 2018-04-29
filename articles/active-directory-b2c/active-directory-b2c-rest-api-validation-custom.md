---
title: 'Usługi Azure Active Directory B2C: Oświadczenia interfejsu API REST wymiany jako weryfikacji | Dokumentacja firmy Microsoft'
description: Temat dotyczący zasad niestandardowych usługi Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2017
ms.author: davidmu
ms.openlocfilehash: 31ed69b669a92ad38c4ab7b2f54886e4ed6acf42
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Wskazówki: Integrowanie wymiany oświadczenia interfejsu API REST usługi Azure AD B2C podróży użytkownika jako sprawdzanie poprawności danych wejściowych użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tożsamość środowiska Framework (IEF) źródłową Azure Active Directory B2C (Azure AD B2C) umożliwia deweloperowi tożsamości integracji interakcji z interfejsu API RESTful w podróży użytkownika.  

Na końcu tego przewodnika można utworzyć przebieg użytkownika usługi Azure AD B2C, która współdziała z usług RESTful.

IEF wysyła dane w oświadczeniach i odbiera dane z powrotem w oświadczeniach. Interakcja z interfejsu API:

- Może być zaprojektowana jako exchange oświadczenia interfejsu API REST lub profil sprawdzania poprawności, które odbywa się w kroku aranżacji.
- Zwykle sprawdza poprawność danych wejściowych od użytkownika. W przypadku odrzucenia wartość od użytkownika, użytkownik spróbować ponownie wprowadź prawidłową wartość możliwość zwraca komunikat o błędzie.

Można również projektować interakcji krok aranżacji. Aby uzyskać więcej informacji, zobacz [wskazówki: integrowanie interfejsu API REST oświadczeń wymiany w podróży użytkownika usługi Azure AD B2C krok aranżacji](active-directory-b2c-rest-api-step-custom.md).

Na przykład profil sprawdzania poprawności użyjemy podróży profilu użytkownika edycji w pliku pakietu starter ProfileEdit.xml.

Możemy zweryfikować, że nazwa podana przez użytkownika w edycji profilu nie jest częścią listy wykluczeń.

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawy usługi Azure AD B2C, skonfigurowany tak, aby ukończyć konta lokalnego konta-konta/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).
- Punkt końcowy interfejsu API REST do interakcji z. W ramach tego przewodnika skonfigurowaliśmy pokaz lokacji o nazwie [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) za pomocą usługi interfejsu API REST.

## <a name="step-1-prepare-the-rest-api-function"></a>Krok 1: Przygotowanie funkcji interfejsu API REST

> [!NOTE]
> Instalator funkcji interfejsu API REST jest poza zakres tego artykułu. [Środowisko Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) zapewnia doskonałą zestawu narzędzi do tworzenia usługi RESTful w chmurze.

Utworzyliśmy Azure funkcja, która otrzymuje oświadczenia, że klient oczekuje jako `playerTag`. Funkcja weryfikuje, czy istnieje tego oświadczenia. Dostęp można uzyskać kod pełną funkcji platformy Azure w [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

Oczekuje IEF `userMessage` oświadczenie, które zwraca funkcja platformy Azure. To oświadczenie zostanie wyświetlone jako ciąg do użytkownika w przypadku niepowodzenia weryfikacji, na przykład gdy stan 409 Konflikt są zwracane w poprzednim przykładzie.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Krok 2: Konfigurowanie programu exchange oświadczenia interfejsu API RESTful jako profil techniczne w pliku TrustFrameworkExtensions.xml

Techniczne profil jest pełną konfigurację programu exchange potrzeby z usługą RESTful. Otwórz plik TrustFrameworkExtensions.xml i Dodaj następujący fragment kodu XML wewnątrz `<ClaimsProviders>` elementu.

> [!NOTE]
> W poniższych XML dostawcy RESTful `Version=1.0.0.0` jest określane jako protokół. Należy wziąć pod uwagę ją jako funkcję, która będzie współpracować z zewnętrznej usługi. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

`InputClaims` Element definiuje oświadczenia, które będą wysyłane do usługi REST IEF. W tym przykładzie zawartość oświadczenia `givenName` będą wysyłane do usługi REST jako `playerTag`. W tym przykładzie IEF nie oczekuje oświadczeń z powrotem. Zamiast tego czeka na odpowiedź z usługi REST i działania na podstawie kodów stanu, które otrzymuje.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Krok 3: Dołącz exchange oświadczeń usługi RESTful własnym potwierdzona profilu techniczne której chcesz sprawdzić poprawność danych wejściowych użytkownika

Najczęściej kroku walidacji jest w interakcji z użytkownikiem. Wszystkie interakcje, gdy użytkownik powinien zapewniać dane wejściowe są *własnym potwierdzone techniczne profile*. W tym przykładzie dodamy weryfikacji do profilu techniczne niezależne Asserted ProfileUpdate. Jest to techniczne profil, który pliku zasad jednostki uzależnionej strony (RP) `Profile Edit` używa.

Aby dodać exchange oświadczenia do własnym potwierdzona profilu techniczne:

1. Otwórz plik TrustFrameworkBase.xml i wyszukaj `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Sprawdź konfigurację tego profilu technicznych. Sprawdź, jak zdefiniowano jako oświadczenia, które będą proszeni użytkownika (oświadczenia wejściowe) i oświadczenia, które będzie można oczekiwać od dostawcy własnym potwierdzona (oświadczeń wyjściowych) programu exchange z użytkownikiem.
3. Wyszukaj `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`i zwróć uwagę, że ten profil jest wywoływany jako aranżacji kroku 4 `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Krok 4: Przekaż i przetestować plik zasad RP edycji profilu

1. Przekaż nową wersję pliku TrustFrameworkExtensions.xml.
2. Użyj **Uruchom teraz** do testowania pliku zasad RP edycji profilu.
3. Testowanie weryfikacji udostępniając jedno z istniejących nazw (na przykład mcvinny) w **imię** pola. Jeśli wszystko jest poprawnie skonfigurowane, powinien zostać wyświetlony komunikat, który powiadamia użytkownika player tag jest już używana.

## <a name="next-steps"></a>Kolejne kroki

[Modyfikowanie profilu rejestracji edycji i użytkownika uzyskanie dodatkowych informacji od użytkowników](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Wskazówki: Integrowanie wymiany oświadczenia interfejsu API REST w podróży użytkownika usługi Azure AD B2C krok aranżacji](active-directory-b2c-rest-api-step-custom.md)
