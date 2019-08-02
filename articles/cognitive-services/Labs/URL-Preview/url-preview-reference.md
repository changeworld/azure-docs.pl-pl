---
title: Dokumentacja wersji zapoznawczej adresu URL projektu
titlesuffix: Azure Cognitive Services
description: Odwołanie do punktu końcowego wersji zapoznawczej adresu URL projektu.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: f92c0faaaa3aa0cd2af16a031f3bed4c6b41fc22
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706848"
---
# <a name="project-url-preview-v7-reference"></a>Odwołanie wersji 7 do podglądu adresu URL projektu

Wersja zapoznawcza URL obsługuje krótkie opisy zasobów sieci Web dla wpisów w blogu, dyskusji na forach, stron podglądu itp. Adres URL może być dowolnym typem zasobu internetowego: Strona sieci Web, wiadomości, obrazy lub wideo. Zapytanie musi być bezwzględnym adresem URL ze schematem http lub https; względne adresy URL lub inne schematy, takie jak ftp://, nie są obsługiwane.

Aplikacje korzystające z adresu URL w wersji zapoznawczej wysyłają żądania sieci Web do punktu końcowego z adresem URL, aby wyświetlić podgląd w parametrze zapytania. Żądanie musi zawierać nagłówek *OCP-APIM-Subscription-Key* .

Odpowiedź JSON można przeanalizować w celu uzyskania informacji o wersji zapoznawczej: nazwa, opis zasobu, *isFamilyFriendly*i linki zapewniające dostęp do reprezentatywnego obrazu oraz do kompletnego zasobu w trybie online.

Musisz użyć tylko danych z podglądu adresów URL, aby wyświetlić fragmenty podglądu i miniaturowe obrazy połączone ze swoimi lokacjami źródłowymi, w polu udostępnianie adresów URL zainicjowanym przez użytkownika na nośniku społecznościowym, bot rozmowy lub podobne oferty. Nie trzeba kopiować, przechowywać ani buforować żadnych danych otrzymywanych z wersji zapoznawczej adresu URL projektu. Aby wyłączyć podglądy, które mogą się pojawić z witryny sieci Web lub właścicieli zawartości, należy przestrzegać wszelkich żądań.

## <a name="endpoint"></a>Endpoint
Aby zażądać wyników podglądu adresu URL, Wyślij żądanie do poniższego punktu końcowego. Użyj nagłówków i parametrów adresu URL, aby zdefiniować dalsze specyfikacje.

Pobieranie punktu końcowego:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

Żądanie musi korzystać z protokołu HTTPS i zawierać następujący parametr zapytania:

pytania i odpowiedzi, które identyfikują adres URL do podglądu

W poniższych sekcjach znajdują się szczegółowe informacje techniczne na temat obiektów odpowiedzi, parametrów zapytania i nagłówków, które mają wpływ na wyniki wyszukiwania.

