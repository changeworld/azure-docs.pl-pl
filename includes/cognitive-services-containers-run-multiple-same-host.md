---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753822"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, do uruchamiania każdego kontenera przy użyciu innego portu. Na przykład uruchomić pierwszego kontenera na porcie 5000 oraz drugi kontener na porcie 5001.

Zastąp `<container-registry>` i `<container-name>` wartościami kontenerów, możesz użyć. Te nie muszą być tego samego kontenera. Masz kontener twarzy i kontenerów usługi LUIS jednocześnie uruchomionych na HOŚCIE lub może mieć wiele kontenerów twarzy uruchomiona. 

Uruchamianie pierwszego kontenera na porcie 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Uruchom drugiego kontenera na porcie 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Każdy kontener kolejnych powinna być na innym porcie. 
