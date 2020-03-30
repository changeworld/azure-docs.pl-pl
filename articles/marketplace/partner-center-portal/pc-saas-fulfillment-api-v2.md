---
title: Api realizacji SaaS v2 | Azure Marketplace
description: W tym artykule wyjaśniono, jak utworzyć ofertę SaaS i zarządzać nią w witrynie AppSource i portalu Azure Marketplace przy użyciu skojarzonych interfejsów API realizacji w wersji 2.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275768"
---
# <a name="saas-fulfillment-apis-version-2"></a>Interfejsy API realizacji SaaS, wersja 2 

W tym artykule opisano interfejsy API, które umożliwiają partnerom sprzedawanie aplikacji SaaS w portalu AppSource marketplace i portalu Azure Marketplace. Te interfejsy API są wymagane dla transakcji SaaS oferuje w appsource i portalu Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Zarządzanie cyklem życia subskrypcji SaaS

Usługa Azure SaaS zarządza całym cyklem życia zakupu subskrypcji SaaS. Używa interfejsów API realizacji jako mechanizm do kierowania rzeczywistą realizacji, zmiany w planach i usunięcie subskrypcji z partnerem. Rachunek klienta jest oparty na stanie subskrypcji SaaS, który utrzymuje firma Microsoft. Na poniższym diagramie przedstawiono stany i operacje, które napędzają zmiany między stanami.

