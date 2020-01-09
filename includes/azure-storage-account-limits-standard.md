---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/13/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0ecb4f5fe54e9895898156893c1e686c6cc24e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392267"
---
W poniższej tabeli opisano domyślne limity kont usługi Azure ogólnego przeznaczenia w wersji 1, v2 i BLOB Storage. Limit *ruchu* przychodzącego odnosi się do wszystkich danych z żądań wysyłanych do konta magazynu. Limit *ruchu* wychodzącego odnosi się do wszystkich danych z odpowiedzi odbieranych z konta magazynu.

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym konta Standard i Premium | 250 |
| Maksymalna pojemność konta magazynu | 2 PiB dla Stanów Zjednoczonych i Europy oraz 500 TiB dla wszystkich innych regionów (w tym w Wielkiej Brytanii)<sup>1</sup>|
| Maksymalna liczba kontenerów obiektów blob, obiektów blob, udziałów plików, tabel, kolejek, jednostek lub komunikatów na konto magazynu | Bez ograniczeń |
| Maksymalna liczba żądań<sup>1</sup> na konto magazynu | 20 000 żądań na sekundę |
| Maksymalna liczba danych wejściowych<sup>1</sup> na konto magazynu (USA, regiony Europy) | 25 GB/s |
| Maksymalna liczba danych wejściowych<sup>1</sup> na konto magazynu (regiony inne niż Stany USA i Europa) | 5 GB/s, jeśli włączono RA-GRS/GRS, 10 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba ruchu wychodzącego dla kont ogólnego przeznaczenia w wersji 2 i BLOB Storage (wszystkie regiony) | 50 GB/s |
| Maksymalna liczba ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony USA) | 20 GB/s, jeśli włączono RA-GRS/GRS, 30 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony inne niż Stany USA) | 10 GB/s, jeśli włączono RA-GRS/GRS, 15 GB/s dla LRS/ZRS<sup>2</sup> |
| Maksymalna liczba reguł sieci wirtualnej na konto magazynu | 200 |
| Maksymalna liczba reguł adresów IP na konto magazynu | 200 |

<sup>1</sup> Konta usługi Azure Storage Standard obsługują wyższe limity pojemności i wyższe limity dotyczące ruchu przychodzącego przez żądanie. Aby zażądać zwiększenia limitów kont dla ruchu przychodzącego, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/). Aby uzyskać więcej informacji, zobacz temat [ogłaszanie większych kont magazynu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)o większej skali.

<sup>2</sup> Jeśli konto magazynu ma włączony dostęp do odczytu z magazynu geograficznie nadmiarowego (RA-GRS) lub strefy geograficznej nadmiarowej (Ra-GZRS), a następnie docelowe dane wyjściowe dla lokalizacji dodatkowej są identyczne z tymi, które znajdują się w lokalizacji głównej. Opcje [replikacji usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) obejmują:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> Firma Microsoft zaleca użycie konta magazynu ogólnego przeznaczenia w wersji 2 dla większości scenariuszy. Możesz łatwo uaktualnić do konta ogólnego przeznaczenia w wersji 1 lub Azure Blob Storage, aby nie było przestoju i bez konieczności kopiowania danych. Aby uzyskać więcej informacji, zobacz [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](../articles/storage/common/storage-account-upgrade.md).

Jeśli wymagania aplikacji przekraczają tarcze skalowalności pojedynczego konta magazynu, możesz skompilować aplikację, aby używać wielu kont magazynu. Następnie można podzielić na partycje obiekty danych na kontach magazynu. Aby uzyskać informacje na temat cen zbiorczych, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Wszystkie konta magazynu są uruchamiane na płaskiej topologii sieci i obsługują cele skalowalności i wydajności opisane w tym artykule, niezależnie od tego, kiedy zostały utworzone. Aby uzyskać więcej informacji na temat architektury sieci płaskiej i skalowalności usługi Azure Storage, zobacz [Microsoft Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
