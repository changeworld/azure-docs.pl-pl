---
title: SaaS realizacji interfejsu API w wersji 2 — witryna Azure Marketplace | Dokumentacja firmy Microsoft
description: Wyjaśnia sposób tworzenia oferty SaaS w portalu Azure Marketplace, za pomocą realizacji skojarzone interfejsy API w wersji 2.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 6d18adfaec965d858bdcb1f74ebcea89f57eea39
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878030"
---
# <a name="saas-fulfillment-api"></a>Realizacja SaaS interfejsu API

Ten artykuł szczegółowo opisuje interfejs API, który pozwala niezależnym dostawcom oprogramowania (ISV), aby zintegrować swoje aplikacje SaaS przy użyciu portalu Azure Marketplace. Ten interfejs API umożliwia aplikacji niezależnych dostawców oprogramowania do wzięcia udziału w wszystkie kanały commerce włączone: bezpośrednie, prowadzonej przez partnera (odsprzedawcy) i doprowadziło do pola.  Ten interfejs API jest wymagana dla list transactable SaaS oferty w portalu Azure Marketplace.


## <a name="managing-the-saas-subscription-lifecycle"></a>Zarządzanie cyklem życia subskrypcji SaaS

Microsoft SaaS Service zarządza cały cykl życia zakupu subskrypcji SaaS i używa interfejsu API realizacji mechanizm do realizacji rzeczywiste, zmienia się na planów i usunięcie subskrypcji z niezależnym dostawcą oprogramowania. Klient jest rozliczana na podstawie stanu subskrypcji SaaS, które firma Microsoft udostępnia. Poniższy diagram przedstawia Stany i operacje, które dysku zmiany między stanami.

