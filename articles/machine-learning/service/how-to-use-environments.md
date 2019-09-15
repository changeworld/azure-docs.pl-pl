---
title: Twórz i wdrażaj środowiska oraz zarządzaj nimi, aby korzystać z szkoleń i wdrażania modelu
titleSuffix: Azure Machine Learning
description: Twórz środowiska i zarządzaj nimi, aby uzyskać model szkoleń i wdrożeń. Zarządzaj pakietami języka Python i innymi ustawieniami środowiska.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: seodec18
ms.openlocfilehash: 242f3cec0ac766e537bd0483725ba51685bb7ced
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996616"
---
# <a name="create-and-manage-environments-for-training-and-deployment"></a>Twórz środowiska szkoleniowe i wdrażaj i zarządzaj nimi

W tym artykule dowiesz się, jak tworzyć [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning i zarządzać nimi, aby umożliwić śledzenie i odtwarzanie zależności oprogramowania w trakcie ich rozwoju.

Zarządzanie zależnościami oprogramowania to typowe zadanie dla deweloperów. Chcesz mieć pewność, że kompilacje zostaną odtwarzalne bez konieczności ręcznej konfiguracji oprogramowania. Dzięki rozwiązaniom na potrzeby lokalnego projektowania, takim jak PIP i Conda, Klasa środowisk Azure Machine Learning udostępnia rozwiązanie do programowania w chmurze lokalnej i rozproszonej.

W przykładach w tym artykule przedstawiono sposób wykonywania tych operacji:

* Tworzenie środowiska i określanie zależności pakietu
* Pobieranie i aktualizowanie środowisk
* Używanie środowiska do szkolenia
* Używanie środowiska do wdrażania usług sieci Web

## <a name="what-are-environments"></a>Co to są środowiska

Środowiska określają pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleniowych i oceniających oraz czasy uruchamiania (Python, Spark lub Docker). Są one zarządzane i obsługiwane przez program w obszarze roboczym Azure Machine Learning, które umożliwiają powtarzalny, z inspekcją i przenośne przepływy pracy uczenia maszynowego między różnymi obiektami docelowymi obliczeniowymi.

Możesz użyć obiektu środowiska na lokalnym obliczeniu do opracowania skryptu szkoleniowego i ponownie użyć tego samego środowiska na Azure Machine Learning obliczeń dla szkolenia modeli w dużej skali, a nawet wdrożyć model przy użyciu tego samego środowiska.

Poniżej przedstawiono, że ten sam obiekt środowiska może być używany zarówno w konfiguracji uruchamiania na potrzeby szkoleń, jak i w konfiguracji inferencing i wdrożenia dla wdrożeń usług sieci Web.

![Diagram środowiska w przepływie pracy uczenia maszynowego](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>Typy środowiska

Środowiska można w szerokim zakresie podzielić na dwie kategorie: **zarządzane przez użytkownika** i **zarządzane przez system**.

W przypadku środowiska zarządzanego przez użytkownika użytkownik jest odpowiedzialny za skonfigurowanie środowiska i zainstalowanie każdego pakietu potrzebnych do wykonywania skryptów szkoleniowych w miejscu docelowym obliczeń. Conda nie sprawdzi Twojego środowiska ani nie zainstaluje żadnego z nich. 

Środowiska zarządzane przez system są używane, gdy chcesz [Conda](https://conda.io/docs/) zarządzać środowiskiem Python i zależnościami skryptów. Usługa domyślnie przyjmuje ten typ środowiska, ze względu na jego użyteczność w przypadku zdalnych obiektów docelowych obliczeń, które nie są ręcznie konfigurowane.

## <a name="prerequisites"></a>Wymagania wstępne

* Azure Machine Learning zestawu SDK dla języka [Python.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Tworzenie środowiska

Istnieje wiele sposobów tworzenia środowiska dla eksperymentów.

### <a name="instantiate-an-environment-object"></a>Tworzenie wystąpienia obiektu środowiska

Aby ręcznie utworzyć środowisko, zaimportuj klasę środowiska z zestawu SDK i Utwórz wystąpienie obiektu środowiska z poniższym kodem.

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Pliki specyfikacji Conda i PIP

Możesz również utworzyć środowisko na podstawie specyfikacji Conda lub pliku wymagań PIP.
Użyj metody [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) lub [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) i Uwzględnij nazwę środowiska i ścieżkę pliku żądanego pliku w argumencie metody.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Istniejące środowisko Conda

Jeśli masz istniejące środowisko Conda na komputerze lokalnym, usługa oferuje rozwiązanie do tworzenia obiektu środowiska. W ten sposób można ponownie użyć lokalnego środowiska interaktywnego na zdalnych uruchomieniach.

Następujące tworzy obiekt środowiska poza istniejącym środowiskiem `mycondaenv` Conda z metodą [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Automatycznie Twórz środowiska

Automatyczne tworzenie środowiska przez przesłanie szkolenia uruchamianego za pomocą metody Submit (). Podczas przesyłania przebiegu szkoleniowego Kompilowanie nowego środowiska może potrwać kilka minut w zależności od rozmiaru wymaganych zależności. 

Jeśli nie określisz środowiska w konfiguracji uruchomieniowej przed przesłaniem przebiegu, zostanie utworzone środowisko domyślne.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

Podobnie, jeśli używasz [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) obiektu do szkolenia, możesz przesłać wystąpienie szacowania bezpośrednio jako przebieg, bez konieczności określania środowiska, jest to `Estimator` spowodowane tym, że obiekt już hermetyzuje środowisko i element docelowy obliczeń.


## <a name="add-packages-to-an-environment"></a>Dodawanie pakietów do środowiska

Dodaj pakiety do środowiska przy użyciu plików Conda, PIP lub Private koła. Określ każdą zależność pakietu przy użyciu [klasy CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)i Dodaj ją do PythonSection środowiska.

### <a name="conda-and-pip-packages"></a>Pakiety Conda i PIP

Jeśli pakiet jest dostępny w repozytorium pakietu Conda, zaleca się użycie instalacji Conda przez PIP. Przyczyną jest to, że pakiety Conda zazwyczaj pochodzą z wstępnie skompilowanych plików binarnych, co sprawia, że instalacja jest bardziej niezawodna.

Poniższy `scikit-learn`przykład dodaje odpowiednio wersję 0.21.3 i `myenv` `pillow` pakiet do środowiska przy użyciu [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) metod i [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) .

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Pliki kół prywatnych

Możesz użyć prywatnych plików kółka PIP, najpierw przekazując je do magazynu obszaru roboczego przy użyciu metody [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) statycznej, a następnie przechwytując adres URL magazynu i przekazując adres URL `add_pip_package()` do metody

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Zarządzanie środowiskami

Zarządzaj środowiskami, aby można było je aktualizować, śledzić i ponownie używać w celach obliczeniowych i z innymi użytkownikami obszaru roboczego.

### <a name="register-environments"></a>Zarejestruj środowiska

Środowisko jest automatycznie rejestrowane w obszarze roboczym podczas przesyłania uruchomienia lub wdrożenia usługi sieci Web. Możesz również ręcznie zarejestrować środowisko przy użyciu metody [Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Ta operacja powoduje, że środowisko jest śledzone i używane w chmurze i może być współużytkowane przez użytkowników obszaru roboczego.

Następujące rejestruje środowisko, `myenv`w `ws`obszarze roboczym.

```python
myenv.register(workspace=ws)
```

Gdy jest używany po raz pierwszy, w szkole lub wdrożeniu środowisko jest rejestrowane w obszarze roboczym, skompilowany i wdrożony w elemencie docelowym obliczeń. Środowiska są buforowane przez usługę. W przypadku korzystania z pamięci podręcznej trwa znacznie mniej czasu niż w przypadku użycia nowej usługi lub aktualizacji, która została zaktualizowana.

### <a name="get-existing-environments"></a>Pobierz istniejące środowiska

Klasa Environment oferuje metody, które umożliwiają pobieranie istniejących środowisk w obszarze roboczym według nazwy, jako listę lub według określonego przebiegu szkoleniowego. Na potrzeby rozwiązywania problemów lub inspekcji, odtwarzalność

#### <a name="view-list-of-environments"></a>Wyświetl listę środowisk

Wyświetl środowiska w obszarze roboczym z [listą ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-), a następnie wybierz jeden do ponownego użycia.

```python
from azureml.core import Environment
list("workspace_name")
```

#### <a name="get-environment-by-name"></a>Pobierz środowisko według nazwy

Możesz również uzyskać określone środowisko według nazwy i wersji.
Poniższy kod używa metody [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) do pobierania wersji `1` `ws` środowiska, `myenv` w obszarze roboczym.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Szkolenie dotyczące uruchamiania określonego środowiska

Aby uzyskać środowisko używane dla określonego uruchomienia po zakończeniu szkolenia, użyj metody [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)w klasie Run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aktualizowanie istniejącego środowiska

Jeśli wprowadzisz zmiany w istniejącym środowisku, na przykład Dodaj pakiet języka Python, Nowa wersja środowiska jest tworzona podczas przesyłania przebiegu, wdrażania modelu lub ręcznego rejestrowania środowiska. Wersja umożliwia wyświetlanie zmian w środowisku w miarę upływu czasu.

Aby zaktualizować wersję pakietu języka Python istniejącego środowiska, określ dokładny numer wersji dla tego pakietu. W przeciwnym razie Azure Machine Learning będzie używać istniejącego środowiska z wersjami pakietu z programu podczas tworzenia środowiska.

### <a name="debug-the-image-build"></a>Debuguj kompilację obrazu

W tym przykładzie użyto metody [Build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) , aby ręcznie utworzyć środowisko jako obraz platformy Docker, a następnie monitoruje dzienniki wyjściowe z kompilacji obrazu przy użyciu [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). Utworzony obraz zostanie wyświetlony w obszarze roboczym Azure Container Registry, który jest przydatny do debugowania.

```python
from azureml.core import Image
build = env.build()
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Platformy Docker i środowiska

 [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) klasy Azure Machine Learning `Environments` , pozwala na szczegółowe dostosowanie i kontrolę w systemie operacyjnym gościa, w którym jest wykonywane szkolenie.

`enable` Podczas dokowania usługa kompiluje obraz platformy Docker i tworzy środowisko Python ze specyfikacją w tym kontenerze platformy Docker. Zapewnia to dodatkową izolację i odtwarzalność dla przebiegów szkoleniowych.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Po skompilowaniu obraz platformy Docker zostanie wyświetlony w Azure Container Registry, która jest domyślnie skojarzona z obszarem roboczym.  Nazwa repozytorium ma postać " *Azure/azureml_\<UUID\>* ". Unikatowy identyfikator (*uuuid*) odpowiada skrótowi obliczonemu na podstawie konfiguracji środowiska. Dzięki temu usługa może określić, czy obraz odpowiadający danemu środowisku już istnieje do ponownego użycia.

Ponadto usługa automatycznie używa jednego z [obrazów podstawowych](https://github.com/Azure/AzureML-Containers)opartych na Ubuntu Linux i instaluje określone pakiety języka Python. Obraz podstawowy ma wersje procesora CPU i procesora GPU i można określić obraz procesora GPU według ustawienia `gpu_support=True`.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"

# Specify GPU image
myenv.docker.gpu_support=True
```

> [!NOTE]
> W przypadku określenia `environment.python.user_managed_dependencies=False` przy użyciu niestandardowego obrazu platformy Docker usługa utworzy środowisko Conda w obrazie i wykona uruchomienie w tym środowisku, zamiast korzystać z bibliotek języka Python, które mogą być zainstalowane na podstawowym obrazie. Ustaw parametr `True` na, aby można było korzystać z własnych zainstalowanych pakietów.

## <a name="using-environments-for-training"></a>Używanie środowisk do szkolenia

Aby przesłać uruchomienie szkoleniowe, należy połączyć środowisko, [element docelowy obliczeń](concept-compute-target.md) i szkoleniowy skrypt języka Python do konfiguracji przebiegu; Obiekt otoki używany do przesyłania przebiegów.

Podczas przesyłania przebiegu szkoleniowego Kompilowanie nowego środowiska może potrwać kilka minut w zależności od rozmiaru wymaganych zależności. Środowiska są przechowywane w pamięci podręcznej przez usługę, w związku z czym cały czas instalacji zostanie naliczony tylko raz.

Poniżej znajduje się przykład lokalnego uruchomienia skryptu, w którym można użyć [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) jako obiektu otoki.

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.compute_target = "local"

# Attach environment to run config
runconfig.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Aby wyłączyć historię uruchamiania lub uruchomić migawki, użyj ustawienia w obszarze `ScriptRunConfig.run_config.history`.

Jeśli nie określisz środowiska w konfiguracji uruchomieniowej, usługa utworzy środowisko domyślne podczas przesyłania przebiegu.

### <a name="train-with-an-estimator"></a>Szkolenie przy użyciu narzędzie do szacowania

Jeśli używasz [szacowania](how-to-train-ml-models.md) do szkolenia, możesz po prostu przesłać wystąpienie szacowania bezpośrednio, ponieważ już hermetyzuje środowisko i element docelowy obliczeń.

Poniższe użycie szacowania do szkolenia z jednym węzłem odbywa się w ramach zdalnego obliczenia dla modelu scikit-uczenia i zakłada, że wcześniej utworzony obiekt `compute_target` docelowy obliczeń i `ds`obiekt magazynu danych.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="using-environments-for-web-service-deployment"></a>Używanie środowisk do wdrażania usług sieci Web

Środowiska można używać podczas wdrażania modelu jako usługi sieci Web. Umożliwia to przekształcenie, połączony przepływ pracy, w którym można nauczyć, testować i wdrażać model przy użyciu dokładnie tych samych bibliotek zarówno w przypadku szkoleń, jak i inferencing.

Aby wdrożyć usługę sieci Web, Połącz środowisko, obliczenia wnioskowania, skrypt oceniania i zarejestrowany model w obiekcie wdrożenia, [Wdróż ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-). Dowiedz się więcej o [wdrażaniu usług sieci Web](how-to-deploy-and-where.md).

W tym przykładzie przyjęto założenie, że został ukończony przebieg szkoleniowy, który ma zostać wdrożony w usłudze Azure Container Instance (ACI). Podczas kompilowania usługi sieci Web pliki modelu i oceny są instalowane na obrazie, a Azure Machine Learning stos inferencing jest dodawany do obrazu.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Przykład notesów

Ten [przykładowy Notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) rozwija się wraz z pojęciami i metodami pokazanymi w tym artykule.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* Wyświetl odwołanie do zestawu SDK [klasy środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .
