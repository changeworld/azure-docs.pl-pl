---
title: Metoda BreakSentence interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Metoda BreakSentence interfejs API tekstu usługi Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: de11d493c1a42b67aeab6502f719bcee1adc6e99
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578210"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3.0: BreakSentence

Identyfikuje pozycjonowanie granic zdanie w fragment tekstu.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` limit czasu żądania:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania, parametry przekazane w ciągu zapytania są:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr zapytania*.<br/>Wersja interfejsu API zażądane przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Parametr opcjonalny zapytania*.<br/>Tag języka, który identyfikuje język tekstu wejściowego. Jeśli kod nie zostanie określony, zostaną zastosowane automatyczne wykrywanie języka.</td>
  </tr>
  <tr>
    <td>skrypt</td>
    <td>*Parametr opcjonalny zapytania*.<br/>Tag skryptu, który identyfikuje skryptu używany przez tekst wejściowy. Jeśli skrypt nie jest określony, domyślny skrypt języka zostanie przyjęta wartość.</td>
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

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiekt JSON z właściwością ciągu o nazwie `Text`. Granice zdania są obliczane jako wartość `Text` właściwości. Przykładowe treści żądania, z jednego fragmentu tekstu będzie wyglądać tak jak:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 100 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 10 000 znaków, łącznie ze spacjami.
* Cały tekst zawarty w żądaniu nie może przekraczać 50 000 znaków, łącznie ze spacjami.
* Jeśli `language` jest określony parametr zapytania, a następnie wszystkie elementy tablicy muszą być w tym samym języku. W przeciwnym razie automatycznego wykrywania języka jest stosowana do każdego elementu tablicy niezależnie.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON z jeden wynik dla każdego ciągu w tabeli wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `sentLen`: Tablica liczb całkowitych reprezentujących długości zdań w elemencie tekstowym. Długość tablicy jest liczba zdania, a wartości są długość każdego zdania. 

  * `detectedLanguage`: Obiekt opisujący wykryty język za pomocą następujących właściwości:

     * `language`: Kod wykryty język.

     * `score`: Wartość zmiennoprzecinkowa wskazująca pewność dotyczącą wyników. Wynik jest między zero a, oraz niskim wynikiem wskazuje niski zaufania.
     
    Należy pamiętać, że `detectedLanguage` właściwość jest obecny w obiekcie wynikowym tylko, gdy automatyczne wykrywanie języka.

Przykład odpowiedź JSON to:

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
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
  <th width="20%">Kod stanu</th>
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
    <td>Obiekt wywołujący wysyła zbyt wiele żądań.</td>
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

Poniższy przykład pokazuje, jak uzyskać zdania granice jednym zdaniu. Język zdanie jest wykrywany automatycznie przez usługę.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

