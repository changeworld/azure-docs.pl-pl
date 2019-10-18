---
title: AzCopy wylogowania | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia Wyloguj AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 84b65470b12212eb1038e18bd442ff07511a5c1a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513500"
---
# <a name="azcopy-logout"></a>azcopy logout

Wylogowuje użytkownika i kończy dostęp do zasobów usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

To polecenie spowoduje usunięcie wszystkich buforowanych informacji logowania dla bieżącego użytkownika.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Pokaż zawartość pomocy dla polecenia Wyloguj.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
