---
title: Ogranicza dysku Azure Data Box | Dokumentacja firmy Microsoft
description: Opis ograniczeń systemowych i zalecane rozmiary dysku usługi Microsoft Azure Data Box.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/12/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 4db70fa93914ba0544d9beb8e523241513a2e5ce
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009301"
---
# <a name="azure-data-box-disk-limits-preview"></a>Limity dysku Data Box platformy Azure (wersja zapoznawcza)


Te limity wziąć pod uwagę wdrażania i obsługi rozwiązania dysku systemu Microsoft Azure Data Box. 

> [!IMPORTANT] 
> Dysku Azure Data Box jest w wersji zapoznawczej. Przegląd [warunki użytkowania dotyczące wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) przed wdrożeniem tego rozwiązania. 


## <a name="data-box-service-limits"></a>Limity usługi pole danych

 - Usługa Data Box jest dostępna tylko w USA i UE we wszystkich regionach platformy Azure dla chmury publicznej platformy Azure.
 - Pojedynczego konta magazynu jest obsługiwana przy użyciu dysku Data Box.

## <a name="data-box-disk-performance"></a>Wydajność dysku pole danych

Podczas testowania za pomocą dysków połączonych za pomocą USB 3.0 wydajność dysku była maksymalnie 430 MB/s. Wartości rzeczywistych różnią się w zależności od rozmiaru pliku używane. Mniejsze pliki mogą pojawić się obniżenie wydajności.

## <a name="azure-storage-limits"></a>Limity usługi Azure storage

W tej sekcji opisano limity dla usługi Azure Storage i wymagane konwencje nazewnictwa dla usługi Azure Files, Azure blokowych obiektów blob i Azure BLOB typu Page, jeśli ma to zastosowanie do usługi Data Box. Należy uważnie przeczytać limity magazynu i postępować zgodnie z zaleceniami.

Aby uzyskać najnowsze informacje o limitach magazynu platformy Azure i najlepsze rozwiązania dotyczące nazewnictwa udziałów, kontenerów i plików przejdź do:

- [Nazewnictwo i odwołania do kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nazewnictwo i odwoływanie się do akcji](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokowe obiekty BLOB i konwencje blob strony](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Jeśli istnieją pliki lub katalogi, które przekraczają limity usługi Azure Storage lub nie są zgodne z konwencjami nazewnictwa plików/obiektów Blob platformy Azure, następnie te pliki lub katalogi są nie pozyskane do usługi Azure Storage za pomocą usługi Data Box.

## <a name="data-upload-caveats"></a>Przekazywanie danych do zastrzeżenia

- Nie Kopiuj danych bezpośrednio do dysków. Kopiowanie danych do wstępnie utworzonego *BlockBlob* i *PageBlob* folderów.
- Folder w węźle *BlockBlob* i *PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *BlockBlob/kontenera* i *PageBlob/kontenera*.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt blob) w chmurze o tej samej nazwie jako obiektu, które są kopiowane dysku Data Box spowoduje zastąpienie plików w chmurze.
- Każdy plik zapisywane *BlockBlob* i *PageBlob* akcji jest przekazywany jako blokowe obiekty blob i stronicowych obiektów blob odpowiednio.
- Dowolne puste hierarchii katalogów (bez żadnych plików) utworzone w ramach *BlockBlob* i *PageBlob* folderów nie zostało załadowane.
- Jeśli występują błędy podczas przekazywania danych na platformie Azure, w dzienniku błędów jest tworzony w docelowym koncie magazynu. Ścieżka do tego dziennika błędów jest dostępna w portalu, po zakończeniu przekazywania i przejrzeć dziennik podjęcia akcji naprawczej. Nie należy usuwać dane ze źródła bez weryfikowania przekazane dane.

## <a name="azure-storage-account-size-limits"></a>Limity rozmiaru konta usługi Azure storage

Poniżej przedstawiono limity rozmiaru danych, które są kopiowane do konta magazynu. Upewnij się, że dane, które zostaną przesłane odpowiada tych limitów. Aby uzyskać najbardziej aktualne informacje o tych limitach, przejdź do [obiekty docelowe skalowania magazynu obiektów blob platformy Azure](https://docs.microsoft.com/en-us/azure/storage/cstorage-scalability-targets#azure-blob-storage-scale-targets) i [usługi Azure Files skalowanie elementów docelowych](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Rozmiar danych skopiowane do konta magazynu platformy Azure                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Obiekt blob blokowy obiekt Blob i strony                                            | 500 TB na konto magazynu. <br> Obejmuje to dane ze wszystkich źródeł, w tym dysku Data Box.|


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tych limitów.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowe obiekty Blob        | ~ 8 TB                                                 |
| Obiekt blob typu Page         | 1 TB <br> (Każdy plik przekazany w formacie stronicowych obiektów Blob musi być 512 bajtów wyrównane (całkowitą wielokrotnością), w przeciwnym wypadku przekazywania nie powiedzie się. <br> VHD i VHDX są 512 bajtów wyrównane). |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Usługa Azure blokowych obiektów blob i stronicowych obiektów blob, konwencje nazewnictwa

| Jednostka                                       | Konwencje                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwy kontenerów dla blokowych obiektów blob i stronicowych obiektów blob | Musi być prawidłową nazwą DNS, który składa się z 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i znaki łącznika (-). <br> Każdy łącznik (-) musi być natychmiast poprzedzony i następuje litera lub cyfra. <br> Następujące po sobie łączniki są niedozwolone w nazwach. |
| Nazwy obiektu blob dla blokowych obiektów blob i stronicowych obiektów blob      | Nazwy obiektów blob jest rozróżniana wielkość liter i może zawierać dowolną kombinację znaków. <br> Nazwa obiektu blob musi być od 1 do 1024 znaków. <br> Zastrzeżone znaki adresu URL muszą być poprzedzone odpowiednim znakiem ucieczki. <br>Liczba segmentów ścieżki wchodzących w skład nazwy obiektu blob nie może przekraczać 254. Segment ścieżki jest ciągiem od ogranicznika następujących po sobie znaków (na przykład ukośnika "/") odnoszą się do nazwy katalogu wirtualnego. |
