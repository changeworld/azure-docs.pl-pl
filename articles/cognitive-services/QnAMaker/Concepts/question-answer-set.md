---
title: Baza wiedzy o projekcie QnA Maker
description: Baza wiedzy QnA Maker składa się z zestawu zestawów pytań i odpowiedzi (QnA) oraz opcjonalnych metadanych skojarzonych z poszczególnymi parami QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844324"
---
# <a name="question-and-answer-set-concepts"></a>Pojęcia dotyczące ustawiania pytania i odpowiedzi

Baza wiedzy składa się z zestawów pytań i odpowiedzi (QnA).  Każdy zestaw ma jedną odpowiedź, a zestaw zawiera wszystkie informacje skojarzone z tą _odpowiedzią_. Odpowiedź może być luźno podobna do wiersza bazy danych lub wystąpienia struktury danych.

## <a name="question-and-answer-sets"></a>Zestawy pytań i odpowiedzi

**Wymagane** ustawienia w zestawie pytań i odpowiedzi (QNA) są następujące:

* **pytanie** — tekst zapytania użytkownika, używany do QNA Maker uczenia maszynowego w celu wyrównania z tekstem pytania użytkownika z różnymi wyrazami, ale z tą samą odpowiedzią
* odpowiedź **— odpowiedź** zestawu jest odpowiedzią zwracaną, gdy zapytanie użytkownika jest dopasowane do skojarzonego pytania

Każdy zestaw jest reprezentowany przez **Identyfikator**.

**Opcjonalne** ustawienia dla zestawu obejmują:

* **Alternatywne formy pytania** — pomaga QNA Maker zwrócić poprawną odpowiedź w celu uzyskania większej liczby pytań frazy
* **Metadane**: metadane są tagami skojarzonymi z parą QNA i są reprezentowane jako pary klucz-wartość. Tagi metadanych są używane do filtrowania par QnA i ograniczają zestaw, w jakim jest wykonywane Dopasowywanie zapytań.
* Wyświetlanie wielowierszowych **ekranów**, służących do kontynuowania konwersacji z obsługą wielodostępności

![Usługa QnA Maker baz wiedzy](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Przez jej tradycyjne zredagowanie dodać do bazy wiedzy

Jeśli nie masz wstępnie istniejącej zawartości do zapełnienia bazy wiedzy, możesz dodać zestawy QnA z redakcją w portalu QnA Maker. Dowiedz się, jak można zaktualizować bazy wiedzy [tutaj](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Lokalne edytowanie bazy wiedzy

Po utworzeniu bazy wiedzy zaleca się dokonanie edycji tekstu bazy wiedzy w [portalu QNA Maker](https://qnamaker.ai), a nie eksportowanie i ponowne importowanie za pomocą plików lokalnych. Mogą jednak wystąpić sytuacje, w których należy edytować bazę wiedzy lokalnie.

Wyeksportuj bazę wiedzy ze strony **Ustawienia** , a następnie przeprowadź edycję bazy wiedzy przy użyciu programu Microsoft Excel. Jeśli zdecydujesz się użyć innej aplikacji do edytowania wyeksportowanego pliku, aplikacja może wprowadzić błędy składniowe, ponieważ nie jest w pełni zgodna z TSV. Pliki TSV programu Microsoft Excel zwykle nie wprowadzają żadnych błędów formatowania.

Po dokonaniu edycji ponownie zaimportuj plik TSV ze strony **ustawień** . Spowoduje to całkowite zastąpienie bieżącej bazy wiedzy zaimportowaną bazą wiedzy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cykl życia bazy wiedzy w QnA Maker](./development-lifecycle-knowledge-base.md)