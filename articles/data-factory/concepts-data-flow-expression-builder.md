---
title: Konstruktor wyrażeń przepływu danych usługi Azure Data Factory mapowania
description: Konstruktor wyrażeń dla usługi Azure Data Factory mapowanie danych przepływów
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: df9cfb0c0e36f54c8b1fbee4def552c78e9d42c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269166"
---
# <a name="mapping-data-flow-expression-builder"></a>Konstruktor wyrażeń przepływu danych mapowania

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

W usługi Azure Data Factory mapowania przepływ danych można znaleźć pola wyrażenia, w którym możesz wprowadzić wyrażeń do przekształcania danych. Użyj kolumn, pola, zmiennych, parametrów, funkcje z przepływu danych w tych polach. Aby utworzyć wyrażenie, użyj Kreatora wyrażeń, który jest uruchamiany, klikając pozycję w polu tekstowym wyrażenie wewnątrz przekształcenie. Czasami zostaną wyświetlone opcje "Kolumna obliczana", podczas wybierania kolumn dla transformacji. Po kliknięciu, widoczna będzie również uruchomić Konstruktora wyrażeń.

![Konstruktor wyrażeń](media/data-flow/expression.png "Konstruktor wyrażeń")

Konstruktor wyrażeń Domyślnie narzędzie używa opcji edytora tekstu. Funkcja autouzupełniania odczytuje z cały model obiektu przepływ danych fabryki danych Azure przy użyciu składni sprawdzanie i wyróżniania.

![Konstruktor wyrażeń Autouzupełnianie](media/data-flow/expb1.png "Konstruktor wyrażeń autouzupełniania")

## <a name="currently-working-on-field"></a>Aktualnie pracuje nad pola

![Konstruktor wyrażeń](media/data-flow/exp3.png "aktualnie pracuje nad")

W lewym górnym rogu interfejsu użytkownika Konstruktor wyrażeń, pojawi się pole o nazwie "Obecnie działa On" na nazwę pola, które są aktualnie pracuje. Wyrażenie, które kompilujesz w interfejsie użytkownika zostaną zastosowane tylko do obecnie pola pracy. Jeśli chcesz przekształcić kolejne pole, zapisać bieżącą pracę, a następnie użyj tej listy rozwijanej do wybierz inne pole, a następnie utworzyć wyrażenie dla innych pól.

## <a name="data-preview-in-debug-mode"></a>Podgląd danych w trybie debugowania

![Konstruktor wyrażeń](media/data-flow/exp4b.png "wyrażenie danych w wersji zapoznawczej")

Podczas pracy z wyrażenia można opcjonalnie przełączać tryb debugowania z powierzchni projektowej przepływ danych fabryki danych Azure, włączanie podglądu na żywo w toku wyników danych przy użyciu wyrażenia, który jest kompilowany. W czasie rzeczywistym debugowania na żywo jest włączona dla wyrażenia.

![Tryb debugowania](media/data-flow/debugbutton.png "przycisk debugowania")


![Konstruktor wyrażeń](media/data-flow/exp5.png "wyrażenie danych w wersji zapoznawczej")

## <a name="comments"></a>Komentarze

Dodawanie komentarzy do wyrażenia przy użyciu pojedynczego wiersza i komentarzy wielowierszowych składni:

![Komentarze](media/data-flow/comments.png "komentarze")

## <a name="regular-expressions"></a>Wyrażenia regularne

Język wyrażeń przepływ danych fabryki danych Azure [pełna dokumentacja tutaj](https://aka.ms/dataflowexpressions), włącza funkcje, które zawierają składni wyrażeń regularnych. Korzystając z funkcji wyrażenia regularnego, Konstruktor wyrażeń podejmie próbę interpretacji kreski ułamkowej odwróconej (\\) jako sekwencja znaków ucieczki. Korzystając z ukośników odwrotnych w wyrażeniu regularnym, umieść je całego wyrażenia regularnego w dziesięciomilionowych częściach sekundy (\`) lub podwójny ukośnik odwrotny.

Przykład przy użyciu znaczników

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

lub za pomocą podwójny ukośnik

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Adresowanie indeksy tablicy

Za pomocą funkcji wyrażenia, które zwracają tablice umożliwia adresu określonego indeksów wewnątrz tego obiektu w tablicy zwracanej nawiasy kwadratowe []. Tablica jest oparte na nich.

![Wyrażenie Konstruktor tablicy](media/data-flow/expb2.png "wyrażenie danych w wersji zapoznawczej")

## <a name="handling-names-with-special-characters"></a>Obsługa nazw ze znakami specjalnymi

W przypadku nazw kolumn, które zawierają znaków specjalnych ani spacji, należy ująć nazwę w nawiasach klamrowych.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Kolejne kroki

[Rozpocznij tworzenie wyrażenia przekształcania danych](data-flow-expression-functions.md)
