---
title: AzCopy doc | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513857"
---
# <a name="azcopy-doc"></a>azcopy doc

Generuje dokumentację dla narzędzia w formacie promocji.

## <a name="synopsis"></a>Streszczenie

Program generuje dokumentację dla narzędzia w formacie promocji i przechowuje je w wyznaczeniej lokalizacji.

Domyślnie pliki są przechowywane w folderze o nazwie "doc" w bieżącym katalogu.

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Wyświetla zawartość pomocy dla polecenia doc.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
