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
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6bc71c0745493d52128553a78a31c45a3bca30f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2017
ms.locfileid: "26128230"
---
# <a name="error-handling-in-api-management-policies"></a>Obsługa błędów w zasad interfejsu API zarządzania
Zarządzanie interfejsami API Azure umożliwia wydawców odpowiedzieć na błędy, które mogą wystąpić podczas przetwarzania żądania do serwera proxy, udostępniając `ProxyError` obiektu. `ProxyError` Obiekt jest dostępny za pośrednictwem [kontekstu. LastError](api-management-policy-expressions.md#ContextVariables) właściwości i mogą być używane przez zasady w `on-error` sekcji zasad. W tym temacie znajdują się informacje na błąd możliwości obsługi w programie Azure API Management.  
  
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
  
 Podczas przetwarzania żądania wbudowanych kroków wdrożeniowych wraz z żadnych zasad, które znajdują się w zakresie dla żądania. Jeśli wystąpi błąd, przetwarzania natychmiast przechodzi do `on-error` sekcji zasad. `on-error` Sekcji zasad może być używany w żadnych zakresu i wydawców interfejsu API można skonfigurować niestandardowe zachowanie, takie jak rejestrowanie błąd do usługi event hubs lub Utwórz nową odpowiedź, aby powrócić do obiektu wywołującego.  
  
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
 Jeśli wystąpi błąd i formant przechodzi do `on-error` błąd sekcji zasady, są przechowywane w [kontekstu. LastError](api-management-policy-expressions.md#ContextVariables) właściwości, które są dostępne dla zasad w `on-error` sekcji i ma następujące właściwości.  
  
|Nazwa|Typ|Opis|Wymagane|  
|----------|----------|-----------------|--------------|  
|Element źródłowy|Ciąg|Określa nazwę elementu, na którym wystąpił błąd. Może być zasad lub nazwa kroku wbudowanej potoku.|Tak|  
|Przyczyna|Ciąg|Kod błędu przyjaznych dla komputera, który może służyć do obsługi błędów.|Nie|  
|Komunikat|Ciąg|Opis błędu zrozumiałą dla użytkownika.|Tak|  
|Zakres|Ciąg|Nazwa zakresu, w którym wystąpił błąd i może być "globalne", "product", "interfejsu api" lub "operacji"|Nie|  
|Sekcja|Ciąg|Nazwa sekcji, w którym wystąpił błąd i może wynosić "ruchu przychodzącego", "zaplecze", "wychodzący" lub "on error".|Nie|  
|Ścieżka|Ciąg|Określa zagnieżdżonych zasad, np. "Wybierz [3] / po [2]".|Nie|  
|PolicyId|Ciąg|Wartość `id` atrybut, jeśli określony przez klienta i w zasadach, w którym wystąpił błąd|Nie|  
  
> [!NOTE]
>  Wszystkie zasady mają opcjonalny `id` atrybut, który można dodać do elementu głównego zasad. Jeśli ten atrybut jest obecny w zasadach, gdy wystąpi błąd, można pobrać wartości atrybutu za pomocą `context.LastError.PolicyId` właściwości.  
  
## <a name="predefined-errors-for-built-in-steps"></a>Wstępnie zdefiniowane błędów dla wbudowanych kroków  
 Następujące błędy są wstępnie zdefiniowane warunki błędów, które mogą wystąpić podczas obliczania kroki przetwarzania wbudowanych.  
  
|Element źródłowy|Warunek|Przyczyna|Komunikat|  
|------------|---------------|------------|-------------|  
|konfiguracja|Identyfikator URI nie pasuje do operacji lub interfejsu Api|OperationNotFound|Nie można dopasować przychodzące żądanie operacji.|  
|Autoryzacji|Nie podano klucza subskrypcji|SubscriptionKeyNotFound|Odmowa z powodu braku subskrypcji klucz dostępu. Upewnij się uwzględnić subskrypcji klucza w przypadku wysyłania żądań do tego interfejsu API.|  
|Autoryzacji|Wartość klucza subskrypcji jest nieprawidłowy|SubscriptionKeyInvalid|Odmowa z powodu subskrypcji nieprawidłowy klucz dostępu. Upewnij się zapewnić prawidłowy klucz dla aktywnych subskrypcji.|  
  
## <a name="predefined-errors-for-policies"></a>Błędy wstępnie zdefiniowane zasady  
 Następujące błędy są wstępnie zdefiniowane warunki błędów, które mogą wystąpić podczas oceny zasad.  
  
|Element źródłowy|Warunek|Przyczyna|Komunikat|  
|------------|---------------|------------|-------------|  
|limit szybkości|Przekroczono limit szybkości|RateLimitExceeded|Przekroczono limit szybkości|  
|limit przydziału|Przekroczono przydział|QuotaExceeded|Poza limitem woluminu wywołania. Przydział zostanie uzupełniona w xx:xx:xx. - lub - limit przydziału przepustowości. Przydział zostanie uzupełniona w xx:xx:xx.|  
|Format jsonp|Nieprawidłowa wartość parametru wywołania zwrotnego (zawiera nieprawidłowe znaki)|CallbackParameterInvalid|Wartość parametru wywołania zwrotnego {-nazwy parametru wywołania zwrotnego-} nie jest prawidłowym identyfikatorem języka JavaScript.|  
|Filtr IP|Nie można przeanalizować IP wywołującego z żądania|FailedToParseCallerIP|Nie można ustalić adres IP dla obiekt wywołujący. Odmowa dostępu.|  
|Filtr IP|Obiekt wywołujący IP nie ma na liście listy dozwolonych|CallerIpNotAllowed|Adres IP wywołującego {adres ip} nie jest dozwolone. Odmowa dostępu.|  
|Filtr IP|Obiekt wywołujący adres IP należy do listy zablokowanych|CallerIpBlocked|Adres IP wywołującego jest zablokowany. Odmowa dostępu.|  
|Sprawdź — nagłówek|Brak wymaganego nagłówka nie przedstawione lub wartość|HeaderNotFound|Nie znaleziono nagłówka {Nazwa nagłówka} w żądaniu. Odmowa dostępu.|  
|Sprawdź — nagłówek|Brak wymaganego nagłówka nie przedstawione lub wartość|HeaderValueNotAllowed|Wartość nagłówka {Nazwa nagłówka} z {wartość nagłówka} nie jest dozwolona. Odmowa dostępu.|  
|Sprawdź poprawność jwt|W żądaniu brakuje tokenu Jwt|TokenNotFound|Nie można odnaleźć w żądaniu JWT. Odmowa dostępu.|  
|Sprawdź poprawność jwt|Nie można sprawdzić poprawności podpisu|TokenSignatureInvalid|< wiadomości z biblioteki jwt\>. Odmowa dostępu.|  
|Sprawdź poprawność jwt|Nieprawidłowi odbiorcy|TokenAudienceNotAllowed|< wiadomości z biblioteki jwt\>. Odmowa dostępu.|  
|Sprawdź poprawność jwt|Nieprawidłowy wystawca|TokenIssuerNotAllowed|< wiadomości z biblioteki jwt\>. Odmowa dostępu.|  
|Sprawdź poprawność jwt|Token wygasł|TokenExpired|< wiadomości z biblioteki jwt\>. Odmowa dostępu.|  
|Sprawdź poprawność jwt|Klucz podpisu nie został rozwiązany przez identyfikator|TokenSignatureKeyNotFound|< wiadomości z biblioteki jwt\>. Odmowa dostępu.|  
|Sprawdź poprawność jwt|Token brakuje wymaganego oświadczeń|TokenClaimNotFound|JWT token brakuje następujących oświadczeń: < c1\>, < c2\>,... Odmowa dostępu.|  
|Sprawdź poprawność jwt|Niezgodność wartości oświadczeń|TokenClaimValueNotAllowed|Wartość oświadczenia {Nazwa oświadczenia} {wartość oświadczenia} nie jest dozwolona. Odmowa dostępu.|  
|Sprawdź poprawność jwt|Inne błędy sprawdzania poprawności|JwtInvalid|< wiadomości z biblioteki jwt\>|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   