---
title: Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 28c8f2b0005641934f7fcd9549f1cf004b206d80
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271996"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj wyszukiwania, aby dodać dane referencyjne z innego źródła, aby przepływ danych. Przekształcenie wyszukiwania wymaga określonego źródła, wskazuje odwołanie do tabeli, która odpowiada na pola klucza.

![Przekształcanie wyszukiwania](media/data-flow/lookup1.png "wyszukiwania")

Zaznacz pola klucza, które mają być zgodne na przychodzące pola strumienia i pola ze źródła odniesienia. Należy najpierw utworzono nowe źródło na kanwę projektu przepływu danych, aby użyć po prawej stronie do wyszukiwania.

W przypadku znalezienia dopasowania wynikowy wiersze i kolumny źródłowej odwołania zostaną dodane do przepływu danych. Możesz wybrać pola, które interesujące, który chcesz uwzględnić w obiektu Sink na końcu przepływu danych.
