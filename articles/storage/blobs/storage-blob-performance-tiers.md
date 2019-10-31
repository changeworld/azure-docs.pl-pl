---
title: Warstwy wydajności usługi Azure Block BLOB Storage — Azure Storage
description: Warstwy wydajności dla usługi Azure Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: e0d746f1b01784bc383c12543936f06dae66ca09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063259"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Warstwy wydajności bloku blokowych obiektów blob platformy Azure

W miarę jak przedsiębiorstwa wdrażają aplikacje natywne w chmurze, ważne jest, aby dysponować opcjami oszczędnego magazynowania danych na różnych poziomach wydajności.

Magazyn blokowych obiektów blob platformy Azure oferuje dwie różne warstwy wydajności:

- Premium: zoptymalizowane pod kątem wysokich stawek transakcji i jednocyfrowe opóźnienie magazynu
- Standard: zoptymalizowane pod kątem wysokiej wydajności i wysokiej przepływności

Poniższe uwagi dotyczą różnych warstw wydajności:

- Standardowa wydajność jest dostępna we wszystkich [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=storage). Wydajność warstwy Premium jest dostępna w [wybranych regionach](https://azure.microsoft.com/global-infrastructure/services/?products=storage).
- Wydajność Premium zapewnia zoptymalizowane ceny dla aplikacji o wysokich stawkach transakcji, aby [zmniejszyć łączny koszt magazynu](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) dla tych obciążeń.
- Aby uzyskać wydajność warstwy Premium dla blokowych obiektów blob, należy użyć typu konta BlockBlobStorage.
- Standardowa wydajność jest dostępna w przypadku kont Ogólnego przeznaczenia V1, Ogólnego przeznaczenia v2 i BLOB Storage.
- Wydajność warstwy Premium i standardowa obsługuje [wysoką przepływność blokowych obiektów BLOB](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). Blokowe obiekty blob o wysokiej przepływności są dostępne dla wydajności Premium o wartości większej niż 256 KiB. Blokowe obiekty blob o wysokiej przepływności są dostępne dla wydajności standardowej o rozmiarze większym niż 4.
- Wydajność warstwy Premium jest obecnie dostępna tylko w przypadku magazynu lokalnie nadmiarowego (LRS).

## <a name="premium-performance"></a>Wydajność warstwy Premium

Blok wydajności w warstwie Premium magazynu obiektów BLOB udostępnia dane za pośrednictwem sprzętu o wysokiej wydajności. Dane są przechowywane na dyskach półprzewodnikowych (dysków SSD), które są zoptymalizowane pod kątem małych opóźnień. Dysków SSD zapewniają wyższą przepływność w porównaniu z tradycyjnymi dyskami twardymi.

Magazyn obiektów BLOB w warstwie Premium jest idealnym rozwiązaniem w przypadku obciążeń wymagających szybkiego i spójnego czasu odpowiedzi. Jest to najlepsze rozwiązanie w przypadku obciążeń, które wykonują wiele małych transakcji.

## <a name="standard-performance"></a>Wydajność standardowa

Standardowa wydajność obsługuje różne [warstwy dostępu](storage-blob-storage-tiers.md) do przechowywania danych w najbardziej opłacalny sposób. Jest ona zoptymalizowana pod kątem wysokiej wydajności i wysokiej przepływności w przypadku dużych zestawów danych.

## <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów BLOB

Blob Storage Zarządzanie cyklem życia obejmuje zaawansowane zasady oparte na regułach:

- Wersja Premium — wygasłe dane po zakończeniu cyklu życia
- Standardowe przechodzenie danych do najlepszej warstwy dostępu i wygasanie danych na końcu cyklu życia

Aby dowiedzieć się więcej, zobacz [Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md).

Dane przechowywane na koncie magazynu blokowych obiektów BLOB w warstwie Premium nie mogą być przenoszone między warstwami gorąca, chłodna i archiwalna. Można jednak kopiować obiekty blob z konta blokowego usługi BLOB Storage do warstwy dostępu gorąca na *innym* koncie. Użyj [bloku Put z adresu URL](/rest/api/storageservices/put-block-from-url) API lub [AzCopy v10](../common/storage-use-azcopy-v10.md) , aby skopiować dane na inne konto. Interfejs **Put bloku z adresu URL** synchronicznie kopiuje dane na serwerze. Wywołanie kończy się tylko po przeniesieniu wszystkich danych z oryginalnej lokalizacji serwera do lokalizacji docelowej.

## <a name="next-steps"></a>Następne kroki

Oceń gorące, chłodne i archiwalne na kontach GPv2 i BLOB Storage

- [Sprawdzanie dostępności warstw Gorąca, Chłodna i Archiwum według regionu](https://azure.microsoft.com/regions/#services)
- [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)
- [Dowiedz się więcej na temat ponownego wypełniania danych obiektów BLOB w warstwie Archiwum](storage-blob-rehydration.md)
- [Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md)
- [Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)
- [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
