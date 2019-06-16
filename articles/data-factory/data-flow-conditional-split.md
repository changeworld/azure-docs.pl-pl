---
title: Mapowanie transformacji warunkowego dzielenia przepływu danych w usłudze Azure Data Factory
description: Usługi Azure Data Factory danych przepływ warunkowego dzielenia transformacji
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795628"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapowanie przepływu danych warunkowego podziału transformacji

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![warunkowe podziału przybornika](media/data-flow/conditionalsplit2.png "warunkowe podziału przybornika")

Przekształcenie warunkowego dzielenia może kierować wiersze danych do różnych strumieni w zależności od zawartości danych. Implementację transformacji warunkowego dzielenia jest podobne do struktury decyzji wielkości liter w języku programowania. Przekształcenie daje w wyniku wyrażenia i na podstawie wyników, określa, że wiersz danych do określonego strumienia. Ta transformacja udostępnia również domyślne dane wyjściowe, tak, że jeśli wiersz odpowiada wyrażeniu nie zostaje skierowany do domyślne dane wyjściowe.

![Podziel warunkowego](media/data-flow/conditionalsplit1.png "opcji podziału warunkowe")

## <a name="multiple-paths"></a>Wiele ścieżek

Aby dodać dodatkowe warunki, wybierz pozycję "Dodaj Stream" w dolnym okienku konfiguracji, a następnie kliknij przycisk w polu tekstowym Konstruktor wyrażeń do kompilacji w wyrażeniu.

![warunkowe podziału multi](media/data-flow/conditionalsplit3.png "podzielić na wiele warunkowych")

## <a name="next-steps"></a>Kolejne kroki

Używane z warunkowego dzielenia wspólnego przekształcenia przepływu danych: [Dołącz do przekształcania](data-flow-join.md), [przekształcania Loopup](data-flow-lookup.md), [wybierz transformacji](data-flow-select.md)
