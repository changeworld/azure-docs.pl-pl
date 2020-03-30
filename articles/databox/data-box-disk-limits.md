---
title: Limity dysków usługi Azure Data Box | Dokumenty firmy Microsoft
description: W tym artykule opisano limity systemowe i zalecane rozmiary dysku microsoft azure data box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 1bb8300f1e54cf03563704cf00549ce9e09a3916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260165"
---
# <a name="azure-data-box-disk-limits"></a>Limity dysków pola danych platformy Azure


Należy wziąć pod uwagę te limity podczas wdrażania i obsługi rozwiązania Microsoft Azure Data Box Disk.

## <a name="data-box-service-limits"></a>Limity usług Data Box

 - Usługa Data Box jest dostępna w regionach platformy Azure wymienionych w [obszarze Dostępność regionu](data-box-disk-overview.md#region-availability).
 - Jedno konto magazynu jest obsługiwane za pomocą dysku pola danych.

## <a name="data-box-disk-performance"></a>Wydajność dysku pola danych

Podczas testowania dysków podłączonych za pomocą portu USB 3.0 ich wydajność doszła do poziomu 430 MB/s. Rzeczywista wartość różni się w zależności od rozmiaru pliku. Przy mniejszych plikach wydajność może być niższa.

## <a name="azure-storage-limits"></a>Limity magazynu platformy Azure

W tej sekcji opisano limity dla usługi Azure Storage i wymagane konwencje nazewnictwa dla plików Azure, bloków obiektów blob platformy Azure i obiektów blob strony platformy Azure, zgodnie z zastosowaniem usługi Data Box. Dokładnie przejrzyj limity magazynowania i postępuj zgodnie ze wszystkimi zaleceniami.

Aby uzyskać najnowsze informacje na temat limitów usług magazynu platformy Azure i najlepszych rozwiązań dotyczących nazewnictwa udziałów, kontenerów i plików, przejdź do:

- [Nazywanie i odwoływanie się do kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nazywanie i przywoływanie udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Konwencje bloków obiektów blob i stronicowych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Jeśli istnieją jakiekolwiek pliki lub katalogi, które przekraczają limity usługi Usługi Azure Storage lub nie są zgodne z konwencjami nazewnictwa plików azure/obiektów blob, te pliki lub katalogi nie są wychwytywały do usługi Azure Storage za pośrednictwem usługi Data Box.

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

- Nie należy kopiować danych bezpośrednio na dyski. Kopiowanie danych do wstępnie utworzonych folderów *BlockBlob*,*PageBlob*i *AzureFile.*
- Folder pod *BlockBlob* i *PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *BlockBlob/container* i *PageBlob/container*.
- Jeśli masz istniejący obiekt platformy Azure (na przykład obiekt blob) w chmurze o takiej samej nazwie jak obiekt, który jest kopiowany, Data Box Disk zmieni nazwę pliku jako plik(1) w chmurze.
- Każdy plik zapisany w *udziałach BlockBlob* i *PageBlob* jest przekazywane jako blokowy obiekt blob i obiekt blob strony.
- Żadna pusta hierarchia katalogów (bez plików) utworzona w folderach *BlockBlob* i *PageBlob* nie jest przekazywała.
- Jeśli występują błędy podczas przekazywania danych na platformę Azure, dziennik błędów jest tworzony na koncie magazynu docelowego. Ścieżka do tego dziennika błędów jest dostępna w portalu po zakończeniu przekazywania i można przejrzeć dziennik, aby podjąć działania naprawcze. Nie należy usuwać danych ze źródła bez weryfikacji przesłanych danych.
- Metadane plików i uprawnienia NTFS nie są zachowywane, gdy dane są przekazywane do usługi Azure Files. Na przykład atrybut *Ostatniej modyfikacji* plików nie będzie przechowywany podczas kopiowania danych.
- Jeśli określono dyski zarządzane w kolejności, przejrzyj następujące dodatkowe zagadnienia:

    - Można mieć tylko jeden dysk zarządzany o danej nazwie w grupie zasobów we wszystkich wstępnie utworzonych folderach i na wszystkich urządzeniach Data Box Disk. Oznacza to, że wirtualne dyski twarde przekazane do wstępnie utworzonych folderów powinny mieć unikatowe nazwy. Upewnij się, że dana nazwa nie jest zgodna z nazwą już istniejącego dysku zarządzanego w grupie zasobów. Jeśli wirtualne dyski twarde mają takie same nazwy, tylko jeden z nich zostanie przekonwertowany na dysk zarządzany o tej nazwie. Pozostałe wirtualne dyski twarde zostaną przekazane do przejściowego konta magazynu jako stronicowe obiekty blob.
    - Zawsze kopiuj wirtualne dyski twarde do jednego ze wstępnie utworzonych folderów. Jeśli skopiujesz dyski VHD do lokalizacji innej niż te foldery lub do folderu utworzonego przez siebie, wirtualne dyski twarde zostaną przekazane do konta usługi Azure Storage jako stronicowe obiekty blob i dyski niezarządzane.
    - Na potrzeby tworzenia dysków zarządzanych można przekazywać tylko stałe wirtualne dyski twarde. Dynamiczne dyski VHD, różnicowe dyski VHD i pliki VHDX nie są obsługiwane.

## <a name="azure-storage-account-size-limits"></a>Limity rozmiaru konta magazynu platformy Azure

Oto limity rozmiaru danych, które są kopiowane do konta magazynu. Upewnij się, że przesyłane dane są zgodne z tymi limitami. Aby uzyskać najbardziej aktualne informacje na temat tych limitów, przejdź do [obiektów docelowych skalowania obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage) i obiektów [docelowych skalowania usług Azure Files.](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account#scale-targets-for-standard-storage-accounts)

| Rozmiar danych skopiowanych do konta magazynu platformy Azure                      | Limit domyślny          |
|---------------------------------------------------------------------|------------------------|
| Blokuj obiekt blob i obiekt blob strony                                            | 500 TB na konto magazynu. <br> Obejmuje to dane ze wszystkich źródeł, w tym data box disk.|


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektu platformy Azure

Oto rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tymi limitami.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blok blob        | ~ 4,75 TiB                                                 |
| Obiekt blob strony         | 8 TiB <br> (Każdy plik przekazany w formacie stronicowego obiektu blob musi być wyrównany do 512 bajtów, w przeciwnym razie przekazywanie nie powiedzie się. <br> Zarówno VHD, jak i VHDX są wyrównane o 512 bajtów.) |
|Azure Files        | 1 TiB <br> Maksymalnie z wielkość udziału wynosi 5 TiB     |
| Dyski zarządzane     |4 TiB <br> Aby uzyskać więcej informacji na temat rozmiaru i limitów, zobacz: <li>[Cele skalowalności dla dysków zarządzanych](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Konwencje dotyczące bloków bloku platformy Azure, obiektów blob stron i nazewnictwa plików

| Jednostka                                       | Konwencja                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwy kontenerów dla bloków blob i stronicowych obiektów blob <br> Nazwy udziałów plików dla plików platformy Azure | Musi być prawidłowa nazwa DNS, która ma długości od 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i łącznik (-). <br> Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. <br> Nazwy nie mogą zawierać sąsiadujących ze sobą łączników. |
| Nazwy katalogów i plików dla plików platformy Azure     |<li> Zachowanie wielkości liter, bez uwzględniania wielkości liter i nie może przekraczać 255 znaków długości. </li><li> Nie można zakończyć ukośnikiem do przodu (/). </li><li>Jeśli zostanie podana, zostanie ona automatycznie usunięta. </li><li> Następujące znaki nie są dozwolone:<code>" \\ / : \| < > * ?</code></li><li> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li> Nielegalne znaki ścieżki adresu URL nie są dozwolone. Punkty kodu, takie jak \\uE000, nie są prawidłowymi znakami Unicode. Niektóre znaki ASCII lub Unicode, takie jak znaki kontrolne (od \\0x00 do 0x1F, u0081 itp.), również nie są dozwolone. Reguły dotyczące ciągów Unicode w http/1.1 zobacz RFC 2616, Sekcja 2.2: Podstawowe reguły i RFC 3987. </li><li> Następujące nazwy plików nie są dozwolone: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, znak kropki (.) i dwa znaki kropki (..).</li>|
| Nazwy blokowych i stronicowych obiektów blob      | W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. <br> Nazwa obiektu blob musi zawierać od 1 do 1024 znaków. <br> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. <br>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego. |

## <a name="managed-disk-naming-conventions"></a>Konwencje nazewnictwa dysków zarządzanych

| Jednostka | Konwencja                                             |
|-------------------|-----------------------------------------------------------|
| Nazwy dysków zarządzanych       | <li> Nazwa musi mieć od 1 do 80 znaków. </li><li> Nazwa musi zaczynać się od litery lub cyfry, kończyć literą, cyfrą lub podkreśleniem. </li><li> Nazwa może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. </li><li>   Nazwa nie powinna mieć `/`spacji lub .                                              |

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wymagania systemowe dysku pola danych](data-box-disk-system-requirements.md)
