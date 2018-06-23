---
title: Adres URL podglądu odwołanie - kognitywnych usług firmy Microsoft do projektu | Dokumentacja firmy Microsoft
description: Odwołanie do punktu końcowego podglądu adres URL projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: adc2f83f703e740e40d9ba4fd3ed08ba429e5d97
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348737"
---
# <a name="project-url-preview-v7-reference"></a>Adres URL w wersji zapoznawczej w wersji 7 odwołanie do projektu

Adres URL w wersji zapoznawczej obsługuje krótkie opisy zasobów sieci Web blogach dyskusjach na forum, strony Podgląd itp.  Adres URL może być dowolnego typu zasobu internetowego: strony sieci Web, grup dyskusyjnych, obrazu i wideo. Zapytanie musi być bezwzględny adres URL ze schematem http lub https; względne adresy URL lub innych systemów, takich jak ftp: / / nie są obsługiwane.

Aplikacje używające adresu URL w wersji zapoznawczej wysyłać żądania sieci Web z punktem końcowym o adresie URL w parametr zapytania.  Żądanie musi zawierać *Ocp-Apim-subskrypcji — klucz* nagłówka.   

Odpowiedź w formacie JSON może być analizowana pod kątem informacji w wersji zapoznawczej: nazwa, opis zasobu, *isFamilyFriendly*i linki, które zapewniają dostęp do reprezentatywnego obrazu oraz pełną zasób w tryb online.

Możesz korzystać tylko dane z adresu URL w wersji zapoznawczej do wyświetlenia wstawki podglądu i miniatur Link do ich lokacji źródłowych w adresie URL inicjowanych przez użytkownika końcowego, udostępniania mediów społecznościowych, rozmów bot lub podobne oferty. Należy nie skopiować, przechowywania lub pamięci podręcznej danych otrzymywanych z podglądu adres URL projektu. Należy uwzględnić wszystkie żądania, aby wyłączyć podglądy, które mogą wystąpić z witryny sieci Web lub właściciele zawartości.

## <a name="endpoint"></a>Endpoint
Aby poprosić o adres URL Podgląd wyników, należy wysłać żądania do następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

Punkt końcowy GET: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

Żądanie musi używać protokołu HTTPS i obejmują następujące parametru zapytania:

 q zapytania, który określa adres URL do podglądu 

Poniższe sekcje zawierają szczegółowe informacje techniczne dotyczące obiektów odpowiedzi, parametry zapytania i nagłówków, które mają wpływ na wyniki wyszukiwania. 
  
