---
title: Konstruktor wyrażeń w mapowaniu przepływu danych
description: Tworzenie wyrażeń przy użyciu Konstruktora wyrażeń w mapowaniu przepływów danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969385"
---
# <a name="building-expressions-in-mapping-data-flow"></a>Kompilowanie wyrażeń w przepływie danych mapowania

W mapowaniu przepływu danych wiele właściwości transformacji jest wprowadzanych jako wyrażenia. Te wyrażenia składają się z wartości kolumn, parametrów, funkcji, operatorów i literałów, które są obliczane do typu danych Spark w czasie wykonywania.

## <a name="opening-the-expression-builder"></a>Otwieranie konstruktora wyrażeń

Interfejs edytowania wyrażeń w środowisku fabryki danych jest znany jako **Konstruktor wyrażeń**. Podczas wprowadzania w logice wyrażenia Fabryka danych używa uzupełniania kodu [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) do wyróżniania, sprawdzania składni i automatycznego uzupełniania.

![Konstruktor wyrażeń](media/data-flow/xpb1.png "Konstruktor wyrażeń")

W transformacjach, takich jak kolumna pochodna i filtr, gdzie wyrażenia są obowiązkowe, Otwórz konstruktora wyrażeń, klikając niebieskie pole wyrażenia.

![Konstruktor wyrażeń](media/data-flow/expressionbox.png "Konstruktor wyrażeń")

W przypadku odwoływania się do kolumn w odpowiednim warunku lub w klauzuli Group by wyrażenie może wyodrębnić wartości z kolumn. Aby utworzyć wyrażenie, wybierz opcję "kolumna obliczana".

![Konstruktor wyrażeń](media/data-flow/computedcolumn.png "Konstruktor wyrażeń")

W przypadkach, gdy wyrażenie lub wartość literału są prawidłowymi danymi wejściowymi, polecenie "Dodaj zawartość dynamiczną" umożliwi skompilowanie wyrażenia, którego wynikiem jest literał.

![Konstruktor wyrażeń](media/data-flow/add-dynamic-content.png "Konstruktor wyrażeń")

## <a name="expression-language-reference"></a>Dokumentacja języka wyrażeń

Mapowanie przepływów danych ma wbudowaną funkcję i operatory, które mogą być używane w wyrażeniach. Listę dostępnych funkcji można znaleźć na stronie Dokumentacja [języka wyrażenia przepływu danych mapowania](data-flow-expression-functions.md) .

## <a name="column-names-with-special-characters"></a>Nazwy kolumn zawierające znaki specjalne

Jeśli masz nazwy kolumn, które zawierają znaki specjalne lub spacje, umieść je w nawiasach klamrowych, aby odwoływać się do nich w wyrażeniu.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>Wyświetlanie podglądu wyników wyrażeń

Jeśli [tryb debugowania](concepts-data-flow-debug-mode.md) jest włączony, można użyć klastra Spark na żywo, aby zobaczyć w toku podgląd tego, co daje wyrażenie. Podczas kompilowania logiki można debugować wyrażenie w czasie rzeczywistym. 

![Konstruktor wyrażeń](media/data-flow/exp4b.png "Podgląd danych wyrażeń")

Kliknij przycisk Odśwież, aby zaktualizować wyniki wyrażenia do próbki na żywo źródłowej.

![Konstruktor wyrażeń](media/data-flow/exp5.png "Podgląd danych wyrażeń")

## <a name="string-interpolation"></a>Interpolacja ciągów

Używaj cudzysłowów podwójnych, aby ująć tekst ciągu literału razem z wyrażeniami. Można uwzględnić funkcje wyrażenia, kolumny i parametry. Interpolacja ciągów jest przydatna, aby uniknąć rozległego użycia łączenia ciągów podczas dołączania parametrów w ciągach zapytań. Aby użyć składni wyrażenia, należy ująć ją w nawiasy klamrowe,

Przykłady interpolacji ciągów:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>Komentowanie wyrażeń

Dodaj komentarze do wyrażeń przy użyciu jednowierszowej i wielowierszowej składni komentarza:

![Komentarze](media/data-flow/comments.png "Komentarze")

Poniżej znajdują się przykłady prawidłowych komentarzy:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Jeśli umieścisz komentarz w górnej części wyrażenia, pojawi się w polu tekstowym przekształcenia, aby udokumentować wyrażenia przekształcenia:

![Komentarze](media/data-flow/comments2.png "Komentarze")

## <a name="regular-expressions"></a>Wyrażenia regularne

Wiele funkcji języka wyrażeń używa składni wyrażeń regularnych. W przypadku korzystania z funkcji wyrażenia regularnego, Konstruktor wyrażeń będzie próbować interpretować ukośnik odwrotny (\\) jako sekwencję znaków ucieczki. W przypadku używania ukośników odwrotnych w wyrażeniu regularnym należy ująć całe wyrażenie regularne w Takty (\`) lub użyć podwójnego ukośnika odwrotnego.

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

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

* ```Ctrl-K Ctrl-C```: Komentarze cały wiersz
* ```Ctrl-K Ctrl-U```: usuwanie komentarza
* ```F1```: udostępnianie poleceń pomocy edytora
* ```Alt-Down Arrow```: Przenieś w dół bieżący wiersz
* ```Alt-Up Arrow```: Przenieś w górę bieżący wiersz
* ```Cntrl-Space```: Pokaż Pomoc kontekstową

## <a name="convert-to-dates-or-timestamps"></a>Konwertuj na daty lub sygnatury czasowe

Aby uwzględnić literały ciągu w danych wyjściowych znacznika czasu, musisz otoczyć konwersję w ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Aby skonwertować milisekundy z epoki do daty lub sygnatury czasowej, użyj `toTimestamp(<number of milliseconds>)`. Jeśli czas przekroczy sekundy, pomnóż przez 1000.

```toTimestamp(1574127407*1000l)```

Końcowe "l" na końcu wyrażenia powyżej oznacza konwersję na długi typ jako składnię w wierszu.

## <a name="next-steps"></a>Następne kroki

[Rozpocznij tworzenie wyrażeń transformacji danych](data-flow-expression-functions.md)
