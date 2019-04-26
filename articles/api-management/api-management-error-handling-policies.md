---
title: Błąd podczas obsługi w ramach zasad usługi Azure API Management | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 73609e802eceea6aa94d77cef6ca1d654264973d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265011"
---
# <a name="error-handling-in-api-management-policies"></a>Obsługa błędów w zasad interfejsu API zarządzania

Zapewniając `ProxyError` obiektu, usługi Azure API Management umożliwia wydawców odpowiedzieć na błędy, które mogą wystąpić podczas przetwarzania żądania. `ProxyError` Obiekt jest dostępny za pośrednictwem [kontekstu. LastError](api-management-policy-expressions.md#ContextVariables) właściwości i mogą być używane przez zasady w `on-error` sekcji zasad. Ten artykuł zawiera odwołanie błąd możliwości obsługi w programie Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Obsługa błędów w zarządzanie interfejsami API

 Zasady w usłudze Azure API Management są podzielone na `inbound`, `backend`, `outbound`, i `on-error` sekcjach przedstawiono, jak pokazano w poniższym przykładzie.  
  
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
  
Podczas przetwarzania żądania wbudowanych kroków wdrożeniowych oraz wszystkie zasady, które znajdują się w zakresie dla żądania. Jeśli wystąpi błąd, przetwarzania natychmiast przechodzi do `on-error` sekcji zasad.  
`on-error` Sekcji zasad może być używany w żadnych zakresu. Wydawcy interfejsu API można skonfigurować niestandardowe zachowanie, takie jak rejestrowanie błąd do usługi event hubs lub Utwórz nową odpowiedź, aby powrócić do obiektu wywołującego.  
  
> [!NOTE]
>  `on-error` Sekcji nie jest obecny w zasady domyślne. Aby dodać `on-error` sekcji do zasady, przejdź do żądanego zasad w edytorze zasad i dodaj go. Aby uzyskać więcej informacji o konfigurowaniu zasad, zobacz [zasad w usłudze API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  W przypadku nie `on-error` sekcji wywołań będą otrzymywać wiadomości odpowiedzi HTTP 400 lub 500 Jeśli wystąpi błąd.  
  
### <a name="policies-allowed-in-on-error"></a>Zasady dozwolone na błąd

 Następujące zasady mogą być używane w `on-error` sekcji zasad.  
  
-   [Wybierz pozycję](api-management-advanced-policies.md#choose)  
-   [Ustaw zmienną](api-management-advanced-policies.md#set-variable)  
-   [Znajdź i Zamień](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [odpowiedź Return](api-management-advanced-policies.md#ReturnResponse)  
-   [set — nagłówek](api-management-transformation-policies.md#SetHTTPheader)  
-   [set, metoda](api-management-advanced-policies.md#SetRequestMethod)  
-   [Ustaw stan](api-management-advanced-policies.md#SetStatus)  
-   [Żądanie wysłania](api-management-advanced-policies.md#SendRequest)  
-   [sposób żądania, Wyślij co w-](api-management-advanced-policies.md#SendOneWayRequest)  
-   [dziennika do Centrum eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [JSON do pliku xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [XML do formatu json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 Jeśli wystąpi błąd i formant przechodzi do `on-error` błąd sekcji zasady, są przechowywane w [kontekstu. LastError](api-management-policy-expressions.md#ContextVariables) właściwość, która jest możliwy przy użyciu zasad w `on-error` sekcji. LastError ma następujące właściwości.  
  
| Name (Nazwa)     | Typ   | Opis                                                                                               | Wymagane |
|----------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| `Source`   | ciąg | Określa nazwę elementu, na którym wystąpił błąd. Może być zasad lub nazwa kroku wbudowanej potoku.     | Yes      |
| `Reason`   | ciąg | Kod błędu przyjaznych dla komputera, który może służyć do obsługi błędów.                                       | Nie       |
| `Message`  | ciąg | Opis błędu zrozumiałą dla użytkownika.                                                                         | Yes      |
| `Scope`    | ciąg | Nazwa zakresu, w którym wystąpił błąd i może być "globalne", "product", "interfejsu api" lub "operacji" | Nie       |
| `Section`  | ciąg | Nazwa sekcji, w którym wystąpił błąd. Możliwe wartości: "ruchu przychodzącego", "zaplecze", "wychodzący" lub "on error".       | Nie       |
| `Path`     | ciąg | Określa zagnieżdżonych zasad, na przykład "[3] wybierz / po [2]".                                                        | Nie       |
| `PolicyId` | ciąg | Wartość `id` atrybut, jeśli określony przez klienta i w zasadach, w którym wystąpił błąd             | Nie       |

> [!TIP]
> Kod stanu są dostępne za pośrednictwem kontekstu. Response.StatusCode.  

> [!NOTE]
> Wszystkie zasady mają opcjonalny `id` atrybut, który można dodać do elementu głównego zasad. Jeśli ten atrybut jest obecny w zasadach, gdy wystąpi błąd, można pobrać wartości atrybutu za pomocą `context.LastError.PolicyId` właściwości.

## <a name="predefined-errors-for-built-in-steps"></a>Wstępnie zdefiniowane błędów dla wbudowanych kroków  
 Następujące błędy są wstępnie zdefiniowane warunki błędów, które mogą wystąpić podczas obliczania kroki przetwarzania wbudowanych.  
  
| Element źródłowy        | Warunek                                 | Przyczyna                  | Komunikat                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| konfiguracja | Identyfikator URI nie pasuje do operacji lub interfejsu API | OperationNotFound       | Nie można dopasować przychodzące żądanie operacji.                                                                      |
| Autoryzacji | Nie podano klucza subskrypcji             | SubscriptionKeyNotFound | Odmowa z powodu braku subskrypcji klucz dostępu. Upewnij się uwzględnić subskrypcji klucza w przypadku wysyłania żądań do tego interfejsu API. |
| Autoryzacji | Wartość klucza subskrypcji jest nieprawidłowy         | SubscriptionKeyInvalid  | Odmowa z powodu subskrypcji nieprawidłowy klucz dostępu. Upewnij się zapewnić prawidłowy klucz dla aktywnych subskrypcji.            |
  
## <a name="predefined-errors-for-policies"></a>Błędy wstępnie zdefiniowane zasady  
 Następujące błędy są wstępnie zdefiniowane warunki błędów, które mogą wystąpić podczas oceny zasad.  
  
| Element źródłowy       | Warunek                                                       | Przyczyna                    | Komunikat                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| limit szybkości   | Przekroczono limit szybkości                                             | RateLimitExceeded         | Przekroczono limit szybkości                                                                                                               |
| quota        | Przekroczono przydział                                                  | QuotaExceeded             | Poza limitem woluminu wywołania. Przydział zostanie uzupełniona w xx:xx:xx. - lub - limit przydziału przepustowości. Przydział zostanie uzupełniona w xx:xx:xx. |
| Format jsonp        | Nieprawidłowa wartość parametru wywołania zwrotnego (zawiera nieprawidłowe znaki) | CallbackParameterInvalid  | Wartość parametru wywołania zwrotnego {-nazwy parametru wywołania zwrotnego-} nie jest prawidłowym identyfikatorem języka JavaScript.                                          |
| ip-filter    | Nie można przeanalizować IP wywołującego z żądania                          | FailedToParseCallerIP     | Nie można ustalić adres IP dla obiekt wywołujący. Odmowa dostępu.                                                                        |
| ip-filter    | Obiekt wywołujący IP nie ma na liście listy dozwolonych                                | CallerIpNotAllowed        | Adres IP wywołującego {adres ip} nie jest dozwolone. Odmowa dostępu.                                                                        |
| ip-filter    | Obiekt wywołujący adres IP należy do listy zablokowanych                                    | CallerIpBlocked           | Adres IP wywołującego jest zablokowany. Odmowa dostępu.                                                                                         |
| check-header | Brak wymaganego nagłówka nie przedstawione lub wartość               | HeaderNotFound            | Nie znaleziono nagłówka {Nazwa nagłówka} w żądaniu. Odmowa dostępu.                                                                    |
| check-header | Brak wymaganego nagłówka nie przedstawione lub wartość               | HeaderValueNotAllowed     | Wartość nagłówka {Nazwa nagłówka} z {wartość nagłówka} nie jest dozwolona. Odmowa dostępu.                                                          |
| validate-jwt | W żądaniu brakuje tokenu Jwt                                 | TokenNotFound             | Nie można odnaleźć w żądaniu JWT. Odmowa dostępu.                                                                                         |
| validate-jwt | Nie można sprawdzić poprawności podpisu                                     | TokenSignatureInvalid     | < wiadomości z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Nieprawidłowi odbiorcy                                                | TokenAudienceNotAllowed   | < wiadomości z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Nieprawidłowy wystawca                                                  | TokenIssuerNotAllowed     | < wiadomości z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Token wygasł                                                   | TokenExpired              | < wiadomości z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Klucz podpisu nie został rozwiązany przez identyfikator                            | TokenSignatureKeyNotFound | < wiadomości z biblioteki jwt\>. Odmowa dostępu.                                                                                          |
| validate-jwt | Token brakuje wymaganego oświadczeń                          | TokenClaimNotFound        | JWT token brakuje następujących oświadczeń: < c1\>, < c2\>,... Odmowa dostępu.                                                            |
| validate-jwt | Niezgodność wartości oświadczeń                                           | TokenClaimValueNotAllowed | Wartość oświadczenia {Nazwa oświadczenia} {wartość oświadczenia} nie jest dozwolona. Odmowa dostępu.                                                             |
| validate-jwt | Inne błędy sprawdzania poprawności                                       | JwtInvalid                | < wiadomości z biblioteki jwt\>                                                                                                          |

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

![Błąd podczas nieautoryzowanego odpowiedzi](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
