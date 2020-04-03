---
title: Odwołanie do interfejsu API tekstu tłumacza w wersji 3.0
titleSuffix: Azure Cognitive Services
description: Dokumentacja referencyjna dla interfejsu API tekstu tłumacza v3.0. Wersja 3 interfejsu API tekstu translatora zapewnia nowoczesny interfejs API sieci Web oparty na programie JSON.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 4/2/2020
ms.author: swmachan
ms.openlocfilehash: fcbaabac0961f1269a929fb4a56f81ac282bae29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619161"
---
# <a name="translator-text-api-v30"></a>Interfejs API tekstu tłumacza w wersji 3.0

## <a name="whats-new"></a>Co nowego?

Wersja 3 interfejsu API tekstu translatora zapewnia nowoczesny interfejs API sieci Web oparty na programie JSON. Poprawia użyteczność i wydajność, konsolidując istniejące funkcje w mniejszej liczbie operacji i udostępnia nowe funkcje.

 * Transliteracja do konwersji tekstu w jednym języku z jednego skryptu do innego skryptu.
 * Tłumaczenie na wiele języków w jednym żądaniu.
 * Wykrywanie języka, tłumaczenie i transliteracja w jednym żądaniu.
 * Słownik wyszukuje alternatywne tłumaczenia terminu, aby znaleźć tłumaczenia wsteczne i przykłady przedstawiające terminy używane w kontekście.
 * Bardziej pouczające wyniki wykrywania języka.

## <a name="base-urls"></a>Podstawowe adresy URL

