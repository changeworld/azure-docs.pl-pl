---
title: Wysyłanie kwerend wyszukiwania do interfejsu API wyszukiwania wizualnego Bing
titlesuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat parametrów interfejsu API REST używany w interfejsie API wyszukiwania wizualnego Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 4/03/2019
ms.author: aahi
ms.openlocfilehash: 7c6fda2238aa53c4dc1a0f15ef1aaee263e4a8f8
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489352"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Wysyłanie kwerend wyszukiwania do interfejsu API wyszukiwania wizualnego Bing

W tym artykule opisano parametry i atrybuty liczby żądań wysyłanych do interfejsu API wyszukiwania wizualnego Bing, a także obiekt odpowiedzi.

Możesz uzyskać szczegółowe informacje o pliku obrazu na trzy sposoby:

- przy użyciu tokenu szczegółowe informacje, który można pobrać z obrazu w poprzedniego wywołania do jednego z [interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) punktów końcowych.
- Wysyłanie adres URL obrazu.
- Przekazywanie obrazu (w formacie binarnym).

## <a name="bing-visual-search-requests"></a>Żądania wyszukiwania wizualnego Bing

W przypadku wysłania wyszukiwania wizualnego token obrazu lub adres URL poniższy fragment kodu przedstawia obiekt JSON, który musi zawierać w treści wpisu:

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

Obiekt `imageInfo` musi zawierać pole `url` lub pole `imageInsightsToken`, ale nie oba jednocześnie. Ustaw `url` pola Adres URL obrazu dostępne za pośrednictwem Internetu. Maksymalny obsługiwany rozmiar obrazu wynosi 1 MB.

W polu `imageInsightsToken` musi być ustawiony token szczegółowych informacji. Aby uzyskać token szczegółowych informacji, wywołaj interfejs API wyszukiwania obrazów Bing. Odpowiedź zawiera listę obiektów `Image`. Każdy obiekt `Image` zawiera pole `imageInsightsToken`, które zawiera token.

Pole `cropArea` jest opcjonalne. Obszar kadrowania określa lewym górnym rogu i prawym dolnym rogu regionu zainteresowania. Określ wartości z zakresu od 0,0 do 1,0. Wartości te są procentem ogólnej szerokości lub wysokości. W powyższym przykładzie jako region zainteresowania określono prawą połowę obrazu. Uwzględnij je, jeśli chcesz ograniczyć żądanie szczegółowych informacji do regionu zainteresowania.

Obiekt `filters` zawiera filtr witryny (patrz pole `site`), którego możesz użyć, aby ograniczyć wyniki podobnych obrazów i podobnych produktów do określonej domeny. Jeśli na przykład obraz przedstawia książkę Surface Book, dla obiektu `site` możesz ustawić wartość www.microsoft.com.

Jeśli chcesz uzyskać szczegółowe informacje o lokalnej kopii obrazu, przekaż obraz w formie danych binarnych.

