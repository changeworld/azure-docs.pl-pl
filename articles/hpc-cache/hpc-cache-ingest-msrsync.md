---
title: Pozyskiwania danych pamięci podręcznej HPC platformy Azure — msrsync
description: Jak używać msrsync do przenoszenia danych do miejsca docelowego magazynu obiektów Blob w pamięci podręcznej HPC usługi Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168430"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Pozyskiwania danych pamięci podręcznej HPC platformy Azure — metoda msrsync

Ten artykuł zawiera szczegółowe instrukcje dotyczące używania ``msrsync`` narzędzia do kopiowania danych do kontenera magazynu obiektów Blob platformy Azure do użycia z pamięcią podręczną HPC usługi Azure.

Aby dowiedzieć się więcej o przenoszeniu danych do magazynu obiektów Blob dla pamięci podręcznej HPC platformy Azure, przeczytaj artykuł [Przenoszenie danych do magazynu obiektów Blob platformy Azure.](hpc-cache-ingest.md)

Narzędzie ``msrsync`` może służyć do przenoszenia danych do miejsca docelowego magazynu zaplecza dla pamięci podręcznej HPC platformy Azure. To narzędzie zostało zaprojektowane w celu optymalizacji ``rsync`` wykorzystania przepustowości przez uruchomienie wielu procesów równoległych. Jest on dostępny w https://github.com/jbd/msrsyncGitHub w .

``msrsync``dzieli katalog źródłowy na oddzielne "zasobniki", ``rsync`` a następnie uruchamia poszczególne procesy na każdym zasobniku.

Wstępne testy przy użyciu czterordzeniowej maszyny Wirtualnej wykazały najlepszą wydajność podczas korzystania z 64 procesów. Użyj ``msrsync`` opcji, ``-p`` aby ustawić liczbę procesów na 64.

Należy ``msrsync`` zauważyć, że można zapisywać tylko do i z woluminów lokalnych. Źródło i miejsce docelowe muszą być dostępne jako instalacje lokalne na stacji roboczej użytej do wydania polecenia.

Postępuj zgodnie z ``msrsync`` tymi instrukcjami, aby użyć do wypełniania magazynu obiektów blob platformy Azure za pomocą pamięci podręcznej HPC usługi Azure:

1. Zainstaluj ``msrsync`` i jego wymagania wstępne``rsync`` (i Python 2.6 lub nowsza)
1. Określ całkowitą liczbę plików i katalogów do skopiowania.

   Na przykład użyj ``prime.py`` narzędzia ```prime.py --directory /path/to/some/directory``` z argumentami <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>(dostępnego przez pobranie ).

   Jeśli nie ``prime.py``używasz, można obliczyć liczbę ``find`` elementów za pomocą narzędzia GNU w następujący sposób:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Podziel liczbę elementów przez 64, aby określić liczbę elementów na proces. Użyj tego numeru ``-f`` z opcją, aby ustawić rozmiar zasobników po uruchomieniu polecenia.

1. Wydaj ``msrsync`` polecenie kopiowania plików:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Na przykład to polecenie jest przeznaczone do przenoszenia 11 000 plików w 64 procesach z /test/source-repozytorium do /mnt/hpccache/repozytorium:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
