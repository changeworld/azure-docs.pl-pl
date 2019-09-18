---
title: Tworzenie pamięci podręcznej platformy Azure HPC
description: Jak utworzyć wystąpienie pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: c3d14eaefaa1f317cb061273866ffee83747f12b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036841"
---
# <a name="configure-aggregated-namespace"></a>Skonfiguruj zagregowaną przestrzeń nazw
<!-- change link in GUI -->

Pamięć podręczna Azure HPC umożliwia klientom dostęp do różnych systemów magazynowania za pomocą wirtualnej przestrzeni nazw, która ukrywa szczegóły systemu magazynu zaplecza.

Po dodaniu miejsca docelowego magazynu należy ustawić ścieżkę dla klienta. Komputery klienckie instalują tę ścieżkę pliku. Można zmienić miejsce docelowe magazynu skojarzone z tą ścieżką. Można na przykład zastąpić sprzętowy system magazynowania z magazynem w chmurze bez konieczności ponownego zapisywania procedur związanych z klientem.

## <a name="aggregated-namespace-example"></a>Przykład zagregowanego przestrzeni nazw

Zaplanuj zagregowaną przestrzeń nazw, aby komputery klienckie mogły wygodnie uzyskać dostęp do potrzebnych informacji, a administratorzy i inżynierowie przepływów pracy mogą łatwo odróżnić ścieżki.

Rozważmy na przykład system, w którym wystąpienie pamięci podręcznej platformy Azure HPC jest używane do przetwarzania danych przechowywanych w obiekcie blob platformy Azure. Analiza wymaga plików szablonów przechowywanych w lokalnym centrum danych.

Dane szablonu są przechowywane w centrum danych, a informacje wymagające tego zadania są przechowywane w następujących podkatalogach:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

System magazynu centrum danych ujawnia następujące eksporty: 

    /
    /goldline
    /goldline/templates

Dane, które mają zostać poddane analizie, zostały skopiowane do kontenera magazynu obiektów blob platformy Azure o nazwie "SourceCollection" przy użyciu [Narzędzia CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Aby zapewnić łatwy dostęp za pomocą pamięci podręcznej, należy rozważyć utworzenie obiektów docelowych magazynu przy użyciu tych ścieżek wirtualnych przestrzeni nazw:

| Ścieżka systemu plików NFS lub kontener obiektów BLOB zaplecza | Ścieżka wirtualnej przestrzeni nazw |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| obiekcie SourceCollection wprowadzanych                        | /source               |

Ponieważ ścieżki źródłowe NFS są podkatalogami tego samego eksportu, należy zdefiniować wiele ścieżek przestrzeni nazw z tego samego miejsca docelowego magazynu. 

| Nazwa hosta docelowego magazynu  | Ścieżka eksportowania NFS      | Ścieżka podkatalogu | Ścieżka przestrzeni nazw    |
|--------------------------|----------------------|-------------------|-------------------|
| *Adres IP lub nazwa hosta* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *Adres IP lub nazwa hosta* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Aplikacja kliencka może zainstalować pamięć podręczną i łatwo uzyskać dostęp do zagregowanych nazw FilePath/source,/templates/sku798 i/templates/sku980.

## <a name="next-steps"></a>Następne kroki

Po podjęciu decyzji o sposobie konfigurowania wirtualnego systemu plików [Utwórz miejsce docelowe magazynu](hpc-cache-add-storage.md) , aby zmapować magazyn zaplecza na ścieżki wirtualne z możliwością klienta.
