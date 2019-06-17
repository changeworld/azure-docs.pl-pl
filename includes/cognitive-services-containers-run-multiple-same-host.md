---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124334"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, do uruchamiania każdego kontenera przy użyciu innego portu narażone. Na przykład uruchomić pierwszego kontenera na porcie 5000 oraz drugi kontener na porcie 5001.

Może mieć ten kontener i uruchomiona na HOŚCIE ze sobą innego kontenera usług Azure Cognitive Services. Również może mieć wiele kontenerów działanie tego samego kontenera usług Cognitive Services.
