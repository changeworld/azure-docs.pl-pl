---
title: Odwołanie V3.0 interfejs API tłumaczenia tekstu w usłudze Translator
titlesuffix: Azure Cognitive Services
description: Dokumentacja dotycząca V3.0 interfejs API tekstu usługi Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 4a5bed67252c3b87233c8d2e677e3c620adb8a17
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918811"
---
# <a name="translator-text-api-v30"></a>Interfejs API tekstu usługi Translator w wersji 3.0

## <a name="whats-new"></a>Co nowego?

Interfejs API tekstu usługi Translator w wersji 3 zapewnia nowoczesnych opartych na formacie JSON internetowego interfejsu API. Zwiększa użyteczność i wydajność dzięki konsolidacji istniejących funkcji w mniejszą liczbę operacji i udostępnia nowe funkcje.

 * Transliterację konwersji tekstu w jednym języku jeden skrypt inny skrypt.
 * Tłumaczenie na wiele języków, w jednym żądaniu.
 * Wykrywanie języka, tłumaczenia i transliterację w jednym żądaniu.
 * Słownik, który wyszukiwanie alternatywnych tłumaczeń okresu, można znaleźć kopii tłumaczenia i przykłady pokazujące terminy używane w kontekście.
 * Bardziej szczegółowy wyniki wykrywanie języka.

## <a name="base-urls"></a>Podstawowych adresach URL

