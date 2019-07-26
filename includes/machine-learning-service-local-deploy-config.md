---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: e8c0b35f2ac6d3468a5e38a90cc7f4de09d3682b
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348533"
---
Wpisy w `deploymentconfig.json` dokumencie są mapowane na parametry [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. W przypadku wartości lokalnych wartość musi być `local`równa. |
| `port` | `port` | Port lokalny, na którym ma zostać ujawniony punkt końcowy HTTP usługi. |

Poniższy kod JSON jest przykładową konfiguracją wdrożenia do użycia z interfejsem wiersza polecenia:

```json
{
    "computeType": "local",
    "port": 32267
}
```
