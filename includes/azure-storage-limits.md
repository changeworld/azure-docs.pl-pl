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
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852062"
---
| Zasób | Limit domyślny |
| --- | --- |
| Liczba kont magazynu na region na subskrypcję | 200<sup>1</sup> |
| Maksymalna pojemność konta | 500 TiB<sup>2</sup> |
| Maksymalna liczba kontenerów obiektów blob, obiekty BLOB, udziałów plików, tabel, kolejek, jednostki lub wiadomości dla konta magazynu | Bez ograniczeń |
| Żądanie maksymalną szybkością konta magazynu | 20 000 żądań na sekundę<sup>2</sup> |
| Maksymalna liczba wejściowych<sup>3</sup> na konto magazynu (nam regiony) | 10 GB/s włączenie RA-GRS/GRS 20 GB/s dla LRS/ZRS<sup>4</sup> |
| Maksymalna liczba wyjście<sup>3</sup> na konto magazynu (nam regiony) | 20 GB/s włączenie RA-GRS/GRS 30 GB/s dla LRS/ZRS<sup>4</sup> |
| Maksymalna liczba wejściowych<sup>3</sup> na konto magazynu (regiony Non-US) | 5 GB/s włączenie RA-GRS/GRS 10 GB/s dla LRS/ZRS<sup>4</sup> |
| Maksymalna liczba wyjście<sup>3</sup> na konto magazynu (regiony Non-US) | 10 GB/s włączenie RA-GRS/GRS 15 GB/s dla LRS/ZRS<sup>4</sup> |

<sup>1</sup>obejmuje zarówno standardowa i Premium konta magazynu. Jeśli potrzebujesz więcej niż 200 kont magazynu w danym regionie, Utwórz żądanie za pośrednictwem [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/). Zespół usługi Azure Storage przejrzeć tej sprawy biznesowych i może zatwierdzić do 250 kont magazynu dla danego regionu. 

<sup>2</sup> Jeśli potrzebujesz rozwinięte limity konta magazynu, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/). Zespół usługi Azure Storage będzie Przejrzyj żądanie i może zatwierdzić wyższe limity na podstawie przypadku. Zarówno ogólnego przeznaczenia i kont magazynu obiektów Blob obsługują zwiększenia pojemności, wejście/wyjście i liczby żądań przez żądanie. Dla nowego maksymalne wartości dla konta magazynu obiektów Blob, zobacz [Announcing kont magazynu w skali większych i wyższe](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> ograniczone tylko przez limity wejście/wyjście konta. *Transfer danych przychodzących* odwołuje się do wszystkich danych (liczba żądań) są wysyłane do konta magazynu. *Ruch wychodzący* odnosi się do wszystkich danych (żądań) wysyłanych z konta magazynu.  

<sup>4</sup>opcje nadmiarowość magazynu azure obejmują:
* **RA-GRS**: dostęp do odczytu z magazynu geograficznie nadmiarowego magazynu. Jeśli włączono RA-GRS, docelowe wyjście dodatkowej lokalizacji są identyczne jak do lokalizacji głównej.
* **GRS**: Magazyn geograficznie nadmiarowy. 
* **Magazyn ZRS**: Magazyn Strefowo nadmiarowy.
* **Magazyn LRS**: Magazyn lokalnie nadmiarowy. 
