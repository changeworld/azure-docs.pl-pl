---
title: 'Dodaj kolumny: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu Dodawanie kolumn w usłudze Azure Machine Learning do łączenia dwóch zestawów danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029358"
---
# <a name="add-columns-module"></a>Dodaj moduł kolumn

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do łączenia dwóch zestawów danych. Możesz połączyć wszystkie kolumny z dwóch zestawów danych, które określisz jako dane wejściowe, aby utworzyć jeden zestaw danych. Jeśli zachodzi potrzeba łączenia więcej niż dwóch zestawów danych, użyj kilka wystąpień **Dodaj kolumny**.



## <a name="how-to-configure-add-columns"></a>Jak skonfigurować Dodawanie kolumn
1. Dodaj **Dodaj kolumny** modułu do eksperymentu.

2. Łączenie dwóch zestawów danych, które chcesz połączyć. Jeśli chcesz połączyć więcej niż dwóch zestawów danych, można połączyć w łańcuch ze sobą kilka kombinacji **Dodaj kolumny**.

    - Istnieje możliwość połączyć dwie kolumny, które mają różną liczbę wierszy. Wyjściowy zestaw danych jest dopełniana brakuje wartości dla każdego wiersza w mniejszych kolumny źródłowej.

    - Nie można wybrać poszczególnych kolumn do dodania. Wszystkie kolumny z każdego zestawu danych są łączone, gdy używasz **Dodaj kolumny**. W związku z tym jeśli chcesz dodać tylko podzbiór kolumn, umożliwia Wybieranie kolumn w zestawie danych Tworzenie zestawu danych z kolumnami, które chcesz.

3. Uruchom eksperyment.

### <a name="results"></a>Wyniki
Po uruchomieniu eksperymentu:

- Aby wyświetlić pierwsze wiersze nowy zestaw danych, kliknij prawym przyciskiem myszy dane wyjściowe **Dodaj kolumny** i wybierz pozycję Visualize.

Liczba kolumn w nowy zestaw danych jest równa sumy kolumny zarówno wejściowych zestawów danych.

Jeśli istnieją dwie kolumny z tej samej nazwie w danych wejściowych zestawów danych, sufiksu liczbowego jest dodawany do nazwy kolumny. Na przykład w przypadku dwóch wystąpień z kolumną o nazwie TargetOutcome lewa kolumna będzie można zmienić nazwy TargetOutcome_1 i prawa kolumna będzie zmieniona TargetOutcome_2.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 