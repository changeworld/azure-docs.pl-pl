---
title: Wdróż modele ml do Azure App Service (wersja zapoznawcza)
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak za pomocą usługi Azure Machine Learning wdrożyć model w aplikacji sieci Web w programie Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 889158aeb40cfcbc69291845acfee833af0930b6
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114294"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Wdróż model uczenia maszynowego w Azure App Service (wersja zapoznawcza)

Dowiedz się, jak wdrożyć model z usługi Azure Machine Learning jako aplikację sieci Web w programie Azure App Service.

> [!IMPORTANT]
> Chociaż usługa Azure Machine Learning i Azure App Service są ogólnie dostępne, możliwość wdrażania modelu z usługi Machine Learning na App Service jest w wersji zapoznawczej.

Za pomocą usługi Azure Machine Learning można tworzyć obrazy platformy Docker z szkoleń modeli uczenia maszynowego. Ten obraz zawiera usługę sieci Web, która odbiera dane, przesyła ją do modelu, a następnie zwraca odpowiedź. Azure App Service można użyć do wdrożenia obrazu i zapewnia następujące funkcje:

* Zaawansowane [uwierzytelnianie](/azure/app-service/configure-authentication-provider-aad) na potrzeby zwiększonych zabezpieczeń. Metody uwierzytelniania obejmują zarówno Azure Active Directory, jak i uwierzytelnianie wieloskładnikowe.
* [Skalowanie automatyczne](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) bez konieczności ponownego wdrażania.
* [Obsługa protokołu SSL](/azure/app-service/app-service-web-ssl-cert-load) w celu zapewnienia bezpiecznej komunikacji między klientami a usługą.

Aby uzyskać więcej informacji na temat funkcji zapewnianych przez Azure App Service, zobacz [omówienie App Service](/azure/app-service/overview).

> [!IMPORTANT]
> Jeśli potrzebujesz możliwości rejestrowania danych oceniających używanych przez wdrożony model lub wyniki oceny, zamiast tego należy przeprowadzić wdrożenie w usłudze Azure Kubernetes Service. Aby uzyskać więcej informacji, zobacz [zbieranie danych w modelach produkcyjnych](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .
* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Model uczenia maszynowego zarejestrowany w Twoim obszarze roboczym. Jeśli nie masz modelu, Skorzystaj z [samouczka klasyfikacji obrazów: uczenie modelu](tutorial-train-models-with-aml.md) do uczenia i zarejestrowania go.

    > [!IMPORTANT]
    > W fragmentach kodu w tym artykule przyjęto założenie, że ustawiono następujące zmienne:
    >
    > * `ws`— Obszar roboczy Azure Machine Learning.
    > * `model`-Zarejestrowany model, który zostanie wdrożony.
    > * `inference_config`-Konfiguracja wnioskowania dla modelu.
    >
    > Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem należy zdefiniować, co jest potrzebne do uruchomienia modelu jako usługi sieci Web. Na poniższej liście opisano podstawowe elementy, które są związane z wdrożeniem:

* __Skrypt wejściowy__. Ten skrypt akceptuje żądania, ocenia żądanie przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > Skrypt wejścia jest specyficzny dla modelu; musi on zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwracanych do klientów.
    >
    > Jeśli dane żądania są w formacie, którego nie można używać w modelu, skrypt może przekształcić go w akceptowalny format. Może również przekształcić odpowiedź przed powrotem do klienta programu.

    > [!IMPORTANT]
    > Zestaw SDK Azure Machine Learning nie pozwala na dostęp usługi sieci Web do magazynu danych ani Twoich zestawów. Jeśli chcesz, aby wdrożony model miał dostęp do danych przechowywanych poza wdrożeniem, na przykład na koncie usługi Azure Storage, musisz opracować niestandardowe rozwiązanie kodu przy użyciu odpowiedniego zestawu SDK. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python).
    >
    > Kolejną alternatywą, która może posłużyć do danego scenariusza, są [przewidywania wsadowe](how-to-run-batch-predictions.md), które zapewniają dostęp do magazynów danych podczas oceniania.

    Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

* **Zależności**, takie jak skrypty pomocnika lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wprowadzania

Te jednostki są hermetyzowane w __konfiguracji wnioskowania__. Konfiguracja wnioskowania odwołuje się do skryptu wejścia i innych zależności.

