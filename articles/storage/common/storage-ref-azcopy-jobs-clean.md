---
title: czyszczenie zadań AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dla polecenia AzCopy Jobs Clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518514"
---
# <a name="azcopy-jobs-clean"></a>czyszczenie zadań AzCopy

Usuń wszystkie pliki dziennika i planu dla wszystkich zadań

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>Przykłady

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opcje

**-h,--pomoc**                Pomoc dla czyszczenia.

parametr **--with-status** usuwa tylko zadania z tym stanem, dostępne wartości: anulowane, zakończone, zakończone niepowodzeniem, w toku, wszystkie (domyślnie "wszystkie")

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--Cap-MB/s UInt32**      Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.

**--** format ciągu typu danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text". (domyślny "tekst")

## <a name="see-also"></a>Zobacz także

- [zadania AzCopy](storage-ref-azcopy-jobs.md)
