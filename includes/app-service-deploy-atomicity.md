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
ms.openlocfilehash: 91a4a9ae1d3d84f1396adad07d1cda73ee3747c9
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312470"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co się dzieje z mojej aplikacji podczas wdrażania?

Wszystkich metod wdrażania oficjalnie obsługiwanych mają jedną wspólną: oni wprowadzić zmiany do plików w `/site/home/wwwroot` folderu aplikacji. Są to te same pliki, które są uruchamiane w środowisku produkcyjnym. W związku z tym wdrożenie może zakończyć się niepowodzeniem z powodu zablokowanych plików lub aplikacji w środowisku produkcyjnym mogą mieć nieprzewidywalne zachowanie podczas wdrażania, ponieważ nie wszystkie pliki są aktualizowane jednocześnie. Istnieje kilka różnych sposobów, aby uniknąć tych problemów:

- Zatrzymaj aplikację lub włączyć tryb offline dla aplikacji podczas wdrażania. Aby uzyskać więcej informacji, zobacz [zajmujących się pliki zablokowane podczas wdrażania](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Wdrażanie [miejscu przejściowym](../articles/app-service/web-sites-staged-publishing.md) z [automatycznej wymiany](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) włączone. 
- Użyj [uruchomienia z pakietu](https://github.com/Azure/app-service-announcements/issues/84) zamiast tego.
