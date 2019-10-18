---
title: AzCopy zadania usuwania | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy zadania usuwania.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518293"
---
# <a name="azcopy-jobs-remove"></a>AzCopy zadania usuwania

Usuń wszystkie pliki skojarzone z danym IDENTYFIKATORem zadania.

> [!NOTE] 
> Można dostosować lokalizację, w której zapisywane są pliki dziennika i planowania. Aby dowiedzieć się więcej, zobacz [AzCopy ENV](storage-ref-azcopy-env.md) .

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>Przykłady

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opcje

**-h,--pomoc**                Pomoc dotycząca usuwania.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--Cap-MB/s UInt32**      Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.

**--** format ciągu typu danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text". (domyślny "tekst")

## <a name="see-also"></a>Zobacz także

- [zadania AzCopy](storage-ref-azcopy-jobs.md)
