---
title: Pochodne przekształcenie kolumny na mapowanie przepływu danych — usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przekształcać dane w dużej skali w usłudze Azure Data Factory przy użyciu transformacji mapowanie przepływu pochodnej kolumny danych.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312186"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Pochodne przekształcenie kolumny na mapowanie przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformacji kolumny nie pochodzącej ze należy używać, aby wygenerować nowe kolumny w przepływie danych lub modyfikować istniejące pola.

## <a name="derived-column-settings"></a>Pochodne ustawienia kolumny

Aby zastąpić istniejącą kolumnę, wybierz ją za pomocą listy rozwijanej kolumny. W przeciwnym razie użyj pól wyboru jako pole tekstowe i wpisz nazwę nowej kolumny. Aby skompilować wyrażenia kolumny pochodnej, kliknij w polu "Wprowadź wyrażenie", aby otworzyć [Konstruktor wyrażeń przepływu danych](concepts-data-flow-expression-builder.md).

![Ustawienia kolumn pochodnych](media/data-flow/dc1.png "pochodne ustawienia kolumny")

Aby dodać dodatkowych kolumn pochodnych, umieść kursor nad istniejące pochodnej kolumny i kliknij przycisk "+". Następnie wybierz pozycję "Dodaj kolumnę" lub "Dodaj kolumnę pattern". Wzorce kolumny może mają przydatne, jeśli Twojej nazwy kolumn są zmienne ze swoich źródeł. Aby uzyskać więcej informacji, zobacz [wzorców kolumny](concepts-data-flow-column-pattern.md).

![Nowy pochodne wybór kolumn](media/data-flow/columnpattern.png "New pochodne wybór kolumn")

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [mapowanie przepływu danych język wyrażeń](data-flow-expression-functions.md).
