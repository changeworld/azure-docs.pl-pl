---
title: Interfejsy API realizacji SaaS v1 | Azure Marketplace
description: W tym artykule wyjaśniono, jak utworzyć ofertę SaaS i zarządzać nią w portalu Azure Marketplace przy użyciu skojarzonych interfejsów API realizacji w wersji 1.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288346"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>Interfejsy API realizacji SaaS w wersji 1 (przestarzałe)

W tym artykule wyjaśniono, jak utworzyć ofertę SaaS za pomocą interfejsów API. Interfejsy API, składające się z metod REST i punktów końcowych, są niezbędne do umożliwienia subskrypcji oferty SaaS, jeśli masz wyprzedane za pośrednictwem platformy Azure wybrany.  

> [!WARNING]
> Ta początkowa wersja interfejsu API realizacji SaaS jest przestarzała; zamiast tego należy użyć [interfejsu API realizacji SaaS V2](./pc-saas-fulfillment-api-v2.md).  Ta początkowa wersja interfejsu API jest obecnie obsługiwana tylko do obsługi istniejących wydawców. 

Następujące interfejsy API są dostarczane, aby ułatwić integrację usługi SaaS z platformą Azure:

-   Rozwiąż
-   Subskrybuj
-   Convert
-   Anuluj subskrypcję


## <a name="api-methods-and-endpoints"></a>Metody API i punkty końcowe

W poniższych sekcjach opisano metody interfejsu API i punkty końcowe dostępne do włączania subskrypcji dla oferty SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Wersja punktu końcowego interfejsu API i interfejsu API w portalu Marketplace

Punktem końcowym interfejsu API `https://marketplaceapi.microsoft.com`portalu Azure Marketplace jest .

Bieżąca wersja `api-version=2017-04-15`interfejsu API to .


### <a name="resolve-subscription"></a>Rozwiązywanie problemów z subskrypcją

Akcja POST w celu rozpoznania punktu końcowego umożliwia użytkownikom rozpoznanie tokenu portalu marketplace na trwały identyfikator zasobu.  Identyfikator zasobu jest unikatowym identyfikatorem subskrypcji usługi SAAS. 

Gdy użytkownik jest przekierowywane do witryny sieci Web niezależnego użytkownika, adres URL zawiera token w parametrach kwerendy. Oczekuje się, że isv użyje tego tokenu i złożyć żądanie, aby go rozwiązać. Odpowiedź zawiera unikatowy identyfikator subskrypcji SAAS, nazwę, identyfikator oferty i plan zasobu. Ten token jest ważny tylko przez godzinę.

*Żądanie*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nazwa parametru** |     **Opis**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  Wersja operacji do użycia dla tego żądania.   |
|  |  |


*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
| x-ms-korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. To pole koreluje wszystkie zdarzenia z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| Typ zawartości       | Tak          | `application/json`                                        |
| autoryzacja      | Tak          | Token nośny tokenu sieci Web JSON (JWT).                    |
| x-ms-marketplace-token| Tak| Parametr zapytania tokenu w adresie URL, gdy użytkownik jest przekierowywane do witryny sieci Web isv SaaS z platformy Azure. **Uwaga:** Ten token jest ważny tylko przez 1 godzinę. Ponadto adres URL dekodować wartość tokenu z przeglądarki przed użyciem go.|
|  |  |  |
  

*Organ odpowiedzi*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Nazwa parametru** | **Typ danych** | **Opis**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Ciąg        | Identyfikator subskrypcji SaaS.          |
| subscriptionName| Ciąg| Nazwa subskrypcji SaaS ustawiona przez użytkownika na platformie Azure podczas subskrybowania usługi SaaS.|
| OfferId (Ład oferty)            | Ciąg        | Identyfikator oferty, który użytkownik zasubskrybował. |
| planId             | Ciąg        | Identyfikator planu, który użytkownik zasubskrybował.  |
|  |  |  |


