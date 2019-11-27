---
title: Konstruktor wyrażeń przepływu danych mapowania Azure Data Factory
description: Konstruktor wyrażeń dla Azure Data Factory mapowania przepływów danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 3664a7c311e15ce3aa61fc71f98a46e3f2618143
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184668"
---
# <a name="mapping-data-flow-expression-builder"></a>Konstruktor wyrażeń przepływu danych mapowania



W obszarze Azure Data Factory mapowanie przepływu danych znajdziesz pola wyrażeń, w których można wprowadzać wyrażenia do przekształcania danych. W tych polach używaj kolumn, pól, zmiennych, parametrów, funkcji w przepływie danych. Aby skompilować wyrażenie, użyj konstruktora wyrażeń, który jest uruchamiany przez kliknięcie w polu tekstowym wyrażenia wewnątrz transformacji. W przypadku wybrania kolumn do przekształcenia czasami widoczne są opcje "kolumna obliczana". Po kliknięciu tego przycisku zobaczysz również uruchomiony program Expression Builder.

![Konstruktor wyrażeń](media/data-flow/xpb1.png "Konstruktor wyrażeń")

Narzędzie Expression Builder domyślnie ustawia opcję Edytor tekstu. Funkcja autouzupełniania odczytuje z całego modelu obiektów przepływu danych Azure Data Factory ze sprawdzaniem składni i wyróżnieniem.

![Autouzupełnianie konstruktora wyrażeń](media/data-flow/expb1.png "Autouzupełnianie konstruktora wyrażeń")

## <a name="build-schemas-in-output-schema-pane"></a>Kompiluj schematy w okienku schematu danych wyjściowych

![Dodawanie złożonej kolumny](media/data-flow/complexcolumn.png "Dodaj kolumny")

W okienku schematu danych wyjściowych po lewej stronie zostaną wyświetlone kolumny, które są modyfikowane i dodawane do schematu. W tym miejscu możesz interaktywnie kompilować proste i złożone struktury danych. Dodaj dodatkowe pola przy użyciu opcji "Dodaj kolumnę" i Utwórz hierarchie przy użyciu polecenia "Dodaj podkolumnę".

![Dodaj podkolumnę](media/data-flow/addsubcolumn.png "Dodaj podkolumnę")

## <a name="data-preview-in-debug-mode"></a>Podgląd danych w trybie debugowania

![Konstruktor wyrażeń](media/data-flow/exp4b.png "Podgląd danych wyrażeń")

Gdy Pracujesz w wyrażeniach przepływu danych, Przełącz tryb debugowania z powierzchni projektowej przepływu danych Azure Data Factory, włączając w to aktualne Podgląd wyników danych z utworzonego wyrażenia. Debugowanie na żywo w czasie rzeczywistym jest włączone dla wyrażeń.

![Tryb debugowania](media/data-flow/debugbutton.png "Przycisk Debuguj")

Kliknij przycisk Odśwież, aby zaktualizować wyniki wyrażenia na żywo próbki źródła w czasie rzeczywistym.

![Konstruktor wyrażeń](media/data-flow/exp5.png "Podgląd danych wyrażeń")

## <a name="comments"></a>Komentarze

Dodaj komentarze do wyrażeń przy użyciu jednowierszowej i wielowierszowej składni komentarza:

![Komentarze](media/data-flow/comments.png "Komentarze")

## <a name="regular-expressions"></a>Wyrażenia regularne

Język wyrażeń przepływu danych Azure Data Factory, [pełna dokumentacja referencyjna](https://aka.ms/dataflowexpressions), umożliwia korzystanie z funkcji, które zawierają składnię wyrażenia regularnego. W przypadku korzystania z funkcji wyrażenia regularnego, Konstruktor wyrażeń będzie próbować interpretować ukośnik odwrotny (\\) jako sekwencję znaków ucieczki. W przypadku używania ukośników odwrotnych w wyrażeniu regularnym należy ująć całe wyrażenie regularne w Takty (\`) lub użyć podwójnego ukośnika odwrotnego.

Przykład użycia znaczników

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

lub przy użyciu podwójnego ukośnika

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Indeksowanie tablicy adresów

Za pomocą funkcji wyrażeń, które zwracają tablice, użyj nawiasów kwadratowych [] do adresowania określonych indeksów wewnątrz tego obiektu tablicy zwracanej. Tablica jest zależna od siebie.

![Tablica konstruktora wyrażeń](media/data-flow/expb2.png "Podgląd danych wyrażeń")

## <a name="handling-names-with-special-characters"></a>Obsługa nazw ze znakami specjalnymi

Jeśli masz nazwy kolumn, które zawierają znaki specjalne lub spacje, umieść ją w nawiasach klamrowych.
* ```{[dbo].this_is my complex name$$$}```

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

* ```Ctrl-K Ctrl-C```: Komentarze cały wiersz
* ```Ctrl-K Ctrl-U```: usuwanie komentarza
* ```F1```: udostępnianie poleceń pomocy edytora
* ```Alt-Down Arrow```: Przenieś bieżący wiersz w dół
* ```Alt-Up Arrow```: Przenieś bieżący wiersz w górę
* ```Cntrl-Space```: Pokaż Pomoc kontekstową

## <a name="manual-comments"></a>Komentarze ręczne

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Jeśli umieścisz komentarz w górnej części wyrażenia, pojawi się w polu tekstowym przekształcenia, aby udokumentować wyrażenia przekształcenia:

![Komentarze](media/data-flow/comments2.png "Komentarze")

## <a name="convert-to-dates-or-timestamps"></a>Konwertuj na daty lub sygnatury czasowe

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Należy pamiętać, że w celu uwzględnienia literałów ciągu w danych wyjściowych znacznika czasu trzeba otoczyć konwersję wewnątrz metody toString ()

## <a name="handling-column-names-with-special-characters"></a>Obsługa nazw kolumn ze znakami specjalnymi

Jeśli masz nazwy kolumn, które zawierają znaki specjalne lub spacje, umieść ją w nawiasach klamrowych.

```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Następne kroki

[Rozpocznij tworzenie wyrażeń transformacji danych](data-flow-expression-functions.md)
