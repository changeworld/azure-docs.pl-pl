---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334951"
---
W poniższej tabeli opisano domyślne limity dla kont magazynu ogólnego przeznaczenia platformy Azure w wersji 1, 2, magazynu obiektów Blob, magazynu obiektów blob blokowego i kont magazynu usługi Data Lake Gen2. Limit *transferu danych przychodzących* odnosi się do wszystkich danych, które są wysyłane do konta magazynu. Limit *ruchu wychodzącego* odnosi się do wszystkich danych, które są odbierane z konta magazynu.

| Zasób | Limit |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym konta magazynu standardowego, premium i usługi Data Lake Storage Gen2. <sup>3</sup> | 250 |
| Maksymalna pojemność konta magazynu | 5 PiB <sup>1</sup>|
| Maksymalna liczba kontenerów obiektów blob, obiektów blob, udziałów plików, tabel, kolejek, encji lub wiadomości na konto magazynu | Bez ograniczeń |
| Maksymalna stawka żądania<sup>1</sup> na konto magazynu | 20 000 żądań na sekundę |
| Maksymalny przystawek<sup>1</sup> na konto magazynu (stany USA, regiony Europy) | 25 Gb/s |
| Maksymalny przychodzących<sup>1</sup> na konto magazynu (regiony inne niż USA i Europa) | 5 Gb/s, jeśli jest włączona funkcja RA-GRS/GRS, 10 Gb/s dla LRS/ZRS<sup>2</sup> |
| Maksymalny ruch wychodzący dla kont magazynu ogólnego przeznaczenia w wersji 2 i obiektów Blob (wszystkie regiony) | 50 Gb/s |
| Maksymalny ruch wychodzący dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony STANÓW Zjednoczonych) | 20 Gb/s, jeśli jest włączona funkcja RA-GRS/GRS, 30 Gb/s dla LRS/ZRS<sup>2</sup> |
| Maksymalny ruch wychodzący dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony inne niż stany USA) | 10 Gb/s, jeśli jest włączona funkcja RA-GRS/GRS, 15 Gb/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba reguł sieci wirtualnej na konto magazynu | 200 |
| Maksymalna liczba reguł adresu IP na konto magazynu | 200 |

<sup>1</sup> Standardowe konta usługi Azure Storage obsługują wyższe limity pojemności i wyższe limity dla transferu danych przychodzących według żądania. Aby zażądać zwiększenia limitów kont, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Jeśli twoje konto magazynu ma włączoną funkcję dostępu do odczytu z magazynem geograficznym (RA-GRS) lub magazynem nadmiarowym strefy geograficznej (RA-GZRS), obiekty docelowe wyjścia dla lokalizacji dodatkowej są identyczne z lokalizacjami podstawowymi. Opcje [replikacji usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) obejmują:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Usługa Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) to zestaw funkcji dedykowanych do analizy dużych zbiorów danych, opartej na magazynie obiektów Blob platformy Azure. Ograniczenia usługi Azure Storage i magazynu obiektów blob mają zastosowanie do usługi Data Lake Storage Gen2.

> [!NOTE]
> Firma Microsoft zaleca używanie konta magazynu ogólnego przeznaczenia w wersji 2 dla większości scenariuszy. Można łatwo uaktualnić ogólne przeznaczenie v1 lub konto magazynu obiektów Blob platformy Azure do ogólnego przeznaczenia konta w wersji 2 bez przestojów i bez konieczności kopiowania danych. Aby uzyskać więcej informacji, zobacz [Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](../articles/storage/common/storage-account-upgrade.md).

Jeśli potrzeby aplikacji przekraczają cele skalowalności pojedynczego konta magazynu, można utworzyć aplikację do używania wielu kont magazynu. Następnie można podzielić obiekty danych na te konta magazynu. Aby uzyskać informacje na temat cen woluminów, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Wszystkie konta magazynu są uruchamiane w płaskiej topologii sieci, niezależnie od tego, kiedy zostały utworzone. Aby uzyskać więcej informacji na temat architektury sieci płaskiej usługi Azure Storage i skalowalności, zobacz [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
