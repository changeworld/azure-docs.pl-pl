---
title: Zakładka Optymalizuj przepływ danych mapowania fabryki danych platformy Azure
description: Optymalizacja Azure danych fabryki mapowanie przepływu danych przy użyciu karty Optymalizowanie przy użyciu ustawień partycji
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 40023931b2a0b3788a583a5b5240e7916b187e34
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190640"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>Mapowanie danych przekształcania przepływ optymalizacji kartę

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Każde przekształcenie przepływ danych ma kartę "Optymalizuj". Karta Optymalizacja zawiera opcjonalne ustawienia, aby skonfigurować poszczególne schematy partycjonowania przepływy danych.

<img src="media/data-flow/opt001.png" width="800">

Ustawieniem domyślnym jest "przy użyciu bieżącego partycjonowania". Bieżący Partitioning powoduje, że usługi Azure Data Factory, aby użyć schematu partycjonowania natywne przepływu danych działający na platformie Spark w usłudze Azure Databricks. Ogólnie rzecz biorąc jest to zalecane podejście.

Istnieją jednak wystąpień, w których możesz chcieć dostosować podział. Na przykład chcąc służący do wypełniania wyjściowego przekształceń do pojedynczego pliku usługi Data lake, następnie wybrano "jednej partycji" na karcie Optymalizuj dla partycjonowania w transformacji ujścia.

Jest innym przypadek, gdzie możesz chcieć sprawowanie kontroli nad poszczególne schematy partycjonowania używanych na potrzeby przekształceń danych pod względem wydajności. Dostosowywanie, partycjonowanie danych zapewnia poziom kontroli nad rozkład danych między węzłami obliczeniowymi i optymalizacje miejscowość danych, które mogą mieć wpływ zarówno dodatnie jak i ujemne na wydajność przepływu danych ogólną.

Jeśli chcesz zmienić, partycjonowania na dowolnego przekształcenia, wystarczy kliknąć kartę optymalizacji i wybierz przycisk radiowy "Ustaw partycjonowania". Następnie zobaczysz za pomocą szeregu opcji partycjonowania. Najlepszą metodą partycji w celu zaimplementowania będą się różnić w zależności od ilości danych, klucze Release candidate, wartości null i kardynalności. Najlepszym rozwiązaniem jest rozpoczynać partycjonowanie domyślne, a następnie spróbuj różnych opcji partycjonowania. Można przetestować, za pomocą debugowania, uruchamiane w potoku, a następnie Wyświetl czas w każde przekształcenie grupowanie, a także użycia partycji z widoku monitorowania.

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>Działanie okrężne

Działanie okrężne jest proste partycji, który automatycznie rozprowadza dane równomiernie między partycjami. Użyj działanie okrężne gdy nie masz klucza nadaje do implementowania strategii partycjonowania stałej, inteligentne. Można ustawić liczbę partycji fizycznych.

### <a name="hash"></a>Skrót

Usługa Azure Data Factory powoduje wygenerowanie skrótów kolumn w celu utworzenia jednolitego partycji taki sposób, że wiersze z podobne wartości mieszczą się w tej samej partycji. Korzystając z opcji wyznaczania wartości skrótu, przetestować pochylenia możliwa partycji. Można ustawić liczbę partycji fizycznych.

### <a name="dynamic-range"></a>Zakres dynamiczny

Zakres dynamiczny użyje Spark zakresów dynamicznych na podstawie kolumny lub wyrażenia, które należy podać. Można ustawić liczbę partycji fizycznych. 

### <a name="fixed-range"></a>Środek zakresu

Należy utworzyć wyrażenie, które zapewnia szereg stałych wartości kolumny danych podzielonych na partycje. Należy dysponować dobrą znajomością danych przed użyciem tej opcji, aby uniknąć niesymetryczność partycji. Wartości, które można wprowadzić wyrażenie będzie służyć jako część funkcji partycji. Można ustawić liczbę partycji fizycznych.

### <a name="key"></a>Klucz

W przypadku dobrej znajomości sposobu Kardynalność dane klucza partycji może być strategii partycji. Partycjonowanie klucza spowoduje utworzenie partycji dla każdej unikatowej wartości w kolumnie. Nie można ustawić liczbę partycji, ponieważ liczba będzie zależeć od unikatowych wartości w danych.

## <a name="next-steps"></a>Kolejne kroki

[Przewodnik dotyczący wydajności przepływ danych mapowania](concepts-data-flow-performance.md)
[przepływ danych monitorowania](concepts-data-flow-monitoring.md)
