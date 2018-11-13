---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/23/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84333b26ac70db4b400f7236d4255f4b57a6ca7d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572524"
---
W poniższej tabeli opisano domyślne limity dla usługi Azure Storage. *Ruch przychodzący* limit odnosi się do wszystkich danych (żądań) są wysyłane do konta magazynu. *Ruch wychodzący* limit odnosi się do wszystkich danych (żądań) wysyłanych z konta magazynu.

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym kont w warstwach standardowa i premium | 250 |
| Maksymalna pojemność konta magazynu | 2 PB dla Stanów Zjednoczonych i Europie, 500 TB dla wszystkich innych regionach, w tym Zjednoczone Królestwo |
| Maksymalna liczba kontenerów obiektów blob, obiekty BLOB, udziały plików, tabel, kolejek, jednostki lub wiadomości na koncie magazynu | Bez ograniczeń |
| Maksymalna liczba żądań<sup>1</sup> na konto magazynu | 20 000 żądań na sekundę |
| Maksymalna liczba przychodzących<sup>1</sup> na konto magazynu (nam regiony) | 10 GB/s włączenie RA-GRS/GRS, 20 GB/s dla magazynu LRS/ZRS<sup>2</sup> |
| Maksymalna liczba przychodzących<sup>1</sup> na konto magazynu (regiony Non-US) | 5 GB/s włączenie RA-GRS/GRS, 10 GB/s dla magazynu LRS/ZRS<sup>2</sup> |
| Maksymalna liczba wychodzącego ogólnego przeznaczenia v2 i konta usługi Blob storage (wszystkich regionów) | 50 GB/s |
| Maksymalna liczba wychodzących dla kont magazynu ogólnego przeznaczenia w wersji 1 (regionów na terenie) | 20 GB/s włączenie RA-GRS/GRS, 30 GB/s dla magazynu LRS/ZRS <sup>2</sup> |
| Maksymalna liczba wychodzących dla kont magazynu ogólnego przeznaczenia w wersji 1 (regiony Non-US) | 10 GB/s włączenie RA-GRS/GRS, 15 GB/s dla magazynu LRS/ZRS <sup>2</sup> |

<sup>1</sup> konta usługi azure storage obsługuje wyższe limity dla ruchu przychodzącego przez żądanie. Aby poprosić o zwiększenie limitów konta dla danych przychodzących, skontaktuj się z [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>[replikacja usługi azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) są następujące opcje:
* **RA-GRS**: Magazyn geograficznie nadmiarowy z dostępem do odczytu. Jeśli Magazyn RA-GRS jest włączona, ruch wychodzący związanych z lokalizacji dodatkowej są identyczne z tymi dla lokalizacji głównej.
* **GRS**: Magazyn geograficznie nadmiarowy. 
* **Magazyn ZRS**: Magazyn strefowo nadmiarowy.
* **LRS**: Magazyn lokalnie nadmiarowy. 

Wymagania aplikacji przekroczy cele skalowalności z jednego konta magazynu, możesz tworzyć aplikacji do użycia wielu kont magazynu. Następnie można podzielić obiekty danych na tych kontach magazynu. Zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) Aby uzyskać informacje dotyczące cennika woluminów.

Wszystkie konta magazynu na topologii siecią płaską a obsługuje cele dotyczące skalowalności i wydajności, opisane w tym artykule, niezależnie od tego, w przypadku ich utworzenia. Aby uzyskać więcej informacji na architekturę siecią płaską usługi Azure Storage i skalowalność, zobacz [usługi Microsoft Azure Storage: A o wysokiej dostępności usługi magazynu w chmurze with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

