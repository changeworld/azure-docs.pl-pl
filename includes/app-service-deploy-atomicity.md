---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836824"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co się dzieje z mojej aplikacji podczas wdrażania?

Wszystkie metody oficjalnie obsługiwane wdrożenie wprowadzić zmiany do plików w `/home/site/wwwroot` folderu aplikacji. Te pliki są identyczne, te, które są uruchamiane w środowisku produkcyjnym. Dlatego wdrożenie może zakończyć się niepowodzeniem z powodu zablokowane pliki. Aplikacji w środowisku produkcyjnym może również nieprzewidywalne zachowanie podczas wdrażania, ponieważ nie wszystkie pliki są aktualizowane w tym samym czasie. Istnieje kilka różnych sposobów, aby uniknąć tych problemów:

- Zatrzymaj aplikację lub włączyć tryb offline dla aplikacji podczas wdrażania. Aby uzyskać więcej informacji, zobacz [przeciwdziałania pliki zablokowane podczas wdrażania](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Wdrażanie [miejscu przejściowym](../articles/app-service/deploy-staging-slots.md) z [automatycznej wymiany](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) włączone. 
- Użyj [uruchomienia z pakietu](https://github.com/Azure/app-service-announcements/issues/84) zamiast ciągłego wdrażania.
