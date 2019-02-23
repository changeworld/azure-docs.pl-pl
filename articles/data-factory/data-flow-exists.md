---
title: Mapowanie przepływu danych w usłudze Azure Data Factory istnieje transformacji
description: Mapowanie przepływu danych w usłudze Azure Data Factory istnieje transformacji
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734493"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Mapowanie przepływu danych w usłudze Azure Data Factory istnieje transformacji

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie Exists jest wiersz przekształcenia, które zatrzymuje się lub umożliwia wierszy w dane przepływają przez filtrowanie. Przekształcanie istnieje jest podobny do ```SQL WHERE EXISTS``` i ```SQL WHERE NOT EXISTS```. Po przekształceniu filtru, wiersze wynikowe ze strumienia danych albo będzie zawierać wszystkie wiersze, w których wartości kolumny ze źródła 1 istnieje w źródle 2 lub nie istnieją w źródle 2.

![Istnieje ustawienia](media/data-flow/exsits.png "istnieje 1")

Wybierz drugie źródło swoje Exists, aby przepływ danych można porównać wartości z zakresu od 1 Stream względem Stream 2.

Wybierz kolumnę, ze źródła 1 i 2 źródła wartości, którego chcesz wyszukać względem Exists, lub nie istnieje.
