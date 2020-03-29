---
title: Odwołanie do interfejsu API wyszukiwania lokalnego firmy Bing w wersji 7
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera szczegółowe informacje techniczne dotyczące obiektów odpowiedzi oraz parametrów i nagłówków zapytań, które mają wpływ na wyniki wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: db764a73aa1bb18ef2fc0f8f6e5ffe8fd60d388c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74075693"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Odwołanie do interfejsu API wyszukiwania lokalnego firmy Bing w wersji 7

Interfejs API wyszukiwania lokalnego firmy wysyła zapytanie wyszukiwania do usługi Bing w celu uzyskania wyników obejmujących restauracje, hotele lub inne lokalne firmy. W przypadku miejsc kwerenda może określić nazwę lokalnej firmy lub kategorii (na przykład restauracje w pobliżu). Wyniki dotyczące jednostek to między innymi osoby, miejsca i rzeczy. W tym kontekście są to podmioty gospodarcze, państwa, kraje/regiony itp.  

Ta sekcja zawiera szczegółowe informacje techniczne dotyczące obiektów odpowiedzi oraz parametry zapytań i nagłówki, które mają wpływ na wyniki wyszukiwania. Aby zapoznać się z przykładami, które pokazują sposób składania żądań, zobacz Szybki [start szybkiego przewodnika wyszukiwania dla firm lokalnych w języku C#](quickstarts/local-quickstart.md) lub [Szybki start w wyszukiwarce lokalnej firmy Java](quickstarts/local-search-java-quickstart.md). 
  
Aby uzyskać informacje o nagłówkach, które żądania powinny zawierać, zobacz [Nagłówki](#headers).  
  
Aby uzyskać informacje o parametrach kwerendy, które powinny zawierać żądania, zobacz [Parametry kwerendy](#query-parameters).  
  
Aby uzyskać informacje o obiektach JSON, które zawiera odpowiedź, zobacz [Response obiektów](#response-objects).

Aby uzyskać informacje o dozwolonym wykorzystaniu i wyświetlaniu wyników, zobacz [Wymagania dotyczące użytkowania i wyświetlania](use-display-requirements.md).


  
## <a name="endpoint"></a>Endpoint  
Aby poprosić o wyniki lokalnych firm, wyślij żądanie GET na: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
Żądanie musi używać protokołu HTTPS.  
  
> [!NOTE]
> Maksymalna długość adresu URL wynosi 2048 znaków. Aby upewnić się, że długość adresu URL nie przekracza limitu, maksymalna długość parametrów zapytania powinna być mniejsza niż 1500 znaków. Jeśli adres URL przekracza 2048 znaków, serwer zwraca 404 Nie znaleziono.  
  
  
## <a name="headers"></a>Nagłówki  
Poniżej znajdują się nagłówki, które żądanie i odpowiedź może zawierać.  
  
|Nagłówek|Opis|  
|------------|-----------------|  
|Zaakceptuj|Opcjonalny nagłówek żądania.<br /><br /> Domyślnym typem nośnika jest application/json. Aby określić, że odpowiedź użyj [JSON-LD,](https://json-ld.org/)ustaw nagłówek Akceptuj na application/ld+json.|  
|<a name="acceptlanguage" />Accept-Language|Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana przecinkami lista języków dla ciągów interfejsu użytkownika. Lista jest zorganizowana w malejącym porządku preferencji. Aby uzyskać więcej informacji, łącznie z oczekiwanym formatem, zobacz [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i parametr zapytania [setLang](#setlang) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Jeśli ustawisz ten nagłówek, musisz także określić parametr zapytania cc. Aby ustalić rynek, dla którego mają zostać zwrócone wyniki, usługa Bing używa pierwszego obsługiwanego języka znalezionego na liście i łączy go z wartością parametru `cc`. Jeśli lista nie zawiera obsługiwanego języka, usługa Bing znajduje najbliższy język i rynek, które obsługują żądanie, lub używa rynku zagregowanego bądź domyślnego. Aby sprawdzić, jakiego rynku użyła usługa Bing, zobacz nagłówek BingAPIs-Market.<br /><br /> Używaj tego nagłówka i parametru zapytania `cc` tylko wtedy, gdy określasz wiele języków. W przeciwnym razie użyj parametrów zapytania [mkt](#mkt) i [setLang](#setlang).<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.|  
|<a name="market" />BingAPIs-Market|Nagłówek odpowiedzi.<br /><br /> Rynek używany przez żądanie. Format jest następujący \<kod_języka\>-\<kod_kraju\>. Na przykład en-US.|  
|<a name="traceid" />BingAPIs-TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwyć ten identyfikator. Jeśli nie możesz określić i rozwiązać problemu, dołącz ten identyfikator wraz z innymi informacjami, które podasz zespołowi pomocy technicznej.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania.<br /><br /> Klucz subskrypcji otrzymany podczas tworzenia konta dla tej usługi w usługach [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Opcjonalny nagłówek żądania<br /><br /> Usługa Bing domyślnie zwraca zawartość buforowaną, jeśli jest dostępna. Aby zapobiec zwracaniu zawartości buforowanej przez usługę Bing, dla nagłówka Pragma ustaw wartość no-cache (na przykład Pragma: no-cache).
|<a name="useragent" />User-Agent|Opcjonalny nagłówek żądania.<br /><br /> Agent użytkownika, od którego pochodzi żądanie. Usługa Bing korzysta z agenta użytkownika, aby zapewnić zoptymalizowane środowisko obsługi dla użytkowników urządzeń przenośnych. Mimo że ten nagłówek jest opcjonalny, zachęcamy, aby go zawsze określać.<br /><br /> Nagłówek user-agent powinien być takim samym ciągiem, jaki wysyła każda powszechnie używana przeglądarka. Aby uzyskać informacje na temat agentów użytkownika, zobacz [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągów nagłówka user-agent.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodne; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; jak Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 jak Mac OS X) AppleWebKit/536.26 (KHTML; jak Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) jak Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 jak Mac OS X) AppleWebKit/537.51.1 (KHTML, jak Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Opcjonalny nagłówek żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego nagłówka, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Usługa Bing często testuje nowe funkcje i ulepszenia i używa identyfikatora klienta jako klucza do przypisywania ruchu dla różnych pakietów testowych. Jeśli nie będziesz używać tego samego identyfikatora klienta dla użytkownika w wielu żądaniach, usługa Bing może przypisać użytkownika do różnych, konfliktowych pakietów testowych. Przypisanie do wielu konfliktowych pakietów testowych może prowadzić do niespójnego środowiska użytkownika. Jeśli na przykład drugie żądanie ma przypisany inny pakiet testowy, niż pierwsze, środowisko obsługi może być nieoczekiwane. Ponadto usługa Bing może używać identyfikatora klienta, aby dopasować wyniki internetowe do historii wyszukiwania tego identyfikatora, zapewniając użytkownikowi bogatsze środowisko obsługi.<br /><br /> Usługa Bing używa także tego nagłówka, aby ulepszyć pozycjonowanie wyników, analizując aktywność generowaną przez identyfikator klienta. Ulepszenia istotności pomagają zwiększyć jakość wyników dostarczanych przez interfejsy API usługi Bing, co w rezultacie daje wyższą częstotliwość kliknięć dla użytkownika interfejsu API.<br /><br /> **WAŻNE:** Mimo że ten nagłówek jest opcjonalny, można go uważać za wymagany. Utrwalenie identyfikatora klienta dla wielu żądań dla połączenia tego samego użytkownika końcowego i urządzenia umożliwia (1) zapewnienie użytkownikowi interfejsu API spójnego środowiska obsługi oraz (2) uzyskanie wyższej częstotliwości kliknięć dzięki lepszej jakość wyników z interfejsów API usługi Bing.<br /><br /> Poniżej przedstawiono podstawowe reguły użycia, które mają zastosowanie do tego nagłówka.<br /><ul><li>Każdy użytkownik, który korzysta z Twojej aplikacji na urządzeniu, musi mieć unikatowy identyfikator klienta wygenerowany przez usługę Bing.<br /><br/>Jeśli nie uwzględnisz tego nagłówka w żądaniu, usługa Bing wygeneruje identyfikator i zwróci go w nagłówku odpowiedzi X-MSEdge-ClientID. Jedyną sytuacją, w której NIE należy uwzględniać tego nagłówka w żądaniu, jest pierwsze użycie Twojej aplikacji przez danego użytkownika na danym urządzeniu.<br /><br/></li><li>Używaj identyfikatora klienta dla każdego żądania interfejsu API usługi Bing, które Twoja aplikacja wykonuje dla tego użytkownika na danym urządzeniu.<br /><br/></li><li>**UWAGA:** Należy upewnić się, że ten identyfikator klienta nie jest powiązany z żadnymi autentycznymi informacjami o koncie użytkownika.</li><br/><li>Utrwal identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, użyj trwałego pliku cookie HTTP, aby mieć pewność, że dany identyfikator będzie używany we wszystkich sesjach. Nie należy używać plików cookie sesji. W przypadku innych aplikacji, takich jak aplikacje mobilne, użyj magazynu trwałego urządzenia, aby utrwalić identyfikator.<br /><br/>Następnym razem, gdy użytkownik będzie używać Twojej aplikacji na tym urządzeniu, uzyskaj utrwalony identyfikator klienta.</li></ul><br /> **UWAGA:** Odpowiedzi usługi Bing mogą zawierać ten nagłówek lub nie. Jeśli odpowiedź zawiera ten nagłówek, przechwyć identyfikator klienta i używaj go dla wszystkich kolejnych żądań usługi Bing dla tego użytkownika na tym urządzeniu.<br /><br /> **UWAGA:** jeśli dodasz nagłówek X-MSEdge-ClientID, nie możesz uwzględnić w żądaniu plików cookie.|  
|<a name="clientip" />X-MSEdge-ClientIP|Opcjonalny nagłówek żądania.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> **UWAGA:** Mimo że ten nagłówek jest opcjonalny, zachęcamy, aby go zawsze określać wraz z nagłówkiem X-Search-Location.<br /><br /> Nie należy zaciemniać adres (na przykład zamieniając ostatni oktet na 0). Zaciemnianie adresu powoduje, że ustalona lokalizacja nie jest nawet przybliżona do rzeczywistej lokalizacji urządzenia, przez co usługa Bing może zwracać błędne wyniki.|  
|<a name="location" />X-Search-Location|Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, które opisują geograficzną lokalizację klienta. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania i aby zwracać odpowiednią zawartość lokalną. Parę klucz/wartość określ w formacie \<klucz\>:\<wartość\>. Poniżej przedstawiono klucze, które służą do określania lokalizacji użytkownika.<br /><br /><ul><li>lat&mdash;Szerokość geograficzna lokalizacji klienta w stopniach. Szerokość geograficzna musi być większa niż lub równa -90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują południową szerokość geograficzną, a wartości dodatnie wskazują północną szerokość geograficzną.<br /><br /></li><li>długa&mdash;długość geograficzna lokalizacji klienta w stopniach. Długość geograficzna musi być większa niż lub równa -180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują zachodnią długość geograficzną, a wartości dodatnie wskazują wschodnią długość geograficzną.<br /><br /></li><li>re&mdash; Promień w metrach, który określa poziomą dokładność współrzędnych. Przekaż wartość zwróconą przez usługę lokalizacji urządzenia. Typowe wartości mogą wynosić 22 m dla sieci GPS/Wi-Fi, 380 m dla triangulacji wieży sieci komórkowej i 18 000 m dla wstecznego wyszukiwania adresu IP.<br /><br /></li><li>ts&mdash; Sygnatura czasowa UTC UNIX, gdy klient był w lokalizacji. (Sygnatura czasowa systemu UNIX to liczba sekund od 1 stycznia 1970 r.).<br /><br /></li><li>head&mdash;Opcjonalne. Względny kierunek przemieszczania się klienta. Określ kierunek ruchu jako liczbę stopni z zakresu od 0 do 360, zliczanych zgodnie z ruchem wskazówek zegara względem prawdziwej północy. Określ ten klucz tylko wtedy, gdy wartość klucza `sp` jest różna od zera.<br /><br /></li><li>sp&mdash; Prędkość pozioma (prędkość), w metrach na sekundę, że urządzenie klienckie podróżuje.<br /><br /></li><li>alt&mdash; Wysokość urządzenia klienckiego w metrach.<br /><br /></li><li>are&mdash;Opcjonalne. Promień, w metrach, który określa pionową dokładność współrzędnych. Promień domyślnie 50 kilometrów. Określ ten klucz tylko wtedy, gdy wartość klucza `alt` także została określona.<br /><br /></li></ul> **UWAGA:** Mimo że te klucze są opcjonalne, im więcej informacji podasz, tym dokładniejsze są wyniki lokalizacji.<br /><br /> **UWAGA:** Zachęcamy do zawsze określania położenia geograficznego użytkownika. Podanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedla precyzyjnie fizycznej lokalizacji użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki należy uwzględnić ten nagłówek i nagłówek X-MSEdge-ClientIP, a co najmniej należy uwzględnić ten nagłówek.|

> [!NOTE] 
> Pamiętaj, że Warunki użytkowania wymagają zgodności ze wszystkimi obowiązującymi przepisami prawa, w tym dotyczącymi korzystania z tych nagłówków. W niektórych systemach prawnych, na przykład w Europie, wymagane jest uzyskania zgody użytkownika przed umieszczeniem narzędzi śledzących na urządzeniu użytkownika.
  

## <a name="query-parameters"></a>Parametry zapytania  
Żądanie może zawierać następujące parametry kwerendy. Aby uzyskać wymagane parametry, zobacz kolumnę Wymagane. Adres URL musi zakodować parametry kwerendy.  
  
  
|Nazwa|Wartość|Typ|Wymagany|  
|----------|-----------|----------|--------------|
|<a name="count" />Liczba|Liczba wyników do zwrócenia, począwszy od `offset` indeksu określonego przez parametr.|Ciąg|Nie|   
|<a name="localCategories" />localKategories (LokalneKategorie)|Lista opcji definiujących wyszukiwanie według kategorii firmy.  Zobacz [wyszukiwanie lokalnych kategorii firm](local-categories.md)|Ciąg|Nie|  
|<a name="mkt" />mkt|Rynek, z którego pochodzą wyniki. <br /><br />Aby uzyskać listę możliwych wartości rynkowych, zobacz Kody rynkowe.<br /><br /> **UWAGA:** Interfejs API wyszukiwania firm lokalnych obsługuje obecnie tylko rynek i język en-us.<br /><br />|Ciąg|Tak|
|<a name="offset"/>Przesunięcie|Indeks, aby rozpocząć wyniki `count` określone przez parametr.|Liczba całkowita|Nie|  
|<a name="query" />P|Wyszukiwany termin użytkownika.|Ciąg|Nie|  
|<a name="responseformat" />Responseformat|Typ nośnika do użycia w odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD ( JSONLD )</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje o obiektach JSON, które zawiera odpowiedź, zobacz [Obiekty odpowiedzi](#response-objects).<br /><br />  Jeśli określisz JsonLd, treść odpowiedzi zawiera obiekty JSON-LD, które zawierają wyniki wyszukiwania. Aby uzyskać informacje na temat JSON-LD, zobacz [JSON-LD](https://json-ld.org/).|Ciąg|Nie|  
|<a name="safesearch" />safeSearch|Filtr używany do odfiltrowania zawartości dla dorosłych. Poniżej przedstawiono możliwe wartości filtru bez uwzględniania wielkości liter.<br /><ul><li>Strony&mdash;internetowe Off Return z tekstem, obrazami lub filmami dla dorosłych.<br /><br/></li><li>Umiarkowane&mdash;strony sieci Web powrotu z tekstem dla dorosłych, ale nie z obrazami ani filmami dla dorosłych.<br /><br/></li><li>Ścisłe&mdash;Nie zwracaj stron internetowych z tekstem dla dorosłych, obrazami lub filmami.</li></ul><br /> Wartość domyślna to Moderate.<br /><br /> **UWAGA:** Jeśli żądanie pochodzi z rynku, który wymaga zasady `safeSearch` Bing dla dorosłych, który `safeSearch` jest ustawiony na ścisłe, Bing ignoruje wartość i używa ścisłe.<br/><br/>**UWAGA:** Jeśli używasz operatora zapytania `site:`, istnieje ryzyko, że odpowiedź będzie zawierać zawartość dla dorosłych niezależnie od ustawienia parametru zapytania `safeSearch`. Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych. |Ciąg|Nie|  
|<a name="setlang" />setLang|Język ciągów interfejsu użytkownika. Określ język przy użyciu 2-literowego kodu języka w standardzie ISO 639-1. Na przykład kod języka dla języka angielskiego to EN. Wartość domyślna to EN (język angielski).<br /><br /> Mimo, że jest to opcjonalne, należy zawsze określić język. Na ogół dla parametru `setLang` ustawia się język określony przez parametr `mkt`, chyba że użytkownik chce, aby ciągi interfejsu użytkownika były wyświetlane w innym języku.<br /><br /> Ten parametr i nagłówek [Accept-Language](#acceptlanguage) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Ponadto wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.|Ciąg|Nie| 


## <a name="response-objects"></a>Obiekty odpowiedzi  
Poniżej znajdują się obiekty odpowiedzi JSON, które odpowiedź może zawierać. Jeśli żądanie zakończy się pomyślnie, obiekt najwyższego poziomu w odpowiedzi jest [SearchResponse](#searchresponse) obiektu. Jeśli żądanie nie powiedzie się, obiekt najwyższego poziomu jest [ErrorResponse](#errorresponse) obiektu.


|Obiekt|Opis|  
|------------|-----------------|  
|[Miejsce](#place)|Definiuje informacje o lokalnej firmie, takiej jak restauracja lub hotel.|  

  
### <a name="error"></a>Błąd  
Definiuje błąd, który wystąpił.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kod|Kod błędu identyfikujący kategorię błędu. Aby uzyskać listę możliwych kodów, zobacz [Kody błędów](#error-codes).|Ciąg|  
|<a name="error-message" />Komunikat|Opis błędu.|Ciąg|  
|<a name="error-moredetails" />więcejSzczegóły|Opis zawierający dodatkowe informacje o błędzie.|Ciąg|  
|<a name="error-parameter" />Parametr|Parametr kwerendy w żądaniu, który spowodował błąd.|Ciąg|  
|<a name="error-subcode" />Subcode|Kod błędu identyfikujący błąd. Na przykład `code` jeśli jest InvalidRequest, `subCode` może być ParameterInvalid lub ParameterInvalidValue. |Ciąg|  
|<a name="error-value" />value|Wartość parametru kwerendy, która była nieprawidłowa.|Ciąg|  
  

### <a name="errorresponse"></a>ErrorResponse (Odpowiadanie na błędy)  
Obiekt najwyższego poziomu, który zawiera odpowiedź, gdy żądanie nie powiedzie się.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|_type|Wpisz wskazówkę.|Ciąg|  
|<a name="errors" />Błędy|Lista błędów opisujących przyczyny niepowodzenia żądania.|[Błąd](#error)[]|  

  
  
### <a name="license"></a>Licencja  
Definiuje licencję, na podstawie której można używać tekstu lub zdjęcia.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|name|Nazwa licencji.|Ciąg|  
|url|Adres URL witryny sieci Web, w której użytkownik może uzyskać więcej informacji o licencji.<br /><br /> Użyj nazwy i adresu URL, aby utworzyć hiperłącze.|Ciąg|  


### <a name="link"></a>Link  
Definiuje składniki hiperłącza.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|_type|Wpisz wskazówkę.|Ciąg|  
|tekst|Wyświetlany tekst.|Ciąg|  
|url|Adres URL. Użyj adresu URL i wyświetlanego tekstu, aby utworzyć hiperłącze.|Ciąg|  
  


  
### <a name="organization"></a>Organizacja  
Definiuje wydawcę.  
  
Pamiętaj, że wydawca może podać swoją nazwę lub swoją witrynę lub obie strony.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|name|Nazwa wydawcy.|Ciąg|  
|url|Adres URL w witrynie wydawcy.<br /><br /> Należy pamiętać, że wydawca może nie udostępniać witryny sieci Web.|Ciąg|  
  
  

### <a name="place"></a>Miejsce  
Definiuje informacje o lokalnej firmie, takiej jak restauracja lub hotel.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|_type|Wskazówki dotyczące typu, które mogą być ustawione na jedną z następujących czynności:<br /><br /><ul><li>Hotel</li><li>Lokalnabiznes<br /></li><li>Restaurant</ul><li>|Ciąg|  
|adres|Adres pocztowy, w którym znajduje się podmiot.|PostalAddress (Adres pocztowy)|  
|entityPresentationInfo|Dodatkowe informacje o jednostce, takie jak wskazówki, których można użyć do określenia typu jednostki. Na przykład, czy jest to restauracja lub hotel. To `entityScenario` pole jest ustawione na ListItem.|EntityPresentationInfo|  
|name|Nazwa jednostki.|Ciąg|  
|telefon|Numer telefonu jednostki.|Ciąg|  
|url|Adres URL do witryny internetowej jednostki.<br /><br /> Użyj tego adresu URL wraz z nazwą encji, aby utworzyć hiperłącze, które po kliknięciu przeniesie użytkownika do witryny sieci Web encji.|Ciąg|  
|webSearchUrl (wyszukiwanie w internecie)|Adres URL do wyniku wyszukiwania Bing dla tego miejsca.|Ciąg| 
  
  
### <a name="querycontext"></a>Kontekst querycontext  
Definiuje kontekst kwerendy, który Bing używane dla żądania.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|dorosłyInteści|Wartość logiczna, która wskazuje, czy określona kwerenda ma zamiar osoby dorosłej. Wartość jest **true,** jeśli kwerenda ma zamiar dorosłych; w przeciwnym razie **false**.|Wartość logiczna|  
|zmianaOverrideQuery|Ciąg kwerendy, który ma być używany do wymuszenia bing używać oryginalnego ciągu. Na przykład, jeśli ciąg zapytania jest *saling downwind*, ciąg kwerendy zastępowania będzie *+saling downwind*. Pamiętaj, aby zakodować ciąg zapytania, który powoduje *%2Bsaling +downwind*.<br /><br /> To pole jest uwzględniane tylko wtedy, gdy oryginalny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|zmienionaQuery|Ciąg zapytania używany przez bing do wykonywania kwerendy. Bing używa zmienionego ciągu zapytania, jeśli oryginalny ciąg zapytania zawierał błędy pisowni. Na przykład, jeśli ciąg `saling downwind`zapytania jest , zmieniony `sailing downwind`ciąg zapytania będzie .<br /><br /> To pole jest uwzględniane tylko wtedy, gdy oryginalny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|askUserForLocation|Wartość logiczna, która wskazuje, czy bing wymaga lokalizacji użytkownika, aby zapewnić dokładne wyniki. Jeśli lokalizacja użytkownika została określona przy użyciu nagłówków [X-MSEdge-ClientIP](#clientip) i [X-Search-Location,](#location) można zignorować to pole.<br /><br /> W przypadku zapytań obsługujących lokalizację, takich jak "dzisiejsza pogoda" lub "restauracje w pobliżu", które potrzebują lokalizacji użytkownika, aby zapewnić dokładne wyniki, to pole jest ustawione na **true**.<br /><br /> W przypadku zapytań obsługujących lokalizację, które zawierają lokalizację (na przykład "Pogoda w Seattle"), to pole jest ustawione na **false**. To pole jest również ustawione na **false** dla kwerend, które nie są znane lokalizacji, takich jak "bestsellerów".|Wartość logiczna|  
|originalQuery (OriginalQuery)|Ciąg zapytania określony w żądaniu.|Ciąg|  

### <a name="identifiable"></a>Zidentyfikowania

|Nazwa|Wartość|Typ|  
|-------------|-----------------|----------|
|id|Identyfikator zasobu|Ciąg|
 
### <a name="rankinggroup"></a>Grupa rankingowa
Definiuje grupę wyników wyszukiwania, taką jak linia główna.

|Nazwa|Wartość|Typ|  
|-------------|-----------------|----------|
|Elementy|Lista wyników wyszukiwania do wyświetlenia w grupie.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definiuje element wyników wyszukiwania do wyświetlenia.

|Nazwa|Wartość|Typ|  
|-------------|-----------------|----------|
|resultIndex (wynik)|Indeks od zera elementu w odpowiedzi do wyświetlenia. Jeśli zapas nie zawiera tego pola, w odpowiedzi wyświetlają wszystkie elementy. Na przykład wyświetl wszystkie artykuły z wiadomościami w odpowiedzi wiadomości.|Liczba całkowita|
|odpowiedźTyp|Odpowiedź, która zawiera element do wyświetlenia. Na przykład Wiadomości.<br /><br />Użyj tego typu, aby znaleźć odpowiedź w SearchResponse obiektu. Typ jest nazwą pola SearchResponse.<br /><br /> Jednak należy użyć typu odpowiedzi tylko wtedy, gdy ten obiekt zawiera pole wartości; w przeciwnym razie zignoruj go.|Ciąg|
|textualIndex ( textualIndex )|Indeks odpowiedzi w textualAnswers do wyświetlenia.| Niepodpisana integer|
|value|Identyfikator identyfikujący odpowiedź do wyświetlenia lub element odpowiedzi do wyświetlenia. Jeśli identyfikator identyfikuje odpowiedź, wyświetl wszystkie elementy odpowiedzi.|Zidentyfikowania|

### <a name="rankingresponse"></a>RankingOdpowiedzialność  
Określa, gdzie na stronie wyników wyszukiwania należy umieścić zawartość i w jakiej kolejności.  
  
|Nazwa|Wartość|  
|----------|-----------|  
|<a name="ranking-mainline" />Mainline|Wyniki wyszukiwania do wyświetlenia w linii głównej.|  
|<a name="ranking-pole" />Biegun|Wyniki wyszukiwania, które powinny być zapewnione najbardziej widoczne traktowanie (na przykład, wyświetlane powyżej linii głównej i paska bocznego).|  
|<a name="ranking-sidebar" />Pasku bocznym|Wyniki wyszukiwania do wyświetlenia na pasku bocznym.| 

### <a name="searchresponse"></a>SzukajOdpowiedzialność  
Definiuje obiekt najwyższego poziomu, który zawiera odpowiedź, gdy żądanie zakończy się pomyślnie.  
  
Należy zauważyć, że jeśli usługa podejrzewa atak typu "odmowa usługi", żądanie zakończy się pomyślnie (kod stanu HTTP to 200 OK); jednak treść odpowiedzi będzie pusta.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|_type|Wpisz wskazówkę, która jest ustawiona na SearchResponse.|Ciąg|  
|Miejsca|Lista jednostek, które są istotne dla kwerendy wyszukiwania.|Obiekt JSON|  
|queryContext (Tekst kontekstowy)|Obiekt, który zawiera ciąg zapytania, który Bing używane dla żądania.<br /><br /> Ten obiekt zawiera ciąg zapytania wprowadzony przez użytkownika. Może również zawierać zmieniony ciąg zapytania, który Bing używany dla kwerendy, jeśli ciąg zapytania zawierał błąd pisowni.|[Kontekst querycontext](#querycontext)|  


## <a name="error-codes"></a>Kody błędów

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|200|Powodzenie.|  
|400|Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy.|  
|401|Brakuje klucza subskrypcji lub jest nieprawidłowy.|  
|403|Użytkownik jest uwierzytelniony (na przykład użył prawidłowego klucza subskrypcji), ale nie ma uprawnień do żądanego zasobu.<br /><br /> Bing może również zwrócić ten stan, jeśli wywołujący przekroczył ich zapytania miesięcznie przydziału.|  
|410|Żądanie używane HTTP zamiast protokołu HTTPS. HTTPS jest jedynym obsługiwanym protokołem.|  
|429|Wywołujący przekroczył ich zapytania na sekundę przydziału.|  
|500|Nieoczekiwany błąd serwera.|

Jeśli żądanie nie powiedzie się, odpowiedź zawiera [ErrorResponse](#errorresponse) obiektu, który zawiera listę [Error](#error) obiektów, które opisują, co spowodowało błąd. Jeśli błąd jest związany z `parameter` parametrem, pole identyfikuje parametr, który jest problemem. A jeśli błąd jest związany z `value` wartością parametru, pole identyfikuje wartość, która jest nieprawidłowa.

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

Poniżej przedstawiono możliwe wartości kodu błędu i kodu błędu podrzędnego.

|Code|Subcode|Opis
|-|-|-
|Serwer Serwera|UnexpectedError (Nieoczekiwanyeror)<br/>Źródło zasobów<br/>Nienałożony|Kod stanu HTTP to 500.
|Prośba o unieważnienie|Odsuwanie parametrów<br/>Wartość parametruInvalidValue<br/>HttpNotallowed (Nieuwolna)<br/>Zablokowane|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP wynosi 400.<br/><br/>Jeśli używasz protokołu HTTP zamiast protokołu HTTPS, bing zwraca httpnotallowed, a kod stanu HTTP to 410.
|RateLimitExceeded (RateLimitExceeded)|Brak kodów podrzędnych|Bing zwraca RateLimitExceeded zawsze, gdy przekraczasz zapytania na sekundę (QPS) lub zapytania miesięcznie (QPM) przydział.<br/><br/>Jeśli przekroczysz QPS, Bing zwraca kod stanu HTTP 429, a jeśli przekroczysz QPM, Bing zwraca wartość 403.
|InvalidAuthorization (In invalidAuthorization)|AuthorizationMissing<br/>AutoryzacjaRedundancy|Bing zwraca InvalidAuthorization, gdy bing nie może uwierzytelnić wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określisz więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|Niewystarczająca autoryzacja|AuthorizationDisabled (Nie można wyjawić)<br/>AuthorizationExpired (Brak uprawnień)|Bing zwraca InsufficientAuthorization, gdy wywołujący nie ma uprawnień dostępu do zasobu. Taka możliwość może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

## <a name="next-steps"></a>Następne kroki
- [Szybki start wyszukiwania lokalnych firm](quickstarts/local-quickstart.md)
- [Przewodnik Szybki start java wyszukiwania lokalnego firmy](quickstarts/local-search-java-quickstart.md)
- [Szybki start lokalnego węzła wyszukiwania firm](quickstarts/local-search-node-quickstart.md)
- [Szybki start wyszukiwania dla firm lokalnych](quickstarts/local-search-python-quickstart.md)
