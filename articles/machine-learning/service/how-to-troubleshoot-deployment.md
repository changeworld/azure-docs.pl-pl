---
title: Wdrożenia przewodnik rozwiązywania problemów dla usługi Azure Machine Learning
description: Dowiedz się, jak obejść, rozwiązania i rozwiązywanie typowych problemów wdrażania platformy Docker za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 10/01/2018
ms.openlocfilehash: 5c5468619533e66ddaac352dea8bdcbc9616b10d
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242969"
---
# <a name="troubleshoot-your-azure-machine-learning-service-deployments"></a>Rozwiązywanie problemów z wdrożenia usługi Azure Machine Learning

W tym artykule dowiesz się, jak obejść lub Rozwiązywanie typowych błędów wdrażania platformy Docker za pomocą usługi Azure Machine Learning.

W przypadku wdrażania modelu w usłudze Azure Machine Learning, system wykonuje wiele zadań. Jest to złożonej sekwencji zdarzeń, a czasami pojawiają się problemy. Dostępne są następujące zadania wdrażania:

1. W rejestrze modelu obszaru roboczego, należy zarejestrować model.

2. Zbuduj obraz Docker, w tym:
    1. Pobierz zarejestrowanego modelu z rejestru. 
    2. Utwórz plik dockerfile, za pomocą środowiska Python na podstawie zależności, określonych w pliku yaml środowiska.
    3. Dodawanie plików modelu, a skrypt oceniania, które należy podać w pliku dockerfile.
    4. Twórz nowy obraz platformy Docker przy użyciu pliku dockerfile.
    5. Zarejestruj obrazu platformy Docker za pomocą usługi Azure Container Registry, skojarzone z obszarem roboczym.

3. Wdrażanie obrazu platformy Docker do usługi Azure Container wystąpienia (ACI) lub do usługi Azure Kubernetes Service (AKS).

4. Uruchom nowy kontener (lub kontenery) w usłudze ACI i AKS. 

Dowiedz się więcej na temat tego procesu w [zarządzania modelami](concept-model-management-and-deployment.md) wprowadzenie.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli napotkasz problem z dowolnym najpierw musisz jest podzielenie zadania wdrożenia (opisany poprzedniego) na poszczególne kroki, aby ustalić przyczynę problemu. 

Jest to szczególnie przydatne, jeśli używasz `Webservice.deploy` interfejsu API, lub `Webservice.deploy_from_model` interfejsu API, ponieważ te funkcje zgrupować wyżej czynności w ramach jednej akcji. Zazwyczaj te interfejsy API są dość wygodne, ale warto podzielić kroki podczas rozwiązywania problemów. 

Poniżej przedstawiono funkcje do użycia podczas istotne wdrożenia kroki w dół:

1. Należy zarejestrować model. Poniżej przedstawiono przykładowy kod:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Tworzenie obrazu. Poniżej przedstawiono przykładowy kod:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=img_cfg, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Obraz, który można wdrożyć jako usługę. Poniżej przedstawiono przykładowy kod:

    ```python
    # configure an ACI-based deployment
    aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aciconfig, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Gdy zostały podzielone procesu wdrażania na poszczególne zadania, można przyjrzymy się niektóre z najbardziej typowych błędów.

## <a name="image-building-fails"></a>Obraz tworzenia kończy się niepowodzeniem
Jeśli system jest w stanie utworzyć obraz platformy Docker `image.wait_for_creation` wywołanie zakończy się niektóre komunikaty o błędach. Można również znaleźć więcej szczegółowych informacji o błędzie w dzienniku kompilacji obrazu. Identyfikator uri dziennika jest adres URL sygnatury dostępu Współdzielonego do pliku dziennika przechowywanych w usłudze Azure blob storage. Po prostu skopiuj i wklej identyfikator uri do okna przeglądarki i możesz pobrać i przejrzeć dziennik.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items()
    print (img.name, img.version, img.image_build_log_uri)
```

## <a name="service-launch-fails"></a>Uruchomienie usługi nie powiedzie się.
Po pomyślnym utworzeniu obrazu, system próbuje uruchomić kontener w usłudze ACI i AKS w zależności od konfiguracji wdrożenia. Zalecane jest zawsze najpierw spróbuj wdrożenia usługi ACI, ponieważ jest to wdrożenie prostego jeden kontener. Dzięki temu można następnie wykluczyć problemy specyficzne dla usługi AKS.

