---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ac2cf4d688b1bdc54ed2d7341f0e195d3b2fe42d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236473"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co się dzieje z mojej aplikacji podczas wdrażania?

Wszystkie metody wdrażania wspieranych mają wspólną rzecz: oni wprowadzić zmiany w plikach w `/site/home/wwwroot` folderu aplikacji. Są to tych samych plików, które są uruchamiane w środowisku produkcyjnym. Oznacza to wdrożenie może zakończyć się niepowodzeniem z powodu zablokowanych plików i aplikacji w środowisku produkcyjnym może mieć nieprzewidywalne zachowanie podczas wdrażania, ponieważ nie wszystkie pliki są aktualizowane jednocześnie. Istnieje kilka sposobów, aby uniknąć tych problemów:

- Zatrzymaj aplikację lub Włącz tryb offline dla aplikacji podczas wdrażania. Aby uzyskać więcej informacji, zobacz [zajmujących się pliki zablokowane podczas wdrażania](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Wdrażanie na [miejsca](../articles/app-service/web-sites-staged-publishing.md) z [automatycznej wymiany](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) włączone. 
- Użyj [Uruchom z Zip](https://github.com/Azure/app-service-announcements/issues/84) zamiast tego.
