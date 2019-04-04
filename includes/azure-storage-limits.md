---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: dccc967e7899d8cc9c4bde6829d6fc44f9cba4e0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891042"
---
W poniższej tabeli opisano domyślne limity dla usługi Azure Storage. *Ruch przychodzący* limit odnosi się do wszystkich danych z żądań, które są wysyłane do konta magazynu. *Ruch wychodzący* limit odnosi się do wszystkich danych z odpowiedzi, które są odbierane z konta magazynu.

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym kont w warstwach standardowa i premium | 250 |
| Maksymalna pojemność konta | 2 PB dla Stanów Zjednoczonych i Europie, 500 TB dla wszystkich innych regionów, w tym Wielkiej Brytanii |
| Maksymalna liczba kontenerów obiektów blob, obiekty BLOB, udziały plików, tabel, kolejek, jednostki lub wiadomości na koncie magazynu | Bez ograniczeń |
| Maksymalna liczba żądań<sup>1</sup> na konto magazynu | 20 000 żądań na sekundę |
| Maksymalna ruch przychodzący<sup>1</sup> na konto magazynu (regionami w Stanach Zjednoczonych) | 10 GB/s Jeśli Magazyn RA-GRS/GRS jest włączona, 20 GB/s dla magazynu LRS/ZRS<sup>2</sup> |
| Maksymalna ruch przychodzący<sup>1</sup> na konto magazynu (regiony poza USA) | 5 GB/s Jeśli Magazyn RA-GRS/GRS jest włączona, 10 GB/s dla magazynu LRS/ZRS<sup>2</sup> |
| Maksymalnego wychodzącego ogólnego przeznaczenia v2 i konta usługi Blob storage (wszystkich regionów) | 50 GB/s |
| Maksymalna ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (regionami w Stanach Zjednoczonych) | 20 GB/s Jeśli Magazyn RA-GRS/GRS jest włączona, 30 GB/s dla magazynu LRS/ZRS<sup>2</sup> |
| Maksymalna ruchu wychodzącego dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony poza USA) | 10 GB/s Jeśli Magazyn RA-GRS/GRS jest włączona, 15 GB/s dla magazynu LRS/ZRS<sup>2</sup> |

<sup>1</sup>kont usługi azure Standard Storage obsługuje wyższe limity dla ruchu przychodzącego przez żądanie. Aby poprosić o zwiększenie limitów konta dla danych przychodzących, skontaktuj się z [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [replikacja usługi azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) są następujące opcje:
* **RA-GRS**: Magazyn geograficznie nadmiarowy z dostępem do odczytu. Jeśli Magazyn RA-GRS jest włączona, ruch wychodzący związanych z lokalizacji dodatkowej są identyczne z tymi dla lokalizacji głównej.
* **GRS**: Magazyn geograficznie nadmiarowy. 
* **ZRS**: Magazyn strefowo nadmiarowy.
* **LRS**: Magazyn lokalnie nadmiarowy. 

> [!NOTE]
> Zalecamy użycie konta magazynu ogólnego przeznaczenia w wersji 2 w przypadku większości scenariuszy. Ogólnego przeznaczenia w wersji 1 lub konta usługi Azure Blob storage można łatwo uaktualnić do konta ogólnego przeznaczenia w wersji 2, bez przestojów i bez potrzeby kopiowania danych.
>
> Aby uzyskać więcej informacji na temat kont usługi Azure Storage, zobacz [omówienia kont magazynu](../articles/storage/common/storage-account-overview.md). 

Wymagania aplikacji przekroczy cele skalowalności z jednego konta magazynu, możesz tworzyć aplikacji do użycia wielu kont magazynu. Następnie można podzielić obiekty danych na tych kontach magazynu. Aby uzyskać informacje dotyczące cennika woluminów, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Wszystkie konta magazynu na topologii siecią płaską a obsługuje cele dotyczące skalowalności i wydajności, opisane w tym artykule, niezależnie od tego, w przypadku ich utworzenia. Aby uzyskać więcej informacji na architekturę siecią płaską usługi Azure Storage i skalowalność, zobacz [usługi Microsoft Azure Storage: Usługi magazynu w chmurze o wysokiej dostępności przy użyciu silnej spójności](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

