---
title: Tekst Microsoft Translator interfejsu API Transliterate metody | Dokumentacja firmy Microsoft
description: Użyj metody Transliterate interfejsu API zestawu Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: fdd6fa9236f0c02685198b6de3228c444993dad6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349264"
---
# <a name="text-api-30-transliterate"></a>Tekst interfejsu API 3.0: Transliterate

Konwertuje tekst w jednym języku z jednego skryptu do innego skryptu.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>język</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu, aby przekonwertować z jednego skryptu. Możliwe języki są wymienione w `transliteration` zakresu uzyskany przez zapytanie usługi dla jego [obsługiwanych języków](.\v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Wymagany parametr*.<br/>Określa skryptów używanych przez wejściowego tekstu. Wyszukiwanie [obsługiwanych języków](.\v3-0-languages.md) przy użyciu `transliteration` zakresu, można znaleźć wejściowych skrypty dostępna dla wybranego języka.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Wymagany parametr*.<br/>Określa dane wyjściowe skryptu. Wyszukiwanie [obsługiwanych języków](.\v3-0-languages.md) przy użyciu `transliteration` zakres, aby znaleźć dostępne dla wybranej kombinacji język skryptów danych wyjściowych i wejściowych skryptu.</td>
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

Treść żądania jest tablica JSON. Każdy element tablicy jest obiektem JSON z właściwości ciągu o nazwie `Text`, który reprezentuje ciąg do przekonwertowania.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Mają zastosowanie następujące ograniczenia:

* Tablica może mieć maksymalnie 10 elementów.
* Wartość tekstowego elementu tablicy nie może przekraczać 1000 znaków, łącznie ze spacjami.
* Cały tekst w żądaniu uwzględniono nie może przekraczać 5000 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON o jeden wynik dla każdego elementu w tablicy wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `text`: Ciąg, który jest wynikiem konwertowania ciągu wejściowego do danych wyjściowych skryptu.
  
  * `script`: Ciąg określający skrypt używane w danych wyjściowych.

Przykładowa odpowiedź w formacie JSON wygląda następująco:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

Poniższy przykład pokazuje, jak przekonwertować dwa ciągi znaków japońskich na Romanized japońskiego.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

Ładunek JSON dla żądania, w tym przykładzie:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Jeśli używasz cUrl w oknie wiersza polecenia, który nie obsługuje znaków Unicode, podejmij następujące ładunek JSON i zapisz go w pliku o nazwie `request.txt`. Pamiętaj zapisać plik z `UTF-8` kodowania.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
