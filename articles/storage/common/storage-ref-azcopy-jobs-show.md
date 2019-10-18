---
title: AzCopy zadania Pokaż | Microsoft Docs
description: W tym artykule znajdują się informacje referencyjne dotyczące polecenia AzCopy zadania.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513455"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Pokazuje szczegółowe informacje o danym IDENTYFIKATORze zadania.

## <a name="synopsis"></a>Streszczenie

Jeśli tylko identyfikator zadania jest dostarczany bez flagi, zostanie zwrócone podsumowanie postępu zadania.

Liczba bajtów i procent wykonania, które pojawiają się po uruchomieniu tego polecenia, odzwierciedlają tylko pliki, które są wykonywane w zadaniu. Nie odzwierciedlają one częściowo ukończonych plików.

Jeśli flaga `with-status` jest ustawiona, zostanie wyświetlona lista transferów w zadaniu o podanej wartości.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Wyświetla zawartość pomocy dla polecenia show.|
|--z ciągiem stanu|Tylko lista transferów zadań z tym stanem, dostępne wartości: rozpoczęto, sukces, Niepowodzenie|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [zadania AzCopy](storage-ref-azcopy-jobs.md)
