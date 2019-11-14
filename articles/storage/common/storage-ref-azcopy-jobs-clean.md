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
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033718"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Usuń wszystkie pliki dziennika i planu dla wszystkich zadań

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

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

## <a name="see-also"></a>Zobacz też

- [zadania AzCopy](storage-ref-azcopy-jobs.md)
