---
title: Szkolenie modeli uczenia maszynowego przy użyciu usługi Azure Machine Learning
description: Dowiedz się, jak przeprowadzić jednym węzłem i rozproszonego szkolenia tradycyjnych uczenia maszynowego i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983586"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Sposób trenowania modeli przy użyciu usługi Azure Machine Learning

Szkolenia usługi machine learning modeli, szczególnie głębokich sieciach neuronowych, jest często zadania wymagające obliczeń i czasu. Po zakończeniu pisania skryptu szkolenia i systemem mały podzbiór danych na komputerze lokalnym, prawdopodobnie można skalować obciążenie.

Aby ułatwić szkolenia, zestawu SDK języka Python usługi Azure Machine Learning zapewnia Abstrakcja wysokiego poziomu klasy narzędzie do szacowania, która umożliwia użytkownikom łatwe szkolenie modeli, ich w ekosystemie platformy Azure. Można tworzyć i używać obiekt narzędzie do szacowania do przesyłania żadnego kodu szkolenia, które chcesz uruchomić na zdalnym mocy obliczeniowej, czy jest ono szkolenia jednowęzłowej rozproszonej lub wykonywania w klastrze procesorów GPU. Zadań PyTorch i TensorFlow, Azure Machine Learning udostępnia również odpowiednich PyTorch niestandardowej i TensorFlow aplikacjom, które ułatwiają tworzenie tych platform.

## <a name="train-with-an-estimator"></a>Szkolenie przy użyciu narzędzie do szacowania

Po utworzeniu usługi [obszaru roboczego](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace) i skonfiguruj swoje [środowisko programistyczne](how-to-configure-environment.md), uczenia modelu w usłudze Azure Machine Learning obejmuje następujące czynności:  
1. [Utworzyć cel obliczenia zdalnego](how-to-set-up-training-targets.md)
2. [Przekazywanie danych szkoleniowych](how-to-access-data.md) (opcjonalnie)
3. Utwórz skrypt szkolenia
4. Utwórz obiekt narzędzie do szacowania
5. Przesyłanie zadania szkolenia

Ten artykuł koncentruje się na kroki 4 – 5. Kroki 1 – 3, można znaleźć w tym [samouczek](tutorial-train-models-with-aml.md) przykład.

### <a name="single-node-training"></a>Szkolenie z jednym węzłem

