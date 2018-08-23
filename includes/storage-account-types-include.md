---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/20/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f60c23e34962396d4ea6e030912d1ca3f3e4571b
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "40260629"
---
Istnieją dwa typy kont magazynu:

### <a name="general-purpose-storage-accounts"></a>Konta magazynu ogólnego przeznaczenia
Konto magazynu ogólnego przeznaczenia zapewnia dostęp do usług Azure Storage, takich jak Tables, Queues, Files i Blobs oraz dysków maszyn wirtualnych Azure, w ramach jednego konta. Ten typ konta magazynu ma dwie warstwy wydajności:

* Warstwę wydajności magazynu standardowego umożliwiającą przechowywanie tabel, kolejek, plików, obiektów blob i dysków maszyny wirtualnej Azure.
* Warstwę wydajności magazynu w warstwie Premium, która obecnie obsługuje tylko dyski maszyny wirtualnej Azure. Zobacz temat [Premium Storage: High-Performance Storage for Azure Virtual Machine Workloads](../articles/virtual-machines/windows/premium-storage.md) (Premium Storage: usługa Storage o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure), aby uzyskać szczegółowe informacje o usłudze Premium Storage.

### <a name="blob-storage-accounts"></a>Konta usługi Blob Storage
Konto usługi Blob Storage to specjalne konto magazynu służące do przechowywania danych niestrukturalnych w formie obiektów blob w usłudze Azure Storage. Konta usługi Blob Storage są podobne do istniejących kont magazynu ogólnego przeznaczenia i udostępniają wszystkie używane obecnie funkcje doskonałej trwałości, dostępności, skalowalności i wydajności, łącznie z pełną spójnością interfejsu API na potrzeby blokowych i uzupełnialnych obiektów blob. W przypadku aplikacji wymagających tylko magazynu obiektów blokowych lub uzupełnialnych obiektów blob zalecamy używanie kont usługi Blob Storage.

> [!NOTE]
> Konta Magazynu obiektów blob obsługują tylko blokowe obiekty blob i uzupełnialne obiekty blob — stronicowe obiekty blob nie są obsługiwane.
> 
> 

Konta usługi Blob Storage udostępniają atrybut **Warstwa dostępu**, który można określić podczas tworzenia konta, a później zmodyfikować w zależności od potrzeb. Istnieją dwa typy warstw dostępu, które można określić na podstawie wzorca dostępu do danych:

* **Gorąca** warstwa dostępu, oznaczająca, że dostęp do obiektów na koncie magazynu będzie uzyskiwany częściej. Dzięki temu można przechowywać dane, do których jest tańszy dostęp.
* **Chłodna** warstwa dostępu, oznaczająca, że dostęp do obiektów na koncie magazynu będzie uzyskiwany rzadziej. Dzięki temu można przechowywać dane, których przechowywanie jest tańsze.

W przypadku zmiany wzorca użycia danych można także w dowolnym momencie przełączyć się między tymi warstwami dostępu. Zmiana warstwy dostępu może spowodować naliczenie dodatkowych opłat. Więcej szczegółowych informacji znajduje się w temacie [Pricing and billing for Blob storage accounts](../articles/storage/common/storage-account-options.md#pricing-and-billing) (Cennik i rozliczenia — konta usługi Blob Storage).

Więcej szczegółowych informacji na temat kont usługi Blob Storage znajduje się w temacie [Azure Blob Storage: Cool and Hot tiers](../articles/storage/blobs/storage-blob-storage-tiers.md) (Usługa Azure Blob Storage: warstwa chłodna i gorąca).

Przed utworzeniem konta magazynu musisz mieć subskrypcję platformy Azure, która umożliwia dostęp do jej różnych usług. Możesz rozpocząć pracę z platformą Azure od [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/). Jeśli zdecydujesz się na wykupienie planu subskrypcji, możesz wybrać spośród różnych [opcji zakupu](https://azure.microsoft.com/pricing/purchase-options/). Jeśli jesteś [subskrybentem portalu MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), możesz uzyskać bezpłatne miesięczne środki na korzystanie z usług Azure, m.in. Azure Storage. Informacje dotyczące cennika woluminów znajdują się w temacie [Azure Storage Pricing ](https://azure.microsoft.com/pricing/details/storage/) (Cennik usługi Azure Storage).

Więcej szczegółowych informacji na temat tworzenia konta magazynu znajduje się w temacie [Create a storage account](../articles/storage/common/storage-quickstart-create-account.md) (Tworzenie konta magazynu). Można utworzyć maksymalnie 200 kont magazynu o unikatowej nazwie w ramach jednej subskrypcji. Aby uzyskać szczegółowe informacje na temat limitów konta magazynu, zobacz temat [Cele dotyczące skalowalności i wydajności usługi Azure Storage](../articles/storage/common/storage-scalability-targets.md).

