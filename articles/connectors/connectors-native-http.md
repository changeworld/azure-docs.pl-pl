---
title: Komunikować się z dowolnego punktu końcowego za pośrednictwem protokołu HTTP - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji logiki, które mogą się komunikować z dowolnego punktu końcowego za pośrednictwem protokołu HTTP
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 355d1e02ef8195bcee469b77976f1a5834f192d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-the-http-action"></a>Rozpoczynanie pracy z akcji HTTP

Za pomocą akcji HTTP można rozszerzyć przepływy pracy dla Twojej organizacji i komunikują się z dowolnego punktu końcowego za pośrednictwem protokołu HTTP.

Możesz:

* Tworzenie przepływów pracy aplikacji, które aktywować (wyzwalacz) witryny sieci Web, którą zarządzasz systemowi logiki.
* Komunikują się z dowolnego punktu końcowego za pośrednictwem protokołu HTTP, aby rozszerzyć przepływy pracy do innych usług.

Aby rozpocząć, korzystając z akcji HTTP w aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-trigger"></a>Użyj wyzwalacza HTTP
Wyzwalacz to zdarzenie służy do uruchomienia przepływu pracy, który jest zdefiniowany w aplikacji logiki. [Dowiedz się więcej o wyzwalaczy](connectors-overview.md).

Oto przykład sekwencji sposobu konfigurowania wyzwalacza HTTP w Projektancie aplikacji logiki.

