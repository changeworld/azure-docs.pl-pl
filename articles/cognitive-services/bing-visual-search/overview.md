---
title: Omówienie interfejsu API wyszukiwania wizualnego Bing | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Pokazuje, jak można pobrać szczegółów lub szczegółowych informacjach o pliku obrazu, takie jak podobne obrazy lub źródeł zakupów.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aa563d89b1834f5be952f13c31a2451d809709b1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006533"
---
# <a name="what-is-bing-visual-search-api"></a>Czym jest interfejs API wyszukiwania wizualnego Bing?

Interfejs API wyszukiwania wizualnego Bing udostępnia środowisko podobnie jak pokazano na Bing.com/images szczegółowe informacje dotyczące obrazu. Wyszukiwanie wizualne możesz przekazać obraz i wrócić szczegółowe informacje dotyczące obrazu, takich jak podobnych obrazach, źródła zakupów i stron sieci Web, który zawiera obraz i nie tylko. Zamiast przekazywania obrazu, możesz także podać token szczegółowe informacje, który można pobrać z obrazu w wynikach wyszukiwania obrazów (zobacz [interfejs API obrazów Bing](../bing-image-search/overview.md)).

Wyszukiwanie wizualne można zidentyfikować, osobistości, pomników i charakterystycznych elementów krajobrazu, kompozycję, meble, sposób, produkty, rozpoznawania znaków (OCR) i inne.

Poniżej przedstawiono szczegółowe informacje, że wyszukiwanie wizualne umożliwia odnajdywanie.

- Podobnych obrazach&mdash;listę obrazów, które przypominają wizualnie obrazu wejściowego
- Wizualnie podobnych produktów&mdash;listę obrazów, które zawierają produktów, które wizualnie podobnych do produktu na obrazie danych wejściowych
- Zakupy źródeł&mdash;listy miejsc, w której można kupić elementu przedstawiona w obrazu wejściowego
- Powiązane wyszukiwania&mdash;listy powiązane wyszukiwania wprowadzone przez innych użytkowników lub które są oparte na zawartości obrazu
- Strony sieci Web, które obejmują obraz&mdash;listę stron sieci Web, która obejmuje obrazu wejściowego
- Przepisy&mdash;listę stron sieci Web zawierających przepisy składania płytkę obrazie danych wejściowych

Oprócz wglądowi w szczegółowe dane wyszukiwania wizualnego zwraca zestaw warunków (tagi) pochodzące z obrazu wejściowego. Te znaczniki pozwalają użytkownikom Eksploruj pojęcia znalezione na obrazie. Na przykład jeśli obrazu wejściowego sławę wykresie jeden tag może mieć nazwy wykresie, inny znacznik może być sportu. Lub, w przypadku obrazu wejściowego koła firmy apple, znaczniki może być desery kołowy firmy Apple, wycinków, dzięki czemu użytkownicy mogą eksplorować pojęcia pokrewne.

Wyniki wyszukiwania wizualnego także blokujących pola dla regionów zainteresowania na obrazie. Na przykład jeśli obraz zawiera kilka osobistości, wyniki mogą obejmować blokujących pola dla każdego z rozpoznanym osobistości na obrazie. Lub, jeśli Bing rozpoznaje produktu lub odzieży w obrazie, wynik może zawierać obwiedni rozpoznany produkt lub odzieży elementu.

> [!IMPORTANT]
> Jeśli używasz/obrazy/szczegóły punktu końcowego do [uzyskać szczegółowe informacje o obrazach](../bing-image-search/image-insights.md), należy zaktualizować swój kod, aby zamiast tego użyj wyszukiwania wizualnego, ponieważ zapewnia bardziej szczegółowe informacje.


## <a name="the-request"></a>Żądanie

Dostępne są następujące opcje w celu uzyskania szczegółowych informacji o pliku obrazu. 

- Wyślij token szczegółowe informacje, który można pobrać z obrazu w poprzedniego wywołania do jednego z [interfejs API obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) punktów końcowych
- Wyślij adres URL obrazu
- Przekaż obraz (binarnych)


Jeśli wyślesz wyszukiwania wizualnego token obrazu lub adres URL, poniżej przedstawiono obiekt JSON, który musi zawierać w treści wpisu. 

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

