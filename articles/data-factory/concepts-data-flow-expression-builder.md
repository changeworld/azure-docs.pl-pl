---
title: Konstruktor wyrażeń przepływu danych mapowania Azure Data Factory
description: Konstruktor wyrażeń dla Azure Data Factory mapowania przepływów danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 872c7ce6a0c39ab19165a5f16ea3e4f6ef8bd6a5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388047"
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

## <a name="next-steps"></a>Następne kroki

[Rozpocznij tworzenie wyrażeń transformacji danych](data-flow-expression-functions.md)
