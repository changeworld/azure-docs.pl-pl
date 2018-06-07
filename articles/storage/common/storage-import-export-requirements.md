---
title: Wymagania dotyczące usługi Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Zrozumienie wymagań sprzętu i oprogramowania dla usługi Import/Eksport Azure.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2018
ms.author: alkohli
ms.openlocfilehash: 4c6e22f50f4550cb4a6e25960bcc13a4d92e9819
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825071"
---
# <a name="azure-importexport-system-requirements"></a>Wymagania systemowe Import/Eksport Azure

W tym artykule opisano ważnych wymagań usługi Import/Eksport Azure. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed korzystania z usługi Import/Eksport i następnie odwołują się do niego w razie potrzeby podczas operacji.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Aby przygotować dyski twarde, za pomocą narzędzia WAImportExport, następujące **64-bitowego systemu operacyjnego, który obsługuje szyfrowanie dysków funkcją BitLocker** są obsługiwane.


|Platforma |Wersja |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8.1 Pro, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |



## <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

Usługa Import/Eksport Azure obsługuje następujące konta magazynu platformy Azure.
- Wdrożenie klasyczne
- Konta usługi Blob Storage
- Konta magazynu v1 ogólnego przeznaczenia. 

Każde zadanie może służyć do transferu danych do lub z tylko jedno konto magazynu. Innymi słowy zadanie pojedynczego importu/eksportu nie może obejmować wielu wielu kont magazynu. Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Usługa Azure Importuj Eksportuj nie obsługuje kont magazynu gdzie [punktów końcowych usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md) funkcja została włączona. 

## <a name="supported-storage-types"></a>Typy obsługiwane magazynu

Poniższa lista typów magazynu jest obsługiwana przy użyciu usługi Import/Eksport Azure.


|Zadanie  |Magazyn  |Obsługiwane  |Nieobsługiwane  |
|---------|---------|---------|---------|
|Import     |  Magazyn obiektów Blob Azure. <br>Blokowe, stronicowe obiekty BLOB obsługiwane. <br> Obsługiwane pliki Azure.       |         |
|Eksportowanie     |   Magazyn obiektów Blob Azure. <br>Blokowe obiekty BLOB, stronicowe obiekty BLOB i obiektami blob Append są obsługiwane.       | Usługa pliki Azure nie jest obsługiwane.        |


## <a name="supported-hardware"></a>Obsługiwanego sprzętu 

Dla usługi Import/Eksport Azure potrzeba obsługiwanych dysków i obsługiwane łączniki SATA, aby skopiować dane.

### <a name="supported-disks"></a>Obsługiwane dysków

Poniższa lista dysków jest obsługiwane do użycia z usługą importu i eksportu.


|Typ dysku  |Rozmiar  |Obsługiwane |Nieobsługiwane  |
|---------|---------|---------|---------|
|SSD    |   2,5-calowe      |         |         |
|HDD     |  2,5-calowe<br>3,5"       |SATA II SATA III         |Zewnętrzny dysk twardy z wbudowanych adaptera USB <br> Dysk wewnątrz wielkość liter w wyrazie zewnętrzny dysk twardy         |


Zadanie pojedynczego importu/eksportu może mieć:
- Maksymalnie 10 HDD/SSD.
- Kombinacja HDD/SSD o dowolnym rozmiarze.

Duża liczba dysków można było ich rozmieszczenie wielu zadań i nie ma żadnych limitów liczby zadań, które mogą zostać utworzone. 

Dla zadania importu są przetwarzane tylko pierwszy ilość danych na dysku. Ilość danych musi być sformatowany jako NTFS.

### <a name="supported-external-usb-adaptors"></a>Obsługiwane zewnętrznej karty USB

Podczas przygotowywania dysków twardych i kopiowanie danych za pomocą narzędzia WAImportExport, można użyć następującego zewnętrznej karty USB (poza shelp): 
- Anker 68UPSATAA 02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Orico 6628SUS3-C-BK (6628 Series)
- Thermaltake BlacX gorąca wymiany SATA zewnętrznych twardych dysków stacji dokującej (USB 2.0 & eSATA)


## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przykładowe interfejsu API REST wyeksportować importu platformy Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

