---
title: Działanie walidacji w Azure Data Factory
description: Działanie sprawdzania poprawności nie kontynuuje wykonywania potoku, dopóki nie zostanie zweryfikowany dołączony zestaw danych z określonymi kryteriami, które użytkownik określi.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678369"
---
# <a name="validation-activity-in-azure-data-factory"></a>Działanie walidacji w Azure Data Factory
Można użyć walidacji w potoku, aby upewnić się, że potok będzie nadal wykonywany po zweryfikowaniu, że istnieje odwołanie do dołączonego zestawu danych, że spełnia określone kryteria lub został osiągnięty limit czasu.


## <a name="syntax"></a>Składnia

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania "Walidacja" | Ciąg | Tak |
type | Musi być ustawiona na wartość **Walidacja**. | Ciąg | Tak |
Zestawu | Działanie będzie blokować wykonywanie do momentu zweryfikowania, czy odwołanie do tego zestawu danych istnieje i że spełnia określone kryteria, lub został osiągnięty limit czasu. Dostarczony zestaw danych powinien obsługiwać Właściwość "MinimumSize" lub "ChildItems". | Odwołanie do zestawu danych | Tak |
timeout | Określa limit czasu pracy działania. Jeśli wartość nie jest określona, wartość domyślna to 7 dni ("7.00:00:00"). Format to d. hh: mm: SS | Ciąg | Nie |
chodzenia | Opóźnienie w sekundach między próbami walidacji. Jeśli wartość nie jest określona, wartość domyślna to 10 sekund. | Liczba całkowita | Nie |
childItems | Sprawdza, czy folder ma elementy podrzędne. Można ustawić na wartość-true: Sprawdź, czy folder istnieje i czy zawiera elementy. Bloki do momentu, gdy co najmniej jeden element jest obecny w folderze lub wartość limitu czasu zostanie osiągnięta.-false: Sprawdź, czy folder istnieje i czy jest pusty. Bloki do momentu, gdy folder jest pusty lub zostanie osiągnięta wartość limitu czasu. Jeśli wartość nie zostanie określona, działanie zostanie zablokowane do momentu, aż folder istnieje lub zostanie osiągnięty limit czasu. | Wartość logiczna | Nie |
minimumSize | Minimalny rozmiar pliku w bajtach. Jeśli wartość nie jest określona, wartość domyślna to 0 bajtów. | Liczba całkowita | Nie |


## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Aktywność sieci Web](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
