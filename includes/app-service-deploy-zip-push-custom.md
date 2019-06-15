---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132552"
---
## <a name="deployment-customization"></a>Dostosowanie wdrożenia

Proces wdrażania przyjęto założenie, że plik zip, który możesz wypchnąć zawiera aplikacji gotowych do uruchomienia. Domyślnie są uruchamiane bez dostosowań. Aby włączyć ten sam procesów kompilacji, które zawierają dzięki ciągłej integracji, Dodaj następujący element w ustawieniach Twojej aplikacji:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Użycie opcji wdrożenia wypychania .zip, to ustawienie jest **false** domyślnie. Wartość domyślna to **true** wdrożeń ciągłej integracji. Po ustawieniu **true**, związanych z wdrażaniem ustawienia są używane podczas wdrażania. Te ustawienia można skonfigurować jako ustawienia aplikacji lub w pliku konfiguracyjnym .deployment, który znajduje się w katalogu głównym pliku zip. Aby uzyskać więcej informacji, zobacz [repozytorium i ustawień związanych z wdrażaniem](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) w odwołaniu do wdrożenia.