---
title: azcopy miejsc pracy usunąć | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla zadań azcopy usunąć polecenia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034157"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Usuń wszystkie pliki skojarzone z danym identyfikatorem zadania.

> [!NOTE] 
> Lokalizację, w której są zapisywane pliki dziennika i planowania, można dostosować. Aby dowiedzieć się więcej, zobacz polecenie [azcopy env.](storage-ref-azcopy-env.md)

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opcje

**-h, --pomoc**                Pomoc dotycząca usuwania.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--cap-mbps uint32**      Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.

**--output-type** string Format wyjścia polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "text". (domyślny "tekst")

## <a name="see-also"></a>Zobacz też

- [azcopy jobs](storage-ref-azcopy-jobs.md)
