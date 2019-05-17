---
title: SaaS realizacji interfejsu API w wersji 2 | Portal Azure Marketplace
description: Wyjaśnia sposób tworzenia oferty SaaS w usłudze AppSource i portalu Azure Marketplace przy użyciu realizacji skojarzone interfejsy API w wersji 2.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pabutler
ms.openlocfilehash: 551f3be2ca23bc18224d28faeea6a6df80eba1db
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823537"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS realizacji interfejsy API wersji 2 

Ten artykuł zawiera interfejs API, który pozwala niezależnym dostawcom oprogramowania (ISV) i sprzedawać swoje aplikacje SaaS w portalu Azure Marketplace i AppSource. Ten interfejs API jest wymagane do wyświetlania listy SaaS oferują usługi AppSource i portalu Azure Marketplace.

## <a name="managing-the-saas-subscription-lifecycle"></a>Zarządzanie cyklem życia subskrypcji SaaS

Microsoft SaaS Service zarządza cały cykl życia zakupu subskrypcji SaaS i używa interfejsu API realizacji mechanizm do realizacji rzeczywiste, zmienia się na planów i usunięcie subskrypcji z niezależnym dostawcą oprogramowania. Klient jest rozliczana na podstawie stanu subskrypcji SaaS, które firma Microsoft udostępnia. Poniższy diagram przedstawia Stany i operacje, które dysku zmiany między stanami.

