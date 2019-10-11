---
title: Dokumentacja interfejs API tłumaczenia tekstu w usłudze Translator V 3.0
titleSuffix: Azure Cognitive Services
description: Dokumentacja referencyjna interfejs API tłumaczenia tekstu w usłudze Translator V 3.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: a441ca83230a1c715aadda79683964aaab6d6213
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72252977"
---
# <a name="translator-text-api-v30"></a>Interfejs API tłumaczenia tekstu w usłudze Translator v 3.0

## <a name="whats-new"></a>Co nowego?

Wersja 3 interfejs API tłumaczenia tekstu w usłudze Translator zapewnia nowoczesny internetowy interfejs API oparty na notacji JSON. Zwiększa użyteczność i wydajność dzięki konsolidacji istniejących funkcji do mniejszej liczby operacji i udostępnia nowe funkcje.

 * Przekształć w celu przekonwertowania tekstu w jednym języku z jednego skryptu na inny.
 * Tłumaczenie na wiele języków w jednym żądaniu.
 * Wykrywanie, tłumaczenia i translitrowanie języka w jednym żądaniu.
 * Słownik do wyszukiwania alternatywnych tłumaczeń terminu, aby znaleźć zwroty i przykłady pokazujące terminy używane w kontekście.
 * Dokładniejsze wyniki wykrywania języka.

## <a name="base-urls"></a>Podstawowe adresy URL

