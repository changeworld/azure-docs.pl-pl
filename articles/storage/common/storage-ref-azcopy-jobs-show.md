---
title: azcopy praca pokaż | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia show zadań azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034133"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Pokazuje szczegółowe informacje dotyczące danego identyfikatora zadania.

## <a name="synopsis"></a>Streszczenie

Jeśli tylko identyfikator zadania jest podany bez flagi, zwracane jest podsumowanie postępu zadania.

Liczba bajtów i procent wykonania, który pojawia się po uruchomieniu tego polecenia odzwierciedlają tylko pliki, które zostały ukończone w zadaniu. Nie odzwierciedlają one częściowo ukończonych plików.

Jeśli `with-status` flaga jest ustawiona, zostanie wyświetlona lista transferów w zadaniu o podanej wartości.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h, --pomoc|Pokazuje zawartość pomocy dla polecenia show.|
|--z ciągiem stanu|Wyświetlaj tylko listę transferów zadania o tym stanie, dostępne wartości: Uruchomiono, Powodzenie, Niepowodzenie|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mbps uint32|Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.|
|Ciąg typu --output|Format danych wyjściowych polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "tekst".|

## <a name="see-also"></a>Zobacz też

- [azcopy jobs](storage-ref-azcopy-jobs.md)
