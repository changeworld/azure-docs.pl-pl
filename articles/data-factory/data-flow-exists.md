---
title: Azure Data Factory mapowanie danych przepływu istnieje transformacji
description: Jak sprawdzić, czy istniejące wiersze, używając mapowania fabryki danych, przepływy przy użyciu transformacji Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235957"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mapowanie przepływu danych istnieje transformacji

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie Exists jest wiersz przekształcenia, które zatrzymuje się lub umożliwia wierszy w dane przepływają przez filtrowanie. Przekształcanie istnieje jest podobny do ```SQL WHERE EXISTS``` i ```SQL WHERE NOT EXISTS```. Po przekształceniu istnieje wiersze wynikowe ze strumienia danych albo będzie zawierać wszystkie wiersze, w których wartości kolumny ze źródła 1 istnieje w źródle 2 lub nie istnieją w źródle 2.

![Istnieje ustawienia](media/data-flow/exists.png "istnieje 1")

Wybierz drugie źródło swoje Exists, aby przepływ danych można porównać wartości z zakresu od 1 Stream względem Stream 2.

Wybierz kolumnę, ze źródła 1 i 2 źródła wartości, którego chcesz wyszukać względem Exists, lub nie istnieje.

## <a name="multiple-exists-conditions"></a>Wiele istnieje warunków

Obok każdego wiersza w warunkach kolumny EXISTS znajdziesz + znak dostępnych po najechaniu kursorem na dotrzeć do wiersza. Pozwoli to dodać wiele wierszy dla warunków Exists. Każdy dodatkowy warunek jest "I".

## <a name="custom-expression"></a>Wyrażenie niestandardowe

![Ustawienia niestandardowe istnieje](media/data-flow/exists1.png "istnieje niestandardowy")

Możesz kliknąć pozycję "Niestandardowe wyrażenie", aby zamiast tego utworzyć wyrażenie dowolnej postaci jako usługi nie istnieje lub nie istnieje warunek. Zaznaczenie tego pola wyboru pozwoli na typ w wyrażeniu jako warunek.

## <a name="next-steps"></a>Kolejne kroki

Podobne przekształcenia są [wyszukiwania](data-flow-lookup.md) i [Dołącz](data-flow-join.md).
