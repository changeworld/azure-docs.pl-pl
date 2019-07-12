---
title: SaaS realizacji interfejsu API w wersji 2 | Portal Azure Marketplace
description: W tym artykule wyjaśniono, jak tworzyć i zarządzać nimi to oferta SaaS w usłudze AppSource i portalu Azure Marketplace przy użyciu realizacji skojarzone interfejsy API w wersji 2.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 476aaacbe6f1bf6d1920df0f12599976bfcc27b7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701135"
---
# <a name="saas-fulfillment-apis-version-2"></a>Realizacja SaaS interfejsów API w wersji 2 

Ten artykuł szczegółowo opisuje interfejsy API, pozwalających na korzystanie z partnerami, aby sprzedawać swoje aplikacje SaaS w portalu marketplace usługi AppSource i portalu Azure Marketplace. Te interfejsy API są wymagane dla aplikacji SaaS transactable oferty w usłudze AppSource i portalu Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Zarządzanie cyklem życia subskrypcji w modelu SaaS

SaaS platformy Azure zarządza cały cykl życia zakupu subskrypcji SaaS. Go używa realizacji interfejsów API jako mechanizm do realizacji rzeczywiste, zmiany wprowadzane w planach i usunięcie subskrypcji z partnerem. Bill klienta jest oparty na stan subskrypcji SaaS, które firma Microsoft udostępnia. Poniższy diagram przedstawia Stany i operacje, które dysku zmiany między stanami.

