---
title: Mapowanie transformacji klucza zastępczy przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji klucza zastępczy przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 62e9879f6bc75f042669ecb931ca7459d1317cc7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272089"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Mapowanie transformacji klucza zastępczy przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Użyj przekształcania klucz zastępczy, aby dodać zwiększającej się wartości inne niż firmy dowolnego wartość klucza do Twojego zestawu wierszy przepływu danych. Jest to przydatne podczas projektowania tabel wymiarów w modelu danych analitycznych schemat gwiazdy, gdzie każdy element członkowski w tabelach wymiarów musi mieć unikatowy klucz, który jest częścią klucza niebiznesowe metodologii Kimballa DW.

![Zastępczych przekształcenia klucza](media/data-flow/surrogate.png "zastępczych Przekształcenie klucza")

"Kolumna klucza" jest nazwę, która zostanie nadana do nowej kolumny klucza zastępczy.

"Wartość początkowa" to punkt początkowy wartość przyrostową.
