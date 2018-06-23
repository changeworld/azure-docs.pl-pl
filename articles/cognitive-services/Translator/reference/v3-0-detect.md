---
title: Tekst Microsoft Translator interfejsu API wykryć — metoda | Dokumentacja firmy Microsoft
description: Użyj metody wykrywania Microsoft Translator tekst interfejsu API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 7e81e91230e1ada4423d77d22134b1b64df65d9d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349265"
---
# <a name="text-api-30-detect"></a>Tekst interfejsu API 3.0: wykrywania

Określa język fragmentu tekstu.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania są parametry przekazywane w ciągu kwerendy:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądanego przez klienta. Wartość musi być `3.0`.</td>
  </tr>
</table> 

Nagłówki żądania obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>_Jeden autoryzacji_<br/>_header_</td>
    <td>*Nagłówek żądania wymagane*.<br/>Zobacz [dostępne opcje dla uwierzytelniania](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Nagłówek żądania wymagane*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length.</td>
    <td>*Nagłówek żądania wymagane*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Generowane przez klientów identyfikator GUID, aby jednoznacznie zidentyfikować żądania. Należy pamiętać, że można pominąć ten nagłówek, jeśli zawierają identyfikator śledzenia w ciągu zapytania za pomocą parametru zapytania o nazwie `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablica JSON. Każdy element tablicy jest obiektem JSON z właściwości ciągu o nazwie `Text`. Wykrywanie języka jest stosowany do wartości `Text` właściwości. Treść żądania próbki wygląda tak jak:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Mają zastosowanie następujące ograniczenia:

* Tablica może mieć co najwyżej 100 elementów.
* Wartość tekstowego elementu tablicy nie może przekroczyć 10 000 znaków, łącznie ze spacjami.
* Cały tekst w żądaniu uwzględniono nie może przekraczać 50 000 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON o jeden wynik dla każdego ciągu w tablicy wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `language`: Kod języka wykryte.

  * `score`: Float wartość wskazującą zaufania w wyniku. Wynik jest od zera do jednego i niski wynik wskazuje niski zaufania.

  * `isTranslationSupported`Wartość logiczna, która ma wartość true, jeśli wykryto język jest jednym z języków obsługiwanych na tłumaczenie tekstu.

  * `isTransliterationSupported`Wartość logiczna, która ma wartość true, jeśli język wykryte języki obsługiwane w przypadku transliterację.
  
  * `alternatives`: Tablica innych języków możliwe. Każdy element tablicy jest inny obiekt o tej samej wymienionych powyżej właściwości: `language`, `score`, `isTranslationSupported` i `isTransliterationSupported`.

Przykładowa odpowiedź w formacie JSON wygląda następująco:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Identyfikator żądania X</td>
    <td>Wartość wygenerowana przez usługę, aby zidentyfikować żądania. Jest on używany na potrzeby rozwiązywania problemów.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie. 

<table width="100%">
  <th width="20%">Kod stanu</th>
  <th>Opis</th>
  <tr>
    <td>200</td>
    <td>Powodzenie.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Jeden z parametrów zapytania jest lub jest ona nieprawidłowa. Popraw parametry żądania przed ponowną próbą.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określonego i prawidłowe.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie jest autoryzowane. Sprawdź szczegóły komunikatu o błędzie. Często oznacza zużyte wszystkie tłumaczenia wolnego dostarczane z subskrypcji wersji próbnej.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Obiekt wywołujący wysyła zbyt wiele żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go przy użyciu: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`, a identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go przy użyciu: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`, a identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak pobrać języki obsługiwane w przypadku tłumaczenie tekstu.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```

---
