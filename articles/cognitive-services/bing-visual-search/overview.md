---
title: Omówienie Visual API wyszukiwania usługi Bing | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Pokazuje, jak można pobrać szczegółów lub szczegółowych informacji o obrazie, taką jak obrazy podobne lub zakupów źródeł.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 95f10d8ea7ebe1d40d45231a8ea40df81543fe8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349124"
---
# <a name="what-is-bing-visual-search-api"></a>Co to jest Visual API wyszukiwania usługi Bing?

Visual API wyszukiwania usługi Bing udostępnia środowisko podobne do szczegółów obraz wyświetlany na Bing.com/images. Visual wyszukiwania możesz Przekaż obraz i wrócić szczegółowych informacji o obrazie, takich jak obrazy wizualnie podobne, zakupów źródeł, stron sieci Web, który obejmuje obraz i inne. Zamiast przekazywanie obrazu, można też podać token wgląd, której można korzystać z obrazu w wynikach wyszukiwania obrazów (zobacz [interfejsu API Bing obrazów](../bing-image-search/overview.md)).

Visual wyszukiwania można zidentyfikować znanych osób, pomników i punkty orientacyjne, kompozycji, wyposażenie macierzystego, sposób, produktów, rozpoznawanie znaków (Rozpoznawania) i inne.

Poniżej przedstawiono szczegółowe informacje, że Visual wyszukiwania umożliwia odnajdywanie.

- Wizualne podobne obrazy&mdash;listy obrazów, które wizualnie podobne do obrazu wejściowego
- Wizualne podobnych produktów&mdash;listy obrazów, które zawierają produktów, które wizualnie podobne do produktu wskazanego wejściowego obrazu
- Zakupy źródeł&mdash;listę miejsca, w którym można kupić elementu pokazywanego w obrazu wejściowego
- Powiązane wyszukiwania&mdash;listę wyszukiwań pokrewnych wprowadzone przez innych użytkowników lub które są oparte na zawartość obrazu
- Strony sieci Web, które zawierają obraz&mdash;lista stron sieci Web, który obejmuje obrazu wejściowego
- Przepisami&mdash;lista stron sieci Web, obejmujących przepisami dokonywania płytkę pokazano obrazu wejściowego

Oprócz tych insights Visual wyszukiwania zwraca zestaw warunków (tagów) pochodzące z obrazu wejściowego. Znaczniki umożliwiają użytkownikom Eksploruj pojęcia znaleziono w obrazie. Na przykład w przypadku obrazu wejściowego Słynne atlety, jeden tag może być nazwą Atleta, innego tagu może być sportowych. Lub w przypadku obrazu wejściowego koła firmy apple, znaczniki może być desery kołowego firmy Apple, placki, więc użytkowników można eksplorować pojęcia pokrewne.

Wyniki wyszukiwania Visual także ograniczenia pól dla regionów zainteresowanie obrazu. Na przykład jeśli obraz zawiera kilka znanych osób, wyniki mogą obejmować ograniczenia pola dla każdego z rozpoznanym znanych osób w obrazie. Lub, jeśli Bing rozpoznaje produktu lub odzieży w obrazie, wynik może zawierać obwiedni rozpoznany produkt lub odzieży elementu.

> [!IMPORTANT]
> Jeśli używasz/obrazów/szczegóły punkt końcowy do [uzyskiwanie szczegółowych informacji obrazu](../bing-image-search/image-insights.md), należy zaktualizować swój kod, aby zamiast tego użyj Visual wyszukiwania, ponieważ zapewnia bardziej szczegółowe informacje na temat technologii.


## <a name="the-request"></a>Żądanie

Dostępne są następujące opcje dla uzyskiwanie szczegółowych informacji o obrazie. 

- Wyślij token wgląd, której można korzystać z obrazu w poprzedniego wywołania do jednego z [interfejsu API Bing obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) punkty końcowe
- Wyślij adres URL obrazu
- Przekazywanie obrazu (binary)


W przypadku wysłania Visual wyszukiwania token obrazu lub adres URL, poniżej przedstawiono obiekt JSON, który musi zawierać w treści POST. 

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

`imageInfo` Musi zawierać obiekt `url` i `imageInsightsToken` pole, ale nie oba. Ustaw `url` pola Adres URL obrazu niedostępna Internet. Rozmiar maksymalny obsługiwany obraz to 1 MB.

