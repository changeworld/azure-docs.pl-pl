---
title: Mapowanie przekształcenia okna przepływu danych
description: Przekształcenie okna przepływu danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: fa34def67d91332a00bf0ee92b365957a47f9616
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931478"
---
# <a name="azure-data-factory-window-transformation"></a>Przekształcenie okna Azure Data Factory



Przekształcenie okna polega na określeniu agregacji kolumn w strumieniach danych. W Konstruktorze wyrażeń można definiować różne typy agregacji oparte na danych lub czasie w systemie Windows (klauzula SQL OVER), takich jak ołów, LAG, NTILE, CUMEDIST, RANGa itp.). W danych wyjściowych zostaną wygenerowane nowe pole zawierające te agregacje. Można również dołączyć opcjonalne pola grupowe.

![Opcje okna](media/data-flow/windows1.png "System Windows 1")

## <a name="over"></a>Ponad
Ustaw Partycjonowanie danych kolumn dla transformacji okna. Odpowiednikiem SQL jest ```Partition By``` w klauzuli over w SQL. Jeśli chcesz utworzyć obliczenia lub utworzyć wyrażenie, które ma być używane na potrzeby partycjonowania, możesz to zrobić, umieszczając kursor na nazwie kolumny i wybierając pozycję "kolumna obliczana".

![Opcje okna](media/data-flow/windows4.png "System Windows 4")

## <a name="sort"></a>Sortuj
Inną częścią klauzuli over jest ustawienie ```Order By```. Spowoduje to ustawienie kolejności sortowania danych. Można również utworzyć wyrażenie dla wartości obliczanej w tym polu kolumny w celu sortowania.

![Opcje okna](media/data-flow/windows5.png "System Windows 5")

## <a name="range-by"></a>Zakres według
Następnie ustaw obramowanie okna jako niepowiązane lub ograniczone. Aby ustawić nieograniczone obramowanie okna, ustaw dla suwaka wartość Unbound na obu końcach. W przypadku wybrania ustawienia między niezwiązanym i bieżącym wierszem należy ustawić przesunięcie wartości początkowej i końcowej. Obie wartości będą dodatnimi liczbami całkowitymi. Możesz użyć względnych liczb lub wartości z danych.

Suwak okna ma dwie wartości do ustawienia: wartości przed bieżącym wiersz i wartości po bieżącym wierszu. Przesunięcie początkowe i końcowe dopasowuje się do dwóch selektorów na suwaku.

![Opcje okna](media/data-flow/windows6.png "System Windows 6")

## <a name="window-columns"></a>Kolumny okna
Na koniec użyj konstruktora wyrażeń do definiowania agregacji, które mają być używane z oknami danych, takimi jak RANGa, liczba, MIN, MAX, GĘSTa RANGa, ołów, ZWŁOKa itd.

![Opcje okna](media/data-flow/windows7.png "System Windows 7")

Pełna lista funkcji agregujących i analitycznych dostępnych do użycia w języku wyrażeń przepływu danych ADF za pośrednictwem konstruktora wyrażeń jest wymieniona tutaj: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Następne kroki

Jeśli szukasz prostej agregacji grupowej, użyj [przekształcenia agregacji](data-flow-aggregate.md)
