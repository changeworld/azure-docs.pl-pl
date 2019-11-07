---
title: Przekształcenie filtru w przepływie danych mapowania Azure Data Factory
description: Filtrowanie wierszy przy użyciu transformacji filtru w przepływie danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 77587d007d9bfcdc1461201f676c79880bd89d6b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676173"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Przekształcenie filtru w strumieniu danych mapowania

Przekształcenia filtru umożliwiają filtrowanie wierszy na podstawie warunku. Strumień wyjściowy zawiera wszystkie wiersze, które pasują do warunku filtrowania. Transformacja filtru jest podobna do klauzuli WHERE w SQL.

## <a name="configuration"></a>Konfiguracja

Użyj konstruktora wyrażeń przepływu danych, aby wprowadzić wyrażenie dla warunku filtru. Aby otworzyć Konstruktora wyrażeń, kliknij niebieską ramkę. Warunek filtru musi być typu Boolean. Aby uzyskać więcej informacji na temat tworzenia wyrażenia, zobacz dokumentację programu [Expression Builder](concepts-data-flow-expression-builder.md) .

![Przekształcenie filtru](media/data-flow/filter1.png "Przekształcenie filtru")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład to transformacja podziału warunkowego o nazwie `FilterBefore1960`, która przyjmuje `CleanData`strumienia przychodzącego. Warunek filtru jest `year <= 1960`wyrażenia.

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Przekształcenie filtru](media/data-flow/filter1.png "Przekształcenie filtru")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Następne kroki

Odfiltruj kolumny z użyciem [przekształcenia SELECT](data-flow-select.md)
