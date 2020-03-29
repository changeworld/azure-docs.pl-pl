---
title: Metoda przerwania interfejsu API tłumacza
titleSuffix: Azure Cognitive Services
description: Metoda BreakSentence interfejsu API tłumacza identyfikuje pozycjonowanie granic zdania w tekście.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 4c314148b8e1495a8b5a12c42d4989d13cdd6a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548122"
---
# <a name="translator-text-api-30-breaksentence"></a>Interfejs API tekstu tłumacza 3.0: BreakSentence

Identyfikuje położenie granic zdania w tekście.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` prośbę na:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazywane na ciąg zapytania są:

| Parametr kwerendy | Opis |
| -------| ----------- |
| api-version <img width=200/>   | **Wymagany parametr kwerendy**.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi `3.0`być . |
| language | **Opcjonalny parametr kwerendy**.<br/>Tag języka identyfikujący język tekstu wejściowego. Jeśli kod nie zostanie określony, zostanie zastosowane automatyczne wykrywanie języka. |
| skrypt    | **Opcjonalny parametr kwerendy**.<br/>Znacznik skryptu identyfikujący skrypt używany przez tekst wejściowy. Jeśli skrypt nie zostanie określony, zostanie przyjęty domyślny skrypt języka.  | 

Nagłówki żądań obejmują:

| Nagłówki | Opis |
| ------- | ----------- |
| Nagłówki uwierzytelniania <img width=200/>  | **Wymagany nagłówek żądania**.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>. |
| Content-Type | **Wymagany nagłówek żądania**.<br/>Określa typ zawartości ładunku. Możliwe wartości `application/json`to: . |
| Długość zawartości    | **Wymagany nagłówek żądania**.<br/>Długość treści żądania.  | 
| Identyfikator X-ClientTraceId   | **Opcjonalnie**.<br/>Identyfikator GUID wygenerowany przez klienta, aby jednoznacznie zidentyfikować żądanie. Należy zauważyć, że można pominąć ten nagłówek, jeśli dodasz identyfikator `ClientTraceId`śledzenia do ciągu zapytania przy użyciu parametru kwerendy o nazwie .  | 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością string o nazwie `Text`. Granice zdań są obliczane `Text` dla wartości właściwości. Przykładowy treść żądania z jednym fragmentem tekstu wygląda następująco:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 100 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 10 000 znaków, w tym spacji.
* Cały tekst zawarty w żądaniu nie może przekraczać 50 000 znaków wraz ze spacjami.
* Jeśli `language` parametr kwerendy jest określony, wszystkie elementy tablicy muszą być w tym samym języku. W przeciwnym razie automatyczne wykrywanie języka jest stosowane do każdego elementu tablicy niezależnie.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź jest tablicą JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `sentLen`: Tablica liczby całkowitych reprezentująca długości zdań w elemencie tekstowym. Długość tablicy jest liczbą zdań, a wartości są długością każdego zdania. 

  * `detectedLanguage`: Obiekt opisujący wykryty język za pomocą następujących właściwości:

     * `language`: Kod wykrytego języka.

     * `score`: Wartość pływaka wskazująca zaufanie do wyniku. Wynik jest od zera do jednego, a niski wynik wskazuje na niską pewność siebie.
     
    Należy zauważyć, że `detectedLanguage` właściwość jest obecny tylko w obiekcie wynik, gdy wymagane jest automatyczne wykrywanie języka.

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

W poniższym przykładzie pokazano, jak uzyskać granice zdania dla pojedynczego zdania. Język zdania jest automatycznie wykrywany przez usługę.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