Jako część procesu rozruchu kontenera `init()` funkcji oceniania skryptu zostanie wywołany przez system. Jeśli istnieją nieobsłużone wyjątki w `init()` funkcji, można napotkać **CrashLoopBackOff** błąd w komunikacie o błędzie. Poniżej przedstawiono kilka wskazówek, aby rozwiązać ten problem.

### <a name="inspect-the-docker-log"></a>Sprawdź dziennik platformy Docker
Jeśli obraz jest kompilowany pomyślnie, ale wystąpiły błędy podczas wdrażania obrazu jako kontener, możesz dowiedzieć się komunikat o błędzie w dzienniku platformy Docker.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Debuguj lokalnie za pomocą obrazu platformy Docker
Sytuacje dziennik platformy Docker nie emituje wystarczającej ilości informacji o co będzie problem. Można wykonaj krok dalej i ściągnąć utworzony obraz platformy Docker, uruchom lokalny kontener i interakcyjnie debugować bezpośrednio w kontenerze na żywo. Aby uruchomić lokalny kontener, konieczne jest posiadanie aparat platformy Docker działa lokalnie, a będzie o wiele prostsze, jeśli masz też [wiersza polecenia platformy azure](/cli/azure/install-azure-cli?view=azure-cli-latest) zainstalowane.

Najpierw musimy sprawdzić lokalizację obrazu:

```python
# print image location
print(image.image_location)
```

Lokalizacja obrazu ma następujący format: `<acr-name>.azurecr.io/<image-name>:<version-number>`, takich jak `myworkpaceacr.azurecr.io/myimage:3`. 

Teraz przejdź do okna wiersza polecenia i wpisz następujące polecenia, aby zalogować się do usługi ACR (Azure Container Registry) skojarzone z obszarem roboczym, w którym jest przechowywany obraz.

```sh
# note the acr_name is just the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Jeśli nie masz azure-cli zainstalowana, można również użyć `docker login` polecenie, aby zalogować się do usługi ACR. Wystarczy pobrać nazwę użytkownika i hasło usługi ACR z witryny Azure portal.

Teraz można ściągnąć obraz platformy Docker i uruchomić kontenera w środowisku lokalnym, a następnie uruchom sesji programu bash do debugowania.

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

Po uruchomieniu sesji programu bash działający kontener, można znaleźć oceniania skrypty w `/var/azureml-app` folderu. Następnie można uruchomić sesję języka Python, aby debugować skrypty oceniania. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
W przypadku, gdy potrzebujesz edytora tekstów, aby zmodyfikować skrypty, można zainstalować vim, nano, Emacs lub innych ulubionego edytora.

```sh
# update package index
apt-get update

# install text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Można również uruchomić lokalnie usługi sieci web i wysyłać ruch HTTP. Serwer Flask, w kontenerze platformy Docker jest uruchomiony na porcie 5001. Można mapować do dowolnych portów dostępnych na komputerze hosta.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Funkcja kończy się niepowodzeniem: get_model_path()
Często w `init()` funkcji w skrypt oceniania `Model.get_model_path()` funkcja jest wywoływana, aby zlokalizować plik modelu lub folder z plikami w kontenerze. Jest to często źródło błędu, jeśli nie można odnaleźć modelu pliku lub folderu. Najprostszym sposobem, aby debugować ten błąd jest uruchomienie poniższego kodu języka Python w powłoce kontenera:

```python
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Wypisuje to ścieżka lokalna, że skrypt oceniania jest oczekiwano modelu pliku lub folderu. Następnie można sprawdzić, czy plik lub folder jest w rzeczywistości których oczekuje się, można.


## <a name="function-fails-runinputdata"></a>Funkcja kończy się niepowodzeniem: run(input_data)
Jeśli usługa została pomyślnie wdrożona, a jego ulega awarii, gdy opublikujesz danych do punktu końcowego oceniania, można dodać błąd Przechwytywanie instrukcji w swojej `run(input_data)` funkcji Podziel się komunikat o błędzie. Na przykład:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o wdrażaniu: 
* [Jak wdrożyć w usłudze ACI](how-to-deploy-to-aci.md)

* [Sposób wdrażania usługi AKS](how-to-deploy-to-aks.md)

* [Samouczek, część 1: uczenie modelu](tutorial-train-models-with-aml.md)

* [Samouczek, część 2: Wdrażanie modelu](tutorial-deploy-models-with-aml.md)
