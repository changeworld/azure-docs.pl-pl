---
title: Lista AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia list AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8c40241c49917c6db6663f346aed0ec5d3b96be7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195947"
---
# <a name="azcopy-list"></a>Lista AzCopy

Wyświetla listę jednostek w danym zasobie.

## <a name="synopsis"></a>Streszczenie

Tylko kontenery obiektów BLOB są obsługiwane w bieżącej wersji.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Przykłady

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Pokaż zawartość pomocy dla polecenia list.|
|--do odczytu maszynowego|Wyświetla listę rozmiarów plików w bajtach.|
|--megapiksela|Wyświetla jednostki w zamówieniach 1000, nie 1024.|
|--uruchomiono-Zgadzam się|Zlicza łączną liczbę plików i ich rozmiary.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