![Stany cykl życia subskrypcji w modelu SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stany subskrypcji SaaS

W poniższej tabeli wymieniono stanach aprowizacji dla subskrypcji SaaS, w tym diagramie opis i sekwencji dla każdej (jeśli dotyczy). 

#### <a name="provisioning"></a>Inicjowanie obsługi

Gdy klient inicjuje zakupu, niezależny dostawca oprogramowania odbiera tych informacji w zawiera kod uwierzytelniania klienta interactive stronie sieci web za pomocą parametru adresu URL. Na przykład: `https://contoso.com/signup?token=..`, gdzie jest dostawca adres URL strony docelowej w Centrum partnerskim `https://contoso.com/signup`. Kod uwierzytelniania można sprawdzić poprawności i wymieniane, aby uzyskać szczegółowe informacje o co powinna być przygotowana przez wywołanie interfejsu API rozwiązania.  Po zakończeniu aprowizacji usługi SaaS wysyła wywołania Aktywuj sygnał, że realizację zostało zakończone i może być rozliczana klienta.  Na poniższym diagramie przedstawiono sekwencję wywołań interfejsu API dla scenariusza inicjowania obsługi administracyjnej.  

![Wywołania interfejsu API do inicjowania obsługi usługi SaaS.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Przeprowadzono aprowizację

Ten stan jest w stanie stabilności aprowizowanej usługi.

#### <a name="provisioning-for-update"></a>Inicjowanie obsługi administracyjnej dla aktualizacji
(z witryny marketplace) 

Ten stan reprezentuje aktualizację do istniejącej usługi jest w stanie oczekiwania. Takiej aktualizacji może być inicjowane przez klienta w witrynie marketplace lub w usłudze SaaS (tylko w przypadku bezpośrednio do klienta transakcji). Na poniższym diagramie przedstawiono akcje po zainicjowaniu aktualizacji z witryny marketplace.

![Po zainicjowaniu aktualizacji z witryny marketplace wywołań interfejsu API.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Inicjowanie obsługi administracyjnej dla aktualizacji  
(z usługi SaaS) 

Na poniższym diagramie przedstawiono akcje, gdy aktualizacja jest inicjowane przez usługi SaaS. (Wywołanie elementu webhook jest zastępowany przez aktualizację subskrypcji inicjowane przez usługi SaaS. 

![Liczba wywołań interfejsu API, gdy aktualizacja jest inicjowane przez usługi SaaS.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Wstrzymane

Ten stan wskazuje, nie otrzymał płatności odbiorcy. Zgodnie z zasadami przekazujemy klienta okres prolongaty przed unfulfilling subskrypcji. Gdy subskrypcja jest w tym stanie: 

- Jako niezależny dostawca oprogramowania można zmniejszyć bądź go zablokować dostęp użytkownika do usługi. 
- Subskrypcja musi znajdować się w stanie możliwe do odzyskania, który można przywrócić pełną funkcjonalność bez utraty danych lub ustawień. 
- Można oczekiwać, że można pobrać przywrócenie żądania dla tej subskrypcji za pośrednictwem realizacji interfejsu API lub żądanie do inicjowania obsługi administracyjnej z końcem okresu prolongaty. 

#### <a name="unsubscribed"></a>Brak subskrypcji 

Subskrypcje dotrzeć do tego stanu w odpowiedzi na żądanie klienta jawne lub w odpowiedzi na płatności opłat. Oczekuje od niezależnych dostawców oprogramowania się, że danych klienta jest zachowywana do odzyskania na żądanie, co X dni, a następnie usuwany. 


## <a name="api-reference"></a>Dokumentacja interfejsu API

W tej sekcji omówiono SaaS *API subskrypcji* i *operacji interfejsu API*.  Wartość `api-version` parametru w wersji 2 interfejsy API są `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definicje parametrów i jednostki

Poniższa lista zawiera definicje dla wspólnych parametrów i jednostek używanych przez interfejsy API realizacji.

|     Parametr/w jednostce     |     Definicja                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identyfikator GUID zasobu SaaS  |
| `name`                   | Przyjazna nazwa podana dla tego zasobu przez klienta |
| `publisherId`            | Identyfikator unikatowy ciąg dla każdego wydawcy, na przykład "contoso" |
| `offerId`                | Identyfikator unikatowy ciąg dla każdej oferty, na przykład "offer1"  |
| `planId`                 | Identyfikator unikatowy ciąg w ramach każdego planu/jednostki sku, na przykład "silver" |
| `operationId`            | Identyfikator GUID dla określonej operacji  |
|  `action`                | Działania wykonywane w zasobie, albo `subscribe`, `unsubscribe`, `suspend`, `reinstate`, lub `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

Globalnie unikatowe identyfikatory ([identyfikatorów GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) są liczbami (32 szesnastkowe) 128-bitowe, które zazwyczaj są generowane automatycznie. 

#### <a name="resolve-a-subscription"></a>Rozwiązanie z subskrypcji 

Punkt końcowy resolve umożliwia z wydawcą, aby rozpoznać tokenu marketplace trwały identyfikator zasobu. Identyfikator zasobu jest unikatowy identyfikator subskrypcji w modelu SAAS.  Gdy użytkownik jest przekierowywany do witryny sieci Web niezależnych dostawców oprogramowania, adres URL zawiera token w parametrach zapytania. Niezależny dostawca oprogramowania oczekuje na używanie tego tokenu w celu wykonania żądania, aby go rozwiązać. Odpowiedź zawiera identyfikator subskrypcji w modelu SAAS unikatową nazwę, Identyfikatora oferty i plan dla zasobu. Ten token jest prawidłowy tylko godzinę. 

**Wpis:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji do użycia dla tego żądania  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Unikatowy ciąg wartości dla śledzenia żądania od klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  Autoryzacja     |  [Pobierz token JSON web token elementu nośnego (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Parametr tokenu zapytania w adresie URL, gdy użytkownik jest przekierowywany do witryny sieci Web SaaS ISV na platformie Azure (na przykład: `https://contoso.com/signup?token=..`). *Uwaga:* Adres URL dekoduje wartość tokenu za pomocą przeglądarki przed jego użyciem.  |

*Kody odpowiedzi:*

Kod: 200<br>
Subskrypcja SaaS jest rozpoznawany jako nieprzezroczysty tokenu.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Nieprawidłowe żądanie. x-ms-Portal marketplace — token jest Brak, źle sformułowane lub wygasł.

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 500<br>
Wewnętrzny błąd serwera

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


#### <a name="list-subscriptions"></a>Wyświetl listę subskrypcji

Wyświetla listę wszystkich subskrypcji SaaS dla wydawcy.

**Pobierz:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Parametry zapytania:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Wersja operacji przy użyciu dla tego żądania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Unikatowy ciąg wartości dla śledzenia żądania od klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
| Autoryzacja      |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Kody odpowiedzi:*

Kod: 200 <br/>
Oparte na token uwierzytelniania, Uzyskaj wydawcy i odpowiednich subskrypcji dla wszystkich wydawcy, oferty.<br> Ładunek odpowiedzi:<br>

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
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Token kontynuacji jest dostępne tylko w przypadku dodatkowych "strony" planów do pobrania. 

Kod: 403 <br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy. 

Kod: 500 Wewnętrzny błąd serwera

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

**Pobierz:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Unikatowy identyfikator subskrypcji w modelu SaaS, uzyskany po rozwiązaniu tokenu za pośrednictwem rozwiązania interfejsu API   |
|  ApiVersion        |   Wersja operacji do użycia dla tego żądania   |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Unikatowy ciąg wartości dla śledzenia żądania od klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  Autoryzacja     |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Kody odpowiedzi:*

Kod: 200<br>
Pobiera SaaS subskrypcji z identyfikatorem<br> Ładunek odpowiedzi:<br>

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
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kod: 404<br>
Nie znaleziono<br> 

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 500<br>
Wewnętrzny błąd serwera<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Lista dostępnych planów

Aby sprawdzić, czy publicznego i prywatnego ofert dla bieżącej wydawcy, należy użyć tego wywołania.

**Pobierz:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Wersja operacji do użycia dla tego żądania  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Unikatowy ciąg wartości dla śledzenia żądania od klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  x-ms-correlationid  | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  Autoryzacja     |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Kody odpowiedzi:*

Kod: 200<br>
Pobierz listę dostępnych planów dla klientów.<br>

Treść odpowiedzi:

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
Nie znaleziono<br> 

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy. <br> 

Kod: 500<br>
Wewnętrzny błąd serwera<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktywuj subskrypcję

**Wpis:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji do użycia dla tego żądania  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Unikatowy ciąg wartości dla śledzenia żądania od klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid  | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten ciąg jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  Autoryzacja     |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Żądanie:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Kody odpowiedzi:*

Kod: 200<br>
Aktywuje subskrypcji.<br>

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 500<br>
Wewnętrzny błąd serwera

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

**Poprawka:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid  |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.    |
| Autoryzacja      |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
Żądanie, aby zmienić plan zostało zaakceptowane. Niezależny dostawca oprogramowania oczekuje się, aby sondować lokalizację operację, aby określić Powodzenie/niepowodzenie. <br>

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań.

>[!Note]
>Tylko plan lub ilość, może być poprawionego w tym samym czasie, nie oba. Umożliwia edycję w subskrypcji przy użyciu **aktualizacji** nie znajduje się w `allowedCustomerOperations`.

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 500<br>
Wewnętrzny błąd serwera

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>Zmień ilość w ramach subskrypcji

Zaktualizuj wartość w ramach subskrypcji.

**Poprawka:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid  |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.    |
| Autoryzacja      |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
Zaakceptowane. Żądanie, aby zmienić ilość zostało zaakceptowane. Niezależny dostawca oprogramowania oczekuje się, aby sondować lokalizację operację, aby określić Powodzenie/niepowodzenie. <br>

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań.

>[!Note]
>Tylko plan lub ilość, może być poprawionego w tym samym czasie, nie oba. Umożliwia edycję w subskrypcji przy użyciu **aktualizacji** nie znajduje się w `allowedCustomerOperations`.

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 500<br>
Wewnętrzny błąd serwera

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Usuwanie subskrypcji

Anulowanie subskrypcji, a następnie usuń określonej subskrypcji.

**Usuwanie:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

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
|  Autoryzacja     |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Kody odpowiedzi:*

Kod: 202<br>
Niezależny dostawca oprogramowania zainicjował wywołanie do wskazania anulować subskrypcję SaaS.<br>

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Usuwanie subskrypcji przy użyciu **Usuń** nie `allowedCustomerOperations`.

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 500<br>
Wewnętrzny błąd serwera

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

**Pobierz:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Parametry zapytania:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Wersja operacji przy użyciu dla tego żądania.                |
| subscriptionId     | Unikatowy identyfikator subskrypcji SaaS, która uzyskuje się po usunięciu tokenu przy użyciu interfejsu API rozwiązania.  |

*Nagłówki żądania:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  Autoryzacja     |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Kody odpowiedzi:*

Kod: 200<br> Pobiera listę oczekujących operacji w ramach subskrypcji.<br>
Ładunek odpowiedzi:

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

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 500<br>
Wewnętrzny błąd serwera

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Pobierz stan operacji

Umożliwia z wydawcą, aby śledzić stan operacji asynchronicznej wyzwalane określony (subskrypcja / anulować / Change plan / Change ilość).

**Pobierz:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parametry zapytania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Wersja operacji przy użyciu dla tego żądania.  |

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  Autoryzacja     |[JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Kody odpowiedzi:* Kod: 200<br> Pobiera określony oczekującą operację SaaS<br>
Ładunek odpowiedzi:

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

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.
 
Kod: 500<br> Wewnętrzny błąd serwera

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aktualizuje stan operacji

Zaktualizuj stan operacji można wskazywać Powodzenie/Niepowodzenie przy użyciu podanych wartości.

**Poprawka:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

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
|   x-ms-requestid   |   Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  x-ms-correlationid |  Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ten parametr jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
|  Autoryzacja     |  [JSON web token elementu nośnego (JWT) Pobierz token.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Ładunek żądania:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Kody odpowiedzi:*

Kod: 200<br> Wywołanie, aby informować o zakończeniu operacji po stronie niezależnego dostawcy oprogramowania. Na przykład tej odpowiedzi może oznaczać zmiana stanowisk/planów.

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu pozyskiwania, które nie należą do bieżącej wydawcy.

Kod: 409<br>
Wystąpił konflikt. Na przykład nowsza transakcja jest już spełnione

Kod: 500<br> Wewnętrzny błąd serwera

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Element Webhook w usłudze SaaS

Wydawca musi implementować element webhook w tej usłudze SaaS w celu proaktywnego powiadamiania użytkowników o zmianach wprowadzonych w swojej usłudze. Interfejs API powinien być nieuwierzytelnione i zostanie wywołana przez usługę Microsoft SaaS. Oczekiwano usługi SaaS do wywoływania operacji interfejsu API, aby zweryfikować i autoryzować przed podjęciem działania na powiadomień elementu webhook.

```json
{
    "operationId": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Subscribe",
    "timeStamp": "2018-12-01T00:00:00"
}
```

Gdy akcja może być jeden z nich: 
- `Subscribe`  (Jeśli zasób został aktywowany)
- `Unsubscribe` (Jeśli zasób został usunięty)
- `ChangePlan` (Podczas operacji zmiany w planie została ukończona)
- `ChangeQuantity` (Podczas operacji ilość zmian została ukończona)
- `Suspend` (Jeśli zasób jest wstrzymane)
- `Reinstate` (Gdy zasobów zostały przywrócone po zawieszeniu)


## <a name="mock-api"></a>Testowanie interfejsu API

Makiety interfejsów API umożliwia pomóc Ci rozpocząć pracę z programowaniem, szczególnie tworzenia prototypów i testowania projektów. 

Punkt końcowy hosta: `https://marketplaceapi.microsoft.com/api` <br/>
Wersja interfejsu API: `2018-09-15` <br/>
Uwierzytelnienie niewymagane <br/>
Przykładowy identyfikator Uri: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Ścieżki punktu końcowego interfejsu API są takie same, zarówno projekt, jak i rzeczywistych interfejsów API, ale różnią się wersji interfejsu API. Wersja jest 2018-09-15 dla pozorny i 2018-08-31 wersji produkcyjnej. 

Dowolne wywołania interfejsu API, w tym artykule jest możliwe do endpoint makiety hosta. Można oczekiwać, że można pobrać danych testowych jako odpowiedź. Ogólnie rzecz biorąc można oczekiwać, że można pobrać danych testowych jako odpowiedź. Wywołania metod subskrypcji aktualizacji w interfejsie API makiety zawsze zwracają 500. 

## <a name="next-steps"></a>Kolejne kroki

Deweloperzy mogą także programowo pobrać i manipulowania wydawcy, oferty i obciążeń, profile, przy użyciu [interfejsów API REST usługi Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
