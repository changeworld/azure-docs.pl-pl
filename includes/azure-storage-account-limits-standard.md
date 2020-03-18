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
ms.openlocfilehash: b5de45086b324006a3dafdef5c80689923650759
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79382041"
---
W poniższej tabeli opisano domyślne limity dla kont magazynu w włączoną obsługą magazynu ogólnego przeznaczenia w wersji 1, wersji 2, usługi Blob Storage, magazynu blokowych obiektów blob i usługi Data Lake Storage Gen2 na platformie Azure. Limit dotyczący *ruchu przychodzącego* odnosi się do wszystkich danych wysyłanych na konto magazynu. Limit dotyczący *ruchu wychodzącego* odnosi się do wszystkich danych odbieranych z konta magazynu.

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym kont z włączoną obsługą warstw Standardowa, Premium i Data Lake Storage Gen2.<sup>3</sup> | 250 |
| Maksymalna pojemność konta magazynu | 5 PiB <sup>1</sup>|
| Maksymalna liczba kontenerów obiektów blob, obiektów blob, udziałów plików, tabel, kolejek, jednostek lub komunikatów na konto magazynu | Bez ograniczeń |
| Maksymalna częstotliwość żądań<sup>1</sup> na konto magazynu | 20 000 żądań na sekundę |
| Maksymalna ilość ruchu przychodzącego<sup>1</sup> na konto magazynu (w regionach w USA i Europie) | 25 Gb/s |
| Maksymalna ilość ruchu przychodzącego<sup>1</sup> na konto magazynu (w regionach innych niż w USA i Europie) | 5 GB/s, jeśli włączono RA-GRS/GRS, 10 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna ilość ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 2 i kont usługi Blob Storage (we wszystkich regionach) | 50 Gb/s |
| Maksymalna ilość ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (w regionach w USA) | 20 GB/s, jeśli włączono RA-GRS/GRS, 30 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna ilość ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (w regionach innych niż w USA) | 10 GB/s, jeśli włączono RA-GRS/GRS, 15 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba reguł sieci wirtualnej na konto magazynu | 200 |
| Maksymalna liczba reguł adresów IP na konto magazynu | 200 |

<sup>1</sup> Dla kont usługi Azure Storage w warstwie Standardowa można zażądać obsługi wyższych limitów pojemności i wyższych limitów dotyczących ruchu przychodzącego. Aby poprosić o zwiększenie limitów dla kont, skontaktuj się z [działem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Jeśli konto magazynu ma włączony dostęp do odczytu z magazynem geograficznie nadmiarowym (RA-GRS) lub magazynem geograficznie i strefowo nadmiarowym (RA-GZRS), wartości docelowe ruchu wychodzącego dla lokalizacji pomocniczej są identyczne jak te dla lokalizacji głównej. Opcje [replikacji usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) obejmują:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) to zestaw funkcji przeznaczonych do analizy danych big data, oparty na usłudze Azure Blob Storage. Ograniczenia dotyczące usług Azure Storage i Blob Storage mają zastosowanie do jednostki Data Lake Storage Gen2.

> [!NOTE]
> Firma Microsoft zaleca używanie w większości scenariuszy kont magazynu ogólnego przeznaczenia w wersji 2. Konto ogólnego przeznaczenia w wersji 1 lub konto usługi Azure Blob Storage można łatwo uaktualnić do konta ogólnego przeznaczenia w wersji 2 bez przestoju czy konieczności kopiowania danych. Aby uzyskać więcej informacji, zobacz [Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2](../articles/storage/common/storage-account-upgrade.md).

Jeśli wymagania aplikacji przekraczają możliwości docelowe skalowalności pojedynczego konta magazynu, możesz utworzyć aplikację przy użyciu wielu kont magazynu. W takiej sytuacji obiekty danych można podzielić równomiernie między te konta magazynu. Informacje dotyczące cennika zbiorczego znajdują się w temacie [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Wszystkie konta magazynu działają w płaskiej topologii sieci, niezależnie od tego, kiedy zostały utworzone. Aby uzyskać więcej informacji na temat architektury sieci płaskiej i skalowalności w usłudze Azure Storage, zobacz [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency (Usługa magazynu w chmurze o wysokiej dostępności i silnej spójności)](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
