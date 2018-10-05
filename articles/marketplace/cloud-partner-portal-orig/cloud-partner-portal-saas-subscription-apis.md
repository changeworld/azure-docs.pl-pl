---
title: Sprzedaż SaaS za pośrednictwem platformy Azure — interfejsy API | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak utworzyć ofertę SaaS za pośrednictwem interfejsów API portalu marketplace.
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: 0368d9822df193fbf00d8a2069108e23100a58cd
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810595"
---
<a name="saas-sell-through-azure---apis"></a>Sprzedaż SaaS za pośrednictwem platformy Azure — interfejsy API
==============================

W tym artykule opisano sposób tworzenia oferty SaaS za pomocą interfejsów API. Interfejsy API są niezbędne w przypadku sprzedaży za pośrednictwem wybrane platformy Azure, dzięki czemu subskrypcje do swojej oferty SaaS. Jeśli chcesz skompilować regularne listy SaaS, które ma włączone commerce, zobacz [publikowania Guide]./cloud-partner-portal-saas-offers-tech-publishing-guide.md technicznych aplikacji SaaS).

W tym artykule jest podzielona na dwie sekcje:

-   Usługa Usługa uwierzytelniania między usługą SaaS i portalu Azure Marketplace
-   Metody interfejsu API i punktów końcowych

Aby pomóc w zintegrowaniu usługi SaaS dzięki platformie Azure dostępne są następujące interfejsy API:

-   Rozwiąż
-   Subskrybuj
-   Convert
-   Cofnij subskrypcję

Na poniższym diagramie przedstawiono przepływ subskrypcji nowych klientów i stosowania tych interfejsów API:

![SaaS oferują usługi flow interfejsu API](media/saas-offer-publish-with-subscription-apis/saas-offer-publish-api-flow.png)

<a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>Usługa uwierzytelniania usługi między usługą SaaS i portalu Azure marketplace
----------------------------------------------------------------------------

Azure nie nakłada żadnych ograniczeń dotyczących uwierzytelniania, uwidocznionego przez usługi SaaS dla swoich użytkowników końcowych. Jednakże jeśli chodzi o usłudze SaaS podczas komunikowania się z interfejsów API usługi Azure Marketplace, uwierzytelnianie odbywa się w kontekście aplikacji usługi Azure Active Directory (Azure AD).

W poniższej sekcji opisano, jak utworzyć aplikację usługi Azure AD.

### <a name="register-an-azure-ad-application"></a>Rejestrowanie aplikacji usługi Azure AD

Każda aplikacja, która ma korzystać z funkcji usługi Azure AD, musi najpierw zostać zarejestrowana w dzierżawie usługi Azure AD. Ten proces rejestracji obejmuje, zapewniając usługi Azure AD szczegółów dotyczących aplikacji, na przykład adres URL, gdzie znajduje się, adres URL, aby wysyłać odpowiedzi, po uwierzytelnieniu użytkownika identyfikatora URI, który identyfikuje aplikację i tak dalej.

Aby zarejestrować nową aplikację w witrynie Azure portal, wykonaj następujące czynności:

1.  Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2.  Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, kliknij konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
3.  W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**i kliknij przycisk **rejestrowanie nowej aplikacji**.

    ![Rejestracje aplikacji AD SaaS](media/saas-offer-publish-with-subscription-apis/saas-offer-app-registration.png)

