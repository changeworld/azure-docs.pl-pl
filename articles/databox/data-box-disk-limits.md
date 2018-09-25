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
ms.date: 09/04/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: b7de39ca8723df2d15da7677b7032c98e84ecb1d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960621"
---
# <a name="azure-data-box-disk-limits-preview"></a>Limity dysku Data Box platformy Azure (wersja zapoznawcza)


Te limity wziąć pod uwagę wdrażania i obsługi rozwiązania dysku systemu Microsoft Azure Data Box. 

> [!IMPORTANT] 
> Dysku Azure Data Box jest w wersji zapoznawczej. Przegląd [warunki użytkowania dotyczące wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) przed wdrożeniem tego rozwiązania. 


## <a name="data-box-service-limits"></a>Limity usługi pole danych

 - Usługa Data Box jest dostępna tylko w USA, Europa, Kanadzie i Australii we wszystkich regionach platformy Azure dla chmury publicznej platformy Azure.
 - Pojedynczego konta magazynu jest obsługiwana przy użyciu dysku Data Box.

## <a name="data-box-disk-performance"></a>Wydajność dysku pole danych

Podczas testowania dysków podłączonych za pomocą portu USB 3.0 ich wydajność doszła do poziomu 430 MB/s. Rzeczywista wartość różni się w zależności od rozmiaru pliku. Przy mniejszych plikach wydajność może być niższa.

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

Poniżej przedstawiono limity rozmiaru danych, które są kopiowane do konta magazynu. Upewnij się, że dane, które zostaną przesłane odpowiada tych limitów. Aby uzyskać najbardziej aktualne informacje o tych limitach, przejdź do [obiekty docelowe skalowania magazynu obiektów blob platformy Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) i [usługi Azure Files skalowanie elementów docelowych](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Rozmiar danych skopiowane do konta magazynu platformy Azure                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Obiekt blob blokowy obiekt Blob i strony                                            | 500 TB na konto magazynu. <br> Obejmuje to dane ze wszystkich źródeł, w tym dysku Data Box.|


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tych limitów.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt blob        | ~ 8 TB                                                 |
| Stronicowy obiekt blob         | 1 TB <br> (Każdy plik przekazany w formacie stronicowych obiektów Blob musi być 512 bajtów wyrównane (całkowitą wielokrotnością), w przeciwnym wypadku przekazywania nie powiedzie się. <br> VHD i VHDX są 512 bajtów wyrównane). |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Usługa Azure blokowych obiektów blob i stronicowych obiektów blob, konwencje nazewnictwa

| Jednostka                                       | Konwencja                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwy kontenerów dla blokowych obiektów blob i stronicowych obiektów blob | Musi być prawidłową nazwą DNS, który składa się z 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i znaki łącznika (-). <br> Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. <br> Nazwy nie mogą zawierać sąsiadujących ze sobą łączników. |
| Nazwy blokowych i stronicowych obiektów blob      | W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. <br> Nazwa obiektu blob musi zawierać od 1 do 1024 znaków. <br> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. <br>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego. |


## <a name="next-steps"></a>Kolejne kroki
* Przegląd [wymagania systemowe urządzenia Data Box](data-box-system-requirements.md)
