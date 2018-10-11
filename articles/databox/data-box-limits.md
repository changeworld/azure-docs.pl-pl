---
title: Limity usługi Azure Data Box | Dokumentacja firmy Microsoft
description: Opis ograniczeń systemowych i zalecane rozmiary dla połączeń i składniki programu Microsoft Azure Data Box.
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
ms.date: 10/10/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: d1aeabd2de529d2c6b3159d9cd65996fb5096d0a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069373"
---
# <a name="azure-data-box-limits"></a>Limity usługi Azure Data Box

Te limity wziąć pod uwagę wdrażania i obsługi usługi Microsoft Azure Data Box. W poniższej tabeli opisano limity dla pola danych.


## <a name="data-box-service-limits"></a>Limity usługi pole danych

 - Usługa Data Box jest dostępna tylko w Stanach Zjednoczonych w ogóle [regiony platformy Azure dla publicznej chmury Azure](https://azure.microsoft.com/regions/).
 - Jeśli przy użyciu wielu kont magazynu przy użyciu usługi Data Box, wszystkie konta magazynu musisz należą do tylko tym samym regionie platformy Azure.
 - Zaleca się, że używasz nie więcej niż trzy konta magazynu. Więcej kont usługi storage można mogłoby to wpłynąć na wydajność.

## <a name="data-box-limits"></a>Ogranicza urządzenia Data Box

- Urządzenie Data Box można przechowywać maksymalnie 500 milionów plików.

## <a name="azure-storage-limits"></a>Limity usługi Azure storage

W tej sekcji opisano limity dla usługi Azure Storage i wymagane konwencje nazewnictwa dla usługi Azure Files, Azure blokowych obiektów blob i Azure BLOB typu Page, jeśli ma to zastosowanie do usługi Data Box. Należy uważnie przeczytać limity magazynu i postępować zgodnie z zaleceniami.

Aby uzyskać najnowsze informacje o limitach magazynu platformy Azure i najlepsze rozwiązania dotyczące nazewnictwa udziałów, kontenerów i plików przejdź do:

- [Nazewnictwo i odwołania do kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nazywanie i przywoływanie udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokowe obiekty BLOB i konwencje blob strony](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Jeśli istnieją pliki lub katalogi, które przekraczają limity usługi Azure Storage lub nie są zgodne z konwencjami nazewnictwa plików/obiektów Blob platformy Azure, następnie te pliki lub katalogi są nie pozyskane do usługi Azure Storage za pomocą usługi Data Box.

## <a name="data-upload-caveats"></a>Przekazywanie danych do zastrzeżenia

- Nie Kopiuj danych bezpośrednio pod precreated udziałów. Należy utworzyć folder w udziale, a następnie skopiować dane do tego folderu.
- Folder w węźle *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *StorageAccount_BlockBlob/kontenera* i *StorageAccount_PageBlob/kontenera*.
- Każdy folder utworzone bezpośrednio w ramach *StorageAccount_AzureFiles* jest tłumaczony na udział plików platformy Azure.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt blob lub pliku) w chmurze o tej samej nazwie jako obiektu, które są kopiowane urządzenia Data Box spowoduje zastąpienie plików w chmurze.
- Każdy plik zapisywane *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* akcji jest przekazywany jako blokowe obiekty blob i stronicowych obiektów blob odpowiednio.
- Magazyn obiektów blob platformy Azure nie obsługuje katalogów. Jeśli utworzysz folder w węźle *StorageAccount_BlockBlob* folder, a następnie foldery wirtualne, które są tworzone w nazwie obiektu blob. Dla usługi Azure Files jest zachowywana na strukturę katalogu.
- Dowolne puste hierarchii katalogów (bez żadnych plików) utworzone w ramach *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* folderów nie zostało załadowane.
- Jeśli występują błędy podczas przekazywania danych na platformie Azure, w dzienniku błędów jest tworzony w docelowym koncie magazynu. Ścieżka do tego dziennika błędów jest dostępna po zakończeniu przekazywania i przejrzeć dziennik podjęcia akcji naprawczej. Nie należy usuwać dane ze źródła bez weryfikowania przekazane dane.

## <a name="azure-storage-account-size-limits"></a>Limity rozmiaru konta usługi Azure storage

Poniżej przedstawiono limity rozmiaru danych, które są kopiowane do konta magazynu. Upewnij się, że dane, które zostaną przesłane odpowiada tych limitów. Aby uzyskać najbardziej aktualne informacje o tych limitach, przejdź do [obiekty docelowe skalowania magazynu obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) i [usługi Azure Files skalowanie elementów docelowych](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Rozmiar danych skopiowane do konta magazynu platformy Azure                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Blokowe obiekty blob i stronicowych obiektów blob                                            | 500 TiB na konto magazynu. <br> Obejmuje to dane ze wszystkich źródeł, w tym urządzenia Data Box.|
| Plików platformy Azure                                                          | 5 TiB na jedną akcję.<br> Wszystkie foldery w *StorageAccount_AzureFiles* należy wykonać ten limit.       |

## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tych limitów.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt blob        | ~ 4.75 TiB                                                 |
| Obiekt blob typu Page         | 8 TiB <br> Każdy plik przekazany w formacie obiektu blob strony musi być 512 bajtów wyrównane (całkowitą wielokrotnością), inne przekazywanie nie powiodło się. <br> VHD i VHDX są 512 bajtów wyrównane. |
| Plików platformy Azure        | 1 TiB                                                      |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure blokowych obiektów blob, stronicowych obiektów blob i konwencje nazewnictwa plików

| Jednostka                                       | Konwencja                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwy kontenerów dla blokowych obiektów blob i stronicowych obiektów blob | Musi być prawidłową nazwą DNS, który składa się z 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i znaki łącznika (-). <br> Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. <br> Nazwy nie mogą zawierać sąsiadujących ze sobą łączników. |
| Nazwy udziałów plików platformy Azure                  | Jak wyżej                                                                                                                                                                                                                                                                                                             |
| Nazwy katalogów i plików dla usługi Azure files     |<li> Zachowywanie, bez uwzględniania wielkości liter i nie może przekraczać 255 znaków długości. </li><li> Nie może kończyć się ukośnikiem (/). </li><li>Jeśli nie dostarczono, zostaną automatycznie usunięte. </li><li> Następujące znaki nie są dozwolone: "" \ /: | < > *? "</li><li> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li> Niedozwolone znaki ścieżki adresu URL nie są dozwolone. Punkty kodowe, takich jak \uE000 nie są prawidłowymi znakami Unicode. Niektóre znaki ASCII lub Unicode, takie jak znaki kontrolne (\u0081 0x00 do 0x1F itp.), również nie są dozwolone. Zasady dotyczące Unicode ciągów w HTTP/1.1 temacie dokumencie RFC 2616 sekcji 2.2: podstawowe zasady i RFC 3987. </li><li> Następujący plik nazwy są niedozwolone: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, znak kropki (.) i kropka dwa znaki (.).</li>|
| Nazwy blokowych i stronicowych obiektów blob      | </li><li>W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. </li><li>Nazwa obiektu blob musi zawierać od 1 do 1024 znaków. </li><li>Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego.</li> |
