---
title: Aplikacje zarządzane z powiadomieniami
description: Konfigurowanie zarządzanych aplikacji z punktami końcowymi webhook do odbierania powiadomień o tworzy, aktualizacje, usuwa i błędy w wystąpieniach aplikacji zarządzanych.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715670"
---
# <a name="azure-managed-applications-with-notifications"></a>Aplikacje zarządzane na platformie Azure z powiadomieniami

Powiadomienia o aplikacjach zarządzanych platformy Azure umożliwiają wydawcom automatyzację akcji na podstawie zdarzeń cyklu życia wystąpień aplikacji zarządzanych. Wydawcy mogą określić niestandardowe punkty końcowe powiadomień webhook do odbierania powiadomień o zdarzeniach o nowych i istniejących wystąpieniach aplikacji zarządzanych. Wydawcy mogą konfigurować niestandardowe przepływy pracy w czasie inicjowania obsługi administracyjnej aplikacji, aktualizacji i usuwania.

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć odbieranie zarządzanych aplikacji, należy wykręcić publiczny punkt końcowy HTTPS i określić go podczas publikowania definicji aplikacji katalogu usług lub oferty portalu Azure Marketplace.

Oto zalecane kroki, aby szybko rozpocząć pracę:
1. Rozkręcanie publicznego punktu końcowego HTTPS, `200 OK`który rejestruje przychodzące żądania POST i zwraca .
2. Dodaj punkt końcowy do definicji aplikacji katalogu usług lub oferty portalu Azure Marketplace, jak wyjaśniono w dalszej części tego artykułu.
3. Utwórz wystąpienie aplikacji zarządzanej, które odwołuje się do definicji aplikacji lub oferty portalu Azure Marketplace.
4. Sprawdź, czy powiadomienia są odbierane.
5. Włącz autoryzację zgodnie z wyjaśnieniem w sekcji **uwierzytelniania punktu końcowego** w tym artykule.
6. Postępuj zgodnie z instrukcjami w sekcji **Schemat powiadomień** w tym artykule, aby przeanalizować żądania powiadomień i zaimplementować logikę biznesową na podstawie powiadomienia.

## <a name="add-service-catalog-application-definition-notifications"></a>Dodawanie powiadomień o definicji aplikacji katalogu usług
#### <a name="azure-portal"></a>Portal Azure
Aby rozpocząć, zobacz [Publikowanie aplikacji wykazu usług za pośrednictwem witryny Azure portal](./publish-portal.md).

