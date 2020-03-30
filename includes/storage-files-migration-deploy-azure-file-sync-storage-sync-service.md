---
title: Wdrażanie usługi synchronizacji magazynu
description: Wdrażanie zasobu chmury usługi Azure File Sync. Usługa synchronizacji magazynu. Wspólny blok tekstu, współużytkowane przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124709"
---
W tym kroku potrzebujesz poświadczeń subskrypcji platformy Azure.

Podstawowy zasób do konfigurowania usługi Azure File Sync jest nazywany "usługą synchronizacji magazynu".
Zaleca się wdrożenie tylko jednego dla wszystkich serwerów w firmie, które synchronizują ten sam zestaw plików teraz lub w przyszłości. Utwórz wiele usług synchronizacji magazynu tylko wtedy, gdy masz różne zestawy serwerów, które nigdy nie mogą wymieniać danych. (na przykład: zsynchronizować ten sam udział plików platformy Azure). W przeciwnym razie jedną usługę synchronizacji magazynu jest najlepszym rozwiązaniem.

Wybierz region platformy Azure dla usługi synchronizacji magazynu, która znajduje się w pobliżu lokalizacji biura. Wszystkie inne zasoby w chmurze muszą być wdrożone w tym samym regionie.
Aby uprościć zarządzanie, utwórz nową grupę zasobów w ramach subskrypcji, w której znajdują się zasoby synchronizacji i magazynu.

W poniższym artykule opisano sposób wdrażania usługi synchronizacji magazynu. Postępuj tylko zgodnie z tą częścią doc. W późniejszych krokach będą dostępne łącza do innych podsekcji tego doc.

[Dowiedz się, jak wdrożyć usługę synchronizacji magazynu.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
