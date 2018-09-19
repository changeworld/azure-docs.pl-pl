---
title: Odwołanie V3.0 interfejs API tłumaczenia tekstu w usłudze Translator
titlesuffix: Azure Cognitive Services
description: Dokumentacja dotycząca V3.0 interfejs API tekstu usługi Translator.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9282d8af30cbfb3346394bcd71510faf8d8c8a21
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129390"
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

Tekst interfejsu API w wersji 3.0 jest dostępna w następujących chmury:

| Opis | Region | Podstawowy adres URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Global | API.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Authentication

Subskrybowanie do interfejsu API tłumaczenia tekstu w usługach Microsoft Cognitive Services i używają klucz subskrypcji (dostępne w witrynie Azure portal) do uwierzytelniania. 

Najprostszym sposobem jest przekazać klucz tajny platformy Azure do usługi Translator, przy użyciu nagłówka żądania `Ocp-Apim-Subscription-Key`.

Alternatywą jest do użycia tego klucza tajnego do uzyskania tokenu autoryzacji z usługi tokenu. Następnie należy przekazać token autoryzacji za pomocą usługi Translator `Authorization` nagłówek żądania. Aby uzyskać token autoryzacji, należy `POST` żądania do następującego adresu URL:

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

Aby podsumować, żądanie klienta interfejsu API usługi Translator będzie obejmować jeden nagłówek autoryzacji z poniższej tabeli:

<table width="100%">
  <th width="30%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>OCP-Apim-Subscription-Key</td>
    <td>*Używane z subskrypcją usług Cognitive Services, jeśli przekazujesz klucz tajny*.<br/>Wartość jest platformy Azure klucz tajny dla Twojej subskrypcji do interfejsu API tłumaczenia tekstu.</td>
  </tr>
  <tr>
    <td>Autoryzacja</td>
    <td>*Jeśli przekazujesz tokenu uwierzytelniania za pomocą subskrypcji usług Cognitive Services.*<br/>Wartość tokenu elementu nośnego: "Bearer <token>".</td>
  </tr>
</table> 

## <a name="errors"></a>Błędy

Odpowiedzi błędu standardowego jest obiekt JSON z pary nazwa/wartość o nazwie `error`. Wartość jest także obiekt JSON z właściwościami:

  * `code`Kod błędu zdefiniowany przez serwer.

  * `message`: Ciąg, zapewniając czytelny dla człowieka reprezentację błędu.

Na przykład klient z bezpłatnej subskrypcji próbnej będzie komunikat o błędzie po wyczerpaniu bezpłatny limit przydziału:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