![Stany cyklu życia subskrypcji w modelu SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stany subskrypcji SaaS

W poniższej tabeli wymieniono stanach aprowizacji dla subskrypcji SaaS, w tym diagramie opis i sekwencji dla każdej (jeśli dotyczy). 

#### <a name="provisioning"></a>Inicjowanie obsługi

Gdy klient inicjuje zakupu, partnera odbiera tych informacji w kod autoryzacji na stronie sieci web klienta interactive, która używa parametru URL. Na przykład `https://contoso.com/signup?token=..`, a adres URL strony docelowej w Centrum partnerskim `https://contoso.com/signup`. Kod autoryzacji można sprawdzić poprawności i wymieniane szczegóły usługi aprowizacji, przez wywołanie interfejsu API rozwiązania.  Po zakończeniu aprowizacji usługi SaaS wysyła wywołania Aktywuj sygnał, że realizację zostało zakończone i może być rozliczana klienta. 

Na poniższym diagramie przedstawiono sekwencję wywołań interfejsu API dla scenariusza inicjowania obsługi administracyjnej.  

![Wywołania interfejsu API do inicjowania obsługi usługi SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Zaaprowizowane

Ten stan jest w stanie stabilności aprowizowanej usługi.

##### <a name="provisioning-for-update"></a>Inicjowanie obsługi administracyjnej dla aktualizacji 

Ten stan oznacza, że oczekuje aktualizacji do istniejącej usługi. Takiej aktualizacji może być inicjowane przez klienta z witryny marketplace lub w usłudze SaaS (tylko w przypadku transakcji bezpośrednio do klienta).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Inicjowanie obsługi administracyjnej dla aktualizacji (po zainicjowaniu z portalu marketplace)

Na poniższym diagramie przedstawiono sekwencję akcji, gdy aktualizacja jest inicjowane z witryny marketplace.

![Wywołania interfejsu API po zainicjowaniu aktualizacji z witryny marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Inicjowanie obsługi administracyjnej dla aktualizacji (po zainicjowaniu usługi SaaS)

Na poniższym diagramie przedstawiono akcje, gdy aktualizacja jest inicjowane z usługi SaaS. (Wywołanie elementu webhook jest zastępowany przez aktualizację subskrypcji inicjowanego przez usługę SaaS). 

![Wywołania interfejsu API, gdy aktualizacja jest inicjowane z usługi SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

Ten stan wskazuje, nie otrzymał płatności odbiorcy. Zgodnie z zasadami, firma Microsoft udostępni klienta okres prolongaty przed anulowaniem subskrypcji. Gdy subskrypcja jest w tym stanie: 

- Jako partner można zmniejszyć bądź go zablokować dostęp użytkownika do usługi.
- Subskrypcja musi znajdować się w stanie możliwe do odzyskania, który można przywrócić pełną funkcjonalność bez utraty danych lub ustawień. 
- Chcą uzyskać żądania przywrócenie tej subskrypcji za pośrednictwem realizacji interfejsów API lub żądanie do inicjowania obsługi administracyjnej z końcem okresu prolongaty. 

#### <a name="unsubscribed"></a>Anulowano subskrypcję 

Subskrypcje dotrzeć do tego stanu w odpowiedzi na żądanie klienta jawne lub zgłaszane opłat. Oczekuje od partnera się, że danych klienta jest zachowana na potrzeby odzyskiwania w żądaniu określoną liczbę dni, a następnie usuwany. 


## <a name="api-reference"></a>Dokumentacja interfejsów API

W tej sekcji omówiono SaaS *API subskrypcji* i *operacji interfejsu API*.  Wartość `api-version` parametru w wersji 2 interfejsy API są `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definicje parametrów i jednostki

Poniższa lista zawiera definicje dla wspólnych parametrów i jednostki używane przez realizacji interfejsów API.

|     Parametr/w jednostce     |     Definicja                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identyfikator GUID zasobu SaaS.  |
| `name`                   | Przyjazna nazwa podana dla tego zasobu przez klienta. |
| `publisherId`            | Identyfikator unikatowy ciąg, dla każdego wydawcy (na przykład: "contoso"). |
| `offerId`                | Identyfikator unikatowy ciąg, dla każdej oferty (na przykład: "offer1").  |
| `planId`                 | Identyfikator unikatowy ciąg, w ramach każdego planu/jednostki SKU (na przykład: "srebrna"). |
| `operationId`            | Identyfikator GUID dla określonej operacji.  |
|  `action`                | Działania wykonywane w zasobie, albo `unsubscribe`, `suspend`, `reinstate`, lub `changePlan`, `changeQuantity`, `transfer`.  |
|   |   |

Globalnie unikatowe identyfikatory ([identyfikatorów GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) są liczbami (32-szesnastkowa) 128-bitowe, które zazwyczaj są generowane automatycznie. 

#### <a name="resolve-a-subscription"></a>Rozwiązanie z subskrypcji 

Punkt końcowy resolve umożliwia z wydawcą, aby rozpoznać identyfikatora persistent resource token witryny marketplace Identyfikator zasobu jest unikatowy identyfikator subskrypcji SaaS. Gdy użytkownik jest przekierowywany do witryny internetowej partnera, adres URL zawiera token w parametrach zapytania. Używanie tego tokenu i zgłosić wniosek o rozwiązać ten problem powinien partnera. Odpowiedź zawiera identyfikator subskrypcji w modelu SaaS unikatową nazwę, Identyfikatora oferty i plan dla zasobu. Ten token jest ważny przez tylko jedną godzinę. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Wpis<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  authorization     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |
|  x-ms-marketplace-token  |  Parametr tokenu zapytania w adresie URL, gdy użytkownik jest przekierowywany do witryny internetowej partnera SaaS na platformie Azure (na przykład: `https://contoso.com/signup?token=..`). *Uwaga:* Adres URL dekoduje wartość tokenu za pomocą przeglądarki przed jego użyciem.  |

*Kody odpowiedzi:*

Kod: 200<br>
Subskrypcja SaaS jest rozpoznawany jako nieprzezroczysty tokenu. Treść odpowiedzi:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kod: 400<br>
Nieprawidłowe żądanie. x-ms-Portal marketplace — token jest Brak, źle sformułowane lub wygasła.

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 404<br>
Nie można odnaleźć.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>Subskrypcja interfejsu API

Subskrypcja interfejs API obsługuje następujące operacje protokołu HTTPS: **Pobierz**, **wpis**, **poprawki**, i **Usuń**.


#### <a name="list-subscriptions"></a>Subskrypcji listy

Wyświetla listę wszystkich subskrypcji SaaS dla wydawcy.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametry zapytania:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Wersja operacji przy użyciu dla tego żądania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
| authorization      |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kody odpowiedzi:*

Kod: 200 <br/>
Pobiera wydawcę i odpowiednich subskrypcji dla ofert wszystkich wydawcy, oparte na token uwierzytelniania.
Ładunek odpowiedzi:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Token kontynuacji będzie obecny tylko wtedy, gdy istnieją dodatkowe "strony" planów do pobrania. 

Kod: 403 <br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy. 

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Pobierz subskrypcję

Pobiera określoną subskrypcję SaaS. Użyj tego wywołania do plan informacji i uzyskać informacje o licencji.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu za pośrednictwem rozwiązania interfejsu API.   |
|  ApiVersion        |   Wersja operacji przy użyciu dla tego żądania.   |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  authorization     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Kody odpowiedzi:*

Kod: 200<br>
Pobiera subskrypcji SaaS z identyfikatorem. Ładunek odpowiedzi:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M"
        },
}
```

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 404<br>
Nie można odnaleźć.<br> 

Kod: 500<br>
Wewnętrzny błąd serwera.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Lista dostępnych planów

Użyj tego wywołania, aby sprawdzić, czy prywatną lub publiczną ofert dla bieżącej wydawcy.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Wersja operacji przy użyciu dla tego żądania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  x-ms-correlationid  | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  authorization     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Kody odpowiedzi:*

Kod: 200<br>
Pobiera listę dostępnych planów dla klientów. Treść odpowiedzi:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Kod: 404<br>
Nie można odnaleźć.<br> 

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy. <br> 

Kod: 500<br>
Wewnętrzny błąd serwera.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktywuj subskrypcję

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Wpis<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid  | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten ciąg jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  authorization     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Ładunek żądania:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Kody odpowiedzi:*

Kod: 200<br>
Aktywuje subskrypcji.<br>

Kod: 400<br>
Nieprawidłowe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 404<br>
Nie można odnaleźć.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Zmień plan subskrypcji

Zaktualizuj plan w ramach subskrypcji.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Poprawki<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid  |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.    |
| authorization      |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Ładunek żądania:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operacja lokalizacji | Link do zasobu, można uzyskać stanu operacji.   |

*Kody odpowiedzi:*

Kod: 202<br>
Żądanie, aby zmienić plan zostało zaakceptowane. Partner powinien sondowanie operacji lokalizacji do ustalenia powodzenia lub niepowodzenia. <br>

Kod: 400<br>
Nieprawidłowe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 404<br>
Nie można odnaleźć.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Tylko plan lub ilość, może być poprawionego w tym samym czasie, nie oba. Umożliwia edycję w subskrypcji przy użyciu **aktualizacji** nie znajduje się w `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Zmień ilość w ramach subskrypcji

Zaktualizuj wartość w ramach subskrypcji.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Poprawka:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid  |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.    |
| authorization      |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Ładunek żądania:*

```json
Request Body:
{
    "quantity": 5
}
```

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operacja lokalizacji | Link do zasobu, można uzyskać stanu operacji.   |

*Kody odpowiedzi:*

Kod: 202<br>
Żądanie, aby zmienić ilość zostało zaakceptowane. Partner powinien sondowanie operacji lokalizacji do ustalenia powodzenia lub niepowodzenia. <br>

Kod: 400<br>
Nieprawidłowe żądanie: błędy sprawdzania poprawności.


Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 404<br>
Nie można odnaleźć.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Tylko plan lub ilość, może być poprawionego w tym samym czasie, nie oba. Umożliwia edycję w subskrypcji przy użyciu **aktualizacji** nie znajduje się w `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Usuwanie subskrypcji

Anulowanie subskrypcji, a następnie usuń określonej subskrypcji.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Usuwanie<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.   |
|  x-ms-correlationid  |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.   |
|  authorization     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kody odpowiedzi:*

Kod: 202<br>
Partner zainicjował wywołanie, aby anulować subskrypcję SaaS.<br>

Kod: 400<br>
Usuwanie subskrypcji przy użyciu **Usuń** nie `allowedCustomerOperations`.

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 404<br>
Nie można odnaleźć.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Operacje interfejsu API

Interfejs API operacji obsługuje następujące operacje poprawek i Get.

#### <a name="list-outstanding-operations"></a>Oczekujące operacje dotyczące list 

Wyświetla listę oczekujących operacji dla bieżącego wydawcy. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry zapytania:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Wersja operacji przy użyciu dla tego żądania.                |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  authorization     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kody odpowiedzi:*

Kod: 200<br> Pobiera listę oczekujących operacji w ramach subskrypcji. Ładunek odpowiedzi:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Kod: 400<br>
Nieprawidłowe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 404<br>
Nie można odnaleźć.

Kod: 500<br>
Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Pobierz stan operacji

Włącza z wydawcą, aby śledzić stan operacji asynchronicznej wyzwalane określonego (takie jak `subscribe`, `unsubscribe`, `changePlan`, lub `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  authorization     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kody odpowiedzi:*<br>

Kod: 200<br> Pobiera określony oczekującą operację SaaS. Ładunek odpowiedzi:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Kod: 400<br>
Nieprawidłowe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.
 
Kod: 404<br>
Nie można odnaleźć.

Kod: 500<br> Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aktualizuje stan operacji

Aktualizuje stan operacji w celu wskazania powodzenia lub błędu przy użyciu podanych wartości.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Poprawki<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Wersja operacji przy użyciu dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |
|  operationId       | Operacja, która jest kończone. |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  authorization     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Ładunek żądania:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Kody odpowiedzi:*

Kod: 200<br> Wywołanie do informowania o zakończeniu operacji po stronie partnerów. Na przykład tej odpowiedzi może oznaczać zmiana stanowisk lub planami.

Kod: 400<br>
Nieprawidłowe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Brak autoryzacji. Token uwierzytelniania nie został podany lub jest nieprawidłowy lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 404<br>
Nie można odnaleźć.

Kod: 409<br>
Wystąpił konflikt. Na przykład nowsza transakcja jest już spełnione.

Kod: 500<br> Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementacja elementu webhook w usłudze SaaS

Wydawca musi implementować element webhook w tej usłudze SaaS w celu proaktywnego powiadamiania użytkowników o zmianach wprowadzonych w swojej usłudze. Oczekiwano usługi SaaS do wywoływania operacji interfejsu API, aby zweryfikować i autoryzować przed podjęciem działania na powiadomień elementu webhook.

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Gdy akcja może być jedną z następujących czynności: 
- `unsubscribe` (Jeśli zasób został usunięty)
- `changePlan` (podczas operacji zmiany w planie została ukończona)
- `changeQuantity` (podczas operacji ilość zmian została ukończona)
- `suspend` (Jeśli zasób jest wstrzymane)
- `reinstate` (gdy zasobów zostały przywrócone po zawieszeniu)

Gdy stan może być jedną z następujących czynności: 
- **NotStarted** <br>
 - **InProgress** <br>
- **Powodzenie** <br>
- **Niepowodzenie** <br>
- **Konflikt** <br>

W powiadomieniu elementu webhook, Stany informacje z możliwością działania są **Powodzenie** i **niepowodzenie**. Cykl życia operacji pochodzi z **NotStarted** na stan końcowy, takich jak **Powodzenie**, **nie powiodło się**, lub **konflikt**. Jeśli zostanie wyświetlony **NotStarted** lub **InProgress**, przejdź do żądania stanu za pośrednictwem UZYSKAĆ interfejsu API, dopóki operacja osiągnie stan końcowy przed podjęciem działania. 

## <a name="mock-apis"></a>Testowanie interfejsów API

Aby ułatwić Ci rozpoczęcie pracy z programowaniem, szczególnie prototypowego projekty również jako testowania, można użyć naszych makiety interfejsów API. 

Hosta punktu końcowego: `https://marketplaceapi.microsoft.com/api` (bez uwierzytelniania wymagany)<br/>
Wersja interfejsu API: `2018-09-15`<br/>
Przykład identyfikatora URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Ścieżki punktu końcowego interfejsu API są takie same w rzeczywistych i makiety interfejsów API, ale różnią się wersji interfejsu API. Wersja jest `2018-09-15` wersję makiety i `2018-08-31` wersji produkcyjnej. 

Dowolne wywołania interfejsu API, w tym artykule jest możliwe do endpoint makiety hosta. Ogólnie rzecz biorąc należy oczekiwać pobieranie danych testowych jako odpowiedź. Wywołania metod subskrypcji aktualizacji w interfejsie API makiety zawsze zwracają 500. 

## <a name="next-steps"></a>Kolejne kroki

Deweloperzy mogą także programowo pobrać i manipulowania nimi profile wydawcy, oferty i obciążeń przy użyciu [interfejsów API REST usługi Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
