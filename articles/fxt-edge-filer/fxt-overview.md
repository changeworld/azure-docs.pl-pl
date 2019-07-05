---
title: Filtr Edge FXT Azure Microsoft — omówienie
description: W tym artykule opisano filtr Edge FXT Azure hybrydowego magazynu w pamięci podręcznej, aktywne archiwum i rozwiązanie akceleratora dostępu do plików do obliczeń o wysokiej wydajności
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 58d4a2a52757b6ace1059fcccf379df3de5fd75c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542905"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Co to jest filtr Edge FXT Azure hybrydowego magazynu w pamięci podręcznej?

Usługa Azure filtr Edge FXT jest hybrydowy magazyn w pamięci podręcznej urządzenia, która zapewnia dostęp szybkie pliku i archiwum aktywnych zadań obliczeń o wysokiej wydajności (HPC).

Współpracuje z wielu źródeł danych, czy przechowywane w lokalnym centrum danych, zdalnie i w chmurze. Filtr Edge FXT Azure może zapewnić ujednolicony obszar nazw dla danych w systemach różnych magazynowania.

Co najmniej trzech urządzeń sprzętowych filtr Edge FXT współpracują ze sobą jako klastrowany system plików zapewnienie pamięci podręcznej. Aby uzyskać szczegółowe informacje dotyczące zakupu wymaganego sprzętu skontaktuj się z przedstawicielem handlowym firmy Microsoft. 

Aby dowiedzieć się więcej, przeczytaj arkusz danych i informacji o produktu na [filtr Edge FXT Azure](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Przypadki zastosowań

Filtr Edge FXT Azure zwiększa wydajność, najlepszym rozwiązaniem dla przepływów pracy, takie jak te:

* Przepływ pracy dostępu odczycie pliku 
* Protokoły NFSv3 lub SMB2
* Obliczenia farm 1000 do 100 000 rdzeni procesora CPU

### <a name="nas-optimization-and-scaling"></a>NAS optymalizacji i skalowania

Pamięć podręczna Azure FXT krawędzi filtr służy do wygładzania dostęp do istniejących systemów firm NetApp i Dell EMC Isilon NAS. Możesz również dodać obiektów Blob platformy Azure lub innych magazyn w chmurze w celu zapewnienia skalowalności, bez konieczności przerabiać procesy dostępu do danych po stronie klienta. 

### <a name="wan-caching"></a>Buforowanie w sieci WAN

Filtr krawędzi w usłudze Azure FXT może służyć do obsługi dostępu do plików szybkie z użytkownicy zaawansowani gdy danych, które są im potrzebne są przechowywane w innym miejscu. Zapewniają dostęp przy zachowaniu tworzenia kopii zapasowych i innych systemach zarządzania danymi w centrum danych scentralizowany. 

### <a name="active-archive-in-azure-blob"></a>Aktywne archiwum w usłudze Azure Blob

Rozwiń centrum danych do magazynu w chmurze za pomocą filtr Edge FXT Azure jako punkt dostępu. 

## <a name="features"></a>Funkcje 

Dostępne są dwa modele sprzętu. 

| Modelowanie | DRAM | NVMe SSD | Porty sieciowe | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25Gb/10Gb + 2 x 1Gb |


## <a name="next-steps"></a>Kolejne kroki

* Kontynuować zapoznawanie się filtr Edge FXT platformy Azure, czytając [specyfikacje](fxt-specs.md) lub [samouczek instalacji](fxt-install.md).
* Dowiedz się, jak kupić filtr Edge FXT platformy Azure na [stronę produktu Azure FXT krawędzi filtr](https://azure.microsoft.com/services/fxt-edge-filer/).