*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token został pomyślnie rozwiązany.                                                            |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub określono nieprawidłową wersję interfejsu API. Nie można rozpoznać tokenu, ponieważ token jest nieprawidłowo sformułowany lub wygasł (token jest prawidłowy tylko przez 1 godzinę po wygenerowaniu). |
| 403                  | `Forbidden`          | Osoba wywołująca nie jest autoryzowana do wykonania tej operacji.                                 |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, ponów próbę później.                                |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączna, ponów próbę później.                                        |
|  |  |  |


*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-ms-korelacji | Tak          | Identyfikator korelacji, jeśli przekazywane przez klienta, w przeciwnym razie ta wartość jest identyfikator korelacji serwera.                   |
| x-ms-activityid    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Ten identyfikator jest używany dla wszelkich uzgodnień. |
| Ponów próbę po        | Nie           | Ta wartość jest ustawiona tylko dla odpowiedzi 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Subskrybuj

Punkt końcowy subskrybowania umożliwia użytkownikom rozpoczęcie subskrypcji usługi SaaS dla danego planu i włączenie rozliczeń w systemie handlowym.

**Umieścić**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Unikatowy identyfikator subskrypcji SaaS, który jest uzyskiwany po rozwiązaniu tokenu za pośrednictwem interfejsu API rozpoznawania.                              |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

|  **Klucz nagłówka**        | **Wymagane** |  **Opis**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nie         | Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli nie zostanie to podane, jeden zostanie wygenerowany i podana w nagłówkach odpowiedzi. |
| x-ms-korelacji     |   Nie         | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do korelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli nie zostanie to podane, jeden zostanie wygenerowany i podana w nagłówkach odpowiedzi. |
| If-Match/If-None-Match (Mecz if-Match/If-None-Match) |   Nie         |   Silna wartość ETag walidatora.                                                          |
| typ zawartości           |   Tak        |    `application/json`                                                                   |
|  autoryzacja         |   Tak        |    Token nośny tokenu sieci Web JSON (JWT).                                               |
| tryb x-ms-marketplace-session| Nie | Flaga, aby włączyć tryb pracy na sucho podczas subskrybowania oferty SaaS. Jeśli jest ustawiona, subskrypcja nie zostanie obciążona. Jest to przydatne w przypadku scenariuszy testowania isv. Ustaw go na **"dryrun"**|
|  |  |  |

*Treść*

``` json
{
    "lanId": "",
}
```

| **Nazwa elementu** | **Typ danych** | **Opis**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Wymagane) Ciąg        | Identyfikator planu użytkownika usługi SaaS subskrybuje.  |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktywacja subskrypcji SaaS odebrana dla danego planu.                   |
| 400                  | `BadRequest`         | Brak wymaganych nagłówków lub treść JSON jest zniekształcony. |
| 403                  | `Forbidden`          | Osoba wywołująca nie jest autoryzowana do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji z podanym identyfikatorem                                  |
| 409                  | `Conflict`           | Kolejna operacja jest w toku w ramach subskrypcji.                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, ponów próbę później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączna, ponów próbę później.                          |
|  |  |  |

W przypadku odpowiedzi 202 należy śledzić stan operacji żądania w nagłówku "Lokalizacja operacji". Uwierzytelnianie jest takie samo jak inne interfejsy API portalu Marketplace.

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-ms-korelacji | Tak          | Identyfikator korelacji, jeśli przekazywane przez klienta, w przeciwnym razie ta wartość jest identyfikator korelacji serwera.                   |
| x-ms-activityid    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Ta wartość jest używana dla wszelkich uzgodnień. |
| Ponów próbę po        | Tak          | Interwał, z którym klient może sprawdzić stan.                                                       |
| Operacja-lokalizacja | Tak          | Łącze do zasobu, aby uzyskać stan operacji.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Zmień punkt końcowy planu

Punkt końcowy zmiany umożliwia użytkownikowi konwertowanie aktualnie subskrybowanego planu na nowy plan.