Aby uzyskać informacje o nagłówkach, które powinny obejmować żądania, zobacz [nagłówki](#headers).

Aby uzyskać informacje na temat parametrów zapytania, które powinny zawierać żądania, zobacz [parametry zapytania](#query-parameters).

Aby uzyskać informacje na temat obiektów JSON, które zawiera odpowiedź, zobacz [obiekty odpowiedzi](#response-objects).

Maksymalna długość adresu URL zapytania to 2 048 znaków. Aby zapewnić, że długość adresu URL nie przekracza limitu, Maksymalna długość parametrów zapytania powinna być krótsza niż 1 500 znaków. Jeśli adres URL przekracza 2 048 znaków, serwer zwraca 404 nie znaleziono.

Aby uzyskać informacje na temat dozwolonych użycia i wyświetlania wyników, zobacz [użycie i wyświetlanie wymagań](use-display-requirements.md).

> [!NOTE]
> Niektóre nagłówki żądań mające znaczenie dla innych interfejsów API wyszukiwania nie wpływają na Podgląd adresu URL
> - Pragma — obiekt wywołujący nie ma kontroli nad tym, czy adres URL podglądu używa pamięci podręcznej
> - User-Agent — teraz interfejs API podglądu adresów URL nie udostępnia różnych odpowiedzi na wywołania pochodzące z komputerów, laptopów lub urządzeń przenośnych.
> 
> Ponadto niektóre parametry nie są obecnie zrozumiałe dla interfejsu API w wersji zapoznawczej, ale mogą być używane w przyszłości na potrzeby ulepszonego globalizacji.

## <a name="headers"></a>Nagłówki
Poniżej znajdują się nagłówki, których może dotyczyć żądanie i odpowiedź.

|nagłówek|Opis|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|Nagłówek odpowiedzi.<br /><br /> Rynek używany przez żądanie. Format jest następujący \<kod_języka\>-\<kod_kraju\>. Na przykład en-US.|
|<a name="traceid" />BingAPIs-TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwyć ten identyfikator. Jeśli nie możesz określić i rozwiązać problemu, dołącz ten identyfikator wraz z innymi informacjami, które podasz zespołowi pomocy technicznej.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania.<br /><br /> Klucz subskrypcji otrzymany podczas tworzenia konta dla tej usługi w usługach [Cognitive Services](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Opcjonalny nagłówek żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego nagłówka, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Usługa Bing często testuje nowe funkcje i ulepszenia i używa identyfikatora klienta jako klucza do przypisywania ruchu dla różnych pakietów testowych. Jeśli nie będziesz używać tego samego identyfikatora klienta dla użytkownika w wielu żądaniach, usługa Bing może przypisać użytkownika do różnych, konfliktowych pakietów testowych. Przypisanie do wielu konfliktowych pakietów testowych może prowadzić do niespójnego środowiska użytkownika. Jeśli na przykład drugie żądanie ma przypisany inny pakiet testowy, niż pierwsze, środowisko obsługi może być nieoczekiwane. Ponadto usługa Bing może używać identyfikatora klienta, aby dopasować wyniki internetowe do historii wyszukiwania tego identyfikatora, zapewniając użytkownikowi bogatsze środowisko obsługi.<br /><br /> Usługa Bing używa także tego nagłówka, aby ulepszyć pozycjonowanie wyników, analizując aktywność generowaną przez identyfikator klienta. Ulepszenia istotności pomagają zwiększyć jakość wyników dostarczanych przez interfejsy API usługi Bing, co w rezultacie daje wyższą częstotliwość kliknięć dla użytkownika interfejsu API.<br /><br />Poniżej przedstawiono podstawowe reguły użycia, które mają zastosowanie do tego nagłówka.<br /><ul><li>Każdy użytkownik, który korzysta z Twojej aplikacji na urządzeniu, musi mieć unikatowy identyfikator klienta wygenerowany przez usługę Bing.<br /><br/>Jeśli nie uwzględnisz tego nagłówka w żądaniu, usługa Bing wygeneruje identyfikator i zwróci go w nagłówku odpowiedzi X-MSEdge-ClientID. Jedyną sytuacją, w której NIE należy uwzględniać tego nagłówka w żądaniu, jest pierwsze użycie Twojej aplikacji przez danego użytkownika na danym urządzeniu.<br /><br/></li><li>Używaj identyfikatora klienta dla każdego żądania interfejsu API usługi Bing, które Twoja aplikacja wykonuje dla tego użytkownika na danym urządzeniu.<br /><br/></li><li>**UWAGA** Należy upewnić się, że ten identyfikator klienta nie jest połączony z żadnym z uwierzytelnianymi informacjami o koncie użytkownika.</li><br/><li>Utrwal identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, użyj trwałego pliku cookie HTTP, aby mieć pewność, że dany identyfikator będzie używany we wszystkich sesjach. Nie należy używać plików cookie sesji. W przypadku innych aplikacji, takich jak aplikacje mobilne, użyj magazynu trwałego urządzenia, aby utrwalić identyfikator.<br /><br/>Następnym razem, gdy użytkownik będzie używać Twojej aplikacji na tym urządzeniu, uzyskaj utrwalony identyfikator klienta.</li></ul><br /> **UWAGA:** Odpowiedzi Bing mogą być nieuwzględnione w tym nagłówku lub nie. Jeśli odpowiedź zawiera ten nagłówek, przechwyć identyfikator klienta i używaj go dla wszystkich kolejnych żądań usługi Bing dla tego użytkownika na tym urządzeniu.<br /><br /> **UWAGA:** Jeśli dołączysz symbol X-MSEdge-ClientID, nie musisz zawierać plików cookie w żądaniu.|
|<a name="clientip" />X-MSEdge-ClientIP|Opcjonalny nagłówek żądania.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> Nie należy zaciemniać adres (na przykład zamieniając ostatni oktet na 0). Zaciemnianie adresu powoduje, że ustalona lokalizacja nie jest nawet przybliżona do rzeczywistej lokalizacji urządzenia, przez co usługa Bing może zwracać błędne wyniki.|

## <a name="query-parameters"></a>Parametry zapytania
Żądanie może zawierać następujące parametry zapytania. Sprawdź wymaganą kolumnę dla wymaganych parametrów. Należy zakodować parametry zapytania w adresie URL. Zapytanie musi być bezwzględnym adresem URL ze schematem http lub https; nie obsługujemy względnych adresów URL ani innych schematów, takich jak ftp://

|Name (Nazwa)|Value|Type|Wymagane|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|Rynek, z którego pochodzą wyniki. <br /><br />Aby uzyskać listę możliwych wartości rynkowych, zobacz Kody rynku.<br /><br /> **UWAGA:** Interfejs API wersji zapoznawczej dla adresu URL obsługuje obecnie tylko stany USA i języki angielskie.<br /><br />|String|Tak|
|<a name="query" />pytania|Adres URL do podglądu|Ciąg|Tak|
|<a name="responseformat" />responseFormat|Typ nośnika, który ma być używany na potrzeby odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje na temat obiektów JSON zawartych w odpowiedzi, zobacz temat [obiekty odpowiedzi](#response-objects).<br /><br />Jeśli określisz JsonLd, treść odpowiedzi zawiera obiekty JSON-LD, które zawierają wyniki wyszukiwania. Aby uzyskać informacje na temat JSON-LD, zobacz [JSON-LD](https://json-ld.org/).|String|Nie|
|<a name="safesearch"/>safeSearch|Niedozwolona zawartość dla dorosłych lub zawartość pirackia jest blokowana z kodem błędu 400 i flaga *isFamilyFriendly* nie jest zwracana. <p>W przypadku legalnej zawartości dla dorosłych poniżej przedstawiono zachowanie. Kod stanu zwraca 200, a flaga *isFamilyFriendly* jest ustawiona na false.<ul><li>Bezpieczne wyszukiwanie = Strict: Tytuł, opis, adres URL i obraz nie zostaną zwrócone.</li><li>Bezpieczne wyszukiwanie = umiarkowany; Pobierz tytuł, adres URL i opis, ale nie obraz opisowy.</li><li>Bezpieczne wyszukiwanie = wyłączone; Pobierz wszystkie obiekty/elementy odpowiedzi — tytuł, adres URL, opis i obraz.</li></ul> |String|Nie jest wymagane. </br> Wartość domyślna to bezpieczne wyszukiwanie = Strict.|

## <a name="response-objects"></a>Obiekty odpowiedzi
Schemat odpowiedzi to [Strona sieci Web] lub zwrócono, jak w interfejsie API wyszukiwanie w sieci Web. Jeśli żądanie nie powiedzie się, obiekt najwyższego poziomu jest obiektem [zwrócono](#errorresponse) .

|Object|Opis|
|------------|-----------------|
|[Stron](#webpage)|Obiekt JSON najwyższego poziomu, który zawiera atrybuty wersji zapoznawczej.|

### <a name="error"></a>Błąd
Określa błąd, który wystąpił.

|Element|Opis|Type|
|-------------|-----------------|----------|
|<a name="error-code" />kodu|Kod błędu, który identyfikuje kategorię błędu. Listę możliwych kodów można znaleźć w temacie [kody błędów](#error-codes).|Ciąg|
|<a name="error-message" />Komunikat|Opis błędu.|String|
|<a name="error-moredetails" />moreDetails|Opis, który zawiera dodatkowe informacje o błędzie.|String|
|<a name="error-parameter" />konstruktora|Parametr zapytania w żądaniu, który spowodował błąd.|String|
|<a name="error-subcode" />podkod|Kod błędu, który identyfikuje błąd. Na przykład jeśli `code` jest InvalidRequest, `subCode` może to być ParameterInvalid lub ParameterInvalidValue. |String|
|<a name="error-value" />wartościami|Wartość parametru zapytania, która jest nieprawidłowa.|String|

### <a name="errorresponse"></a>Zwrócono
Obiekt najwyższego poziomu, który odpowiedź zawiera, gdy żądanie nie powiedzie się.

|Name (Nazwa)|Wartość|Type|
|----------|-----------|----------|
|_type|Wskazówka dotycząca typu.|Ciąg|
|<a name="errors" />Błędy|Lista błędów opisujących przyczyny niepowodzenia żądania.|[Błąd](#error) []|

### <a name="webpage"></a>Stron
Definiuje informacje o stronie sieci Web w wersji zapoznawczej.

|Name (Nazwa)|Wartość|Type|
|----------|-----------|----------|
|name|Tytuł strony, niekoniecznie tytuł HTML|String|
|url|Adres URL, który był w rzeczywistości przeszukiwany (żądanie mogło być wykonane przekierowania)|Ciąg|
|description|Krótki opis strony i zawartości|String|
|isFamilyFriendly|Najdokładniejsze dla elementów w indeksie sieci Web; Pobieranie w czasie rzeczywistym umożliwia wykrycie wyłącznie na podstawie adresu URL, a nie zawartości strony|boolean|
|primaryImageOfPage/contentUrl|Adres URL reprezentatywnego obrazu do uwzględnienia w wersji zapoznawczej|String|

### <a name="identifiable"></a>Identyfikowan
|Name (Nazwa)|Value|Type|
|-------------|-----------------|----------|
|id|Identyfikator zasobu|String|

## <a name="error-codes"></a>Kody błędów

Oto możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|-----------------|-----------------|
|200|Powodzenie.|
|400|Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy.|
|400|Błąd servererror, ResourceError podkodu: Nie można skontaktować się z żądanym adresem URL|
|400|Błąd servererror, ResourceError podkodu: Żądany adres URL nie zwrócił kodu sukcesu (w tym, czy zwrócił on HTTP 404).|
|400|InvalidRequest, zablokowano kod w kodzie: Żądany adres URL może zawierać zawartość dla dorosłych i został zablokowany|
|401|Brak klucza subskrypcji lub jest on nieprawidłowy.|
|403|Użytkownik jest uwierzytelniany (na przykład użył prawidłowego klucza subskrypcji), ale nie ma uprawnień do żądanego zasobu.<br /><br /> Jeśli obiekt wywołujący przekroczył limit przydziału zapytań na miesiąc, może również zwrócić ten stan.|
|410|Żądanie używało protokołu HTTP zamiast protokołu HTTPS. Jedynym obsługiwanym protokołem jest protokół HTTPS.|
|429|Obiekt wywołujący przekroczył liczbę zapytań na sekundę.|
|500|Nieoczekiwany błąd serwera.|

Jeśli żądanie nie powiedzie się, odpowiedź zawiera obiekt [zwrócono](#errorresponse) , który zawiera listę obiektów [błędów](#error) , które opisują przyczynę błędu. Jeśli błąd jest związany z parametrem, `parameter` pole Identyfikuje parametr, który jest problemem. A jeśli błąd jest związany z wartością parametru, `value` pole określa nieprawidłową wartość.

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

Poniżej przedstawiono możliwy kod błędu i wartości kodu błędu podrzędnego.

|Kod|Podkod|Opis
|-|-|-
|Błąd servererror|UnexpectedError<br/>ResourceError<br/>NotImplemented|Kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowany|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP to 400.<br/><br/>Jeśli używasz protokołu HTTP zamiast HTTPS, Bing zwraca HttpNotAllowed, a kod stanu HTTP to 410.
|RateLimitExceeded|Brak kodów podrzędnych|Bing zwraca RateLimitExceeded za każdym razem, gdy przekroczą limit przydziału zapytań na sekundę (zapytań) lub zapytania miesięcznie (QPM).<br/><br/>W przypadku przekroczenia zapytań, Bing zwraca kod stanu HTTP 429 i w przypadku przekroczenia QPM, Bing zwróci 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Usługa Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Taka sytuacja może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd to InsufficientAuthorization, kod stanu HTTP to 403.

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka C#](csharp.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla języka JavaScript](javascript.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