![Powiadomienia o definicji aplikacji katalogu usług w witrynie Azure portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>Interfejs API REST

> [!NOTE]
> Obecnie można podać tylko jeden punkt `notificationEndpoints` końcowy we właściwościach definicji aplikacji.

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>Dodawanie powiadomień aplikacji zarządzanych w portalu Azure Marketplace
Aby uzyskać więcej informacji, zobacz [Tworzenie oferty aplikacji platformy Azure](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Powiadomienia o aplikacjach zarządzanych w portalu Azure Marketplace w portalu Azure](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Wyzwalacze zdarzeń
W poniższej tabeli opisano wszystkie możliwe kombinacje EventType i ProvisioningState i ich wyzwalaczy:

Typ zdarzenia | Stan inicjowania obsługi administracyjnej | Wyzwalacz powiadomienia
---|---|---
PUT | Zaakceptowane | Zarządzana grupa zasobów została utworzona i pomyślnie przeprojektowana po aplikacji PUT (przed rozpoczęciem wdrożenia wewnątrz zarządzanej grupy zasobów).
PUT | Powodzenie | Pełna inicjowanie obsługi administracyjnej aplikacji zarządzanej powiodła się po PUT.
PUT | Niepowodzenie | Niepowodzenie put inicjowania obsługi administracyjnej wystąpienia aplikacji w dowolnym momencie.
Patch | Powodzenie | Po pomyślnym patchu w wystąpieniu aplikacji zarządzanej, aby zaktualizować tagi, zasady dostępu JIT lub tożsamość zarządzaną.
DELETE | Usuwanie | Tak szybko, jak użytkownik inicjuje DELETE wystąpienia aplikacji zarządzanej.
DELETE | Usunięte | Po pełnym i pomyślnym usunięciu zarządzanej aplikacji.
DELETE | Niepowodzenie | Po każdym błędzie podczas procesu anulowania obsługi administracyjnej, który blokuje usunięcie.
## <a name="notification-schema"></a>Schemat powiadomień
Po uruchomieniu punktu końcowego elementu webhook do obsługi powiadomień, należy przeanalizować ładunek, aby uzyskać ważne właściwości, aby następnie działać na powiadomienie. Katalog usług i powiadomienia aplikacji zarządzanych w portalu Azure Marketplace zapewniają wiele tych samych właściwości. Dwie małe różnice są opisane w tabeli, która następuje po przykładach.

#### <a name="service-catalog-application-notification-schema"></a>Schemat powiadomień aplikacji katalogu usług
Oto przykładowe powiadomienie katalogu usług po pomyślnym zainicjowaniu obsługi administracyjnej wystąpienia aplikacji zarządzanej:
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

Jeśli inicjowanie obsługi administracyjnej zakończy się niepowodzeniem, powiadomienie ze szczegółami błędu zostanie wysłane do określonego punktu końcowego.

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

#### <a name="azure-marketplace-application-notification-schema"></a>Schemat powiadomień aplikacji portalu Azure Marketplace

Oto przykładowe powiadomienie katalogu usług po pomyślnym zainicjowaniu obsługi administracyjnej wystąpienia aplikacji zarządzanej:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Jeśli inicjowanie obsługi administracyjnej zakończy się niepowodzeniem, powiadomienie ze szczegółami błędu zostanie wysłane do określonego punktu końcowego.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
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
Eventtype | Typ zdarzenia, które wyzwoliło powiadomienie. (Na przykład PUT, PATCH, DELETE.)
applicationId | W pełni kwalifikowany identyfikator zasobu zarządzanej aplikacji, dla której zostało wyzwolone powiadomienie.
eventTime | Sygnatura czasowa zdarzenia, które wyzwoliło powiadomienie. (Data i godzina w formacie UTC ISO 8601).
aprowizacjiPaństwo | Stan inicjowania obsługi administracyjnej wystąpienia aplikacji zarządzanej. (Na przykład powodzenie, niepowodzenie, usuwanie, usunięcie).
error | *Określono tylko wtedy, gdy aprowizowanieState nie powiodło się*. Zawiera kod błędu, komunikat i szczegóły problemu, który spowodował błąd.
identyfikator zdefiniowanie aplikacji | *Określono tylko dla aplikacji zarządzanych katalogu usług*. Reprezentuje w pełni kwalifikowany identyfikator zasobu definicji aplikacji, dla której zostało aprowizowane wystąpienie aplikacji zarządzanej.
plan | *Określono tylko dla aplikacji zarządzanych w portalu Azure Marketplace*. Reprezentuje wydawcę, ofertę, jednostkę SKU i wersję wystąpienia aplikacji zarządzanej.
rozliczeniaSzczegóły | *Określono tylko dla aplikacji zarządzanych w portalu Azure Marketplace.* Szczegóły rozliczeń wystąpienia aplikacji zarządzanej. Zawiera resourceUsageId, którego można użyć do kwerendy w portalu Azure Marketplace w celu uzyskania szczegółowych informacji na temat użycia.

## <a name="endpoint-authentication"></a>Uwierzytelnianie w punkcie końcowym
Aby zabezpieczyć punkt końcowy elementu webhook i zapewnić autentyczność powiadomienia:
1. Podaj parametr zapytania na górze identyfikatora URI elementu\:webhook, taki jak: https //your-endpoint.com?sig=Guid. Przy każdym powiadomieniu sprawdź, `sig` czy parametr `Guid`kwerendy ma oczekiwaną wartość .
2. Problem GET w wystąpieniu aplikacji zarządzanej przy użyciu applicationId. Sprawdź poprawność, że inicjującastate odpowiada inicjującejPaństwo powiadomienia, aby zapewnić spójność.

## <a name="notification-retries"></a>Ponownych prób powiadomień

Usługa powiadamiania o aplikacji `200 OK` zarządzanej oczekuje odpowiedzi od punktu końcowego elementu webhook do powiadomienia. Usługa powiadomień ponowi próbę, jeśli punkt końcowy elementu webhook zwraca kod błędu HTTP większy lub równy 500, jeśli zwraca kod błędu 429 lub jeśli punkt końcowy jest tymczasowo nieosiągalny. Jeśli punkt końcowy elementu webhook nie stają się dostępne w ciągu 10 godzin, komunikat powiadomienia zostanie usunięty i ponownych prób zostanie zatrzymana.
