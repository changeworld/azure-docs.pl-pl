---
title: Szkolenie modeli uczenia maszynowego przy użyciu klasy narzędzie do szacowania za pomocą usługi Azure Machine Learning
description: Dowiedz się, jak przeprowadzić jednym węzłem i rozproszonego szkolenia tradycyjnych uczenia maszynowego i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning services narzędzie do szacowania klasy
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 8c2f4ad9acc789a23eb951265b0464aee6caaa6c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815039"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Sposób trenowania modeli przy użyciu usługi Azure Machine Learning

Szkolenia usługi machine learning modeli, szczególnie głębokich sieciach neuronowych, jest często zadania wymagające obliczeń i czasu. Po zakończeniu pisania skryptu szkolenia i systemem mały podzbiór danych na komputerze lokalnym, prawdopodobnie można skalować obciążenie.

Aby ułatwić szkolenia, zestawu SDK języka Python usługi Azure Machine Learning zapewnia Abstrakcja wysokiego poziomu klasy narzędzie do szacowania, która umożliwia użytkownikom łatwe szkolenie modeli, ich w ekosystemie platformy Azure. Można tworzyć i używać `Estimator` obiektu w celu przesłania jakiegokolwiek kodu szkolenia, należy uruchomić na zdalnym mocy obliczeniowej, czy jest ono szkolenia jednowęzłowej rozproszonej lub wykonywania w klastrze procesorów GPU. Zadania PyTorch i TensorFlow, Azure Machine Learning zawiera także niestandardowe odpowiednich `PyTorch` i `TensorFlow` aplikacjom w celu uproszczenia korzystania z tych platform.

## <a name="train-with-an-estimator"></a>Szkolenie przy użyciu narzędzie do szacowania