> [!IMPORTANT]
> Podczas tworzenia konfiguracji wnioskowania do użytku z Azure App Service należy użyć obiektu [środowiska](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . Poniższy przykład ilustruje tworzenie obiektu środowiska i używanie go z konfiguracją wnioskowania:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Podczas wdrażania programu w celu Azure App Service nie trzeba tworzyć __konfiguracji wdrożenia__.

## <a name="create-the-image"></a>Tworzenie obrazu

Aby utworzyć obraz platformy Docker wdrożony w Azure App Service, użyj [modelu model. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). Poniższy fragment kodu przedstawia sposób tworzenia nowego obrazu z konfiguracji modelu i wnioskowania:

> [!NOTE]
> W fragmencie kodu założono `model` , że zawiera zarejestrowany model `inference_config` i zawiera konfigurację środowiska wnioskowania. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Gdy `show_output=True`są wyświetlane dane wyjściowe procesu kompilacji platformy Docker. Po zakończeniu procesu obraz został utworzony w Azure Container Registry dla obszaru roboczego. Po skompilowaniu obrazu zostanie wyświetlona lokalizacja w Azure Container Registry. Zwrócona Lokalizacja ma format `<acrinstance>.azurecr.io/package:<imagename>`. Na przykład `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Zapisz informacje o lokalizacji, ponieważ są używane podczas wdrażania obrazu.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Użyj poniższego polecenia, aby uzyskać poświadczenia logowania dla Azure Container Registry zawierającej obraz. Zamień `<acrinstance>` na wartość e zwracaną wcześniej z `package.location`: 

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

    Zapisz wartość dla __nazwy użytkownika__ i jednego z __haseł__.

1. Jeśli nie masz jeszcze grupy zasobów lub planu usługi App Service do wdrożenia usługi, następujące polecenia pokazują, jak utworzyć obie:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    W tym przykładzie jest używana __podstawowa__ warstwa cenowa`--sku B1`().

    > [!IMPORTANT]
    > Obrazy utworzone przez usługę Azure Machine Learning korzystają z systemu Linux, dlatego należy użyć `--is-linux` parametru.

1. Aby utworzyć aplikację sieci Web, użyj następującego polecenia. Zamień `<app-name>` na nazwę, której chcesz użyć. Zamień `<acrinstance>` `package.location` i `<imagename>` na wartości zwracane wcześniej:

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
    > W tym momencie aplikacja sieci Web została utworzona. Jednak ponieważ nie podano poświadczeń do Azure Container Registry, które zawierają obraz, aplikacja sieci Web nie jest aktywna. W następnym kroku podajesz informacje o uwierzytelnianiu dla rejestru kontenerów.

1. Aby zapewnić aplikacji sieci Web poświadczenia potrzebne do uzyskania dostępu do rejestru kontenerów, użyj następującego polecenia. Zamień `<app-name>` na nazwę, której chcesz użyć. Zamień `<acrinstance>` `package.location` i `<imagename>` na wartości zwracane wcześniej. Zamień `<username>` i`<password>` na pobrane wcześniej informacje logowania ACR:

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

W tym momencie aplikacja sieci Web rozpocznie ładowanie obrazu.

> [!IMPORTANT]
> Załadowanie obrazu może potrwać kilka minut. Aby monitorować postęp, użyj następującego polecenia:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Po załadowaniu obrazu, gdy lokacja jest aktywna, w dzienniku zostanie wyświetlony komunikat z informacją `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`o stanie.

Po wdrożeniu obrazu można znaleźć nazwę hosta za pomocą następującego polecenia:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

To polecenie zwraca informacje podobne do następującej nazwy hosta `<app-name>.azurewebsites.net`. Użyj tej wartości jako części __podstawowego adresu URL__ usługi.

## <a name="use-the-web-app"></a>Korzystanie z aplikacji sieci Web

Usługa sieci Web, która przekazuje żądania do modelu, znajduje się `{baseurl}/score`w lokalizacji. Na przykład `https://<app-name>.azurewebsites.net/score`. Poniższy kod w języku Python pokazuje, jak przesłać dane do adresu URL i wyświetlić odpowiedź:

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

* Aby uzyskać więcej informacji na temat konfigurowania aplikacji sieci Web, zapoznaj się z dokumentacją [App Service w systemie Linux](/azure/app-service/containers/) .
* Aby uzyskać więcej informacji na temat skalowania, zobacz [Rozpoczynanie pracy z funkcją automatycznego skalowania na platformie Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Aby uzyskać więcej informacji na temat obsługi protokołu SSL, zobacz [Używanie certyfikatu SSL w Azure App Service](/azure/app-service/app-service-web-ssl-cert-load).
* Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [Konfigurowanie aplikacji App Service do korzystania z Azure Active Directory logowania](/azure/app-service/configure-authentication-provider-aad).
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)