**Patch**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Identyfikator subskrypcji SaaS.                              |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**          | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nie           | Unikatowa wartość ciągu do śledzenia żądania od klienta. Polec identyfikator GUID. Jeśli nie zostanie to podane, jeden zostanie wygenerowany i podana w nagłówkach odpowiedzi.   |
| x-ms-korelacji      | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do korelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli nie zostanie to podane, jeden zostanie wygenerowany i podana w nagłówkach odpowiedzi. |
| If-Match /If-None-Match (Dopasowanie do if-None-Match) | Nie           | Silna wartość ETag walidatora.                              |
| typ zawartości            | Tak          | `application/json`                                        |
| autoryzacja           | Tak          | Token nośny tokenu sieci Web JSON (JWT).                    |
|  |  |  |

*Treść*

```json
{
    "planId": ""
}
```

|  **Nazwa elementu** |  **Typ danych**  | **Opis**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Wymagane) Ciąg         | Identyfikator planu użytkownika usługi SaaS subskrybuje.          |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktywacja subskrypcji SaaS odebrana dla danego planu.                   |
| 400                  | `BadRequest`         | Brak wymaganych nagłówków lub treść JSON jest zniekształcony. |
| 403                  | `Forbidden`          | Osoba wywołująca nie jest autoryzowana do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji z podanym identyfikatorem                                  |
| 409                  | `Conflict`           | Kolejna operacja jest w toku w ramach subskrypcji.                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, ponów próbę później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączna, ponów próbę później.                          |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-ms-korelacji | Tak          | Identyfikator korelacji, jeśli przekazywane przez klienta, w przeciwnym razie ta wartość jest identyfikator korelacji serwera.                   |
| x-ms-activityid    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Ta wartość jest używana dla wszelkich uzgodnień. |
| Ponów próbę po        | Tak          | Interwał, z którym klient może sprawdzić stan.                                                       |
| Operacja-lokalizacja | Tak          | Łącze do zasobu, aby uzyskać stan operacji.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Usuwanie subskrypcji

Akcja Usuń w punkcie końcowym subskrybowania umożliwia użytkownikowi usunięcie subskrypcji o podanym identyfikatorze.

*Żądanie*

**Usunąć**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Identyfikator subskrypcji SaaS.                              |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowa wartość ciągu do śledzenia żądania od klienta. Polec identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.                                                           |
| x-ms-korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do korelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli nie zostanie to podane, jeden zostanie wygenerowany i podana w nagłówkach odpowiedzi. |
| autoryzacja      | Tak          | Token nośny tokenu sieci Web JSON (JWT).                    |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktywacja subskrypcji SaaS odebrana dla danego planu.                   |
| 400                  | `BadRequest`         | Brak wymaganych nagłówków lub treść JSON jest zniekształcony. |
| 403                  | `Forbidden`          | Osoba wywołująca nie jest autoryzowana do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji z podanym identyfikatorem                                  |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, ponów próbę później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączna. Spróbuj ponownie później.                          |
|  |  |  |

W przypadku odpowiedzi 202 należy śledzić stan operacji żądania w nagłówku "Lokalizacja operacji". Uwierzytelnianie jest takie samo jak inne interfejsy API portalu Marketplace.

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-ms-korelacji | Tak          | Identyfikator korelacji, jeśli przekazywane przez klienta, w przeciwnym razie jest to identyfikator korelacji serwera.                   |
| x-ms-activityid    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane do wszelkich uzgodnień. |
| Ponów próbę po        | Tak          | Interwał, z którym klient może sprawdzić stan.                                                       |
| Operacja-lokalizacja | Tak          | Łącze do zasobu, aby uzyskać stan operacji.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Uzyskiwanie stanu operacji

Ten punkt końcowy umożliwia użytkownikowi śledzenie stanu wyzwalanej operacji asynchronicznego (Subskrybuj/Ubskrypcja/Zmień plan).

*Żądanie*

**Pobierz**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Unikatowy identyfikator wyzwalaneją operacji.                |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowa wartość ciągu do śledzenia żądania od klienta. Polec identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.   |
| x-ms-korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do korelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.  |
| autoryzacja      | Tak          | Token nośny tokenu sieci Web JSON (JWT).                    |
|  |  |  | 

