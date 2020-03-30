---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477259"
---
Aby zaktualizować usługę sieci `update` web, użyj tej metody. Można zaktualizować usługę sieci web, aby użyć nowego modelu, nowego skryptu wpisu lub nowych zależności, które można określić w konfiguracji wnioskowania. Aby uzyskać więcej informacji, zobacz dokumentację [witryny Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Podczas tworzenia nowej wersji modelu należy ręcznie zaktualizować każdą usługę, która ma być używana.
>
> Nie można użyć zestawu SDK do aktualizacji usługi sieci web opublikowanej za pomocą projektanta usługi Azure Machine Learning.

**Używanie zestawu SDK**

Poniższy kod pokazuje, jak używać SDK do aktualizowania modelu, środowiska i skryptu wpisu dla usługi sieci web:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Korzystanie z interfejsu wiersza polecenia**

Usługę sieci web można również zaktualizować za pomocą interfejsu wiersza polecenia ML. Poniższy przykład pokazuje rejestrowanie nowego modelu, a następnie aktualizowanie usługi sieci web w celu użycia nowego modelu:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> W tym przykładzie dokument JSON jest używany do przekazywania informacji o modelu z polecenia rejestracji do polecenia aktualizacji.
>
> Aby zaktualizować usługę w celu użycia nowego skryptu lub środowiska wpisu, `ic` utwórz [plik konfiguracyjny wnioskowania](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) i określ go za pomocą parametru.

Aby uzyskać więcej informacji, zobacz dokumentację [aktualizacji usługi az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)