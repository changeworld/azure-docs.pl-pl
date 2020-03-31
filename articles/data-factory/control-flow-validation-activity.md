---
title: Działanie sprawdzania poprawności w fabryce danych platformy Azure
description: Działanie sprawdzania poprawności nie kontynuuje wykonywania potoku, dopóki nie weryfikuje dołączonego zestawu danych z określonymi kryteriami określonymi przez użytkownika.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678369"
---
# <a name="validation-activity-in-azure-data-factory"></a>Działanie sprawdzania poprawności w fabryce danych platformy Azure
Można użyć sprawdzania poprawności w potoku, aby upewnić się, że potok kontynuuje wykonywanie tylko po zatwierdzeniu dołączone odwołanie do dołączonego zestawu danych istnieje, że spełnia określone kryteria lub limit czasu został osiągnięty.


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
name | Nazwa działania "Sprawdzanie poprawności" | Ciąg | Tak |
type | Musi być **ustawiona**na Sprawdzanie poprawności . | Ciąg | Tak |
Dataset | Działanie zablokuje wykonanie, dopóki nie potwierdzi, że to odwołanie do tego zestawu danych istnieje i że spełnia określone kryteria lub limit czasu został osiągnięty. Zestaw danych pod warunkiem, powinny obsługiwać "MinimumSize" lub "ChildItems" właściwości. | Odwołanie do zestawu danych | Tak |
timeout | Określa limit czasu pracy działania. Jeśli nie określono żadnej wartości, wartość domyślna to 7 dni ("7.00:00:00"). Format to d.hh:mm:ss | Ciąg | Nie |
Snu | Opóźnienie w sekundach między próbami sprawdzania poprawności. Jeśli żadna wartość nie zostanie określona, wartość domyślna to 10 sekund. | Liczba całkowita | Nie |
Childitems | Sprawdza, czy folder zawiera elementy podrzędne. Można ustawić na true: Sprawdź, czy folder istnieje i czy zawiera elementy. Bloki, dopóki co najmniej jeden element jest obecny w folderze lub wartość limitu czasu zostanie osiągnięty.-false: Sprawdź, czy folder istnieje i czy jest pusty. Blokuje, dopóki folder nie będzie pusty lub do momentu osiągnięcia wartości limitu czasu. Jeśli nie określono żadnej wartości, działanie zostanie zablokowane, dopóki folder nie istnieje lub do momentu osiągnięcia limitu czasu. | Wartość logiczna | Nie |
Minimumsize | Minimalny rozmiar pliku w bajtach. Jeśli żadna wartość nie zostanie określona, wartość domyślna to 0 bajtów | Liczba całkowita | Nie |


## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Uzyskaj aktywność metadanych](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
