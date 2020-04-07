---
title: Baza wiedzy projektowej - QnA Maker
description: Baza wiedzy QnA Maker składa się z zestawu zestawów pytań i odpowiedzi (QnA) i opcjonalnych metadanych skojarzonych z każdą parą QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 1d24434d1343f4174cfbfeb3a30c36737b213168
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756786"
---
# <a name="question-and-answer-set-concepts"></a>Pojęcia zestawu pytań i odpowiedzi

Baza wiedzy składa się z zestawów pytań i odpowiedzi (QnA).  Każdy zestaw ma jedną odpowiedź, a zestaw zawiera wszystkie informacje związane z tą _odpowiedzią_. Odpowiedź może luźno przypominać wiersz bazy danych lub wystąpienie struktury danych.

## <a name="question-and-answer-sets"></a>Zestawy pytań i odpowiedzi

**Wymagane** ustawienia w zestawie pytań i odpowiedzi (QnA) to:

* **pytanie** - tekst zapytania użytkownika, używany do uczenia maszynowego QnA Maker, aby dostosować się do tekstu pytania użytkownika z innym sformułowaniem, ale tą samą odpowiedzią
* **odpowiedź** - odpowiedź zestawu jest odpowiedzią, która jest zwracana, gdy zapytanie użytkownika jest dopasowywany do skojarzonego pytania

Każdy zestaw jest reprezentowany przez **identyfikator**.

**Opcjonalne** ustawienia zestawu obejmują:

* **Alternatywne formy pytania** - to pomaga QnA Maker zwrócić poprawną odpowiedź dla szerszej gamy fraz pytanie
* **Metadane**: Metadane są tagami skojarzonymi z parą QnA i są reprezentowane jako pary klucz-wartość. Tagi metadanych służą do filtrowania par QnA i ograniczania zestawu, nad którym jest wykonywane dopasowywanie zapytań.
* **Monity z wieloma turami,** używane do kontynuowania konwersacji wieloekrętowej

![Bazy wiedzy QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Edytorze dodaj do bazy wiedzy

Jeśli nie masz wcześniej istniejącej zawartości do zapełnienia bazy wiedzy, możesz dodać pary QnA zgodnie z artykułem w portalu QnA Maker. Dowiedz się, jak zaktualizować swoją bazę wiedzy [tutaj](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Lokalna edycja bazy wiedzy

Po utworzeniu bazy wiedzy zaleca się wprowadzanie zmian w tekście bazy wiedzy w [portalu QnA Maker](https://qnamaker.ai)zamiast eksportowania i ponownego zaimportowania za pośrednictwem plików lokalnych. Jednak może się okazać, że trzeba edytować bazę wiedzy lokalnie.

Wyeksportuj bazę wiedzy ze strony **Ustawienia,** a następnie edytuj bazę wiedzy za pomocą programu Microsoft Excel. Jeśli zdecydujesz się użyć innej aplikacji do edycji eksportowanego pliku, aplikacja może wprowadzić błędy składni, ponieważ nie jest w pełni zgodna z TSV. Pliki TSV programu Microsoft Excel zazwyczaj nie wprowadzają żadnych błędów formatowania.

Po zakończeniu wprowadzania zmian ponownie zaimportuj plik TSV ze strony **Ustawienia.** Spowoduje to całkowite zastąpienie bieżącej bazy wiedzy zaimportowanymi bazą wiedzy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Podstawowy cykl życia wiedzy w QnA Maker](./development-lifecycle-knowledge-base.md)