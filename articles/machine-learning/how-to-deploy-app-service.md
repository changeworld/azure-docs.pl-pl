---
title: Wdrażanie modeli ml w usłudze Azure App Service (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą usługi Azure Machine Learning wdrożyć model w aplikacji sieci Web w usłudze Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282821"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Wdrażanie modelu uczenia maszynowego w usłudze Azure App Service (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak wdrożyć model z usługi Azure Machine Learning jako aplikację sieci web w usłudze Azure App Service.

> [!IMPORTANT]
> Chociaż usługa Azure Machine Learning i usługa Azure App Service są ogólnie dostępne, możliwość wdrożenia modelu z usługi uczenia maszynowego do usługi App Service jest w wersji zapoznawczej.

Za pomocą usługi Azure Machine Learning można tworzyć obrazy platformy Docker na podstawie wyszkolonych modeli uczenia maszynowego. Ten obraz zawiera usługę sieci web, która odbiera dane, przesyła je do modelu, a następnie zwraca odpowiedź. Usługa Azure App Service może służyć do wdrażania obrazu i zawiera następujące funkcje:

* Zaawansowane [uwierzytelnianie](/azure/app-service/configure-authentication-provider-aad) w celu zwiększenia bezpieczeństwa. Metody uwierzytelniania obejmują zarówno usługę Azure Active Directory, jak i wieloczynnikową auth.
* [Skalowanie automatyczne](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) bez konieczności ponownego rozmieszczenia.
* [Obsługa protokołu TLS](/azure/app-service/configure-ssl-certificate-in-code) dla bezpiecznej komunikacji między klientami a usługą.

Aby uzyskać więcej informacji na temat funkcji udostępnianych przez usługę Azure App Service, zobacz [omówienie usługi App Service](/azure/app-service/overview).

> [!IMPORTANT]
> Jeśli potrzebujesz możliwości rejestrowania danych oceniania używanych z wdrożonym modelem lub wyników oceniania, należy zamiast tego wdrożyć w usłudze Azure Kubernetes. Aby uzyskać więcej informacji, zobacz [Zbieranie danych dotyczących modeli produkcyjnych](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego.](how-to-manage-workspace.md)
* [Interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Przeszkolony model uczenia maszynowego zarejestrowany w obszarze roboczym. Jeśli nie masz modelu, użyj [samouczka klasyfikacji obrazów: trenuj model,](tutorial-train-models-with-aml.md) aby go trenować i rejestrować.

    > [!IMPORTANT]
    > Fragmenty kodu w tym artykule zakładają, że ustawiono następujące zmienne:
    >
    > * `ws`- Obszar roboczy usługi Azure Machine Learning.
    > * `model`- Zarejestrowany model, który zostanie wdrożony.
    > * `inference_config`- Konfiguracja wnioskowania dla modelu.
    >
    > Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem należy zdefiniować, co jest potrzebne do uruchomienia modelu jako usługi sieci web. Na poniższej liście opisano podstawowe elementy potrzebne do wdrożenia:

* __Skrypt wpisu__. Ten skrypt akceptuje żądania, ocenia żądanie przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > Skrypt wpisowy jest specyficzny dla danego modelu; musi zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwróconych klientom.
    >
    > Jeśli dane żądania jest w formacie, który nie jest użyteczny przez model, skrypt można przekształcić go w formacie dopuszczalne. Może również przekształcić odpowiedź przed zwróceniem do niego do klienta.

    > [!IMPORTANT]
    > Zestaw SDK usługi Azure Machine Learning nie zapewnia dostępu do magazynu danych lub zestawów danych w usłudze sieci Web. Jeśli potrzebujesz wdrożonego modelu, aby uzyskać dostęp do danych przechowywanych poza wdrożeniem, na przykład na koncie usługi Azure Storage, musisz opracować niestandardowe rozwiązanie kodu przy użyciu odpowiedniego zestawu SDK. Na przykład zestaw [SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python).
    >
    > Inną alternatywą, która może działać dla twojego scenariusza jest [przewidywanie partii,](how-to-use-parallel-run-step.md)który zapewnia dostęp do magazynów danych podczas oceniania.

    Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

* **Zależności**, takie jak skrypty pomocnicze lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wejścia

Te jednostki są hermetyzowane __w konfiguracji wnioskowania__. Konfiguracja wnioskowania przywołuje skrypt wejściowy i inne zależności.

> [!IMPORTANT]
> Podczas tworzenia konfiguracji wnioskowania do użytku z usługą Azure App Service, należy użyć [środowiska](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) obiektu. Należy pamiętać, że jeśli definiujesz środowisko niestandardowe, należy dodać azureml-defaults z wersją >= 1.0.45 jako zależności pipsa. Ten pakiet zawiera funkcje potrzebne do obsługi modelu jako usługi sieci web. Poniższy przykład pokazuje tworzenie obiektu środowiska i używanie go z konfiguracją wnioskowania:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Aby uzyskać więcej informacji na temat środowisk, zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi.](how-to-use-environments.md)

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Podczas wdrażania w usłudze Azure App Service nie trzeba tworzyć __konfiguracji wdrażania__.

## <a name="create-the-image"></a>Tworzenie obrazu

Aby utworzyć obraz platformy Docker wdrożony w usłudze Azure App Service, należy użyć [pliku Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-). Poniższy fragment kodu pokazuje, jak utworzyć nowy obraz z konfiguracji modelu i wnioskowania:

> [!NOTE]
> Fragment kodu zakłada, że `model` zawiera zarejestrowany model i `inference_config` który zawiera konfigurację dla środowiska wnioskowania. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Kiedy `show_output=True`jest wyświetlany wynik procesu kompilacji platformy Docker. Po zakończeniu procesu obraz został utworzony w rejestrze kontenerów platformy Azure dla obszaru roboczego. Po zbudowaniu obrazu zostanie wyświetlona lokalizacja w rejestrze kontenerów platformy Azure. Zwrócona lokalizacja jest `<acrinstance>.azurecr.io/package:<imagename>`w formacie . Na przykład `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Zapisz informacje o lokalizacji, ponieważ jest on używany podczas wdrażania obrazu.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Użyj następującego polecenia, aby uzyskać poświadczenia logowania dla rejestru kontenerów platformy Azure, który zawiera obraz. Zastąp `<acrinstance>` wartością zwróconą wcześniej z: `package.location`

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Dane wyjściowe tego polecenia są podobne do następującego dokumentu JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Zapisz wartość __nazwy użytkownika__ i jednego z __haseł__.

1. Jeśli nie masz jeszcze grupy zasobów lub planu usługi aplikacji do wdrożenia usługi, następujące polecenia pokazują, jak utworzyć oba:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    W tym przykładzie używana jest`--sku B1` __podstawowa__ warstwa cenowa ( ).

    > [!IMPORTANT]
    > Obrazy utworzone przez usługę Azure Machine Learning `--is-linux` używają systemu Linux, więc należy użyć tego parametru.

1. Aby utworzyć aplikację sieci web, użyj następującego polecenia. Zamień `<app-name>` na nazwę, której chcesz użyć. Zastąp `<acrinstance>` i `<imagename>` `package.location` wartości z zwróconych wcześniej:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    To polecenie zwraca informacje podobne do następującego dokumentu JSON:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > W tym momencie aplikacja internetowa została utworzona. Jednak ponieważ nie podano poświadczeń do rejestru kontenerów platformy Azure, który zawiera obraz, aplikacja sieci web nie jest aktywna. W następnym kroku należy podać informacje uwierzytelniania dla rejestru kontenerów.

1. Aby zapewnić aplikacji sieci web poświadczenia potrzebne do uzyskania dostępu do rejestru kontenerów, należy użyć następującego polecenia. Zamień `<app-name>` na nazwę, której chcesz użyć. Zamień `<acrinstance>` i `<imagename>` wartości `package.location` z zwróconych wcześniej. Zastąp `<username>` i `<password>` z informacjami logowania ACR pobranymi wcześniej:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    To polecenie zwraca informacje podobne do następującego dokumentu JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

W tym momencie aplikacja internetowa rozpoczyna ładowanie obrazu.

> [!IMPORTANT]
> Może upłynąć kilka minut, zanim obraz zostanie załadowany. Aby monitorować postęp, użyj następującego polecenia:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Po załadowaniu obrazu i uaktywnieniu witryny `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`w dzienniku zostanie wyświetlony komunikat o podaniu .

Po wdrożeniu obrazu można znaleźć nazwa hosta za pomocą następującego polecenia:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

To polecenie zwraca informacje podobne do `<app-name>.azurewebsites.net`następującej nazwy hosta - . Użyj tej wartości jako części __podstawowego adresu URL__ dla usługi.

## <a name="use-the-web-app"></a>Korzystanie z aplikacji sieci Web

Usługa sieci web, która przekazuje żądania `{baseurl}/score`do modelu znajduje się w . Na przykład `https://<app-name>.azurewebsites.net/score`. Poniższy kod języka Python pokazuje, jak przesłać dane do adresu URL i wyświetlić odpowiedź:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować aplikację sieci Web w dokumentacji [usługi App Service w systemie Linux.](/azure/app-service/containers/)
* Dowiedz się więcej o skalowaniu w [obszarze Wprowadzenie do skalowania automatycznego na platformie Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Użyj certyfikatu TLS/SSL w usłudze Azure App Service](/azure/app-service/configure-ssl-certificate-in-code).
* [Skonfiguruj aplikację usługi App Service do logowania się do usługi Azure Active Directory](/azure/app-service/configure-authentication-provider-aad).
* [Korzystanie z modelu ml wdrożonego jako usługa sieci web](how-to-consume-web-service.md)
