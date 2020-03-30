---
title: Tworzenie pamięci podręcznej HPC platformy Azure
description: Jak utworzyć wystąpienie pamięci podręcznej HPC platformy Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582181"
---
# <a name="plan-the-aggregated-namespace"></a>Planowanie zagregowanej przestrzeni nazw

Usługa Azure HPC Cache umożliwia klientom dostęp do różnych systemów pamięci masowej za pośrednictwem wirtualnej przestrzeni nazw, która ukrywa szczegóły systemu pamięci masowej zaplecza.

Po dodaniu obiektu docelowego magazynu ustawiono ścieżkę pliku skierowaną do klienta. Komputery klienckie montują tę ścieżkę pliku i mogą przesyłać żądania odczytu pliku do pamięci podręcznej zamiast bezpośrednio montować system magazynowania.

Ponieważ usługa Azure HPC Cache zarządza tym wirtualnym systemem plików, można zmienić miejsce docelowe magazynu bez zmiany ścieżki skierowanej do klienta. Na przykład można zastąpić sprzętowy system magazynowania magazynem w chmurze bez konieczności przepisywania procedur przeznaczonych dla klienta.

## <a name="aggregated-namespace-example"></a>Przykład zagregowanego obszaru nazw

Zaplanuj zagregowany obszar nazw, aby maszyny klienckie mogły wygodnie uzyskać dostęp do potrzebnych informacji, a administratorzy i inżynierowie przepływu pracy mogli łatwo odróżnić ścieżki.

Rozważmy na przykład system, w którym wystąpienie pamięci podręcznej HPC usługi Azure jest używane do przetwarzania danych przechowywanych w obiekcie blob platformy Azure. Analiza wymaga plików szablonów, które są przechowywane w lokalnym centrum danych.

Dane szablonu są przechowywane w centrum danych, a informacje potrzebne do tego zadania są przechowywane w następujących podkatalogach:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

System magazynowania centrów danych udostępnia te eksporty:

    /
    /goldline
    /goldline/templates

Dane, które mają być analizowane został skopiowany do kontenera magazynu obiektów Blob platformy Azure o nazwie "sourcecollection" przy użyciu [narzędzia CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Aby umożliwić łatwy dostęp za pośrednictwem pamięci podręcznej, należy rozważyć utworzenie obiektów docelowych magazynu z tych ścieżek wirtualnego obszaru nazw:

| System pamięci masowej zaplecza <br/> (Ścieżka pliku NFS lub kontener obiektów blob) | Ścieżka wirtualnej przestrzeni nazw |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /szablony/sku980      |
| pozyskiwanie źródeł                        | /źródło/               |

Obiekt docelowy magazynu systemu plików NFS może mieć wiele ścieżek wirtualnego obszaru nazw, o ile każda z nich odwołuje się do unikatowej ścieżki eksportu.

Ponieważ ścieżki źródłowe systemu plików NFS są podkatalogami tego samego eksportu, należy zdefiniować wiele ścieżek obszaru nazw z tego samego miejsca docelowego magazynu.

| Nazwa hosta docelowego magazynu  | Ścieżka eksportu nfs      | Ścieżka podkatalogu | Ścieżka obszaru nazw    |
|--------------------------|----------------------|-------------------|-------------------|
| *Adres IP lub nazwa hosta* | /goldline/szablony  | acme2017/sku798   | /templates/sku798 |
| *Adres IP lub nazwa hosta* | /goldline/szablony  | acme2017/sku980   | /szablony/sku980 |

Aplikacja kliencka może zainstalować pamięć podręczną i łatwo ``/source`` ``/templates/sku798``uzyskać ``/templates/sku980``dostęp do zagregowanych ścieżek plików obszaru nazw , i .

## <a name="next-steps"></a>Następne kroki

Po podjęciu decyzji o skonfigurowaniu wirtualnego systemu plików [należy utworzyć obiekty docelowe magazynu,](hpc-cache-add-storage.md) aby zamapować magazyn zaplecza na ścieżki wirtualnych plików skierowane do klienta.
