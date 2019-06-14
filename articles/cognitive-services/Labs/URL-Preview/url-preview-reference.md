---
title: Podgląd adresu URL odwołania do projektu
titlesuffix: Azure Cognitive Services
description: Odwołanie do projektu Podgląd adresu URL punktu końcowego.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 69db722295c9c81d45913bd078fe9cc5ab74c512
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60462592"
---
# <a name="project-url-preview-v7-reference"></a>Odwołanie do projektu Podgląd adresu URL w wersji 7

Podgląd adresu URL obsługuje krótkie opisy zasobów sieci Web dla wpisów w blogu, dyskusjach prowadzonych na forach, stron podglądu itp. Adres URL może być dowolnego typu zasobu internetowego: Strony sieci Web, grup dyskusyjnych, obrazu lub filmu wideo. Zapytanie musi być bezwzględnym adresem URL za pomocą protokołu http lub https schematu; względnych adresów URL lub innych systemów, takich jak ftp: / / nie są obsługiwane.

Aplikacje, które używają adresu URL w wersji zapoznawczej wysyłania żądań sieci Web do punktu końcowego przy użyciu adresu URL, aby wyświetlić podgląd w parametrze zapytania. Żądanie musi zawierać *Ocp-Apim-Subscription-Key* nagłówka.

Odpowiedź w formacie JSON może być analizowana pod kątem informacji o wersji zapoznawczej: nazwa, opis zasobu, *isFamilyFriendly*i łączy, które zapewniają dostęp do reprezentatywny obraz i pełny zasób w tryb online.

Do wyświetlania fragmentów (wersja zapoznawcza) i obrazy miniatur hiperłącza ich lokacji źródłowych w adresie URL inicjowane przez użytkownika końcowego, udostępnianie w mediach społecznościowych, czatbot lub podobne ofert, należy użyć tylko dane z adresu URL w wersji zapoznawczej. Należy nie kopiowania, przechowywania lub wszelkie dane, otrzymanymi od Podgląd adresu URL projektu z pamięci podręcznej. Należy uwzględnić wszystkie żądania, aby wyłączyć wersji zapoznawczych, które użytkownik może otrzymywać z witryny sieci Web lub właściciele zawartości.

## <a name="endpoint"></a>Endpoint
Aby zażądać Podgląd adresu URL wyników, należy wysłać żądanie do następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

Pobierz punkt końcowy:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

Żądanie musi używać protokołu HTTPS i obejmują następujące parametru zapytania:

q - kwerendę, która określa adres URL, aby wyświetlić podgląd

Poniższe sekcje zawierają szczegółowe informacje techniczne dotyczące obiektów odpowiedzi, parametry zapytania i nagłówków, które mają wpływ na wyniki wyszukiwania.

