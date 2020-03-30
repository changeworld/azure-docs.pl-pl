---
title: Obsługa błędów w zasadach usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak reagować na warunki błędów, które mogą wystąpić podczas przetwarzania żądań w usłudze Azure API Management.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902455"
---
# <a name="error-handling-in-api-management-policies"></a>Obsługa błędów w zasadach usługi API Management

Udostępniając `ProxyError` obiekt usługi Azure API Management umożliwia wydawcom reagowanie na warunki błędów, które mogą wystąpić podczas przetwarzania żądań. Obiekt `ProxyError` jest dostępny za pośrednictwem [kontekstu. LastError](api-management-policy-expressions.md#ContextVariables) właściwości i mogą być `on-error` używane przez zasady w sekcji zasad. Ten artykuł zawiera odwołanie do możliwości obsługi błędów w usłudze Azure API Management.

## <a name="error-handling-in-api-management"></a>Obsługa błędów w zarządzaniu interfejsami API

Zasady w usłudze Azure `inbound`API `backend` `outbound`Management `on-error` są podzielone na , i sekcje, jak pokazano w poniższym przykładzie.

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

Podczas przetwarzania żądania wbudowane kroki są wykonywane wraz z wszelkimi zasadami, które są w zakresie żądania. Jeśli wystąpi błąd, przetwarzanie natychmiast `on-error` przeskakuje do sekcji zasad.
Sekcja `on-error` zasad może być używana w dowolnym zakresie. Wydawcy interfejsu API można skonfigurować niestandardowe zachowanie, takie jak rejestrowanie błędu do centrów zdarzeń lub tworzenie nowej odpowiedzi, aby powrócić do wywołującego.

> [!NOTE]
> Sekcja `on-error` nie jest domyślnie obecna w zasadach. Aby dodać `on-error` sekcję do zasad, przejdź do żądanej zasady w edytorze zasad i dodaj ją. Aby uzyskać więcej informacji na temat konfigurowania zasad, zobacz [Zasady w zarządzania interfejsami API](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).
>
> Jeśli nie `on-error` ma sekcji, wywołujący otrzymają 400 lub 500 komunikatów odpowiedzi HTTP, jeśli wystąpi warunek błędu.

### <a name="policies-allowed-in-on-error"></a>Zasady dozwolone w błąd

W sekcji `on-error` zasad można użyć następujących zasad.

-   [Wybierz](api-management-advanced-policies.md#choose)
-   [ustawiona zmienna](api-management-advanced-policies.md#set-variable)
-   [znajdowanie i zastępowanie](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [odpowiedź zwrotna](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [metoda set-](api-management-advanced-policies.md#SetRequestMethod)
-   [ustaw-status](api-management-advanced-policies.md#SetStatus)
-   [wyślij żądanie](api-management-advanced-policies.md#SendRequest)
-   [wyślij jednokierunkowe żądanie](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-do-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Gdy wystąpi błąd i kontrola `on-error` przeskakuje do sekcji zasad, błąd jest przechowywany w [kontekście. LastError](api-management-policy-expressions.md#ContextVariables) właściwość, do której można `on-error` uzyskać dostęp przez zasady w sekcji. LastError ma następujące właściwości.

| Nazwa       | Typ   | Opis                                                                                               | Wymagany |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | ciąg | Nazwy elementu, w którym wystąpił błąd. Może to być nazwa kroku wbudowanego lub wbudowanego potoku.      | Tak      |
| `Reason`   | ciąg | Kod błędu przyjazny dla komputera, który może być używany w obsłudze błędów.                                       | Nie       |
| `Message`  | ciąg | Opis błędu czytelny dla człowieka.                                                                         | Tak      |
| `Scope`    | ciąg | Nazwa zakresu, w którym wystąpił błąd i może być jednym z "globalnych", "produkt", "api" lub "operacja" | Nie       |
| `Section`  | ciąg | Nazwa sekcji, w której wystąpił błąd. Możliwe wartości: "przychodzące", "zaplecze", "wychodzące" lub "on-error".      | Nie       |
| `Path`     | ciąg | Określa zasady zagnieżdżone, na przykład "choose[3]/when[2]".                                                 | Nie       |
| `PolicyId` | ciąg | Wartość atrybutu, `id` jeśli jest określona przez klienta, w zasadach, w których wystąpił błąd             | Nie       |

> [!TIP]
> Dostęp do kodu stanu można uzyskać za pośrednictwem kontekstu. Response.StatusCode.

> [!NOTE]
> Wszystkie zasady mają `id` opcjonalny atrybut, który można dodać do głównego elementu zasad. Jeśli ten atrybut jest obecny w zasadach, gdy wystąpi warunek błędu, wartość `context.LastError.PolicyId` atrybutu można pobrać za pomocą właściwości.

## <a name="predefined-errors-for-built-in-steps"></a>Wstępnie zdefiniowane błędy dla wbudowanych kroków

Następujące błędy są wstępnie zdefiniowane dla warunków błędu, które mogą wystąpić podczas oceny wbudowanych kroków przetwarzania.

| Element źródłowy        | Warunek                                 | Przyczyna                  | Komunikat                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfiguracja | Uri nie pasuje do żadnego interfejsu API lub operacji | OperacjaNotfound       | Nie można dopasować żądania przychodzącego do operacji.                                                                      |
| autoryzacja | Nie podano klucza subskrypcji             | SubscriptionKeyNotFound | Odmowa dostępu z powodu braku klucza subskrypcji. Upewnij się, że podczas tworzenia żądań do tego interfejsu API jest dołączany klucz subskrypcji. |
| autoryzacja | Wartość klucza subskrypcji jest nieprawidłowa         | Wartość klucza subskrypcji  | Odmowa dostępu z powodu nieprawidłowego klucza subskrypcji. Upewnij się, że podaj prawidłowy klucz dla aktywnej subskrypcji.            |
| wiele | Połączenie podrzędne (od klienta do bramy zarządzania interfejsem API) zostało przerwane przez klienta, gdy żądanie było oczekujące | ClientConnectionPostęp | wiele |
| wiele | Połączenie nadrzędne (z bramy zarządzania interfejsami API do usługi wewnętrznej bazy danych) nie zostało ustanowione lub zostało przerwane przez zaplecze | ZapleczeConnectionFailure | wiele |
| wiele | Wystąpił wyjątek środowiska uruchomieniowego podczas oceny określonego wyrażenia | ExpressionValueEvaluationFailure | wiele |

## <a name="predefined-errors-for-policies"></a>Wstępnie zdefiniowane błędy zasad

Następujące błędy są wstępnie zdefiniowane dla warunków błędu, które mogą wystąpić podczas oceny zasad.

| Element źródłowy       | Warunek                                                       | Przyczyna                    | Komunikat                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| limit kursowy   | Przekroczono limit szybkości                                             | RateLimitExceeded (RateLimitExceeded)         | Limit szybkości został przekroczony                                                                                                               |
| limit przydziału        | Przekroczono kwotę                                                  | PrzydziałRozszył się             | Poza limitem woluminu wywołania. Przydział zostanie uzupełniony w xx:xx:xx. -lub- Limit przepustowości. Przydział zostanie uzupełniony w xx:xx:xx. |
| Jsonp        | Wartość parametru wywołania zwrotnego jest nieprawidłowa (zawiera nieprawidłowe znaki) | CallbackParameterInvalid  | Wartość parametru wywołania zwrotnego {callback-parameter-name} nie jest prawidłowym identyfikatorem JavaScript.                                          |
| filtr ip    | Nie można przeanalizować adresu IP wywołującego z żądania                          | Nie można pozycją ToParseCallerIP     | Nie można ustalić adresu IP dla osoby dzwoniącej. Odmowa dostępu.                                                                        |
| filtr ip    | Adres IP rozmówcy nie znajduje się na liście dozwolonych                                | CallerIpNotallowed (Nieumiejęć)        | Adres IP dzwoniącego {ip-address} jest niedozwolony. Odmowa dostępu.                                                                        |
| filtr ip    | Adres IP rozmówcy znajduje się na liście zablokowanych                                    | Blokada callerip           | Adres IP dzwoniącego jest zablokowany. Odmowa dostępu.                                                                                         |
| nagłówek czeku | Wymagany nagłówek nie został przedstawiony lub brakuje wartości               | HeaderNotFound (Nieukryty nagłówek)            | Nie znaleziono nagłówka {header-name} w żądaniu. Odmowa dostępu.                                                                    |
| nagłówek czeku | Wymagany nagłówek nie został przedstawiony lub brakuje wartości               | Wartość nagłówka Nieuwolna     | Nagłówek {header-name} wartość {header-value} jest niedozwolona. Odmowa dostępu.                                                          |
| validate-jwt | W żądaniu brakuje tokenu Jwt                                 | TokenNotfound             | JWT nie znaleziono w żądaniu. Odmowa dostępu.                                                                                         |
| validate-jwt | Sprawdzanie poprawności podpisu nie powiodło się                                     | TokenSignatureInvalid     | <wiadomość z biblioteki\>jwt . Odmowa dostępu.                                                                                          |
| validate-jwt | Nieprawidłowa grupa odbiorców                                                | TokenAudienceNiej nie dozwolona   | <wiadomość z biblioteki\>jwt . Odmowa dostępu.                                                                                          |
| validate-jwt | Nieprawidłowy wystawca                                                  | TokenIssuerNieuj     | <wiadomość z biblioteki\>jwt . Odmowa dostępu.                                                                                          |
| validate-jwt | Token wygasł                                                   | TokenExpired              | <wiadomość z biblioteki\>jwt . Odmowa dostępu.                                                                                          |
| validate-jwt | Klucz podpisu nie został rozwiązany przez identyfikator                            | TokenSignatureKeyNotFound | <wiadomość z biblioteki\>jwt . Odmowa dostępu.                                                                                          |
| validate-jwt | W tokenie brakuje wymaganych oświadczeń                          | TokenClaimNotFound        | Token JWT brakuje następujących roszczeń: <c1\>, <\>c2 , ... Odmowa dostępu.                                                            |
| validate-jwt | Niezgodność wartości oświadczeń                                           | TokenClaimValueNoallowed | Oświadczenie {nazwa oświadczenia} wartość {claim-value} jest niedozwolona. Odmowa dostępu.                                                             |
| validate-jwt | Inne błędy sprawdzania poprawności                                       | JwtInvalid ( JwtInvalid )                | <wiadomość z biblioteki jwt\>                                                                                                          |
| żądanie przesyłania dalej lub żądanie wysłania | Kod stanu odpowiedzi HTTP i nagłówki nie zostały odebrane z wewnętrznej bazy danych w skonfigurowanym przekroju czasu | Limit czasu | wiele |

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

a wysłanie nieautoryzowanego żądania spowoduje następującą odpowiedź:

![Nieautoryzowana odpowiedź na błąd](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
-   [Przekształcanie interfejsów API](transform-api.md)
-   [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
-   [Przykłady zasad](policy-samples.md)
