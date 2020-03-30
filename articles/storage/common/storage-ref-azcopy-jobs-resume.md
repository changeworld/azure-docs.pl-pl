---
title: azcopy praca wznowić | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia wznowienia zadań azkopii.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034140"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Wznawia istniejące zadanie z danym identyfikatorem zadania.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|Ciąg --destination-sas|Docelowy sygnatura dostępu Sygnatura docelowego dla danego jobid.|
|Ciąg --exclude|Filtr: Wyklucz te nieudane transfery podczas wznawiania zadania. Pliki powinny być oddzielone ';'.|
|-h, --pomoc|Pokaż zawartość pomocy dla polecenia resume.|
|--include ciąg|Filtr: uwzględnij tylko te nieudane transfery podczas wznawiania zadania. Pliki powinny być oddzielone ';'.|
|Ciąg --source-sas |źródła SAS źródła dla danego jobid.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mbps uint32|Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.|
|Ciąg typu --output|Format danych wyjściowych polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "tekst".|

## <a name="see-also"></a>Zobacz też

- [azcopy jobs](storage-ref-azcopy-jobs.md)
