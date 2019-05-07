---
title: Obsługa błędów w zasady usługi Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak reagować na błędy, które mogą wystąpić podczas przetwarzania żądań w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: apimpm
ms.openlocfilehash: 2bde63bb668188936b3dd3cf5ecbf3b8c604eb95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564331"
---
# <a name="error-handling-in-api-management-policies"></a>Obsługa błędów w zasady usługi API Management

Zapewniając `ProxyError` obiektu w usłudze Azure API Management umożliwia wydawcom reagować na błędy, które mogą wystąpić podczas przetwarzania żądań. `ProxyError` Obiekt jest dostępny za pośrednictwem [kontekstu. LastError](api-management-policy-expressions.md#ContextVariables) właściwości i może być używany przez zasady w `on-error` sekcji zasady. Ten artykuł zawiera odniesienie błędu możliwości obsługi w usłudze Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Obsługa błędów w usługi API Management

 Zasady w usłudze Azure API Management są podzielone na `inbound`, `backend`, `outbound`, i `on-error` sekcje, jak pokazano w poniższym przykładzie.  
  
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
  
Podczas przetwarzania żądania wbudowanych kroki są wykonywane oraz wszystkie zasady, które znajdują się w zakresie dla żądania. Jeśli wystąpi błąd, przetwarzania natychmiast przechodzi do `on-error` sekcji zasady.  
`on-error` Sekcji zasady mogą być używane w jakikolwiek zakres. Wydawcy interfejsów API można skonfigurować zachowanie niestandardowe, takie jak rejestrowanie błędu do usługi event hubs lub utworzenie nowej odpowiedzi, aby powrócić do obiektu wywołującego.  
  
> [!NOTE]
>  `on-error` Sekcja nie jest obecna w zasadach domyślnie. Aby dodać `on-error` sekcji z zasadami, przejdź do żądane zasady w edytorze zasad i dodaj go. Aby uzyskać więcej informacji na temat konfigurowania zasad, zobacz [zasad w usłudze API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Jeśli ma nie `on-error` sekcji, obiekty wywołujące otrzyma 400 lub 500 komunikatów odpowiedzi HTTP, jeśli wystąpi błąd.  
  
### <a name="policies-allowed-in-on-error"></a>Zasady mogą na błąd

 Następujące zasady mogą być używane w `on-error` sekcji zasady.  
  
-   [Wybierz opcję](api-management-advanced-policies.md#choose)  
-   [Ustaw zmienną](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [dziennik do Centrum zdarzeń](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 Gdy wystąpi błąd i kontrola przechodzi do `on-error` sekcji zasady błędu są przechowywane w [kontekstu. LastError](api-management-policy-expressions.md#ContextVariables) właściwość, która może zostać oceniony przez zasady w `on-error` sekcji. LastError ma następujące właściwości.  
  
| Name (Nazwa)     | Typ   | Opis                                                                                               | Wymagane |
|----------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| Source   | string | Nazwy elementu, w którym wystąpił błąd. Może być zasad lub nazwa kroku wbudowanej potoku.     | Yes      |
| Reason   | string | Kod błędu przyjaznego dla komputera, który może być używany do obsługi błędów.                                       | Nie       |
| Message  | string | Opis błędu czytelny dla człowieka.                                                                         | Yes      |
| Scope    | string | Nazwa zakresu, w którym wystąpił błąd i mogą mieć jedną z "global", "produkt", "interfejs api" lub "Operacja" | Nie       |
| `Section`  | string | Nazwa sekcji, w którym wystąpił błąd. Możliwe wartości: "dla ruchu przychodzącego", "wewnętrzna", "wychodzące" lub "on error".       | Nie       |
| `Path`     | string | Określa zasady zagnieżdżonych, na przykład "Wybierz [3] / podczas [2]".                                                        | Nie       |
| `PolicyId` | string | Wartość atrybutu `id` atrybutu, jeśli określony przez klienta w zasadach, w którym wystąpił błąd             | Nie       |

> [!TIP]
> Kod stanu dostęp za pomocą kontekstu. Response.StatusCode.  

> [!NOTE]
> Wszystkie zasady mają opcjonalny `id` atrybut, który można dodać do głównego elementu zasad. Jeśli ten atrybut jest obecny w zasadach, gdy wystąpi błąd, wartość atrybutu można pobrać przy użyciu `context.LastError.PolicyId` właściwości.

## <a name="predefined-errors-for-built-in-steps"></a>Wstępnie zdefiniowane błędy wbudowanych kroków  
 Następujące błędy są wstępnie zdefiniowane warunki błędów, które mogą wystąpić podczas obliczania kroków przetwarzania wbudowanych.  
  
| Source        | Warunek                                 | Reason                  | Message                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| konfiguracja | Identyfikator URI nie pasują do dowolnego interfejsu API lub operacji | OperationNotFound       | Nie można dopasować przychodzące żądanie operacji.                                                                      |
| Autoryzacja | Klucz subskrypcji nie podano             | SubscriptionKeyNotFound | Odmowa z powodu braku subskrypcji klucz dostępu. Upewnij się uwzględnić klucz subskrypcji, w przypadku wysyłania żądań do tego interfejsu API. |
| Autoryzacja | Wartość klucza subskrypcji jest nieprawidłowy         | SubscriptionKeyInvalid  | Odmowa z powodu nieprawidłowej subskrypcji klucz dostępu. Upewnij się zapewnić prawidłowy klucz dla aktywną subskrypcję.            |
  
## <a name="predefined-errors-for-policies"></a>Błędy wstępnie zdefiniowane zasady  
 Następujące błędy są wstępnie zdefiniowane warunki błędów, które mogą wystąpić podczas oceny zasad.  
  
| Source       | Warunek                                                       | Reason                    | Message                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| limit szybkości   | Przekroczono limit szybkości                                             | RateLimitExceeded         | Przekroczono limit szybkości                                                                                                               |
| limit przydziału        | Przekroczono limit przydziału                                                  | QuotaExceeded             | Poza limitem woluminu wywołania. Limit przydziału zostanie uzupełniona w xx:xx:xx. - lub - limit przydziału przepustowości. Limit przydziału zostanie uzupełniona w xx:xx:xx. |
| jsonp        | Nieprawidłowa wartość parametru wywołania zwrotnego (zawiera nieprawidłowe znaki) | CallbackParameterInvalid  | Wartość parametru wywołania zwrotnego {wywołania zwrotnego — parametr name} nie jest prawidłowym identyfikatorem języka JavaScript.                                          |
| ip-filter    | Nie można przeanalizować IP obiektu wywołującego z żądania                          | FailedToParseCallerIP     | Nie można ustanowić adresu IP do obiektu wywołującego. Odmowa dostępu.                                                                        |
| ip-filter    | Obiekt wywołujący IP nie znajduje się w listy dozwolonych                                | CallerIpNotAllowed        | Adres IP obiektu wywołującego {adres ip} jest niedozwolone. Odmowa dostępu.                                                                        |
| ip-filter    | Obiekt wywołujący adres IP należy do listy zablokowanych                                    | CallerIpBlocked           | Adres IP obiektu wywołującego jest blokowany. Odmowa dostępu.                                                                                         |
| check-header | Brak wymaganego nagłówka nie prezentowane lub wartość               | HeaderNotFound            | Nie znaleziono nagłówka {Nazwa nagłówka} w żądaniu. Odmowa dostępu.                                                                    |
| check-header | Brak wymaganego nagłówka nie prezentowane lub wartość               | HeaderValueNotAllowed     | Wartość nagłówka {Nazwa nagłówka} {wartości nagłówka} nie jest dozwolone. Odmowa dostępu.                                                          |
| validate-jwt | W żądaniu brakuje tokenu Jwt                                 | TokenNotFound             | Nie można odnaleźć w żądania tokenu JWT. Odmowa dostępu.                                                                                         |
| validate-jwt | Weryfikacja podpisu nie powiodła się                                     | TokenSignatureInvalid     | < komunikat z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Nieprawidłowy odbiorców                                                | TokenAudienceNotAllowed   | < komunikat z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Nieprawidłowy wystawcy                                                  | TokenIssuerNotAllowed     | < komunikat z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Token wygasł                                                   | TokenExpired              | < komunikat z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Nie rozpoznano klucza podpisu według Identyfikatora                            | TokenSignatureKeyNotFound | < komunikat z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Wymagane oświadczenia brakuje tokenu                          | TokenClaimNotFound        | JWT token nie zawiera następujących oświadczenia: < c1\>, < c2\>,... Odmowa dostępu.                                                            |
| validate-jwt | Niezgodność wartości oświadczeń                                           | TokenClaimValueNotAllowed | Wartość oświadczenia {oświadczenia name} {wartość oświadczenia} nie jest dozwolone. Odmowa dostępu.                                                             |
| validate-jwt | Inne błędy sprawdzania poprawności                                       | JwtInvalid                | < komunikat z biblioteki jwt\>                                                                                                          |

## <a name="example"></a>Przykład

Ustawienie zasad interfejsu API:

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

i wysyłanie nieautoryzowanego żądania spowoduje następującą odpowiedź:

![Odpowiedzi na błąd dotyczący nieautoryzowanego dostępu](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

+ [Zasady usługi API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   