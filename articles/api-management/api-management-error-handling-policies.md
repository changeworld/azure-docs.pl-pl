---
title: Obsługa błędów w zasadach usługi Azure API Management | Microsoft Docs
description: Dowiedz się, jak odpowiedzieć na warunki błędów, które mogą wystąpić podczas przetwarzania żądań w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 2c021a6d10c95b58ac444de8ea895ca01371a2b0
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902455"
---
# <a name="error-handling-in-api-management-policies"></a>Obsługa błędów w zasadach API Management

Dostarczając obiekt `ProxyError`, usługa Azure API Management umożliwia wydawcom reagowanie na warunki błędów, które mogą wystąpić podczas przetwarzania żądań. Dostęp do obiektu `ProxyError` jest uzyskiwany za pomocą [kontekstu. Właściwość LastError](api-management-policy-expressions.md#ContextVariables) i może być używana przez zasady w sekcji zasad `on-error`. Ten artykuł zawiera informacje dotyczące możliwości obsługi błędów w usłudze Azure API Management.

## <a name="error-handling-in-api-management"></a>Obsługa błędów w API Management

Zasady w usłudze Azure API Management są podzielone na sekcje `inbound`, `backend`, `outbound`i `on-error`, jak pokazano w poniższym przykładzie.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Podczas przetwarzania żądania wbudowane kroki są wykonywane wraz z wszelkimi zasadami, które znajdują się w zakresie żądania. Jeśli wystąpi błąd, przetwarzanie natychmiast przeskakuje do sekcji zasad `on-error`.
Sekcji zasad `on-error` można używać w dowolnym zakresie. Wydawcy interfejsu API mogą konfigurować zachowanie niestandardowe, takie jak rejestrowanie błędu w centrach zdarzeń lub tworzenie nowej odpowiedzi w celu powrotu do obiektu wywołującego.

> [!NOTE]
> Sekcja `on-error` nie jest obecna w zasadach domyślnie. Aby dodać sekcję `on-error` do zasad, przejdź do odpowiedniej zasady w edytorze zasad i Dodaj ją. Aby uzyskać więcej informacji o konfigurowaniu zasad, zobacz [zasady w API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).
>
> Jeśli nie ma sekcji `on-error`, obiekty wywołujące otrzymają komunikaty odpowiedzi HTTP 400 lub 500 w przypadku wystąpienia błędu.

### <a name="policies-allowed-in-on-error"></a>Zasady dozwolone w przypadku błędu

W sekcji zasady `on-error` można używać następujących zasad.

-   [następnie](api-management-advanced-policies.md#choose)
-   [Set-Variable](api-management-advanced-policies.md#set-variable)
-   [Znajdź i Zamień](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [odpowiedź zwrotna](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)
-   [Set-status](api-management-advanced-policies.md#SetStatus)
-   [send-request](api-management-advanced-policies.md#SendRequest)
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)
-   [Logowanie do centrum eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Gdy wystąpi błąd i formant przechodzi do sekcji zasad `on-error`, ten błąd jest przechowywany w [kontekście. Właściwość LastError](api-management-policy-expressions.md#ContextVariables) , do której można uzyskać dostęp za pomocą zasad w sekcji `on-error`. LastError ma następujące właściwości.

| Nazwa       | Typ   | Opis                                                                                               | Wymagane |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | string | Nazwa elementu, w którym wystąpił błąd. Może to być zasada lub wbudowana Nazwa kroku potoku.      | Tak      |
| `Reason`   | string | Przyjazny dla maszyn kod błędu, który może być używany w obsłudze błędów.                                       | Nie       |
| `Message`  | string | Opis błędu z możliwością odczytu przez człowieka.                                                                         | Tak      |
| `Scope`    | string | Nazwa zakresu, w którym wystąpił błąd i może być jednym z "globalna", "Product", "API" lub "Operation" | Nie       |
| `Section`  | string | Nazwa sekcji, w której wystąpił błąd. Możliwe wartości: "przychodzące", "zaplecze", "wychodzące" lub "On-Error".      | Nie       |
| `Path`     | string | Określa zasady zagnieżdżone, na przykład "Wybierz [3]/when [2]".                                                 | Nie       |
| `PolicyId` | string | Wartość atrybutu `id`, jeśli jest określona przez klienta, na zasadach, w których wystąpił błąd             | Nie       |

> [!TIP]
> Dostęp do kodu stanu można uzyskać za pomocą kontekstu. Response. StatusCode.

> [!NOTE]
> Wszystkie zasady mają opcjonalny atrybut `id`, który można dodać do elementu głównego zasad. Jeśli ten atrybut jest obecny w zasadach w przypadku wystąpienia błędu, wartość atrybutu można pobrać przy użyciu właściwości `context.LastError.PolicyId`.

## <a name="predefined-errors-for-built-in-steps"></a>Wstępnie zdefiniowane błędy dla wbudowanych kroków

Następujące błędy są wstępnie zdefiniowane dla warunków błędów, które mogą wystąpić podczas oceny wbudowanych kroków przetwarzania.

| Źródło        | Warunek                                 | Przyczyna                  | Wiadomość                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfiguracja | Identyfikator URI nie jest zgodny z żadnym interfejsem API ani operacją | OperationNotFound       | Nie można dopasować przychodzącego żądania do operacji.                                                                      |
| authorization | Nie podano klucza subskrypcji             | SubscriptionKeyNotFound | Odmowa dostępu z powodu braku klucza subskrypcji. Pamiętaj o dołączeniu klucza subskrypcji podczas wykonywania żądań do tego interfejsu API. |
| authorization | Wartość klucza subskrypcji jest nieprawidłowa         | SubscriptionKeyInvalid  | Odmowa dostępu z powodu nieprawidłowego klucza subskrypcji. Upewnij się, że podano prawidłowy klucz dla aktywnej subskrypcji.            |
| wielokrotność | Połączenie podrzędne (od klienta do bramy API Management) zostało przerwane przez klienta podczas oczekiwania na żądanie | ClientConnectionFailure | wielokrotność |
| wielokrotność | Połączenie nadrzędne (z bramy API Management do usługi wewnętrznej bazy danych) nie zostało nawiązane lub zostało przerwane przez zaplecze | BackendConnectionFailure | wielokrotność |
| wielokrotność | Wystąpił wyjątek czasu wykonywania podczas obliczania określonego wyrażenia | ExpressionValueEvaluationFailure | wielokrotność |

## <a name="predefined-errors-for-policies"></a>Wstępnie zdefiniowane błędy zasad

Następujące błędy są wstępnie zdefiniowane dla warunków błędów, które mogą wystąpić podczas oceny zasad.

| Źródło       | Warunek                                                       | Przyczyna                    | Wiadomość                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| stawka — limit   | Przekroczono limit szybkości                                             | RateLimitExceeded         | Przekroczono limit szybkości                                                                                                               |
| limit przydziału        | Przekroczono limit przydziału                                                  | QuotaExceeded             | Poza limitem woluminu wywołania. Przydział zostanie uzupełniony w XX: XX: XX. -lub do limitu przydziału przepustowości. Przydział zostanie uzupełniony w XX: XX: XX. |
| JSONP        | Wartość parametru wywołania zwrotnego jest nieprawidłowa (zawiera nieprawidłowe znaki) | CallbackParameterInvalid  | Wartość parametru wywołania zwrotnego {callback-Parameter-Name} nie jest prawidłowym identyfikatorem języka JavaScript.                                          |
| ip-filter    | Nie można przeanalizować adresu IP wywołującego z żądania                          | FailedToParseCallerIP     | Nie można ustanowić adresu IP dla obiektu wywołującego. Odmowa dostępu.                                                                        |
| ip-filter    | Adres IP obiektu wywołującego nie znajduje się na liście dozwolonych                                | CallerIpNotAllowed        | Adres IP obiektu wywołującego {IP-Address} jest niedozwolony. Odmowa dostępu.                                                                        |
| ip-filter    | Adres IP obiektu wywołującego znajduje się na liście zablokowanych                                    | CallerIpBlocked           | Adres IP wywołującego jest zablokowany. Odmowa dostępu.                                                                                         |
| check-header | Wymagany nagłówek nie został przedstawiony lub brak wartości               | HeaderNotFound            | W żądaniu nie znaleziono nagłówka {header-Name}. Odmowa dostępu.                                                                    |
| check-header | Wymagany nagłówek nie został przedstawiony lub brak wartości               | HeaderValueNotAllowed     | Wartość nagłówka {header-name} elementu {header-Value} jest niedozwolona. Odmowa dostępu.                                                          |
| validate-jwt | Brak tokenu JWT w żądaniu                                 | TokenNotFound             | Nie znaleziono tokenu JWT w żądaniu. Odmowa dostępu.                                                                                         |
| validate-jwt | Weryfikacja podpisu nie powiodła się                                     | TokenSignatureInvalid     | < komunikat z biblioteki JWT\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Nieprawidłowi odbiorcy                                                | TokenAudienceNotAllowed   | < komunikat z biblioteki JWT\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Nieprawidłowy wystawca                                                  | TokenIssuerNotAllowed     | < komunikat z biblioteki JWT\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Token wygasł                                                   | TokenExpired              | < komunikat z biblioteki JWT\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Klucz podpisu nie został rozpoznany przez identyfikator                            | TokenSignatureKeyNotFound | < komunikat z biblioteki JWT\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Brak wymaganych oświadczeń w tokenie                          | TokenClaimNotFound        | W tokenie JWT brakuje następujących oświadczeń: < C1\>, < C2\>,... Odmowa dostępu.                                                            |
| validate-jwt | Niezgodność wartości zgłoszeń                                           | TokenClaimValueNotAllowed | Wartość {Claim-name} elementu {Claim-Value} jest niedozwolona. Odmowa dostępu.                                                             |
| validate-jwt | Inne błędy weryfikacji                                       | JwtInvalid                | < komunikat z biblioteki JWT\>                                                                                                          |
| żądanie przesyłania dalej lub wysyłanie żądania | Kod stanu odpowiedzi HTTP i nagłówki nie zostały odebrane z zaplecza w skonfigurowanym limicie czasu | limit czasu | wielokrotność |

## <a name="example"></a>Przykład

Ustawianie zasad interfejsu API na:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

a wysłanie nieautoryzowanego żądania spowoduje powstanie następującej odpowiedzi:

![Nieautoryzowana odpowiedź na błąd](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w API Management](api-management-howto-policies.md)
-   [Przekształć interfejsy API](transform-api.md)
-   [Dokumentacja zasad](api-management-policy-reference.md) pełna lista instrukcji zasad i ich ustawień
-   [Przykłady zasad](policy-samples.md)
