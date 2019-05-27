---
title: Szkolenie modelu uczenia Maszynowego dzięki aplikacjom
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak przeprowadzić jednym węzłem i rozproszonego szkolenia tradycyjnych uczenia maszynowego i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning services narzędzie do szacowania klasy
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 98f7dc2e295c0c994db9a0189814b0ef2a19b758
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66153614"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Szkolenie modeli przy użyciu usługi Azure Machine Learning przy użyciu narzędzie do szacowania

Za pomocą usługi Azure Machine Learning, można łatwo przesłać skrypt szkolenia, aby [różnych celów obliczeń](how-to-set-up-training-targets.md#compute-targets-for-training)przy użyciu [obiektu RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) i [obiektu ScriptRunConfig](how-to-set-up-training-targets.md#submit). Ten wzorzec zapewnia wysoki poziom kontroli elastyczność i maksymalną.

Ułatwiające głębokiego uczenia szkoleń modelowych Azure Machine Learning zestawu SDK języka Python zapewnia alternatywny wyższego poziomu abstrakcji klasy narzędzie do szacowania, która umożliwia użytkownikom łatwo uruchomić konstrukcję konfiguracje. Można tworzyć i korzystają z ogólnego [narzędzie do szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) przesłać skrypt szkolenia, używając dowolną platformę nauczania, możesz wybrać (takich jak scikit — informacje) należy uruchomić na dowolnym obliczeniowych elementów docelowych, czy komputer lokalny, pojedynczej maszyny Wirtualnej na platformie Azure lub procesora GPU klaster na platformie Azure. Dla zadań PyTorch i TensorFlow, Chainer, Azure Machine Learning udostępnia również odpowiednich [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) i [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) aplikacjom, które ułatwiają korzystanie z tych struktury.

## <a name="train-with-an-estimator"></a>Szkolenie przy użyciu narzędzie do szacowania

Po utworzeniu usługi [obszaru roboczego](concept-workspace.md) i skonfiguruj swoje [środowisko programistyczne](how-to-configure-environment.md), uczenia modelu w usłudze Azure Machine Learning obejmuje następujące czynności:  
1. Tworzenie [zdalnego obliczeniowych elementów docelowych](how-to-set-up-training-targets.md) (Uwaga komputerze lokalnym można również użyć jako cel obliczenia)
2. Przekaż swoje [dane szkoleniowe](how-to-access-data.md) magazynem danych (opcjonalnie)
3. Utwórz swoje [skrypt szkoleniowy](tutorial-train-models-with-aml.md#create-a-training-script)
4. Utwórz `Estimator` obiektu
5. Prześlij narzędzie do szacowania obiektowi eksperymentu w obszarze roboczym

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
`source_directory`| Katalog lokalny, który zawiera wszystkie wymagane na potrzeby zadania szkolenia kodu. Ten folder skopiowane z komputera lokalnego do zdalnego obliczeń 
`script_params`| Określanie argumentów wiersza polecenia do skryptu szkolenia słownika `entry_script`, w postaci < argument wiersza polecenia, wartość > pary. Aby określić pełne flagi w `script_params`, użyj `<command-line argument, "">`.
`compute_target`| Zdalne obliczeniowego elementu docelowego, uruchamianego skrypt szkolenia, w tym przypadku Azure obliczeniowego usługi Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) klastra. (Należy pamiętać, nawet jeśli AmlCompute klastra jest często używane docelowej, jest również można określić innych obliczeń typów docelowych, takich jak maszyny wirtualne platformy Azure lub na komputerze lokalnym nawet.)
`entry_script`| FilePath (względem `source_directory`) skryptu szkolenia, należy uruchomić na zdalne zasoby obliczeniowe. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny się znajdować w tym folderze
`conda_packages`| Lista pakietów języka Python, aby ją zainstalować za pomocą narzędzia conda, wymagane przez skrypt szkolenia.  

Konstruktor ma inny parametr o nazwie `pip_packages` używaną dla dowolnego pakietu pip, wymagane

Teraz, po utworzeniu usługi `Estimator` obiektu i przesyłaj zadania szkolenia, należy uruchomić na zdalne zasoby obliczeniowe z wywołaniem `submit` działać na Twoje [eksperymentu](concept-azure-machine-learning-architecture.md#experiment) obiektu `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Foldery specjalne** dwa foldery *generuje* i *dzienniki*, specjalne traktowane przez uczenie maszynowe Azure. Podczas szkolenia, kiedy piszesz pliki do folderu o nazwie *generuje* i *dzienniki* , które są względem katalogu głównego (`./outputs` i `./logs`odpowiednio), pliki zostaną automatycznie tak, aby mieli do nich dostęp, po zakończeniu przebieg, należy przekazać do Twojej historii uruchamiania.
>
> Do tworzenia artefaktów podczas uczenia (np. plików modeli, punkty kontrolne, pliki danych lub wykreślona obrazów) zapisać je do `./outputs` folderu.
>
> Podobnie, można zapisywać żadnych dzienników z szkolenia Uruchom, aby `./logs` folderu. Korzystanie z usługi Azure Machine Learning [integracji narzędzia TensorBoard](https://aka.ms/aml-notebook-tb) upewnij się, zapisać Dzienniki narzędzia TensorBoard do tego folderu. Gdy przebieg jest w toku, można uruchomić narzędzia TensorBoard i przesyłanie strumieniowe dzienników.  Później również można przywrócić dzienników pochodzących z dowolnych z poprzednich przebiegów.
>
> Na przykład, aby pobrać plik zapisywane *generuje* folderu na komputerze lokalnym po szkolenia zdalnego uruchamiania: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Rozproszonego szkolenia i niestandardowych obrazów platformy Docker

Istnieją dwa scenariusze dodatkowe szkolenie, które można wykonywać za pomocą `Estimator`:
* Przy użyciu niestandardowego obrazu platformy Docker
* Rozproszonego szkolenia na klastra wielowęzłowego

Poniższy kod przedstawia sposób przeprowadzania rozproszonego szkolenia modelu biblioteki Keras. Ponadto, zamiast domyślnych obrazów usługi Azure Machine Learning, określa on obraz niestandardowy platformy docker z usługi Docker Hub `continuumio/miniconda` szkolenia.

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
                      custom_docker_base_image='continuumio/miniconda')
```

Powyższy kod udostępnia następujące nowe parametry `Estimator` Konstruktor:

Parametr | Opis | Domyślne
--|--|--
`custom_docker_base_image`| Nazwa obrazu, którego chcesz użyć. Podaj tylko obrazów dostępnych w repozytoriach publicznych platformy docker (w tym przypadku usłudze Docker Hub). Aby użyć obrazu z repozytorium prywatnego platformy docker, należy użyć konstruktora `environment_definition` parametru zamiast tego. [Zobacz przykład](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Liczba węzłów na potrzeby zadania szkolenia. | `1`
`process_count_per_node`| Liczba procesów (lub "pracowników przetwarzających"), aby uruchomić w każdym węźle. W tym przypadku używasz `2` procesorów GPU jest dostępne w każdym węźle.| `1`
`distributed_backend`| Zaplecze dla uruchamiania rozproszonych, szkolenia, która estymatora oferuje za pośrednictwem MPI.  Przeprowadzenie szkolenia równoległego lub rozproszonej (np. `node_count`> 1 lub `process_count_per_node`> 1 lub oba), ustaw `distributed_backend='mpi'`. Jest używana przez AML implementacja MPI [Otwórz MPI](https://www.open-mpi.org/).| `None`

Na koniec można przesłać zadania szkolenia:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>Śledzenie usługi GitHub i integracja

Po rozpoczęciu szkolenia, gdzie katalog źródłowy jest w nim program lokalnego repozytorium Git, informacje o repozytorium znajduje się w historii uruchamiania. Na przykład identyfikator bieżącego zatwierdzenia w repozytorium jest rejestrowany jako część historii.

## <a name="examples"></a>Przykłady
Aby uzyskać Notes, która przedstawia podstawy wzorca narzędzie do szacowania zobacz:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Dla notesu, który przygotowuje scikit-informacje modelu przy użyciu narzędzie do szacowania, zobacz:
* [Samouczki/img klasyfikacji — część 1 — training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Aby uzyskać notesów na szkolenie modeli za pomocą określonych aplikacjom framework w przypadku uczenia głębokiego zobacz:
* [How-to-use-azureml/Training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Szkolenie modeli PyTorch](how-to-train-pytorch.md)
* [Szkolenie modeli TensorFlow](how-to-train-tensorflow.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
