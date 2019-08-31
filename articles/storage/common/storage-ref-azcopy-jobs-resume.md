---
title: wznowienie zadań AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące polecenia AzCopy zadania Resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8786da42f000a2f13279499159a7af424aa10748
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195791"
---
# <a name="azcopy-jobs-resume"></a>wznowienie zadań AzCopy

Wznawia istniejące zadanie z danym IDENTYFIKATORem zadania.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|--ciąg docelowy-sygnatura dostępu współdzielonego|Docelowe sygnatury dostępu współdzielonego miejsca docelowego dla danego identyfikatora zadania.|
|--Wyklucz ciąg|Filtru Wyklucz te nieudane transfery podczas wznawiania zadania. Pliki powinny być rozdzielone znakami ";".|
|-h,--pomoc|Pokaż zawartość pomocy dla polecenia Resume.|
|--include String|Filtr: Uwzględnij tylko te nieudane transfery podczas wznawiania zadania. Pliki powinny być rozdzielone znakami ";".|
|--ciąg źródłowy-SAS |Źródło SAS źródła dla danego identyfikatora zadania.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [zadania AzCopy](storage-ref-azcopy-jobs.md)