Usługa Microsoft Translator jest obsługiwany z wielu lokalizacji centrów danych. Obecnie znajdują się one w 6 [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Rozwiązanie to przyniosło im:** Zachodnie stany USA 2 i środkowe stany USA zachodnie 
* **Azja i Pacyfik:** Azja południowo-wschodnia i Korea Południowa
* **Europa:** Europa Północna i Europa Zachodnia

Żądania interfejs API tekstu usługi Microsoft Translator są w większości przypadków obsługiwane przez centrum danych, które znajduje się najbliżej skąd pochodzi żądanie. W razie awarii centrum danych może być przesłane żądanie, poza regionem.

Aby wymusić żądania, które mają być obsługiwane przez określone centrum danych, zmienić globalny punkt końcowy w żądaniu interfejsu API do żądanego punktu końcowego regionalne:

|Opis|Region|Podstawowy adres URL|
|:--|:--|:--|
|Azure|Globalny|  api.cognitive.microsofttranslator.com|
|Azure|Ameryka Północna|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Azja i Pacyfik|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

Subskrybowanie do interfejsu API tłumaczenia tekstu lub [wielu usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) usług Microsoft Cognitive Services i użycie subskrypcji kluczy (dostępny w witrynie Azure portal) do uwierzytelniania. 

Istnieją trzy nagłówków, których można użyć do uwierzytelnienia Twojej subskrypcji. Ta tabela zawiera, w tym artykule opisano, jak każdy obiekt jest używany:

|Nagłówki|Opis|
|:----|:----|
|OCP-Apim-Subscription-Key|*Używane z subskrypcją usług Cognitive Services, jeśli przekazujesz klucz tajny*.<br/>Wartość jest platformy Azure klucz tajny dla Twojej subskrypcji do interfejsu API tłumaczenia tekstu.|
|Autoryzacja|*Jeśli przekazujesz tokenu uwierzytelniania za pomocą subskrypcji usług Cognitive Services.*<br/>Wartość tokenu elementu nośnego: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Jeśli przekazujesz klucz tajny wielu usług za pomocą subskrypcji wielu usług Cognitive Services.*<br/>Wartość jest region subskrypcji wielu usług. Ta wartość jest opcjonalna, bez korzystania z wieloma usługami subskrypcji.|

###  <a name="secret-key"></a>Klucz tajny
Pierwszym z nich jest uwierzytelnianie przy użyciu `Ocp-Apim-Subscription-Key` nagłówka. Po prostu Dodaj `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` nagłówek na żądanie.

### <a name="authorization-token"></a>Token autoryzacji
Alternatywnie można wymienić klucz tajny tokenu dostępu. Token ten jest dołączony do każdego żądania jako `Authorization` nagłówka. Aby uzyskać token autoryzacji, należy `POST` żądania do następującego adresu URL:

| Środowisko     | Adres URL usługi uwierzytelniania                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Oto przykład żądania do uzyskania tokenu danego klucza tajnego:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Żądania zakończonego powodzeniem zwraca token dostępu zakodowany jako zwykły tekst w treści odpowiedzi. Prawidłowy token jest przekazywany do usługi Translator jako token elementu nośnego w autoryzacji.

```
Authorization: Bearer <Base64-access_token>
```

Token uwierzytelniania jest ważny przez 10 minut. Token powinien być ponownie używane, gdy wielu wywołań interfejsów API usługi Translator. Jednak jeśli program sprawia, że żądania do interfejsu API usługi Translator przez dłuższy czas, następnie program musi żądać nowy token dostępu w regularnych odstępach czasu (np. co 8 minut).

### <a name="multi-service-subscription"></a>Wielu usług subskrypcji

Ostatnia opcja uwierzytelniania jest korzystać z subskrypcji wielu usług Cognitive Service. Dzięki temu można korzystać z jednego klucza tajnego do uwierzytelniania żądań dla wielu usług. 

Korzystając z wieloma usługami klucz tajny musi zawierać dwa nagłówki uwierzytelniania z żądaniem. Pierwszy przekazuje klucz tajny, drugi określa regionu skojarzonego z Twoją subskrypcją. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Region jest wymagane dla wielu usług subskrypcji interfejsu API tłumaczenia tekstu. Regionu, możesz wybrać to jedyny region, który umożliwia tłumaczenie tekstu po przy użyciu klucza subskrypcji z wieloma usługami, a musi być tym samym regionie, które są wybrane podczas tworzenia konta dla subskrypcji wielu usług za pośrednictwem witryny Azure portal.

Dostępne regiony to `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, i `westus2`.

Jeśli przesuwasz klucz tajny w ciągu zapytania za pomocą parametru `Subscription-Key`, a następnie za pomocą parametru zapytania należy określić region `Subscription-Region`.

Jeśli używasz tokenu elementu nośnego, należy uzyskać token z punktu końcowego regionu: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Błędy

Odpowiedzi błędu standardowego jest obiekt JSON z pary nazwa/wartość o nazwie `error`. Wartość jest także obiekt JSON z właściwościami:

  * `code`: Kod błędu zdefiniowany przez serwer.

  * `message`: Ciąg, zapewniając czytelny dla człowieka reprezentację błędu.

Na przykład klient z bezpłatnej subskrypcji próbnej będzie komunikat o błędzie po wyczerpaniu bezpłatny limit przydziału:

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Kod błędu to łączenie liczb 6-cyfrowym, 3-cyfrowy kod stanu HTTP następuje 3-cyfrowy numer do dalszego kategoryzowanie błędu. Typowe kody błędów są:

| Kod | Opis |
|:----|:-----|
| 400000| Dane wejściowe żądania jest nieprawidłowa.|
| 400001| Parametr "scope" jest nieprawidłowy.|
| 400002| Parametr "category" jest nieprawidłowy.|
| 400003| Specyfikator języka jest brakujący lub nieprawidłowy.|
| 400004| Specyfikatora skryptu docelowego ("do"skrypt") jest nieprawidłowy lub.|
| 400005| Wprowadzany tekst jest brakujący lub nieprawidłowy.|
| 400006| Kombinacja języka i skrypt nie jest prawidłowy.|
| 400018| Specyfikatora źródła skryptu ("od"skrypt") jest nieprawidłowy lub.|
| 400019| Jeden określony język nie jest obsługiwane.|
| 400020| Jeden z elementów w tablicy, której tekst wejściowy jest nieprawidłowy.|
| 400021| Parametr wersji interfejsu API jest brakujący lub nieprawidłowy.|
| 400023| Jeden z pary określony język jest nieprawidłowy.|
| 400035| Język źródłowy ("od" pole) jest nieprawidłowy.|
| 400036| Języka docelowego ("do" pole) jest nieprawidłowy lub niedostępny.|
| 400042| Jedną z opcji określone ("Opcje" pole) jest nieprawidłowy.|
| 400043| Identyfikator śledzenia klienta (ClientTraceId pola lub Nagłówek X-ClientTranceId) Brak lub jest nieprawidłowy.|
| 400050| Wprowadzany tekst jest zbyt długa.|
| 400064| Parametr "translacji" Brak lub jest nieprawidłowa.|
| 400070| Liczba docelowych skryptów (parametr ToScript) jest niezgodny z liczba docelowych języki (parametr).|
| 400071| Wartość nie jest prawidłowa dla TextType.|
| 400072| Tablica wprowadzania tekstu ma za dużo elementów.|
| 400073| Parametr skryptu jest nieprawidłowy.|
| 400074| Treść żądania nie jest prawidłowym plikiem JSON.|
| 400075| Kombinacja pary i kategorii języka jest nieprawidłowa.|
| 400077| Rozmiar maksymalny żądania został przekroczony.|
| 400079| Nie ma żądanego translacji od i do języka systemu niestandardowych.|
| 401000| Żądanie nie ma uprawnień, ponieważ poświadczenia są brakujący lub nieprawidłowy.|
| 401015| "Są podane poświadczenia dla interfejsu API rozpoznawania mowy. To żądanie wymaga poświadczeń dla interfejsu API tłumaczenia tekstu. Użyj subskrypcji interfejsu API tłumaczenia tekstu."|
| 403000| Operacja nie jest dozwolona.|
| 403001| Operacja jest niedozwolona, ponieważ subskrypcja przekroczyła limit przydziału bezpłatnych.|
| 405000| Metoda żądania nie jest obsługiwana dla żądanego zasobu.|
| 408001| Żądanego systemu tłumaczenia niestandardowych nie jest jeszcze dostępna. Spróbuj ponownie za kilka minut.|
| 415000| Nagłówek Content-Type jest brakujący lub nieprawidłowy.|
| 429000, 429001, 429002| Serwer odrzucił żądanie, ponieważ klient wysyła zbyt wiele żądań. Zmniejsz częstotliwość żądań, aby uniknąć ograniczenia przepustowości.|
| 500000| Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś to za pomocą daty/godziny wystąpienia błędu, poproś identyfikator odpowiedzi nagłówek X-RequestId i identyfikator klienta z nagłówek żądania X-ClientTraceId.|
| 503000| Usługa jest tymczasowo niedostępna. Spróbuj ponownie. Jeśli błąd będzie się powtarzać, zgłoś to za pomocą daty/godziny wystąpienia błędu, poproś identyfikator odpowiedzi nagłówek X-RequestId i identyfikator klienta z nagłówek żądania X-ClientTraceId.|

