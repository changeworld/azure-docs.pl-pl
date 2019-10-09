---
title: Azure Data Factory przekształcenie podziału warunkowego przepływu danych
description: Przekształcenie podziału warunkowego przepływu danych Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: fec2b09b9dc471135d9cdd00ac2465728a47bdbf
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026922"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapowanie przekształcenia podziału warunkowego przepływu danych



(media/data-flow/conditionalsplit2.png "Przybornik") podziału warunkowego dla ![przybornika]

Przekształcenie podziału warunkowego może kierować wiersze danych do różnych strumieni w zależności od zawartości danych. Implementacja przekształcenia podziału warunkowego jest podobna do struktury decyzji CASE w języku programowania. Transformacja oblicza wyrażenia, a na podstawie wyników kieruje wiersz danych do określonego strumienia. Ta transformacja zapewnia również domyślne dane wyjściowe, tak więc jeśli wiersz nie pasuje do żadnego wyrażenia, jest on przekierowywany do domyślnych danych wyjściowych.

(media/data-flow/conditionalsplit1.png "Opcje podziału warunkowego") z podziałem ![warunkowym]

## <a name="multiple-paths"></a>Wiele ścieżek

Aby dodać dodatkowe warunki, wybierz pozycję "Dodaj strumień" w dolnym okienku konfiguracji, a następnie kliknij pole tekstowe Konstruktor wyrażeń, aby skompilować wyrażenie.

![podział warunkowy]z podziałem na wiele(media/data-flow/conditionalsplit3.png "warunkowe")

## <a name="next-steps"></a>Następne kroki

Typowe przekształcenia przepływu danych używane z podziałem warunkowym: [transformacja sprzężenia](data-flow-join.md), [transformacja wyszukiwania](data-flow-lookup.md), [Wybieranie przekształcenia](data-flow-select.md)
