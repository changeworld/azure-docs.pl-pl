---
title: Opcje konwersji widoku widoku Usługi Azure Monitor do skoroszytów
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658714"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Opcje konwersji widoku widoku Usługi Azure Monitor do skoroszytów
[Projektant widoku](view-designer.md) jest funkcją usługi Azure Monitor, która umożliwia tworzenie widoków niestandardowych ułatwiające wizualizację danych w obszarze roboczym usługi Log Analytics za pomocą wykresów, list i osi czasu. Są one stopniowo wycofywane i zastępowane skoroszytami, które zapewniają dodatkowe funkcje. W tym artykule porównano podstawowe pojęcia między tymi dwoma i opcje konwertowania widoków na skoroszyty.

## <a name="basic-workbook-designs"></a>Podstawowe projekty skoroszytów

Projektant widoku ma stały statyczny styl reprezentacji, podczas gdy skoroszyty umożliwiają swobodę dołączania i modyfikowania sposobu przedstawiania danych. Poniższe obrazy przedstawiają dwa przykłady rozmieszczania skoroszytów podczas konwertowania widoków.

[Pionowy skoroszyt](view-designer-conversion-examples.md#vertical)
![w pionie](media/view-designer-conversion-options/view-designer-vertical.png)

[Tabbed workbook](view-designer-conversion-examples.md#tabbed)
![Karta Rozkład typu typu](media/view-designer-conversion-options/distribution-tab.png)
![typu z kartami z kartami Data(lista)](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Konwersja kafelków
Projektant widoku używa funkcji kafelka przeglądu do reprezentowania i podsumowywania ogólnego stanu. Są one reprezentowane w siedmiu kafelkach, od liczb do wykresów. W skoroszytach użytkownicy mogą tworzyć podobne wizualizacje i przypinać je tak, aby przypominały oryginalny styl kafelków przeglądowych. 

![Galeria](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Wyświetlanie konwersji na pulpicie nawigacyjnym
Zobacz kafelki projektanta zazwyczaj składają się z dwóch sekcji, wizualizacji i listy, która pasuje do danych z wizualizacji, na przykład kafelka **Donut & List.**

![Pierścień](media/view-designer-conversion-options/donut-example.png)

W przypadku skoroszytów zezwalamy użytkownikowi na wykonywanie kwerend w jednej lub obu sekcjach widoku. Formułowanie zapytań w skoroszytach jest prostym procesem dwuetapowym. Po pierwsze dane są generowane z kwerendy, a po drugie, dane są renderowane jako wizualizacja.  Przykład sposobu odtworzenia tego widoku w skoroszytach jest następujący:

![Convert](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Następne kroki
- [Uzyskiwanie dostępu do skoroszytów & uprawnień](view-designer-conversion-access.md)