![Stany cyklu życia subskrypcji SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stany subskrypcji SaaS

W poniższej tabeli wymieniono stany inicjowania obsługi administracyjnej dla subskrypcji SaaS, w tym opis i diagram sekwencji dla każdego (jeśli dotyczy). 

#### <a name="provisioning"></a>Inicjowanie obsługi

Gdy klient inicjuje zakup, partner otrzymuje te informacje w kodzie autoryzacji na interaktywnej stronie sieci web klienta, która używa parametru ADRESU URL. Przykładem `https://contoso.com/signup?token=..`jest adres URL strony docelowej `https://contoso.com/signup`w Centrum partnerów. Kod autoryzacji można sprawdzić i wymienić na szczegóły usługi inicjowania obsługi administracyjnej, wywołując resolve interfejsu API.  Po zakończeniu inicjowania obsługi administracyjnej usługi SaaS, wysyła wywołanie aktywacji, aby zasygnalizować, że realizacja została zakończona, a klient może zostać obciążony. 

Na poniższym diagramie przedstawiono sekwencję wywołań interfejsu API dla scenariusza inicjowania obsługi administracyjnej.  

![Interfejs API wymaga inicjowania obsługi administracyjnej usługi SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Przygotowana

Ten stan jest stanem ustalonym usługi aprowizowana.

##### <a name="provisioning-for-update"></a>Inicjowanie obsługi administracyjnej do aktualizacji 

Ten stan oznacza, że aktualizacja istniejącej usługi jest w toku. Taka aktualizacja może być zainicjowana przez klienta, albo z rynku, albo z usługi SaaS (tylko w przypadku transakcji bezpośrednich do klienta).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Inicjowanie obsługi administracyjnej do aktualizacji (gdy jest inicjowane z marketplace)

Na poniższym diagramie przedstawiono sekwencję akcji, gdy aktualizacja jest inicjowana z portalu Marketplace.

![Wywołania interfejsu API po zainicjowaniu aktualizacji z portalu Marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Inicjowanie obsługi administracyjnej do aktualizacji (po zainicjowaniu z usługi SaaS)

Na poniższym diagramie przedstawiono akcje, gdy aktualizacja jest inicjowana z usługi SaaS. (Wywołanie elementu webhook jest zastępowane przez aktualizację subskrypcji zainicjowanej przez usługę SaaS). 

![Wywołania interfejsu API po zainicjowaniu aktualizacji z usługi SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

Ten stan wskazuje, że płatność odbiorcy nie została odebrana. Zgodnie z zasadami zapewnimy klientowi okres prolongaty przed anulowaniem subskrypcji. Gdy subskrypcja jest w tym stanie: 

- Jako partner możesz obniżyć lub zablokować dostęp użytkownika do usługi.
- Subskrypcja musi być utrzymywana w stanie odzyskania, który może przywrócić pełną funkcjonalność bez utraty danych lub ustawień. 
- Oczekiwać, aby uzyskać żądanie przywrócenia dla tej subskrypcji za pośrednictwem interfejsów API realizacji lub żądania de-inicjowania obsługi administracyjnej na koniec okresu prolongaty. 

#### <a name="unsubscribed"></a>Anulowanie subskrypcji 

Subskrypcje osiągają ten stan w odpowiedzi na jawne żądanie klienta lub niepłacenie należności. Partner oczekuje, że dane klienta są przechowywane do odzyskania na żądanie przez określoną liczbę dni, a następnie usuwane. 


## <a name="api-reference"></a>Odwołanie API

Ta sekcja dokumentuje *interfejs API subskrypcji* SaaS i interfejs API *operacji*.  Wartość parametru `api-version` dla interfejsów API w `2018-08-31`wersji 2 to .  


### <a name="parameter-and-entity-definitions"></a>Definicje parametrów i encji

W poniższej tabeli wymieniono definicje typowych parametrów i jednostek używanych przez interfejsy API realizacji.

|     Encja/parametr     |     Definicja                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identyfikator guid zasobu SaaS.  |
| `name`                   | Przyjazna nazwa podana dla tego zasobu przez klienta. |
| `publisherId`            | Unikatowy identyfikator ciągu dla każdego wydawcy (na przykład: "contoso"). |
| `offerId`                | Unikatowy identyfikator ciągu dla każdej oferty (na przykład: "offer1").  |
| `planId`                 | Unikatowy identyfikator ciągu dla każdego planu/jednostki SKU (na przykład: "srebrny"). |
| `operationId`            | Identyfikator guid dla określonej operacji.  |
|  `action`                | Akcja wykonywana na zasobie, `Unsubscribe`, `Suspend` `Reinstate`, `ChangePlan`, `ChangeQuantity` `Transfer`lub , , . |
|   |   |

Globalnie unikatowe[identyfikatory (GUID)](https://en.wikipedia.org/wiki/Universally_unique_identifier)to 128-bitowe (32-szesnastkowe) liczby, które są zwykle generowane automatycznie. 

#### <a name="resolve-a-subscription"></a>Rozwiązywanie problemu z subskrypcją 

Punkt końcowy rozpoznawania umożliwia wydawcy rozpoznać token portalu marketplace do identyfikatora trwałego zasobu. Identyfikator zasobu jest unikatowym identyfikatorem subskrypcji SaaS. Gdy użytkownik jest przekierowywane do witryny sieci Web partnera, adres URL zawiera token w parametrach kwerendy. Oczekuje się, że partner użyje tego tokenu i złożyć żądanie, aby go rozwiązać. Odpowiedź zawiera unikatowy identyfikator subskrypcji SaaS, nazwę, identyfikator oferty i plan zasobu. Ten token jest ważny tylko przez jedną godzinę. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Opublikuj<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (Wersja apiwersa        |  Wersja operacji do użycia dla tego żądania.  |

*Nagłówki żądań:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  x-ms-korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Na przykład:`Bearer <access_token>`" ". |
|  x-ms-marketplace-token  |  Parametr zapytania tokenu w adresie URL, gdy użytkownik jest przekierowywał do `https://contoso.com/signup?token=..`witryny sieci Web partnera SaaS z platformy Azure (na przykład: ). *Uwaga:* Adres URL dekoduje wartość tokenu z przeglądarki przed użyciem go.  |

*Kody odpowiedzi:*

Kod: 200<br>
Rozwiązuje nieprzezroczysty token do subskrypcji SaaS. Organ odpowiedzi:
 

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
Zła prośba. x-ms-marketplace-token brakuje, zniekształcony lub wygasł.

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

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

### <a name="subscription-api"></a>Interfejs API subskrypcji

Interfejs API subskrypcji obsługuje następujące operacje HTTPS: **Pobierz**, **Opublikuj**, **Patch**i **Usuń**.


#### <a name="list-subscriptions"></a>Lista subskrypcji

Wyświetla listę wszystkich subskrypcji SaaS dla wydawcy.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametry kwerendy:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion (Wersja apiwersa  |  Wersja operacji do użycia dla tego żądania.  |

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| x-ms-korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
| autoryzacja      |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Na przykład:`Bearer <access_token>`" ".  |

*Kody odpowiedzi:*

Kod: 200 <br/>
Pobiera wydawcy i odpowiadające subskrypcje dla wszystkich ofert wydawcy, na podstawie tokenu uwierzytelniania.

>[!Note]
>[Makiety interfejsów API](#mock-apis) są używane podczas pierwszego tworzenia oferty, podczas gdy rzeczywiste interfejsy API muszą być używane podczas publikowania oferty.  Prawdziwe interfejsy API i makiety interfejsów API różnią się w pierwszym wierszu kodu.  W rzeczywistym interfejsie `subscription` API znajduje się sekcja, podczas gdy ta sekcja nie istnieje dla makiety interfejsu API.

Ładunek odpowiedzi dla makiety interfejsu API:<br>

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
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
I dla prawdziwego API: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
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
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Token kontynuacji będzie obecny tylko wtedy, gdy istnieją dodatkowe "strony" planów do pobrania. 

Kod: 403 <br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy. 

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

#### <a name="get-subscription"></a>Uzyskaj subskrypcję

Pobiera określoną subskrypcję SaaS. Użyj tego połączenia, aby uzyskać informacje o licencji i planach.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozpoznaniu tokenu za pośrednictwem interfejsu API rozpoznawania.   |
|  ApiVersion (Wersja apiwersa        |   Wersja operacji do użycia dla tego żądania.   |

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  x-ms-korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Na przykład:`Bearer <access_token>`" ".  |

*Kody odpowiedzi:*

Kod: 200<br>
Pobiera subskrypcję SaaS z identyfikatora. Ładowność odpowiedzi:<br>

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
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.<br> 

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

Użyj tego połączenia, aby dowiedzieć się, czy istnieją jakieś oferty prywatne lub publiczne dla bieżącego wydawcy.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (Wersja apiwersa        |   Wersja operacji do użycia dla tego żądania.  |

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  x-ms-korelacji  | Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  autoryzacja     |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Na przykład:`Bearer <access_token>`" ". |

*Kody odpowiedzi:*

Kod: 200<br>
Pobiera listę dostępnych planów dla klienta. Organ odpowiedzi:

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
Nie znaleziono.<br> 

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy. <br> 

Kod: 500<br>
Wewnętrzny błąd serwera.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktywowanie subskrypcji

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Opublikuj<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (Wersja apiwersa        |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozpoznaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądań:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-ms-korelacji  | Unikatowa wartość ciągu dla operacji na kliencie. Ten ciąg koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Na przykład:`Bearer <access_token>`" ". |

*Żądanie ładunku:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Kody odpowiedzi:*

Kod: 200<br>
Aktywuje subskrypcję.<br>

Kod: 400<br>
Złe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

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

#### <a name="change-the-plan-on-the-subscription"></a>Zmienianie planu w ramach subskrypcji

Zaktualizuj plan w ramach subskrypcji.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (Wersja apiwersa        |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozpoznaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-ms-korelacji  |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.    |
| autoryzacja      |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Na przykład:`Bearer <access_token>`" ".  |

*Żądanie ładunku:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operacja-lokalizacja | Łącze do zasobu, aby uzyskać stan operacji.   |

*Kody odpowiedzi:*

Kod: 202<br>
Wniosek o zmianę planu został zaakceptowany. Oczekuje się, że partner sonduje operację-lokalizację, aby określić powodzenie lub niepowodzenie. <br>

Kod: 400<br>
Złe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

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
>Tylko plan lub ilość mogą być załatane w tym czasie, a nie oba. Edycji w subskrypcji z **aktualizacją** nie ma . `allowedCustomerOperations`

#### <a name="change-the-quantity-on-the-subscription"></a>Zmienianie ilości w subskrypcji

Zaktualizuj ilość w subskrypcji.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (Wersja apiwersa        |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozpoznaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-ms-korelacji  |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.    |
| autoryzacja      |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Na przykład:`Bearer <access_token>`" ".  |

*Żądanie ładunku:*

```json
Request Body:
{
    "quantity": 5
}
```

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operacja-lokalizacja | Połącz się z zasobem, aby uzyskać stan operacji.   |

*Kody odpowiedzi:*

Kod: 202<br>
Żądanie zmiany ilości zostało zaakceptowane. Oczekuje się, że partner sonduje operację-lokalizację, aby określić powodzenie lub niepowodzenie. <br>

Kod: 400<br>
Złe żądanie: błędy sprawdzania poprawności.


Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

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
>Tylko plan lub ilość mogą być załatane w tym czasie, a nie oba. Edycji w subskrypcji z **aktualizacją** nie ma . `allowedCustomerOperations`

#### <a name="delete-a-subscription"></a>Usuwanie subskrypcji

Anulowanie subskrypcji i usunięcie określonej subskrypcji.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Usuń<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (Wersja apiwersa        |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozpoznaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądań:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.   |
|  x-ms-korelacji  |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.   |
|  autoryzacja     |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Na przykład:`Bearer <access_token>`" ".  |

*Kody odpowiedzi:*

Kod: 202<br>
Partner zainicjował wezwanie do anulowania subskrypcji usługi SaaS.<br>

Kod: 400<br>
Usuń w ramach **Delete** subskrypcji `allowedCustomerOperations`z usuń nie w pliku .

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

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


### <a name="operations-api"></a>Interfejs API operacji

Interfejs API operacji obsługuje następujące operacje Patch i Get.

#### <a name="list-outstanding-operations"></a>Lista zaległych operacji 

Wyświetla listę zaległych operacji dla bieżącego wydawcy. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry kwerendy:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion (Wersja apiwersa                |   Wersja operacji do użycia dla tego żądania.                |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozpoznaniu tokenu przy użyciu interfejsu API rozpoznawania.  |

*Nagłówki żądań:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-ms-korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Na przykład:`Bearer <access_token>`" ".  |

*Kody odpowiedzi:*

Kod: 200<br> Pobiera listę oczekujących operacji w ramach subskrypcji. Ładowność odpowiedzi:

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
Złe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

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

#### <a name="get-operation-status"></a>Uzyskaj stan operacji

Umożliwia wydawcy śledzenie stanu określonej wyzwalanej operacji asynchronicznego `ChangeQuantity`(np. `Subscribe` `Unsubscribe` `ChangePlan`

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion (Wersja apiwersa        |  Wersja operacji do użycia dla tego żądania.  |

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  x-ms-korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
|  autoryzacja     |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Na przykład:`Bearer <access_token>`" ".  |

*Kody odpowiedzi:*<br>

Kod: 200<br> Pobiera określoną oczekującą operację SaaS. Ładowność odpowiedzi:

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
Złe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.
 
Kod: 404<br>
Nie znaleziono.

Kod: 500<br> Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aktualizowanie stanu operacji

Zaktualizuj stan operacji, aby wskazać powodzenie lub niepowodzenie z podanymi wartościami.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry kwerendy:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion (Wersja apiwersa       |  Wersja operacji do użycia dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozpoznaniu tokenu przy użyciu interfejsu API rozpoznawania.  |
|  operationId       | Operacja, która jest zakończona. |

*Nagłówki żądań:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  x-ms-korelacji |  Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
|  autoryzacja     |  [Pobierz token na okaziciela tokenu internetowego JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Na przykład:`Bearer <access_token>`" ".  |

*Żądanie ładunku:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Kody odpowiedzi:*

Kod: 200<br> Wezwanie do poinformowania o zakończeniu operacji po stronie partnera. Na przykład ta odpowiedź może sygnalizować zmianę miejsc lub planów.

Kod: 400<br>
Złe żądanie: błędy sprawdzania poprawności.

Kod: 403<br>
Nieautoryzowanych. Token uwierzytelniania nie został dostarczony lub jest nieprawidłowy lub żądanie próbuje uzyskać dostęp do nabycia, które nie należy do bieżącego wydawcy.

Kod: 404<br>
Nie znaleziono.

Kod: 409<br>
Konflikt. Na przykład nowsza transakcja jest już spełniona.

Kod: 500<br> Wewnętrzny błąd serwera.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementowanie elementu webhook w usłudze SaaS

Wydawca musi zaimplementować element webhook w tej usłudze SaaS, aby proaktywnie powiadamiać użytkowników o zmianach w jego usłudze. Usługa SaaS ma wywołać interfejs API operacji, aby sprawdzić poprawność i autoryzować przed podjęciem akcji na powiadomienie webhook.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
W przypadku gdy działanie może być jedną z następujących czynności: 
- `Unsubscribe`(po usunięciu zasobu)
- `ChangePlan`(po zakończeniu operacji planu zmian)
- `ChangeQuantity`(po zakończeniu operacji ilości zmiany)
- `Suspend`(gdy zasób został zawieszony)
- `Reinstate`(gdy zasób został przywrócony po zawieszeniu)

W przypadku gdy status może być jednym z następujących: 
- **NotStarted (Nierozpoczęcie)** <br>
 - **InProgress (Ruch przychodzący)** <br>
- **Powodzenie** <br>
- **Niepowodzenie** <br>
- **Konflikt** <br>

W powiadomieniu elementu webhook stany, które można zasłaniać, są **powiódł się** i **nie powiódł się**. Cykl życia operacji jest od **NotStarted** do stanu terminalu, takiego jak **Failed**, **Failed**lub **Conflict**. Jeśli otrzymasz **NotStarted** lub **InProgress,** kontynuuj żądanie stanu za pośrednictwem interfejsu GET API, dopóki operacja nie osiągnie stanu terminala przed podjęciem działania. 

## <a name="mock-apis"></a>Makiety interfejsów API

Możesz użyć naszych makiet interfejsów API, aby ułatwić rozpoczęcie opracowywania, szczególnie prototypowania, a także testowania projektów. 

Punkt końcowy `https://marketplaceapi.microsoft.com/api` hosta: (nie jest wymagane uwierzytelnianie)<br/>
Wersja interfejsu API:`2018-09-15`<br/>
Przykładowy identyfikator URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Ścieżki punktu końcowego interfejsu API są takie same zarówno w makiety i rzeczywistych interfejsów API, ale wersje interfejsu API są różne. Wersja jest `2018-09-15` dla wersji `2018-08-31` makiety i dla wersji produkcyjnej. 

Każdy z wywołań interfejsu API w tym artykule można dokonać makiety punktu końcowego hosta. Ogólnie rzecz biorąc należy oczekiwać, aby uzyskać makiety danych z powrotem jako odpowiedź. Wywołania metody subskrypcji aktualizacji w makiety interfejsu API zawsze zwracają 500. 

## <a name="next-steps"></a>Następne kroki

Deweloperzy mogą również programowo pobierać i manipulować obciążeniami, ofertami i profilami wydawców za pomocą [interfejsów API REST portalu partnerów w chmurze.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)
