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
ms.openlocfilehash: b8e3d32f0e230673ecbc043597afe8e7b5f25e06
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742453"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Co się dzieje z mojej aplikacji podczas wdrażania?

Wszystkich metod wdrażania oficjalnie obsługiwanych mają jedną wspólną: oni wprowadzić zmiany do plików w `/home/site/wwwroot` folderu aplikacji. Są to te same pliki, które są uruchamiane w środowisku produkcyjnym. W związku z tym wdrożenie może zakończyć się niepowodzeniem z powodu zablokowanych plików lub aplikacji w środowisku produkcyjnym mogą mieć nieprzewidywalne zachowanie podczas wdrażania, ponieważ nie wszystkie pliki są aktualizowane jednocześnie. Istnieje kilka różnych sposobów, aby uniknąć tych problemów:

- Zatrzymaj aplikację lub włączyć tryb offline dla aplikacji podczas wdrażania. Aby uzyskać więcej informacji, zobacz [zajmujących się pliki zablokowane podczas wdrażania](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Wdrażanie [miejscu przejściowym](../articles/app-service/web-sites-staged-publishing.md) z [automatycznej wymiany](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) włączone. 
- Użyj [uruchomienia z pakietu](https://github.com/Azure/app-service-announcements/issues/84) zamiast tego.