![Stany cykl życia subskrypcji w modelu SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stany subskrypcji SaaS

W poniższej tabeli wymieniono stanach aprowizacji dla subskrypcji SaaS, w tym diagramie opis i sekwencji dla każdej (jeśli dotyczy). 

#### <a name="provisioning"></a>Inicjowanie obsługi

Gdy klient inicjuje zakupu, niezależny dostawca oprogramowania odbiera tych informacji w AuthCode na klienta interactive stronie sieci web za pomocą parametru adresu URL. AuthCode można sprawdzić poprawności i wymieniane, aby uzyskać szczegółowe informacje, z czego potrzebuje do zainicjowania obsługi administracyjnej.  Po zakończeniu aprowizacji usługi SaaS wysyła wywołania Aktywuj sygnał, że realizację zostało zakończone i może być rozliczana klienta.  Na poniższym diagramie przedstawiono sekwencję wywołań interfejsu API dla scenariusza inicjowania obsługi administracyjnej.  

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

W tej sekcji omówiono SaaS *API subskrypcji* i *operacji interfejsu API*.


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
| Autoryzacja      |  JSON web token (JWT) token elementu nośnego.  |

*Kody odpowiedzi:*

Kod: 200<br>
Pobierz token uwierzytelniania w oparciu wydawcy i odpowiednich subskrypcji dla wszystkich wydawcy, oferty.<br> Ładunek odpowiedzi:<br>

```json
{
  "subscriptions": [
      {
          "id": "",
          "name": "CloudEndure for Production use",
          "publisherId": "cloudendure",
          "offerId": "ce-dr-tier2",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "status": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Kod: 403 <br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika. 

Kod: 500 — wewnętrzny błąd serwera

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
|  Autoryzacja     |  JSON web token tokenu elementu nośnego (JWT)  |

*Kody odpowiedzi:*

Kod: 200<br>
Pobiera saas subskrypcji z identyfikatorem<br> Ładunek odpowiedzi:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"CloudEndure for Production use",
        "publisherId": "cloudendure",
        "offerId": "ce-dr-tier2",
        "planId": "silver",
        "quantity": "10"",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kod: 404<br>
Nie znaleziono<br> 

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika.

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

Użyj tego wywołania, aby sprawdzić, czy wszystkie oferty prywatne/publiczne dla bieżącego użytkownika.

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
|  Autoryzacja     |  JSON web token tokenu elementu nośnego (JWT) |

*Kody odpowiedzi:*

Kod: 200<br>
Pobierz listę dostępnych planów dla klientów.<br>

```json
Response Body:
[{
    "planId": "silver",
    "displayName": "Silver",
    "isPrivate": false
},
{
    "planId": "silver-private",
    "displayName": "Silver-private",
    "isPrivate": true
}]
```

Kod: 404<br>
Nie znaleziono<br> 

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowy lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika. <br> 

Kod: 500<br>
Wewnętrzny błąd serwera<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="resolve-a-subscription"></a>Rozwiązanie z subskrypcji 

Punkt końcowy resolve umożliwia użytkownikom można rozpoznać tokenu marketplace trwały identyfikator zasobu. Identyfikator zasobu jest unikatowy identyfikator subskrypcji w modelu SAAS.  Gdy użytkownik jest przekierowywany do witryny sieci Web niezależnych dostawców oprogramowania, adres URL zawiera token w parametrach zapytania. Niezależny dostawca oprogramowania oczekuje na używanie tego tokenu w celu wykonania żądania, aby go rozwiązać. Odpowiedź zawiera identyfikator subskrypcji w modelu SAAS unikatową nazwę, Identyfikatora oferty i plan dla zasobu. Ten token jest prawidłowy tylko godzinę. 

**Post:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

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
|  Autoryzacja     |  JSON web token tokenu elementu nośnego (JWT)  |
|  x-ms-marketplace-token  |  Parametr tokenu zapytania w adresie URL, gdy użytkownik jest przekierowywany do witryny sieci Web SaaS ISV na platformie Azure. *Uwaga:* Adres URL dekoduje wartość tokenu za pomocą przeglądarki przed jego użyciem. |

*Kody odpowiedzi:*

Kod: 200<br>
Subskrypcja SaaS jest rozpoznawany jako nieprzezroczysty tokenu.<br>

```json
Response body:
{
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",  
    "subscriptionName": "My Saas application",
    "offerId": "ce-dr-tier2",
    "planId": "silver",
    "quantity": "20",
    "operationId": " be750acb-00aa-4a02-86bc-476cbe66d7fa"  
}
```

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika.

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

#### <a name="activate-a-subscription"></a>Aktywuj subskrypcję

**Post:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

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
|  x-ms-correlationid  | Unikatowy ciąg wartości dla operacji na komputerze klienckim. To jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie jest podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
|  Autoryzacja     |  JSON web token tokenu elementu nośnego (JWT) |

*Żądanie:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Kody odpowiedzi:*

Kod: 202<br>
Aktywuje subskrypcji.<br>

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika.

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

#### <a name="update-a-subscription"></a>Aktualizowanie subskrypcji

Zaktualizuj lub Zmień plan subskrypcji przy użyciu podanych wartości.

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
| Autoryzacja      |  JSON web token (JWT) token elementu nośnego.  |

*Ładunek żądania:*

```json
Request Body:
{
    "planId": "gold",
    "quantity": ""
}
```

*Nagłówki żądania:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operacja lokalizacji | Link do zasobu, można uzyskać stanu operacji.   |

*Kody odpowiedzi:*

Kod: 202<br>
Niezależny dostawca oprogramowania inicjuje zmiany planu lub ilość zmian. <br>

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań.

>[!Note]
>Tylko plan lub ilość, może być poprawionego w tym samym czasie, nie oba. Umożliwia edycję w subskrypcji przy użyciu **aktualizacji** nie znajduje się w `allowedCustomerOperations`.

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika.

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
|  Autoryzacja     |  JSON web token (JWT) token elementu nośnego.   |

*Kody odpowiedzi:*

Kod: 200<br>
Niezależny dostawca oprogramowania zainicjował wywołanie do wskazania anulować subskrypcję SaaS.<br>

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Usuwanie subskrypcji przy użyciu **Usuń** nie `allowedCustomerOperations`.

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika.

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


#### <a name="update-a-subscription"></a>Aktualizowanie subskrypcji

Zaktualizuj subskrypcji przy użyciu podanych wartości.

**Poprawka:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operation/<operationId>?api-version=<ApiVersion>`**

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
|  Autoryzacja     |  JSON web token (JWT) token elementu nośnego.  |

*Ładunek żądania:*

```json
{
    "planId": "",
    "quantity": "",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}
```

*Kody odpowiedzi:*

Kod: 200<br> Wywołanie, aby informować o zakończeniu operacji po stronie niezależnego dostawcy oprogramowania. Na przykład może to być zmiana stanowisk/planów.

Kod: 404<br>
Nie znaleziono

Kod: 400<br>
Błędy nieprawidłowych weryfikacji żądań

Kod: 403<br>
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowy lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika.

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

#### <a name="list-outstanding-operations"></a>Oczekujące operacje dotyczące list 

Wyświetla listę oczekujących operacji dla bieżącego użytkownika. 

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
|  Autoryzacja     |  JSON web token (JWT) token elementu nośnego.  |

*Kody odpowiedzi:*

Kod: 200<br> Pobiera listę oczekujących operacji w ramach subskrypcji.<br>
Ładunek odpowiedzi:

```json
[{
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",  
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
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
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika.

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

Umożliwia użytkownikowi śledzenie stanu operacji asynchronicznych wyzwolone (Subskrybuj/anulowania subskrypcji/Zmień plan).

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
|  Autoryzacja     | JSON web token (JWT) token elementu nośnego.  |

*Kody odpowiedzi:* Kod: 200<br> Pobiera listę wszystkich oczekujących operacji SaaS<br>
Ładunek odpowiedzi:

```json
Response body:
[{
    "id  ": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
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
Bez autoryzacji. Nie podano tokenu uwierzytelniania, jest nieprawidłowa lub żądanie jest próba uzyskania dostępu nabycia, która nie należy do bieżącego użytkownika.
 
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
    "operationId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Activate",   // Activate/Delete/Suspend/Reinstate/Change[new]  
    "timeStamp": "2018-12-01T00:00:00"
}

```

<!-- Review following, might not be needed when this publishes -->


## <a name="mock-api"></a>Testowanie interfejsu API

Aby ułatwić Ci rozpoczęcie pracy z programowaniem, szczególnie tworzenia prototypów i projektów testowych, można użyć naszych makiety interfejsów API. 

Punkt końcowy hosta: https://marketplaceapi.microsoft.com/api Wersja interfejsu API: 2018-09-15 bez uwierzytelniania wymagany identyfikator Uri próbki: https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15

Dowolne wywołania interfejsu API, w tym artykule jest możliwe do endpoint makiety hosta. Można oczekiwać, że można pobrać danych testowych jako odpowiedź.


## <a name="next-steps"></a>Kolejne kroki

Deweloperzy mogą także programowo pobrać i manipulowania wydawcy, oferty i obciążeń, profile, przy użyciu [interfejsów API REST usługi Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
