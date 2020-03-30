---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75945145"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co się stanie z moją aplikacją podczas wdrażania?

Wszystkie oficjalnie obsługiwane metody wdrażania wprowadzać zmiany `/home/site/wwwroot` do plików w folderze aplikacji. Te pliki są używane do uruchamiania aplikacji. W związku z tym wdrożenie może zakończyć się niepowodzeniem z powodu zablokowanych plików. Aplikacja może również zachowywać się nieprzewidywalnie podczas wdrażania, ponieważ nie wszystkie pliki zaktualizowane w tym samym czasie. Jest to niepożądane dla aplikacji skierowanej do klienta. Istnieje kilka różnych sposobów, aby uniknąć tych problemów:

- [Uruchom aplikację bezpośrednio z pakietu ZIP](../articles/app-service/deploy-run-package.md) bez rozpakowywania.
- Zatrzymaj aplikację lub włącz tryb offline dla aplikacji podczas wdrażania. Aby uzyskać więcej informacji, zobacz [Radzenie sobie z zablokowanymi plikami podczas wdrażania](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Wdrażanie w [miejscu przejściowym](../articles/app-service/deploy-staging-slots.md) z włączoną [automatyczną zamianą.](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) 
