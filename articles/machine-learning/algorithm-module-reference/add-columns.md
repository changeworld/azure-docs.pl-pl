---
title: 'Dodaj kolumny: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Dodaj kolumny w usłudze Azure Machine Learning, aby połączyć dwa zestawy danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456782"
---
# <a name="add-columns-module"></a>Dodaj moduł Kolumny

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do łączenia dwóch zestawów danych. Łączysz wszystkie kolumny z dwóch zestawów danych, które można określić jako dane wejściowe, aby utworzyć pojedynczy zestaw danych. Jeśli chcesz połączyć więcej niż dwa zestawy danych, użyj kilku wystąpień **Dodaj kolumny**.



## <a name="how-to-configure-add-columns"></a>Jak skonfigurować dodawanie kolumn
1. Dodaj moduł **Dodaj kolumny** do potoku.

2. Połącz dwa zestawy danych, które chcesz połączyć. Jeśli chcesz połączyć więcej niż dwa zestawy danych, możesz połączyć ze sobą kilka kombinacji **Dodaj kolumny**.

    - Istnieje możliwość połączenia dwóch kolumn, które mają różną liczbę wierszy. Wyjściowy zestaw danych jest wypełniany brakującymi wartościami dla każdego wiersza w mniejszej kolumnie źródłowej.

    - Nie można wybrać poszczególnych kolumn do dodania. Wszystkie kolumny z każdego zestawu danych są łączone podczas korzystania z **funkcji Dodaj kolumny**. W związku z tym jeśli chcesz dodać tylko podzbiór kolumn, użyj wybierz kolumny w zestawie danych, aby utworzyć zestaw danych z kolumnami, które chcesz.

3. Prześlij potok.

### <a name="results"></a>Wyniki
Po uruchomieniu potoku:

- Aby wyświetlić pierwsze wiersze nowego zestawu danych, kliknij prawym przyciskiem myszy moduł **Dodaj kolumny** i wybierz polecenie Wizualizuj. Lub Wybierz moduł i przełącz się na kartę **Wyjścia** w prawym panelu, kliknij ikonę histogramu w **wyjściach portu,** aby zwizualizować wynik.

Liczba kolumn w nowym zestawie danych jest równa sumie kolumn obu wejściowych zestawów danych.

Jeśli w wejściowych zestawach danych znajdują się dwie kolumny o tej samej nazwie, do nazwy kolumny dodawany jest sufiks numeryczny. Na przykład jeśli istnieją dwa wystąpienia kolumny o nazwie TargetOutcome, lewa kolumna zostanie zmieniona TargetOutcome_1, a prawa kolumna zostanie zmieniona TargetOutcome_2.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 