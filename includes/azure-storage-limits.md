---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/13/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fb349c9ea2178d84e367e763797d5d93d3ae4c53
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46299136"
---
W poniższej tabeli opisano domyślne limity dla usługi Azure Storage. *Ruch przychodzący* limit odnosi się do wszystkich danych (żądań) są wysyłane do konta magazynu. *Ruch wychodzący* limit odnosi się do wszystkich danych (żądań) wysyłanych z konta magazynu.

| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję, w tym kont w warstwach standardowa i premium | 200 |
| Maksymalna pojemność konta magazynu | 500 TiB |
| Maksymalna liczba kontenerów obiektów blob, obiekty BLOB, udziały plików, tabel, kolejek, jednostki lub wiadomości na koncie magazynu | Bez ograniczeń |
| Maksymalna liczba żądań na konto magazynu | 20 000 żądań na sekundę |
| Maksymalna liczba transferu danych przychodzących na konto magazynu (nam regiony) | 10 GB/s włączenie RA-GRS/GRS, 20 GB/s dla magazynu LRS/ZRS<sup>1</sup> |
| Maksymalna liczba wychodzących na konto magazynu (nam regiony) | 50 GB/s |
| Maksymalna liczba transferu danych przychodzących na konto magazynu (regiony Non-US) | 5 GB/s włączenie RA-GRS/GRS, 10 GB/s dla magazynu LRS/ZRS<sup>1</sup> |
| Maksymalna liczba wychodzących na konto magazynu (regiony Non-US) | 50 GB/s |

<sup>1</sup>[replikacja usługi azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) są następujące opcje:
* **RA-GRS**: Magazyn geograficznie nadmiarowy z dostępem do odczytu. Jeśli Magazyn RA-GRS jest włączona, ruch wychodzący związanych z lokalizacji dodatkowej są identyczne z tymi dla lokalizacji głównej.
* **GRS**: Magazyn geograficznie nadmiarowy. 
* **Magazyn ZRS**: Magazyn strefowo nadmiarowy.
* **LRS**: Magazyn lokalnie nadmiarowy. 

