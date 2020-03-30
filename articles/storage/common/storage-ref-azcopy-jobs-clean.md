---
title: azcopy praca czyste | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla zadania azcopy clean polecenia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033718"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Usuwanie wszystkich plików dziennika i planowania dla wszystkich zadań

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opcje

**-h, --pomoc**                Pomoc dla czystego.

**--z-status** string Tylko usunąć zadania o tym stanie, dostępne wartości: Anulowane, Zakończone, Nie powiodło się, InProgress, Wszystkie (domyślnie "Wszystkie")

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--cap-mbps uint32**      Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.

**--output-type** string Format wyjścia polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "text". (domyślny "tekst")

## <a name="see-also"></a>Zobacz też

- [azcopy jobs](storage-ref-azcopy-jobs.md)
