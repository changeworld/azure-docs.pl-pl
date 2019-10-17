---
title: Przekształcenie przepływu danych mapowania Azure Data Factory istnieje
description: Sprawdzanie istniejących wierszy przy użyciu funkcji mapowania usługi Fabryka danych z przekształceniem EXISTS
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6048a6d30d37b9d2b46c3105c5f8eac0a9ca41c0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387850"
---
# <a name="mapping-data-flow-exists-transformation"></a>Przekształcenie przepływu danych mapowania istnieje



Transformacja EXISTS to transformacja filtru wierszy, która powoduje zatrzymanie lub umożliwienie przepływania wierszy w danych. Transformacja istnieje podobna do ```SQL WHERE EXISTS``` i ```SQL WHERE NOT EXISTS```. Po przekształceniu EXISTS wiersze ze strumienia danych będą obejmować wszystkie wiersze, w których wartości kolumn ze źródła 1 istnieją w źródle 2 lub nie istnieją w źródle 2.

![Istnieją ustawienia](media/data-flow/exists.png "Istnieje 1")

Wybierz drugie źródło istnieje, aby przepływ danych mógł porównać wartości z strumienia 1 w strumieniu 2.

Wybierz kolumnę ze źródła 1 i ze źródła 2, których wartości mają być sprawdzane, czy nie istnieją.

## <a name="multiple-exists-conditions"></a>Istnieje wiele warunków

Obok każdego wiersza w warunkach kolumny istnieje znak + dostępny po umieszczeniu wskaźnika myszy nad wierszem REACH. Pozwoli to dodać wiele wierszy dla warunków istnienia. Każdy dodatkowy warunek jest "i".

## <a name="custom-expression"></a>Wyrażenie niestandardowe

![Istnieją ustawienia niestandardowe](media/data-flow/exists1.png "Istnieje niestandardowe")

Możesz kliknąć pozycję "wyrażenie niestandardowe", aby zamiast tego utworzyć wyrażenie o dowolnej postaci jako warunek istnieje lub nie istnieje. Zaznaczenie tego pola wyboru umożliwi wpisanie własnego wyrażenia jako warunku.

## <a name="next-steps"></a>Następne kroki

Podobne przekształcenia to [Wyszukiwanie](data-flow-lookup.md) i [sprzężenie](data-flow-join.md).
