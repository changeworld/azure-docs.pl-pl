---
title: Format TSV testu wsadowego - QnA Maker
titleSuffix: Azure Cognitive Services
description: Opis formatu TSV do testowania wsadowego
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507826"
---
# <a name="batch-testing-tsv-format"></a>Format TSV testowania wsadowego

Testowanie wsadowe jest dostępne z [kodu źródłowego](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) lub jako plik [wykonywalny do pobrania spakowany.](https://aka.ms/qna_btzip) Format polecenia uruchomienia testu wsadowego jest:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Wartość oczekiwana|
|--|--|
|1|nazwa pliku tsv sformatowanego za pomocą [pól wejściowych TSV](#tsv-input-fields)|
|2|Identyfikator URI dla punktu końcowego z YOUR-HOST ze strony Publikowania portalu QnA Maker.|
|3|ENDPOINT-KEY, znaleziono na stronie Publikowania portalu QnA Maker.|
|4|nazwa pliku tsv utworzonego przez test wsadowy dla wyników.|

Poniższe informacje można wykorzystać, aby zrozumieć i zaimplementować format TSV do testowania wsadowego. 

## <a name="tsv-input-fields"></a>Pola wejściowe TSV

|Pola pliku wejściowego TSV|Uwagi|
|--|--|
|KBID|Identyfikator KB znajduje się na stronie Publikowanie.|
|Pytanie|Pytanie, które użytkownik wprowadziłby.|
|Tagi metadanych|optional|
|Parametr górny|optional| 
|Identyfikator oczekiwanej odpowiedzi|optional|

![Format wejściowy dla pliku TSV do testowania wsadowego.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Pola wyjściowe TSV 

|Parametry pliku wyjściowego TSV|Uwagi|
|--|--|
|KBID|Identyfikator KB znajduje się na stronie Publikowanie.|
|Pytanie|Pytanie wprowadzone z pliku wejściowego.|
|Odpowiedź|Najlepsza odpowiedź z bazy wiedzy.|
|Identyfikator odpowiedzi|Identyfikator odpowiedzi|
|Wynik|Wynik przewidywania odpowiedzi. |
|Tagi metadanych|związane z zwróconą odpowiedzią|
|Identyfikator oczekiwanej odpowiedzi|opcjonalne (tylko wtedy, gdy podany jest oczekiwany identyfikator odpowiedzi)|
|Etykieta wyroku|opcjonalne, wartości mogą być: poprawne lub nieprawidłowe (tylko wtedy, gdy udzielona jest oczekiwana odpowiedź)|
