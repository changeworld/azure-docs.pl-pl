---
title: Dokumentacja projektu wyszukiwania odpowiedzi
titlesuffix: Azure Cognitive Services
description: Odwołanie do projektu, wyszukiwanie odpowiedzi punktu końcowego.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 1149f4d5ec0a3ef55c435d0555f944329cf5b890
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869602"
---
# <a name="project-answer-search-v7-reference"></a>Odwołanie do projektu odpowiedzi wyszukiwania w wersji 7

Bing odpowiedzi SearchAPI przyjmuje parametr zapytania i zwraca `searchResponse` z `answerType`: `facts` lub `entities`. 

Aplikacje, które używają interfejsu API wyszukiwania w odpowiedzi wysyłać żądania do punktu końcowego przy użyciu adresu URL, aby wyświetlić podgląd w parametrze zapytania.  Żądanie musi zawierać `q=searchTerm` parametru i *Ocp-Apim-Subscription-Key* nagłówka.   

Odpowiedź w formacie JSON, może być analizowana pod kątem faktów i jednostek, które zawierają szczegółowe informacje o obiekcie wyszukiwania.

## <a name="endpoint"></a>Endpoint
Aby zażądać wyniki wyszukiwania odpowiedzi, należy wysłać żądanie do następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

Pobierz punkt końcowy: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

Żądanie musi używać protokołu HTTPS i obejmują następujące parametru zapytania:
-  q =<URL> -kwerendę, która identyfikuje obiekt wyszukiwania

