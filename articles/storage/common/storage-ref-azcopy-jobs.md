---
title: oferty pracy w azcopy | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia zadania azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 27c06656d95c5165b33b6056a3cf3b554f0e5469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034109"
---
# <a name="azcopy-jobs"></a>azcopy jobs

Polecenia podrzędne związane z zarządzaniem zadaniami.

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h, --pomoc|Pokaż zawartość pomocy dla polecenia zadania.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mbps uint32|Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.|
|Ciąg typu --output|Format danych wyjściowych polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "tekst".|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