Usługi Microsoft Translator są obsługiwane z wielu lokalizacji centrów danych. Obecnie znajdują się one w 10 [lokalizacje geograficzne Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Ameryka Północna:** Wschodnie stany USA, Południowo-środkowe stany USA, zachodnio-środkowe stany USA i zachodnie stany USA 2 
* **Azja i Pacyfik:** Korea Południowa, Japonia Wschodnia, Azja Południowo-Wschodnia i Australia Wschodnia
* **Europa:** Europa Północna i Europa Zachodnia

Żądania kierowane do interfejs API tłumaczenia tekstu w usłudze Translator firmy Microsoft są w większości przypadków obsługiwane przez centrum danych, które znajdują się najbliżej lokalizacji, z której pochodzi żądanie. W przypadku awarii centrum danych żądanie może być kierowane poza lokalizację geograficzną platformy Azure.

Aby wymusić obsługę żądania przez określoną lokalizację geograficzną platformy Azure, Zmień globalny punkt końcowy w żądaniu interfejsu API na żądany regionalny punkt końcowy:

|Opis|Lokalizacja geograficzna platformy Azure|Podstawowy adres URL|
|:--|:--|:--|
|Azure|Globalne (nieregionalne)|   api.cognitive.microsofttranslator.com|
|Azure|Stany Zjednoczone|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Azja i Pacyfik|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Uwierzytelnianie

Zasubskrybuj usługę interfejs API tłumaczenia tekstu w usłudze Translator lub [Cognitive Services wiele usług](https://azure.microsoft.com/pricing/details/cognitive-services/) w usłudze Azure Cognitive Services i Użyj klucza subskrypcji (dostępnego w Azure Portal) do uwierzytelniania. 

Istnieją trzy nagłówki, za pomocą których można uwierzytelniać swoją subskrypcję. W tej tabeli opisano, jak są używane:

|Nagłówki|Opis|
|:----|:----|
|OCP-APIM-Subscription-Key|*Jeśli przekazujesz klucz tajny, Użyj usługi z subskrypcją Cognitive Services*.<br/>Wartość jest kluczem tajnym platformy Azure dla Twojej subskrypcji do interfejs API tłumaczenia tekstu w usłudze Translator.|
|Autoryzacja|*Użyj usługi z subskrypcją Cognitive Services, jeśli przekazujesz token uwierzytelniania.*<br/>Wartość jest tokenem okaziciela: `Bearer <token>`.|
|OCP-APIM-Subscription-region|*Użyj usługi with Cognitive Services subskrypcję usługi, jeśli przekazujesz klucz tajny usługi wiele usług.*<br/>Wartość jest regionem subskrypcji wielousługowej. Ta wartość jest opcjonalna, gdy nie jest używana subskrypcja Wielousługowa.|

###  <a name="secret-key"></a>Klucz tajny
Pierwsza opcja polega na uwierzytelnianiu przy użyciu nagłówka `Ocp-Apim-Subscription-Key`. Dodaj nagłówek `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` do żądania.

### <a name="authorization-token"></a>Token autoryzacji
Alternatywnie możesz wymienić klucz tajny dla tokenu dostępu. Token ten jest dołączany do każdego żądania jako nagłówek `Authorization`. Aby uzyskać Token autoryzacji, wprowadź żądanie `POST` do następującego adresu URL:

| Środowisko     | Adres URL usługi uwierzytelniania                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Oto przykładowe żądania uzyskania tokenu danego klucza tajnego:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Pomyślne żądanie zwraca zakodowany token dostępu jako zwykły tekst w treści odpowiedzi. Prawidłowy token jest przesyłany do usługi Translator jako token okaziciela w autoryzacji.

```http
Authorization: Bearer <Base64-access_token>
```

Token uwierzytelniania jest ważny przez 10 minut. Token powinien być ponownie używany podczas wykonywania wielu wywołań interfejsów API usługi Translator. Jeśli jednak program wysyła żądania do interfejsu API usługi Translator w dłuższym okresie, program musi zażądać nowego tokenu dostępu w regularnych odstępach czasu (na przykład co 8 minut).

### <a name="multi-service-subscription"></a>Subskrypcja Wielousługowa

Ostatnia opcja uwierzytelniania polega na użyciu wielousługowej subskrypcji usługi poznawczej. Dzięki temu można używać jednego klucza tajnego do uwierzytelniania żądań dla wielu usług. 

W przypadku korzystania z wielousługowego klucza tajnego należy dołączyć dwa nagłówki uwierzytelniania do żądania. Pierwszy przekazuje klucz tajny, drugi określa region skojarzony z subskrypcją. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Region jest wymagany w przypadku subskrypcji wielousługowego interfejsu API tekstu. Wybrany region jest jedynym regionem, którego można użyć do tłumaczenia tekstu przy użyciu klucza subskrypcji wielousługowej i musi być tym samym regionem wybranym podczas rejestrowania się w ramach subskrypcji wielousługowej za pośrednictwem Azure Portal.

Dostępne regiony to `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centralus` `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `francecentral`, 0, 1, 2, 3, 4, 5, 6, @no__t – 17, 8, 9, 0, @no__ t-21 i 2.

Jeśli przekażesz klucz tajny w ciągu zapytania z parametrem `Subscription-Key`, należy określić region z parametrem zapytania `Subscription-Region`.

Jeśli używasz tokenu okaziciela, musisz uzyskać token z punktu końcowego regionu: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Błędy

Standardowa odpowiedź na błąd to obiekt JSON o parze nazwa/wartość o nazwie `error`. Wartość jest również obiektem JSON z właściwościami:

  * `code`: kod błędu zdefiniowany przez serwer.
  * `message`: ciąg dający czytelny dla człowieka reprezentację błędu.

Na przykład klient z subskrypcją bezpłatnej wersji próbnej otrzyma następujący błąd po wyczerpaniu limitu przydziału:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, a następnie 3-cyfrowy numer do dalszej kategoryzacji błędu. Typowe kody błędów to:

| Kod | Opis |
|:----|:-----|
| 400000| Jeden z danych wejściowych żądania jest nieprawidłowy.|
| 400001| Parametr "Scope" jest nieprawidłowy.|
| 400002| Parametr "Category" jest nieprawidłowy.|
| 400003| Brak specyfikatora języka lub jest on nieprawidłowy.|
| 400004| Brak specyfikatora skryptu docelowego ("do skryptu") lub jest on nieprawidłowy.|
| 400005| Brak tekstu wejściowego lub jest on nieprawidłowy.|
| 400006| Kombinacja języka i skryptu jest nieprawidłowa.|
| 400018| Brak specyfikatora skryptu źródłowego ("ze skryptu") lub jest on nieprawidłowy.|
| 400019| Jeden z określonych języków nie jest obsługiwany.|
| 400020| Jeden z elementów w tablicy tekstu wejściowego jest nieprawidłowy.|
| 400021| Brak parametru wersji interfejsu API lub jest on nieprawidłowy.|
| 400023| Jedna z określonych par języków jest nieprawidłowa.|
| 400035| Nieprawidłowy język źródłowy ("from").|
| 400036| Brak języka docelowego (pole "do") lub jest on nieprawidłowy.|
| 400042| Jedna z opcji określonych w polu "Opcje" jest nieprawidłowa.|
| 400043| Brak identyfikatora śledzenia klienta (pole ClientTraceId lub nagłówek X-ClientTranceId) lub jest on nieprawidłowy.|
| 400050| Tekst wejściowy jest zbyt długi. Wyświetlanie [limitów żądań](../request-limits.md).|
| 400064| Brak parametru "translation" lub jest on nieprawidłowy.|
| 400070| Liczba skryptów docelowych (parametr ToScript) nie jest zgodna z liczbą języków docelowych (do parametru).|
| 400071| Wartość jest nieprawidłowa dla typu texttype.|
| 400072| Tablica tekstu wejściowego ma zbyt wiele elementów.|
| 400073| Parametr skryptu jest nieprawidłowy.|
| 400074| Treść żądania nie jest prawidłowym kodem JSON.|
| 400075| Para językowa i kombinacja kategorii są nieprawidłowe.|
| 400077| Przekroczono maksymalny rozmiar żądania. Wyświetlanie [limitów żądań](../request-limits.md).|
| 400079| System niestandardowy żądany do tłumaczenia między językiem i a nie istnieje.|
| 400080| Narzędzie transliteracji nie jest obsługiwane dla języka lub skryptu.|
| 401000| Żądanie nie jest autoryzowane, ponieważ brakuje poświadczeń lub są one nieprawidłowe.|
| 401015| "Podane poświadczenia dotyczą Speech API. To żądanie wymaga poświadczeń dla interfejsu API tekstu. Użyj subskrypcji, aby interfejs API tłumaczenia tekstu w usłudze Translator ".|
| 403000| Operacja jest niedozwolona.|
| 403001| Operacja jest niedozwolona, ponieważ subskrypcja przekroczyła bezpłatny limit przydziału.|
| 405000| Metoda Request nie jest obsługiwana dla żądanego zasobu.|
| 408001| Żądany system tłumaczenia jest przygotowywany. Spróbuj ponownie za kilka minut.|
| 408002| Upłynął limit czasu żądania podczas oczekiwania na strumień przychodzący. Klient nie wygenerował żądania w czasie, gdy serwer został przygotowany do oczekiwania. Klient może powtórzyć żądanie bez modyfikacji w późniejszym czasie.|
| 415000| Brak nagłówka Content-Type lub jest on nieprawidłowy.|
| 429000, 429001, 429002| Serwer odrzucił żądanie z powodu przekroczenia limitów żądań przez klienta.|
| 500000| Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś ten błąd w polu Data/godzina błędu, identyfikator żądania z nagłówka odpowiedzi X-IdentyfikatorŻądania i identyfikator klienta z nagłówka żądania X-ClientTraceId.|
| 503000| Usługa jest tymczasowo niedostępna. Spróbuj ponownie. Jeśli błąd będzie się powtarzać, zgłoś ten błąd w polu Data/godzina błędu, identyfikator żądania z nagłówka odpowiedzi X-IdentyfikatorŻądania i identyfikator klienta z nagłówka żądania X-ClientTraceId.|

