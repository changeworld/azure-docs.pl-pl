---
title: Brama pola danych platformy Azure ogranicza | Dokumentacja firmy Microsoft
description: Opis ograniczeń systemowych i zalecane rozmiary Microsoft Azure Data Box Gateway.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: edb4995b626055be830a7accb74d99f1db3ef8d0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962209"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Limity bramy pola danych platformy Azure (wersja zapoznawcza)


Te limity wziąć pod uwagę wdrażania i obsługi rozwiązania Microsoft Azure Data Box Gateway. 

> [!IMPORTANT] 
> Brama pole danych jest w wersji zapoznawczej. Przegląd [warunki użytkowania dotyczące wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) przed wdrożeniem tego rozwiązania. 


## <a name="data-box-gateway-service-limits"></a>Limity usługi bramy pola danych

- W tej wersji usługa jest dostępna tylko w określonych regionach w USA, Europa oraz Azja i Pacyfik. Aby uzyskać więcej informacji, przejdź do [dostępność w poszczególnych regionach](#data-box-gateway-overview#region-availability). Konta magazynu powinna być fizycznie najbliższe region wdrożonym urządzenia (może się różnić z obszaru geograficznego usługi).
- Przenoszenie zasobu bramy pola danych do innej subskrypcji lub grupy zasobów nie jest obsługiwana. Aby uzyskać więcej informacji, przejdź do [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="data-box-gateway-device-limits"></a>Limity urządzeń bramy pola danych

W poniższej tabeli opisano limity dla urządzenia bramy pola danych.

| Opis | Wartość |
|---|---|
|Nie. pliki na urządzenie |100 milionów <br> Limit to ~ 25 milionów plików dla każdego 2 TB miejsca na dysku maksymalny limit na 100 mln |
|Nie. akcji na urządzenie |24 |
|Maksymalny rozmiar pliku zapisywane do udziału|5 TB |

## <a name="azure-storage-limits"></a>Limity usługi Azure storage

W tej sekcji opisano limity dla usługi Azure Storage i wymagane konwencje nazewnictwa dla usługi Azure Files, obiekty BLOB typu block platformy Azure i Azure stronicowych obiektów blob, jeśli ma to zastosowanie do krawędzi pola danych/bramy danych usługi. Należy uważnie przeczytać limity magazynu i postępować zgodnie z zaleceniami.

Aby uzyskać najnowsze informacje o limitach magazynu platformy Azure i najlepsze rozwiązania dotyczące nazewnictwa udziałów, kontenerów i plików przejdź do:

- [Nazewnictwo i odwołania do kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nazewnictwo i odwoływanie się do akcji](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokowe obiekty BLOB i konwencje blob strony](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Jeśli istnieją pliki lub katalogi, które przekraczają limity usługi Azure Storage lub nie są zgodne z konwencjami nazewnictwa plików/obiektów Blob platformy Azure, następnie te pliki lub katalogi są nie pozyskane do usługi Azure Storage za pośrednictwem usługi krawędź pole danych/bramy pola danych.

## <a name="data-upload-caveats"></a>Przekazywanie danych do zastrzeżenia

Następujące zastrzeżenia stosowane do danych, kiedy przesuwa się on na platformie Azure.

- Zaleca się, że więcej niż jedno urządzenie nie należy zapisać do tego samego kontenera.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt blob lub pliku) w chmurze o tej samej nazwie jako obiektu, które są kopiowane urządzenia spowoduje zastąpienie plików w chmurze. 
- Hierarchia pusty katalog (bez żadnych plików) utworzone w ramach folderów udziału nie jest przekazywany do kontenerów obiektów blob.


## <a name="azure-storage-account-size-and-object-size-limits"></a>Rozmiar konta magazynu platformy Azure i obiekt ograniczeń rozmiarów

Poniżej przedstawiono limity rozmiaru danych, które są kopiowane do konta magazynu. Upewnij się, że dane, które zostaną przesłane odpowiada tych limitów. Aby uzyskać najbardziej aktualne informacje o tych limitach, przejdź do [obiekty docelowe skalowania magazynu obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) i [usługi Azure Files skalowanie elementów docelowych](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Rozmiar danych skopiowane do konta magazynu platformy Azure                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Obiekt blob blokowy obiekt Blob i strony                                            | 500 TB na konto magazynu|


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tych limitów.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt blob        | ~ 8 TB                                                 |
| Stronicowy obiekt blob         | 1 TB <br> Każdy plik przekazany w formacie stronicowy obiekt Blob musi być 512 bajtów wyrównane (całkowitą wielokrotnością), inne przekazywanie nie powiodło się. <br> VHD i VHDX są 512 bajtów wyrównane. |


## <a name="next-steps"></a>Kolejne kroki

- [Przygotowanie do wdrożenia bramy pola danych platformy Azure](data-box-gateway-deploy-prep.md)
