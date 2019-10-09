---
title: Transformacja kolumn pochodnych w mapowaniu przepływu danych — Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak przekształcać dane w skali w Azure Data Factory przy użyciu transformacji kolumn pochodnych przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026852"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformacja kolumn pochodnych w mapowaniu przepływu danych

Użyj przekształcenia kolumn pochodnych, aby wygenerować nowe kolumny w przepływie danych lub zmodyfikować istniejące pola.

## <a name="derived-column-settings"></a>Ustawienia kolumn pochodnych

Aby zastąpić istniejącą kolumnę, wybierz ją za pomocą listy rozwijanej kolumny. W przeciwnym razie użyj pola wybór kolumny jako pola tekstowego i wpisz nazwę nowej kolumny. Aby utworzyć wyrażenie kolumny pochodnej, kliknij pole "wprowadź wyrażenie", aby otworzyć [konstruktora wyrażeń przepływu danych](concepts-data-flow-expression-builder.md).

Ustawienia kolumn ![pochodnych]dla ustawień(media/data-flow/dc1.png "kolumny pochodnej")

Aby dodać dodatkowe kolumny pochodne, umieść kursor na istniejącej kolumnie pochodnej i kliknij przycisk "+". Następnie wybierz pozycję "Dodaj kolumnę" lub "Dodaj wzorzec kolumny". Wzorce kolumn mogą być przydatne, jeśli nazwy kolumn są zmienne ze źródeł. Aby uzyskać więcej informacji, zobacz [wzorce kolumn](concepts-data-flow-column-pattern.md).

![Nowy wybór kolumn pochodnych]—(media/data-flow/columnpattern.png "wybór nowej kolumny pochodnej")

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku wyrażeń przepływu danych mapowania](data-flow-expression-functions.md).
