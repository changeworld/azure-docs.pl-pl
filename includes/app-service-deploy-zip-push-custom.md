---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183034"
---
## <a name="deployment-customization"></a>Dostosowywanie wdrażania

Proces wdrażania zakłada, że wypychany plik zip zawiera gotową do uruchomienia aplikację. Domyślnie nie są uruchamiane żadne dostosowania. Aby włączyć te same procesy kompilacji, które można uzyskać z ciągłej integracji, dodać następujące do ustawień aplikacji:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

W przypadku korzystania z .zip push wdrożenia, to ustawienie jest **false** domyślnie. Wartość domyślna **dotyczy** wdrożeń integracji ciągłej. Po ustawieniu na **true**ustawienia związane z wdrożeniem są używane podczas wdrażania. Ustawienia te można skonfigurować jako ustawienia aplikacji lub w pliku konfiguracyjnym .deployment, który znajduje się w katalogu głównym pliku zip. Aby uzyskać więcej informacji, zobacz [Ustawienia repozytorium i związane z wdrażaniem](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) w odwołaniu do wdrożenia.