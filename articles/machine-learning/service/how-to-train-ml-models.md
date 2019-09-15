---
title: Uczenie modeli ML z szacowania
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wykonywać szkolenia Jednowęzłowe i rozproszone dla tradycyjnych modeli uczenia maszynowego i uczenia głębokiego przy użyciu Azure Machine Learning klasie szacowania
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 041f80937e3ebae15dd5bd64858ccbd8269104a0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002580"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Uczenie modeli za pomocą Azure Machine Learning przy użyciu szacowania

Za pomocą Azure Machine Learning można łatwo przesłać skrypt szkoleniowy do [różnych obiektów docelowych obliczeń](how-to-set-up-training-targets.md#compute-targets-for-training)przy użyciu [obiektu RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) i [obiektu ScriptRunConfig](how-to-set-up-training-targets.md#submit). Ten wzorzec zapewnia dużą elastyczność i maksymalną kontrolę.

Aby ułatwić szkolenie modelu uczenia głębokiego, zestaw Azure Machine Learning Python SDK udostępnia alternatywne abstrakcję wyższego poziomu, klasy szacowania, która umożliwia użytkownikom łatwe konstruowanie konfiguracji uruchomieniowych. Można utworzyć i użyć generycznej [szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) do przesyłania skryptu szkoleniowego za pomocą wybranej platformy szkoleniowej (na przykład scikit-Learning) na dowolnym wybranym miejscu docelowym obliczeń, niezależnie od tego, czy jest to maszyna lokalna, jedna maszyna wirtualna na platformie Azure czy klaster GPU na platformie Azure. W przypadku zadań PyTorch, TensorFlow i łańcucha Azure Machine Learning zapewnia także odpowiednie [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)i [łańcucha](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) szacowania, aby uprościć korzystanie z tych platform.

## <a name="train-with-an-estimator"></a>Szkolenie przy użyciu narzędzie do szacowania

Po utworzeniu usługi [obszaru roboczego](concept-workspace.md) i skonfiguruj swoje [środowisko programistyczne](how-to-configure-environment.md), uczenia modelu w usłudze Azure Machine Learning obejmuje następujące czynności:  
1. Tworzenie [zdalnego obiektu docelowego obliczeń](how-to-set-up-training-targets.md) (Uwaga: Możesz również użyć komputera lokalnego jako obiektu docelowego obliczeń)
2. Przekaż [dane szkoleniowe](how-to-access-data.md) do magazynu danych (opcjonalnie)
3. Utwórz swoje [skrypt szkoleniowy](tutorial-train-models-with-aml.md#create-a-training-script)
4. Utwórz `Estimator` obiektu
5. Prześlij szacowania do obiektu eksperymentu w obszarze roboczym

Ten artykuł koncentruje się na kroki 4 – 5. Kroki 1 – 3, można znaleźć na [uczenie modelu samouczek](tutorial-train-models-with-aml.md) przykład.

### <a name="single-node-training"></a>Szkolenie z jednym węzłem

Użyj `Estimator` szkolenia jednym węzłem, uruchom na zdalne zasoby obliczeniowe na platformie Azure na potrzeby scikit-informacje modelu. Powinna już utworzono usługi [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#amlcompute) obiektu `compute_target` i [datastore](how-to-access-data.md) obiektu `ds`.

```Python
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
```

Ten fragment kodu określa następujące parametry `Estimator` konstruktora.

Parametr | Opis
--|--
`source_directory`| Katalog lokalny, który zawiera wszystkie wymagane na potrzeby zadania szkolenia kodu. Ten folder zostanie skopiowany z komputera lokalnego do zdalnego obliczenia.
`script_params`| Słownik określający argumenty wiersza polecenia do przekazania do skryptu `entry_script`szkoleniowego, w `<command-line argument, value>` postaci par. Aby określić flagę verbose w `script_params`, użyj. `<command-line argument, "">`
`compute_target`| Miejsce docelowe obliczeń zdalnych, na których będzie wykonywany skrypt szkoleniowy, w tym przypadku klaster Azure Machine Learning COMPUTE ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Należy pamiętać, że klaster AmlCompute jest często używanym miejscem docelowym, dlatego można wybrać inne typy obiektów docelowych obliczeń, takie jak maszyny wirtualne platformy Azure, a nawet komputer lokalny).
`entry_script`| FilePath (względem `source_directory`) skryptu szkolenia, należy uruchomić na zdalne zasoby obliczeniowe. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny znajdować się w tym folderze.
`conda_packages`| Lista pakietów języka Python, aby ją zainstalować za pomocą narzędzia conda, wymagane przez skrypt szkolenia.  

Konstruktor ma inny parametr o nazwie `pip_packages` , który jest używany dla wszystkich wymaganych pakietów PIP.

Teraz, po utworzeniu usługi `Estimator` obiektu i przesyłaj zadania szkolenia, należy uruchomić na zdalne zasoby obliczeniowe z wywołaniem `submit` działać na Twoje [eksperymentu](concept-azure-machine-learning-architecture.md#experiments) obiektu `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Foldery specjalne** dwa foldery *generuje* i *dzienniki*, specjalne traktowane przez uczenie maszynowe Azure. Podczas szkolenia, kiedy piszesz pliki do folderu o nazwie *generuje* i *dzienniki* , które są względem katalogu głównego (`./outputs` i `./logs`odpowiednio), pliki zostaną automatycznie tak, aby mieli do nich dostęp, po zakończeniu przebieg, należy przekazać do Twojej historii uruchamiania.
>
> Do tworzenia artefaktów podczas uczenia (np. plików modeli, punkty kontrolne, pliki danych lub wykreślona obrazów) zapisać je do `./outputs` folderu.
>
> Podobnie można napisać wszystkie dzienniki z poziomu szkolenia szkoleniowego do `./logs` folderu. Korzystanie z usługi Azure Machine Learning [integracji narzędzia TensorBoard](https://aka.ms/aml-notebook-tb) upewnij się, zapisać Dzienniki narzędzia TensorBoard do tego folderu. Gdy przebieg jest w toku, można uruchomić narzędzia TensorBoard i przesyłanie strumieniowe dzienników.  Później również można przywrócić dzienników pochodzących z dowolnych z poprzednich przebiegów.
>
> Na przykład, aby pobrać plik zapisywane *generuje* folderu na komputerze lokalnym po szkolenia zdalnego uruchamiania: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Rozproszonego szkolenia i niestandardowych obrazów platformy Docker

Istnieją dwa scenariusze dodatkowe szkolenie, które można wykonywać za pomocą `Estimator`:
* Przy użyciu niestandardowego obrazu platformy Docker
* Rozproszonego szkolenia na klastra wielowęzłowego

Poniższy kod przedstawia sposób przeprowadzenia szkolenia rozproszonego dla modelu Keras. Ponadto zamiast używać domyślnych obrazów Azure Machine Learning, określa niestandardowy obraz platformy Docker z usługi Docker Hub `continuumio/miniconda` do szkoleń.

Powinna już utworzono swoje [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#amlcompute) obiektu `compute_target`. Narzędzie do szacowania można utworzyć w następujący sposób:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

Powyższy kod udostępnia następujące nowe parametry `Estimator` Konstruktor:

Parametr | Opis | Domyślne
--|--|--
`custom_docker_image`| Nazwa obrazu, którego chcesz użyć. Podaj tylko obrazów dostępnych w repozytoriach publicznych platformy docker (w tym przypadku usłudze Docker Hub). Aby użyć obrazu z prywatnego repozytorium platformy Docker, Użyj zamiast niego `environment_definition` parametru konstruktora. [Zobacz przykład](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Liczba węzłów na potrzeby zadania szkolenia. | `1`
`process_count_per_node`| Liczba procesów (lub "pracowników przetwarzających"), aby uruchomić w każdym węźle. W tym przypadku używasz `2` procesorów GPU jest dostępne w każdym węźle.| `1`
`distributed_backend`| Zaplecze dla uruchamiania rozproszonych, szkolenia, która estymatora oferuje za pośrednictwem MPI.  Aby przeprowadzić równoległe lub rozproszone uczenie (np. `node_count`> 1 lub `process_count_per_node`> 1 lub oba), ustaw `distributed_backend='mpi'`. Jest używana przez AML implementacja MPI [Otwórz MPI](https://www.open-mpi.org/).| `None`

Na koniec można przesłać zadania szkolenia:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>Śledzenie i integracja z usługą GitHub

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Na przykład bieżący identyfikator zatwierdzenia dla repozytorium jest rejestrowany jako część historii.

## <a name="examples"></a>Przykłady
Aby zapoznać się z notesem, który pokazuje podstawy wzorca szacowania, zobacz:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

W przypadku notesu, który pociąga za scikit model uczenia przy użyciu szacowania, zobacz:
* [Samouczki/img klasyfikacji — część 1 — training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

W przypadku notesów na potrzeby modeli szkoleniowych przy użyciu szacowania z konkretną nauką, zobacz:
* [How-to-use-azureml/Training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Szkolenie modeli PyTorch](how-to-train-pytorch.md)
* [Szkolenie modeli TensorFlow](how-to-train-tensorflow.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