Aby uzyskać informacje o uwzględnianiu tych opcji w treści żądania POST, zobacz [Typy formularzy zawartości](#content-form-types).

### <a name="search-endpoint"></a>Punkt końcowy wyszukiwania

Punkt końcowy wyszukiwania wizualnego jest następujący: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Żądania należy wysyłać tylko jako żądania HTTP POST.

### <a name="query-parameters"></a>Parametry zapytania

Poniżej przedstawiono parametry zapytania, które należy określić w żądaniu. Jako minimum, należy uwzględnić `mkt` parametru zapytania:

| Name (Nazwa) | Wartość | Typ | Wymagane |
| --- | --- | --- | --- |
| <a name="cc" />cc  | Kod kraju, który reprezentuje, skąd pochodzą wyniki.<br /><br /> Jeśli ustawisz ten parametr, musisz także określić nagłówek [Accept-Language](#acceptlanguage). Usługa Bing używa pierwszego obsługiwanego języka znalezionego na liście języków i łączy ten język z określonym kodem kraju, aby ustalić rynek, z którego mają być zwrócone wyniki. Jeśli lista języków nie zawiera obsługiwanego języka, usługa Bing znajduje najbliższy język i rynek, które obsługują żądanie. Może też używać rynku zagregowanego lub domyślnego, zamiast podanego.<br /><br /> Tego parametru zapytania i parametru `Accept-Language` należy używać tylko wtedy, gdy jest określanych wiele języków. W przeciwnym razie należy użyć parametrów zapytania `mkt` i `setLang`.<br /><br /> Ten parametr i parametr zapytania [mkt](#mkt) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie. | String | Nie       |
| <a name="mkt" />mkt   | Rynek, z którego pochodzą wyniki. <br /><br /> **UWAGA:** Na rynku, należy zawsze określić, jeśli jest znany. Określenie rynku pomaga usłudze Bing w kierowaniu żądania i zwracaniu odpowiedniej i optymalnej odpowiedzi.<br /><br /> Ten parametr i parametr zapytania [cc](#cc) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie. | String | Yes      |
| <a name="safesearch" />bezpieczne wyszukiwanie | Filtr zawartości dla dorosłych. Poniżej przedstawiono możliwe wartości filtru bez uwzględniania wielkości liter.<br /><ul><li>Off&mdash;zwracaj strony internetowe z tekstem i obrazami dla dorosłych.<br /><br/></li><li>Moderate&mdash;zwracaj strony internetowe z tekstem dla dorosłych, ale bez obrazów dla dorosłych.<br /><br/></li><li>Strict&mdash;nie zwracaj stron internetowych z tekstem i obrazami dla dorosłych.</li></ul><br /> Wartość domyślna to Moderate.<br /><br /> **UWAGA:** Jeśli żądanie pochodzi z rynku zasad treści dla dorosłych tego Bing wymaga `safeSearch` równa Strict, ignoruje Bing `safeSearch` wartość i używa Strict.<br/><br/>**UWAGA:** Jeśli używasz `site:` — operator zapytań, istnieje możliwość, że odpowiedzi może zawierać treści dla dorosłych niezależnie od tego, co będzie `safeSearch` ustawiono parametr zapytania. Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych.  | String | Nie       |
| <a name="setlang" />setLang  | Język ciągów interfejsu użytkownika. Określ język, przy użyciu ISO 639-1 dwuliterowych języka kodu. Na przykład kod języka dla języka angielskiego to EN. Wartość domyślna to EN (język angielski).<br /><br /> Mimo, że jest to opcjonalne, należy zawsze określić język. Na ogół dla parametru `setLang` ustawia się język określony przez parametr `mkt`, chyba że użytkownik chce, aby ciągi interfejsu użytkownika były wyświetlane w innym języku.<br /><br /> Ten parametr i nagłówek [Accept-Language](#acceptlanguage) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Ponadto wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język. | String | Nie   |

## <a name="headers"></a>Nagłówki

Poniżej przedstawiono nagłówki, które należy określić w żądaniu. `Content-Type` i `Ocp-Apim-Subscription-Key` nagłówki są tylko nagłówki wymagane, ale powinien również zawierać `User-Agent`, `X-MSEdge-ClientID`, `X-MSEdge-ClientIP`, i `X-Search-Location`.

| Nagłówek | Opis |
| --- | --- |
| <a name="acceptlanguage" />Zaakceptuj języka  | Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana przecinkami lista języków dla ciągów interfejsu użytkownika. Lista jest zorganizowana w malejącym porządku preferencji. Aby uzyskać więcej informacji, łącznie z oczekiwanym formatem, zobacz [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i parametr zapytania [setLang](#setlang) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Jeśli ustawisz ten nagłówek, musisz także określić parametr zapytania [cc](#cc). Aby ustalić rynek, dla którego mają zostać zwrócone wyniki, usługa Bing używa pierwszego obsługiwanego języka znalezionego na liście i łączy go z wartością parametru `cc`. Jeśli lista nie zawiera obsługiwanego języka, usługa Bing znajduje najbliższy język i rynek, które obsługują żądanie, lub używa rynku zagregowanego bądź domyślnego. Aby określić na rynku, używanym w usłudze Bing, zobacz `BingAPIs-Market` nagłówka.<br /><br /> Używaj tego nagłówka i parametru zapytania `cc` tylko wtedy, gdy określasz wiele języków. W przeciwnym razie użyj parametrów zapytania [mkt](#mkt) i [setLang](#setlang).<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.  |
| <a name="contenttype" />Content-Type  |     |
| <a name="market" />BingAPIs-Market    | Nagłówek odpowiedzi.<br /><br /> Rynek używany przez żądanie. Format jest następujący \<kod_języka\>-\<kod_kraju\>. Na przykład en-US.  |
| <a name="traceid" />BingAPIs TraceId  | Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwyć ten identyfikator. Jeśli nie możesz określić i rozwiązać problemu, dołącz ten identyfikator wraz z innymi informacjami, które podasz zespołowi pomocy technicznej. |
| <a name="subscriptionkey" />OCP-Apim-Subscription-Key | Wymagany nagłówek żądania.<br /><br /> Klucz subskrypcji otrzymany podczas tworzenia konta dla tej usługi w usługach [Cognitive Services](https://www.microsoft.com/cognitive-services/). |
| <a name="pragma" />Pragma |   |
| <a name="useragent" />Agent użytkownika  | Opcjonalny nagłówek żądania.<br /><br /> Agent użytkownika, od którego pochodzi żądanie. Usługa Bing korzysta z agenta użytkownika, aby zapewnić zoptymalizowane środowisko obsługi dla użytkowników urządzeń przenośnych. Mimo że ten nagłówek jest opcjonalny, zachęcamy, aby go zawsze określać.<br /><br /> Nagłówek user-agent powinien być takim samym ciągiem, jaki wysyła każda powszechnie używana przeglądarka. Aby uzyskać informacji na temat agentów użytkownika, zobacz [dokumencie RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągów nagłówka user-agent.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodne; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; jak Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 jak Mac OS X) AppleWebKit/536.26 (KHTML; jak Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) jak Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 jak Mac OS X) AppleWebKit/537.51.1 (KHTML, jak Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid" />X-MSEdge-ClientID  | Opcjonalny nagłówek żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego nagłówka, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Usługa Bing często testuje nowe funkcje i ulepszenia i używa identyfikatora klienta jako klucza do przypisywania ruchu dla różnych pakietów testowych. Jeśli nie będziesz używać tego samego identyfikatora klienta dla użytkownika w wielu żądaniach, usługa Bing może przypisać użytkownika do różnych, konfliktowych pakietów testowych. Przypisanie do wielu konfliktowych pakietów testowych może prowadzić do niespójnego środowiska użytkownika. Jeśli na przykład drugie żądanie ma przypisany inny pakiet testowy, niż pierwsze, środowisko obsługi może być nieoczekiwane. Ponadto usługa Bing może używać identyfikatora klienta, aby dopasować wyniki internetowe do historii wyszukiwania tego identyfikatora, zapewniając użytkownikowi bogatsze środowisko obsługi.<br /><br /> Usługa Bing używa także tego nagłówka, aby ulepszyć pozycjonowanie wyników, analizując aktywność generowaną przez identyfikator klienta. Ulepszenia istotności pomagają zwiększyć jakość wyników dostarczanych przez interfejsy API usługi Bing, co w rezultacie daje wyższą częstotliwość kliknięć dla użytkownika interfejsu API.<br /><br /> **WAŻNE:** Mimo że jest to opcjonalne, należy rozważyć tego pliku nagłówkowego wymagane. Utrwalenie identyfikatora klienta dla wielu żądań dla połączenia tego samego użytkownika końcowego i urządzenia umożliwia (1) zapewnienie użytkownikowi interfejsu API spójnego środowiska obsługi oraz (2) uzyskanie wyższej częstotliwości kliknięć dzięki lepszej jakość wyników z interfejsów API usługi Bing.<br /><br /> Poniżej przedstawiono podstawowe reguły użycia, które mają zastosowanie do tego nagłówka.<br /><ul><li>Każdy użytkownik, który korzysta z Twojej aplikacji na urządzeniu, musi mieć unikatowy identyfikator klienta wygenerowany przez usługę Bing.<br /><br/>Jeśli nie uwzględnisz tego nagłówka w żądaniu, usługa Bing wygeneruje identyfikator i zwróci go w nagłówku odpowiedzi X-MSEdge-ClientID. Jedyną sytuacją, w której NIE należy uwzględniać tego nagłówka w żądaniu, jest pierwsze użycie Twojej aplikacji przez danego użytkownika na danym urządzeniu.<br /><br/></li><li>**UWAGI:** Należy się upewnić, że tego Identyfikatora klienta nie jest możliwym do żadnych informacji konta uwierzytelnionego użytkownika.</li><li>Używaj identyfikatora klienta dla każdego żądania interfejsu API usługi Bing, które Twoja aplikacja wykonuje dla tego użytkownika na danym urządzeniu.<br /><br/></li><li>Utrwal identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, użyj trwałego pliku cookie HTTP, aby mieć pewność, że dany identyfikator będzie używany we wszystkich sesjach. Nie należy używać plików cookie sesji. W przypadku innych aplikacji, takich jak aplikacje mobilne, użyj magazynu trwałego urządzenia, aby utrwalić identyfikator.<br /><br/>Następnym razem, gdy użytkownik będzie używać Twojej aplikacji na tym urządzeniu, uzyskaj utrwalony identyfikator klienta.</li></ul><br /> **UWAGA:** Odpowiedzi Bing może lub nie może zawierać tego nagłówka. Jeśli odpowiedź zawiera ten nagłówek, przechwyć identyfikator klienta i używaj go dla wszystkich kolejnych żądań usługi Bing dla tego użytkownika na tym urządzeniu.<br /><br /> **UWAGA:** Jeśli dodasz X MSEdge ClientID, nie może zawierać pliki cookie w żądaniu. |
| <a name="clientip" />X-MSEdge-ClientIP   | Opcjonalny nagłówek żądania.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> **UWAGA:** Mimo że jest to opcjonalne, zachęcamy do zawsze określać tego pliku nagłówkowego i Nagłówek X-Search-lokalizacji.<br /><br /> Nie należy zaciemniać adres (na przykład zamieniając ostatni oktet na 0). Zaciemnianie adresu powoduje, że ustalona lokalizacja nie jest nawet przybliżona do rzeczywistej lokalizacji urządzenia, przez co usługa Bing może zwracać błędne wyniki. |
| <a name="location" />Lokalizacja w przypadku wyszukiwania X   | Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, które opisują geograficzną lokalizację klienta. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania i aby zwracać odpowiednią zawartość lokalną. Parę klucz/wartość określ w formacie \<klucz\>:\<wartość\>. Poniżej przedstawiono klucze, które służą do określania lokalizacji użytkownika.<br /><br /><ul><li>lat&mdash;Wymagane. Szerokość geograficzna lokalizacji klienta w stopniach. Szerokość geograficzna musi być większa niż lub równa -90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują południową szerokość geograficzną, a wartości dodatnie wskazują północną szerokość geograficzną.<br /><br /></li><li>long&mdash;Wymagane. Długość geograficzna lokalizacji klienta w stopniach. Długość geograficzna musi być większa niż lub równa -180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują zachodnią długość geograficzną, a wartości dodatnie wskazują wschodnią długość geograficzną.<br /><br /></li><li>re&mdash;Wymagane. Promień, w metrach, który określa poziomą dokładność współrzędnych. Przekaż wartość zwróconą przez usługę lokalizacji urządzenia. Typowe wartości może być 22 m dla GPS/Wi-Fi, m 380 triangulacji tower komórka i 18,000 m dla odwrotnego wyszukiwania adresu IP.<br /><br /></li><li>ts&mdash;Opcjonalne. Sygnatura czasowa w systemie UTC UNIX dla czasu, kiedy klient znajdował się w danej lokalizacji. (Sygnatura czasowa systemu UNIX to liczba sekund od 1 stycznia 1970 r.).<br /><br /></li><li>head&mdash;Opcjonalne. Względny kierunek przemieszczania się klienta. Określ kierunek ruchu jako liczbę stopni z zakresu od 0 do 360, zliczanych zgodnie z ruchem wskazówek zegara względem prawdziwej północy. Określ ten klucz tylko wtedy, gdy wartość klucza `sp` jest różna od zera.<br /><br /></li><li>sp&mdash;Opcjonalne. Prędkość pozioma (szybkość) w metrach na sekundę, z którą przemieszcza się urządzenie klienckie.<br /><br /></li><li>alt&mdash;Opcjonalne. Wysokość urządzenia klienta, w metrach.<br /><br /></li><li>are&mdash;Opcjonalne. Promień, w metrach, który określa pionową dokładność współrzędnych. Określ ten klucz tylko wtedy, gdy wartość klucza `alt` także została określona.<br /><br /></li></ul> **UWAGA:** Chociaż wiele kluczy są opcjonalne, więcej informacji, które podasz, tym bardziej dokładne wyniki lokalizacji są.<br /><br /> **UWAGA:** Mimo że jest to opcjonalne, zachęcamy do zawsze określić lokalizację geograficzną użytkownika. Podanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedla precyzyjnie fizycznej lokalizacji użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki, należy dołączyć ten nagłówek i `X-MSEdge-ClientIP` nagłówka, ale co najmniej powinien być dołączany do tego pliku nagłówkowego.       |

> [!NOTE]
> Należy pamiętać, że [Użyj interfejsu API wyszukiwania Bing i wyświetlają wymagania dotyczące](/../bing-web-search/use-display-requirements.md) Wymagaj zgodności ze wszystkimi obowiązującymi przepisami, w tym dotyczących korzystania z tych nagłówków. W niektórych systemach prawnych, na przykład w Europie, wymagane jest uzyskania zgody użytkownika przed umieszczeniem narzędzi śledzących na urządzeniu użytkownika.

<a name="content-form-types" />

### <a name="content-form-types"></a>Typy formularzy zawartości

Każde żądanie musi zawierać `Content-Type` nagłówka. Musi mieć wartość nagłówka: `multipart/form-data; boundary=\<boundary string\>`, gdzie \<ciąg granic\> jest unikatowy, nieprzezroczysty ciąg, który identyfikuje granic danych formularza. Na przykład `boundary=boundary_1234-abcd`.

W przypadku wysłania wyszukiwania wizualnego token obrazu lub adres URL poniższy fragment kodu pokazuje dane formularza należy uwzględnić w treści wpisu. Musi zawierać dane formularza `Content-Disposition` nagłówka i ustawić jej `name` parametr "knowledgeRequest". Aby uzyskać szczegółowe informacje o `imageInfo` obiektu, zobacz żądania.

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

Jeśli załadujesz lokalny obraz, poniższy fragment kodu przedstawia dane formularza należy uwzględnić w treści wpisu. Musi zawierać dane formularza `Content-Disposition` nagłówka. Jego parametr `name` musi mieć wartość "image", a parametr `filename` może być ustawiony na dowolny ciąg. `Content-Type` Nagłówek może być ustawiona na dowolny typ mime obrazu często używane. Zawartość formularza jest dane binarne obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB. Największa szerokość lub wysokość powinna wynosić maksymalnie 1500 pikseli.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Poniższy fragment kodu pokazuje sposób określania regionu zainteresowania przekazanego obrazu:

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


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

### <a name="example-request"></a>Przykładowe żądanie

Poniższy fragment kodu przedstawia żądanie insights pełny obraz, które przekazuje token obrazu i regionu zainteresowania. Możesz pobrać tokenu szczegółowe informacje z poprzedniego wywołania do /images/search:

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

## <a name="bing-visual-search-responses"></a>Odpowiedzi wyszukiwania wizualnego Bing

Jeśli dla obrazu są dostępne szczegółowe informacje, odpowiedź obejmuje co najmniej jeden obiekt `tags` zawierający te szczegółowe informacje. `image` Pole zawiera token szczegółowe informacje dla obrazu wejściowego:

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

Pole `tags` zawiera nazwę wyświetlaną i listę akcji (szczegółowe informacje). Jeden z tagów zawiera pole `displayName`, dla którego ustawiono pusty ciąg. Ten tag zawiera domyślne informacje szczegółowe, takie jak strony internetowe, na których znajduje się obraz, obrazy podobne wizualnie i źródła zakupów dla elementów znalezionych na obrazie. Ponieważ cały obraz ma znaczenie, domyślnego znacznika szczegółowych informacji nie obejmuje blokujących pola dla regionów zainteresowania:

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

Aby uzyskać listę wglądu w szczegółowe dane domyślne, zobacz [insights-znacznik domyślny](../default-insights-tag.md).

Pozostałe tagi zawierają inne szczegółowe informacje, które mogą interesować użytkownika. Jeśli na przykład obraz zawiera tekst, jeden tag może obejmować szczegółowe informacje TextResults, które zawierają rozpoznany tekst. Lub, jeśli Bing rozpoznaje jednostki (oznacza to, że osoby, miejsca lub rzeczy) w obrazie, jeden tag identyfikują jednostkę. Wyszukiwanie wizualne zwraca także zróżnicowany zestaw terminów (tagów) pobranych z obrazu wejściowego. Te znaczniki pozwalają użytkownikom na Eksploruj pojęcia znalezione na obrazie. Jeśli na przykład obrazu wejściowy przedstawia sławnego sportowca, jeden z tagów może reprezentować dyscyplinę sportową i zawierać linki do obrazów tej dyscypliny.

Każdy tag zawiera nazwę wyświetlaną, która służy do kategoryzowania szczegółowych informacji, pole ograniczenia identyfikujące region zainteresowania, których dotyczą szczegółowe informacje, same szczegółowe informację oraz miniaturę obrazu. Jeśli na przykład obraz przedstawia osobę w bluzie sportowej, jeden z tagów może zawierać pole ograniczenia, które obejmuje bluzę i szczegółowe informacje VisualSearch oraz ProductVisualSearch. Natomiast inny tag może zawierać szczegółowe informacje ImageResults, które obejmują adres URL żądania interfejsu API /images/search umożliwiający pobranie obrazów powiązanych tematycznie, lub adres URL wyszukiwania witryny Bing.com, który powoduje otwarcie wyników wyszukiwania obrazu w witrynie Bing.com.

Wszystkie tagi, poza domyślnym tagiem szczegółowych informacji, zawierają pola ograniczenia, które określają regiony zainteresowania na obrazie. Jeśli na przykład obraz przedstawia wiele rozpoznawalnych osób, tagi mogą zawierać pola ograniczenia dla każdej osoby. Jeśli natomiast obraz przedstawia rozpoznawalne elementy odzieży, tagi mogą zawierać pola ograniczenia dla każdego rozpoznanego elementu odzieży. Przy użyciu pól ograniczenia można tworzyć punkty aktywne na obrazie, które po kliknięciu wyświetlają informacje na temat zawartości w danym regionie obrazu. Punktów aktywnych nie należy dołączać do obrazów, dla których pole ograniczenia obejmuje cały obraz.

### <a name="text-recognition"></a>Rozpoznawanie tekstu

Jeśli obraz zawiera tekst, który usługa rozpoznaje, jeden tag będzie zawierać szczegółowe informacje TextResults (akcja). Szczegółowe informacje o `displayName` zawiera rozpoznany tekst:

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

Ponieważ pole `displayName` tagu zawiera ciąg ##TextRecognition, nie należy używać go jako tytułu kategorii w środowisku użytkownika. Dotyczy to każdej nazwy wyświetlanej rozpoczynającej się od znaków ##. Zamiast tego należy użyć nazwy wyświetlania akcji.

Rozpoznawanie tekstu może także rozpoznawać informacje kontaktowe na wizytówkach, takie jak numery telefonów i adresy e-mail. Pole ograniczenia określa lokalizację informacji kontaktowych na wizytówce.

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

Jeśli obraz zawiera rozpoznaną jednostkę, taką jak osoba, miejsce lub rzecz, jeden tag może obejmować szczegółowe informacje jednostki.

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

## <a name="see-also"></a>Zobacz także

- [Co to jest interfejs API wyszukiwania wizualnego Bing?](../overview.md)
- [Samouczek: Tworzenie aplikacji internetowej z jednej strony wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
