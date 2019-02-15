---
title: Usługi Azure Data Factory mapowanie danych przepływu nowe przekształcenie gałęzi
description: Usługi Azure Data Factory mapowanie danych przepływu nowe przekształcenie gałęzi
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: c48c1500a31ce8f22bcef7207c0ea67caca24231
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272110"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Usługi Azure Data Factory mapowanie danych przepływu nowe przekształcenie gałęzi

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Gałąź opcje](media/data-flow/menu.png "menu")

Rozgałęzianie wykonać bieżącej strumień danych w przepływie danych i replikowane na inny strumień. Użyj nowej gałęzi do wykonywania wielu zestawów działań i przekształcenia względem tego samego strumienia danych.

Przykład: Przepływ danych zawiera źródło przekształcenia przy użyciu wybranego zestawu kolumn i konwersje typów danych. Następnie można złożyć kolumny pochodzi bezpośrednio po tego źródła. W kolumnie pochodne już utworzyć nowego pola, tej łączy w sobie imię i nazwisko, aby udostępnić pole "Pełna nazwa".

Można traktować strumieniu nowy zestaw przekształcenia, jak i ujście na jeden wiersz i użyj nowej gałęzi, aby utworzyć kopię tego strumienia, w którym można przekształcać tych samych danych z innym zestawem przekształcenia. Przekształcanie danych skopiowanych w oddzielnej gałęzi, możesz następnie ujścia danych, do innej lokalizacji.

> [!NOTE]
> "Nową gałąź" zostaną wyświetlone tylko jako akcję na + menu przekształcenia po kolejne przekształcenie, zgodnie z bieżącą lokalizację, w którym próbujesz się gałęzi. czyli nie będą widoczne z opcją "Nową gałąź" na końcu, w tym miejscu do momentu dodania innego przekształcania po wyboru

![Gałąź](media/data-flow/branch2.png "gałęzi 2")
