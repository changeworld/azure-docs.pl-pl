---
title: Wdrażanie usługi synchronizacji magazynu
description: Wdrażanie zasobu chmury Azure File Sync. Usługa synchronizacji magazynu. Wspólny blok tekstu współużytkowany przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209456"
---
W tym kroku wymagane są poświadczenia subskrypcji platformy Azure. Używana subskrypcja platformy Azure może być różna od używanej przez StorSimple.

Zasób podstawowy do skonfigurowania Azure File Sync nosi nazwę "usługa synchronizacji magazynu".
Zalecamy wdrożenie tylko jednego dla wszystkich serwerów w firmie, które synchronizują ten sam zestaw plików teraz lub w przyszłości. Jeśli masz więcej niż jedno urządzenie StorSimple, możesz rozważyć utworzenie zasobu usługi synchronizacji magazynu dla każdego z nich. Należy jednak utworzyć wiele usług synchronizacji magazynu, jeśli istnieją różne zestawy serwerów, które nigdy nie muszą wymieniać danych. W przeciwnym razie najlepszym rozwiązaniem jest usługa synchronizacji magazynu.

Wybierz region platformy Azure dla usługi synchronizacji magazynu znajdującej się blisko lokalizacji biura. Wszystkie inne zasoby w chmurze muszą zostać wdrożone w tym samym regionie.
Najlepszym rozwiązaniem jest utworzenie nowej grupy zasobów w ramach subskrypcji w celu ułatwienia zarządzania nimi.

W tym artykule opisano sposób wdrażania usługi synchronizacji magazynu. Wykonaj tylko tę część dokumentu. W dalszych krokach zostaną połączone z innymi podsekcjami tego dokumentu.

[Dowiedz się, jak wdrożyć usługę synchronizacji magazynu.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
