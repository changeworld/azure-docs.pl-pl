---
title: wznowienie zadań AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące polecenia AzCopy zadania Resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9e8dbbd40259c7a71f252d0d6e93dd6f135973de
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513546"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Wznawia istniejące zadanie z danym IDENTYFIKATORem zadania.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|--ciąg docelowy-sygnatura dostępu współdzielonego|Docelowe sygnatury dostępu współdzielonego miejsca docelowego dla danego identyfikatora zadania.|
|--Wyklucz ciąg|Filtr: wykluczanie nieudanych transferów podczas wznawiania zadania. Pliki powinny być rozdzielone znakami ";".|
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
