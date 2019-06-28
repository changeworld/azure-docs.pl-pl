---
title: Translator Text API Detect Method
titlesuffix: Azure Cognitive Services
description: Metoda Translator tekstu interfejsu API wykrywania.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 6e90626759b3c78d98c8c0f6e32d37c1440385cd
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357735"
---
# <a name="translator-text-api-30-detect"></a>Translator Text API 3.0: Detect

Określa język fragmentu tekstu.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` limit czasu żądania:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania, parametry przekazane w ciągu zapytania są:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API zażądane przez klienta. Wartość musi być `3.0`.</td>
  </tr>
</table> 

Nagłówki żądania obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Nagłówki uwierzytelniania</td>
    <td><em>Nagłówek żądania wymagane</em>.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Nagłówek żądania wymagane*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td>*Nagłówek żądania wymagane*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Generowane przez klienta identyfikator GUID do unikatowego identyfikowania żądania. Należy pamiętać, że można pominąć tego pliku nagłówkowego, jeśli zawierają identyfikator śledzenia w ciągu zapytania za pomocą parametru zapytania o nazwie `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiekt JSON z właściwością ciągu o nazwie `Text`. Wykrywanie języka jest stosowany do wartości `Text` właściwości. Treść żądania przykładowe będzie wyglądać tak jak:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 100 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 10 000 znaków, łącznie ze spacjami.
* Cały tekst zawarty w żądaniu nie może przekraczać 50 000 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON z jeden wynik dla każdego ciągu w tabeli wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `language`: Kod wykryty język.

  * `score`: Wartość zmiennoprzecinkowa wskazująca pewność dotyczącą wyników. Wynik jest między zero a, oraz niskim wynikiem wskazuje niski zaufania.

  * `isTranslationSupported`: Wartość logiczna, która ma wartość true, jeśli wykryty język jest jednym z języków obsługiwanych na potrzeby tłumaczenia tekstu.

  * `isTransliterationSupported`: Wartość logiczna, która ma wartość true, jeśli wykryty język jest jednym z języków obsługiwanych dla transliterację.
  
  * `alternatives`: Tablica inne języki możliwe. Każdy element tablicy jest inny obiekt z tymi samymi właściwościami wymienionych powyżej: `language`, `score`, `isTranslationSupported` i `isTransliterationSupported`.

Przykład odpowiedź JSON to:

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
    <td>X-RequestId</td>
    <td>Wartość generowane przez usługę w celu zidentyfikowania żądania. Jest on używany na potrzeby rozwiązywania problemów.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kody stanów odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie. 

<table width="100%">
  <th width="20%">Kod stanu:</th>
  <th>Opis</th>
  <tr>
    <td>200</td>
    <td>Powodzenie.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>To jeden z parametrów zapytania, lub jest on nieprawidłowy. Popraw parametry żądania przed ponowieniem próby.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określonego i prawidłowy.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie jest autoryzowany. Sprawdź szczegóły komunikatu o błędzie. Często oznacza to, zużyte wszystkie bezpłatne tłumaczenia dostarczane z subskrypcji wersji próbnej.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie, ponieważ klienta przekroczyła limity żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`oraz identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`oraz identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci błąd odpowiedź w formacie JSON. Kod błędu to łączenie liczb 6-cyfrowym, 3-cyfrowy kod stanu HTTP następuje 3-cyfrowy numer do dalszego kategoryzowanie błędu. Typowe kody błędów można znaleźć na [strona referencyjna interfejsu API tłumaczenia tekstu w wersji 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak pobrać języki obsługiwane w przypadku tłumaczenie tekstu.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```

---
