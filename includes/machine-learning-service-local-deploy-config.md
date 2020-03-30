---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477276"
---
Wpisy w `deploymentconfig.json` dokumencie są mapowane na parametry [localwebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr Metody | Opis |
| ----- | ----- | ----- |
| `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. W przypadku celów lokalnych `local`wartość musi być . |
| `port` | `port` | Port lokalny, na którym mają być uwidaczniane punkt końcowy HTTP usługi. |

Ten JSON jest przykładową konfiguracją wdrożenia do użytku z wierszem polecenia:

```json
{
    "computeType": "local",
    "port": 32267
}
```
