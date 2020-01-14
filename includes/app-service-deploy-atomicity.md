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
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752066"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co się stanie z moją aplikacją podczas wdrażania?

Wszystkie oficjalnie obsługiwane metody wdrażania wprowadzają zmiany w plikach w folderze `/home/site/wwwroot` aplikacji. Te pliki są używane do uruchamiania aplikacji. W związku z tym wdrożenie może zakończyć się niepowodzeniem ze względu na zablokowane pliki. Aplikacja może również zachowywać się nieprzewidywalne podczas wdrażania, ponieważ nie wszystkie pliki zostały zaktualizowane w tym samym czasie. Jest to niepożądane dla aplikacji klienta. Istnieje kilka różnych sposobów, aby uniknąć następujących problemów:

- Zatrzymaj aplikację lub Włącz tryb offline dla aplikacji podczas wdrażania. Aby uzyskać więcej informacji, zobacz temat [postępowanie z zablokowanymi plikami podczas wdrażania](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Wdróż w [miejscu przejściowym](../articles/app-service/deploy-staging-slots.md) z włączonym funkcją [autozamieniania](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) . 
- Użyj polecenia [Run z pakietu](https://github.com/Azure/app-service-announcements/issues/84) zamiast ciągłego wdrażania.