4.  Na stronie tworzenia wprowadź aplikację\'informacje rejestracyjne s:
    -   **Nazwa**: Wprowadź nazwę opisową aplikacji
    -   **Typ aplikacji**: Wybierz **aplikacji sieci Web / interfejs API** dla [aplikacje klienckie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) i [aplikacji interfejsu API i zasobów](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) są instalowane na zabezpieczonym serwerze. To ustawienie jest używane do uwierzytelniania OAuth poufne [sieci web klientów](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) i publicznych [klientów z systemem agenta użytkownika](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Ta sama aplikacja może ujawniać zarówno klienta, jak i interfejs API lub zasób.
    -   **Adres URL logowania**: dla aplikacji sieci Web interfejsu API i aplikacji, podaj podstawowy adres URL aplikacji. Na przykład **http://localhost:31544** może być adres URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy następnie użyje tego adresu URL do logowania do aplikacji klienta sieci web.
    -   **Identyfikator URI przekierowania**: dla natywnych aplikacji, podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla twojej aplikacji, na przykład **http://MyFirstAADApp**.

        Konkretne przykłady dla aplikacji sieci web lub aplikacji natywnych, zapoznaj się szybki start z przewodnikiem konfiguracje, które są dostępne w sekcji wprowadzenie [przewodnik dla deweloperów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#get-started).

5.  Po zakończeniu kliknij pozycję **Gotowe**. Usługa Azure AD przypisuje unikatowy identyfikator aplikacji do aplikacji, a\'ponowne przekierowanie do aplikacji\'strony głównej rejestracji s. W zależności od tego, czy aplikacja jest internetowa czy natywna, dostępne są różne opcje umożliwiające dodawanie dodatkowych funkcji do aplikacji.

    **Uwaga:** domyślnie nowo zarejestrowana aplikacja jest skonfigurowane i umożliwiają tylko użytkownicy z tą samą dzierżawą zalogować się do aplikacji.

<a name="api-methods-and-endpoints"></a>Metody interfejsu API i punktów końcowych
-------------------------

Poniżej opisano metody interfejsu API i punktów końcowych dostępnej włączania subskrypcji skorzystania z oferty SaaS.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Pobierz token oparty na aplikacji usługi Azure AD

Metoda HTTP

`GET`

*Adres URL żądania*

**https://login.microsoftonline.com/*{Identyfikator dzierżawy}*  /oauth2/token.**

*Parametr identyfikatora URI*

|  **Nazwa parametru**  | **Wymagane**  | **Opis**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| Identyfikator dzierżawy             | True          | Identyfikator dzierżawy zarejestrowana aplikacja usługi AAD   |
|  |  |  |


*Nagłówek żądania*

|  **Nazwa nagłówka**  | **Wymagane** |  **Opis**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Typ zawartości skojarzonej z żądaniem. Wartość domyślna to `application/x-www-form-urlencoded`.  |
|  |  |  |


*Treść żądania*

| **Nazwa właściwości**   | **Wymagane** |  **Opis**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ udzielenia uprawnień. Wartość domyślna to `client_credentials`.                    |
|  Client_id          | True         |  Identyfikator klienta/aplikacji skojarzone z aplikacją usługi Azure AD.                  |
|  client_secret      | True         |  Hasło skojarzone z aplikacją usługi Azure AD.                               |
|  Zasób           | True         |  Zasób docelowy, dla którego żądany jest token. Wartość domyślna to `b3cca048-ed2e-406c-aff2-40cf19fe7bf5`. |
|  |  |  |


*Odpowiedź*

|  **Nazwa**  | **Typ**       |  **Opis**    |
| ---------- | -------------  | ------------------- |
| 200 OK /    | TokenResponse  | Żądanie zakończone powodzeniem   |
|  |  |  |

*TokenResponse*

Przykładowe tokenu odpowiedzi:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "b3cca048-ed2e-406c-aff2-40cf19fe7bf5",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

### <a name="marketplace-api-endpoint-and-api-version"></a>Punkt końcowy interfejsu API w portalu Marketplace i wersja interfejsu API

Punkt końcowy dla interfejsu API usługi Azure Marketplace jest `https://marketplaceapi.microsoft.com`.

Bieżąca wersja interfejsu API to `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Rozwiązanie z subskrypcji

Akcja POST na rozpoznać punktu końcowego umożliwia użytkownikom można rozpoznać tokenu trwały identyfikator zasobu.

*Żądanie*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nazwa parametru** |     **Opis**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  wersja interfejsu API        |  Wersja operacji przy użyciu dla tego żądania.   |
|  |  |


*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
| x-ms-identyfikator korelacji | Nie           | Unikatowy ciąg wartości dla operacji na komputerze klienckim. To jest skorelowane wszystkie zdarzenia w operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| Typ zawartości       | Yes          | `application/json`                                        |
| Autoryzacja      | Yes          | JSON web token (JWT) token elementu nośnego.                    |
|  |  |  |
  

*Treść odpowiedzi*

 ``` json       
    { 
        “id”: “”, 
        “offerId”:””, 
         “planId”:””, 
    }     
```

| **Nazwa parametru** | **Typ danych** | **Opis**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Ciąg        | Identyfikator subskrypcji SaaS.          |
| OfferId            | Ciąg        | Identyfikator oferty, które subskrybuje użytkownik. |
| Identyfikator planu             | Ciąg        | Identyfikator planu, który użytkownik.  |
|  |  |  |


*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu:**     | **Opis**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token zostały rozpoznane prawidłowo.                                                            |
| 400                  | `BadRequest`         | Wymagane albo brakuje nagłówków lub określono nieprawidłową wersję interfejsu api. Nie można rozpoznać tokenu, ponieważ albo token jest źle sformułowany lub wygasł. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma uprawnień do wykonania tej operacji.                                 |
| 429                  | `RequestThrottleId`  | Usługa jest zajęty przetwarzaniem żądania, spróbuj ponownie później.                                |
| 503                  | `ServiceUnavailable` | Usługa jest w dół tymczasowo niedostępny, spróbuj ponownie później.                                        |
|  |  |  |


*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Yes          | Żądaj Identyfikatora otrzymanych od klienta.                                                                   |
| x-ms-identyfikator korelacji | Yes          | Identyfikator korelacji, jeśli przekazany przez klienta, w przeciwnym razie wartość ta jest identyfikator serwera korelacji.                   |
| x-ms-identyfikator działania    | Yes          | Unikatowy ciąg wartości dla śledzenia żądania z usługi. Służy to do dowolnego uzgadniania. |
| Retry-After        | Nie           | Ta wartość jest ustawiona tylko w przypadku odpowiedzi 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Subskrybuj

Subskrybuj punktu końcowego umożliwia użytkownikom Rozpocznij subskrypcję do usługi SaaS dla danego planu i włączenia opcji naliczania opłat w systemie handlu.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ? api-version = 2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Subskrypcja identyfikator SaaS.                              |
| wersja interfejsu API         | Wersja operacji przy użyciu dla tego żądania. |
|  |  |

*Nagłówki*

|  **Klucz nagłówka**        | **Wymagane** |  **Opis**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nie         | Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| x-ms-identyfikator korelacji     |   Nie         | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ta wartość jest do korelacji wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| If-Match/If-None-Match |   Nie         |   Silne modułu sprawdzania poprawności wartość elementu ETag.                                                          |
| Typ zawartości           |   Yes        |    `application/json`                                                                   |
|  Autoryzacja         |   Yes        |    JSON web token (JWT) token elementu nośnego.                                               |
|  |  |  |

*Treść*

``` json
  { 
      “planId”:””, 
   }      
```

| **Nazwa elementu** | **Typ danych** | **Opis**                      |
|------------------|---------------|--------------------------------------|
| Identyfikator planu           | Ciąg        | Identyfikator planu, który użytkownik. |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu:**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktywacja subskrypcji SaaS dla danego planu.                   |
| 400                  | `BadRequest`         | Wymagane albo brakuje nagłówków lub treść JSON jest nieprawidłowo sformułowany. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma uprawnień do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono z danym Identyfikatorem subskrypcji                                  |
| 409                  | `Conflict`           | Trwa wykonywanie innej operacji w ramach subskrypcji.                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęty przetwarzaniem żądania, spróbuj ponownie później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest w dół tymczasowo niedostępny, spróbuj ponownie później.                          |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Yes          | Żądaj Identyfikatora otrzymanych od klienta.                                                                   |
| x-ms-identyfikator korelacji | Yes          | Identyfikator korelacji, jeśli przekazany przez klienta, w przeciwnym razie wartość ta jest identyfikator serwera korelacji.                   |
| x-ms-identyfikator działania    | Yes          | Unikatowy ciąg wartości dla śledzenia żądania z usługi. Ta wartość jest używana do dowolnego uzgadniania. |
| Retry-After        | Yes          | Stan można sprawdzić na interwał, z którego komputera klienckiego.                                                       |
| Operacja lokalizacji | Yes          | Link do zasobu, można uzyskać stanu operacji.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Punkt końcowy plan zmiany

Zmiana punktu końcowego umożliwia użytkownikowi konwertowanie ich aktualnie subskrybowanych plan do nowego planu.

**POPRAWKI**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ? api-version = 2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Subskrypcja identyfikator SaaS.                              |
| wersja interfejsu API         | Wersja operacji przy użyciu dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**          | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nie           | Unikatowy ciąg wartości dla śledzenia żądania od klienta. Zaleca się identyfikator GUID. Jeśli nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.   |
| x-ms-identyfikator korelacji      | Nie           | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ta wartość jest do korelacji wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| If-Match /If-None-Match | Nie           | Silne modułu sprawdzania poprawności wartość elementu ETag.                              |
| Typ zawartości            | Yes          | `application/json`                                        |
| Autoryzacja           | Yes          | JSON web token (JWT) token elementu nośnego.                    |
|  |  |  |


*Treść*

``` json
                { 
                    “planId”:””, 
                } 
```


|  **Nazwa elementu** |  **Typ danych**  | **Opis**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  Identyfikator planu           |  Ciąg         | Identyfikator planu, który użytkownik.         |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu:**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktywacja subskrypcji SaaS dla danego planu.                   |
| 400                  | `BadRequest`         | Wymagane albo brakuje nagłówków lub treść JSON jest nieprawidłowo sformułowany. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma uprawnień do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono z danym Identyfikatorem subskrypcji                                  |
| 409                  | `Conflict`           | Trwa wykonywanie innej operacji w ramach subskrypcji.                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęty przetwarzaniem żądania, spróbuj ponownie później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest w dół tymczasowo niedostępny, spróbuj ponownie później.                          |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Yes          | Żądaj Identyfikatora otrzymanych od klienta.                                                                   |
| x-ms-identyfikator korelacji | Yes          | Identyfikator korelacji, jeśli przekazany przez klienta, w przeciwnym razie wartość ta jest identyfikator serwera korelacji.                   |
| x-ms-identyfikator działania    | Yes          | Unikatowy ciąg wartości dla śledzenia żądania z usługi. Ta wartość jest używana do dowolnego uzgadniania. |
| Retry-After        | Yes          | Stan można sprawdzić na interwał, z którego komputera klienckiego.                                                       |
| Operacja lokalizacji | Yes          | Link do zasobu, można uzyskać stanu operacji.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Usuwanie subskrypcji

Akcja usuwania w punkcie końcowym Subskrybuj umożliwia użytkownikowi usuwanie subskrypcji o podanym identyfikatorze.

*Żądanie*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ? api-version = 2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Subskrypcja identyfikator SaaS.                              |
| wersja interfejsu API         | Wersja operacji przy użyciu dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowy ciąg wartości dla śledzenia żądania od klienta. Zaleca się identyfikator GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.                                                           |
| x-ms-identyfikator korelacji | Nie           | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ta wartość jest do korelacji wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| Autoryzacja      | Yes          | JSON web token (JWT) token elementu nośnego.                    |
|  |  |  |
 

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu:**     | **Opis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktywacja subskrypcji SaaS dla danego planu.                   |
| 400                  | `BadRequest`         | Wymagane albo brakuje nagłówków lub treść JSON jest nieprawidłowo sformułowany. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma uprawnień do wykonania tej operacji.                   |
| 404                  | `NotFound`           | Nie znaleziono z danym Identyfikatorem subskrypcji                                  |
| 429                  | `RequestThrottleId`  | Usługa jest zajęty przetwarzaniem żądania, spróbuj ponownie później.                  |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo w dół. Spróbuj ponownie później.                          |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Yes          | Żądaj Identyfikatora otrzymanych od klienta.                                                                   |
| x-ms-identyfikator korelacji | Yes          | Identyfikator korelacji, jeśli przekazany przez klienta, w przeciwnym razie, to jest identyfikator serwera korelacji.                   |
| x-ms-identyfikator działania    | Yes          | Unikatowy ciąg wartości dla śledzenia żądania z usługi. Służy to do dowolnego uzgadniania. |
| Retry-After        | Yes          | Stan można sprawdzić na interwał, z którego komputera klienckiego.                                                       |
| Operacja lokalizacji | Yes          | Link do zasobu, można uzyskać stanu operacji.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Pobierz stan operacji

Ten punkt końcowy umożliwia użytkownikowi śledzenie stanu operacji asynchronicznych wyzwolone (Subskrybuj/anulowania subskrypcji/Zmień plan).

*Żądanie*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}* ? api-version = 2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Unikatowy identyfikator dla tej operacji wyzwolone.                |
| wersja interfejsu API         | Wersja operacji przy użyciu dla tego żądania. |
|  |  |


*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowy ciąg wartości dla śledzenia żądania od klienta. Zaleca się identyfikator GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.   |
| x-ms-identyfikator korelacji | Nie           | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ta wartość jest do korelacji wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.  |
| Autoryzacja      | Yes          | JSON web token (JWT) token elementu nośnego.                    |
|  |  |  | 
  

*Treść odpowiedzi*

``` json
  { 
      “id”: “”, 
      “status”:””, 
       “resourceLocation”:””, 
      “created”:””, 
      “lastModified”:”” 
  } 
```

| **Nazwa parametru** | **Typ danych** | **Opis**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Ciąg        | Identyfikator operacji.                                                                      |
| status             | Wyliczenia          | Stan operacji, jedną z następujących: `In Progress`, `Succeeded`, lub `Failed`.          |
| resourceLocation   | Ciąg        | Link do subskrypcji, który został utworzony lub zmodyfikowany. Dzięki temu klient, aby uzyskać zaktualizowany stan operacji post. Ta wartość nie jest ustawiony dla `Unsubscribe` operacji. |
| utworzone            | DateTime      | Czas utworzenia operacji w formacie UTC.                                                           |
| Ostatnia modyfikacja       | DateTime      | Ostatnia aktualizacja o nieudanej operacji w formacie UTC.                                                      |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu:**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Żądanie get zostały rozpoznane prawidłowo i treści zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Wymagane albo brak nagłówków lub określono nieprawidłową wersję interfejsu api. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma uprawnień do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono z danym Identyfikatorem subskrypcji                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęty przetwarzaniem żądania, spróbuj ponownie później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest w dół tymczasowo niedostępny, spróbuj ponownie później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Yes          | Żądaj Identyfikatora otrzymanych od klienta.                                                                   |
| x-ms-identyfikator korelacji | Yes          | Identyfikator korelacji, jeśli przekazany przez klienta, w przeciwnym razie, to jest identyfikator serwera korelacji.                   |
| x-ms-identyfikator działania    | Yes          | Unikatowy ciąg wartości dla śledzenia żądania z usługi. Służy to do dowolnego uzgadniania. |
| Retry-After        | Yes          | Stan można sprawdzić na interwał, z którego komputera klienckiego.                                                       |
| Operacja lokalizacji | Yes          | Link do zasobu, można uzyskać stanu operacji.                                                        |
|  |  |  |

### <a name="get-subscription"></a>Uzyskiwanie subskrypcji

Subskrybowanie akcję Get na punkt końcowy pozwala na użytkownika, aby pobrać subskrypcji o identyfikatorze danego zasobu.

*Żądanie*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ? api-version = 2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Subskrypcja identyfikator SaaS.                              |
| wersja interfejsu API         | Wersja operacji przy użyciu dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowy ciąg wartości do śledzenia żądania z klienta, najlepiej z identyfikatorem GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.                                                           |
| x-ms-identyfikator korelacji | Nie           | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ta wartość jest do korelacji wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| Autoryzacja      | Yes          | JSON web token (JWT) token elementu nośnego.                                                                    |
|  |  |  |

*Treść odpowiedzi*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:”” 
      “created”:”” 
      “lastModified”: “”, 
  }
```
| **Nazwa parametru**     | **Typ danych** | **Opis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Ciąg        | Zasób subskrypcji identyfikator SaaS na platformie Azure.    |
| identyfikatora oferty                | Ciąg        | Identyfikator oferty, które subskrybuje użytkownik.         |
| Identyfikator planu                 | Ciąg        | Identyfikator planu, który użytkownik.          |
| saasSubscriptionName   | Ciąg        | Nazwa subskrypcji SaaS.                |
| saasSubscriptionStatus | Wyliczenia          | Stan operacji.  Jeden z następujących czynności:  <br/> - `Subscribed`: Subskrypcja jest aktywna.  <br/> - `Pending`: Użytkownik tworzenie zasobu, ale nie zostanie aktywowany przez niezależnych dostawców oprogramowania.   <br/> - `Unsubscribed`: Użytkownik anulował.   <br/> - `Suspended`: Użytkownik został zawieszony subskrypcji.   <br/> - `Deactivated`: Subskrypcja platformy azure została zawieszona.  |
| utworzone                | DateTime      | Wartość sygnatury czasowej tworzenia subskrypcji w formacie UTC. |
| Ostatnia modyfikacja           | DateTime      | Subskrypcja zmodyfikowana wartość znacznika czasu w formacie UTC. |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu:**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Żądanie get zostały rozpoznane prawidłowo i treści zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Wymagane albo brak nagłówków lub określono nieprawidłową wersję interfejsu api. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma uprawnień do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono z danym Identyfikatorem subskrypcji                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęty przetwarzaniem żądania, spróbuj ponownie później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest w dół tymczasowo niedostępny, spróbuj ponownie później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Yes          | Żądaj Identyfikatora otrzymanych od klienta.                                                                   |
| x-ms-identyfikator korelacji | Yes          | Identyfikator korelacji, jeśli przekazany przez klienta, w przeciwnym razie, to jest identyfikator serwera korelacji.                   |
| x-ms-identyfikator działania    | Yes          | Unikatowy ciąg wartości dla śledzenia żądania z usługi. Służy to do dowolnego uzgadniania. |
| Retry-After        | Nie           | Stan można sprawdzić na interwał, z którego komputera klienckiego.                                                       |
| Element eTag               | Yes          | Link do zasobu, można uzyskać stanu operacji.                                                        |
|  |  |  |


### <a name="get-subscriptions"></a>Pobiera subskrypcje

Akcja Get w punkcie końcowym subskrypcje umożliwia użytkownika, aby pobrać wszystkie subskrypcje dla wszystkich ofert z niezależnym dostawcą oprogramowania.

*Żądanie*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/subscriptions?api-version=2017-04-15**

| **Nazwa parametru**  | **Opis**                                       |
|---------------------|-------------------------------------------------------|
| wersja interfejsu API         | Wersja operacji przy użyciu dla tego żądania. |
|  |  |

*Nagłówki*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nie           | Unikatowy ciąg wartości dla śledzenia żądania od klienta. Zaleca się identyfikator GUID. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi.             |
| x-ms-identyfikator korelacji | Nie           | Unikatowy ciąg wartości dla operacji na komputerze klienckim. Ta wartość jest do korelacji wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podany, jeden zostanie wygenerowany i podana w nagłówki odpowiedzi. |
| Autoryzacja      | Yes          | JSON web token (JWT) token elementu nośnego.                    |
|  |  |  |


*Treść odpowiedzi*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:”” 
      “created”:”” 
      “lastModified”: “”, 
  }
```

| **Nazwa parametru**     | **Typ danych** | **Opis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Ciąg        | Zasób subskrypcji identyfikator SaaS na platformie Azure.    |
| identyfikatora oferty                | Ciąg        | Identyfikator oferty, które subskrybuje użytkownik.         |
| Identyfikator planu                 | Ciąg        | Identyfikator planu, który użytkownik.          |
| saasSubscriptionName   | Ciąg        | Nazwa subskrypcji SaaS.                |
| saasSubscriptionStatus | Wyliczenia          | Stan operacji.  Jeden z następujących czynności:  <br/> - `Subscribed`: Subskrypcja jest aktywna.  <br/> - `Pending`: Użytkownik tworzenie zasobu, ale nie zostanie aktywowany przez niezależnych dostawców oprogramowania.   <br/> - `Unsubscribed`: Użytkownik anulował.   <br/> - `Suspended`: Użytkownik został zawieszony subskrypcji.   <br/> - `Deactivated`: Subskrypcja platformy azure została zawieszona.  |
| utworzone                | DateTime      | Wartość sygnatury czasowej tworzenia subskrypcji w formacie UTC. |
| Ostatnia modyfikacja           | DateTime      | Subskrypcja zmodyfikowana wartość znacznika czasu w formacie UTC. |
|  |  |  |

*Kody odpowiedzi*

| **Kod stanu HTTP** | **Kod błędu:**     | **Opis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Żądanie get zostały rozpoznane prawidłowo i treści zawiera odpowiedź.    |
| 400                  | `BadRequest`         | Wymagane albo brak nagłówków lub określono nieprawidłową wersję interfejsu api. |
| 403                  | `Forbidden`          | Obiekt wywołujący nie ma uprawnień do wykonania tej operacji.                      |
| 404                  | `NotFound`           | Nie znaleziono z danym Identyfikatorem subskrypcji                                     |
| 429                  | `RequestThrottleId`  | Usługa jest zajęty przetwarzaniem żądania, spróbuj ponownie później.                     |
| 503                  | `ServiceUnavailable` | Usługa jest tymczasowo w dół. Spróbuj ponownie później.                             |
|  |  |  |

*Nagłówki odpowiedzi*

| **Klucz nagłówka**     | **Wymagane** | **Opis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Yes          | Żądaj Identyfikatora otrzymanych od klienta.                                                                   |
| x-ms-identyfikator korelacji | Yes          | Identyfikator korelacji, jeśli przekazany przez klienta, w przeciwnym razie, to jest identyfikator serwera korelacji.                   |
| x-ms-identyfikator działania    | Yes          | Unikatowy ciąg wartości dla śledzenia żądania z usługi. Służy to do dowolnego uzgadniania. |
| Retry-After        | Nie           | Stan można sprawdzić na interwał, z którego komputera klienckiego.                                                       |
|  |  |  |