1. Dodaj wyzwalacza HTTP w aplikacji logiki.
2. Wprowadź parametry dla punktu końcowego HTTP, który chcesz przeprowadzać sondowanie.
3. Zmodyfikuj interwał cyklu na częstotliwości powinna sondowania.

   Aplikację logiki teraz generowane z zawartość, która jest zwracana podczas każdego wyboru.

   ![Wyzwalacz HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Jak działa wyzwalacza HTTP

Wyzwalacza HTTP wysyła wywołanie punkt końcowy HTTP w stałych odstępach czasu. Domyślnie kodu odpowiedzi HTTP, który jest niższa niż 300 powoduje, że aplikacji logiki do uruchomienia. Aby określić, czy należy uruchomić aplikację logiki, można edytować aplikację logiki w widoku kodu i dodać warunek oceniający po wywołaniu metody HTTP. Oto przykład wyzwalacza HTTP, które są generowane, gdy zwrócony kod stanu jest większa niż lub równa `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Szczegółowe informacje o parametrach wyzwalacza HTTP są dostępne na [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Akcja HTTP

Akcja jest operacja odbywa się przez przepływ pracy, który jest zdefiniowany w aplikacji logiki. 
[Dowiedz się więcej o akcjach](connectors-overview.md).

1. Wybierz **nowy krok** > **Dodaj akcję**.
3. W polu wyszukiwania akcji wpisz **http** Aby wyświetlić listę akcji HTTP.
   
    ![Wybierz akcję HTTP](./media/connectors-native-http/using-action-1.png)

4. Dodaj wszelkie wymagane parametry połączenia HTTP.
   
    ![Zakończenie akcji HTTP](./media/connectors-native-http/using-action-2.png)

5. Kliknij na pasku narzędzi projektanta, **zapisać**. Aplikację logiki zostaną zapisane i opublikowane (aktywowanego) w tym samym czasie.

## <a name="http-trigger"></a>Wyzwalacz HTTP
Poniżej przedstawiono szczegóły wyzwalacz, który obsługuje ten łącznik. Łącznik HTTP ma jeden wyzwalacz.

| Wyzwalacz | Opis |
| --- | --- |
| HTTP |Wykonuje wywołanie HTTP i zwraca zawartości odpowiedzi. |

## <a name="http-action"></a>Akcja HTTP
Poniżej przedstawiono szczegóły akcję, która obsługuje ten łącznik. Łącznik HTTP ma jedną akcję możliwe.

| Akcja | Opis |
| --- | --- |
| HTTP |Wykonuje wywołanie HTTP i zwraca zawartości odpowiedzi. |

## <a name="http-details"></a>Szczegóły HTTP
Poniższe tabele zawierają opis wymaganych i opcjonalnych pól wejściowych dla akcji i odpowiednie szczegóły danych wyjściowych, które są skojarzone z przy użyciu akcji.

#### <a name="http-request"></a>Żądanie HTTP
Poniżej przedstawiono pól wejściowych dla akcji, dzięki czemu wychodzące żądania HTTP.
A * oznacza, że jest polem wymaganym.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Metoda * |method |Zlecenie HTTP do użycia |
| IDENTYFIKATOR URI * |identyfikator URI |Identyfikator URI dla żądania HTTP |
| Nagłówki |nagłówki |Obiekt JSON nagłówków HTTP w celu uwzględnienia |
| Treść |treść |Treść żądania HTTP |
| Authentication |uwierzytelnianie |Szczegółowe informacje w [uwierzytelniania](#authentication) sekcji |

<br>

#### <a name="output-details"></a>Szczegóły danych wyjściowych
Poniżej przedstawiono szczegóły danych wyjściowych dla odpowiedzi HTTP.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Nagłówki |obiekt |Nagłówki odpowiedzi |
| Treść |obiekt |Obiekt odpowiedzi |
| Kod stanu |int |Kod stanu HTTP |

## <a name="authentication"></a>Authentication
Z funkcji Logic Apps umożliwia przy użyciu różnych typów uwierzytelniania względem punktów końcowych HTTP. Korzystając z uwierzytelniania za pomocą **HTTP**,  **[HTTP + Swagger](connectors-native-http-swagger.md)**, i **[HTTP elementu Webhook](connectors-native-webhook.md)** łączników. Konfigurowane są następujące typy uwierzytelniania:

* [Uwierzytelnianie podstawowe](#basic-authentication)
* [Uwierzytelnianie certyfikatu klienta](#client-certificate-authentication)
* [Azure uwierzytelniania OAuth usługi Active Directory (Azure AD)](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Następujący obiekt uwierzytelniania jest wymagany dla uwierzytelniania podstawowego.
A * oznacza, że jest polem wymaganym.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Typ * |type |Typ uwierzytelniania (musi być `Basic` dla uwierzytelniania podstawowego) |
| Username* |nazwa użytkownika |Nazwa użytkownika do uwierzytelniania |
| Hasło * |hasło |Hasło do uwierzytelniania |

> [!TIP]
> Jeśli chcesz użyć hasła, które nie można pobrać z definicji, użyj `securestring` parametru i `@parameters()`  
>  [funkcji definicji przepływu pracy](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow).

Na przykład:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Uwierzytelnianie certyfikatów klientów

Następujący obiekt uwierzytelniania jest potrzebne uwierzytelnianie certyfikatu klienta. A * oznacza, że jest polem wymaganym.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Typ * |type |Typ uwierzytelniania (musi być `ClientCertificate` dla certyfikatów klienta SSL) |
| PFX* |PFX |Zawartość pliku wymiany informacji osobistych (PFX) algorytmem Base64 |
| Hasło * |hasło |Hasło dostępu do pliku PFX |

> [!TIP]
> Aby użyć parametru, który nie będzie można odczytać w definicji po zapisaniu aplikację logiki, można użyć `securestring` parametru i `@parameters()`  
>  [funkcji definicji przepływu pracy](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow).

Na przykład:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Azure uwierzytelniania AD OAuth
Następujący obiekt uwierzytelniania jest wymagane do uwierzytelniania usługi Azure AD OAuth. A * oznacza, że jest polem wymaganym.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Typ * |type |Typ uwierzytelniania (musi być `ActiveDirectoryOAuth` dla usługi Azure AD OAuth) |
| Dzierżawy * |dzierżawa |Identyfikator dzierżawy dla dzierżawy usługi Azure AD |
| Grupy odbiorców * |grupy odbiorców |Zasób żądania autoryzacji do użycia. Na przykład: `https://management.core.windows.net/` |
| Klient identyfikator * |clientId |Identyfikator klienta aplikacji usługi Azure AD |
| Klucz tajny * |wpis tajny |Klucz tajny klienta, który żąda tokenu |

> [!TIP]
> Można użyć `securestring` parametru i `@parameters()` [funkcji definicji przepływu pracy](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow) do używania parametru, który nie będzie można odczytać w definicji po zapisaniu.
> 
> 

Na przykład:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, wypróbuj platformy i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Dostępne łączniki w aplikacjach logiki można eksplorować analizując naszych [listy interfejsów API](apis-list.md).

