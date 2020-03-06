---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/27/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e29cdd56d1c43b3d0e8fc6ca233ac19d8b0004ff
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357435"
---
W poniższej tabeli opisano domyślne limity dla usługi Azure ogólnego przeznaczenia w wersji 1 (GPv1), v2 (GPv2), BLOB, Premium BlockBlob i Data Lake Gen2 włączonych kont magazynu. Limit *ruchu* przychodzącego odnosi się do wszystkich danych wysyłanych do konta magazynu. Limit *ruchu* wychodzącego odnosi się do wszystkich danych odebranych z konta magazynu.

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym kont Standard, Premium i Data Lake Gen2<sup>3</sup> | 250 |
| Maksymalna pojemność konta magazynu | 5 PiB <sup>1</sup>|
| Maksymalna liczba kontenerów obiektów blob, obiektów blob, udziałów plików, tabel, kolejek, jednostek lub komunikatów na konto magazynu | Bez ograniczeń |
| Maksymalna liczba żądań<sup>1</sup> na konto magazynu | 20 000 żądań na sekundę |
| Maksymalna liczba danych wejściowych<sup>1</sup> na konto magazynu (USA, regiony Europy) | 25 GB/s |
| Maksymalna liczba danych wejściowych<sup>1</sup> na konto magazynu (regiony inne niż Stany USA i Europa) | 5 GB/s, jeśli włączono RA-GRS/GRS, 10 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba ruchu wychodzącego dla kont ogólnego przeznaczenia w wersji 2 i BLOB Storage (wszystkie regiony) | 50 GB/s |
| Maksymalna liczba ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony USA) | 20 GB/s, jeśli włączono RA-GRS/GRS, 30 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony inne niż Stany USA) | 10 GB/s, jeśli włączono RA-GRS/GRS, 15 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba reguł sieci wirtualnej na konto magazynu | 200 |
| Maksymalna liczba reguł adresów IP na konto magazynu | 200 |

<sup>1</sup> standardowe konta usługi Azure Storage obsługują wyższe limity pojemności i wyższe limity dotyczące ruchu przychodzącego przez żądanie. Aby zażądać zwiększenia limitów kont, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Jeśli konto magazynu ma włączony dostęp do odczytu z magazynu geograficznie nadmiarowego (RA-GRS) lub strefy geograficznej nadmiarowej (Ra-GZRS), a następnie docelowe dane wyjściowe dla lokalizacji dodatkowej są identyczne z tymi, które znajdują się w lokalizacji głównej. Opcje [replikacji usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) obejmują:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) to zestaw funkcji przeznaczonych do analizy danych Big Data, opartych na usłudze Azure Blob Storage. Ograniczenia dotyczące usługi Azure Storage i magazynu obiektów BLOB są stosowane w usłudze Data Lake Gen2.

> [!NOTE]
> Firma Microsoft zaleca użycie konta magazynu ogólnego przeznaczenia w wersji 2 dla większości scenariuszy. Możesz łatwo uaktualnić do konta ogólnego przeznaczenia w wersji 1 lub Azure Blob Storage, aby nie było przestoju i bez konieczności kopiowania danych. Aby uzyskać więcej informacji, zobacz [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](../articles/storage/common/storage-account-upgrade.md).

Jeśli wymagania aplikacji przekraczają tarcze skalowalności pojedynczego konta magazynu, możesz skompilować aplikację, aby używać wielu kont magazynu. Następnie można podzielić na partycje obiekty danych na kontach magazynu. Aby uzyskać informacje na temat cen zbiorczych, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Wszystkie konta magazynu są uruchamiane na płaskiej topologii sieci, niezależnie od tego, kiedy zostały utworzone. Aby uzyskać więcej informacji na temat architektury sieci płaskiej i skalowalności usługi Azure Storage, zobacz [Microsoft Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx). W przypadku [konta usługi Data Lake Gen2 można włączyć hierarchiczną przestrzeń](../articles/storage/blobs/data-lake-storage-namespace.md) nazw, a nie tylko do płaskiej przestrzeni nazw, aby uzyskać dostęp do wieloprotokołowego dostępu. Proste i hierarchiczne konta magazynu przestrzeni nazw obsługują te same cele skalowalności i wydajności opisane w tym artykule.
