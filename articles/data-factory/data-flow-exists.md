---
title: Mapowanie przepływu danych w usłudze Azure Data Factory istnieje transformacji
description: Mapowanie przepływu danych w usłudze Azure Data Factory istnieje transformacji
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006142"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Mapowanie przepływu danych w usłudze Azure Data Factory istnieje transformacji

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie Exists jest wiersz przekształcenia, które zatrzymuje się lub umożliwia wierszy w dane przepływają przez filtrowanie. Przekształcanie istnieje jest podobny do ```SQL WHERE EXISTS``` i ```SQL WHERE NOT EXISTS```. Po przekształceniu filtru, wiersze wynikowe ze strumienia danych albo będzie zawierać wszystkie wiersze, w których wartości kolumny ze źródła 1 istnieje w źródle 2 lub nie istnieją w źródle 2.

![Istnieje ustawienia](media/data-flow/exsits.png "istnieje 1")

Wybierz drugie źródło swoje Exists, aby przepływ danych można porównać wartości z zakresu od 1 Stream względem Stream 2.

Wybierz kolumnę, ze źródła 1 i 2 źródła wartości, którego chcesz wyszukać względem Exists, lub nie istnieje.

## <a name="multiple-exists-conditions"></a>Wiele istnieje warunków

Obok każdego wiersza w warunkach kolumny dla Exsits znajdziesz + znak dostępnych po najechaniu kursorem na dotrzeć do wiersza. Pozwoli to dodać wiele wierszy dla warunków Exists.

## <a name="next-steps"></a>Kolejne kroki

