---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704255"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, do uruchamiania każdego kontenera przy użyciu innego portu narażone. Na przykład uruchomić pierwszego kontenera na porcie 5000 oraz drugi kontener na porcie 5001.

Może mieć ten kontener i uruchomiona na HOŚCIE ze sobą innego kontenera usług Azure Cognitive Services. Również może mieć wiele kontenerów działanie tego samego kontenera usług Cognitive Services.
