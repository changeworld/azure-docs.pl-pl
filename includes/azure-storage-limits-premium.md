---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e3d2466f05028a963256dbcc052c46650857836d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372717"
---
### <a name="premium-performance-block-blob-storage"></a>Magazyn Premium wydajności block blob storage

Wydajność blokowych obiektów blob konta magazynu premium storage jest zoptymalizowany dla aplikacji, które używają mniejszych, KB zakresu obiektów. To idealne rozwiązanie dla aplikacji wymagających bardzo dużo transakcji stawek lub spójnego magazynu o małych opóźnieniach. Magazyn obiektów blob bloku wydajności Premium została zaprojektowana do skalowania za pomocą aplikacji. Jeśli planujesz wdrożyć aplikacje wymagające setki tysięcy żądań na sekundę lub petabajtom pojemności magazynu, skontaktuj się z nami, przesyłając żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-page-blob-storage"></a>Magazyn obiektów blob w warstwie Premium wydajności strony

Wydajność warstwy Premium kont magazynu ogólnego zastosowania v1 lub v2 ma następujące cele skalowalności:

| Łączna liczba kont pojemności                            | Całkowitej przepustowości dla konta magazynu lokalnie nadmiarowego                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Pojemność dysku: 35 TB <br>Pojemność migawki: 10 TB | Się do 50 gigabity na sekundę dla ruchu przychodzącego<sup>1</sup> + wychodzącego<sup>2</sup> |

<sup>1</sup> wszystkich danych (żądań), które są wysyłane do konta magazynu

<sup>2</sup> wszystkich danych (żądań), które są odbierane z konta magazynu

Jeśli używasz kont usługi premium storage wydajności dla dysków niezarządzanych aplikacji przekracza cele skalowalności z jednego konta magazynu, można migrować do usługi managed disks. Jeśli nie chcesz przeprowadzić migrację do usługi managed disks, tworzenie aplikacji na używanie wielu kont magazynu. Następnie podzielić dane na tych kontach magazynu. Na przykład jeśli chcesz dołączyć dyski 51 – TB na wielu maszynach wirtualnych, rozkładają się je na dwa konta magazynu. 35 TB jest limit dla konta magazynu premium jednego. Upewnij się, że konto magazynu premium pojedynczego wydajności nigdy nie ma więcej niż 35 TB aprowizowanego dysku.