---
title: Skrypt programu PowerShell Azure przykładowe — Oblicz łączny rozmiar rozliczeń kontenera obiektów blob | Dokumentacja firmy Microsoft
description: Oblicz łączny rozmiar kontenera w magazynie obiektów Blob platformy Azure na potrzeby rozliczeń.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: c37b416578a76e9b12e29d68e413d851796ccc6f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
ms.locfileid: "26368548"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Oblicz łączny rozmiar rozliczeń kontenera obiektów blob

Ten skrypt oblicza rozmiar kontenera w magazynie obiektów Blob platformy Azure na potrzeby Szacowanie kosztów rozliczeń. Skrypt sumy rozmiar obiektów blob w kontenerze.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Ten skrypt programu PowerShell oblicza rozmiar kontenera do celów rozliczeń. Jeśli są obliczania rozmiaru kontenera do innych celów, zobacz [Oblicz łączny rozmiar kontenera magazynu obiektów Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) dla prostsze skrypt, który udostępnia szacowania.

## <a name="determine-the-size-of-the-blob-container"></a>Określanie rozmiaru kontenera obiektów blob

Całkowity rozmiar kontenera obiektów blob obejmuje rozmiar samego kontenera i rozmiar wszystkich obiektów blob w kontenerze.

Następujące sekcje w tym artykule opisano obliczania pojemności dla obiekt blob kontenerów i obiektów blob. W poniższej sekcji Len(X) oznacza liczbę znaków w ciągu.

### <a name="blob-containers"></a>Kontenerów obiektów blob

Następujące obliczenia opisano sposób oszacować ilość miejsca w magazynie, który jest używany na kontenera obiektów blob:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Podział jest następujący:
* 48 bajtów koszty dla każdego kontenera obejmuje czasu ostatniej modyfikacji, uprawnienia ustawienia publicznego i niektóre metadane systemu.

* Nazwa kontenera jest przechowywane w formacie Unicode, więc wykonaj liczbę znaków i pomnożyć przez dwa.

* Dla każdego bloku metadanych kontenera obiektów blob, który jest przechowywany przechowujemy długość nazwy (ASCII) oraz długość wartości ciągu.

* 512 bajtów na identyfikator podpisane między innymi nazwą podpisanych identyfikatora czas rozpoczęcia, czas wygaśnięcia i uprawnienia.

### <a name="blobs"></a>Obiekty blob

Poniższe obliczenia przedstawiają sposób oszacować ilość miejsca w magazynie zużytych według obiektu blob.

* Obiekt blob blokowy (podstawowy obiektów blob lub migawki):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* Stronicowych obiektów blob (podstawowy obiektów blob lub migawki):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

Podział jest następujący:

* 124 bajtów koszty dla obiekt blob, który zawiera:
    - Godzina ostatniej modyfikacji
    - Rozmiar
    - Cache-Control
    - Content-Type
    - Język zawartości
    - Kodowanie zawartości
    - Content-MD5
    - Uprawnienia
    - Informacje o migawki
    - Dzierżawy
    - Niektóre metadane systemu

* Nazwa obiektu blob jest przechowywane w formacie Unicode, więc wykonaj liczbę znaków i pomnożyć przez dwa.

* Dla każdego bloku metadanych, które są przechowywane należy dodać długość nazwy (przechowywany w formacie ASCII), oraz długość wartości ciągu.

* Dla blokowych obiektów blob:
    * 8 bajtów zablokowanych.
    * Liczba bloków czasu identyfikator bloku rozmiar w bajtach.
    * Rozmiar danych we wszystkich blokach zatwierdzonej i niezatwierdzone. 
    
    >[!NOTE]
    >Gdy migawki są używane, ten rozmiar zawiera tylko unikatowe dane dla tego obiektu blob podstawowej lub migawki. Niezatwierdzone bloki nie są używane po upływie tygodnia, są zbierane z pamięci. Po wykonaniu tej nie są wliczane do rozliczeń.

* Stronicowych obiektów blob:
    * Liczba zakresów niesąsiadujące strony z danymi czasu 12 bajtów. Jest to liczba zakresów unikatową stronę podczas wywoływania **GetPageRanges** interfejsu API.

    * Rozmiar danych w bajtach wszystkich przechowywanych stron. 
    
    >[!NOTE]
    >Gdy migawki są używane, ten rozmiar zawiera tylko unikatowe strony podstawowej obiektów blob lub migawki obiektu blob, który inwentaryzacji.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Następne kroki

- Zobacz [Oblicz łączny rozmiar kontenera magazynu obiektów Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) dla prostego skrypt, który zawiera oszacowanie rozmiaru kontenera.

- Aby uzyskać więcej informacji dotyczących rozliczeń usługi Azure Storage, zobacz [opis systemu Windows Azure magazynu rozliczeń](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentacji programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1).

- Można znaleźć dodatkowe przykłady skryptów PowerShell magazynu w [przykłady środowiska PowerShell dla usługi Azure Storage](../blobs/storage-samples-blobs-powershell.md).
