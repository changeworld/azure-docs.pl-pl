---
title: Transformację kolumn pochodnych w Azure Data Factory mapowaniu przepływu danych | Microsoft Docs
description: Dowiedz się, jak przekształcać dane w skali w Azure Data Factory przy użyciu transformacji kolumn pochodnych przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 60451fa6152590ed0fde51be436c867f39906acf
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514816"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformacja kolumn pochodnych w mapowaniu przepływu danych

Użyj przekształcenia kolumn pochodnych, aby wygenerować nowe kolumny w przepływie danych lub zmodyfikować istniejące pola.

## <a name="derived-column-settings"></a>Ustawienia kolumn pochodnych

Aby zastąpić istniejącą kolumnę, wybierz ją za pomocą listy rozwijanej kolumny. W przeciwnym razie użyj pola wybór kolumny jako pola tekstowego i wpisz nazwę nowej kolumny. Aby utworzyć wyrażenie kolumny pochodnej, kliknij pole "wprowadź wyrażenie", aby otworzyć [konstruktora wyrażeń przepływu danych](concepts-data-flow-expression-builder.md).

![Ustawienia kolumn pochodnych](media/data-flow/dc1.png "Ustawienia kolumn pochodnych")

Aby dodać dodatkowe kolumny pochodne, umieść kursor na istniejącej kolumnie pochodnej i kliknij ikonę znaku plus. Wybierz opcję **Dodaj kolumnę** lub **Dodaj wzorzec kolumny**. Wzorce kolumn mogą być przydatne, jeśli nazwy kolumn są zmienne ze źródeł. Aby uzyskać więcej informacji, zobacz [wzorce kolumn](concepts-data-flow-column-pattern.md).

![Nowy wybór kolumny pochodnej](media/data-flow/columnpattern.png "Nowy wybór kolumny pochodnej")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład jest kolumną pochodną o nazwie `CleanData`, która przyjmuje strumień przychodzący `MoviesYear` i tworzy dwie kolumny pochodne. Pierwsza kolumna pochodna zastępuje kolumnę `Rating` wartością klasyfikacji jako typ liczby całkowitej. Druga kolumna pochodna to wzorzec pasujący do każdej kolumny, której nazwa rozpoczyna się od "filmów". Dla każdej dopasowanej kolumny tworzy kolumnę `movie`, która jest równa wartości dopasowanej kolumny poprzedzonej prefiksem "movie_". 

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Utwórz przykład](media/data-flow/derive-script1.png "Utwórz przykład")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku wyrażeń przepływu danych mapowania](data-flow-expression-functions.md).
