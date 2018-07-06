---
title: Projekt odwołanie adresu URL (wersja zapoznawcza) — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Odwołanie do projektu Podgląd adresu URL punktu końcowego.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 46c011d62b6ae51f5f7d292345e6ece0e27a8541
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865879"
---
# <a name="project-url-preview-v7-reference"></a>Odwołanie do projektu Podgląd adresu URL w wersji 7

Podgląd adresu URL obsługuje krótkie opisy zasobów sieci Web dla wpisów w blogu, dyskusjach prowadzonych na forach, stron podglądu itp.  Adres URL może być dowolnego typu zasobu internetowego: strony sieci Web, grup dyskusyjnych, obrazu lub filmu wideo. Zapytanie musi być bezwzględnym adresem URL za pomocą protokołu http lub https schematu; względnych adresów URL lub innych systemów, takich jak ftp: / / nie są obsługiwane.

Aplikacje, które używają adresu URL w wersji zapoznawczej wysyłania żądań sieci Web do punktu końcowego przy użyciu adresu URL, aby wyświetlić podgląd w parametrze zapytania.  Żądanie musi zawierać *Ocp-Apim-Subscription-Key* nagłówka.   

Odpowiedź w formacie JSON może być analizowana pod kątem informacji o wersji zapoznawczej: nazwa, opis zasobu, *isFamilyFriendly*i łączy, które zapewniają dostęp do reprezentatywny obraz i pełny zasób w tryb online.

Do wyświetlania fragmentów (wersja zapoznawcza) i obrazy miniatur hiperłącza ich lokacji źródłowych w adresie URL inicjowane przez użytkownika końcowego, udostępnianie w mediach społecznościowych, czatbot lub podobne ofert, należy użyć tylko dane z adresu URL w wersji zapoznawczej. Należy nie kopiowania, przechowywania lub wszelkie dane, otrzymanymi od Podgląd adresu URL projektu z pamięci podręcznej. Należy uwzględnić wszystkie żądania, aby wyłączyć wersji zapoznawczych, które użytkownik może otrzymywać z witryny sieci Web lub właściciele zawartości.

## <a name="endpoint"></a>Endpoint
Aby zażądać Podgląd adresu URL wyników, należy wysłać żądanie do następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

Pobierz punkt końcowy: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

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

> Ponadto niektóre parametry nie są obecnie istotnych dla adresu URL interfejsu API (wersja zapoznawcza), ale mogą być używane w przyszłości dla globalizacji ulepszone. 
 
## <a name="headers"></a>Nagłówki  
Dostępne są następujące nagłówki, które mogą obejmować żądania i odpowiedzi.  
  