Informacji o nagłówkach, obejmujących żądań, zobacz [nagłówki](#headers).  
  
Informacje o parametrach zapytania, obejmujących żądań, zobacz [parametry zapytania](#query-parameters).  
  
Aby uzyskać informacje o formacie JSON obiekty odpowiedź zawiera, zobacz [obiektów odpowiedzi](#response-objects).

Zapytanie maksymalna długość adresu URL jest 2048 znaków. Aby upewnić się, Twoje długość adresu URL przekracza limit, maksymalna długość parametry zapytania należy 1 mniej niż 500 znaków. Jeśli adres URL przekracza 2048 znaków, serwer zwraca 404, nie można odnaleźć.  

Uzyskać informacje o dozwolonych użycia i wyświetlania wyników, zobacz [użycia i wyświetlić wymagania](use-display-requirements.md). 

> [!NOTE]
> Niektóre nagłówków żądań, które zrozumiały dla innych interfejsów API wyszukiwania nie wpływają na adres URL w wersji zapoznawczej
> - Pragma — obiekt wywołujący nie ma kontroli nad Określa, czy adres URL podglądu korzysta z pamięci podręcznej
> - Agent użytkownika — obecnie adresu Url interfejsu API w wersji zapoznawczej nie zapewnia różnych odpowiedzi na wywołania pochodzące z komputera, laptopów lub Mobile.

> Ponadto niektóre parametry nie są obecnie zrozumiały dla interfejsu API w wersji zapoznawczej adres URL, ale mogą być używane w przyszłości ulepszone globalizacji. 
 
## <a name="headers"></a>Nagłówki  
Poniżej przedstawiono nagłówki, które mogą obejmować żądania i odpowiedzi.  
  
|Nagłówek|Opis|  
|------------|-----------------|   
|<a name="market" />BingAPIs rynku|Nagłówek odpowiedzi.<br /><br /> Rynku używany przez żądanie. Formularz jest \<atrybutu languageCode\>-\<countryCode\>. Na przykład en US.|  
|<a name="traceid" />BingAPIs TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegóły żądania. Gdy wystąpi błąd, przechwycić tego identyfikatora. Jeśli nie jest możliwe określić i rozwiązać problem, należy uwzględnić ten identyfikator wraz z innymi informacjami, musisz zapewnić zespołem pomocy technicznej.|  
|<a name="subscriptionkey" />OCP-Apim subskrypcji — klawisz|Nagłówek żądania wymagane.<br /><br /> Klucz subskrypcji, odebrany po zarejestrowaniu dla tej usługi w [kognitywnych usług](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|Opcjonalne nagłówki żądań i odpowiedzi.<br /><br /> Bing używa tego nagłówka użytkownikom zachowanie spójności w wywołaniach interfejsu API usługi Bing. Bing często lotach nowe funkcje i ulepszenia, i używa Identyfikatora klienta jako klucza do przypisywania ruchu w różnych lotach. Jeśli nie używasz tego samego Identyfikatora klienta dla użytkownika dla wielu żądań przesyłanych, Bing może przypisać użytkownika do wielu lotach powodujące konflikt. Przypisane do wielu lotach powodujące konflikt może spowodować niespójne użytkowników. Na przykład jeśli drugi żądania przypisaniem transmitowane innego niż pierwszy, środowisko użytkownika może być nieoczekiwany. Ponadto Bing można użyć Identyfikatora klienta, aby dostosować wyniki sieci web na tym kliencie identyfikatory historii wyszukiwania, podając bardziej rozbudowane środowisko użytkownika.<br /><br /> Bing również używa tego nagłówka do poprawy wyników klasyfikacji za analizowanie aktywności generowane przez identyfikator klienta. Istotne ulepszenia pomoc lepszą jakość wyniki dostarczone przez interfejsy API usługi Bing i z kolei umożliwia kliknięć większe dla klienta interfejsu API.<br /><br />Poniżej przedstawiono zasady podstawowe sposoby użycia, które są stosowane do tego nagłówka.<br /><ul><li>Każdy użytkownik, który używa aplikacji na urządzeniu musi mieć unikatową, Bing wygenerowany identyfikator klienta.<br /><br/>Jeśli nie obejmują tego nagłówka w żądaniu Bing generuje identyfikator i zwraca go w nagłówka X-MSEdge-ClientID odpowiedzi. Tylko wtedy, że nie należy używać tego nagłówka w żądaniu po raz pierwszy użytkownik korzysta z aplikacji na tym urządzeniu.<br /><br/></li><li>Dla każdego żądania interfejsu API Bing sprawia, że aplikacja dla tego użytkownika na urządzeniu, należy użyć Identyfikatora klienta.<br /><br/></li><li>**Uwaga:** musi upewnij się, że ten identyfikator klienta nie jest możliwym do żadnych informacji o koncie użytkownika poświadczalne.</li><br/><li>Utrwalanie identyfikator klienta. Aby zachować identyfikator w aplikacji przeglądarki, umożliwia upewnij się, że ten identyfikator jest używany we wszystkich sesjach trwały plik cookie HTTP. Nie należy używać pliku cookie sesji. Dla innych aplikacji, takich jak aplikacje mobilne Użyj urządzenia magazynu trwałego do utrzymania identyfikator.<br /><br/>Następnym razem, użytkownik korzysta z aplikacji na tym urządzeniu pobieranie Identyfikatora klienta, które zostały utrwalone.</li></ul><br /> **Uwaga:** Bing odpowiedzi może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera ten nagłówek, przechwytywania identyfikator klienta i użyć jej do wszystkich kolejnych żądań Bing dla użytkownika na tym urządzeniu.<br /><br /> **Uwaga:** Jeśli dołączysz X MSEdge ClientID nie może zawierać pliki cookie w żądaniu.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nagłówek żądania opcjonalne.<br /><br /> Adres IPv4 lub IPv6 urządzenia klienckiego. Adres IP jest używany do odnajdywania lokalizacji użytkownika. Bing używa informacji o lokalizacji w celu określenia działanie wyszukiwania bezpieczne.<br /><br />  Adres nie zasłaniają (na przykład, zmieniając ostatni oktet 0). Obfuscating wyników adresu w tej lokalizacji nie jest dowolne miejsce w pobliżu urządzenia rzeczywistej lokalizacji, co może spowodować Bing obsługująca błędne wyniki.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Parametry zapytania  
Żądanie może obejmować następujące parametry zapytania. Zobacz wymaganych kolumn dla wymaganych parametrów. Adres URL należy zakodować parametry zapytania. Zapytanie musi być bezwzględny adres URL ze schematem http lub https; nie obsługujemy względnych adresów URL lub innych systemów, takich jak ftp: / /
  
  
|Name (Nazwa)|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Rynku, skąd wyniki. <br /><br />Aby uzyskać listę możliwych wartości rynku, zobacz [kody rynku](#market-codes).<br /><br /> **Uwaga:** interfejsu API w wersji zapoznawczej adresu URL aktualnie obsługuje tylko USA geograficzne i język angielski.<br /><br />|Ciąg|Yes|  
|<a name="query" />Q|Adres URL do podglądu|Ciąg|Yes|  
|<a name="responseformat" />Format odpowiedzi|Typ nośnika do użycia dla odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje o formacie JSON obiekty odpowiedź zawiera, zobacz [obiektów odpowiedzi](#response-objects).<br /><br />  Jeśli określisz JsonLd treść odpowiedzi zawiera obiekty JSON LD, które zawierają wyniki wyszukiwania. Informacje o JSON LD, zobacz [JSON-LD](http://json-ld.org/).|Ciąg|Nie|
|<a name="safesearch"/>bezpieczne wyszukiwanie|Zawartość dla dorosłych niedozwolony lub pirackich zawartość, jest zablokowany z kodem błędu 400 i *isFamilyFriendly* flagi nie są zwracane. <p>Treści dla dorosłych prawnych poniżej jest to zachowanie. Zwraca wartość Kod stanu 200 i *isFamilyFriendly* flaga jest ustawiona na wartość false.<ul><li>bezpieczne wyszukiwanie = strict: tytuł, opis i adres URL obrazu nie zostaną zwrócone.</li><li>bezpieczne wyszukiwanie = umiarkowany; Pobierz tytuł, adres URL i opis, ale nie opis obrazu.</li><li>bezpieczne wyszukiwanie = wyłączone; Pobierz wszystkie odpowiedzi obiektów/elementy — tytuł, adres URL, opis i obraz.</li></ul> |Ciąg|Nie jest wymagane. </br> Domyślnie bezpieczne wyszukiwanie = strict.| 

## <a name="response-objects"></a>Obiekty odpowiedzi  
Schemat odpowiedzi jest albo [strony sieci Web] lub errorresponse języka, tak jak interfejsu API sieci Web wyszukiwania. Jeśli żądanie kończy się niepowodzeniem, obiekt najwyższego poziomu jest [errorresponse języka](#errorresponse) obiektu.


|Obiekt|Opis|  
|------------|-----------------|  
|[Strony sieci Web](#webpage)|Najwyższego poziomu obiekt JSON, który zawiera atrybuty podglądu.|  

  
### <a name="error"></a>Błąd  
Definiuje wystąpił błąd.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kod|Kod błędu, który identyfikuje kategorię błędu. Listę kodów można znaleźć [kody błędów](#error-codes).|Ciąg|  
|<a name="error-message" />Komunikat|Opis błędu.|Ciąg|  
|<a name="error-moredetails" />moreDetails|Opis, który udostępnia dodatkowe informacje o błędzie.|Ciąg|  
|<a name="error-parameter" />Parametr|Parametr zapytania w żądaniu, który spowodował błąd.|Ciąg|  
|<a name="error-subcode" />Kod podrzędny|Kod błędu, który identyfikuje błąd. Na przykład jeśli `code` jest InvalidRequest, `subCode` może być ParameterInvalid lub ParameterInvalidValue. |Ciąg|  
|<a name="error-value" />Wartość|Wartość parametru zapytania, która jest nieprawidłowa.|Ciąg|  
  

### <a name="errorresponse"></a>Errorresponse języka  
Obiekt najwyższego poziomu, który obejmuje odpowiedzi, gdy żądanie nie powiodło się.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_wprowadź|Wskazówka typu.|Ciąg|  
|<a name="errors" />błędy|Lista błędów, które opisują przyczyny, dlaczego żądanie zawiodło.|[Błąd](#error)]|   
  

### <a name="webpage"></a>Strony sieci Web  
Określa informacje o stronie sieci Web w wersji zapoznawczej.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|
|name|Tytuł strony, niekoniecznie tytuł HTML|Ciąg|
|url|Adres URL, który faktycznie pochodził (żądanie może zostały wykonane przekierowania)|Ciąg|  
|description|Krótki opis strony i zawartości|Ciąg|  
|isFamilyFriendly|Najbardziej dokładna dla elementów w indeksie sieci web; pobiera w czasie rzeczywistym czy wykryciem wyłącznie na adres URL, a nie zawartości strony|wartość logiczna|
|primaryImageOfPage/contentUrl|Adres URL obrazu reprezentatywny do uwzględnienia w wersji zapoznawczej|Ciąg| 


### <a name="identifiable"></a>Do zidentyfikowania
|Name (Nazwa)|Wartość|Typ|  
|-------------|-----------------|----------|
|id|Identyfikator zasobu|Ciąg|
 

## <a name="error-codes"></a>Kody błędów

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|200|Powodzenie.|  
|400|Jeden z parametrów zapytania jest lub jest ona nieprawidłowa.| 
|400|ServerError, kodów podrzędnych ResourceError: nie można osiągnąć żądanego adresu URL|
|400|ServerError, kodów podrzędnych ResourceError: żądany adres URL nie zwrócił kod powodzenia (w tym przypadku zwróceniem HTTP 404)|
|400|InvalidRequest, kodów podrzędnych zablokowane: żądany adres URL może zawierać zawartość dla dorosłych i zostało zablokowane| 
|401|Klucz subskrypcji nie istnieje lub jest nieprawidłowy.|  
|403|Użytkownik jest uwierzytelniany (na przykład używały klucza ważnej subskrypcji), ale nie mają uprawnień do żądanego zasobu.<br /><br /> Bing również mogą zwracać ten stan przekroczeniu wywołującego ich zapytania na przydziału miesięcznego.|  
|410|Żądania HTTP są używane zamiast protokołu HTTPS. HTTPS jest tylko obsługiwanych protokołów.|  
|429|Obiekt wywołujący przekracza ich zapytania na drugie przydziału.|  
|500|Nieoczekiwanego błędu serwera.|

Jeśli żądanie kończy się niepowodzeniem, odpowiedź zawiera [errorresponse języka](#errorresponse) obiekt, który zawiera listę [błąd](#error) obiektów, których opisano, co spowodowało błąd. Jeśli błąd jest związany z parametrem, `parameter` pole określa parametr, który jest problem. A jeśli błąd jest związany z wartością parametru `value` pole określa wartość, która jest nieprawidłowa.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Poniżej przedstawiono wartości kodów kodu i podrzędnego błędu może zawierać błąd.

|Kod|Kod podrzędny|Opis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowany|Bing zwraca InvalidRequest zawsze, gdy częścią żądania jest nieprawidłowy. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd jest ParameterMissing lub ParameterInvalidValue, kod stanu HTTP jest 400.<br/><br/>Jeśli używasz protokołu HTTP zamiast HTTPS Bing zwraca HttpNotAllowed i jest kod stanu HTTP 410.
|RateLimitExceeded|Żadnych kodów podrzędnych|Bing zwraca RateLimitExceeded zawsze, gdy przekracza z zapytania na sekundę (QPS) lub zapytania na przydziału miesięcznego (QPM).<br/><br/>Jeśli przekroczysz QPS Bing zwraca kod stanu HTTP 429, natomiast Jeśli przekroczysz QPM Bing 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy obiekt wywołujący nie mogą uwierzytelnić się Bing. Na przykład `Ocp-Apim-Subscription-Key` Brak nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Może to wystąpić, jeśli klucz Subskrypcja została wyłączona lub jego ważność wygasła. <br/><br/>Jeśli błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

## <a name="next-steps"></a>Kolejne kroki
- [C# — Szybki Start](csharp.md)
- [Szybki Start Java](java-quickstart.md)
- [JavaScript — Szybki Start](javascript.md)
- [Węzeł Szybki Start](node-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)

