---
title: Wymagania dotyczące usługi importowania/eksportowania platformy Azure | Dokumenty firmy Microsoft
description: Zapoznaj się z wymaganiami dotyczącymi oprogramowania i sprzętu dla usługi Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255316"
---
# <a name="azure-importexport-system-requirements"></a>Wymagania systemowe usługi Azure Import/Export

W tym artykule opisano ważne wymagania dotyczące usługi Azure Import/Export. Zaleca się dokładne zapoznanie się z informacjami przed skorzystaniem z usługi Import/Eksport, a następnie odesłanie do niej w razie potrzeby podczas operacji.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Aby przygotować dyski twarde za pomocą narzędzia WAImportExport, obsługiwany jest następujący **64-bitowy system operacyjny obsługujący szyfrowanie dysków funkcją BitLocker.**


|Platforma |Wersja |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Inne wymagane oprogramowanie dla klienta systemu Windows

|Platforma |Wersja |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

Usługa Import/Eksport platformy Azure obsługuje następujące typy kont magazynu:

- Konta magazynu ogólnego przeznaczenia ogólnego przeznaczenia w wersji 2 (zalecane w większości scenariuszy)
- Konta usługi Blob Storage
- Konta magazynu ogólnego przeznaczenia w wersji 1 (zarówno wdrożenia klasycznego, jak i usługi Azure Resource Manager),

Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie kont magazynu platformy Azure](storage-account-overview.md).

Każde zadanie może służyć do przesyłania danych do lub z jednego konta magazynu. Innymi słowy pojedyncze zadanie importu/eksportu nie może obejmować wielu kont magazynu. Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [Jak utworzyć konto magazynu](storage-account-create.md).

> [!IMPORTANT]
> Usługa Eksportu importu platformy Azure nie obsługuje kont magazynu, w których włączono funkcję punktów końcowych usługi sieci [wirtualnej.](../../virtual-network/virtual-network-service-endpoints-overview.md) 

## <a name="supported-storage-types"></a>Obsługiwane typy magazynu

Poniższa lista typów magazynu jest obsługiwana za pomocą usługi Azure Import/Export.


|Zadanie  |Usługa magazynowania |Obsługiwane  |Nieobsługiwane  |
|---------|---------|---------|---------|
|Import     |  Azure Blob Storage <br><br> Azure File Storage       | Obsługiwane obiekty blob i page blob <br><br> Obsługiwane pliki          |
|Eksportowanie     |   Azure Blob Storage       | Obsługiwane obiekty blob bloków, obiekty blob strony i dołączanie obiektów blob         | Usługa Azure Files nie jest obsługiwana


## <a name="supported-hardware"></a>Obsługiwany sprzęt

W przypadku usługi Azure Import/Export potrzebne są obsługiwane dyski do kopiowania danych.

### <a name="supported-disks"></a>Obsługiwane dyski

Poniższa lista dysków jest obsługiwana do użytku z usługą Importuj/Eksportuj.


|Typ dysku  |Rozmiar  |Obsługiwane |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          |
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |

Następujące typy dysków nie są obsługiwane:
- Usbs.
- Zewnętrzny dysk twardy z wbudowaną adapterem USB.
- Dyski znajdujące się wewnątrz obudowy zewnętrznego dysku twardego.

Jedno zadanie importu/eksportu może mieć:
- Maksymalnie 10 dysków TWARDYCH/SSD.
- Połączenie dysku twardego/dysku SSD o dowolnym rozmiarze.

Duża liczba dysków może być rozłożona na wiele zadań i nie ma żadnych ograniczeń co do liczby zadań, które mogą zostać utworzone. W przypadku zadań importowania przetwarzany jest tylko pierwszy wolumin danych na dysku. Wolumin danych musi być sformatowany za pomocą systemu plików NTFS.

Podczas przygotowywania dysków twardych i kopiowania danych za pomocą narzędzia WAImportExport można użyć zewnętrznych adapterów USB. Większość gotowych adapterów USB 3.0 lub nowszych powinna działać.


## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Przesyłanie danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przykład interfejsu API REST eksportujący narzędzie Azure Export](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