Aby dowiedzieć się, nagłówki, które powinny zawierać żądań, zobacz [nagłówki](#headers).

Aby uzyskać informacji na temat parametrów zapytania, które powinny zawierać żądań, zobacz [parametry zapytania](#query-parameters).

Aby uzyskać informacje o formacie JSON obiektów, że odpowiedź zawiera, zobacz [obiekty odpowiedzi](#response-objects).

Zapytanie maksymalna długość adresu URL to 2048 znaków. Aby upewnić się, że Twoje długość adresu URL nie przekracza limit, maksymalna długość parametry zapytania powinny być mniejszy niż 1500 znaków. Jeśli adres URL przekracza 2048 znaków, serwer zwraca błąd 404 — Nie odnaleziono.

Aby uzyskać informacji o dozwolone użycie i wyświetlania wyników, zobacz [użycia i wyświetlają wymagania dotyczące](use-display-requirements.md).

> [!NOTE]
> Niektóre nagłówki żądania, które mają znaczenie dla innych interfejsów API wyszukiwania nie mają wpływu na adres URL (wersja zapoznawcza)
> - Pragma — obiekt wywołujący nie ma kontroli nad tego, czy adres URL w wersji zapoznawczej używa pamięci podręcznej
> - Agent użytkownika — teraz adresu Url interfejsu API (wersja zapoznawcza) nie zapewnia różne odpowiedzi na wywołania pochodzące z komputera, Laptop lub Mobile.
> 
> Ponadto niektóre parametry nie są obecnie istotnych dla adresu URL interfejsu API (wersja zapoznawcza), ale mogą być używane w przyszłości dla globalizacji ulepszone.

## <a name="headers"></a>Nagłówki
Dostępne są następujące nagłówki, które mogą obejmować żądania i odpowiedzi.

|nagłówek|Opis|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|Nagłówek odpowiedzi.<br /><br /> Rynek używany przez żądanie. Format jest następujący \<kod_języka\>-\<kod_kraju\>. Na przykład en-US.|
|<a name="traceid" />BingAPIs-TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwyć ten identyfikator. Jeśli nie możesz określić i rozwiązać problemu, dołącz ten identyfikator wraz z innymi informacjami, które podasz zespołowi pomocy technicznej.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania.<br /><br /> Klucz subskrypcji otrzymany podczas tworzenia konta dla tej usługi w usługach [Cognitive Services](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Opcjonalny nagłówek żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego nagłówka, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Usługa Bing często testuje nowe funkcje i ulepszenia i używa identyfikatora klienta jako klucza do przypisywania ruchu dla różnych pakietów testowych. Jeśli nie będziesz używać tego samego identyfikatora klienta dla użytkownika w wielu żądaniach, usługa Bing może przypisać użytkownika do różnych, konfliktowych pakietów testowych. Przypisanie do wielu konfliktowych pakietów testowych może prowadzić do niespójnego środowiska użytkownika. Jeśli na przykład drugie żądanie ma przypisany inny pakiet testowy, niż pierwsze, środowisko obsługi może być nieoczekiwane. Ponadto usługa Bing może używać identyfikatora klienta, aby dopasować wyniki internetowe do historii wyszukiwania tego identyfikatora, zapewniając użytkownikowi bogatsze środowisko obsługi.<br /><br /> Usługa Bing używa także tego nagłówka, aby ulepszyć pozycjonowanie wyników, analizując aktywność generowaną przez identyfikator klienta. Ulepszenia istotności pomagają zwiększyć jakość wyników dostarczanych przez interfejsy API usługi Bing, co w rezultacie daje wyższą częstotliwość kliknięć dla użytkownika interfejsu API.<br /><br />Poniżej przedstawiono podstawowe reguły użycia, które mają zastosowanie do tego nagłówka.<br /><ul><li>Każdy użytkownik, który korzysta z Twojej aplikacji na urządzeniu, musi mieć unikatowy identyfikator klienta wygenerowany przez usługę Bing.<br /><br/>Jeśli nie uwzględnisz tego nagłówka w żądaniu, usługa Bing wygeneruje identyfikator i zwróci go w nagłówku odpowiedzi X-MSEdge-ClientID. Jedyną sytuacją, w której NIE należy uwzględniać tego nagłówka w żądaniu, jest pierwsze użycie Twojej aplikacji przez danego użytkownika na danym urządzeniu.<br /><br/></li><li>Używaj identyfikatora klienta dla każdego żądania interfejsu API usługi Bing, które Twoja aplikacja wykonuje dla tego użytkownika na danym urządzeniu.<br /><br/></li><li>**UWAGI:** Należy się upewnić, że tego Identyfikatora klienta nie jest możliwym do żadnych informacji o koncie użytkownika poświadczalne.</li><br/><li>Utrwal identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, użyj trwałego pliku cookie HTTP, aby mieć pewność, że dany identyfikator będzie używany we wszystkich sesjach. Nie należy używać plików cookie sesji. W przypadku innych aplikacji, takich jak aplikacje mobilne, użyj magazynu trwałego urządzenia, aby utrwalić identyfikator.<br /><br/>Następnym razem, gdy użytkownik będzie używać Twojej aplikacji na tym urządzeniu, uzyskaj utrwalony identyfikator klienta.</li></ul><br /> **UWAGA:** Odpowiedzi Bing może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera ten nagłówek, przechwyć identyfikator klienta i używaj go dla wszystkich kolejnych żądań usługi Bing dla tego użytkownika na tym urządzeniu.<br /><br /> **UWAGA:** Jeśli dodasz X MSEdge ClientID, nie może zawierać pliki cookie w żądaniu.|
|<a name="clientip" />X-MSEdge-ClientIP|Opcjonalny nagłówek żądania.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> Nie należy zaciemniać adres (na przykład zamieniając ostatni oktet na 0). Zaciemnianie adresu powoduje, że ustalona lokalizacja nie jest nawet przybliżona do rzeczywistej lokalizacji urządzenia, przez co usługa Bing może zwracać błędne wyniki.|

## <a name="query-parameters"></a>Parametry zapytania
Żądanie może obejmować następujące parametry zapytania. Zobacz wymaganej kolumny dla wymaganych parametrów. Należy najpierw, adres URL zakodować parametry zapytania. Zapytanie musi być bezwzględnym adresem URL za pomocą protokołu http lub https schematu; Firma Microsoft nie obsługuje względnych adresów URL lub innych systemów, takich jak ftp: / /

|Name (Nazwa)|Wartość|Typ|Wymagane|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|Rynek, z którego pochodzą wyniki. <br /><br />Aby uzyskać listę możliwych wartości na rynku Zobacz kodów na rynku.<br /><br /> **UWAGA:** Adres URL interfejsu API w wersji zapoznawczej aktualnie obsługuje tylko język angielski i położenia geograficznego w Stanach Zjednoczonych.<br /><br />|String|Tak|
|<a name="query" />q|Adres URL, aby wyświetlić podgląd|String|Tak|
|<a name="responseformat" />responseFormat|Typ multimediów do użycia dla odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje o formacie JSON obiektów, że odpowiedź zawiera, zobacz [obiekty odpowiedzi](#response-objects).<br /><br />Jeśli określisz JsonLd, treść odpowiedzi zawiera obiekty JSON-LD, zawierające wyniki wyszukiwania. Aby uzyskać informacji na temat JSON LD, zobacz [JSON-LD](https://json-ld.org/).|String|Nie|
|<a name="safesearch"/>safeSearch|Nielegalnych treści dla dorosłych lub pirackich zawartości jest zablokowany kod błędu: 400 i *isFamilyFriendly* flaga nie jest zwracana. <p>Treści dla dorosłych prawnych poniżej jest to zachowanie. Zwraca wartość Kod stanu 200, a *isFamilyFriendly* flaga jest ustawiona na wartość false.<ul><li>bezpieczne wyszukiwanie = ograniczeniami: Tytuł i opis, adres URL obrazu nie zostaną zwrócone.</li><li>bezpieczne wyszukiwanie = średni; Uzyskaj tytuł, adres URL i opis, ale nie opisowy obraz.</li><li>bezpieczne wyszukiwanie = wyłączone; Pobierz wszystkie odpowiedzi obiektów/elementy — tytuł, adres URL, opis i obraz.</li></ul> |String|Nie jest wymagane. </br> Wartość domyślna to bezpieczne wyszukiwanie = strict.|

## <a name="response-objects"></a>Obiekty odpowiedzi
Schemat odpowiedzi to albo [Strona internetowa] lub ErrorResponse, tak jak API wyszukiwania w Internecie. Jeśli żądanie zakończy się niepowodzeniem, jest obiektem najwyższego poziomu [ErrorResponse](#errorresponse) obiektu.

|Object|Opis|
|------------|-----------------|
|[WebPage](#webpage)|Najwyższego poziomu obiekt JSON, który zawiera atrybuty (wersja zapoznawcza).|

### <a name="error"></a>Błąd
Definiuje błąd, który wystąpił.

|Element|Opis|Typ|
|-------------|-----------------|----------|
|<a name="error-code" />Kod|Kod błędu, który identyfikuje kategorii błędów. Aby uzyskać listę możliwych kodów, zobacz [kody błędów](#error-codes).|String|
|<a name="error-message" />Komunikat|Opis błędu.|String|
|<a name="error-moredetails" />moreDetails|Opis, który zawiera dodatkowe informacje o tym błędzie.|String|
|<a name="error-parameter" />Parametr|Parametr zapytania w żądaniu, które spowodowały błąd.|String|
|<a name="error-subcode" />subCode|Kod błędu, który identyfikuje błąd. Na przykład jeśli `code` jest InvalidRequest, `subCode` może być ParameterInvalid lub ParameterInvalidValue. |String|
|<a name="error-value" />Wartość|Wartość parametru zapytania, która nie jest prawidłowa.|String|

### <a name="errorresponse"></a>ErrorResponse
Obiekt najwyższego poziomu, który zawiera odpowiedź, gdy żądanie zakończy się niepowodzeniem.

|Name (Nazwa)|Wartość|Typ|
|----------|-----------|----------|
|_type|Wskazówka typu.|String|
|<a name="errors" />Błędy|Lista błędów, które opisują przyczyny niepowodzenia żądania.|[Błąd](#error)]|

### <a name="webpage"></a>Strony sieci Web
Określa informacje o stronie sieci Web w wersji zapoznawczej.

|Name (Nazwa)|Wartość|Typ|
|----------|-----------|----------|
|name|Tytuł strony, niekoniecznie tytuł HTML|String|
|url|Adres URL, który faktycznie został przeszukane (żądanie może wykonano przekierowania)|String|
|description|Krótki opis strony i zawartości|String|
|isFamilyFriendly|Najdokładniejszych dla elementów w indeksie sieci web; Odczyty w czasie rzeczywistym, czy to wykrywanie wyłącznie na podstawie adresu URL i nie zawartości strony|wartość logiczna|
|primaryImageOfPage/contentUrl|Adres URL, który jest reprezentatywny obraz do uwzględnienia w wersji zapoznawczej|String|

### <a name="identifiable"></a>Do zidentyfikowania
|Name (Nazwa)|Wartość|Typ|
|-------------|-----------------|----------|
|id|Identyfikator zasobu|String|

## <a name="error-codes"></a>Kody błędów

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie.

|Kod stanu:|Opis|
|-----------------|-----------------|
|200|Powodzenie.|
|400|To jeden z parametrów zapytania, lub jest on nieprawidłowy.|
|400|Błąd ServerError, kodów podrzędnych ResourceError: Nie można połączyć się z żądanego adresu URL|
|400|Błąd ServerError, kodów podrzędnych ResourceError: Żądany adres URL nie zwrócił kod powodzenia (w tym przypadku zwrócony HTTP 404)|
|400|InvalidRequest, kodów podrzędnych zablokowane: Żądany adres URL może zawierać treści dla dorosłych i zostało zablokowane|
|401|Klucz subskrypcji nie istnieje lub jest nieprawidłowy.|
|403|Użytkownik jest uwierzytelniany (na przykład są używane klucz ważnej subskrypcji), ale nie mają uprawnień do żądanego zasobu.<br /><br /> Bing może również zwracać taki stan, obiekt wywołujący przekroczeniu ich zapytań na miesięcznego limitu przydziału.|
|410|Żądania HTTP są używane zamiast protokołu HTTPS. Protokół HTTPS jest jedynym protokołem obsługiwane.|
|429|Obiekt wywołujący przekracza ich zapytań na drugi limit przydziału.|
|500|Nieoczekiwanego błędu serwera.|

Jeśli żądanie zakończy się niepowodzeniem, odpowiedź zawiera [ErrorResponse](#errorresponse) obiekt, który zawiera listę [błąd](#error) obiekty, które opisują, co było przyczyną błędu. Jeśli ten błąd jest związany z parametrem, `parameter` pole określa parametr, który jest problem. A jeśli błąd jest związany z wartością parametru `value` pole określa wartość, która jest nieprawidłowa.

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

Poniżej przedstawiono wartości możliwy błąd kodu i podrzędnego błędu kodu.

|Kod|Podrzędnego|Opis
|-|-|-
|Błąd ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowane|Wyszukiwarka Bing zwróci InvalidRequest zawsze wtedy, gdy dowolnej części żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>W przypadku ParameterMissing lub ParameterInvalidValue błędu 400 jest kod stanu HTTP.<br/><br/>Jeśli używasz protokołu HTTP zamiast HTTPS, Wyszukiwarka Bing zwróci HttpNotAllowed i jest kod stanu HTTP 410.
|RateLimitExceeded|Nie kodów podrzędnych|Wyszukiwarka Bing zwróci RateLimitExceeded zawsze wtedy, gdy przekracza z zapytań na sekundę (QPS) lub zapytania na miesiąc (QPM) limitu przydziału.<br/><br/>Po przekroczeniu liczby zapytań na Sekundę, Wyszukiwarka Bing zwróci kod stanu HTTP 429, a Jeśli przekroczysz QPM, Wyszukiwarka Bing zwróci 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Wyszukiwarka Bing zwróci InvalidAuthorization, kiedy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje w przypadku określenia więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli ten błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wyszukiwarka Bing zwróci InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Może to wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli ten błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki start dla języka C#](csharp.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla języka JavaScript](javascript.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
