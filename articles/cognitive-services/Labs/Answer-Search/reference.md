---
title: Odwołanie wyszukiwania odpowiedzi - kognitywnych usług firmy Microsoft do projektu | Dokumentacja firmy Microsoft
description: Odwołanie wyszukiwania odpowiedzi projektu punktu końcowego.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 8c95fac0c031ec62a9d98d6c3278bd3b3f345140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348728"
---
# <a name="project-answer-search-v7-reference"></a>Odwołanie do projektu odpowiedzi wyszukiwania w wersji 7

SearchAPI odpowiedzi Bing przyjmuje parametr zapytania i zwraca `searchResponse` z `answerType`: `facts` lub `entities`. 

Aplikacje, które korzystają z interfejsu API wyszukiwania odpowiedzi wysyłać żądania do punktu końcowego o adresie URL w parametr zapytania.  Żądanie musi zawierać `q=searchTerm` parametru i *Ocp-Apim-subskrypcji — klucz* nagłówka.   

Odpowiedź w formacie JSON może być analizowana pod kątem faktów i jednostek, które zawierają szczegółowe informacje o obiekcie wyszukiwania.

## <a name="endpoint"></a>Endpoint
Aby poprosić o wynikach wyszukiwania odpowiedzi, Wyślij żądanie do następujący punkt końcowy. Umożliwia dalsze Definiowanie specyfikacji w nagłówki i parametry adresu URL.

Punkt końcowy GET: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

Żądanie musi używać protokołu HTTPS i obejmują następujące parametru zapytania:
-  q =<URL> -kwerendę, która identyfikuje obiekt wyszukiwania

