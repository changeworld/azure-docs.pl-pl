---
title: Metoda BreakSentence tekst interfejsu API Microsoft Translator | Dokumentacja firmy Microsoft
description: Użyj metody BreakSentence interfejsu API systemu Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 8ce6644d21b397ea0e7f2e71e3c3a5a96638eec5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349272"
---
# <a name="text-api-30-breaksentence"></a>Tekst interfejsu API 3.0: BreakSentence

Określa położenie granice zdanie w fragment tekstu.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania są parametry przekazywane w ciągu kwerendy:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr zapytania*.<br/>Wersja interfejsu API żądanego przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>język</td>
    <td>*Parametr opcjonalny zapytania*.<br/>Znacznik języka identyfikujący język wprowadzania tekstu. Jeśli nie określono kodu, automatyczne wykrywanie języka będą stosowane.</td>
  </tr>
  <tr>
    <td>skrypt</td>
    <td>*Parametr opcjonalny zapytania*.<br/>Tag skryptu identyfikowanie skryptów używanych przez wejściowego tekstu. Jeśli skrypt nie jest określony, przyjmuje się domyślny skrypt języka.</td>
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

Treść żądania jest tablica JSON. Każdy element tablicy jest obiektem JSON z właściwości ciągu o nazwie `Text`. Zdania granice są obliczane dla wartości `Text` właściwości. Przykład treści żądania z jednego fragmentu tekstu wygląda tak jak:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Mają zastosowanie następujące ograniczenia:

* Tablica może mieć co najwyżej 100 elementów.
* Wartość tekstowego elementu tablicy nie może przekroczyć 10 000 znaków, łącznie ze spacjami.
* Cały tekst w żądaniu uwzględniono nie może przekraczać 50 000 znaków, łącznie ze spacjami.
* Jeśli `language` został określony parametr zapytania, a następnie wszystkie elementy tablicy muszą być w tym samym języku. W przeciwnym razie automatycznego wykrywania języka jest stosowane do każdego elementu tablicy niezależnie.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON o jeden wynik dla każdego ciągu w tablicy wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `sentLen`: Tablica liczb całkowitych reprezentujący długości zdań w elemencie tekstowym. Długość tablicy jest liczbę zdań i wartości są długość każdego zdania. 

  * `detectedLanguage`: Obiekt opisujący języka wykryte za pomocą następujących właściwości:

     * `language`: Kod języka wykryte.

     * `score`: Float wartość wskazującą zaufania w wyniku. Wynik jest od zera do jednego i niski wynik wskazuje niski zaufania.
     
    Należy pamiętać, że `detectedLanguage` właściwości jest obecna tylko w obiekcie wynikowym zleconą automatycznego wykrywania języka.

Przykładowa odpowiedź w formacie JSON wygląda następująco:

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

Poniższy przykład pokazuje, jak uzyskać zdanie granice dla pojedynczego zdanie. Język zdania jest automatyczne wykrywany przez usługę.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

