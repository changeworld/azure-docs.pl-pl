---
title: AzCopy doc | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d1f57ae40b47c1d910ba20ae8a8f19cdc6908d6b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196038"
---
# <a name="azcopy-doc"></a>AzCopy doc

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
