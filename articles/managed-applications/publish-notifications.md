---
title: Azure Managed Applications z powiadomieniami
description: Skonfiguruj aplikację zarządzaną za pomocą punktów końcowych elementu webhook, aby otrzymywać powiadomienia dotyczące tworzenia, aktualizacji, usunięć i błędów w zarządzanych wystąpieniach aplikacji.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: a00e5be4493b8c8116e2925e88a3ce4bf8cfb722
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085319"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure Managed Applications z powiadomieniami

Powiadomienia aplikacji zarządzanych przez platformę Azure umożliwiają wydawcom Automatyzowanie akcji opartych na zdarzeniach cyklu życia zarządzanych wystąpień aplikacji. Wydawcy mogą określić niestandardowe punkty końcowe elementu webhook powiadomień, aby otrzymywać powiadomienia o zdarzeniach o nowych i istniejących wystąpieniach aplikacji zarządzanych. Pozwala to wydawcy na Konfigurowanie niestandardowych przepływów pracy w momencie aprowizacji, aktualizacji i usuwania aplikacji.

## <a name="getting-started"></a>Wprowadzenie
Aby zacząć otrzymywać zarządzane aplikacje, uruchom publiczny punkt końcowy HTTPS i określ go podczas publikowania definicji aplikacji katalogu usług lub oferty portalu Marketplace.

Oto zalecana seria kroków, które należy wykonać, aby szybko rozpocząć pracę:
1. Uruchom publiczny punkt końcowy HTTPS, który rejestruje przychodzące żądania POST i zwraca `200 OK`.
2. Dodaj punkt końcowy do definicji aplikacji katalogu usług lub oferty Marketplace, jak wyjaśniono poniżej.
3. Utwórz wystąpienie aplikacji zarządzanej, które odwołuje się do definicji aplikacji lub oferty portalu Marketplace.
4. Sprawdź, czy powiadomienia są odbierane pomyślnie.
5. Włącz autoryzację zgodnie z opisem w sekcji **uwierzytelnianie punktu końcowego** poniżej.
6. Postępuj zgodnie z poniższą dokumentacją dotyczącą **schematu powiadomień** , aby przeanalizować żądania powiadomień i wdrożyć logikę biznesową na podstawie powiadomienia.

## <a name="adding-service-catalog-application-definition-notifications"></a>Dodawanie powiadomień definicji aplikacji katalogu usług
#### <a name="azure-portal"></a>Azure Portal
Aby rozpocząć, przeczytaj artykuł [publikowanie aplikacji katalogu usług za pomocą Azure Portal](./publish-portal.md) .

