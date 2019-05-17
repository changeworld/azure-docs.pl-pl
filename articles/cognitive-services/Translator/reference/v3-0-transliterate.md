---
title: Interfejs API tekstu usługi Translator transliteracja — metoda
titlesuffix: Azure Cognitive Services
description: Metoda transliteracja interfejsu API usługi Translator tekstu.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: cd7fee34f020f16d1511bc57b4ad86dbd5eb168d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796961"
---
# <a name="translator-text-api-30-transliterate"></a>Translator Text API 3.0: Transliterate

Konwertuje tekst w jednym języku ze skryptu jeden inny skrypt.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` limit czasu żądania:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania, parametry przekazane w ciągu zapytania są:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API zażądane przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>język</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu, aby przekonwertować z jednego skryptu. Języki możliwe są wymienione w `transliteration` zakres uzyskać, badając usługę dla jego [obsługiwane języki](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Wymagany parametr*.<br/>Określa skrypt używany przez tekst wejściowy. Wyszukaj [obsługiwane języki](./v3-0-languages.md) przy użyciu `transliteration` zakres, aby znaleźć dostępnych danych wejściowych skryptów dla wybranego języka.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Wymagany parametr*.<br/>Określa skrypt wyjściowy. Wyszukaj [obsługiwane języki](./v3-0-languages.md) przy użyciu `transliteration` zakres, aby znaleźć dostępne dla wybranej kombinacji języka skryptów danych wyjściowych i wejściowych skryptu.</td>
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

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiekt JSON z właściwością ciągu o nazwie `Text`, która reprezentuje ciąg do przekonwertowania.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 1000 znaków, łącznie ze spacjami.
* Cały tekst zawarty w żądaniu nie może przekraczać 5000 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON z jeden wynik dla każdego elementu w tablicy wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `text`: Ciąg, który jest wynikiem konwersji ciągu wejściowego do skryptu, danych wyjściowych.
  
  * `script`: Ciąg określający skrypt używany w danych wyjściowych.

Przykład odpowiedź JSON to:

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

Poniższy przykład pokazuje, jak przekonwertować dwa ciągi znaków japońskich na Romanized japońskiej.

# <a name="curltabcurl"></a>[curl](#tab/curl)

Ładunek JSON dla żądania, w tym przykładzie:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Korzystając z programu cURL w oknie wiersza polecenia, który nie obsługuje znaki Unicode, wykonaj następujące ładunek w formacie JSON i zapisz go w pliku o nazwie `request.txt`. Pamiętaj zapisać plik z `UTF-8` kodowania.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
