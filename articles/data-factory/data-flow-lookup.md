---
title: Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738389"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Mapowanie transformacji odnośnika przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj wyszukiwania, aby dodać dane referencyjne z innego źródła, aby przepływ danych. Przekształcenie wyszukiwania wymaga określonego źródła, wskazuje odwołanie do tabeli, która odpowiada na pola klucza.

![Przekształcanie wyszukiwania](media/data-flow/lookup1.png "wyszukiwania")

Zaznacz pola klucza, które mają być zgodne na przychodzące pola strumienia i pola ze źródła odniesienia. Należy najpierw utworzono nowe źródło na kanwę projektu przepływu danych, aby użyć po prawej stronie do wyszukiwania.

W przypadku znalezienia dopasowania wynikowy wiersze i kolumny źródłowej odwołania zostaną dodane do przepływu danych. Możesz wybrać pola, które interesujące, który chcesz uwzględnić w obiektu Sink na końcu przepływu danych.