*Organ odpowiedzi*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Nazwa parametru** | **Typ danych** | **Opis**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Ciąg        | Identyfikator operacji.                                                                      |
| status             | Wyliczenie          | Stan operacji, jedna z `In Progress` `Succeeded`następujących `Failed`czynności: , , lub .          |
| resourceLocation   | Ciąg        | Łącze do subskrypcji, która została utworzona lub zmodyfikowana. Pomaga to klientowi uzyskać zaktualizowaną operację postu stanu. Ta wartość nie `Unsubscribe` jest ustawiona dla operacji. |
| Utworzone            | DateTime      | Czas tworzenia operacji w czasie UTC.                                                           |
| Lastmodified       | DateTime      | Ostatnia aktualizacja operacji w utc.                                                      |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Rozwiązano żądanie get pomyślnie i treść zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub określono nieprawidłową wersję interfejsu API. |
| 403                  | `Forbidden`          | Osoba wywołująca nie jest autoryzowana do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji z podanym identyfikatorem.                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, ponów próbę później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączna, ponów próbę później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-ms-korelacji | Tak          | Identyfikator korelacji, jeśli przekazywane przez klienta, w przeciwnym razie jest to identyfikator korelacji serwera.                   |
| x-ms-activityid    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane do wszelkich uzgodnień. |
| Ponów próbę po        | Tak          | Interwał, z którym klient może sprawdzić stan.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Uzyskaj subskrypcję

Akcja Pobierz w punkcie końcowym subskrybowania umożliwia użytkownikowi pobieranie subskrypcji z danym identyfikatorem zasobu.

*Żądanie*

**Pobierz**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Identyfikator subskrypcji SaaS.                              |
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowa wartość ciągu do śledzenia żądania od klienta, najlepiej identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.                                                           |
| x-ms-korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do korelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| autoryzacja      | Tak          | Token nośny tokenu sieci Web JSON (JWT).                                                                    |
|  |  |  |

*Organ odpowiedzi*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nazwa parametru**     | **Typ danych** | **Opis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Ciąg        | Identyfikator zasobu subskrypcji SaaS na platformie Azure.    |
| offerId                | Ciąg        | Identyfikator oferty, który użytkownik zasubskrybował.         |
| planId                 | Ciąg        | Identyfikator planu, który użytkownik zasubskrybował.          |
| nazwa saasSubscriptionname   | Ciąg        | Nazwa subskrypcji SaaS.                |
| saasSubscriptionStatus | Wyliczenie          | Stan operacji.  Jeden z poniższych programów:  <br/> - `Subscribed`: Subskrypcja jest aktywna.  <br/> - `Pending`: Użytkownik tworzy zasób, ale nie jest on aktywowany przez isv.   <br/> - `Unsubscribed`: Użytkownik anulował subskrypcję.   <br/> - `Suspended`: Użytkownik zawiesił subskrypcję.   <br/> - `Deactivated`: Subskrypcja platformy Azure jest zawieszona.  |
| Utworzone                | DateTime      | Wartość sygnatury czasjednacjona w czasie utc. |
| Lastmodified           | DateTime      | Wartość sygnatury czasjednostniejsty subskrypcji w czasie UTC. |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Rozwiązano żądanie get pomyślnie i treść zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub określono nieprawidłową wersję interfejsu API. |
| 403                  | `Forbidden`          | Osoba wywołująca nie jest autoryzowana do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji z podanym identyfikatorem                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, ponów próbę później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączna, ponów próbę później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-ms-korelacji | Tak          | Identyfikator korelacji, jeśli przekazywane przez klienta, w przeciwnym razie jest to identyfikator korelacji serwera.                   |
| x-ms-activityid    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane do wszelkich uzgodnień. |
| Ponów próbę po        | Nie           | Interwał, z którym klient może sprawdzić stan.                                                       |
| Etag               | Tak          | Łącze do zasobu, aby uzyskać stan operacji.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Uzyskaj subskrypcje

Get akcji na subskrypcji punktu końcowego umożliwia użytkownikowi pobrać wszystkie subskrypcje dla wszystkich ofert z isv.

*Żądanie*

**Pobierz**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | Wersja operacji do użycia dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowa wartość ciągu do śledzenia żądania od klienta. Polec identyfikator GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi.             |
| x-ms-korelacji | Nie           | Unikatowa wartość ciągu dla operacji na kliencie. Ta wartość służy do korelowania wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| autoryzacja      | Tak          | Token nośny tokenu sieci Web JSON (JWT).                    |
|  |  |  |

