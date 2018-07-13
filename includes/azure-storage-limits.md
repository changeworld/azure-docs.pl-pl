---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/03/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6572adb0d8d629910492603a17988b89acce2f17
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756039"
---
| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję | 200<sup>1</sup> |
| Maksymalna pojemność konta magazynu | 500 TiB<sup>2</sup> |
| Maksymalna liczba kontenerów obiektów blob, obiekty BLOB, udziały plików, tabel, kolejek, jednostki lub wiadomości na koncie magazynu | Bez ograniczeń |
| Maksymalna liczba żądań na konto magazynu | 20 000 żądań na sekundę<sup>2</sup> |
| Maksymalna liczba przychodzących<sup>3</sup> na konto magazynu (nam regiony) | 10 GB/s włączenie RA-GRS/GRS, 20 GB/s dla magazynu LRS/ZRS<sup>4</sup> |
| Maksymalna liczba wychodzących<sup>3</sup> na konto magazynu (nam regiony) | 20 GB/s włączenie RA-GRS/GRS, 30 GB/s dla magazynu LRS/ZRS<sup>4</sup> |
| Maksymalna liczba przychodzących<sup>3</sup> na konto magazynu (regiony Non-US) | 5 GB/s włączenie RA-GRS/GRS, 10 GB/s dla magazynu LRS/ZRS<sup>4</sup> |
| Maksymalna liczba wychodzących<sup>3</sup> na konto magazynu (regiony Non-US) | 10 GB/s włączenie RA-GRS/GRS, 15 GB/s dla magazynu LRS/ZRS<sup>4</sup> |

<sup>1</sup>obejmuje konta magazynu warstwy standardowa i Premium. Jeśli potrzebujesz więcej niż 200 kont magazynu w danym regionie, należy wykonać żądanie za pośrednictwem [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/). Zespół usługi Azure Storage przejrzy Twój przypadek biznesowy i może zatwierdzić do 250 kont magazynu dla danego regionu. 

<sup>2</sup> Jeśli potrzebujesz rozwiniętej limity konta magazynu, skontaktuj się z pomocą [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/). Zespół usługi Azure Storage przejrzy żądanie i może zatwierdzić wyższe limity na podstawie przypadku. Zarówno ogólnego przeznaczenia i kont usługi Blob storage obsługują większą pojemność, transferu danych przychodzących i wychodzących i liczba żądań przez żądanie. Aby uzyskać nowe maksymalne wartości dla kont usługi Blob storage, zobacz [ogłoszenie kont magazynu większych i wyższej skali](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> ograniczone tylko przez limity ruchem przychodzącym/wychodzącym dla konta. *Ruch przychodzący* odnosi się do wszystkich danych (żądań) są wysyłane do konta magazynu. *Ruch wychodzący* odnosi się do wszystkich danych (żądań) wysyłanych z konta magazynu.  

<sup>4</sup>Opcje nadmiarowości usługi azure Storage obejmują:
* **RA-GRS**: Magazyn geograficznie nadmiarowy z dostępem do odczytu. Jeśli Magazyn RA-GRS jest włączona, ruch wychodzący związanych z lokalizacji dodatkowej są identyczne z tymi dla lokalizacji głównej.
* **GRS**: Magazyn geograficznie nadmiarowy. 
* **Magazyn ZRS**: Magazyn strefowo nadmiarowy.
* **LRS**: Magazyn lokalnie nadmiarowy. 