Przykłady, które pokazują, jak wysyłać żądania można znaleźć [C# — Szybki Start](c-sharp-quickstart.md) lub [szybkiego startu Java](java-quickstart.md). 

Poniższe sekcje zawierają szczegółowe informacje techniczne dotyczące obiektów odpowiedzi, parametry zapytania i nagłówków, które mają wpływ na wyniki wyszukiwania. 
  
Informacji o nagłówkach, obejmujących żądań, zobacz [nagłówki](#headers).  
  
Informacje o parametrach zapytania, obejmujących żądań, zobacz [parametry zapytania](#query-parameters).  
  
Aby uzyskać informacje o formacie JSON obiekty odpowiedź zawiera, zobacz [obiektów odpowiedzi](#response-objects).

Zapytanie maksymalna długość adresu URL jest 2048 znaków. Aby upewnić się, Twoje długość adresu URL przekracza limit, maksymalna długość parametry zapytania należy 1 mniej niż 500 znaków. Jeśli adres URL przekracza 2048 znaków, serwer zwraca 404, nie można odnaleźć.  

Uzyskać informacje o dozwolonych użycia i wyświetlania wyników, zobacz [użycia i wyświetlić wymagania](use-display-requirements.md). 

> [!NOTE]
> Niektóre nagłówków żądań, które zrozumiały dla innych interfejsów API wyszukiwania nie wpływają na adres URL w wersji zapoznawczej
> - Pragma — obiekt wywołujący nie ma kontroli nad Określa, czy adres URL podglądu korzysta z pamięci podręcznej
> - Cache-Control — obiekt wywołujący nie ma kontroli nad Określa, czy adres URL podglądu korzysta z pamięci podręcznej
> - Agent użytkownika

> Ponadto niektóre parametry nie są obecnie zrozumiały dla interfejsu API w wersji zapoznawczej adres URL, ale mogą być używane w przyszłości ulepszone globalizacji. 
 
## <a name="headers"></a>Nagłówki  
Poniżej przedstawiono nagłówki, które mogą obejmować żądania i odpowiedzi.  
  
|Nagłówek|Opis|  
|------------|-----------------|  
|Zaakceptuj|Nagłówek żądania opcjonalne.<br /><br /> Domyślny typ nośnika jest application/json. Aby określić, czy używać odpowiedzi [JSON-LD](http://json-ld.org/), ustawić nagłówek Accept do aplikacji/ld + json.|  
|<a name="acceptlanguage" />Zaakceptuj języka|Nagłówek żądania opcjonalne.<br /><br /> Rozdzielana przecinkami lista języków dla ciągów interfejsu użytkownika. Lista jest w porządku malejącym. Więcej informacji, łącznie z oczekiwanego formatu [specyfikacją RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i [setLang](#setlang) parametru zapytania wykluczają się wzajemnie&mdash;nie podawaj jednocześnie.<br /><br /> Jeśli ustawisz ten nagłówek, należy także określić [DW](#cc) parametr zapytania. Ustalenie dostępnych na rynku wyników dla Bing używa pierwszego obsługiwanych języków znajduje się na liście i łączy go z `cc` wartość parametru. Jeśli lista nie zawiera obsługiwanych języków, Bing znajduje się najbliżej języka i rynku, która obsługuje żądania lub używa zagregowane lub domyślna rynku dla wyników. Aby określić rynku Bing używany, zobacz nagłówek BingAPIs rynku.<br /><br /> Używają tego nagłówka i `cc` parametr zapytania tylko wtedy, gdy można określić wiele języków. W przeciwnym razie użyj [mkt](#mkt) i [setLang](#setlang) parametry zapytania.<br /><br /> Ciąg interfejsu użytkownika jest ciągiem, który jest używana jako etykieta w interfejsie użytkownika. Istnieje kilka ciągów interfejsu użytkownika w obiektach odpowiedzi JSON. Łącza do właściwości Bing.com w obiektach odpowiedzi zastosowania określonego języka.|  
|<a name="market" />BingAPIs rynku|Nagłówek odpowiedzi.<br /><br /> Rynku używany przez żądanie. Formularz jest \<atrybutu languageCode\>-\<countryCode\>. Na przykład en US.|  
|<a name="traceid" />BingAPIs TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegóły żądania. Gdy wystąpi błąd, przechwycić tego identyfikatora. Jeśli nie jest możliwe określić i rozwiązać problem, należy uwzględnić ten identyfikator wraz z innymi informacjami, musisz zapewnić zespołem pomocy technicznej.|  
|<a name="subscriptionkey" />OCP-Apim subskrypcji — klawisz|Nagłówek żądania wymagane.<br /><br /> Klucz subskrypcji, odebrany po zarejestrowaniu dla tej usługi w [kognitywnych usług](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Wartość dyrektywy pragma|Nagłówek żądania opcjonalne<br /><br /> Domyślnie Bing zwraca zawartości w pamięci podręcznej, jeśli jest dostępna. Aby zapobiec Bing zwracanie zawartości w pamięci podręcznej, ustaw nagłówek Pragma no-cache (na przykład Pragma: nie-cache).
|<a name="useragent" />Agent użytkownika|Nagłówek żądania opcjonalne.<br /><br /> Agent użytkownika wysłał żądanie. Bing używa agenta użytkownika, aby zapewnić użytkownikom mobilnym zoptymalizowane środowisko. Mimo że jest to opcjonalne, zachęca się zawsze określić tego nagłówka.<br /><br /> Agent użytkownika powinna być tego samego ciąg, który wysyła dowolnej przeglądarki często używane. Informacje o agentów użytkownika, zobacz [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągi agentów użytkownika.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodny; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 kompilacji/PIERNIKI) AppleWebKit/533.1 (KHTML; aparatu Gecko, takich jak) wersji i 4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; Procesor CPU 6_1 iPhone systemu operacyjnego Mac OS x) AppleWebKit/536.26 (KHTML; aparatu Gecko, takich jak) iPhone4 Mobile/10B142; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer PC&mdash;Mozilla/5.0 (system Windows NT 6.3; EMULATOR WOW64; Trident/7.0. Touch; Rv:11.0) aparatu Gecko, takich jak<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; 7_0 Procesora systemu operacyjnego, takich jak system Mac OS X) AppleWebKit/537.51.1 (takich jak aparat Gecko KHTML) w wersji/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Opcjonalne nagłówki żądań i odpowiedzi.<br /><br /> Bing używa tego nagłówka użytkownikom zachowanie spójności w wywołaniach interfejsu API usługi Bing. Bing często lotach nowe funkcje i ulepszenia, i używa Identyfikatora klienta jako klucza do przypisywania ruchu w różnych lotach. Jeśli nie używasz tego samego Identyfikatora klienta dla użytkownika dla wielu żądań przesyłanych, Bing może przypisać użytkownika do wielu lotach powodujące konflikt. Przypisane do wielu lotach powodujące konflikt może spowodować niespójne użytkowników. Na przykład jeśli drugi żądania przypisaniem transmitowane innego niż pierwszy, środowisko użytkownika może być nieoczekiwany. Ponadto Bing można użyć Identyfikatora klienta, aby dostosować wyniki sieci web na tym kliencie identyfikatory historii wyszukiwania, podając bardziej rozbudowane środowisko użytkownika.<br /><br /> Bing również używa tego nagłówka do poprawy wyników klasyfikacji za analizowanie aktywności generowane przez identyfikator klienta. Istotne ulepszenia pomoc lepszą jakość wyniki dostarczone przez interfejsy API usługi Bing i z kolei umożliwia kliknięć większe dla klienta interfejsu API.<br /><br /> **Ważne:** mimo, że jest to opcjonalne, należy rozważyć ten wymagany nagłówek. Dla tego samego użytkownika końcowego i połączeniu urządzenia z trwałym identyfikator klienta dla wielu żądań przesyłanych umożliwia 1) konsumenta interfejsu API odbierać spójną obsługę użytkowników i 2) kliknięć większe za pośrednictwem lepszą jakość wyników z interfejsów API usługi Bing.<br /><br /> Poniżej przedstawiono zasady podstawowe sposoby użycia, które są stosowane do tego nagłówka.<br /><ul><li>Każdy użytkownik, który używa aplikacji na urządzeniu musi mieć unikatową, Bing wygenerowany identyfikator klienta.<br /><br/>Jeśli nie obejmują tego nagłówka w żądaniu Bing generuje identyfikator i zwraca go w nagłówka X-MSEdge-ClientID odpowiedzi. Tylko wtedy, że nie należy używać tego nagłówka w żądaniu po raz pierwszy użytkownik korzysta z aplikacji na tym urządzeniu.<br /><br/></li><li>Dla każdego żądania interfejsu API Bing sprawia, że aplikacja dla tego użytkownika na urządzeniu, należy użyć Identyfikatora klienta.<br /><br/></li><li>**Uwaga:** musi upewnij się, że ten identyfikator klienta nie jest możliwym do żadnych informacji o koncie użytkownika poświadczalne.</li><br/><li>Utrwalanie identyfikator klienta. Aby zachować identyfikator w aplikacji przeglądarki, umożliwia upewnij się, że ten identyfikator jest używany we wszystkich sesjach trwały plik cookie HTTP. Nie należy używać pliku cookie sesji. Dla innych aplikacji, takich jak aplikacje mobilne Użyj urządzenia magazynu trwałego do utrzymania identyfikator.<br /><br/>Następnym razem, użytkownik korzysta z aplikacji na tym urządzeniu pobieranie Identyfikatora klienta, które zostały utrwalone.</li></ul><br /> **Uwaga:** Bing odpowiedzi może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera ten nagłówek, przechwytywania identyfikator klienta i użyć jej do wszystkich kolejnych żądań Bing dla użytkownika na tym urządzeniu.<br /><br /> **Uwaga:** Jeśli dołączysz X MSEdge ClientID nie może zawierać pliki cookie w żądaniu.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nagłówek żądania opcjonalne.<br /><br /> Adres IPv4 lub IPv6 urządzenia klienckiego. Adres IP jest używany do odnajdywania lokalizacji użytkownika. Bing używa informacji o lokalizacji w celu określenia działanie wyszukiwania bezpieczne.<br /><br /> **Uwaga:** mimo, że jest to opcjonalne, możesz zachęcamy do zawsze podać tego nagłówka i nagłówka X wyszukiwania lokalizacji.<br /><br /> Adres nie zasłaniają (na przykład, zmieniając ostatni oktet 0). Obfuscating wyników adresu w tej lokalizacji nie jest dowolne miejsce w pobliżu urządzenia rzeczywistej lokalizacji, co może spowodować Bing obsługująca błędne wyniki.|  
|<a name="location" />X wyszukiwania lokalizacji|Nagłówek żądania opcjonalne.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, opisujących lokalizacji geograficznej klienta. Bing używa informacji o lokalizacji określają zachowanie wyszukiwania bezpieczne i zwracać odpowiedniej zawartości lokalnej. Określ parę klucz wartość jako \<klucza\>:\<wartość\>. Poniżej przedstawiono kluczy, które umożliwia określenie lokalizacji użytkownika.<br /><br /><ul><li>lat&mdash;szerokość lokalizacji klienta w stopniach. Zakres musi być większa lub równa-90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują geograficznej Terytoria Południowe i liczbę wartości dodatnich wskazują geograficznej północnej.<br /><br /></li><li>długie&mdash;wysokość lokalizacji klienta w stopniach. Wysokość musi być większa lub równa-180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują western szerokości geograficzne i liczbę wartości dodatnich wskazują Wschodniej szerokości geograficzne.<br /><br /></li><li>re&mdash; promień, w liczników, który określa poziomy dokładność współrzędne. Należy przekazać wartość zwrócona przez usługę lokalizacji urządzenia. Typowe wartości może być 22 MB dla GPS/Wi-Fi, 380m dla komórki wieża triangulacji i 18,000m dla wyszukiwanie wsteczne adresu IP.<br /><br /></li><li>TS&mdash; sygnaturę czasową UTC UNIX gdy klient nie był w tej lokalizacji. (Sygnatura czasowa UNIX jest liczba sekund od 1 stycznia 1970).<br /><br /></li><li>HEAD&mdash;opcjonalne. Nagłówek względną lub kierunek ruchu klienta. Określ kierunek ruchu jako stopni od 0 do 360 zliczania do ruchu wskazówek zegara względem Północna wartość true. Określ kluczy tylko wtedy, gdy `sp` klucz jest różna od zera.<br /><br /></li><li>SP&mdash; prędkość pozioma (szybkość) metry na sekundę, które urządzenia klienckie są przesyłane.<br /><br /></li><li>ALT&mdash; wysokość urządzenia klienta, w liczników.<br /><br /></li><li>są&mdash;opcjonalne. Promień, metry, określająca dokładność pionowy współrzędne. Domyślnie usługi RADIUS kilometrach 50. Określ ten klucz, tylko jeśli określisz `alt` klucza.<br /><br /></li></ul> **Uwaga:** Chociaż klucze te są opcjonalne, więcej informacji, które zostaną podane, są bardziej dokładne wyniki lokalizacji.<br /><br /> **Uwaga:** zaleca się zawsze określać położenie geograficzne użytkownika. Dostarczanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedlają dokładnie fizyczną lokalizację użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki tego nagłówka i nagłówka X-MSEdge-ClientIP należy uwzględnić, ale co najmniej powinna zawierać tego nagłówka.|

> [!NOTE] 
> Należy pamiętać, że warunki użytkowania wymagają zgodności z wszelkimi właściwymi prawami, w tym dotyczące tych nagłówków. Na przykład w niektórych krajach, takich jak Europy, istnieją wymagania, aby uzyskać zgodę użytkownika przed wprowadzeniem niektórych urządzeń śledzenia na urządzeniach użytkowników.
  

## <a name="query-parameters"></a>Parametry zapytania  
Żądanie może obejmować następujące parametry zapytania. Zobacz wymaganych kolumn dla wymaganych parametrów. Adres URL należy zakodować parametry zapytania.  
  
  
|Name (Nazwa)|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Rynku, skąd wyniki. <br /><br />Aby uzyskać listę możliwych wartości rynku, zobacz [kody rynku](#market-codes).<br /><br /> **Uwaga:** interfejsu API w wersji zapoznawczej adresu URL aktualnie obsługuje tylko en-us rynku i języka.<br /><br />|Ciąg|Yes|  
|<a name="query" />Q|Adres URL do podglądu|Ciąg|Yes|  
|<a name="responseformat" />Format odpowiedzi|Typ nośnika do użycia dla odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje o formacie JSON obiekty odpowiedź zawiera, zobacz [obiektów odpowiedzi](#response-objects).<br /><br />  Jeśli określisz JsonLd treść odpowiedzi zawiera obiekty JSON LD, które zawierają wyniki wyszukiwania. Informacje o JSON LD, zobacz [JSON-LD](http://json-ld.org/).|Ciąg|Nie|  
|<a name="safesearch" />bezpieczne wyszukiwanie|Filtr używane do filtrowania zawartość dla dorosłych. Poniżej przedstawiono wartości możliwe filtru bez uwzględniania wielkości liter.<br /><ul><li>Wyłącz&mdash;zwracać stron sieci Web z dorosłych tekst, obrazy lub wideo.<br /><br/></li><li>Umiarkowany&mdash;zwracać stron sieci Web z dorosłych tekstu, ale nie dla dorosłych obrazy lub wideo.<br /><br/></li><li>Strict&mdash;nie zwracać stron sieci Web z dorosłych tekst, obrazy lub wideo.</li></ul><br /> Wartość domyślna to umiarkowany.<br /><br /> **Uwaga:** Jeśli żądanie pochodzi z rynku zasad dla dorosłych tego Bing wymaga, aby `safeSearch` jest ustawiony na Strict, ignoruje Bing `safeSearch` wartość i używa Strict.<br/><br/>**Uwaga:** użycie `site:` — operator zapytań, istnieje ryzyko, że odpowiedź może zawierać zawartość dla dorosłych niezależnie od tego, co `safeSearch` ustawiono parametr zapytania. Użyj `site:` tylko wtedy, gdy masz świadomość zawartości w witrynie i scenariusz obsługuje możliwości zawartość dla dorosłych. |Ciąg|Nie|  
|<a name="setlang" />setLang|Język do użycia dla ciągów interfejsu użytkownika. Wybierz język, przy użyciu obrazu ISO 639 1 2-znakowy kod języka. Na przykład kod język angielski jest EN. Wartość domyślna to EN (angielski).<br /><br /> Mimo że jest to opcjonalne, należy zawsze określić język. Zwykle ustawić `setLang` w języku określonym przez `mkt` , chyba że użytkownik będzie chciał ciągów interfejsu użytkownika, które są wyświetlane w innym języku.<br /><br /> Ten parametr i [Accept-Language](#acceptlanguage) nagłówka wykluczają się wzajemnie&mdash;nie podawaj jednocześnie.<br /><br /> Ciąg interfejsu użytkownika jest ciągiem, który jest używana jako etykieta w interfejsie użytkownika. Istnieje kilka ciągów interfejsu użytkownika w obiektach odpowiedzi JSON. Ponadto łącza do właściwości Bing.com w obiektach odpowiedzi zastosowania określonego języka.|Ciąg|Nie| 


## <a name="response-objects"></a>Obiekty odpowiedzi  
Schemat odpowiedzi jest albo [strony sieci Web] lub errorresponse języka, tak jak interfejsu API sieci Web wyszukiwania. Jeśli żądanie kończy się niepowodzeniem, obiekt najwyższego poziomu jest [errorresponse języka](#errorresponse) obiektu.


|Obiekt|Opis|  
|------------|-----------------|  
|[Strona sieci Web]|Najwyższego poziomu obiekt JSON, który zawiera atrybuty podglądu.|  
|[Fakt]|Najwyższego poziomu obiekt JSON, który zawiera dane.| 
|[Jednostek|Najwyższego poziomu obiekt JSON, który zawiera szczegóły jednostki.| 

  
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

  
  
### <a name="license"></a>Licencja  
Definiuje licencji, w którym tekst lub fotografii mogą być używane.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|name|Nazwa licencji.|Ciąg|  
|url|Adres URL witryny sieci Web, w której użytkownik może uzyskać więcej informacji o licencji.<br /><br /> Użyj nazwy i adresu URL, aby utworzyć hiperłącze.|Ciąg|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definiuje umownymi regułę autorstwa licencji.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_wprowadź|Wskazówka typu, która jest ustawiona na LicenseAttribution.|Ciąg|  
|licencja|Licencji, w którym można używać zawartości.|[Licencji](#license)|  
|licenseNotice|Licencja do wyświetlenia obok pola docelowego. Na przykład "Tekst w ramach licencji DW przez administratora systemu".<br /><br /> Użyj nazwy i adresu URL w licencji `license` pola, aby utworzyć hiperłącze do witryny sieci Web, który opisuje szczegóły licencji. Następnie zastąp nazwę licencji w `licenseNotice` ciąg (na przykład CC-za-SA) z hiperłączem właśnie utworzony.|Ciąg|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły muszą znajdować się w pobliżu do pola, które będzie stosowana ta reguła. Jeśli **true**, zawartość należy umieścić w pobliżu. Jeśli **false**, lub to pole nie istnieje, zawartość może być umieszczany uznania obiektu wywołującego.|Wartość logiczna|  
|targetPropertyName|Nazwa pola, które będzie stosowana ta reguła.|Ciąg|  
  

### <a name="link"></a>Link  
Określa składniki hiperłącza.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_wprowadź|Wskazówka typu.|Ciąg|  
|tekst|Wyświetlany tekst.|Ciąg|  
|url|ADRES URL. Użyj adresu URL i wyświetl tekst, aby utworzyć hiperłącze.|Ciąg|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definiuje umownymi regułę autorstwa łącza.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_wprowadź|Wskazówka typu, która jest ustawiona na LinkAttribution.|Ciąg|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły muszą znajdować się w pobliżu do pola, które będzie stosowana ta reguła. Jeśli **true**, zawartość należy umieścić w pobliżu. Jeśli **false**, lub to pole nie istnieje, zawartość może być umieszczany uznania obiektu wywołującego.|Wartość logiczna|  
|targetPropertyName|Nazwa pola, które będzie stosowana ta reguła.<br /><br /> Jeśli nie określono elementu docelowego, wpisu dotyczy jednostki jako całości i powinien być wyświetlany natychmiast po przedstawieniu jednostki. W przypadku wielu tekstu i łącze reguł autorstwa, które nie określono elementu docelowego, połącz je i wyświetlać je za pomocą "dane z:" etykiety. Na przykład "dane z < Nazwa1 dostawcy\> &#124; < Nazwa2 dostawcy\>".|Ciąg|  
|tekst|Tekst autorstwa.|Ciąg|  
|url|Adres URL witryny sieci Web dostawcy. Użyj `text` i adres URL, aby utworzyć hiperłącza.|Ciąg|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definiuje umownymi regułę autorstwa nośnika.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_wprowadź|Wskazówka typu, która jest ustawiona na MediaAttribution.|Ciąg|  
|mustBeCloseToContent|Wartość logiczna określająca, czy zawartość reguły muszą znajdować się w pobliżu do pola, które będzie stosowana ta reguła. Jeśli **true**, zawartość należy umieścić w pobliżu. Jeśli **false**, lub to pole nie istnieje, zawartość może być umieszczany uznania obiektu wywołującego.|Wartość logiczna|  
|targetPropertyName|Nazwa pola, które będzie stosowana ta reguła.|Ciąg|  
|url|Adres URL, który umożliwia tworzenie hiperłącza multimediów. Na przykład jeśli element docelowy to obraz, czy używaj adres URL umożliwia obrazu aktywne.|Ciąg|  
  
  
  
### <a name="organization"></a>Organizacja  
Definiuje wydawcy.  
  
Należy pamiętać, że wydawca nazwy witryny sieci Web i/lub.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|name|Nazwa wydawcy.|Ciąg|  
|url|Adres URL witryny sieci Web wydawcy.<br /><br /> Należy pamiętać, że wydawca nie witryny sieci Web.|Ciąg|  
  
  

### <a name="webpage"></a>Strony sieci Web  
Określa informacje o stronie sieci Web w wersji zapoznawczej.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|
|name|Tytuł strony, niekoniecznie tytuł HTML|Ciąg|
|url|Adres URL, który faktycznie pochodził (żądanie może zostały wykonane przekierowania)|Ciąg|  
|description|Krótki opis strony i zawartości|Ciąg|  
|isFamilyFriendly|Najbardziej dokładna dla elementów w indeksie sieci web; pobiera w czasie rzeczywistym czy wykryciem wyłącznie na adres URL, a nie zawartości strony|wartość logiczna|
|primaryImageOfPage/contentUrl|Adres URL obrazu reprezentatywny do uwzględnienia w wersji zapoznawczej|Ciąg| 
  
  
### <a name="querycontext"></a>QueryContext  
Definiuje kontekst zapytania, który Bing użytej w żądaniu.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Wartość logiczna wskazująca, czy określone zapytanie ma zamiar dla dorosłych. Wartość jest **true** Jeśli zapytanie ma zamiar dla dorosłych; w przeciwnym razie **false**.|Wartość logiczna|  
|alterationOverrideQuery|Ciąg zapytania do użycia, aby wymusić Bing, aby użyć oryginalnego ciągu. Na przykład, jeśli ciąg zapytania jest *saling downwind*, ciąg zapytania zastąpienie będzie *+ saling downwind*. Pamiętaj, aby zakodować ciąg zapytania, co powoduje *% 2Bsaling + downwind*.<br /><br /> To pole jest uwzględniane tylko wtedy, gdy oryginalny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|alteredQuery|Ciąg zapytania używane przez Bing w celu wykonania zapytania. Bing używa ciągu zapytania zmienionych oryginalnego ciągu kwerendy zawarte błędów pisowni. Na przykład, jeśli ciąg zapytania jest `saling downwind`, ciąg zapytania zmieniony będzie `sailing downwind`.<br /><br /> To pole jest uwzględniane tylko wtedy, gdy oryginalny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|askUserForLocation|Wartość logiczna wskazująca, czy Bing wymaga lokalizacji użytkownika, aby zapewnić dokładne wyniki. Jeśli w określonej lokalizacji użytkownika przy użyciu [ClientIP-X-MSEdge](#clientip) i [X wyszukiwania lokalizacji](#location) nagłówków, możesz zignorować to pole.<br /><br /> Dla zapytań pamiętać lokalizacji, takie jak "współczesnych pogody" lub "restauracji pobliżu", wymagające lokalizacji użytkownika, aby zapewnić dokładne wyniki, to pole jest ustawione na **true**.<br /><br /> Dla lokalizacji pamiętać zapytań, które należą: Lokalizacja (na przykład "Seattle pogody"), to pole jest ustawione na **false**. To pole jest również opcja **false** dla zapytań, które nie są zależne, takie jak "najlepiej sprzedających" od lokalizacji.|Wartość logiczna|  
|originalQuery|Ciąg zapytania określony w żądaniu.|Ciąg|  

### <a name="identifiable"></a>Do zidentyfikowania
|Name (Nazwa)|Wartość|Typ|  
|-------------|-----------------|----------|
|id|Identyfikator zasobu|Ciąg|
 
### <a name="rankinggroup"></a>RankingGroup
Definiuje grupę w wynikach wyszukiwania, takie jak mainline.
|Name (Nazwa)|Wartość|Typ|  
|-------------|-----------------|----------|
|pozycje|Lista wyników wyszukiwania do wyświetlenia w grupie.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definiuje element wyników wyszukiwania do wyświetlenia.
|Name (Nazwa)|Wartość|Typ|  
|-------------|-----------------|----------|
|resultIndex|Liczony od zera indeks elementu w odpowiedzi do wyświetlenia. Jeśli element nie zawiera tego pola, Wyświetl wszystkie elementy w odpowiedzi. Na przykład wyświetlić wszystkie artykuły w odpowiedzi na wiadomości.|Liczba całkowita|
|answerType|Odpowiedź, który zawiera element, aby wyświetlić. Na przykład wiadomości.<br /><br />Aby znaleźć odpowiedzi w obiekcie SearchResponse należy używać typu. Typ jest nazwa pola SearchResponse.<br /><br /> Jednak użyć typ odpowiedzi tylko wtedy, gdy ten obiekt zawiera pola wartości; w przeciwnym razie je zignorować.|Ciąg|
|textualIndex|Indeks odpowiedzi w textualAnswers do wyświetlenia.| Liczba całkowita bez znaku|
|wartość|Identyfikator identyfikuje odpowiedzi, aby wyświetlić lub elementu odpowiedzi do wyświetlenia. Jeśli identyfikator identyfikuje odpowiedzi, Wyświetl wszystkie elementy odpowiedzi.|Do zidentyfikowania|

### <a name="rankingresponse"></a>RankingResponse  
Określa, w przypadku, gdy na wyszukiwanie zawartości strony wyników powinna zostać umieszczona i w jakiej kolejności.  
  
|Name (Nazwa)|Wartość|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Aby wyświetlić wyniki wyszukiwania w połączeniach.|  
|<a name="ranking-pole" />pole|Wyniki wyszukiwania, które powinny uzyskać traktowania najbardziej widoczne (na przykład wyświetlany powyżej połączeniach i paska bocznego).|  
|<a name="ranking-sidebar" />pasek boczny|Aby wyświetlić wyniki wyszukiwania na pasku bocznym.| 


### <a name="searchresponse"></a>SearchResponse  
Definiuje obiekt najwyższego poziomu zawierający odpowiedzi, gdy żądanie zakończy się pomyślnie.  
  
Należy pamiętać, że jeśli usługa podejrzewa, odmowę usługi, żądanie powiedzie się (kod stanu HTTP jest 200 OK); Jednak treść odpowiedzi będzie pusty.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_wprowadź|Wskazówka typu, która jest ustawiona na SearchResponse.|Ciąg|  
|Strony sieci Web|Obiekt JSON, który definiuje podglądu|ciąg|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definiuje umownymi regułę autorstwa zwykłego tekstu.  
  
|Name (Nazwa)|Wartość|Typ|  
|----------|-----------|----------|  
|_wprowadź|Wskazówka typu, która jest ustawiona na TextAttribution.|Ciąg|  
|tekst|Tekst autorstwa.<br /><br /> Tekst przydzielenia ma zastosowanie do całej jednostki i powinien być wyświetlany natychmiast po przedstawieniu jednostki. W przypadku wielu tekstu lub łącze reguł autorstwa, które nie określono elementu docelowego, połącz je i wyświetlać je za pomocą "dane z:" etykiety.|Ciąg| 


## <a name="error-codes"></a>Kody błędów

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|200|Powodzenie.|  
|400|Jeden z parametrów zapytania jest lub jest ona nieprawidłowa.|  
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
- [C# — Szybki Start](c-sharp-quickstart.md)
- [Szybki Start Java](java-quickstart.md)
- [Węzeł Szybki Start](node-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)

