---
title: Metoda transliteratu interfejsu API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: Konwertuj tekst w jednym języku z jednego skryptu na inny za pomocą metody Transliterate interfejsu API tłumacza.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837278"
---
# <a name="translator-text-api-30-transliterate"></a>Interfejs API tekstu tłumacza 3.0: Transliterate

Konwertuje tekst w jednym języku z jednego skryptu na inny.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` prośbę na:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazywane na ciąg zapytania są:

<table width="100%">
  <th width="20%">Parametr kwerendy</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi `3.0`być .</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu do konwersji z jednego skryptu na inny. Możliwe języki są wymienione `transliteration` w zakresie uzyskanym przez zapytanie usługi dla [obsługiwanych języków](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript (odScript)</td>
    <td>*Wymagany parametr*.<br/>Określa skrypt używany przez tekst wejściowy. Wyszukuj [obsługiwane](./v3-0-languages.md) `transliteration` języki przy użyciu zakresu, aby znaleźć skrypty wejściowe dostępne dla wybranego języka.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Wymagany parametr*.<br/>Określa skrypt wyjściowy. Wyszukuj [obsługiwane](./v3-0-languages.md) `transliteration` języki przy użyciu zakresu, aby znaleźć skrypty wyjściowe dostępne dla wybranej kombinacji języka wejściowego i skryptu wejściowego.</td>
  </tr>
</table> 

Nagłówki żądań obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Nagłówki uwierzytelniania</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Wymagany nagłówek żądania*.<br/>Określa typ zawartości ładunku. Możliwe wartości `application/json`to: .</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td>*Wymagany nagłówek żądania*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>Identyfikator X-ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Identyfikator GUID wygenerowany przez klienta, aby jednoznacznie zidentyfikować żądanie. Należy zauważyć, że można pominąć ten nagłówek, jeśli dodasz identyfikator `ClientTraceId`śledzenia do ciągu zapytania przy użyciu parametru kwerendy o nazwie .</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością string o nazwie `Text`, która reprezentuje ciąg do konwersji.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 1000 znaków, w tym spacji.
* Cały tekst zawarty w żądaniu nie może przekraczać 5000 znaków, w tym spacji.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź jest tablicą JSON z jednym wynikiem dla każdego elementu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `text`: Ciąg, który jest wynikiem konwersji ciągu wejściowego do skryptu wyjściowego.
  
  * `script`: Ciąg określający skrypt używany w danych wyjściowych.

Przykładowa odpowiedź JSON to:

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
    <td>X-RequestId (ida żądania X)</td>
    <td>Wartość generowana przez usługę w celu zidentyfikowania żądania. Jest on używany do rozwiązywania problemów.</td>
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
    <td>Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy. Popraw parametry żądania przed ponowieniem próby.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określone i prawidłowe.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie jest autoryzowane. Sprawdź komunikat o błędzie szczegółów. Często oznacza to, że wszystkie bezpłatne tłumaczenia dostarczane z subskrypcją próbną zostały wykorzystane.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie, ponieważ klient przekroczył limity żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzał, zgłoś go z: datą i godziną awarii, identyfikatorem żądania z nagłówka `X-RequestId`odpowiedzi i identyfikatorem klienta z nagłówka `X-ClientTraceId`żądania .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer tymczasowo niedostępny. Ponów próbę żądania. Jeśli błąd będzie się powtarzał, zgłoś go z: datą i godziną awarii, identyfikatorem żądania z nagłówka `X-RequestId`odpowiedzi i identyfikatorem klienta z nagłówka `X-ClientTraceId`żądania .</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci również odpowiedź na błąd JSON. Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, po którym następuje 3-cyfrowy numer w celu dalszej kategoryzowania błędu. Typowe kody błędów można znaleźć na [stronie odwołania do interfejsu API tekstu tłumacza translatora](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Przykłady

W poniższym przykładzie pokazano, jak przekonwertować dwa japońskie ciągi na romanizowany japoński.

Ładunek JSON dla żądania w tym przykładzie:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Jeśli używasz cURL w oknie wiersza polecenia, które nie obsługuje znaków Unicode, weź następujący `request.txt`ładunek JSON i zapisz go w pliku o nazwie . Pamiętaj, aby zapisać `UTF-8` plik z kodowaniem.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
