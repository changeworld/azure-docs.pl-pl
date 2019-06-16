---
title: Mapowanie przepływu danych w usłudze Azure Data Factory pochodnej kolumny transformacji
description: Jak przekształcać dane w dużej skali przy użyciu Azure danych fabryki mapowanie przepływu pochodnej kolumny przekształcania danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917580"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Mapowanie przepływu danych pochodzi przekształcania kolumny

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformacji kolumny nie pochodzącej ze należy używać, aby wygenerować nowe kolumny w przepływie danych lub modyfikować istniejące pola.

![Utwórz kolumnę pochodną](media/data-flow/dc1.png "kolumny nie pochodzącej ze")

Można wykonywać wiele akcji kolumny nie pochodzącej ze w pojedyncze przekształcenie kolumny nie pochodzącej ze. Kliknij przycisk "Dodaj kolumnę", do przekształcania więcej niż 1 kolumny w kroku pojedyncze przekształcenie.

W polu kolumny wybierz istniejącą kolumnę w celu zastąpienia z nową wartością pochodnej lub kliknij przycisk "Utwórz nową kolumnę" Aby wygenerować nową kolumnę z nowo pochodnym.

Pole tekstowe wyrażenie otworzy Konstruktor wyrażeń, w którym tworzysz wyrażenie nie zawiera kolumn pochodnych, za pomocą funkcji wyrażenia.

## <a name="column-patterns"></a>Wzorce kolumn

Jeśli nazwy kolumny zmiennej ze swoich źródeł, możesz tworzyć przekształcenia wewnątrz kolumny nie pochodzącej ze przy użyciu wzorców kolumny zamiast o nazwie kolumny. Zobacz [dryfu schematu](concepts-data-flow-schema-drift.md) artykuł, aby uzyskać więcej informacji.

![wzorzec kolumny](media/data-flow/columnpattern.png "wzorców kolumny")

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [język wyrażeń usługi Data Factory dla przekształceń](https://aka.ms/dataflowexpressions) i [Konstruktor wyrażeń](concepts-data-flow-expression-builder.md)