*Organ odpowiedzi*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nazwa parametru**     | **Typ danych** | **Opis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Ciąg        | Identyfikator zasobu subskrypcji usługi SaaS na platformie Azure    |
| offerId                | Ciąg        | Identyfikator oferty, który użytkownik zasubskrybował         |
| planId                 | Ciąg        | Identyfikator planu, który użytkownik zasubskrybował          |
| nazwa saasSubscriptionname   | Ciąg        | Nazwa subskrypcji SaaS                |
| saasSubscriptionStatus | Wyliczenie          | Stan operacji.  Jeden z poniższych programów:  <br/> - `Subscribed`: Subskrypcja jest aktywna.  <br/> - `Pending`: Użytkownik tworzy zasób, ale nie jest on aktywowany przez isv.   <br/> - `Unsubscribed`: Użytkownik anulował subskrypcję.   <br/> - `Suspended`: Użytkownik zawiesił subskrypcję.   <br/> - `Deactivated`: Subskrypcja platformy Azure jest zawieszona.  |
| Utworzone                | DateTime      | Wartość sygnatury czasjonasa tworzenia subskrypcji w czasie UTC |
| Lastmodified           | DateTime      | Zmodyfikowana wartość sygnatury czasjednatej subskrypcji w czasie UTC |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Rozwiązano żądanie get pomyślnie i treść zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Brakuje wymaganych nagłówków lub określono nieprawidłową wersję interfejsu API. |
| 403                  | `Forbidden`          | Osoba wywołująca nie jest autoryzowana do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono subskrypcji z podanym identyfikatorem                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęta przetwarzaniem żądań, ponów próbę później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo wyłączna. Spróbuj ponownie później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Tak          | Identyfikator żądania odebrany od klienta.                                                                   |
| x-ms-korelacji | Tak          | Identyfikator korelacji, jeśli przekazywane przez klienta, w przeciwnym razie jest to identyfikator korelacji serwera.                   |
| x-ms-activityid    | Tak          | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane do wszelkich uzgodnień. |
| Ponów próbę po        | Nie           | Interwał, z którym klient może sprawdzić stan.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

Element webhook SaaS służy do proaktywnego powiadamiania o zmianach w usłudze SaaS. Oczekuje się, że ten interfejs API POST będzie nieuwierzyty i zostanie wywołany przez usługę firmy Microsoft. Usługa SaaS ma wywołać interfejs API operacji, aby sprawdzić poprawność i autoryzować przed podjęciem działań na powiadomienie elementu webhook. 

*Treść*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Nazwa parametru**     | **Typ danych** | **Opis**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Ciąg       | Unikatowy identyfikator wyzwalaneją operacji.                |
| Activityid   | Ciąg        | Unikatowa wartość ciągu do śledzenia żądania z usługi. Jest to używane do wszelkich uzgodnień.               |
| subscriptionId                     | Ciąg        | Identyfikator zasobu subskrypcji SaaS na platformie Azure.    |
| offerId                | Ciąg        | Identyfikator oferty, który użytkownik zasubskrybował. Dostępne tylko z akcji "Aktualizuj".        |
| identyfikator wydawcy                | Ciąg        | Identyfikator wydawcy oferty SaaS         |
| planId                 | Ciąg        | Identyfikator planu, który użytkownik zasubskrybował. Dostępne tylko z akcji "Aktualizuj".          |
| action                 | Ciąg        | Akcja wyzwalająca to powiadomienie. Możliwe wartości — aktywowanie, usuwanie, zawieszanie, przywracanie, aktualizowanie          |
| Sygnatury czasowej                 | Ciąg        | TImestamp wartość w utc po wyzwoleniu tego powiadomienia.          |
|  |  |  |


## <a name="next-steps"></a>Następne kroki

Deweloperzy mogą również programowo pobierać i manipulować obciążeniami, ofertami i profilami wydawców za pomocą [interfejsów API REST portalu partnerów w chmurze.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)
