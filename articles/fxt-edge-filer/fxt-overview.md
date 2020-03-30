---
title: Omówienie narzędzia Microsoft Azure FXT Edge Filer
description: W tym artykule opisano hybrydową pamięć podręczną magazynu plików usługi Azure FXT Edge Filer, aktywne rozwiązanie do akceleratora archiwum i dostępu do plików do obliczeń o wysokiej wydajności
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72254838"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Co to jest hybrydowa pamięć podręczna magazynu usługi Azure FXT Edge Filer?

Azure FXT Edge Filer to hybrydowe urządzenie do buforowania pamięci masowej, które zapewnia szybki dostęp do plików i aktywne archiwum dla zadań obliczeniowych o wysokiej wydajności (HPC).

Działa z wieloma źródłami danych, niezależnie od tego, czy są przechowywane w lokalnym centrum danych, zdalnie, czy w chmurze. Azure FXT Edge Filer może zapewnić ujednoliconą przestrzeń nazw dla danych w różnych systemach magazynu.

Co najmniej trzy urządzenia sprzętowe FXT Edge Filer współpracują ze sobą jako klastrowany system plików w celu zapewnienia pamięci podręcznej. Aby uzyskać szczegółowe informacje na temat zakupu wymaganego sprzętu, skontaktuj się z przedstawicielem firmy Microsoft. 

Aby dowiedzieć się więcej, przeczytaj informacje o produkcie i arkusz danych na [platformie Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Przypadki zastosowań

Azure FXT Edge Filer zwiększa produktywność najlepiej dla przepływów pracy, takich jak te:

* Przepływ pracy dostępu do plików o dużej przełkowej 
* Protokoły NFSv3 lub SMB2
* Farmy obliczeniowe od 1000 do 100 000 rdzeni procesora

### <a name="nas-optimization-and-scaling"></a>Optymalizacja i skalowanie serwera NAS

Za pomocą pamięci podręcznej Azure FXT Edge Filer można uzyskać płynny dostęp do istniejących systemów Nas NetApp i Dell EMC Isilon. Można również dodać azure blob lub innego magazynu w chmurze, aby zapewnić skalowalność bez konieczności ponownego przetwarzania procesów dostępu do danych po stronie klienta. 

### <a name="wan-caching"></a>Buforowanie wan

Azure FXT Edge Filer może służyć do obsługi szybkiego dostępu do plików od użytkowników zaaprosznika, gdy dane, których potrzebują, są przechowywane w innym miejscu. Zapewnij dostęp przy jednoczesnym zachowaniu kopii zapasowych i innych systemów zarządzania danymi w scentralizowanym centrum danych. 

### <a name="active-archive-in-azure-blob"></a>Aktywne archiwum w obiekcie Blob platformy Azure

Rozszerz swoje centrum danych do magazynu w chmurze z Azure FXT Edge Filer jako punkt dostępu. 

## <a name="features"></a>Funkcje 

Dostępne są dwa modele sprzętu. 

| Model | Dram | NVMe SSD | Porty sieciowe | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |


## <a name="next-steps"></a>Następne kroki

* Kontynuuj naukę o usłudze Azure FXT Edge Filer, czytając [specyfikacje](fxt-specs.md) lub [samouczek instalacyjny](fxt-install.md).
* Dowiedz się, jak kupić plik usługi Azure FXT Edge filer na [stronie produktu Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).