|Nagłówek|Opis|  
|------------|-----------------|   
|<a name="market" />Rynek BingAPIs|Nagłówek odpowiedzi.<br /><br /> Na rynku, używany przez żądanie. Formularz jest \<languageCode\>-\<countryCode\>. Na przykład: en US.|  
|<a name="traceid" />BingAPIs TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwytywać tego identyfikatora. Jeśli nie jest możliwe określić i rozwiązać problem, należy dołączyć ten identyfikator oraz inne informacje, które podasz zespołem pomocy technicznej.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Nagłówek żądania wymagane.<br /><br /> Klucz subskrypcji, który otrzymał podczas tworzenia konta dla tej usługi w [usług Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />ClientID-X-MSEdge|Opcjonalne nagłówki żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego pliku nagłówkowego, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Bing często lotach nowe funkcje i ulepszenia i używa Identyfikatora klienta jako klucza do przypisywania ruch na różnych lotów. Jeśli nie używasz tego samego Identyfikatora klienta dla użytkownika dla wielu żądań, Bing może przypisać użytkownika do wielu lotów powodujące konflikt. Przypisywane do wielu lotów powodujące konflikt może prowadzić do środowiska użytkownika niespójne. Na przykład jeśli drugie żądanie przypisania lotu inny niż pierwszy, środowisko może być nieoczekiwany. Ponadto Bing można użyć Identyfikatora klienta, aby dostosować wyniki z Internetu na tym kliencie identyfikatory historii wyszukiwania, zapewniając bardziej zaawansowane środowisko użytkownika.<br /><br /> Usługa Bing używa tego pliku nagłówkowego również do poprawy wyników w klasyfikacji przez analizowanie aktywności generowane przez identyfikator klienta. Ulepszenia istotności ułatwić lepszą jakość wyników dostarczane przez interfejsy API usługi Bing i z kolei umożliwia za pomocą kliknięć większe konsumenta interfejsu API.<br /><br />Dostępne są następujące reguły podstawowe użycia, które są stosowane do tego pliku nagłówkowego.<br /><ul><li>Każdy użytkownik, który korzysta z aplikacji na urządzeniu musi mieć unikatową, Bing, wygenerowany identyfikator klienta.<br /><br/>Jeśli tego pliku nagłówkowego nie zostanie uwzględniony w żądaniu, Bing generuje identyfikator i zwraca go w nagłówku odpowiedzi ClientID-X-MSEdge. Jedyną sytuacją, że nie może zawierać tego nagłówka w żądaniu po raz pierwszy, o których użytkownik używa aplikacji na tym urządzeniu.<br /><br/></li><li>Dla każdego żądania interfejsu API usługi Bing, która aplikacja sprawia, że dla tego użytkownika na urządzeniu, należy użyć Identyfikatora klienta.<br /><br/></li><li>**Uwaga:** należy upewnić się, że tego Identyfikatora klienta nie jest możliwym do żadnych informacji o koncie użytkownika poświadczalne.</li><br/><li>Utrwalanie identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, umożliwia upewnij się, że identyfikator jest używany we wszystkich sesjach trwały plik cookie HTTP. Nie należy używać pliku cookie sesji. Dla innych aplikacji, takich jak aplikacje mobilne należy użyć urządzenia magazynu trwałego można utrwalić identyfikatora.<br /><br/>Następnym razem użytkownik używa aplikacji na tym urządzeniu, Uzyskaj identyfikator klienta, który zostały utrwalone.</li></ul><br /> **Uwaga:** odpowiedzi Bing może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera tego pliku nagłówkowego, przechwytywanie Identyfikatora klienta i użyć jej do wszystkich kolejnych żądań Bing dla użytkownika na tym urządzeniu.<br /><br /> **Uwaga:** Jeśli dodasz X MSEdge ClientID, nie może zawierać pliki cookie w żądaniu.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nagłówek żądania opcjonalne.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br />  Adres nie zaciemniania (na przykład, zmieniając ostatni oktet 0). Polega na zaciemnianiu wyniki adres, w tym miejscu nie jest w dowolnym miejscu w pobliżu lokalizacji rzeczywistego urządzenia, co może spowodować Bing obsługująca błędnych wyników.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Parametry zapytania  
Żądanie może obejmować następujące parametry zapytania. Zobacz wymaganej kolumny dla wymaganych parametrów. Należy najpierw, adres URL zakodować parametry zapytania. Zapytanie musi być bezwzględnym adresem URL za pomocą protokołu http lub https schematu; Firma Microsoft nie obsługuje względnych adresów URL lub innych systemów, takich jak ftp: / /
  
  
|Name (Nazwa)|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Na rynku, skąd pochodzą wyniki. <br /><br />Aby uzyskać listę możliwych wartości na rynku, zobacz [kody rynku](#market-codes).<br /><br /> **Uwaga:** adresu URL interfejsu API w wersji zapoznawczej aktualnie obsługuje tylko język angielski i położenia geograficznego w Stanach Zjednoczonych.<br /><br />|Ciąg|Yes|  
|<a name="query" />pytania i odpowiedzi|Adres URL, aby wyświetlić podgląd|Ciąg|Yes|  
|<a name="responseformat" />Format odpowiedzi|Typ multimediów do użycia dla odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje o formacie JSON obiektów, że odpowiedź zawiera, zobacz [obiekty odpowiedzi](#response-objects).<br /><br />  Jeśli określisz JsonLd, treść odpowiedzi zawiera obiekty JSON-LD, zawierające wyniki wyszukiwania. Aby uzyskać informacji na temat JSON LD, zobacz [JSON-LD](http://json-ld.org/).|Ciąg|Nie|
|<a name="safesearch"/>bezpieczne wyszukiwanie|Nielegalnych treści dla dorosłych lub pirackich zawartości jest zablokowany kod błędu: 400 i *isFamilyFriendly* flaga nie jest zwracana. <p>Treści dla dorosłych prawnych poniżej jest to zachowanie. Zwraca wartość Kod stanu 200, a *isFamilyFriendly* flaga jest ustawiona na wartość false.<ul><li>bezpieczne wyszukiwanie = strict: tytuł, opis, adres URL i obraz nie zostanie zwrócona.</li><li>bezpieczne wyszukiwanie = średni; Uzyskaj tytuł, adres URL i opis, ale nie opisowy obraz.</li><li>bezpieczne wyszukiwanie = wyłączone; Pobierz wszystkie odpowiedzi obiektów/elementy — tytuł, adres URL, opis i obraz.</li></ul> |Ciąg|Nie jest wymagane. </br> Wartość domyślna to bezpieczne wyszukiwanie = strict.| 

## <a name="response-objects"></a>Obiekty odpowiedzi  
Schemat odpowiedzi to albo [Strona internetowa] lub ErrorResponse, tak jak API wyszukiwania w Internecie. Jeśli żądanie zakończy się niepowodzeniem, jest obiektem najwyższego poziomu [ErrorResponse](#errorresponse) obiektu.


|Obiekt|Opis|  
|------------|-----------------|  
|[Strony sieci Web](#webpage)|Najwyższego poziomu obiekt JSON, który zawiera atrybuty (wersja zapoznawcza).|  

  
### <a name="error"></a>Błąd  
Definiuje błąd, który wystąpił.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kod|Kod błędu, który identyfikuje kategorii błędów. Aby uzyskać listę możliwych kodów, zobacz [kody błędów](#error-codes).|Ciąg|  
|<a name="error-message" />Komunikat|Opis błędu.|Ciąg|  
|<a name="error-moredetails" />moreDetails|Opis, który zawiera dodatkowe informacje o tym błędzie.|Ciąg|  
|<a name="error-parameter" />Parametr|Parametr zapytania w żądaniu, które spowodowały błąd.|Ciąg|  
|<a name="error-subcode" />podrzędnego|Kod błędu, który identyfikuje błąd. Na przykład jeśli `code` jest InvalidRequest, `subCode` może być ParameterInvalid lub ParameterInvalidValue. |Ciąg|  
|<a name="error-value" />Wartość|Wartość parametru zapytania, która nie jest prawidłowa.|Ciąg|  
  

### <a name="errorresponse"></a>ErrorResponse  
Obiekt najwyższego poziomu, który zawiera odpowiedź, gdy żądanie zakończy się niepowodzeniem.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_typ|Wskazówka typu.|Ciąg|  
|<a name="errors" />błędy|Lista błędów, które opisują przyczyny niepowodzenia żądania.|[Błąd](#error)]|   
  

### <a name="webpage"></a>Strony sieci Web  
Określa informacje o stronie sieci Web w wersji zapoznawczej.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|
|name|Tytuł strony, niekoniecznie tytuł HTML|Ciąg|
|url|Adres URL, który faktycznie został przeszukane (żądanie może wykonano przekierowania)|Ciąg|  
|description|Krótki opis strony i zawartości|Ciąg|  
|isFamilyFriendly|Najdokładniejszych dla elementów w indeksie sieci web; Odczyty w czasie rzeczywistym, czy to wykrywanie wyłącznie na podstawie adresu URL i nie zawartości strony|wartość logiczna|
|primaryImageOfPage/contentUrl|Adres URL, który jest reprezentatywny obraz do uwzględnienia w wersji zapoznawczej|Ciąg| 


### <a name="identifiable"></a>Do zidentyfikowania
|Name (Nazwa)|Wartość|Typ|  
|-------------|-----------------|----------|
|id|Identyfikator zasobu|Ciąg|
 

## <a name="error-codes"></a>Kody błędów

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|200|Powodzenie.|  
|400|To jeden z parametrów zapytania, lub jest on nieprawidłowy.| 
|400|Błąd ServerError, kodów podrzędnych ResourceError: nie można połączyć się z żądanego adresu URL|
|400|Błąd ServerError, kodów podrzędnych ResourceError: żądany adres URL nie zwrócił kod powodzenia (w tym przypadku zwrócony HTTP 404)|
|400|InvalidRequest, kodów podrzędnych zablokowane: żądany adres URL może zawierać treści dla dorosłych i zostało zablokowane| 
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
|Błąd ServerError|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowany|Wyszukiwarka Bing zwróci InvalidRequest zawsze wtedy, gdy dowolnej części żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>W przypadku ParameterMissing lub ParameterInvalidValue błędu 400 jest kod stanu HTTP.<br/><br/>Jeśli używasz protokołu HTTP zamiast HTTPS, Wyszukiwarka Bing zwróci HttpNotAllowed i jest kod stanu HTTP 410.
|RateLimitExceeded|Nie kodów podrzędnych|Wyszukiwarka Bing zwróci RateLimitExceeded zawsze wtedy, gdy przekracza z zapytań na sekundę (QPS) lub zapytania na miesiąc (QPM) limitu przydziału.<br/><br/>Po przekroczeniu liczby zapytań na Sekundę, Wyszukiwarka Bing zwróci kod stanu HTTP 429, a Jeśli przekroczysz QPM, Wyszukiwarka Bing zwróci 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Wyszukiwarka Bing zwróci InvalidAuthorization, kiedy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje w przypadku określenia więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli ten błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wyszukiwarka Bing zwróci InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Może to wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli ten błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki Start języka C#](csharp.md)
- [Przewodnik Szybki Start języka Java](java-quickstart.md)
- [Przewodnik Szybki Start języka JavaScript](javascript.md)
- [Przewodnik Szybki Start węzła](node-quickstart.md)
- [Przewodnik Szybki Start języka Python](python-quickstart.md)