Usługa Microsoft Translator jest obsługiwana z wielu lokalizacji centrów danych. Obecnie znajdują się one w 10 [regionach platformy Azure:](https://azure.microsoft.com/global-infrastructure/regions)

* **Ameryki:** Wschodnie stany USA, południowo-środkowe stany USA, środkowe zachodnie stany USA i zachodnie stany USA 2 
* **Azja i Pacyfik:** Korea Południowa, Japonia Wschodnia, Azja Południowo-Wschodnia i Australia Wschodnia
* **Europa:** Europa Północna i Europa Zachodnia

Żądania do interfejsu API tekstu usługi Microsoft Translator są w większości przypadków obsługiwane przez centrum danych, które jest najbliżej miejsca, z którego pochodzi żądanie. W przypadku awarii centrum danych żądanie może być kierowane poza geografii platformy Azure.

Aby wymusić, że żądanie ma być obsługiwane przez określoną lokalizację geograficzną platformy Azure, zmień globalny punkt końcowy w żądaniu interfejsu API na żądany regionalny punkt końcowy:

|Opis|Geografia platformy Azure|Podstawowy adres URL|
|:--|:--|:--|
|Azure|Globalny (nieregionalne)|   api.cognitive.microsofttranslator.com|
|Azure|Stany Zjednoczone|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Azja i Pacyfik|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Authentication

Subskrybuj interfejs API tekstu translatora lub [usługi Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) w wielu usługach w usłudze Azure Cognitive Services i użyj klucza subskrypcji (dostępnego w witrynie Azure portal) do uwierzytelniania. 

Istnieją trzy nagłówki, których można użyć do uwierzytelnienia subskrypcji. W tej tabeli opisano sposób, w jaki każdy z nich jest używany:

|Nagłówki|Opis|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Użyj z subskrypcją usług Cognitive Services, jeśli przekazujesz swój tajny klucz*.<br/>Wartość jest klucz tajny platformy Azure dla subskrypcji interfejsu API tekstu translatora.|
|Autoryzacja|*Użyj z subskrypcją usług Cognitive Services, jeśli przekazujesz token uwierzytelniania.*<br/>Wartość jest token okaziciela: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Użyj z usługami Cognitive Services wielodosługowymi i regionalnymi zasobami tłumacza.*<br/>Wartość jest regionem zasobu tłumacza wielodosługowego lub regionalnego. Ta wartość jest opcjonalna podczas korzystania z zasobu usługi tłumacza globalnego.|

###  <a name="secret-key"></a>Klucz tajny
Pierwszą opcją jest uwierzytelnienie `Ocp-Apim-Subscription-Key` przy użyciu nagłówka. Dodaj `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` nagłówek do żądania.

#### <a name="authenticating-with-a-global-resource"></a>Uwierzytelnianie za pomocą zasobu globalnego

Podczas korzystania z [globalnego zasobu tłumacza](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)należy dołączyć jeden nagłówek, aby wywołać interfejs API translatora.

|Nagłówki|Opis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Wartość jest klucz tajny platformy Azure dla subskrypcji interfejsu API tekstu translatora.|

Oto przykładowe żądanie wywołania interfejsu API translatora przy użyciu zasobu usługi tłumacza globalnego

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Uwierzytelnianie za pomocą zasobu regionalnego

W przypadku korzystania z [zasobu tłumacza regionalnego](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
Istnieją 2 nagłówki, które należy wywołać interfejs API translatora.

|Nagłówki|Opis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Wartość jest klucz tajny platformy Azure dla subskrypcji interfejsu API tekstu translatora.|
|Ocp-Apim-Subscription-Region| Wartość jest regionem zasobu tłumacza. |

Oto przykładowe żądanie wywołania interfejsu API translatora przy użyciu zasobu translatora regionalnego

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Uwierzytelnianie za pomocą zasobu wielodochowego

Podczas korzystania z zasobu wielu usług usługi Cognitive Service. Dzięki temu można użyć jednego klucza tajnego do uwierzytelniania żądań dla wielu usług. 

Korzystając z klucza tajnego wielu usług, należy dołączyć dwa nagłówki uwierzytelniania z żądaniem. Istnieją 2 nagłówki, które należy wywołać interfejs API translatora.

|Nagłówki|Opis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Wartość jest klucz tajny platformy Azure dla zasobu wielu usług.|
|Ocp-Apim-Subscription-Region| Wartość jest regionem zasobu wielodochowego. |

Region jest wymagany dla subskrypcji wielodochowego interfejsu API tekstu. Wybrany region jest jedynym regionem, którego można używać do tłumaczenia tekstu podczas korzystania z klucza subskrypcji z wieloma usługami i musi być tym samym regionem wybranym podczas konfigurowania subskrypcji wielu usług za pośrednictwem witryny Azure portal.

Dostępne regiony `australiaeast` `brazilsouth`to `canadacentral` `centralindia`, `centralus` `centraluseuap`, `eastasia` `eastus`, `eastus2` `francecentral`, `japaneast` `japanwest`, `koreacentral` `northcentralus`, `northeurope` `southcentralus`, `southeastasia` `uksouth`, `westcentralus` `westeurope`, `westus` `westus2`, `southafricanorth`, , , , , , , , , , , , , , , , .

Jeśli przekażesz klucz tajny w ciągu `Subscription-Key`zapytania z parametrem, należy `Subscription-Region`określić region z parametrem kwerendy .

### <a name="authenticating-with-an-access-token"></a>Uwierzytelnianie za pomocą tokenu dostępu
Alternatywnie można wymienić klucz tajny tokenu dostępu. Ten token jest dołączony do `Authorization` każdego żądania jako nagłówek. Aby uzyskać token autoryzacji, należy złożyć `POST` żądanie o następujący adres URL:

| Typ zasobu     | Adres URL usługi uwierzytelniania                                |
|-----------------|-----------------------------------------------------------|
| Globalny          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Usługi regionalne lub wielosłużełowe | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Oto przykładowe żądania uzyskania tokenu, podanym kluczem tajnym:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Pomyślne żądanie zwraca zakodowany token dostępu jako zwykły tekst w treści odpowiedzi. Prawidłowy token jest przekazywany do usługi Translator jako token na okaziciela w autoryzacji.

```http
Authorization: Bearer <Base64-access_token>
```

Token uwierzytelniania jest prawidłowy przez 10 minut. Token powinien być ponownie używany podczas wykonywania wielu wywołań interfejsów API translatora. Jeśli jednak program żąda żądań do interfejsu API translatora przez dłuższy czas, program musi zażądać nowego tokenu dostępu w regularnych odstępach czasu (na przykład co 8 minut).

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej

Usługa translator jest teraz dostępna z możliwościami`WestUS2`sieci `EastUS` `SouthCentralUS`wirtualnej w ograniczonych regionach ( , , , `WestUS`, `Central US EUAP`, , `global`). Aby włączyć sieć wirtualną, zobacz [Konfigurowanie sieci wirtualnych usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal). 

Po włączeniu tej funkcji należy użyć niestandardowego punktu końcowego, aby wywołać interfejs API translatora. Nie można użyć punktu końcowego usługi tłumacza globalnego ("api.cognitive.microsofttranslator.com") i nie można uwierzytelnić za pomocą tokenu dostępu.

Niestandardowy punkt końcowy można znaleźć po utworzeniu [zasobu tłumacza](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).

|Nagłówki|Opis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Wartość jest klucz tajny platformy Azure dla subskrypcji interfejsu API tekstu translatora.|
|Ocp-Apim-Subscription-Region| Wartość jest regionem zasobu tłumacza. Ta wartość jest opcjonalna, jeśli zasób jest`global`|

Oto przykładowe żądanie wywołania interfejsu API translatora przy użyciu niestandardowego punktu końcowego

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Errors

Standardowa odpowiedź na błąd to obiekt JSON `error`o nazwie nazwa/para wartości . Wartość jest również obiekt JSON z właściwościami:

  * `code`: Kod błędu zdefiniowany przez serwer.
  * `message`: Ciąg dający czytelną dla człowieka reprezentację błędu.

Na przykład klient z bezpłatną subskrypcją próbną otrzyma następujący błąd po wyczerpaniu przydziału wolnego:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, po którym następuje 3-cyfrowy numer w celu dalszej kategoryzowania błędu. Typowe kody błędów to:

| Code | Opis |
|:----|:-----|
| 400000| Jedno z danych wejściowych żądania jest nieprawidłowe.|
| 400001| Parametr "scope" jest nieprawidłowy.|
| 400002| Parametr "kategoria" jest nieprawidłowy.|
| 400003| Brak specyfikatora języka lub jest nieprawidłowy.|
| 400004| Brakuje specyfikatora skryptu docelowego ("Do skryptu") lub jest nieprawidłowy.|
| 400005| Brakuje tekstu wejściowego lub jest on nieprawidłowy.|
| 400006| Kombinacja języka i skryptu jest nieprawidłowa.|
| 400018| Brakuje specyfikatora skryptu źródłowego ("Od skryptu") lub jest nieprawidłowy.|
| 400019| Jeden z określonych języków nie jest obsługiwany.|
| 400020| Jeden z elementów w tablicy tekstu wejściowego jest nieprawidłowy.|
| 400021| Brak parametru wersji interfejsu API lub jest on nieprawidłowy.|
| 400023| Jedna z określonych par języków jest nieprawidłowa.|
| 400035| Język źródłowy (pole "Od") jest nieprawidłowy.|
| 400036| Brakuje języka docelowego (pole "Do") lub jest on nieprawidłowy.|
| 400042| Jedna z określonych opcji (pole "Opcje") jest nieprawidłowa.|
| 400043| Brak identyfikatora śledzenia klienta (pola ClientTraceId lub nagłówka X-ClientTranceId) lub jest nieprawidłowy.|
| 400050| Tekst wejściowy jest za długi. Wyświetl [limity żądań](../request-limits.md).|
| 400064| Brakuje parametru "translation" lub jest on nieprawidłowy.|
| 400070| Liczba skryptów docelowych (parametr ToScript) nie odpowiada liczbie języków docelowych (parametr To).|
| 400071| Wartość nie jest prawidłowa dla TextType.|
| 400072| Tablica tekstu wejściowego ma zbyt wiele elementów.|
| 400073| Parametr skryptu jest nieprawidłowy.|
| 400074| Treść żądania jest nieprawidłowa JSON.|
| 400075| Para języków i kombinacja kategorii jest nieprawidłowa.|
| 400077| Przekroczono maksymalny rozmiar żądania. Wyświetl [limity żądań](../request-limits.md).|
| 400079| System niestandardowy wymagany do tłumaczenia między i na język nie istnieje.|
| 400080| Transliteracja nie jest obsługiwana dla języka lub skryptu.|
| 401000| Żądanie nie jest autoryzowane, ponieważ brakuje poświadczeń lub są nieprawidłowe.|
| 401015| "Podane poświadczenia są dla interfejsu API mowy. To żądanie wymaga poświadczeń dla interfejsu API tekstu. Użyj subskrypcji interfejsu API tekstu tłumacza."|
| 403000| Operacja jest niedozwolona.|
| 403001| Operacja jest niedozwolona, ponieważ subskrypcja przekroczyła swój przydział wolny.|
| 405000| Metoda żądania nie jest obsługiwana dla żądanego zasobu.|
| 408001| Przygotowywany jest wymagany system tłumaczeń. Spróbuj ponownie w ciągu kilku minut.|
| 408002| Termin oczekiwania na żądanie w strumieniu przychodzącym. Klient nie wygenerował żądania w czasie, w której serwer był przygotowany do oczekiwania. Klient może powtórzyć żądanie bez modyfikacji w późniejszym czasie.|
| 415000| Brak nagłówka Typ zawartości lub jest nieprawidłowy.|
| 429000, 429001, 429002| Serwer odrzucił żądanie, ponieważ klient przekroczył limity żądań.|
| 500000| Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzał, zgłoś go z datą/godziną błędu, identyfikatorem żądania z nagłówka odpowiedzi X-RequestId i identyfikatorem klienta z nagłówka żądania X-ClientTraceId.|
| 503000| Usługa jest tymczasowo niedostępna. Spróbuj ponownie. Jeśli błąd będzie się powtarzał, zgłoś go z datą/godziną błędu, identyfikatorem żądania z nagłówka odpowiedzi X-RequestId i identyfikatorem klienta z nagłówka żądania X-ClientTraceId.|

## <a name="metrics"></a>Metryki 
Metryki umożliwiają wyświetlanie informacji o użyciu i dostępności tłumacza w witrynie Azure portal w sekcji metryki, jak pokazano na poniższym zrzucie ekranu. Aby uzyskać więcej informacji, zobacz [Dane i dane platformy](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

![Metryki tłumacza](../media/translatormetrics.png)

W tej tabeli wymieniono dostępne metryki z opisem sposobu, w jaki są one używane do monitorowania wywołań interfejsu API tłumaczenia.

| Metryki | Opis |
|:----|:-----|
| Całkowita liczba powołów| Całkowita liczba wywołań interfejsu API.|
| TotalTokenCalls (Liczba powołów)| Całkowita liczba wywołań interfejsu API za pośrednictwem usługi tokenu przy użyciu tokenu uwierzytelniania.|
| Udane rozmowy| Liczba udanych połączeń.|
| TotalErrors ( TotalErrors )| Liczba połączeń z odpowiedzią na błąd.|
| BlockedCalls (Zablokowane telefony)| Liczba wywołań, które przekroczyły limit stawki lub przydziału.|
| Serwery serwerowe| Liczba połączeń z wewnętrznym błędem serwera(5XX).|
| KlientErrors (KlientErrors)| Liczba połączeń z błędem po stronie klienta(4XX).|
| Opóźnienie| Czas trwania do ukończenia żądania w milisekundach.|
| Znaki Przetłumaczone| Całkowita liczba znaków w przychodzącym żądaniu tekstowym.|
