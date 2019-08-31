---
title: AzCopy zadania Pokaż | Microsoft Docs
description: W tym artykule znajdują się informacje referencyjne dotyczące polecenia AzCopy zadania.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195960"
---
# <a name="azcopy-jobs-show"></a>Pokaż zadania AzCopy

Pokazuje szczegółowe informacje o danym IDENTYFIKATORze zadania.

## <a name="synopsis"></a>Streszczenie

Jeśli tylko identyfikator zadania jest dostarczany bez flagi, zostanie zwrócone podsumowanie postępu zadania.

`with-status` Jeśli flaga jest ustawiona, zostanie wyświetlona lista transferów w zadaniu o podanej wartości.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Wyświetla zawartość pomocy dla polecenia show.|
|--z ciągiem stanu|Tylko lista transferów zadań z tym stanem, dostępne wartości: Rozpoczęto, powodzenie, zakończone niepowodzeniem|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [zadania AzCopy](storage-ref-azcopy-jobs.md)
