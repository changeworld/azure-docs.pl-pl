---
title: Odwołanie w wersji 7 interfejs API wyszukiwania Bing w lokalnych firmach | Dokumentacja firmy Microsoft
description: W tym artykule opisano elementy programowania lokalnych firm interfejsu API wyszukiwania Bing.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: bc38b4457179c11f9d6b2656aacb8aa66848c444
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581041"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Odwołanie do lokalnych firm interfejsu API wyszukiwania Bing w wersji 7

Interfejsu API wyszukiwania w lokalnych firm wysyła zapytanie wyszukiwania do usługi Bing w celu uzyskania wyników, które zawierają restauracje, hotele lub innych lokalnych firm. Dla miejsc zapytanie można określić nazwę lokalnych firmach lub kategorii (na przykład, restauracje w pobliżu). Wyniki dotyczące jednostek to między innymi osoby, miejsca i rzeczy. Miejsce, w tym kontekście jest jednostek biznesowych, Stany, kraje, itp.  

Ta sekcja zawiera szczegółowe informacje techniczne dotyczące obiektów odpowiedzi i parametrów zapytania i nagłówków, które mają wpływ na wyniki wyszukiwania. Przykłady pokazujące, jak żądań, zobacz [lokalnego wyszukiwania firm C# Szybki Start](quickstarts/local-quickstart.md) lub [Szybki Start lokalnych firm wyszukiwania Java](quickstarts/local-search-java-quickstart.md). 
  
Aby dowiedzieć się, nagłówki, które powinny zawierać żądań, zobacz [nagłówki](#headers).  
  
Aby uzyskać informacji na temat parametrów zapytania, które powinny zawierać żądań, zobacz [parametry zapytania](#query-parameters).  
  
Aby uzyskać informacje o formacie JSON obiektów, że odpowiedź zawiera, zobacz [obiekty odpowiedzi](#response-objects).

Aby uzyskać informacji o dozwolone użycie i wyświetlania wyników, zobacz [użycia i wyświetlają wymagania dotyczące](use-display-requirements.md).


  
## <a name="endpoint"></a>Endpoint  
Aby zażądać wyników lokalnych firmach, Wyślij żądanie Pobierz do: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
Żądanie musi używać protokołu HTTPS.  
  
> [!NOTE]
> Maksymalna długość adresu URL to 2048 znaków. Aby upewnić się, że Twoje długość adresu URL nie przekracza limit, maksymalna długość parametry zapytania powinny być mniejszy niż 1500 znaków. Jeśli adres URL przekracza 2048 znaków, serwer zwraca błąd 404 — Nie odnaleziono.  
  
  
## <a name="headers"></a>Nagłówki  
Dostępne są następujące nagłówki, które mogą obejmować żądania i odpowiedzi.  
  
|Nagłówek|Opis|  
|------------|-----------------|  
|Zaakceptuj|Opcjonalny nagłówek żądania.<br /><br /> Domyślny typ nośnika jest application/json. Aby określić, czy odpowiedź korzystać [JSON-LD](https://json-ld.org/), ustaw nagłówek Accept application/ld + json.|  
|<a name="acceptlanguage" />Accept-Language|Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana przecinkami lista języków dla ciągów interfejsu użytkownika. Lista jest zorganizowana w malejącym porządku preferencji. Aby uzyskać więcej informacji, łącznie z oczekiwanym formatem, zobacz [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i parametr zapytania [setLang](#setlang) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Jeśli ustawisz ten nagłówek, należy także określić parametr zapytania DW. Aby ustalić rynek, dla którego mają zostać zwrócone wyniki, usługa Bing używa pierwszego obsługiwanego języka znalezionego na liście i łączy go z wartością parametru `cc`. Jeśli lista nie zawiera obsługiwanego języka, usługa Bing znajduje najbliższy język i rynek, które obsługują żądanie, lub używa rynku zagregowanego bądź domyślnego. Aby sprawdzić, jakiego rynku użyła usługa Bing, zobacz nagłówek BingAPIs-Market.<br /><br /> Używaj tego nagłówka i parametru zapytania `cc` tylko wtedy, gdy określasz wiele języków. W przeciwnym razie użyj parametrów zapytania [mkt](#mkt) i [setLang](#setlang).<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.|  
|<a name="market" />BingAPIs-Market|Nagłówek odpowiedzi.<br /><br /> Rynek używany przez żądanie. Format jest następujący \<kod_języka\>-\<kod_kraju\>. Na przykład en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwyć ten identyfikator. Jeśli nie możesz określić i rozwiązać problemu, dołącz ten identyfikator wraz z innymi informacjami, które podasz zespołowi pomocy technicznej.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania.<br /><br /> Klucz subskrypcji otrzymany podczas tworzenia konta dla tej usługi w usługach [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Opcjonalny nagłówek żądania<br /><br /> Usługa Bing domyślnie zwraca zawartość buforowaną, jeśli jest dostępna. Aby zapobiec zwracaniu zawartości buforowanej przez usługę Bing, dla nagłówka Pragma ustaw wartość no-cache (na przykład Pragma: no-cache).
|<a name="useragent" />User-Agent|Opcjonalny nagłówek żądania.<br /><br /> Agent użytkownika, od którego pochodzi żądanie. Usługa Bing korzysta z agenta użytkownika, aby zapewnić zoptymalizowane środowisko obsługi dla użytkowników urządzeń przenośnych. Mimo że ten nagłówek jest opcjonalny, zachęcamy, aby go zawsze określać.<br /><br /> Nagłówek user-agent powinien być takim samym ciągiem, jaki wysyła każda powszechnie używana przeglądarka. Aby uzyskać informacje na temat agentów użytkownika, zobacz [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągów nagłówka user-agent.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodne; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; jak Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 jak Mac OS X) AppleWebKit/536.26 (KHTML; jak Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) jak Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 jak Mac OS X) AppleWebKit/537.51.1 (KHTML, jak Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Opcjonalny nagłówek żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego nagłówka, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Usługa Bing często testuje nowe funkcje i ulepszenia i używa identyfikatora klienta jako klucza do przypisywania ruchu dla różnych pakietów testowych. Jeśli nie będziesz używać tego samego identyfikatora klienta dla użytkownika w wielu żądaniach, usługa Bing może przypisać użytkownika do różnych, konfliktowych pakietów testowych. Przypisanie do wielu konfliktowych pakietów testowych może prowadzić do niespójnego środowiska użytkownika. Jeśli na przykład drugie żądanie ma przypisany inny pakiet testowy, niż pierwsze, środowisko obsługi może być nieoczekiwane. Ponadto usługa Bing może używać identyfikatora klienta, aby dopasować wyniki internetowe do historii wyszukiwania tego identyfikatora, zapewniając użytkownikowi bogatsze środowisko obsługi.<br /><br /> Usługa Bing używa także tego nagłówka, aby ulepszyć pozycjonowanie wyników, analizując aktywność generowaną przez identyfikator klienta. Ulepszenia istotności pomagają zwiększyć jakość wyników dostarczanych przez interfejsy API usługi Bing, co w rezultacie daje wyższą częstotliwość kliknięć dla użytkownika interfejsu API.<br /><br /> **WAŻNE:** Mimo że jest to opcjonalne, należy rozważyć tego pliku nagłówkowego wymagane. Utrwalenie identyfikatora klienta dla wielu żądań dla połączenia tego samego użytkownika końcowego i urządzenia umożliwia (1) zapewnienie użytkownikowi interfejsu API spójnego środowiska obsługi oraz (2) uzyskanie wyższej częstotliwości kliknięć dzięki lepszej jakość wyników z interfejsów API usługi Bing.<br /><br /> Poniżej przedstawiono podstawowe reguły użycia, które mają zastosowanie do tego nagłówka.<br /><ul><li>Każdy użytkownik, który korzysta z Twojej aplikacji na urządzeniu, musi mieć unikatowy identyfikator klienta wygenerowany przez usługę Bing.<br /><br/>Jeśli nie uwzględnisz tego nagłówka w żądaniu, usługa Bing wygeneruje identyfikator i zwróci go w nagłówku odpowiedzi X-MSEdge-ClientID. Jedyną sytuacją, w której NIE należy uwzględniać tego nagłówka w żądaniu, jest pierwsze użycie Twojej aplikacji przez danego użytkownika na danym urządzeniu.<br /><br/></li><li>Używaj identyfikatora klienta dla każdego żądania interfejsu API usługi Bing, które Twoja aplikacja wykonuje dla tego użytkownika na danym urządzeniu.<br /><br/></li><li>**UWAGI:** Należy się upewnić, że tego Identyfikatora klienta nie jest możliwym do żadnych informacji o koncie użytkownika poświadczalne.</li><br/><li>Utrwal identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, użyj trwałego pliku cookie HTTP, aby mieć pewność, że dany identyfikator będzie używany we wszystkich sesjach. Nie należy używać plików cookie sesji. W przypadku innych aplikacji, takich jak aplikacje mobilne, użyj magazynu trwałego urządzenia, aby utrwalić identyfikator.<br /><br/>Następnym razem, gdy użytkownik będzie używać Twojej aplikacji na tym urządzeniu, uzyskaj utrwalony identyfikator klienta.</li></ul><br /> **UWAGA:** Odpowiedzi Bing może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera ten nagłówek, przechwyć identyfikator klienta i używaj go dla wszystkich kolejnych żądań usługi Bing dla tego użytkownika na tym urządzeniu.<br /><br /> **UWAGA:** Jeśli dodasz X MSEdge ClientID, nie może zawierać pliki cookie w żądaniu.|  
|<a name="clientip" />X-MSEdge-ClientIP|Opcjonalny nagłówek żądania.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> **UWAGA:** Mimo że jest to opcjonalne, zachęcamy do zawsze określać tego pliku nagłówkowego i Nagłówek X-Search-lokalizacji.<br /><br /> Nie należy zaciemniać adres (na przykład zamieniając ostatni oktet na 0). Zaciemnianie adresu powoduje, że ustalona lokalizacja nie jest nawet przybliżona do rzeczywistej lokalizacji urządzenia, przez co usługa Bing może zwracać błędne wyniki.|  
|<a name="location" />X-Search-Location|Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, które opisują geograficzną lokalizację klienta. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania i aby zwracać odpowiednią zawartość lokalną. Parę klucz/wartość określ w formacie \<klucz\>:\<wartość\>. Poniżej przedstawiono klucze, które służą do określania lokalizacji użytkownika.<br /><br /><ul><li>lat&mdash;szerokości geograficznej lokalizacji klienta, w stopniach. Szerokość geograficzna musi być większa niż lub równa -90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują południową szerokość geograficzną, a wartości dodatnie wskazują północną szerokość geograficzną.<br /><br /></li><li>długi&mdash;długości geograficznej lokalizacji klienta, w stopniach. Długość geograficzna musi być większa niż lub równa -180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują zachodnią długość geograficzną, a wartości dodatnie wskazują wschodnią długość geograficzną.<br /><br /></li><li>ponownie&mdash; promień, w metrach, który określa dokładność pozioma współrzędne. Przekaż wartość zwróconą przez usługę lokalizacji urządzenia. Typowe wartości mogą wynosić 22 m dla sieci GPS/Wi-Fi, 380 m dla triangulacji wieży sieci komórkowej i 18 000 m dla wstecznego wyszukiwania adresu IP.<br /><br /></li><li>TS&mdash; sygnatura czasowa UTC UNIX kiedy klient nie był w lokalizacji. (Sygnatura czasowa systemu UNIX to liczba sekund od 1 stycznia 1970 r.).<br /><br /></li><li>head&mdash;Opcjonalne. Względny kierunek przemieszczania się klienta. Określ kierunek ruchu jako liczbę stopni z zakresu od 0 do 360, zliczanych zgodnie z ruchem wskazówek zegara względem prawdziwej północy. Określ ten klucz tylko wtedy, gdy wartość klucza `sp` jest różna od zera.<br /><br /></li><li>SP&mdash; prędkości poziome (szybkość) w metrach na sekundę, które są przesyłane na urządzeniu klienckim.<br /><br /></li><li>ALT&mdash; wysokość urządzenia klienta, w metrach.<br /><br /></li><li>are&mdash;Opcjonalne. Promień, w metrach, który określa pionową dokładność współrzędnych. Domyślnie promieniu 50 km. Określ ten klucz tylko wtedy, gdy wartość klucza `alt` także została określona.<br /><br /></li></ul> **UWAGA:** Mimo że te klucze są opcjonalne, więcej informacji, które podasz, tym bardziej dokładne wyniki lokalizacji są.<br /><br /> **UWAGA:** Zachęcamy do zawsze określić lokalizację geograficzną użytkownika. Podanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedla precyzyjnie fizycznej lokalizacji użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki należy uwzględnić ten nagłówek i nagłówek X-MSEdge-ClientIP, a co najmniej należy uwzględnić ten nagłówek.|

> [!NOTE] 
> Pamiętaj, że Warunki użytkowania wymagają zgodności ze wszystkimi obowiązującymi przepisami prawa, w tym dotyczącymi korzystania z tych nagłówków. W niektórych systemach prawnych, na przykład w Europie, wymagane jest uzyskania zgody użytkownika przed umieszczeniem narzędzi śledzących na urządzeniu użytkownika.
  

## <a name="query-parameters"></a>Parametry zapytania  
Żądanie może obejmować następujące parametry zapytania. Zobacz wymaganej kolumny dla wymaganych parametrów. Należy najpierw, adres URL zakodować parametry zapytania.  
  
  
|Name (Nazwa)|Wartość|Type|Wymagane|  
|----------|-----------|----------|--------------|
|<a name="count" />Liczba|Liczba wyników do zwrócenia, począwszy od indeksu określonego przez `offset` parametru.|String|Nie|   
|<a name="localCategories" />localCategories|Lista opcji, które definiują wyszukiwania według kategorii biznesowych.  Zobacz [wyszukiwania lokalnych firm kategorii](local-categories.md)|String|Nie|  
|<a name="mkt" />mkt|Rynek, z którego pochodzą wyniki. <br /><br />Aby uzyskać listę możliwych wartości na rynku Zobacz kodów na rynku.<br /><br /> **UWAGA:** Interfejsu API wyszukiwania w lokalnych firm aktualnie obsługuje tylko en-us rynku i język.<br /><br />|String|Yes|
|<a name="offset"/>offset|Indeks, aby uruchomić wyników określony przez `count` parametru.|Liczba całkowita|Nie|  
|<a name="query" />q|Termin wyszukiwania przez użytkownika.|String|Nie|  
|<a name="responseformat" />responseFormat|Typ multimediów do użycia dla odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje o formacie JSON obiektów, że odpowiedź zawiera, zobacz [obiekty odpowiedzi](#response-objects).<br /><br />  Jeśli określisz JsonLd, treść odpowiedzi zawiera obiekty JSON-LD, zawierające wyniki wyszukiwania. Aby uzyskać informacji na temat JSON LD, zobacz [JSON-LD](https://json-ld.org/).|String|Nie|  
|<a name="safesearch" />safeSearch|Filtr używany do odfiltrowania zawartości dla dorosłych. Poniżej przedstawiono możliwe wartości filtru bez uwzględniania wielkości liter.<br /><ul><li>Wyłącz&mdash;zwracają stron internetowych z treści dla dorosłych tekst, obrazy lub filmy wideo.<br /><br/></li><li>Umiarkowany&mdash;zwracają stron internetowych z treści dla dorosłych tekstu, ale niepełnoletni obrazy lub filmy wideo.<br /><br/></li><li>Ścisłe&mdash;nie zwracają stron internetowych z treści dla dorosłych tekst, obrazy lub filmy wideo.</li></ul><br /> Wartość domyślna to Moderate.<br /><br /> **UWAGA:** Jeśli żądanie pochodzi z rynku zasad treści dla dorosłych tego Bing wymaga `safeSearch` jest ustawiony na Strict, ignoruje Bing `safeSearch` wartość i używa Strict.<br/><br/>**UWAGA:** Jeśli używasz `site:` — operator zapytań, istnieje prawdopodobieństwo, że odpowiedź może zawierać treści dla dorosłych niezależnie od tego, co `safeSearch` ustawiono parametr zapytania. Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych. |String|Nie|  
|<a name="setlang" />setLang|Język ciągów interfejsu użytkownika. Określ język przy użyciu 2-literowego kodu języka w standardzie ISO 639-1. Na przykład kod języka dla języka angielskiego to EN. Wartość domyślna to EN (język angielski).<br /><br /> Mimo, że jest to opcjonalne, należy zawsze określić język. Na ogół dla parametru `setLang` ustawia się język określony przez parametr `mkt`, chyba że użytkownik chce, aby ciągi interfejsu użytkownika były wyświetlane w innym języku.<br /><br /> Ten parametr i nagłówek [Accept-Language](#acceptlanguage) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Ponadto wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.|String|Nie| 


## <a name="response-objects"></a>Obiekty odpowiedzi  
Poniżej przedstawiono obiekty odpowiedzi JSON, które mogą obejmować odpowiedzi. Jeśli żądanie zakończy się powodzeniem, jest obiektem najwyższego poziomu w odpowiedzi [SearchResponse](#searchresponse) obiektu. Jeśli żądanie zakończy się niepowodzeniem, jest obiektem najwyższego poziomu [ErrorResponse](#errorresponse) obiektu.


|Object|Opis|  
|------------|-----------------|  
|[Miejsce](#place)|Definiuje informacje o lokalnych firmach, takich jak restauracji lub hotelu.|  

  
### <a name="error"></a>Błąd  
Definiuje błąd, który wystąpił.  
  
|Element|Opis|Type|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kod|Kod błędu, który identyfikuje kategorii błędów. Aby uzyskać listę możliwych kodów, zobacz [kody błędów](#error-codes).|String|  
|<a name="error-message" />Komunikat|Opis błędu.|String|  
|<a name="error-moredetails" />moreDetails|Opis, który zawiera dodatkowe informacje o tym błędzie.|String|  
|<a name="error-parameter" />Parametr|Parametr zapytania w żądaniu, które spowodowały błąd.|String|  
|<a name="error-subcode" />subCode|Kod błędu, który identyfikuje błąd. Na przykład jeśli `code` jest InvalidRequest, `subCode` może być ParameterInvalid lub ParameterInvalidValue. |String|  
|<a name="error-value" />Wartość|Wartość parametru zapytania, która nie jest prawidłowa.|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
Obiekt najwyższego poziomu, który zawiera odpowiedź, gdy żądanie zakończy się niepowodzeniem.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|_type|Wskazówka typu.|String|  
|<a name="errors" />Błędy|Lista błędów, które opisują przyczyny niepowodzenia żądania.|[Błąd](#error)]|  

  
  
### <a name="license"></a>Licencja  
Definiuje licencji w ramach której można użyć typu text lub zdjęć.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|name|Nazwa licencji.|String|  
|url|Adres URL witryny sieci Web, gdzie użytkownik może uzyskać więcej informacji o licencji.<br /><br /> Użyj nazwy i adresu URL, aby utworzyć hiperłącze.|String|  


### <a name="link"></a>Link  
Określa składniki hiperłącza.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|_type|Wskazówka typu.|String|  
|tekst|Tekst wyświetlany.|String|  
|url|ADRES URL. Użyj adresu URL i wyświetlania tekstu, aby utworzyć hiperłącze.|String|  
  


  
### <a name="organization"></a>Organizacja  
Określa wydawcę.  
  
Należy pamiętać, że wydawca może zapewnić ich nazwy, ich witryny sieci Web lub obu tych.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|name|Nazwa wydawcy.|String|  
|url|Adres URL witryny sieci Web wydawcy.<br /><br /> Należy pamiętać, wydawca nie mogą zawierać witryny sieci Web.|String|  
  
  

### <a name="place"></a>Miejsce  
Definiuje informacje o lokalnych firmach, takich jak restauracji lub hotelu.  
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|_type|Wskazówka typ może być ustawiona na jedną z następujących czynności:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restauracja</ul><li>|String|  
|adres|Adres pocztowy, z którym znajduje się jednostka.|PostalAddress|  
|entityPresentationInfo|Dodatkowe informacje o jednostce, takiej jak wskazówek, które można użyć w celu określenia typu jednostki. Na przykład czy jest to restauracji lub hotelu. `entityScenario` Pole jest ustawione na element listy.|entityPresentationInfo|  
|name|Nazwa jednostki.|String|  
|Telefon|Numer telefonu jednostki.|String|  
|url|Adres URL witryny sieci Web jednostki.<br /><br /> Użyj tego adresu URL wraz z nazwą podmiotu, aby utworzyć hiperłącze, które po kliknięciu powoduje otwarcie witryny sieci Web jednostki.|String|  
|webSearchUrl|Adres URL do wyniku wyszukiwania Bing tego miejsca.|String| 
  
  
### <a name="querycontext"></a>QueryContext  
Definiuje kontekst zapytania, które Bing użyta dla żądania.  
  
|Element|Opis|Type|  
|-------------|-----------------|----------|  
|adultIntent|Wartość logiczna wskazująca, czy określona kwerenda ma dorosłych. Wartość jest **true** Jeśli kwerenda ma dorosłych; w przeciwnym razie **false**.|Boolean|  
|alterationOverrideQuery|Ciąg zapytania do użycia, aby wymusić Bing w celu użycia oryginalny ciąg. Na przykład, jeśli ciąg zapytania jest *saling downwind*, zastąpienie ciągu zapytania będą *+ saling downwind*. Pamiętaj, aby zakodować ciąg zapytania, co skutkuje *% 2Bsaling + downwind*.<br /><br /> To pole jest uwzględniane tylko wtedy, gdy oryginalny ciąg zapytania zawiera błąd pisowni.|String|  
|alteredQuery|Ciąg zapytania używane przez usługę Bing do wykonania zapytania. Usługa Bing używa ciągu zapytania zmieniony, jeśli oryginalny ciąg zapytania zawiera błędy pisowni. Na przykład, jeśli ciąg zapytania jest `saling downwind`, ciąg zapytania zmienionego będzie `sailing downwind`.<br /><br /> To pole jest uwzględniane tylko wtedy, gdy oryginalny ciąg zapytania zawiera błąd pisowni.|String|  
|askUserForLocation|Wartość logiczna wskazująca, czy Bing wymaga lokalizacji użytkownika, aby zapewnić dokładne wyniki. Jeśli w określonej lokalizacji użytkownika przy użyciu [ClientIP-X-MSEdge](#clientip) i [X wyszukiwania lokalizacji](#location) nagłówków, możesz zignorować to pole.<br /><br /> Dla zapytań pamiętać lokalizacji, takich jak "Bieżąca pogoda" lub "restauracje w pobliżu" wymagających lokalizacji użytkownika, aby zapewnić dokładne wyniki, to pole jest ustawione **true**.<br /><br /> Dla lokalizacji pamiętać zapytań, które obejmują lokalizację (na przykład "Seattle o pogodzie"), to pole jest ustawione **false**. To pole jest również ustawiona na **false** dla zapytań, które nie są lokalizacji, takich jak "najlepiej sprzedających".|Boolean|  
|originalQuery|Ciąg zapytania określony w żądaniu.|String|  

### <a name="identifiable"></a>Do zidentyfikowania

|Name (Nazwa)|Wartość|Type|  
|-------------|-----------------|----------|
|id|Identyfikator zasobu|String|
 
### <a name="rankinggroup"></a>RankingGroup
Definiuje grupy w wynikach wyszukiwania, takie jak mainline.

|Name (Nazwa)|Wartość|Type|  
|-------------|-----------------|----------|
|pozycje|Lista wyników wyszukiwania do wyświetlenia w grupie.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definiuje element wyników wyszukiwania, aby wyświetlić.

|Name (Nazwa)|Wartość|Type|  
|-------------|-----------------|----------|
|resultIndex|Liczony od zera indeks elementu w odpowiedzi na pytanie do wyświetlenia. Jeśli element nie zawiera tego pola, można wyświetlić wszystkie elementy w odpowiedzi na pytanie. Na przykład wyświetlić wszystkie artykuły w odpowiedzi na wiadomości.|Liczba całkowita|
|answerType|Odpowiedź, który zawiera element, aby wyświetlić. Na przykład wiadomości.<br /><br />Aby znaleźć odpowiedzi w obiekcie SearchResponse, należy użyć typu. Typ jest nazwa pola SearchResponse.<br /><br /> Jednak używać typu odpowiedzi, tylko wtedy, gdy ten obiekt zawiera pola wartości; w przeciwnym razie go zignorować.|String|
|textualIndex|Indeks odpowiedzi w textualAnswers do wyświetlenia.| Liczba całkowita bez znaku|
|value|Identyfikatora, który identyfikuje odpowiedzi, aby wyświetlić lub element odpowiedź do wyświetlenia. Jeśli identyfikator identyfikuje odpowiedzi, wyświetlanie wszystkich elementów w odpowiedzi.|Do zidentyfikowania|

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
  
|Name (Nazwa)|Wartość|Type|  
|----------|-----------|----------|  
|_type|Wskazówka typu jest ustawiona na SearchResponse.|String|  
|Miejsca|Listę jednostek, które są istotne dla zapytania wyszukiwania.|Obiekt JSON|  
|QueryContext|Obiekt, który zawiera ciąg zapytania, który Bing użyta dla żądania.<br /><br /> Ten obiekt zawiera ciąg zapytania, tak jak zostały wprowadzone przez użytkownika. Może również zawierać ciąg zapytania zmieniony, który Bing używany dla zapytania, jeśli ciąg zapytania zawiera błąd pisowni.|[QueryContext](#querycontext)|  


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
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowany|Wyszukiwarka Bing zwróci InvalidRequest zawsze wtedy, gdy dowolnej części żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>W przypadku ParameterMissing lub ParameterInvalidValue błędu 400 jest kod stanu HTTP.<br/><br/>Jeśli używasz protokołu HTTP zamiast HTTPS, Wyszukiwarka Bing zwróci HttpNotAllowed i jest kod stanu HTTP 410.
|RateLimitExceeded|Nie kodów podrzędnych|Wyszukiwarka Bing zwróci RateLimitExceeded zawsze wtedy, gdy przekracza z zapytań na sekundę (QPS) lub zapytania na miesiąc (QPM) limitu przydziału.<br/><br/>Po przekroczeniu liczby zapytań na Sekundę, Wyszukiwarka Bing zwróci kod stanu HTTP 429, a Jeśli przekroczysz QPM, Wyszukiwarka Bing zwróci 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Wyszukiwarka Bing zwróci InvalidAuthorization, kiedy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje w przypadku określenia więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli ten błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wyszukiwarka Bing zwróci InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Może to wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli ten błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

## <a name="next-steps"></a>Kolejne kroki
- [Lokalne wyszukiwanie firm Szybki Start](quickstarts/local-quickstart.md)
- [Lokalnych firm wyszukiwania Java Szybki Start](quickstarts/local-search-java-quickstart.md)
- [Lokalny węzeł wyszukiwania firm Szybki Start](quickstarts/local-search-node-quickstart.md)
- [Lokalnych firm wyszukiwania Python Szybki Start](quickstarts/local-search-python-quickstart.md)