Poniższy kod przeprowadzi szkolenia jednym węzłem, uruchom na zdalne zasoby obliczeniowe na platformie Azure na potrzeby scikit-informacje modelu. Powinna już utworzono usługi [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#batch) obiektu `compute_target` i [datastore](how-to-access-data.md) obiektu `ds`.

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

Powyższym fragmencie kodu określa następujące parametry do konstruktora narzędzie do szacowania:
* `source_directory`: Katalog lokalny, który zawiera wszystkie wymagane na potrzeby zadania szkolenia kodu. Ten folder skopiowane z komputera lokalnego do zdalnego obliczeń 
* `script_params`: Określanie argumentów wiersza polecenia do skryptu szkolenia słownik `entry_script`, w postaci < argument wiersza polecenia, wartość > par
* `compute_target`: Zdalne zasoby obliczeniowe uruchamianego skrypt szkolenia, w tym przypadku [usługi Batch AI](how-to-set-up-training-targets.md#batch) klastra
* `entry_script`Filepath (względem `source_directory`) skryptu szkolenia, należy uruchomić na zdalne zasoby obliczeniowe. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny się znajdować w tym folderze
* `conda_packages`: Lista pakietów języka Python, aby ją zainstalować za pomocą narzędzia conda, wymagane przez skrypt szkolenia.  
Konstruktor ma inny parametr o nazwie `pip_packages` używanego do dowolnego pakietu pip, wymagane

Teraz, po utworzeniu obiektu narzędzie do szacowania, można przesłać zadania szkolenia, należy uruchomić na zdalne zasoby obliczeniowe poprzez wywołanie `submit` działać na Twoje [eksperymentu](concept-azure-machine-learning-architecture.md#experiment) obiektu `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Foldery specjalne** dwa foldery *generuje* i *dzienniki*, specjalne traktowane przez uczenie maszynowe Azure. Podczas szkolenia, jeśli zapisywać pliki do folderu o nazwie *generuje* i *dzienniki* , które są względem katalogu głównego (`./outputs` i `./logs`odpowiednio), otrzymają te pliki Dzięki temu będą mieli do nich dostęp, po zakończeniu przebieg automatycznie przekazywane do Twojej historii uruchamiania. 
>
> Aby uzyskać dostęp do artefaktów utworzonego podczas uczenia (np. plików modeli, punkty kontrolne, pliki danych lub wykreślona obrazów) zapisu je do `./outputs` folderu.
>
> Podobnie, można zapisywać żadnych dzienników z szkolenia Uruchom, aby `./logs` folderu. Korzystanie z usługi Azure Machine Learning [integracji narzędzia TensorBoard](https://aka.ms/aml-notebook-tb) upewnij się, zapisać Dzienniki narzędzia TensorBoard do tego folderu. Gdy przebieg jest w toku, można uruchomić narzędzia TensorBoard i przesyłanie strumieniowe dzienników.  Później również można przywrócić dzienników pochodzących z dowolnych z poprzednich przebiegów.
>
> Na przykład, aby pobrać plik zapisywane *generuje* folderu na komputerze lokalnym po szkolenia zdalnego uruchamiania: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Rozproszonego szkolenia i niestandardowych obrazów platformy Docker

Istnieją dwa scenariusze dodatkowe szkolenie, które można wykonywać za pomocą narzędzia szacunkowej oceny:
1. Przy użyciu niestandardowego obrazu platformy Docker
2. Rozproszonego szkolenia na klastra wielowęzłowego

Poniższy kod przedstawia sposób przeprowadzania rozproszonego szkolenia CNTK modelu. Ponadto zamiast domyślnych obrazów usługi Azure Machine Learning, założono, że użytkownik ma swój własny obraz niestandardowy platformy docker, którego chcesz użyć do trenowania.

Powinna już utworzono swoje [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#batch) obiektu `compute_target`. Narzędzie do szacowania można utworzyć w następujący sposób:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

Powyższy kod udostępnia następujące nowe parametry do konstruktora narzędzie do szacowania:
* `custom_docker_base_image`: Nazwa obrazu, którego chcesz użyć. Należy podać tylko obrazów dostępnych w repozytoriach publicznych platformy docker (w tym przypadku usłudze Docker Hub). Aby użyć obrazu z repozytorium prywatnego platformy docker, należy użyć konstruktora `environment_definition` parametru zamiast tego
* `node_count`: Liczba węzłów na potrzeby zadania szkolenia. Wartością domyślną tego argumentu `1`
* `process_count_per_node`: Liczba procesów (lub "pracowników przetwarzających"), aby uruchomić w każdym węźle. W tym przypadku używasz `2` procesorów GPU jest dostępne w każdym węźle. Wartością domyślną tego argumentu `1`
* `distributed_backend`: Zaplecze do uruchamiania rozproszonych szkoleń, która estymatora oferuje za pośrednictwem MPI. Domyślnie ten argument `None`. Jeśli chcesz przeprowadzić szkolenia równoległego lub rozproszonej (np. `node_count`> 1 lub `process_count_per_node`> 1 lub oba), ustaw `distributed_backend='mpi'`. Jest używana przez AML implementacja MPI [Otwórz MPI](https://www.open-mpi.org/).

Na koniec można przesłać zadania szkolenia:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Przykłady
Samouczek dotyczący uczenia modelu skryptu sklearn zobacz:
* `tutorials/01.train-models.ipynb`

Samouczek dotyczący rozproszonego CNTK przy użyciu niestandardowego platformy docker zobacz:
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

Pobierz te notesy:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Szkolenie modeli PyTorch](how-to-train-pytorch.md)
* [Szkolenie modeli TensorFlow](how-to-train-tensorflow.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
