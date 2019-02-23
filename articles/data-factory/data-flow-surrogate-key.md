---
title: Mapowanie transformacji klucza zastępczy przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji klucza zastępczy przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6243905857f0450168541f556636d90bb4d855f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734952"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Mapowanie transformacji klucza zastępczy przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj przekształcania klucz zastępczy, aby dodać zwiększającej się wartości inne niż firmy dowolnego wartość klucza do Twojego zestawu wierszy przepływu danych. Jest to przydatne podczas projektowania tabel wymiarów w modelu danych analitycznych schemat gwiazdy, gdzie każdy element członkowski w tabelach wymiarów musi mieć unikatowy klucz, który jest częścią klucza niebiznesowe metodologii Kimballa DW.

![Zastępczych przekształcenia klucza](media/data-flow/surrogate.png "zastępczych Przekształcenie klucza")

"Kolumna klucza" jest nazwę, która zostanie nadana do nowej kolumny klucza zastępczy.

"Wartość początkowa" to punkt początkowy wartość przyrostową.
