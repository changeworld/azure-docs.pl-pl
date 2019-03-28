---
title: Działanie sprawdzania poprawności w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Działanie sprawdzania poprawności nie kontynuacja wykonywania potoku sprawdza poprawność dołączonych zestaw danych o określone kryteria, określonego przez użytkownika.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523029"
---
# <a name="validation-activity-in-azure-data-factory"></a>Działanie sprawdzania poprawności w usłudze Azure Data Factory
Sprawdzanie poprawności w potoku służy do zapewnienia potoku tylko kontynuuje wykonywanie, gdy został zweryfikowany dołączonego istnieje odwołanie do zestawu danych, że spełnia określone kryteria lub osiągnięto limit czasu.


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

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa działania "Weryfikacji" | String | Yes |
type | Musi być równa **weryfikacji**. | String | Yes |
Zestaw danych | Działanie spowoduje wykonanie bloku, dopóki nie został zweryfikowany, to odwołanie do zestawu danych istnieje i że spełnia określone kryteria, lub osiągnięto limit czasu. Podany zestaw danych powinien obsługiwać właściwość "MinimumSize" lub "ChildItems". | Odwołanie do zestawu danych | Yes |
timeout | Określa limit czasu pracy działania. Jeśli nie określono wartości, wartość domyślna to 7 dni ("7.00:00:00"). Format jest d.hh:mm:ss | String | Nie |
Stan uśpienia | Opóźnienie w ciągu kilku sekund między prób weryfikacji. Jeśli nie określono wartości, wartość domyślna to 10 sekund. | Liczba całkowita | Nie |
childItems | Sprawdza, czy folder ma elementy podrzędne. Można ustawić na wartość true: Sprawdź, czy folder istnieje i że ma on elementy. Blokuje, dopóki co najmniej jeden element znajduje się w folderze lub nie zostanie przekroczony limit czasu.-false: Sprawdź, czy folder istnieje i że jest pusty. Blokuje, aż do folderu jest pusta lub do przekroczenia limitu czasu jest osiągnięta wartość. Jeśli wartość nie zostanie określona, działanie zablokuje dopóki folder istnieje, lub do momentu osiągnięcia limitu czasu. | Wartość logiczna | Nie |
minimumSize | Minimalny rozmiar pliku w bajtach. Jeśli wartość nie zostanie określona, wartością domyślną jest 0 bajtów | Liczba całkowita | Nie |


## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