`imageInsightsToken` Musi mieć wartość tokenu szczegółowych informacji. Aby uzyskać token szczegółowe informacje, należy wywołać interfejs API obrazu Bing. Odpowiedź zawiera listę `Image` obiektów. Każdy `Image` obiekt zawiera `imageInsightsToken` pola, które zawiera token.

`cropArea` Pole jest opcjonalne. Obszar kadrowania Określa górny, lewym rogu i dolny, prawym rogu obszaru zainteresowania. Określ wartości w zakresie od 0,0 do 1,0. Dopuszczalne wartości to procent całkowitej szerokość lub wysokość. Na przykład powyższy przykład oznacza prawej połowy obrazu jako obszaru zainteresowania. Dołącz ją, jeśli chcesz ograniczyć żądanie wgląd do obszaru zainteresowania.

`filters` Obiektu zawiera filtr lokacji (zobacz `site` pole) czy można użyć do ograniczenia podobne obrazy i podobnych produktów wyników do określonej domeny. Na przykład, jeśli obraz jest Surface Book, możesz ustawić `site` do www.microsoft.com. 

Jeśli chcesz uzyskać szczegółowe informacje o kopii lokalnej obrazu, Przekaż obraz jako dane binarne.

Aby uzyskać szczegółowe informacje o tym w treści POST, zobacz [formularza Typy zawartości](#content-form-types).


### <a name="endpoint"></a>Endpoint

Punkt końcowy Visual wyszukiwania jest: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Należy wysłać żądania jako tylko żądania HTTP POST. 


### <a name="query-parameters"></a>Parametry zapytania

Poniżej przedstawiono parametrów zapytania, które należy określić żądanie. Co najmniej powinna zawierać `mkt` parametr zapytania.

|Name (Nazwa)|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|  
|<a name="cc" />DW|Kod kraju 2 kraju, skąd wyniki.<br /><br /> Jeśli ten parametr zostanie ustawiony, należy także określić [Accept-Language](#acceptlanguage) nagłówka. Bing używa pierwszego obsługiwanych języków znajduje się na liście języków i łączy z kod kraju, który wskazuje, na rynek do zwracania wyników z języka. Jeśli języki zawierają obsługiwanych języków, Bing znajduje się najbliżej języka i rynku, która obsługuje żądania. Lub może używać zagregowanych lub domyślna rynku wyniki zamiast określony.<br /><br /> Należy używać tego parametru zapytania i `Accept-Language` parametr zapytania tylko wtedy, gdy można określić wiele języków; w przeciwnym razie należy użyć `mkt` i `setLang` parametry zapytania.<br /><br /> Ten parametr i [mkt](#mkt) parametru zapytania wykluczają się wzajemnie&mdash;nie podawaj jednocześnie.|Ciąg|Nie|  
|<a name="mkt" />mkt|Rynku, skąd wyniki. <br /><br /> **Uwaga:** są zdecydowanie zaleca się zawsze podać na rynku, jeśli znane. Określanie dostępnych na rynku pomaga Bing trasy żądania i odpowiednie i optymalne odpowiedź zwrócona.<br /><br /> Ten parametr i [DW](#cc) parametru zapytania wykluczają się wzajemnie&mdash;nie podawaj jednocześnie.|Ciąg|Yes|  
|<a name="safesearch" />bezpieczne wyszukiwanie|Filtr używane do filtrowania zawartość dla dorosłych. Poniżej przedstawiono wartości możliwe filtru bez uwzględniania wielkości liter.<br /><ul><li>Wyłącz&mdash;zwracać stron sieci Web z dorosłych tekstu lub obrazów.<br /><br/></li><li>Umiarkowany&mdash;zwracać stron sieci Web z dorosłych tekstu, ale nie dla dorosłych obrazów.<br /><br/></li><li>Strict&mdash;nie zwracać stron sieci Web z dorosłych tekstu lub obrazów.</li></ul><br /> Wartość domyślna to umiarkowany.<br /><br /> **Uwaga:** Jeśli żądanie pochodzi z rynku zasad dla dorosłych tego Bing wymaga `safeSearch` można ustawić Strict, ignoruje Bing `safeSearch` wartość i używa Strict.<br/><br/>**Uwaga:** użycie `site:` — operator zapytań, istnieje ryzyko, że odpowiedź może zawierać zawartość dla dorosłych niezależnie od tego, co `safeSearch` ustawiono parametr zapytania. Użyj `site:` tylko wtedy, gdy masz świadomość zawartości w witrynie i scenariusz obsługuje możliwości zawartość dla dorosłych. |Ciąg|Nie|  
|<a name="setlang" />setLang|Język do użycia dla ciągów interfejsu użytkownika. Wybierz język, przy użyciu obrazu ISO 639 1 2-znakowy kod języka. Na przykład kod język angielski jest EN. Wartość domyślna to EN (angielski).<br /><br /> Mimo że jest to opcjonalne, należy zawsze określić język. Zwykle ustawić `setLang` w języku określonym przez `mkt` , chyba że użytkownik będzie chciał ciągów interfejsu użytkownika, które są wyświetlane w innym języku.<br /><br /> Ten parametr i [Accept-Language](#acceptlanguage) nagłówka wykluczają się wzajemnie&mdash;nie podawaj jednocześnie.<br /><br /> Ciąg interfejsu użytkownika jest ciągiem, który jest używana jako etykieta w interfejsie użytkownika. Istnieje kilka ciągów interfejsu użytkownika w obiektach odpowiedzi JSON. Ponadto łącza do właściwości Bing.com w obiektach odpowiedzi zastosowania określonego języka.|Ciąg|Nie| 

### <a name="headers"></a>Nagłówki

Poniżej przedstawiono nagłówki, które należy określić żądanie. Nagłówki typu zawartości i Ocp-Apim-subskrypcji-klucza są tylko wymagane nagłówki, ale należy także uwzględnić agenta użytkownika, X-MSEdge-ClientID X-MSEdge-ClientIP i lokalizację-X-wyszukiwania.


|Nagłówek|Opis|  
|------------|-----------------|  
|<a name="acceptlanguage" />Zaakceptuj języka|Nagłówek żądania opcjonalne.<br /><br /> Rozdzielana przecinkami lista języków dla ciągów interfejsu użytkownika. Lista jest w porządku malejącym. Więcej informacji, łącznie z oczekiwanego formatu [specyfikacją RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i [setLang](#setlang) parametru zapytania wykluczają się wzajemnie&mdash;nie podawaj jednocześnie.<br /><br /> Jeśli ustawisz ten nagłówek, należy także określić [DW](#cc) parametr zapytania. Ustalenie dostępnych na rynku wyników dla Bing używa pierwszego obsługiwanych języków znajduje się na liście i łączy go z `cc` wartość parametru. Jeśli lista nie zawiera obsługiwanych języków, Bing znajduje się najbliżej języka i rynku, która obsługuje żądania lub używa zagregowane lub domyślna rynku dla wyników. Aby określić rynku Bing używany, zobacz nagłówek BingAPIs rynku.<br /><br /> Używają tego nagłówka i `cc` parametr zapytania tylko wtedy, gdy można określić wiele języków. W przeciwnym razie użyj [mkt](#mkt) i [setLang](#setlang) parametry zapytania.<br /><br /> Ciąg interfejsu użytkownika jest ciągiem, który jest używana jako etykieta w interfejsie użytkownika. Istnieje kilka ciągów interfejsu użytkownika w obiektach odpowiedzi JSON. Łącza do właściwości Bing.com w obiektach odpowiedzi zastosowania określonego języka.|  
|<a name="contenttype" />Typ zawartości|Nagłówek żądania wymagane.<br /><br />Musi mieć ustawioną multipart/dane formularza i parametr granic (na przykład dane multipart/formularza; boundary =\<ciąg granic\>). Aby uzyskać więcej informacji, zobacz [formularza Typy zawartości](#content-form-types).
|<a name="market" />BingAPIs rynku|Nagłówek odpowiedzi.<br /><br /> Rynku używany przez żądanie. Formularz jest \<atrybutu languageCode\>-\<countryCode\>. Na przykład en US.|  
|<a name="traceid" />BingAPIs TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegóły żądania. Gdy wystąpi błąd, przechwycić tego identyfikatora. Jeśli nie jest możliwe określić i rozwiązać problem, należy uwzględnić ten identyfikator wraz z innymi informacjami, musisz zapewnić zespołem pomocy technicznej.|  
|<a name="subscriptionkey" />OCP-Apim subskrypcji — klawisz|Nagłówek żądania wymagane.<br /><br /> Klucz subskrypcji, odebrany po zarejestrowaniu dla tej usługi w [kognitywnych usług](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Wartość dyrektywy pragma|Nagłówek żądania opcjonalne<br /><br /> Domyślnie Bing zwraca zawartości w pamięci podręcznej, jeśli jest dostępna. Aby zapobiec Bing zwracanie zawartości w pamięci podręcznej, ustaw nagłówek Pragma no-cache (na przykład Pragma: nie-cache).
|<a name="useragent" />Agent użytkownika|Nagłówek żądania opcjonalne.<br /><br /> Agent użytkownika wysłał żądanie. Bing używa agenta użytkownika, aby zapewnić użytkownikom mobilnym zoptymalizowane środowisko. Mimo że jest to opcjonalne, zachęca się zawsze określić tego nagłówka.<br /><br /> Agent użytkownika powinna być tego samego ciąg, który wysyła dowolnej przeglądarki często używane. Informacje o agentów użytkownika, zobacz [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągi agentów użytkownika.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodny; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 kompilacji/PIERNIKI) AppleWebKit/533.1 (KHTML; aparatu Gecko, takich jak) wersji i 4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; Procesor CPU 6_1 iPhone systemu operacyjnego Mac OS x) AppleWebKit/536.26 (KHTML; aparatu Gecko, takich jak) iPhone4 Mobile/10B142; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer PC&mdash;Mozilla/5.0 (system Windows NT 6.3; EMULATOR WOW64; Trident/7.0. Touch; Rv:11.0) aparatu Gecko, takich jak<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; 7_0 Procesora systemu operacyjnego, takich jak system Mac OS X) AppleWebKit/537.51.1 (takich jak aparat Gecko KHTML) w wersji/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Opcjonalne nagłówki żądań i odpowiedzi.<br /><br /> Bing używa tego nagłówka użytkownikom zachowanie spójności w wywołaniach interfejsu API usługi Bing. Bing często lotach nowe funkcje i ulepszenia, i używa Identyfikatora klienta jako klucza do przypisywania ruchu w różnych lotach. Jeśli nie używasz tego samego Identyfikatora klienta dla użytkownika dla wielu żądań przesyłanych, Bing może przypisać użytkownika do wielu lotach powodujące konflikt. Przypisane do wielu lotach powodujące konflikt może spowodować niespójne użytkowników. Na przykład jeśli drugi żądania przypisaniem transmitowane innego niż pierwszy, środowisko użytkownika może być nieoczekiwany. Ponadto Bing można użyć Identyfikatora klienta, aby dostosować wyniki sieci web na tym kliencie identyfikatory historii wyszukiwania, podając bardziej rozbudowane środowisko użytkownika.<br /><br /> Bing również używa tego nagłówka do poprawy wyników klasyfikacji za analizowanie aktywności generowane przez identyfikator klienta. Istotne ulepszenia pomoc lepszą jakość wyniki dostarczone przez interfejsy API usługi Bing i z kolei umożliwia kliknięć większe dla klienta interfejsu API.<br /><br /> **Ważne:** mimo, że jest to opcjonalne, należy rozważyć ten wymagany nagłówek. Dla tego samego użytkownika końcowego i połączeniu urządzenia z trwałym identyfikator klienta dla wielu żądań przesyłanych umożliwia 1) konsumenta interfejsu API odbierać spójną obsługę użytkowników i 2) kliknięć większe za pośrednictwem lepszą jakość wyników z interfejsów API usługi Bing.<br /><br /> Poniżej przedstawiono zasady podstawowe sposoby użycia, które są stosowane do tego nagłówka.<br /><ul><li>Każdy użytkownik, który używa aplikacji na urządzeniu musi mieć unikatową, Bing wygenerowany identyfikator klienta.<br /><br/>Jeśli nie obejmują tego nagłówka w żądaniu Bing generuje identyfikator i zwraca go w nagłówka X-MSEdge-ClientID odpowiedzi. Tylko wtedy, że nie należy używać tego nagłówka w żądaniu po raz pierwszy użytkownik korzysta z aplikacji na tym urządzeniu.<br /><br/></li><li>**Uwaga:** musi upewnij się, że tego Identyfikatora klienta nie jest możliwym do żadnych informacji o kontach uwierzytelnionego użytkownika.</li><li>Dla każdego żądania interfejsu API Bing sprawia, że aplikacja dla tego użytkownika na urządzeniu, należy użyć Identyfikatora klienta.<br /><br/></li><li>Utrwalanie identyfikator klienta. Aby zachować identyfikator w aplikacji przeglądarki, umożliwia upewnij się, że ten identyfikator jest używany we wszystkich sesjach trwały plik cookie HTTP. Nie należy używać pliku cookie sesji. Dla innych aplikacji, takich jak aplikacje mobilne Użyj urządzenia magazynu trwałego do utrzymania identyfikator.<br /><br/>Następnym razem, użytkownik korzysta z aplikacji na tym urządzeniu pobieranie Identyfikatora klienta, które zostały utrwalone.</li></ul><br /> **Uwaga:** Bing odpowiedzi może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera ten nagłówek, przechwytywania identyfikator klienta i użyć jej do wszystkich kolejnych żądań Bing dla użytkownika na tym urządzeniu.<br /><br /> **Uwaga:** Jeśli dołączysz X MSEdge ClientID nie może zawierać pliki cookie w żądaniu.|  
|<a name="clientip" />X-MSEdge-ClientIP|Nagłówek żądania opcjonalne.<br /><br /> Adres IPv4 lub IPv6 urządzenia klienckiego. Adres IP jest używany do odnajdywania lokalizacji użytkownika. Bing używa informacji o lokalizacji w celu określenia działanie wyszukiwania bezpieczne.<br /><br /> **Uwaga:** mimo, że jest to opcjonalne, możesz zachęcamy do zawsze podać tego nagłówka i nagłówka X wyszukiwania lokalizacji.<br /><br /> Adres nie zasłaniają (na przykład, zmieniając ostatni oktet 0). Obfuscating wyników adresu w tej lokalizacji nie jest dowolne miejsce w pobliżu urządzenia rzeczywistej lokalizacji, co może spowodować Bing obsługująca błędne wyniki.|  
|<a name="location" />X wyszukiwania lokalizacji|Nagłówek żądania opcjonalne.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, opisujących lokalizacji geograficznej klienta. Bing używa informacji o lokalizacji określają zachowanie wyszukiwania bezpieczne i zwracać odpowiedniej zawartości lokalnej. Określ parę klucz wartość jako \<klucza\>:\<wartość\>. Poniżej przedstawiono kluczy, które umożliwia określenie lokalizacji użytkownika.<br /><br /><ul><li>lat&mdash;wymagane. Szerokość lokalizacji klienta w stopniach. Zakres musi być większa lub równa-90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują geograficznej Terytoria Południowe i liczbę wartości dodatnich wskazują geograficznej północnej.<br /><br /></li><li>długie&mdash;wymagane. Wysokość lokalizacji klienta w stopniach. Wysokość musi być większa lub równa-180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują western szerokości geograficzne i liczbę wartości dodatnich wskazują Wschodniej szerokości geograficzne.<br /><br /></li><li>re&mdash;wymagane. Promień, w liczników, który określa poziomy dokładność współrzędne. Należy przekazać wartość zwrócona przez usługę lokalizacji urządzenia. Typowe wartości może być 22 MB dla GPS/Wi-Fi, 380m dla komórki wieża triangulacji i 18,000m dla wyszukiwanie wsteczne adresu IP.<br /><br /></li><li>TS&mdash;opcjonalne. Czas UTC UNIX sygnatura czasowa gdy klient nie był w tej lokalizacji. (Sygnatura czasowa UNIX jest liczba sekund od 1 stycznia 1970).<br /><br /></li><li>HEAD&mdash;opcjonalne. Nagłówek względną lub kierunek ruchu klienta. Określ kierunek ruchu jako stopni od 0 do 360 zliczania do ruchu wskazówek zegara względem Północna wartość true. Określ kluczy tylko wtedy, gdy `sp` klucz jest różna od zera.<br /><br /></li><li>SP&mdash;opcjonalne. Prędkość pozioma (szybkość) metry na sekundę, które urządzenia klienckie są przesyłane.<br /><br /></li><li>ALT&mdash;opcjonalne. Wysokość urządzenia klienta, w liczników.<br /><br /></li><li>są&mdash;opcjonalne. Promień, metry, określająca dokładność pionowy współrzędne. Określ ten klucz, tylko jeśli określisz `alt` klucza.<br /><br /></li></ul> **Uwaga:** mimo że wiele kluczy są opcjonalne, więcej informacji, które zostaną podane, są bardziej dokładne wyniki lokalizacji.<br /><br /> **Uwaga:** mimo, że jest to opcjonalne, jesteś powinni zawsze określać położenie geograficzne użytkownika. Dostarczanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedlają dokładnie fizyczną lokalizację użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki tego nagłówka i nagłówka X-MSEdge-ClientIP należy uwzględnić, ale co najmniej powinna zawierać tego nagłówka.|

> [!NOTE] 
> Należy pamiętać, że warunki użytkowania wymagają zgodności z wszelkimi właściwymi prawami, w tym dotyczące tych nagłówków. Na przykład w niektórych krajach, takich jak Europy, istnieją wymagania, aby uzyskać zgodę użytkownika przed wprowadzeniem niektórych urządzeń śledzenia na urządzeniach użytkowników.


<a name="content-form-types" />

### <a name="content-form-types"></a>Typy zawartości formularza

Każde żądanie musi zawierać nagłówek Content-Type. Musi mieć wartość nagłówka: multipart/dane formularza; granic =\<ciąg granic\>, gdzie \<ciąg granic\> jest unikatowy, nieprzezroczyste ciąg identyfikujący granic danych formularza. Na przykład granic = boundary_1234 abcd.


W przypadku wysłania Visual wyszukiwania token obrazu lub adres URL, poniżej przedstawiono dane formularza należy uwzględnić w treści POST. Dane formularza musi zawierać nagłówek Content-Disposition i jego `name` parametr musi być ustawiony na "knowledgeRequest". Aby uzyskać więcej informacji o `imageInfo` obiektów, zobacz [żądania](#the-request).


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

Jeśli Przekaż obraz lokalnych, poniżej przedstawiono dane formularza należy uwzględnić w treści POST. Dane formularza musi zawierać nagłówek Content-Disposition. Jego `name` parametr musi być ustawiony na "obrazu" i `filename` parametr może być ustawiony na dowolny ciąg. Nagłówek Content-Type może być ustawiona na dowolny typ mime obrazu często używane. Zawartość formularza jest plikiem binarnym obrazu. Rozmiar maksymalny obrazu, które mogą przekazać to 1 MB. 


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Poniżej przedstawiono sposób określania obszaru zainteresowania załadowanego obrazu.

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

Poniższy kod przedstawia pełny obraz żądanie szczegółowe informacje, które przekazuje token obrazu i region odsetek. Token szczegółowe informacje można uzyskać od poprzedniego wywołania /images/search.


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

Jeśli szczegółowe informacje są dostępne dla obrazu, odpowiedź zawiera co najmniej jeden `tags` zawierających szczegółowych danych. `image` Pole zawiera token insights dla obrazu wejściowego.

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

`tags` Pole zawiera nazwę wyświetlaną i listę akcji (szczegółowe informacje). Jeden tag zawiera `displayName` pola, które jest ustawiony na pusty ciąg. Ten tag zawiera domyślne szczegółowych danych takich jak strony sieci Web obejmują obrazu, wizualnie podobne obrazy i zakupów źródeł dla elementów znalezionych w obrazie. Ponieważ całego obrazu jest przydatne, domyślny znacznik insights nie zawiera ograniczenia pola regionów odsetek.


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

Listę szczegółowych danych domyślnego, zobacz [domyślne insights](./default-insights-tag.md).



Pozostałe tagi zawierają inne szczegółowe informacje, które mogą być przydatne dla użytkownika. Na przykład jeśli obraz zawiera tekst, jeden tag może obejmować insight TextResults, który zawiera rozpoznany. Lub, jeśli Bing rozpoznaje jednostki (osoby, miejsca lub operacją) w obrazie, jeden tag identyfikują jednostkę. Wyszukiwanie Visual również zwraca zestaw warunków (tagów) pochodzące z obrazu wejściowego. Znaczniki umożliwiają użytkownikom Eksploruj pojęcia znaleziono w obrazie. Na przykład w przypadku obrazu wejściowego Słynne atlety, jeden tag może być sportowych, który zawiera łącza do obrazów sportowych.

Każdy znacznik zawiera nazwy wyświetlanej, która umożliwia klasyfikowanie wgląd, obwiedni identyfikujący obszaru zainteresowania, którego dotyczy wiedzę, same szczegółowych danych i miniaturę obrazu. Na przykład jeśli obraz jest osoby sobie jersey sportowych, jeden tag może zawierać obwiedni zakresem jersey i zawierający VisualSearch i ProductVisualSearch szczegółowych informacji. I innego tagu może obejmować szczegółowe informacje o ImageResults, który zawiera adres URL dla żądania interfejsu API /images/search można pobrać obrazów, które są powiązane podane naskórnie lub Bing.com wyszukiwania pobierającej użytkownikowi wyniki wyszukiwania Bing.com obrazu.

Wszystkie tagi innego niż domyślny znacznik insights obejmują ograniczenia pól, których określenie obszarów zainteresowania obrazu. Na przykład jeśli obraz zawiera wiele osób rozpoznany, tagi mogą zawierać ograniczenia pól dla każdej osoby, lub jeśli obraz zawiera elementy odzieży rozpoznany, tagi mogą zawierać ograniczenia pola dla każdego elementu rozpoznanym odzieży. Obwiedni służy do tworzenia punkty aktywne na obrazie która po kliknięciu, podaj szczegóły dotyczące zawartości w tym regionie obrazu. Punkty aktywne nie może zawierać obrazu dla obwiedni, które identyfikują całego obrazu.

### <a name="text-recognition"></a>Rozpoznawanie tekstu

Jeśli obraz zawiera tekst, który usługa rozpozna, jeden tag będzie zawierał TextResults szczegółowe informacje o (Akcja). Szczegółowe informacje o `displayName` zawiera rozpoznany. 

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

Ponieważ znacznika `displayName` pole zawiera ##TextRecognition nie używaj go jako kategorię tytuł w UX. Zbliża się do wyświetlania dowolnej nazwy, która rozpoczyna się od ##. Zamiast tego użyj nazwy wyświetlania akcji.


Rozpoznawanie tekstu może rozpoznawać również informacje kontaktowe kart biznesowej, takich jak numery telefonów i adresów e-mail. Pole określa lokalizację informacji kontaktowych na karcie. 

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

Jeśli obraz zawiera rozpoznanym jednostki takie jak osoby, miejsca lub operacją, jeden tag może obejmować wglądu jednostki. Jednostek może również zawierać elementy towarzyszące składni, jak pokazano w poniższym przykładzie:

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
        },
        {
          "_type" : "ImageModuleAction",
          "actionType" : "Trivia",
          "data" : {
            "value" : [
              {
                "name" : "Where was the cornerstone of the statue of liberty laid",
                "text" : "<the answer>",
                "hostPageUrl" : "http:\/\/contoso.com\/history\/...",
              },
              {
                "name" : "Why Is the Statue of Liberty Green",
                "text" : "<the answer>",
                "hostPageUrl" : "https:\/\/www.contoso.com\/why-statue-of-liberty-is-green",
              },
              {
                "name" : "What is the Statue of Liberty made of",
                "text" : "<the answer>",
                "hostPageUrl" : "https:\/\/www.contoso.com\/art-literature\/statue-liberty-made",
              }
            ]
          }
        }
      ]
    }
```



## <a name="next-steps"></a>Kolejne kroki

Aby szybko rozpocząć pracę z pierwszego żądania, zobacz poradniki Szybki Start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

Wypróbuj interfejsu API. Przejdź do [konsoli testowania Visual wyszukiwania interfejsu API](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Zapoznaj się z [dokumentacja interfejsu API języka Visual wyszukiwania](https://aka.ms/bingvisualsearchreferencedoc). Odwołanie zawiera listę punktów końcowych, nagłówki i parametry zapytania, które ma zostać użyty do żądania w wynikach wyszukiwania. Zawiera także definicje obiektów odpowiedzi. 

Należy przeczytać [Bing oraz wymagania dotyczące wyświetlania](./use-and-display-requirements.md) , nie zostanie przerwane dowolne zasady dotyczące korzystania z wyników wyszukiwania.


