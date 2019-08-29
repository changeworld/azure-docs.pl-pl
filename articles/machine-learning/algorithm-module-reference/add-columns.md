---
title: 'Dodaj kolumny: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak łączyć dwa zestawy danych przy użyciu modułu dodawania kolumn w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 630f8f7d4561ed6e7e9f943f4b3b123daeee4d67
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129022"
---
# <a name="add-columns-module"></a>Dodawanie modułu kolumn

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do łączenia dwóch zestawów danych. Wszystkie kolumny z dwóch zestawów danych, które można określić jako dane wejściowe, są łączone w celu utworzenia jednego elementu DataSet. Jeśli musisz połączyć więcej niż dwa zestawy danych, użyj kilku wystąpień **Dodaj kolumny**.



## <a name="how-to-configure-add-columns"></a>Jak skonfigurować Dodawanie kolumn
1. Dodaj moduł **Dodaj kolumny** do Twojego eksperymentu.

2. Połącz dwa zestawy danych, które chcesz połączyć. Jeśli chcesz połączyć więcej niż dwa zestawy danych, możesz utworzyć łańcuch ze sobą kilka kombinacji **dodawania kolumn**.

    - Istnieje możliwość połączenia dwóch kolumn, które mają różną liczbę wierszy. Wyjściowy zestaw danych jest uzupełniony o brakujące wartości dla każdego wiersza w mniejszej kolumnie źródłowej.

    - Nie można wybrać pojedynczych kolumn do dodania. Wszystkie kolumny z każdego zestawu danych są łączone podczas korzystania z **dodawania kolumn**. W związku z tym, jeśli chcesz dodać tylko podzestaw kolumn, użyj opcji wybierz kolumny w zestawie danych, aby utworzyć zestaw danych z kolumnami, które chcesz.

3. Uruchom eksperyment.

### <a name="results"></a>Wyniki
Po uruchomieniu eksperymentu:

- Aby wyświetlić pierwsze wiersze nowego zestawu danych, kliknij prawym przyciskiem myszy dane wyjściowe polecenia **Dodaj kolumny** i wybierz polecenie Wizualizuj.

Liczba kolumn w nowym zestawie danych jest równa sumie kolumn obu wejściowych zestawów danych.

Jeśli istnieją dwie kolumny o tej samej nazwie w wejściowych zestawach danych, do nazwy kolumny zostanie dodany sufiks liczbowy. Na przykład jeśli istnieją dwa wystąpienia kolumny o nazwie TargetOutcome, po lewej stronie zostanie zmieniona nazwa TargetOutcome_1, a prawa kolumna zostanie zmieniona na TargetOutcome_2.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 