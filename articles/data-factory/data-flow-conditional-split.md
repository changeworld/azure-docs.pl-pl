---
title: Azure Data Factory przekształcenie podziału warunkowego przepływu danych
description: Przekształcenie podziału warunkowego przepływu danych Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd51cc2d5f95598154b76b5da8e3fc9e4801100d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104949"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapowanie przekształcenia podziału warunkowego przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Przybornik podziału warunkowego](media/data-flow/conditionalsplit2.png "Przybornik podziału warunkowego")

Przekształcenie podziału warunkowego może kierować wiersze danych do różnych strumieni w zależności od zawartości danych. Implementacja przekształcenia podziału warunkowego jest podobna do struktury decyzji CASE w języku programowania. Transformacja oblicza wyrażenia, a na podstawie wyników kieruje wiersz danych do określonego strumienia. Ta transformacja zapewnia również domyślne dane wyjściowe, tak więc jeśli wiersz nie pasuje do żadnego wyrażenia, jest on przekierowywany do domyślnych danych wyjściowych.

![podział warunkowy](media/data-flow/conditionalsplit1.png "Opcje podziału warunkowego")

## <a name="multiple-paths"></a>Wiele ścieżek

Aby dodać dodatkowe warunki, wybierz pozycję "Dodaj strumień" w dolnym okienku konfiguracji, a następnie kliknij pole tekstowe Konstruktor wyrażeń, aby skompilować wyrażenie.

![podział warunkowy — wiele](media/data-flow/conditionalsplit3.png "podział warunkowy — wiele")

## <a name="next-steps"></a>Następne kroki

Typowe przekształcenia przepływu danych używane z podziałem warunkowym: [Przekształcanie sprzężenia](data-flow-join.md), [transformacja wyszukiwania](data-flow-lookup.md), [Wybieranie przekształcenia](data-flow-select.md)