Przykłady pokazujące, jak żądań, zobacz [Szybki Start języka C#](c-sharp-quickstart.md) lub [Szybki Start Java](java-quickstart.md). 

Poniższe sekcje zawierają szczegółowe informacje techniczne dotyczące obiektów odpowiedzi, parametry zapytania i nagłówków, które mają wpływ na wyniki wyszukiwania. 
  
Aby dowiedzieć się, nagłówki, które powinny zawierać żądań, zobacz [nagłówki](#headers).  
  
Aby uzyskać informacji na temat parametrów zapytania, które powinny zawierać żądań, zobacz [parametry zapytania](#query-parameters).  
  
Aby uzyskać informacje o formacie JSON obiektów, że odpowiedź zawiera, zobacz [obiekty odpowiedzi](#response-objects).

Zapytanie maksymalna długość adresu URL to 2048 znaków. Aby upewnić się, że Twoje długość adresu URL nie przekracza limit, maksymalna długość parametry zapytania powinny być mniejszy niż 1500 znaków. Jeśli adres URL przekracza 2048 znaków, serwer zwraca błąd 404 — Nie odnaleziono.  

Aby uzyskać informacji o dozwolone użycie i wyświetlania wyników, zobacz [użycia i wyświetlają wymagania dotyczące](use-display-requirements.md). 

> [!NOTE]
> Niektóre nagłówki żądania, które mają znaczenie dla innych interfejsów API wyszukiwania nie mają wpływu na adres URL (wersja zapoznawcza)
> - Pragma — obiekt wywołujący nie ma kontroli nad tego, czy adres URL w wersji zapoznawczej używa pamięci podręcznej
> - Cache-Control — obiekt wywołujący nie ma kontroli nad tego, czy adres URL w wersji zapoznawczej używa pamięci podręcznej
> - Agent użytkownika

> Ponadto niektóre parametry nie są obecnie istotnych dla adresu URL interfejsu API (wersja zapoznawcza), ale mogą być używane w przyszłości dla globalizacji ulepszone. 
 
## <a name="headers"></a>Nagłówki  
Dostępne są następujące nagłówki, które mogą obejmować żądania i odpowiedzi.  
  
|Nagłówek|Opis|  
|------------|-----------------|  
|Zaakceptuj|Nagłówek żądania opcjonalne.<br /><br /> Domyślny typ nośnika jest application/json. Aby określić, czy odpowiedź korzystać [JSON-LD](http://json-ld.org/), ustaw nagłówek Accept application/ld + json.|  
|<a name="acceptlanguage" />Zaakceptuj języka|Nagłówek żądania opcjonalne.<br /><br /> Rozdzielana przecinkami lista języków na potrzeby ciągi interfejsu użytkownika. Lista jest w porządku malejącym. Aby uzyskać więcej informacji, łącznie z oczekiwanym formatem, zobacz [specyfikacją RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i [setLang](#setlang) parametr zapytania wykluczają się wzajemnie&mdash;nie określać jednocześnie.<br /><br /> Jeśli ustawisz ten nagłówek, należy także określić [DW](#cc) parametr zapytania. Aby ustalić, do zwracania wyników na rynku, usługa Bing używa pierwszego obsługiwanego języka znajdzie się na liście i łączy ją z `cc` wartość parametru. Jeżeli lista nie zawiera obsługiwanych języków, Bing znajduje się najbliższy języku i za pomocą rynku, który obsługuje żądania lub używa zagregowane lub domyślna rynku dla wyników. Aby określić na rynku, używanym w usłudze Bing, zobacz nagłówek BingAPIs rynku.<br /><br /> Użyj tego pliku nagłówkowego i `cc` parametr zapytania tylko wtedy, gdy należy określić w wielu językach. W przeciwnym razie użyj [mkt](#mkt) i [setLang](#setlang) parametry zapytania.<br /><br /> Ciąg interfejsu użytkownika jest ciągiem, który jest używana jako etykieta w interfejsie użytkownika. Istnieje kilka ciągów interfejsu użytkownika w obiektach odpowiedź JSON. Łącza do właściwości Bing.com w obiektach odpowiedzi zastosowanie określonego języka.|  
|<a name="market" />Rynek BingAPIs|Nagłówek odpowiedzi.<br /><br /> Na rynku, używany przez żądanie. Formularz jest \<languageCode\>-\<countryCode\>. Na przykład: en US.|  
|<a name="traceid" />BingAPIs TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwytywać tego identyfikatora. Jeśli nie jest możliwe określić i rozwiązać problem, należy dołączyć ten identyfikator oraz inne informacje, które podasz zespołem pomocy technicznej.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Nagłówek żądania wymagane.<br /><br /> Klucz subskrypcji, który otrzymał podczas tworzenia konta dla tej usługi w [usług Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Dyrektywy pragma|Nagłówek żądania opcjonalne<br /><br /> Domyślnie Wyszukiwarka Bing zwróci zawartości w pamięci podręcznej, jeśli jest dostępny. Aby zapobiec zwracaniem buforowanych zawartości Bing, ustawić nagłówek Pragma no-cache (na przykład Pragma: nie-cache).
|<a name="useragent" />Agent użytkownika|Nagłówek żądania opcjonalne.<br /><br /> Agent użytkownika wysłał żądanie. Usługa Bing używa agenta użytkownika zapewnienie zoptymalizowane środowiska użytkowników urządzeń przenośnych. Mimo że jest to opcjonalne, zachęcamy do zawsze określać tego nagłówka.<br /><br /> Agent użytkownika powinna być ten sam ciąg, który wysyła w dowolnej przeglądarce często używane. Aby uzyskać informacji na temat agentów użytkownika, zobacz [dokumencie RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągów agenta użytkownika.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodny; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Dotyk; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 kompilacji/GINGERBREAD) AppleWebKit/533.1 (KHTML; Gecko, takich jak) w wersji/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; Procesor CPU 6_1 system operacyjny telefonu iPhone, takich jak system Mac OS X) AppleWebKit/536.26 (KHTML; Gecko, takich jak) iPhone4 Mobile/10B142; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer&mdash;Mozilla/5.0 (Windows NT 6.3; EMULATOR WOW64; Trident/7.0. Dotyk; Rv:11.0) Gecko, takich jak<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; 7_0 Procesora systemu operacyjnego, takich jak system Mac OS X) AppleWebKit/537.51.1 (takich jak aparat Gecko KHTML) w wersji/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />ClientID-X-MSEdge|Opcjonalne nagłówki żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego pliku nagłówkowego, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Bing często lotach nowe funkcje i ulepszenia i używa Identyfikatora klienta jako klucza do przypisywania ruch na różnych lotów. Jeśli nie używasz tego samego Identyfikatora klienta dla użytkownika dla wielu żądań, Bing może przypisać użytkownika do wielu lotów powodujące konflikt. Przypisywane do wielu lotów powodujące konflikt może prowadzić do środowiska użytkownika niespójne. Na przykład jeśli drugie żądanie przypisania lotu inny niż pierwszy, środowisko może być nieoczekiwany. Ponadto Bing można użyć Identyfikatora klienta, aby dostosować wyniki z Internetu na tym kliencie identyfikatory historii wyszukiwania, zapewniając bardziej zaawansowane środowisko użytkownika.<br /><br /> Usługa Bing używa tego pliku nagłówkowego również do poprawy wyników w klasyfikacji przez analizowanie aktywności generowane przez identyfikator klienta. Ulepszenia istotności ułatwić lepszą jakość wyników dostarczane przez interfejsy API usługi Bing i z kolei umożliwia za pomocą kliknięć większe konsumenta interfejsu API.<br /><br /> **Ważne:** mimo, że jest to opcjonalne, należy wziąć pod uwagę tego pliku nagłówkowego wymagane. Przechowywanie identyfikator klienta dla wielu żądań dla tego samego użytkownika końcowego lub kombinacji urządzeń umożliwia (1) interfejsu API odbiorcę, aby otrzymywać spójne środowisko użytkownika oraz (2) za pomocą kliknięć większe za pośrednictwem lepszą jakość wyników z interfejsów API Bing.<br /><br /> Dostępne są następujące reguły podstawowe użycia, które są stosowane do tego pliku nagłówkowego.<br /><ul><li>Każdy użytkownik, który korzysta z aplikacji na urządzeniu musi mieć unikatową, Bing, wygenerowany identyfikator klienta.<br /><br/>Jeśli tego pliku nagłówkowego nie zostanie uwzględniony w żądaniu, Bing generuje identyfikator i zwraca go w nagłówku odpowiedzi ClientID-X-MSEdge. Jedyną sytuacją, że nie może zawierać tego nagłówka w żądaniu po raz pierwszy, o których użytkownik używa aplikacji na tym urządzeniu.<br /><br/></li><li>Dla każdego żądania interfejsu API usługi Bing, która aplikacja sprawia, że dla tego użytkownika na urządzeniu, należy użyć Identyfikatora klienta.<br /><br/></li><li>**Uwaga:** należy upewnić się, że tego Identyfikatora klienta nie jest możliwym do żadnych informacji o koncie użytkownika poświadczalne.</li><br/><li>Utrwalanie identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, umożliwia upewnij się, że identyfikator jest używany we wszystkich sesjach trwały plik cookie HTTP. Nie należy używać pliku cookie sesji. Dla innych aplikacji, takich jak aplikacje mobilne należy użyć urządzenia magazynu trwałego można utrwalić identyfikatora.<br /><br/>Następnym razem użytkownik używa aplikacji na tym urządzeniu, Uzyskaj identyfikator klienta, który zostały utrwalone.</li></ul><br /> **Uwaga:** odpowiedzi Bing może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera tego pliku nagłówkowego, przechwytywanie Identyfikatora klienta i użyć jej do wszystkich kolejnych żądań Bing dla użytkownika na tym urządzeniu.<br /><br /> **Uwaga:** Jeśli dodasz X MSEdge ClientID, nie może zawierać pliki cookie w żądaniu.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nagłówek żądania opcjonalne.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> **Uwaga:** mimo, że jest to opcjonalne, zachęcamy do zawsze określać tego pliku nagłówkowego i Nagłówek X-Search-lokalizacji.<br /><br /> Adres nie zaciemniania (na przykład, zmieniając ostatni oktet 0). Polega na zaciemnianiu wyniki adres, w tym miejscu nie jest w dowolnym miejscu w pobliżu lokalizacji rzeczywistego urządzenia, co może spowodować Bing obsługująca błędnych wyników.|  
|<a name="location" />Lokalizacja w przypadku wyszukiwania X|Nagłówek żądania opcjonalne.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, które opisują lokalizację geograficzną klienta. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania i zwrócić odpowiedniej zawartości lokalnej. Określ pary klucz/wartość jako \<klucz\>:\<wartość\>. Dostępne są następujące klucze, które służy do określania lokalizacji użytkownika.<br /><br /><ul><li>lat&mdash;szerokości geograficznej lokalizacji klienta, w stopniach. Szerokość musi być większa lub równa-90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują Latitude południowy, a wartości dodatnich wskazują Północna geograficznej.<br /><br /></li><li>długi&mdash;długości geograficznej lokalizacji klienta, w stopniach. Długość geograficzna musi być większa lub równa-180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują szerokości geograficzne western, a wartości dodatnich wskazują Wschodniej szerokości geograficzne.<br /><br /></li><li>ponownie&mdash; promień, w metrach, który określa dokładność pozioma współrzędne. Należy przekazać wartość zwrócona przez obiekt usługi lokalizacji urządzenia. Typowe wartości może być 22m dla GPS/Wi-Fi, 380m triangulacji tower komórka i 18,000m dla odwrotnego wyszukiwania adresu IP.<br /><br /></li><li>TS&mdash; sygnatura czasowa UTC UNIX kiedy klient nie był w lokalizacji. (Sygnatura czasowa systemu UNIX jest liczba sekund od 1 stycznia 1970 r.).<br /><br /></li><li>HEAD&mdash;atrybut opcjonalny. Klient względnej pozycji lub kierunku ruchu. Określ kierunek ruchu jako stopni od 0 do 360, zliczania do ruchu wskazówek zegara względem Północna wartość true. Określ kluczy tylko wtedy, gdy `sp` klucza jest różna od zera.<br /><br /></li><li>SP&mdash; prędkości poziome (szybkość) w metrach na sekundę, które są przesyłane na urządzeniu klienckim.<br /><br /></li><li>ALT&mdash; wysokość urządzenia klienta, w metrach.<br /><br /></li><li>czy&mdash;atrybut opcjonalny. Promień, w metrach, określająca dokładność pionowych we współrzędnych. Domyślnie promieniu 50 km. Określ ten klucz, tylko wtedy, gdy należy określić `alt` klucza.<br /><br /></li></ul> **Uwaga:** mimo że te klucze są opcjonalne, więcej informacji, które podasz, tym bardziej dokładne wyniki lokalizacji są.<br /><br /> **Uwaga:** zaleca się zawsze określić lokalizację geograficzną użytkownika. Podanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedla precyzyjnie lokalizacji fizycznej przez użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki należy dołączyć ten nagłówek i Nagłówek X-MSEdge-ClientIP, ale co najmniej powinien być dołączany do tego pliku nagłówkowego.|

> [!NOTE] 
> Należy pamiętać, że warunków użytkowania wymaga zgodności ze wszystkimi obowiązującymi przepisami, w tym dotyczących korzystania z tych nagłówków. Na przykład w niektórych systemach prawnych, takich jak Europa, istnieją wymagania w celu uzyskania zgody użytkownika przed wprowadzeniem niektórych urządzeń śledzenia na urządzeniach użytkowników.
  

## <a name="query-parameters"></a>Parametry zapytania  
Żądanie może obejmować następujące parametry zapytania. Zobacz wymaganej kolumny dla wymaganych parametrów. Należy najpierw, adres URL zakodować parametry zapytania.  
  
  
|Name (Nazwa)|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Na rynku, skąd pochodzą wyniki. <br /><br />Aby uzyskać listę możliwych wartości na rynku, zobacz [kody rynku](#market-codes).<br /><br /> **Uwaga:** adresu URL interfejsu API w wersji zapoznawczej aktualnie obsługuje tylko en-us rynku i język.<br /><br />|Ciąg|Yes|  
|<a name="query" />pytania i odpowiedzi|Adres URL, aby wyświetlić podgląd|Ciąg|Yes|  
|<a name="responseformat" />Format odpowiedzi|Typ multimediów do użycia dla odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje o formacie JSON obiektów, że odpowiedź zawiera, zobacz [obiekty odpowiedzi](#response-objects).<br /><br />  Jeśli określisz JsonLd, treść odpowiedzi zawiera obiekty JSON-LD, zawierające wyniki wyszukiwania. Aby uzyskać informacji na temat JSON LD, zobacz [JSON-LD](http://json-ld.org/).|Ciąg|Nie|  
|<a name="safesearch" />bezpieczne wyszukiwanie|Filtr używany do filtrowania zawartości dla dorosłych. Poniżej przedstawiono wartości możliwe filtru bez uwzględniania wielkości liter.<br /><ul><li>Wyłącz&mdash;zwracają stron internetowych z treści dla dorosłych tekst, obrazy lub filmy wideo.<br /><br/></li><li>Umiarkowany&mdash;zwracają stron internetowych z treści dla dorosłych tekstu, ale niepełnoletni obrazy lub filmy wideo.<br /><br/></li><li>Ścisłe&mdash;nie zwracają stron internetowych z treści dla dorosłych tekst, obrazy lub filmy wideo.</li></ul><br /> Wartość domyślna to średni.<br /><br /> **Uwaga:** Jeśli żądanie pochodzi z rynku zasad treści dla dorosłych tego Bing wymaga `safeSearch` jest ustawiony na Strict, ignoruje Bing `safeSearch` wartość i używa Strict.<br/><br/>**Uwaga:** Jeśli używasz `site:` — operator zapytań, istnieje prawdopodobieństwo, że odpowiedź może zawierać treści dla dorosłych niezależnie od tego, co `safeSearch` ustawiono parametr zapytania. Użyj `site:` tylko wtedy, gdy masz świadomość zawartości w witrynie i scenariusza obsługuje możliwość treści dla dorosłych. |Ciąg|Nie|  
|<a name="setlang" />setLang|Język na potrzeby ciągi interfejsu użytkownika. Określ język, przy użyciu ISO 639-1 literę 2 języka kodu. Na przykład kod języka na język angielski jest EN. Wartość domyślna to EN (w języku angielskim).<br /><br /> Mimo że jest to opcjonalne, należy zawsze określić język. Zwykle ustawiasz `setLang` w języku określonym przez `mkt` , chyba że użytkownik chce ciągi interfejsu użytkownika, które są wyświetlane w innym języku.<br /><br /> Ten parametr i [Accept-Language](#acceptlanguage) nagłówka wykluczają się wzajemnie&mdash;nie określać jednocześnie.<br /><br /> Ciąg interfejsu użytkownika jest ciągiem, który jest używana jako etykieta w interfejsie użytkownika. Istnieje kilka ciągów interfejsu użytkownika w obiektach odpowiedź JSON. Także łącza do właściwości Bing.com w obiektach odpowiedzi zastosować określonego języka.|Ciąg|Nie| 


## <a name="response-objects"></a>Obiekty odpowiedzi  
Schemat odpowiedzi to albo [Strona internetowa] lub ErrorResponse, tak jak API wyszukiwania w Internecie. Jeśli żądanie zakończy się niepowodzeniem, jest obiektem najwyższego poziomu [ErrorResponse](#errorresponse) obiektu.


|Obiekt|Opis|  
|------------|-----------------|  
|[Strona sieci Web]|Najwyższego poziomu obiekt JSON, który zawiera atrybuty (wersja zapoznawcza).|  
|[Fakt]|Najwyższego poziomu obiekt JSON, który zawiera dane.| 
|[Jednostek|Najwyższego poziomu obiekt JSON, który zawiera szczegóły jednostki.| 

  
### <a name="error"></a>Błąd  
Definiuje błąd, który wystąpił.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kod|Kod błędu, który identyfikuje kategorii błędów. Aby uzyskać listę możliwych kodów, zobacz [kody błędów](#error-codes).|Ciąg|  
|<a name="error-message" />Komunikat|Opis błędu.|Ciąg|  
|<a name="error-moredetails" />moreDetails|Opis, który zawiera dodatkowe informacje o tym błędzie.|Ciąg|  
|<a name="error-parameter" />Parametr|Parametr zapytania w żądaniu, które spowodowały błąd.|Ciąg|  
|<a name="error-subcode" />Podrzędnego|Kod błędu, który identyfikuje błąd. Na przykład jeśli `code` jest InvalidRequest, `subCode` może być ParameterInvalid lub ParameterInvalidValue. |Ciąg|  
|<a name="error-value" />Wartość|Wartość parametru zapytania, która nie jest prawidłowa.|Ciąg|  
  

### <a name="errorresponse"></a>ErrorResponse  
Obiekt najwyższego poziomu, który zawiera odpowiedź, gdy żądanie zakończy się niepowodzeniem.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_typ|Wskazówka typu.|Ciąg|  
|<a name="errors" />Błędy|Lista błędów, które opisują przyczyny niepowodzenia żądania.|[Error](#error)|  

  
  
### <a name="license"></a>Licencja  
Definiuje licencji w ramach której można użyć typu text lub zdjęć.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|name|Nazwa licencji.|Ciąg|  
|url|Adres URL witryny sieci Web, gdzie użytkownik może uzyskać więcej informacji o licencji.<br /><br /> Użyj nazwy i adresu URL, aby utworzyć hiperłącze.|Ciąg|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definiuje umownych regułę autorstwa licencji.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_typ|Wskazówka typu, która jest równa LicenseAttribution.|Ciąg|  
|licencja|Licencja, pod którym zawartość może być używany.|[Licencja](#license)|  
|licenseNotice|Licencja do wyświetlenia obok pola docelowego. Na przykład "tekst w ramach licencji DW przez administratora systemu".<br /><br /> Użyj nazwy i adresu URL w licencji `license` pola, aby utworzyć hiperłącze do witryny sieci Web, która opisuje szczegółowe informacje o licencji. Następnie zastąp nazwę licencji w `licenseNotice` ciąg (na przykład, CC-przez-SA), z hiperłącza właśnie utworzony.|Ciąg|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły muszą być umieszczone w bliskim sąsiedztwie do pola, które dotyczy reguła. Jeśli **true**, zawartość musi być umieszczony w pobliżu. Jeśli **false**, lub nie istnieje w tym polu, zawartość można umieścić uznania obiektu wywołującego.|Wartość logiczna|  
|targetPropertyName|Nazwa pola, które dotyczy reguła.|Ciąg|  
  

### <a name="link"></a>Link  
Określa składniki hiperłącza.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_typ|Wskazówka typu.|Ciąg|  
|tekst|Tekst wyświetlany.|Ciąg|  
|url|ADRES URL. Użyj adresu URL i wyświetlania tekstu, aby utworzyć hiperłącze.|Ciąg|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definiuje umownych regułę autorstwa łącza.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_typ|Wskazówka typu, która jest równa LinkAttribution.|Ciąg|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły muszą być umieszczone w bliskim sąsiedztwie do pola, które dotyczy reguła. Jeśli **true**, zawartość musi być umieszczony w pobliżu. Jeśli **false**, lub nie istnieje w tym polu, zawartość można umieścić uznania obiektu wywołującego.|Wartość logiczna|  
|targetPropertyName|Nazwa pola, które dotyczy reguła.<br /><br /> Jeśli element docelowy nie zostanie określony, uznanie autorstwa stosuje do jednostki jako całość i powinien być wyświetlany natychmiast po przedstawieniu jednostki. Jeśli istnieje wiele tekst i link reguł autorstwa, których nie określono elementu docelowego, połącz je i wyświetlaj je za pomocą "dane:" etykiety. Na przykład "dane z < Nazwa1 dostawcy\> &#124; < name2 dostawcy\>".|Ciąg|  
|tekst|Tekst: uznanie autorstwa.|Ciąg|  
|url|Adres URL witryny sieci Web dostawcy. Użyj `text` i adres URL, aby utworzyć hiperłącza.|Ciąg|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definiuje umownych regułę autorstwa nośnika.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_typ|Wskazówka typu, która jest równa MediaAttribution.|Ciąg|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły muszą być umieszczone w bliskim sąsiedztwie do pola, które dotyczy reguła. Jeśli **true**, zawartość musi być umieszczony w pobliżu. Jeśli **false**, lub nie istnieje w tym polu, zawartość można umieścić uznania obiektu wywołującego.|Wartość logiczna|  
|targetPropertyName|Nazwa pola, które dotyczy reguła.|Ciąg|  
|url|Adres URL, który umożliwia tworzenie hiperłącza zawartości multimedialnej. Na przykład jeśli element docelowy jest obrazem, można użyć adresu URL Aby utworzyć obraz możesz klikać.|Ciąg|  
  
  
  
### <a name="organization"></a>Organizacja  
Określa wydawcę.  
  
Należy pamiętać, że wydawca może zapewnić ich nazwy, ich witryny sieci Web lub obu tych.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|name|Nazwa wydawcy.|Ciąg|  
|url|Adres URL witryny sieci Web wydawcy.<br /><br /> Należy pamiętać, wydawca nie mogą zawierać witryny sieci Web.|Ciąg|  
  
  

### <a name="webpage"></a>Strony sieci Web  
Określa informacje o stronie sieci Web w wersji zapoznawczej.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|
|name|Tytuł strony, niekoniecznie tytuł HTML|Ciąg|
|url|Adres URL, który faktycznie został przeszukane (żądanie może wykonano przekierowania)|Ciąg|  
|description|Krótki opis strony i zawartości|Ciąg|  
|isFamilyFriendly|Najdokładniejszych dla elementów w indeksie sieci web; Odczyty w czasie rzeczywistym, czy to wykrywanie wyłącznie na podstawie adresu URL i nie zawartości strony|wartość logiczna|
|primaryImageOfPage/contentUrl|Adres URL, który jest reprezentatywny obraz do uwzględnienia w wersji zapoznawczej|Ciąg| 
  
  
### <a name="querycontext"></a>QueryContext  
Definiuje kontekst zapytania, które Bing użyta dla żądania.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Wartość logiczna wskazująca, czy określona kwerenda ma dorosłych. Wartość jest **true** Jeśli kwerenda ma dorosłych; w przeciwnym razie **false**.|Wartość logiczna|  
|alterationOverrideQuery|Ciąg zapytania do użycia, aby wymusić Bing w celu użycia oryginalny ciąg. Na przykład, jeśli ciąg zapytania jest *saling downwind*, zastąpienie ciągu zapytania będą *+ saling downwind*. Pamiętaj, aby zakodować ciąg zapytania, co skutkuje *% 2Bsaling + downwind*.<br /><br /> To pole jest uwzględniane tylko wtedy, gdy oryginalny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|alteredQuery|Ciąg zapytania używane przez usługę Bing do wykonania zapytania. Usługa Bing używa ciągu zapytania zmieniony, jeśli oryginalny ciąg zapytania zawiera błędy pisowni. Na przykład, jeśli ciąg zapytania jest `saling downwind`, ciąg zapytania zmienionego będzie `sailing downwind`.<br /><br /> To pole jest uwzględniane tylko wtedy, gdy oryginalny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|askUserForLocation|Wartość logiczna wskazująca, czy Bing wymaga lokalizacji użytkownika, aby zapewnić dokładne wyniki. Jeśli w określonej lokalizacji użytkownika przy użyciu [ClientIP-X-MSEdge](#clientip) i [X wyszukiwania lokalizacji](#location) nagłówków, możesz zignorować to pole.<br /><br /> Dla zapytań pamiętać lokalizacji, takich jak "Bieżąca pogoda" lub "restauracje w pobliżu" wymagających lokalizacji użytkownika, aby zapewnić dokładne wyniki, to pole jest ustawione **true**.<br /><br /> Dla lokalizacji pamiętać zapytań, które obejmują lokalizację (na przykład "Seattle o pogodzie"), to pole jest ustawione **false**. To pole jest również ustawiona na **false** dla zapytań, które nie są lokalizacji, takich jak "najlepiej sprzedających".|Wartość logiczna|  
|originalQuery|Ciąg zapytania określony w żądaniu.|Ciąg|  

### <a name="identifiable"></a>Do zidentyfikowania
|Name (Nazwa)|Wartość|Typ|  
|-------------|-----------------|----------|
|id|Identyfikator zasobu|Ciąg|
 
### <a name="rankinggroup"></a>RankingGroup
Definiuje grupy w wynikach wyszukiwania, takie jak mainline.
|Name (Nazwa)|Wartość|Typ|  
|-------------|-----------------|----------|
|pozycje|Lista wyników wyszukiwania do wyświetlenia w grupie.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definiuje element wyników wyszukiwania, aby wyświetlić.
|Name (Nazwa)|Wartość|Typ|  
|-------------|-----------------|----------|
|resultIndex|Liczony od zera indeks elementu w odpowiedzi na pytanie do wyświetlenia. Jeśli element nie zawiera tego pola, można wyświetlić wszystkie elementy w odpowiedzi na pytanie. Na przykład wyświetlić wszystkie artykuły w odpowiedzi na wiadomości.|Liczba całkowita|
|answerType|Odpowiedź, który zawiera element, aby wyświetlić. Na przykład wiadomości.<br /><br />Aby znaleźć odpowiedzi w obiekcie SearchResponse, należy użyć typu. Typ jest nazwa pola SearchResponse.<br /><br /> Jednak używać typu odpowiedzi, tylko wtedy, gdy ten obiekt zawiera pola wartości; w przeciwnym razie go zignorować.|Ciąg|
|textualIndex|Indeks odpowiedzi w textualAnswers do wyświetlenia.| Liczba całkowita bez znaku|
|wartość|Identyfikatora, który identyfikuje odpowiedzi, aby wyświetlić lub element odpowiedź do wyświetlenia. Jeśli identyfikator identyfikuje odpowiedzi, wyświetlanie wszystkich elementów w odpowiedzi.|Do zidentyfikowania|

### <a name="rankingresponse"></a>RankingResponse  
Określa, gdzie na wyszukiwanie zawartości strony wyników powinny zostać umieszczone i w jakiej kolejności.  
  
|Name (Nazwa)|Wartość|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Wyniki wyszukiwania w celu wyświetlenia w ramach linii głównej.|  
|<a name="ranking-pole" />pole|Wyniki wyszukiwania, które powinny otrzymać najbardziej widoczne traktowania (na przykład wyświetlane powyżej linii głównej i paska bocznego).|  
|<a name="ranking-sidebar" />pasek boczny|Wyniki wyszukiwania, aby wyświetlić na pasku bocznym.| 


### <a name="searchresponse"></a>SearchResponse  
Definiuje obiekt najwyższego poziomu, który zawiera odpowiedź, gdy żądanie zakończy się pomyślnie.  
  
Należy pamiętać, że jeśli usługa podejrzewa "odmowa usługi", żądanie zakończy się pomyślnie (kod stanu HTTP jest 200 OK); jednak treści odpowiedzi jest pusta.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_typ|Wskazówka typu jest ustawiona na SearchResponse.|Ciąg|  
|Strony sieci Web|Obiekt JSON, który definiuje korzystania z wersji zapoznawczej|ciąg|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definiuje umownych regułę autorstwa zwykły tekst.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_typ|Wskazówka typu, która jest równa TextAttribution.|Ciąg|  
|tekst|Tekst: uznanie autorstwa.<br /><br /> Tekst: uznanie autorstwa dotyczy jednostki jako całość i powinien zostać wyświetlony od razu po przedstawieniu jednostki. Jeśli istnieje wiele tekstu lub linków reguł autorstwa, których nie określono elementu docelowego, połącz je i wyświetlaj je za pomocą "dane:" etykiety.|Ciąg| 


## <a name="error-codes"></a>Kody błędów

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|200|Powodzenie.|  
|400|To jeden z parametrów zapytania, lub jest on nieprawidłowy.|  
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
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowane|Wyszukiwarka Bing zwróci InvalidRequest zawsze wtedy, gdy dowolnej części żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>W przypadku ParameterMissing lub ParameterInvalidValue błędu 400 jest kod stanu HTTP.<br/><br/>Jeśli używasz protokołu HTTP zamiast HTTPS, Wyszukiwarka Bing zwróci HttpNotAllowed i jest kod stanu HTTP 410.
|RateLimitExceeded|Nie kodów podrzędnych|Wyszukiwarka Bing zwróci RateLimitExceeded zawsze wtedy, gdy przekracza z zapytań na sekundę (QPS) lub zapytania na miesiąc (QPM) limitu przydziału.<br/><br/>Po przekroczeniu liczby zapytań na Sekundę, Wyszukiwarka Bing zwróci kod stanu HTTP 429, a Jeśli przekroczysz QPM, Wyszukiwarka Bing zwróci 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Wyszukiwarka Bing zwróci InvalidAuthorization, kiedy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje w przypadku określenia więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli ten błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wyszukiwarka Bing zwróci InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Może to wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli ten błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

## <a name="next-steps"></a>Kolejne kroki
- [Przewodnik Szybki Start języka C#](c-sharp-quickstart.md)
- [Przewodnik Szybki Start języka Java](java-quickstart.md)
- [Przewodnik Szybki Start węzła](node-quickstart.md)
- [Przewodnik Szybki Start języka Python](python-quickstart.md)

