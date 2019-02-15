---
title: Kolumny pochodnej przekształcania przepływu danych fabryki danych platformy Azure
description: Kolumny pochodnej przekształcania przepływu danych fabryki danych platformy Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272017"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Kolumny pochodnej przekształcania przepływu danych fabryki danych platformy Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformacji kolumny nie pochodzącej ze należy używać, aby wygenerować nowe kolumny w przepływie danych lub modyfikować istniejące pola.

![Utwórz kolumnę pochodną](media/data-flow/dc1.png "kolumny nie pochodzącej ze")

Można wykonywać wiele akcji kolumny nie pochodzącej ze w pojedyncze przekształcenie kolumny nie pochodzącej ze. Kliknij przycisk "Dodaj kolumnę", do przekształcania więcej niż 1 kolumny w kroku pojedyncze przekształcenie.

W polu kolumny wybierz istniejącą kolumnę w celu zastąpienia z nową wartością pochodnej lub kliknij przycisk "Utwórz nową kolumnę" Aby wygenerować nową kolumnę z nowo pochodnym.

Pole tekstowe wyrażenie otworzy Konstruktor wyrażeń, w którym tworzysz wyrażenie nie zawiera kolumn pochodnych, za pomocą funkcji wyrażenia.
