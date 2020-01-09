---
title: interfejs API tłumaczenia tekstu w usłudze Translator Metoda BreakSentence
titleSuffix: Azure Cognitive Services
description: Metoda interfejs API tłumaczenia tekstu w usłudze Translator BreakSentence określa pozycjonowanie granic zdania w fragmencie tekstu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: c8ef1d4dacf500c459ae1ab9a534ed118ca9e05a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446693"
---
# <a name="translator-text-api-30-breaksentence"></a>Interfejs API tłumaczenia tekstu w usłudze Translator 3,0: BreakSentence

Określa położenie granic zdania w fragmencie tekstu.

## <a name="request-url"></a>Adres URL żądania

Wyślij żądanie `POST` do:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td>*Wymagany parametr zapytania*.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Opcjonalny parametr zapytania*.<br/>Tag języka identyfikujący język tekstu wejściowego. Jeśli kod nie zostanie określony, zostanie zastosowane automatyczne wykrywanie języka.</td>
  </tr>
  <tr>
    <td>script</td>
    <td>*Opcjonalny parametr zapytania*.<br/>Tag skryptu identyfikujący skrypt używany przez tekst wejściowy. Jeśli skrypt nie jest określony, zostanie przyjęty domyślny skrypt języka.</td>
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
    <td>*Wymagany nagłówek żądania*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td>*Wymagany nagłówek żądania*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania. Należy pamiętać, że ten nagłówek można pominąć, jeśli w ciągu zapytania zostanie uwzględniony identyfikator śledzenia, przy użyciu parametru zapytania o nazwie `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością ciągu o nazwie `Text`. Granice zdania są obliczane dla wartości właściwości `Text`. Przykładowa treść żądania z jednym fragmentem tekstu wygląda następująco:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Mają zastosowanie następujące ograniczenia:

* Tablica może zawierać maksymalnie 100 elementów.
* Wartość tekstowa elementu tablicy nie może być dłuższa niż 10 000 znaków, w tym spacje.
* Cały tekst zawarty w żądaniu nie może zawierać więcej niż 50 000 znaków, w tym spacji.
* Jeśli parametr zapytania `language` jest określony, wszystkie elementy tablicy muszą być w tym samym języku. W przeciwnym razie Autowykrywanie języka jest stosowane do każdego elementu tablicy niezależnie.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `sentLen`: tablica liczb całkowitych reprezentująca długości zdań w elemencie tekstowym. Długość tablicy jest liczbą zdań, a wartości to długość każdego zdania. 

  * `detectedLanguage`: obiekt opisujący wykryty język przez następujące właściwości:

     * `language`: Kod wykrytego języka.

     * `score`: wartość zmiennoprzecinkowa wskazująca wiarygodność w wyniku. Wynik jest z przedziału od zera do jednego, a niski Wynik wskazuje na niski poziom pewności.
     
    Należy pamiętać, że właściwość `detectedLanguage` jest obecna tylko w obiekcie wynikowym, gdy żąda się automatycznego wykrywania języka.

Przykładowa odpowiedź JSON to:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
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
    <td>Wartość wygenerowana przez usługę w celu zidentyfikowania żądania. Służy do rozwiązywania problemów.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie. 

<table width="100%">
  <th width="20%">Kod stanu</th>
  <th>Opis</th>
  <tr>
    <td>200</td>
    <td>Powodzenie.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy. Popraw parametry żądania przed ponowną próbą.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określone i prawidłowe.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie jest autoryzowany. Sprawdź komunikat o błędzie szczegóły. Często oznacza to, że używane są wszystkie bezpłatne tłumaczenia udostępnione w ramach wersji próbnej.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie z powodu przekroczenia limitów żądań przez klienta.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go z: Data i godzina błędu, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`i identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go z: Data i godzina błędu, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`i identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci także odpowiedź na błąd JSON. Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, a następnie 3-cyfrowy numer do dalszej kategoryzacji błędu. Typowe kody błędów można znaleźć na [stronie odniesienia interfejs API tłumaczenia tekstu w usłudze translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak uzyskać granice zdania dla pojedynczego zdania. Język zdania jest automatycznie wykrywany przez usługę.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

