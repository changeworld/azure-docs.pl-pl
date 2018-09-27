---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396131"
---
W poniższej tabeli opisano domyślne limity dla usługi Azure Storage. *Ruch przychodzący* limit odnosi się do wszystkich danych (żądań) są wysyłane do konta magazynu. *Ruch wychodzący* limit odnosi się do wszystkich danych (żądań) wysyłanych z konta magazynu.

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym kont w warstwach standardowa i premium | 200 |
| Maksymalna pojemność konta magazynu<sup>1</sup> | 500 TiB |
| Maksymalna liczba kontenerów obiektów blob, obiekty BLOB, udziały plików, tabel, kolejek, jednostki lub wiadomości na koncie magazynu | Bez ograniczeń |
| Maksymalna liczba żądań<sup>1</sup> na konto magazynu | 20 000 żądań na sekundę |
| Maksymalna liczba przychodzących<sup>1</sup> na konto magazynu (nam regiony) | 10 GB/s włączenie RA-GRS/GRS, 20 GB/s dla magazynu LRS/ZRS<sup>2</sup> |
| Maksymalna liczba wychodzących<sup>1</sup> na konto magazynu (nam regiony) | 20 GB/s włączenie RA-GRS/GRS, 30 GB/s dla magazynu LRS/ZRS |
| Maksymalna liczba przychodzących<sup>1</sup> na konto magazynu (regiony Non-US) | 5 GB/s włączenie RA-GRS/GRS, 10 GB/s dla magazynu LRS/ZRS<sup>2</sup> |
| Maksymalna liczba wychodzących<sup>1</sup> na konto magazynu (regiony Non-US) | 10 GB/s włączenie RA-GRS/GRS, 15 GB/s dla magazynu LRS/ZRS |

<sup>1</sup> konta usługi azure storage obsługę wyższych limitów pojemności, liczba żądań, przychodzący i wychodzący przez żądanie. Aby uzyskać więcej informacji na temat zwiększenia limitów zobacz [ogłoszenie kont magazynu większych i wyższej skali](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/). Aby poprosić o zwiększenie limitów konta, skontaktuj się z [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>[replikacja usługi azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) są następujące opcje:
* **RA-GRS**: Magazyn geograficznie nadmiarowy z dostępem do odczytu. Jeśli Magazyn RA-GRS jest włączona, ruch wychodzący związanych z lokalizacji dodatkowej są identyczne z tymi dla lokalizacji głównej.
* **GRS**: Magazyn geograficznie nadmiarowy. 
* **Magazyn ZRS**: Magazyn strefowo nadmiarowy.
* **LRS**: Magazyn lokalnie nadmiarowy. 