`imageInfo` Obiektu musi zawierać albo `url` lub `imageInsightsToken` pola, ale nie oba jednocześnie. Ustaw `url` pola Adres URL obrazu dostępny Internet. Rozmiar maksymalny obsługiwany obraz to 1 MB.

`imageInsightsToken` Musi być równa tokenu szczegółowych informacji. Do uzyskania tokenu szczegółowe informacje, należy wywołać interfejsu API obrazów Bing. Odpowiedź zawiera listę `Image` obiektów. Każdy `Image` obiekt zawiera `imageInsightsToken` pola, które zawiera token.

`cropArea` Pole jest opcjonalne. Obszar kadrowania Określa górny, lewym rogu i dolny, prawy róg regionu zainteresowania. Należy określić wartości z zakresu od 0,0 do 1,0. Wartości są procent ogólnej szerokości lub wysokości. Na przykład powyższy przykład oznacza po prawej stronie połowę obrazu jako regionu zainteresowania. Uwzględnij go ograniczyć żądanie insights regionu zainteresowania.

`filters` Obiekt zawiera filtr witryn (zobacz `site` pola), można użyć do ograniczenia podobnych obrazów i wyniki podobne produkty do określonej domeny. Na przykład, jeśli obraz jest Surface Book, możesz ustawić `site` do www.microsoft.com. 

Jeśli chcesz uzyskać szczegółowe informacje o lokalną kopię obrazu, Przekaż obraz jako dane binarne.

