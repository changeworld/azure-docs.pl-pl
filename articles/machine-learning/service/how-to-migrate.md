---
title: Przeprowadź migrację do usługi Azure Machine Learning
description: Dowiedz się, jak uaktualnienie lub migrację do wersji opóźnione usługa Azure Machine Learning ze starszej wersji.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: 9778d348cf49d4066f034931dc350a1f4a608ad2
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236588"
---
# <a name="migrate-to-the-latest-version-of-azure-machine-learning-service"></a>Migracja do najnowszej wersji usługi Azure Machine Learning 

**Jeśli zainstalowano aplikację Workbench (wersja zapoznawcza) lub mają eksperymentowanie i zarządzanie modelami konta w wersji zapoznawczej, aby przeprowadzić migrację do najnowszej wersji, skorzystaj z tego artykułu.**  Jeśli nie masz wersji zapoznawczej Workbench zainstalowana, lub eksperymentowania i/lub konta zarządzania modelami, nie trzeba niczego migracji.

## <a name="what-can-i-migrate"></a>Co można migrować?
Większość artefaktów, utworzonego w pierwszej wersji zapoznawczej usługi Azure Machine Learning są przechowywane w lokalnym własnych lub magazyn w chmurze. Te artefakty nie są usuwane. Aby przeprowadzić migrację, należy ponownie zarejestrować artefaktów w usłudze Azure Machine Learning zaktualizowane. 

Poniższej tabeli i artykule wyjaśniono, co można zrobić z istniejących zasobów i zasobów przed lub po przeniesieniem do najnowszej wersji usługi Azure Machine Learning. Nadal m ożna również używać poprzedniej wersji i zasobów przez pewien czas ([Zobacz przejścia plan pomocy technicznej](overview-what-happened-to-workbench.md#timeline)).

|Zasobów lub zasobu ze starej wersji|Czy mogę zmigrować?|Akcje|
|-----------------|:-------------:|-------------|
|Modele (jako pliki lokalne) uczenia maszynowego|Yes|Brak. Działa tak jak poprzednio.|
|Model zależności & schematy (pliki lokalne)|Yes|Brak. Działa tak jak poprzednio.|
|Projekty|Yes|[Dołącz lokalnego folderu do nowego obszaru roboczego](#projects).|
|Historie uruchamiania|Nie|[Do pobrania](#history) od pewnego czasu.|
|Pliki przeznaczonego do przygotowania danych|Nie|[Przygotowanie dowolnego rozmiaru zestawu danych](#dataprep) do modelowania nowe Machine Learning danych Prep zestawu SDK usługi Azure lub użyj usługi Azure Databricks.|
|Celów obliczeń|Nie|Zarejestruj je w nowym obszarem roboczym.|
|Zarejestrowane modele|Nie|Zarejestruj ponownie aplikację modelu w ramach nowego obszaru roboczego.|
|Manifesty zarejestrowane|Nie|Brak. Manifesty nie istnieje już jako pojęcie w nowym obszarem roboczym.|
|Zarejestrowane obrazów|Nie|Ponownie utworzyć obraz platformy Docker wdrożenia w ramach nowego obszaru roboczego.|
|Wdrożonymi usługami sieci web|Nie|Brak. Będą one nadal działać jako — jest <br/>lub [wdrażać je ponownie przy użyciu najnowszej wersji](#services).|
|Eksperymentowanie i <br/>Konta zarządzania modelami|Nie|[Utwórz obszar roboczy](#resources) zamiast tego.|
|Usługi Machine learning interfejs wiersza polecenia i zestawu SDK|Nie|Użyj nowego [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) i [SDK](http://aka.ms/aml-sdk) dla nowej pracy.|


Dowiedz się więcej o [co zmieniło się w tej wersji](overview-what-happened-to-workbench.md)?

>[!Warning]
>Ten artykuł nie jest dostępne dla użytkowników usługi Azure Machine Learning Studio. To dla klientów usługi Azure Machine Learning, którzy zainstalowano aplikację Workbench (wersja zapoznawcza) i/lub mieć eksperymentowania oraz konta w wersji zapoznawczej zarządzania modelami.

<a name="resources"></a>

## <a name="azure-resources"></a>Zasoby platformy Azure

Zasoby, takie jak konta eksperymentowanie w usłudze, konta zarządzania modelami i uczenia maszynowego obliczeniowe środowiska nie można migrować za pośrednictwem do najnowszej wersji usługi Azure Machine Learning. Zobacz [osi czasu](overview-what-happened-to-workbench.md#timeline) na jak długo zasobów będą nadal działać.

Rozpocznij pracę z najnowszą wersją, tworząc obszar roboczy usługi Azure Machine Learning w [witryny Azure portal](quickstart-get-started.md). Pulpit nawigacyjny z obszaru roboczego portalu jest obsługiwana na tylko w przeglądarkach Edge, Chrome i Firefox.

Ten nowy obszar roboczy jest zasobem najwyższego poziomu usługi i umożliwia korzystanie ze wszystkich najnowszych funkcji usługi Azure Machine Learning. Dowiedz się więcej na ten temat [obszar roboczy i architektura](concept-azure-machine-learning-architecture.md).

<a name="projects"></a>

## <a name="projects"></a>Projekty

Zamiast projektów w obszarze roboczym w chmurze, projekty są teraz katalogów na komputerze lokalnym w najnowszej wersji. Wyświetlany diagram [najnowsza architektura](concept-azure-machine-learning-architecture.md). 

Aby nadal korzystać z lokalnego katalogu zawierającego pliki i skrypty, należy określić nazwę katalogu w ["experiment.submit"](http://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) polecenia języka Python lub za pomocą polecenia interfejsu wiersza polecenia "az ml projektu Dołącz".

Na przykład:
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>Wdrożonymi usługami sieci web

Aby przeprowadzić migrację usług sieci web, należy ponownie wdrożyć swoje modele przy użyciu nowego zestawu SDK lub interfejsu wiersza polecenia do nowych celów wdrożenia. Nie ma potrzeby zmiany oryginalny plik oceniania, plików zależności pliku modelu, pliku środowiska i pliki schematów. 

W najnowszej wersji modeli są wdrażane jako usług sieci web [usługi Azure Container Instances](how-to-deploy-to-aci.md) (ACI) lub [usługi Azure Kubernetes Service](how-to-deploy-to-aks.md) klastrów (AKS). 

Dowiedz się więcej, zobacz następujące artykuły:
+ [Wdrażanie w usłudze ACI](how-to-deploy-to-aci.md)
+ [Wdrażanie w usłudze AKS](how-to-deploy-to-aks.md)
+ [Samouczek: Wdrażanie modeli przy użyciu usługi Azure Machine Learning](tutorial-deploy-models-with-aml.md)

Gdy [obsługę poprzednie punkty końcowe interfejsu wiersza polecenia](overview-what-happened-to-workbench.md#timeline), nie będzie można zarządzać usługami sieci web pierwotnie wdrożone przy użyciu konta zarządzania modelami. Jednak te usługi sieci web będą w dalszym ciągu działać w przypadku, tak długo, jak usługi Azure Container Service (ACS) nadal jest obsługiwany.

<a name="history"></a>

## <a name="run-history-records"></a>Rekordy historii uruchamiania

Gdy nie można kontynuować dodać do swojej istniejącej uruchomień w obszarze stary obszar roboczy, możesz wyeksportować historie, masz przy użyciu poprzedniego wiersza. Gdy [obsługę poprzednie punkty końcowe interfejsu wiersza polecenia](overview-what-happened-to-workbench.md#timeline), nie będzie można już wyeksportować te uruchomień.

Rozpocznij szkolenie modeli i śledzenie uruchomień przy użyciu nowego interfejsu wiersza polecenia i zestawu SDK. Możesz dowiedzieć się jak za pomocą [samouczek: uczenia modeli za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

Aby wyeksportować historię przebiegów z poprzednim interfejsem wiersza polecenia:

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>Pliki przygotowywania danych
Pliki przygotowywania danych nie są przenośne bez aplikacji Workbench. Ale nadal można przygotować dowolnego rozmiaru zestawu danych w przypadku modelowania, za pomocą nowego Machine Learning danych Prep zestawu SDK usługi Azure lub przy użyciu usługi Azure Databricks dla zestawów danych big Data.  [Dowiedz się, jak uzyskać przygotowywanie danych zestawu SDK](how-to-data-prep.md). 

## <a name="next-steps"></a>Kolejne kroki

Szybki Start omawiający Tworzenie obszaru roboczego, Utwórz projekt, za pomocą skryptu i Poznaj historię uruchamiania skryptu z najnowszą wersją usługi Azure Machine Learning, spróbuj [wprowadzenie do usługi Azure Machine Learning](quickstart-get-started.md).

Aby uzyskać bardziej szczegółowe wyniki tego przepływu pracy wykonaj cały samouczek, który zawiera szczegółowe informacje na temat szkoleń i wdrażanie modeli przy użyciu usługi Azure Machine Learning. 

> [!div class="nextstepaction"]
> [Samouczek: Uczenie i wdrażanie modeli](tutorial-train-models-with-aml.md)
