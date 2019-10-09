---
title: Konstruktor wyrażeń przepływu danych mapowania Azure Data Factory
description: Konstruktor wyrażeń dla Azure Data Factory mapowania przepływów danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9862866d5cddb227d9417ac15db6b8ea851507e6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030296"
---
# <a name="mapping-data-flow-expression-builder"></a>Konstruktor wyrażeń przepływu danych mapowania



W obszarze Azure Data Factory mapowanie przepływu danych znajdziesz pola wyrażeń, w których można wprowadzać wyrażenia do przekształcania danych. W tych polach używaj kolumn, pól, zmiennych, parametrów, funkcji w przepływie danych. Aby skompilować wyrażenie, użyj konstruktora wyrażeń, który jest uruchamiany przez kliknięcie w polu tekstowym wyrażenia wewnątrz transformacji. W przypadku wybrania kolumn do przekształcenia czasami widoczne są opcje "kolumna obliczana". Po kliknięciu tego przycisku zobaczysz również uruchomiony program Expression Builder.

(media/data-flow/xpb1.png "Konstruktor wyrażeń") ![konstruktora wyrażeń]

Narzędzie Expression Builder domyślnie ustawia opcję Edytor tekstu. Funkcja autouzupełniania odczytuje z całego modelu obiektów przepływu danych Azure Data Factory ze sprawdzaniem składni i wyróżnieniem.

(media/data-flow/expb1.png "Autouzupełnianie wyrażeń") ![autouzupełniania konstruktora wyrażeń]

## <a name="build-schemas-in-output-schema-pane"></a>Kompiluj schematy w okienku schematu danych wyjściowych

![Dodaj kolumny złożonej kolumny](media/data-flow/complexcolumn.png "")

W okienku schematu danych wyjściowych po lewej stronie zostaną wyświetlone kolumny, które są modyfikowane i dodawane do schematu. W tym miejscu możesz interaktywnie kompilować proste i złożone struktury danych. Dodaj dodatkowe pola przy użyciu opcji "Dodaj kolumnę" i Utwórz hierarchie przy użyciu polecenia "Dodaj podkolumnę".

Dodaj podkolumnę(media/data-flow/addsubcolumn.png "Dodawanie") ![podkolumny]

## <a name="data-preview-in-debug-mode"></a>Podgląd danych w trybie debugowania

(media/data-flow/exp4b.png "Podgląd danych wyrażeń") ![konstruktora wyrażeń]

Gdy Pracujesz w wyrażeniach przepływu danych, Przełącz tryb debugowania z powierzchni projektowej przepływu danych Azure Data Factory, włączając w to aktualne Podgląd wyników danych z utworzonego wyrażenia. Debugowanie na żywo w czasie rzeczywistym jest włączone dla wyrażeń.

(media/data-flow/debugbutton.png "Przycisk") debugowania ![trybu debugowania]

Kliknij przycisk Odśwież, aby zaktualizować wyniki wyrażenia na żywo próbki źródła w czasie rzeczywistym.

(media/data-flow/exp5.png "Podgląd danych wyrażeń") ![konstruktora wyrażeń]

## <a name="comments"></a>Komentarze

Dodaj komentarze do wyrażeń przy użyciu jednowierszowej i wielowierszowej składni komentarza:

![](media/data-flow/comments.png "Komentarze") komentarzy

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

(media/data-flow/expb2.png "Podgląd danych wyrażenia") ![tablicy konstruktora wyrażeń]

## <a name="handling-names-with-special-characters"></a>Obsługa nazw ze znakami specjalnymi

Jeśli masz nazwy kolumn, które zawierają znaki specjalne lub spacje, umieść ją w nawiasach klamrowych.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Następne kroki

[Rozpocznij tworzenie wyrażeń transformacji danych](data-flow-expression-functions.md)