Aby uzyskać szczegółowe informacje o tym tych opcji w treść wpisu, zobacz [zawartości typów formularza](#content-form-types).


### <a name="endpoint"></a>Endpoint

Punkt końcowy wyszukiwania wizualnego jest: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Żądania muszą być wysyłane jako tylko żądania HTTP POST. 


### <a name="query-parameters"></a>Parametry zapytania

Poniżej przedstawiono parametrów zapytania, które należy określić żądanie. Jako minimum, należy uwzględnić `mkt` parametr zapytania.

|Name (Nazwa)|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|  
|<a name="cc" />DW|Kraj 2-znakowy kod kraju, skąd pochodzą wyniki.<br /><br /> Jeśli ustawisz ten parametr, należy także określić [Accept-Language](#acceptlanguage) nagłówka. Usługa Bing używa pierwszego obsługiwanego języka umożliwia znalezienie z listy języków i łączy język w celu określenia rynku, w celu zwracania wyników z kodem kraju. Jeśli lista języków nie ma obsługiwanego języka, Bing znajdzie najbliższego języka i rynku, który obsługuje żądania. Lub może używać zagregowanych lub domyślna rynku dla wyników zamiast podanego.<br /><br /> Należy używać tego parametru zapytania i `Accept-Language` parametr zapytania tylko wtedy, gdy należy określić w wielu językach; w przeciwnym razie należy użyć `mkt` i `setLang` parametry zapytania.<br /><br /> Ten parametr i [mkt](#mkt) parametr zapytania wykluczają się wzajemnie&mdash;nie określać jednocześnie.|Ciąg|Nie|  
|<a name="mkt" />mkt|Na rynku, skąd pochodzą wyniki. <br /><br /> **Uwaga:** zachęcamy do zawsze określać na rynku, jeśli jest znany. Określanie rynku pomaga Bing Kieruj żądania i zwrócić odpowiedź odpowiednie i optymalne.<br /><br /> Ten parametr i [DW](#cc) parametr zapytania wykluczają się wzajemnie&mdash;nie określać jednocześnie.|Ciąg|Yes|  
|<a name="safesearch" />bezpieczne wyszukiwanie|Filtr używany do filtrowania zawartości dla dorosłych. Poniżej przedstawiono wartości możliwe filtru bez uwzględniania wielkości liter.<br /><ul><li>Wyłącz&mdash;zwracają stron internetowych z treści dla dorosłych tekst lub obrazy.<br /><br/></li><li>Umiarkowany&mdash;zwracają stron internetowych z treści dla dorosłych tekst, ale nie do treści dla dorosłych obrazów.<br /><br/></li><li>Ścisłe&mdash;nie zwracają stron internetowych z treści dla dorosłych tekst lub obrazy.</li></ul><br /> Wartość domyślna to średni.<br /><br /> **Uwaga:** Jeśli żądanie pochodzi z rynku zasad treści dla dorosłych tego Bing wymaga `safeSearch` równa Strict, ignoruje Bing `safeSearch` wartość i używa Strict.<br/><br/>**Uwaga:** Jeśli używasz `site:` — operator zapytań, istnieje prawdopodobieństwo, że odpowiedź może zawierać treści dla dorosłych niezależnie od tego, co `safeSearch` ustawiono parametr zapytania. Użyj `site:` tylko wtedy, gdy masz świadomość zawartości w witrynie i scenariusza obsługuje możliwość treści dla dorosłych. |Ciąg|Nie|  
|<a name="setlang" />setLang|Język na potrzeby ciągi interfejsu użytkownika. Określ język, przy użyciu ISO 639-1 literę 2 języka kodu. Na przykład kod języka na język angielski jest EN. Wartość domyślna to EN (w języku angielskim).<br /><br /> Mimo że jest to opcjonalne, należy zawsze określić język. Zwykle ustawiasz `setLang` w języku określonym przez `mkt` , chyba że użytkownik chce ciągi interfejsu użytkownika, które są wyświetlane w innym języku.<br /><br /> Ten parametr i [Accept-Language](#acceptlanguage) nagłówka wykluczają się wzajemnie&mdash;nie określać jednocześnie.<br /><br /> Ciąg interfejsu użytkownika jest ciągiem, który jest używana jako etykieta w interfejsie użytkownika. Istnieje kilka ciągów interfejsu użytkownika w obiektach odpowiedź JSON. Także łącza do właściwości Bing.com w obiektach odpowiedzi zastosować określonego języka.|Ciąg|Nie| 

### <a name="headers"></a>Nagłówki

Dostępne są następujące nagłówki, które należy określić żądanie. Nagłówki typu zawartości i Ocp-Apim-Subscription-Key są tylko nagłówki wymagane, ale powinny również obejmować agenta użytkownika, X-MSEdge-ClientID, X-MSEdge-ClientIP i X wyszukiwania lokalizacji.


|Nagłówek|Opis|  
|------------|-----------------|  
|<a name="acceptlanguage" />Zaakceptuj języka|Nagłówek żądania opcjonalne.<br /><br /> Rozdzielana przecinkami lista języków na potrzeby ciągi interfejsu użytkownika. Lista jest w porządku malejącym. Aby uzyskać więcej informacji, łącznie z oczekiwanym formatem, zobacz [specyfikacją RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i [setLang](#setlang) parametr zapytania wykluczają się wzajemnie&mdash;nie określać jednocześnie.<br /><br /> Jeśli ustawisz ten nagłówek, należy także określić [DW](#cc) parametr zapytania. Aby ustalić, do zwracania wyników na rynku, usługa Bing używa pierwszego obsługiwanego języka znajdzie się na liście i łączy ją z `cc` wartość parametru. Jeżeli lista nie zawiera obsługiwanych języków, Bing znajduje się najbliższy języku i za pomocą rynku, który obsługuje żądania lub używa zagregowane lub domyślna rynku dla wyników. Aby określić na rynku, używanym w usłudze Bing, zobacz nagłówek BingAPIs rynku.<br /><br /> Użyj tego pliku nagłówkowego i `cc` parametr zapytania tylko wtedy, gdy należy określić w wielu językach. W przeciwnym razie użyj [mkt](#mkt) i [setLang](#setlang) parametry zapytania.<br /><br /> Ciąg interfejsu użytkownika jest ciągiem, który jest używana jako etykieta w interfejsie użytkownika. Istnieje kilka ciągów interfejsu użytkownika w obiektach odpowiedź JSON. Łącza do właściwości Bing.com w obiektach odpowiedzi zastosowanie określonego języka.|  
|<a name="contenttype" />Typ zawartości|Nagłówek żądania wymagane.<br /><br />Musi być ustawione na danych — multipart/formularza i zawierają parametr granic (na przykład multipart/formularza data; granic =\<ciąg granic\>). Aby uzyskać więcej informacji, zobacz [zawartości typów formularza](#content-form-types).
|<a name="market" />Rynek BingAPIs|Nagłówek odpowiedzi.<br /><br /> Na rynku, używany przez żądanie. Formularz jest \<languageCode\>-\<countryCode\>. Na przykład: en US.|  
|<a name="traceid" />BingAPIs TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwytywać tego identyfikatora. Jeśli nie jest możliwe określić i rozwiązać problem, należy dołączyć ten identyfikator oraz inne informacje, które podasz zespołem pomocy technicznej.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Nagłówek żądania wymagane.<br /><br /> Klucz subskrypcji, który otrzymał podczas tworzenia konta dla tej usługi w [usług Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Dyrektywy pragma|Nagłówek żądania opcjonalne<br /><br /> Domyślnie Wyszukiwarka Bing zwróci zawartości w pamięci podręcznej, jeśli jest dostępny. Aby zapobiec zwracaniem buforowanych zawartości Bing, ustawić nagłówek Pragma no-cache (na przykład Pragma: nie-cache).
|<a name="useragent" />Agent użytkownika|Nagłówek żądania opcjonalne.<br /><br /> Agent użytkownika wysłał żądanie. Usługa Bing używa agenta użytkownika zapewnienie zoptymalizowane środowiska użytkowników urządzeń przenośnych. Mimo że jest to opcjonalne, zachęcamy do zawsze określać tego nagłówka.<br /><br /> Agent użytkownika powinna być ten sam ciąg, który wysyła w dowolnej przeglądarce często używane. Aby uzyskać informacji na temat agentów użytkownika, zobacz [dokumencie RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągów agenta użytkownika.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodny; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Dotyk; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 kompilacji/GINGERBREAD) AppleWebKit/533.1 (KHTML; Gecko, takich jak) w wersji/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; Procesor CPU 6_1 system operacyjny telefonu iPhone, takich jak system Mac OS X) AppleWebKit/536.26 (KHTML; Gecko, takich jak) iPhone4 Mobile/10B142; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer&mdash;Mozilla/5.0 (Windows NT 6.3; EMULATOR WOW64; Trident/7.0. Dotyk; Rv:11.0) Gecko, takich jak<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; 7_0 Procesora systemu operacyjnego, takich jak system Mac OS X) AppleWebKit/537.51.1 (takich jak aparat Gecko KHTML) w wersji/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />ClientID-X-MSEdge|Opcjonalne nagłówki żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego pliku nagłówkowego, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Bing często lotach nowe funkcje i ulepszenia i używa Identyfikatora klienta jako klucza do przypisywania ruch na różnych lotów. Jeśli nie używasz tego samego Identyfikatora klienta dla użytkownika dla wielu żądań, Bing może przypisać użytkownika do wielu lotów powodujące konflikt. Przypisywane do wielu lotów powodujące konflikt może prowadzić do środowiska użytkownika niespójne. Na przykład jeśli drugie żądanie przypisania lotu inny niż pierwszy, środowisko może być nieoczekiwany. Ponadto Bing można użyć Identyfikatora klienta, aby dostosować wyniki z Internetu na tym kliencie identyfikatory historii wyszukiwania, zapewniając bardziej zaawansowane środowisko użytkownika.<br /><br /> Usługa Bing używa tego pliku nagłówkowego również do poprawy wyników w klasyfikacji przez analizowanie aktywności generowane przez identyfikator klienta. Ulepszenia istotności ułatwić lepszą jakość wyników dostarczane przez interfejsy API usługi Bing i z kolei umożliwia za pomocą kliknięć większe konsumenta interfejsu API.<br /><br /> **Ważne:** mimo, że jest to opcjonalne, należy wziąć pod uwagę tego pliku nagłówkowego wymagane. Przechowywanie identyfikator klienta dla wielu żądań dla tego samego użytkownika końcowego lub kombinacji urządzeń umożliwia (1) interfejsu API odbiorcę, aby otrzymywać spójne środowisko użytkownika oraz (2) za pomocą kliknięć większe za pośrednictwem lepszą jakość wyników z interfejsów API Bing.<br /><br /> Dostępne są następujące reguły podstawowe użycia, które są stosowane do tego pliku nagłówkowego.<br /><ul><li>Każdy użytkownik, który korzysta z aplikacji na urządzeniu musi mieć unikatową, Bing, wygenerowany identyfikator klienta.<br /><br/>Jeśli tego pliku nagłówkowego nie zostanie uwzględniony w żądaniu, Bing generuje identyfikator i zwraca go w nagłówku odpowiedzi ClientID-X-MSEdge. Jedyną sytuacją, że nie może zawierać tego nagłówka w żądaniu po raz pierwszy, o których użytkownik używa aplikacji na tym urządzeniu.<br /><br/></li><li>**Uwaga:** należy upewnić się, że tego Identyfikatora klienta nie jest możliwym do żadnych informacji konta uwierzytelnionego użytkownika.</li><li>Dla każdego żądania interfejsu API usługi Bing, która aplikacja sprawia, że dla tego użytkownika na urządzeniu, należy użyć Identyfikatora klienta.<br /><br/></li><li>Utrwalanie identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, umożliwia upewnij się, że identyfikator jest używany we wszystkich sesjach trwały plik cookie HTTP. Nie należy używać pliku cookie sesji. Dla innych aplikacji, takich jak aplikacje mobilne należy użyć urządzenia magazynu trwałego można utrwalić identyfikatora.<br /><br/>Następnym razem użytkownik używa aplikacji na tym urządzeniu, Uzyskaj identyfikator klienta, który zostały utrwalone.</li></ul><br /> **Uwaga:** odpowiedzi Bing może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera tego pliku nagłówkowego, przechwytywanie Identyfikatora klienta i użyć jej do wszystkich kolejnych żądań Bing dla użytkownika na tym urządzeniu.<br /><br /> **Uwaga:** Jeśli dodasz X MSEdge ClientID, nie może zawierać pliki cookie w żądaniu.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nagłówek żądania opcjonalne.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> **Uwaga:** mimo, że jest to opcjonalne, zachęcamy do zawsze określać tego pliku nagłówkowego i Nagłówek X-Search-lokalizacji.<br /><br /> Adres nie zaciemniania (na przykład, zmieniając ostatni oktet 0). Polega na zaciemnianiu wyniki adres, w tym miejscu nie jest w dowolnym miejscu w pobliżu lokalizacji rzeczywistego urządzenia, co może spowodować Bing obsługująca błędnych wyników.|  
|<a name="location" />Lokalizacja w przypadku wyszukiwania X|Nagłówek żądania opcjonalne.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, które opisują lokalizację geograficzną klienta. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania i zwrócić odpowiedniej zawartości lokalnej. Określ pary klucz/wartość jako \<klucz\>:\<wartość\>. Dostępne są następujące klucze, które służy do określania lokalizacji użytkownika.<br /><br /><ul><li>lat&mdash;wymagane. Szerokość geograficzna lokalizacji klienta, w stopniach. Szerokość musi być większa lub równa-90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują Latitude południowy, a wartości dodatnich wskazują Północna geograficznej.<br /><br /></li><li>długi&mdash;wymagane. Długość geograficzna lokalizacji klienta, w stopniach. Długość geograficzna musi być większa lub równa-180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują szerokości geograficzne western, a wartości dodatnich wskazują Wschodniej szerokości geograficzne.<br /><br /></li><li>ponownie&mdash;wymagane. Promień, w metrach, który określa dokładność pozioma współrzędne. Należy przekazać wartość zwrócona przez obiekt usługi lokalizacji urządzenia. Typowe wartości może być 22m dla GPS/Wi-Fi, 380m triangulacji tower komórka i 18,000m dla odwrotnego wyszukiwania adresu IP.<br /><br /></li><li>TS&mdash;opcjonalne. Sygnatura czasowa systemu UNIX UTC z kiedy klient nie był w lokalizacji. (Sygnatura czasowa systemu UNIX jest liczba sekund od 1 stycznia 1970 r.).<br /><br /></li><li>HEAD&mdash;atrybut opcjonalny. Klient względnej pozycji lub kierunku ruchu. Określ kierunek ruchu jako stopni od 0 do 360, zliczania do ruchu wskazówek zegara względem Północna wartość true. Określ kluczy tylko wtedy, gdy `sp` klucza jest różna od zera.<br /><br /></li><li>SP&mdash;opcjonalne. Prędkość poziome (szybkość) w metrach na sekundę, które są przesyłane na urządzeniu klienckim.<br /><br /></li><li>ALT&mdash;atrybut opcjonalny. Wysokość urządzenia klienta, w metrach.<br /><br /></li><li>czy&mdash;atrybut opcjonalny. Promień, w metrach, określająca dokładność pionowych we współrzędnych. Określ ten klucz, tylko wtedy, gdy należy określić `alt` klucza.<br /><br /></li></ul> **Uwaga:** Chociaż wiele kluczy są opcjonalne, więcej informacji, które podasz, tym bardziej dokładne wyniki lokalizacji są.<br /><br /> **Uwaga:** mimo, że jest to opcjonalne, zachęcamy do zawsze określić lokalizację geograficzną użytkownika. Podanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedla precyzyjnie lokalizacji fizycznej przez użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki należy dołączyć ten nagłówek i Nagłówek X-MSEdge-ClientIP, ale co najmniej powinien być dołączany do tego pliku nagłówkowego.|

> [!NOTE] 
> Należy pamiętać, że warunków użytkowania wymaga zgodności ze wszystkimi obowiązującymi przepisami, w tym dotyczących korzystania z tych nagłówków. Na przykład w niektórych systemach prawnych, takich jak Europa, istnieją wymagania w celu uzyskania zgody użytkownika przed wprowadzeniem niektórych urządzeń śledzenia na urządzeniach użytkowników.


<a name="content-form-types" />

### <a name="content-form-types"></a>Typy zawartości formularza

Każde żądanie musi zawierać nagłówek Content-Type. Musi mieć wartość nagłówka: multipart/formularza danych; granic =\<ciąg granic\>, gdzie \<ciąg granic\> jest unikatowy, nieprzezroczysty ciąg, który identyfikuje granic danych formularza. Na przykład granic = boundary_1234 abcd.


Jeśli wyślesz wyszukiwania wizualnego token obrazu lub adres URL, poniżej przedstawiono dane formularza należy uwzględnić w treści wpisu. Dane mogą zawierać nagłówek Content-Disposition i jego `name` parametru musi być równa "knowledgeRequest." Aby uzyskać szczegółowe informacje o `imageInfo` obiektu, zobacz [żądania](#the-request).


```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Jeśli załadujesz lokalny obraz, poniżej przedstawiono dane formularza należy uwzględnić w treści wpisu. Dane mogą zawierać nagłówek Content-Disposition. Jego `name` parametru musi być równa "image" i `filename` parametru może być ustawiona na dowolny ciąg. Nagłówek Content-Type może być ustawiona na dowolny typ mime obrazu często używane. Zawartość formularza jest plik binarny obrazu. Rozmiar maksymalny obrazu, którą możesz przekazać to 1 MB. Największy szerokości lub wysokości powinna być 1 500 pikseli lub mniej.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Poniżej przedstawiono sposób określania regionu zainteresowania przekazanego obrazu.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>Przykładowe żądanie

Na poniższym obrazie przedstawiono żądanie insights pełny obraz, które przekazuje token obrazu i regionu zainteresowania. Token szczegółowe informacje można uzyskać od poprzedniego wywołania do /images/search.


```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```


## <a name="the-response"></a>Odpowiedź

W przypadku szczegółowych informacji dotyczących obrazu, odpowiedź zawiera co najmniej jeden `tags` zawierających szczegółowe informacje. `image` Pole zawiera token szczegółowe informacje dla obrazu wejściowego.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

`tags` Pole zawiera nazwę wyświetlaną i listę akcji (insights). Zawiera jeden tag `displayName` pola, który jest ustawiony na pusty ciąg. Ten tag zawiera szczegółowe informacje domyślne, takich jak strony sieci Web, który zawiera obraz, podobnych wizualnie obrazów i zakupów źródeł dla elementów znalezionych w obrazie. Ponieważ cały obraz ma znaczenie, domyślnego znacznika szczegółowych informacji nie uwzględnia blokujących pola dla regionów zainteresowania.


```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Aby uzyskać listę wglądu w szczegółowe dane domyślne, zobacz [domyślne insights](./default-insights-tag.md).



Pozostałe znaczniki zawierają inne szczegółowe informacje, które mogą być interesujące dla użytkownika. Na przykład jeśli obraz zawiera tekst, jeden tag może obejmować insight TextResults, który zawiera rozpoznanego tekstu. Lub, jeśli Bing rozpoznaje jednostki (osoby, miejsca lub rzeczy) w obrazie, jeden tag identyfikują jednostkę. Wyszukiwanie wizualne również zwraca zestaw warunków (tagi) pochodzące z obrazu wejściowego. Te znaczniki pozwalają użytkownikom Eksploruj pojęcia znalezione na obrazie. Na przykład jeśli obrazu wejściowego jest sławę wykresie, jeden tag może być dyscyplin sportowych, który zawiera linki do obrazów sportu.

Każdy znacznik zawiera nazwę wyświetlaną, która służy do kategoryzowania wgląd, obwiedni identyfikujący regionu zainteresowania, które dotyczą wiedzę, samych szczegółowych danych i miniaturę obrazu. Na przykład jeśli obraz jest osoby na sobie jersey dyscyplin sportowych, jeden tag obejmują obwiedni granic rękaw, która obejmuje VisualSearch i ProductVisualSearch insights. I inny znacznik może zawierać szczegółowe informacje o ImageResults, który zawiera adres URL żądania interfejsu API /images/search można pobrać obrazów, które są związane z badany oddziałuje przez czas lub adres URL wyszukiwania Bing.com, który powoduje otwarcie wyniki wyszukiwania obrazu Bing.com.

Wszystkie tagi innej niż domyślna etykieta szczegółowe informacje obejmują blokujących pola, które identyfikują regionów zainteresowania na obrazie. Na przykład jeśli obraz zawiera wiele osób rozpoznany, tagi mogą obejmować blokujących pola dla każdej osoby lub jeśli obraz zawiera elementy rozpoznane odzieży, tagi mogą obejmować blokujących pola dla każdego elementu odzieży rozpoznane. Obwiedni służy do tworzenia punktów aktywnych na obrazie, po kliknięciu, zawierają szczegółowe informacje o zawartości w danym regionie obrazu. Zaprojektuj nie może zawierać do obrazu dla obwiedni, które identyfikują całego obrazu.

### <a name="text-recognition"></a>Rozpoznawanie tekstu

Jeśli obraz zawiera tekst, który usługa rozpozna, jeden tag będzie zawierać szczegółowe informacje TextResults (działanie). Szczegółowe informacje o `displayName` zawiera rozpoznany tekst. 

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Ponieważ tag `displayName` pole zawiera ##TextRecognition, nie należy używać jako tytuł kategorii w najlepsze środowisko użytkownika. Zbliża się do wyświetlania żadnych nazw, które zaczyna się od ##. Zamiast tego należy użyć nazwy wyświetlania akcji.


Rozpoznawanie tekstu można także rozpoznaje informacje kontaktowe wizytówki, takich jak numery telefonów i adresów e-mail. Pole określa lokalizację informacji kontaktowych przy użyciu tej karty. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Jeśli obraz zawiera rozpoznanego jednostki, takie jak osoby, miejsca lub rzeczy, jeden tag może obejmować wglądu jednostkę. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
      },
      "displayName" : "Statue of Liberty",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        }
      },
      "actions" : [
        {
          "_type" : "ImageEntityAction",
          "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
          "displayName" : "Statue of Liberty",
          "actionType" : "Entity",
        }
      ]
    }
```



## <a name="next-steps"></a>Kolejne kroki

Aby szybko rozpocząć pracę z pierwszego żądania, zobacz artykuł Szybki Start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

Wypróbuj interfejs API. Przejdź do [konsoli testowania interfejs API wyszukiwania wizualnego](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Zapoznaj się z [Visual dokumentacja interfejsu API wyszukiwania](https://aka.ms/bingvisualsearchreferencedoc). Dokumentacja zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach wyników wyszukiwania. Zawiera także definicje obiektów odpowiedzi. 

Nie zapomnij przeczytać [wymagań w zakresie korzystania z usługi Bing i wyświetlania danych z niej](./use-and-display-requirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników wyszukiwania.


