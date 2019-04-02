---
title: Ogranicza dysku Azure Data Box | Dokumentacja firmy Microsoft
description: Opis ograniczeń systemowych i zalecane rozmiary dysku usługi Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 32445e3f6859a6161eb2fae20233c598234f18a0
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791647"
---
# <a name="azure-data-box-disk-limits"></a>Ogranicza dysku Azure Data Box


Te limity wziąć pod uwagę wdrażania i obsługi rozwiązania dysku systemu Microsoft Azure Data Box.

## <a name="data-box-service-limits"></a>Limity usługi pole danych

 - Usługa Data Box jest dostępna w regionach platformy Azure, na liście [dostępność w poszczególnych regionach](data-box-disk-overview.md#region-availability).
 - Pojedynczego konta magazynu jest obsługiwana przy użyciu dysku Data Box.

## <a name="data-box-disk-performance"></a>Wydajność dysku pole danych

Podczas testowania dysków podłączonych za pomocą portu USB 3.0 ich wydajność doszła do poziomu 430 MB/s. Rzeczywista wartość różni się w zależności od rozmiaru pliku. Przy mniejszych plikach wydajność może być niższa.

## <a name="azure-storage-limits"></a>Limity usługi Azure storage

W tej sekcji opisano limity dla usługi Azure Storage i wymagane konwencje nazewnictwa dla usługi Azure Files, Azure blokowych obiektów blob i Azure BLOB typu Page, jeśli ma to zastosowanie do usługi Data Box. Należy uważnie przeczytać limity magazynu i postępować zgodnie z zaleceniami.

Aby uzyskać najnowsze informacje o limitach magazynu platformy Azure i najlepsze rozwiązania dotyczące nazewnictwa udziałów, kontenerów i plików przejdź do:

- [Nazewnictwo i odwołania do kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nazywanie i przywoływanie udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokowe obiekty BLOB i konwencje blob strony](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Jeśli istnieją pliki lub katalogi, które przekraczają limity usługi Azure Storage lub nie są zgodne z konwencjami nazewnictwa plików/obiektów Blob platformy Azure, następnie te pliki lub katalogi są nie pozyskane do usługi Azure Storage za pomocą usługi Data Box.

## <a name="data-upload-caveats"></a>Przekazywanie danych do zastrzeżenia

- Nie Kopiuj danych bezpośrednio do dysków. Kopiowanie danych do wstępnie utworzonego *BlockBlob*,*PageBlob*, i *AzureFile* folderów.
- Folder w węźle *BlockBlob* i *PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *BlockBlob/kontenera* i *PageBlob/kontenera*.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt blob) w chmurze o tej samej nazwie jako obiektu, które są kopiowane dysku Data Box spowoduje zmianę nazwy pliku jako file(1) w chmurze.
- Każdy plik zapisywane *BlockBlob* i *PageBlob* akcji jest przekazywany jako blokowe obiekty blob i stronicowych obiektów blob odpowiednio.
- Dowolne puste hierarchii katalogów (bez żadnych plików) utworzone w ramach *BlockBlob* i *PageBlob* folderów nie zostało załadowane.
- Jeśli występują błędy podczas przekazywania danych na platformie Azure, w dzienniku błędów jest tworzony w docelowym koncie magazynu. Ścieżka do tego dziennika błędów jest dostępna w portalu, po zakończeniu przekazywania i przejrzeć dziennik podjęcia akcji naprawczej. Nie należy usuwać dane ze źródła bez weryfikowania przekazane dane.
- Jeśli określono dysków zarządzanych w kolejności, przejrzyj następujące dodatkowe kwestie:

    - Użytkownik może mieć tylko jeden dysk zarządzany o określonej nazwie w grupie zasobów we wszystkich folderach precreated i we wszystkich dysku Data Box. Oznacza to, że wirtualne dyski twarde przekazany do folderów precreated powinny mieć unikatowe nazwy. Upewnij się, że dana nazwa jest niezgodna już istniejącego dysku zarządzanego w grupie zasobów. Jeśli wirtualne dyski twarde mają takie same nazwy, tylko jeden wirtualny dysk twardy jest konwertowany na dysk zarządzany o tej nazwie. Inne dyski VHD są ładowane jako stronicowe obiekty BLOB konta magazynu przejściowego.
    - Zawsze Kopiuj wirtualne dyski twarde do jednego z precreated folderów. W przypadku kopiowania wirtualnych dysków twardych poza te foldery lub w folderze, który został utworzony, wirtualne dyski twarde są przekazywane do konta usługi Azure Storage jako stronicowe obiekty BLOB i dyski zarządzane nie.
    - Do utworzenia dysków zarządzanych można przekazać tylko stałych dysków VHD. Dynamicznych wirtualnych dysków twardych, różnicowych wirtualnych dysków twardych lub dysk VHDX pliki nie są obsługiwane.

## <a name="azure-storage-account-size-limits"></a>Limity rozmiaru konta usługi Azure storage

Poniżej przedstawiono limity rozmiaru danych, które są kopiowane do konta magazynu. Upewnij się, że dane, które zostaną przesłane odpowiada tych limitów. Aby uzyskać najbardziej aktualne informacje o tych limitach, przejdź do [obiekty docelowe skalowania magazynu obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) i [usługi Azure Files skalowanie elementów docelowych](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Rozmiar danych skopiowane do konta magazynu platformy Azure                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Obiekt blob blokowy obiekt Blob i strony                                            | 500 TB na konto magazynu. <br> Obejmuje to dane ze wszystkich źródeł, w tym dysku Data Box.|


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tych limitów.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt blob        | ~ 4.75 TiB                                                 |
| Stronicowy obiekt blob         | 8 TiB <br> (Każdy plik przekazany w formacie stronicowy obiekt Blob musi być 512 bajtów wyrównane, inne przekazywanie nie powiodło się. <br> Dysk VHD i VHDX są 512 bajtów wyrównane). |
|Azure Files        | 1 TiB <br> Maksymalnie z rozmiar udziału wynosi 5 TiB     |
| Dyski zarządzane     |4 TiB <br> Aby uzyskać więcej informacji o wielkości i ograniczeń zobacz: <li>[Wartości docelowe skalowalności dysków zarządzanych](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure blokowych obiektów blob, stronicowych obiektów blob i konwencje nazewnictwa plików

| Jednostka                                       | Konwencja                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwy kontenerów dla blokowych obiektów blob i stronicowych obiektów blob <br> Nazwy udziału plików dla usługi Azure Files | Musi być prawidłową nazwą DNS, który składa się z 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i znaki łącznika (-). <br> Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. <br> Nazwy nie mogą zawierać sąsiadujących ze sobą łączników. |
| Nazwy katalogów i plików dla usługi Azure files     |<li> Zachowywanie, bez uwzględniania wielkości liter i nie może przekraczać 255 znaków długości. </li><li> Nie może kończyć się ukośnikiem (/). </li><li>Jeśli nie dostarczono, zostaną automatycznie usunięte. </li><li> Następujące znaki są niedozwolone: <code>" \\ / : \| < > * ?</code></li><li> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li> Niedozwolone znaki ścieżki adresu URL nie są dozwolone. Kod punkty, takich jak \\uE000 nie są prawidłowymi znakami Unicode. Niektóre znaki ASCII lub Unicode, takie jak znaki kontrolne (0x00 do 0x1F \\u0081, itp.), również nie są dozwolone. Zasady dotyczące Unicode ciągów w HTTP/1.1 znajduje się dokumencie RFC 2616 2.2 sekcji: Podstawowe zasady i RFC 3987. </li><li> Następujące nazwy plików nie są dozwolone: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, znak kropki (.) i kropka dwa znaki (.).</li>|
| Nazwy blokowych i stronicowych obiektów blob      | W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. <br> Nazwa obiektu blob musi zawierać od 1 do 1024 znaków. <br> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. <br>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego. |

## <a name="managed-disk-naming-conventions"></a>Konwencje nazewnictwa dysku zarządzanego

| Jednostka | Konwencja                                             |
|-------------------|-----------------------------------------------------------|
| Nazwy dysku zarządzanego       | <li> Nazwa musi być 1 do 80 znaków. </li><li> Nazwa musi zaczynać się literą lub cyfrą, kończyć literą, cyfrą lub znakiem podkreślenia. </li><li> Nazwa może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. </li><li>   Nazwa nie powinien mieć miejsca do magazynowania lub `/`.                                              |

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [wymagania systemowe dysku Data Box](data-box-disk-system-requirements.md)