![Powiadomienia definicji aplikacji katalogu usług w portalu](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>Interfejs API REST

> [!NOTE]
> Obecnie tylko jeden punkt końcowy jest obsługiwany jako część **notificationEndpoints** we właściwościach definicji aplikacji.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="adding-marketplace-managed-application-notifications"></a>Dodawanie powiadomień aplikacji zarządzanych przez portal Marketplace
Aby uzyskać więcej informacji, zobacz [Tworzenie oferty aplikacji platformy Azure](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Powiadomienia definicji aplikacji katalogu usług w portalu](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Wyzwalacze zdarzeń
W poniższej tabeli opisano wszystkie możliwe kombinacje elementu EventType + ProvisioningState i ich wyzwalacze:

Klasę | ProvisioningState | Wyzwalacz dla powiadomienia
---|---|---
PUT | Przyjmować | Utworzono zarządzaną grupę zasobów i została ona pomyślnie umieszczona po umieszczeniu aplikacji. (Przed rozpoczęciem wdrażania wewnątrz zarządzanego RG).
PUT | Powodzenie | Pełna aprowizacji aplikacji zarządzanej zakończyła się pomyślnie po UMIESZCZENIU.
PUT | Niepowodzenie | Niepowodzenie inicjowania aprowizacji wystąpienia aplikacji w dowolnym momencie.
PATCH | Powodzenie | Po pomyślnej poprawek w wystąpieniu aplikacji zarządzanej w celu zaktualizowania tagów, zasad dostępu JIT lub tożsamości zarządzanej.
DELETE | Usuwanie | Zaraz po zainicjowaniu przez użytkownika usunięcia wystąpienia aplikacji zarządzanej.
DELETE | Usunięte | Po pełnym i pomyślnym usunięciu zarządzanej aplikacji.
DELETE | Niepowodzenie | Po wystąpieniu błędu w trakcie anulowania aprowizacji, który blokuje usunięcie.
## <a name="notification-schema"></a>Schemat powiadomień
Po utworzeniu punktu końcowego elementu webhook w celu obsługi powiadomień musisz przeanalizować ładunek, aby uzyskać ważne właściwości, które będą działać po powiadomieniu. Zarówno w przypadku usługi Service Catalog, jak i aplikacji zarządzanej w portalu Marketplace, istnieją różne właściwości z niewielką różnicą podaną poniżej.

#### <a name="service-catalog-application-notification-schema"></a>Schemat powiadomień aplikacji katalogu usług
Oto przykładowe powiadomienie dotyczące katalogu usług po pomyślnej aprowizacji wystąpienia aplikacji zarządzanej.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Jeśli Inicjowanie obsługi nie powiedzie się, zostanie wysłane powiadomienie z informacjami o błędzie do określonego punktu końcowego.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="marketplace-application-notification-schema"></a>Schemat powiadomień aplikacji portalu Marketplace

Oto przykładowe powiadomienie dotyczące katalogu usług po pomyślnej aprowizacji wystąpienia aplikacji zarządzanej.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Jeśli Inicjowanie obsługi nie powiedzie się, zostanie wysłane powiadomienie z informacjami o błędzie do określonego punktu końcowego.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parametr | Opis
---|---
eventType | Typ zdarzenia, które wyzwoliło powiadomienie. (np. "PUT", "PATCH", "DELETE")
Identyfikator | W pełni kwalifikowany identyfikator zasobu zarządzanej aplikacji, dla którego zostało wyzwolone powiadomienie. 
eventTime | Sygnatura czasowa zdarzenia, które wyzwoliło powiadomienie. (Data i godzina w formacie UTC ISO 8601).
ProvisioningState | Stan aprowizacji wystąpienia aplikacji zarządzanej. (np. "powodzenie", "nie powiodło się", "Usuwanie", "usunięte")
error | *Określone tylko w przypadku niepowodzenia provisioningState*. Zawiera kod błędu, komunikat i szczegóły problemu, który spowodował awarię.
applicationDefinitionId | *Określona tylko dla aplikacji zarządzanych przez katalog usług*. Reprezentuje w pełni kwalifikowany identyfikator zasobu definicji aplikacji, dla którego Zainicjowano obsługę administracyjną wystąpienia aplikacji zarządzanej.
zamierza | *Określone tylko dla aplikacji zarządzanych w portalu Marketplace*. Reprezentuje wydawcę, ofertę, jednostkę SKU i wersję wystąpienia aplikacji zarządzanej.

## <a name="endpoint-authentication"></a>Uwierzytelnianie punktu końcowego
Aby zabezpieczyć punkt końcowy elementu webhook i zapewnić autentyczność powiadomienia:
- Podaj parametr zapytania na górze identyfikatora URI elementu webhook, takiego jak https://your-endpoint.com?sig=Guid. W przypadku każdego powiadomienia należy szybko sprawdzić, czy parametr zapytania `sig` ma oczekiwaną wartość `Guid`.
- Wydaj element GET w wystąpieniu aplikacji zarządzanej z identyfikatorem klasy. Sprawdź, czy provisioningState pasuje do provisioningState powiadomienia, aby zapewnić spójność.

## <a name="notification-retries"></a>Ponowne próby powiadomień

Usługa powiadamiania aplikacji zarządzanej oczekuje `200 OK` odpowiedzi z punktu końcowego elementu webhook do powiadomienia. Usługa powiadamiania ponowi próbę, jeśli punkt końcowy elementu webhook zwróci kod błędu HTTP > = 500, 429 lub jeśli punkt końcowy jest tymczasowo nieosiągalny. Jeśli punkt końcowy elementu webhook nie stanie się dostępny w ciągu 10 godzin, komunikat powiadomienia zostanie porzucony i ponowne próby zakończą się.

