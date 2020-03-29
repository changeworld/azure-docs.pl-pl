---
title: Wdrażanie modeli ml w usłudze Azure Functions Apps (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą usługi Azure Machine Learning wdrożyć model w aplikacji usługi Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927437"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Wdrażanie modelu uczenia maszynowego w usłudze Azure Functions (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak wdrożyć model z usługi Azure Machine Learning jako aplikację funkcji w usłudze Azure Functions.

> [!IMPORTANT]
> Chociaż usługa Azure Machine Learning i usługi Azure Functions są ogólnie dostępne, możliwość pakowania modelu z usługi uczenia maszynowego dla funkcji jest w wersji zapoznawczej.

Za pomocą usługi Azure Machine Learning można tworzyć obrazy platformy Docker na podstawie wyszkolonych modeli uczenia maszynowego. Usługa Azure Machine Learning ma teraz funkcję w wersji zapoznawczej do tworzenia tych modeli uczenia maszynowego w aplikacjach funkcji, które można [wdrożyć w usłudze Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)

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
    >
    > Domyślnie podczas pakowania funkcji dane wejściowe są traktowane jako tekst. Jeśli chcesz spożywać nieprzetworzone bajty danych wejściowych (na przykład dla wyzwalaczy obiektów Blob), należy użyć [AMLRequest do akceptowania nieprzetworzonych danych](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).


* **Zależności**, takie jak skrypty pomocnicze lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wejścia

Te jednostki są hermetyzowane __w konfiguracji wnioskowania__. Konfiguracja wnioskowania przywołuje skrypt wejściowy i inne zależności.

> [!IMPORTANT]
> Podczas tworzenia konfiguracji wnioskowania do użytku z usługą Azure Functions, należy użyć [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) obiektu. Należy pamiętać, że jeśli definiujesz środowisko niestandardowe, należy dodać azureml-defaults z wersją >= 1.0.45 jako zależności pipsa. Ten pakiet zawiera funkcje potrzebne do obsługi modelu jako usługi sieci web. Poniższy przykład pokazuje tworzenie obiektu środowiska i używanie go z konfiguracją wnioskowania:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
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
> Podczas wdrażania w funkcji, nie trzeba tworzyć __konfiguracji wdrożenia__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalowanie pakietu podglądu zestawu SDK w celu obsługi funkcji

Aby utworzyć pakiety dla usługi Azure Functions, należy zainstalować pakiet w wersji zapoznawczej zestawu SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Tworzenie obrazu

Aby utworzyć obraz platformy Docker, który jest wdrażany w usłudze Azure Functions, użyj [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) lub funkcji określonego pakietu dla wyzwalacza, który jest zainteresowany przy użyciu. Poniższy fragment kodu pokazuje, jak utworzyć nowy pakiet z wyzwalaczem obiektu blob z modelu i konfiguracji wnioskowania:

> [!NOTE]
> Fragment kodu zakłada, że `model` zawiera zarejestrowany model i `inference_config` który zawiera konfigurację dla środowiska wnioskowania. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Kiedy `show_output=True`jest wyświetlany wynik procesu kompilacji platformy Docker. Po zakończeniu procesu obraz został utworzony w rejestrze kontenerów platformy Azure dla obszaru roboczego. Po zbudowaniu obrazu zostanie wyświetlona lokalizacja w rejestrze kontenerów platformy Azure. Zwrócona lokalizacja jest `<acrinstance>.azurecr.io/package@sha256:<hash>`w formacie .

> [!NOTE]
> Pakowanie dla funkcji obsługuje obecnie wyzwalacze HTTP, wyzwalacze obiektów Blob i wyzwalacze magistrali usług. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [powiązania usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns).

> [!IMPORTANT]
> Zapisz informacje o lokalizacji, ponieważ jest on używany podczas wdrażania obrazu.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Użyj następującego polecenia, aby uzyskać poświadczenia logowania dla rejestru kontenerów platformy Azure, który zawiera obraz. Zastąp `<myacr>` wartością `package.location`zwróconą wcześniej z: 

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

    W tym przykładzie używana jest`--sku B1` _podstawowa_ warstwa cenowa systemu Linux ( ).

    > [!IMPORTANT]
    > Obrazy utworzone przez usługę Azure Machine Learning `--is-linux` używają systemu Linux, więc należy użyć tego parametru.

1. Utwórz konto magazynu do użycia w magazynie zadań sieci Web i pobierz jego parametry połączenia. Zamień `<webjobStorage>` na nazwę, której chcesz użyć.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Aby utworzyć aplikację funkcji, użyj następującego polecenia. Zamień `<app-name>` na nazwę, której chcesz użyć. Zamień `<acrinstance>` i `<imagename>` wartości `package.location` z zwróconych wcześniej. Zastąp `<webjobStorage>` nazwą konta magazynu z poprzedniego kroku:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > W tym momencie aplikacja funkcji została utworzona. Jednak ponieważ nie podano parametry połączenia dla wyzwalacza obiektu blob lub poświadczeń do rejestru kontenerów platformy Azure, który zawiera obraz, aplikacja funkcji nie jest aktywna. W następnych krokach należy podać parametry połączenia i informacje uwierzytelniania dla rejestru kontenerów. 

1. Utwórz konto magazynu do użycia dla magazynu wyzwalacza obiektu blob i pobierz jego parametry połączenia. Zamień `<triggerStorage>` na nazwę, której chcesz użyć.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Zarejestruj ten ciąg połączenia, aby zapewnić aplikację funkcji. Wykorzystamy go później, gdy poprosimy o`<triggerConnectionString>`

1. Utwórz kontenery dla danych wejściowych i wyjściowych na koncie magazynu. Zamień `<triggerConnectionString>` na wcześniej zwrócony ciąg połączenia:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Aby skojarzyć ciąg połączenia wyzwalacza z aplikacją funkcyjną, użyj następującego polecenia. Zamień `<app-name>` na nazwę aplikacji funkcyjnej. Zamień `<triggerConnectionString>` na wcześniej zwrócony ciąg połączenia:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Należy pobrać tag skojarzony z utworzonym kontenerem za pomocą następującego polecenia. Zamień `<username>` na nazwę użytkownika zwróconą wcześniej z rejestru kontenerów:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Zapisz zwróconą wartość, będzie on `imagetag` używany jako w następnym kroku.

1. Aby zapewnić aplikacji funkcji poświadczenia potrzebne do uzyskania dostępu do rejestru kontenerów, należy użyć następującego polecenia. Zamień `<app-name>` na nazwę aplikacji funkcyjnej. Zamień `<acrinstance>` i `<imagetag>` wartości z wywołania interfejsu wiersza polecenia AZ w poprzednim kroku. Zastąp `<username>` i `<password>` z informacjami logowania ACR pobranymi wcześniej:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

W tym momencie aplikacja funkcji rozpoczyna ładowanie obrazu.

> [!IMPORTANT]
> Może upłynąć kilka minut, zanim obraz zostanie załadowany. Postęp można monitorować za pomocą usługi Azure Portal.

## <a name="test-the-deployment"></a>Testowanie wdrożenia

Po załadowaniu obrazu i udostępnieniu aplikacji użyj następujących czynności, aby wyzwolić aplikację:

1. Utwórz plik tekstowy zawierający dane, których oczekuje score.py plik. Poniższy przykład może działać z score.py, który oczekuje tablicy 10 liczb:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Format danych zależy od tego, czego oczekuje score.py i model.

2. Użyj następującego polecenia, aby przekazać ten plik do kontenera wejściowego w obiekcie blob magazynu wyzwalacza utworzonym wcześniej. Zastąp `<file>` nazwą pliku zawierającego dane. Zamień `<triggerConnectionString>` na wcześniej zwrócony ciąg połączenia. W tym `input` przykładzie jest nazwa kontenera wejściowego utworzonego wcześniej. Jeśli użyto innej nazwy, zastąp tę wartość:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Dane wyjściowe tego polecenia są podobne do następującego JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Aby wyświetlić dane wyjściowe generowane przez funkcję, użyj następującego polecenia, aby wyświetlić listę wygenerowanych plików wyjściowych. Zamień `<triggerConnectionString>` na wcześniej zwrócony ciąg połączenia. W tym `output` przykładzie jest nazwa kontenera wyjściowego utworzonego wcześniej. Jeśli użyto innej nazwy, zastąp tę wartość::

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    Dane wyjściowe tego polecenia `sample_input_out.json`są podobne do .

4. Aby pobrać plik i sprawdzić zawartość, użyj następującego polecenia. Zamień `<file>` na nazwę pliku zwróconą przez poprzednie polecenie. Zamień `<triggerConnectionString>` na wcześniej zwrócony ciąg połączenia: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Po zakończeniu polecenia otwórz plik. Zawiera dane zwrócone przez model.

Aby uzyskać więcej informacji na temat używania wyzwalaczy obiektów blob, zobacz [Tworzenie funkcji wyzwalane przez usługę Azure Blob storage](/azure/azure-functions/functions-create-storage-blob-triggered-function) artykułu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować aplikację Functions w dokumentacji [funkcji.](/azure/azure-functions/functions-create-function-linux-custom-image)
* Dowiedz się więcej o magazynie obiektów Blob wyzwala [powiązania magazynu obiektów Blob platformy Azure.](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)
* [Wdrażanie modelu w usłudze Azure App Service](how-to-deploy-app-service.md).
* [Korzystanie z modelu ml wdrożonego jako usługa sieci web](how-to-consume-web-service.md)
* [Dokumentacja interfejsu API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