Po utworzeniu usługi [obszaru roboczego](concept-azure-machine-learning-architecture.md#workspace) i skonfiguruj swoje [środowisko programistyczne](how-to-configure-environment.md), uczenia modelu w usłudze Azure Machine Learning obejmuje następujące czynności:  
1. Utwórz [zdalnego obliczeniowego elementu docelowego](how-to-set-up-training-targets.md)
2. Przekaż swoje [dane szkoleniowe](how-to-access-data.md) (opcjonalnie)
3. Utwórz swoje [skrypt szkoleniowy](tutorial-train-models-with-aml.md#create-a-training-script)
4. Utwórz `Estimator` obiektu
5. Przesyłanie zadania szkolenia

Ten artykuł koncentruje się na kroki 4 – 5. Kroki 1 – 3, można znaleźć na [uczenie modelu samouczek](tutorial-train-models-with-aml.md) przykład.

### <a name="single-node-training"></a>Szkolenie z jednym węzłem

Użyj `Estimator` szkolenia jednym węzłem, uruchom na zdalne zasoby obliczeniowe na platformie Azure na potrzeby scikit-informacje modelu. Powinna już utworzono usługi [obliczeniowego elementu docelowego](how-to-set-up-training-targets.md#batch) obiektu `compute_target` i [datastore](how-to-access-data.md) obiektu `ds`.

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
`script_params`| Określanie argumentów wiersza polecenia do skryptu szkolenia słownika `entry_script`, w postaci < argument wiersza polecenia, wartość > par
`compute_target`| Zdalne obliczeń, uruchamianego skrypt szkolenia, w tym przypadku [usługi Batch AI](how-to-set-up-training-targets.md#batch) klastra
`entry_script`| FilePath (względem `source_directory`) skryptu szkolenia, należy uruchomić na zdalne zasoby obliczeniowe. Ten plik i wszelkie dodatkowe pliki, od których zależy, powinny się znajdować w tym folderze
`conda_packages`| Lista pakietów języka Python, aby ją zainstalować za pomocą narzędzia conda, wymagane przez skrypt szkolenia.  
Konstruktor ma inny parametr o nazwie `pip_packages` używaną dla dowolnego pakietu pip, wymagane

Teraz, po utworzeniu usługi `Estimator` obiektu i przesyłaj zadania szkolenia, należy uruchomić na zdalne zasoby obliczeniowe z wywołaniem `submit` działać na Twoje [eksperymentu](concept-azure-machine-learning-architecture.md#experiment) obiektu `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Foldery specjalne** dwa foldery *generuje* i *dzienniki*, specjalne traktowane przez uczenie maszynowe Azure. Podczas szkolenia, kiedy piszesz pliki do folderu o nazwie *generuje* i *dzienniki* , które są względem katalogu głównego (`./outputs` i `./logs`odpowiednio), pliki zostaną automatycznie tak, aby mieli do nich dostęp, po zakończeniu przebieg, należy przekazać do Twojej historii uruchamiania.
>
> Do tworzenia artefaktów podczas uczenia (np. plików modeli, punkty kontrolne, pliki danych lub wykreślona obrazów) zapisać je do `./outputs` folderu.
>
> Podobnie, zapisywać żadnych dzienników z szkolenia uruchomić `./logs` folderu. Korzystanie z usługi Azure Machine Learning [integracji narzędzia TensorBoard](https://aka.ms/aml-notebook-tb) upewnij się, zapisać Dzienniki narzędzia TensorBoard do tego folderu. Gdy przebieg jest w toku, można uruchomić narzędzia TensorBoard i przesyłanie strumieniowe dzienników.  Później również można przywrócić dzienników pochodzących z dowolnych z poprzednich przebiegów.
>
> Na przykład, aby pobrać plik zapisywane *generuje* folderu na komputerze lokalnym po szkolenia zdalnego uruchamiania: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Rozproszonego szkolenia i niestandardowych obrazów platformy Docker

Istnieją dwa scenariusze dodatkowe szkolenie, które można wykonywać za pomocą `Estimator`:
* Przy użyciu niestandardowego obrazu platformy Docker
* Rozproszonego szkolenia na klastra wielowęzłowego

Poniższy kod przedstawia sposób przeprowadzania rozproszonego szkolenia CNTK modelu. Ponadto zamiast domyślnych obrazów usługi Azure Machine Learning, założono, że używasz własnego obrazu niestandardowego platformy docker do trenowania.

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

Powyższy kod udostępnia następujące nowe parametry `Estimator` Konstruktor:

Parametr | Opis | Domyślne
--|--|--
`custom_docker_base_image`| Nazwa obrazu, którego chcesz użyć. Podaj tylko obrazów dostępnych w repozytoriach publicznych platformy docker (w tym przypadku usłudze Docker Hub). Aby użyć obrazu z repozytorium prywatnego platformy docker, należy użyć konstruktora `environment_definition` parametru zamiast tego | `None`
`node_count`| Liczba węzłów na potrzeby zadania szkolenia. | `1`
`process_count_per_node`| Liczba procesów (lub "pracowników przetwarzających"), aby uruchomić w każdym węźle. W tym przypadku używasz `2` procesorów GPU jest dostępne w każdym węźle.| `1`
`distributed_backend`| Zaplecze dla uruchamiania rozproszonych, szkolenia, która estymatora oferuje za pośrednictwem MPI.  Przeprowadzenie szkolenia równoległego lub rozproszonej (np. `node_count`> 1 lub `process_count_per_node`> 1 lub oba), ustaw `distributed_backend='mpi'`. Jest używana przez AML implementacja MPI [Otwórz MPI](https://www.open-mpi.org/).| `None`

Na koniec można przesłać zadania szkolenia:
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Przykłady
Samouczek dotyczący uczenia modelu skryptu sklearn zobacz:
* `tutorials/01.train-models.ipynb`

Samouczek dotyczący rozproszonego CNTK przy użyciu niestandardowego platformy docker zobacz:
* `training/06.distributed-cntk-with-custom-docker`

Pobierz te notesy:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Śledzenie metryk są uruchamiane podczas szkolenia](how-to-track-experiments.md)
* [Szkolenie modeli PyTorch](how-to-train-pytorch.md)
* [Szkolenie modeli TensorFlow](how-to-train-tensorflow.md)
* [Dostosowywanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie uczonego modelu](how-to-deploy-and-where.md)
