---
title: Co się stało z aplikacją Workbench?
titleSuffix: Azure Machine Learning
description: Dowiedz się, co jest możliwe bez aplikacji Workbench i jaka jest oś czasu pomocy technicznej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 03/05/2020
ms.openlocfilehash: 9b49eb6237346a76903202a118331383c5a8f623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944251"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Co się stało z aplikacją Azure Machine Learning Workbench?

Aplikacja Usługi Azure Machine Learning Workbench i niektóre inne wczesne funkcje zostały przestarzałe i zastąpione w wersji **z września 2018 r.,** aby zrobić miejsce dla ulepszonej [architektury.](concept-azure-machine-learning-architecture.md)

Wersja zawiera wiele znaczących aktualizacji wprowadzonych w oparciu o opinie klientów, które mają na celu udoskonalenie środowiska użytkownika. Podstawowe funkcje z przebiegów eksperymentu w zakresie wdrażania modelu nie uległy zmianie. Ale teraz można użyć niezawodnego <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK języka Python,</a>zestaw SDK języka R i [interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) do wykonywania zadań uczenia maszynowego i potoków.

Większość artefaktów, które zostały utworzone we wcześniejszej wersji usługi Azure Machine Learning są przechowywane we własnym magazynie lokalnym lub w chmurze. Te artefakty nigdy nie zostaną usunięte.

Z tego artykułu dowiesz się, co się zmieniło i jak zmiany wpłynęły na zadania wykonywane obecnie przy użyciu aplikacji Azure Machine Learning Workbench i jej interfejsów API.

>[!Warning]
>Ten artykuł nie jest przeznaczony dla użytkowników środowiska Azure Machine Learning Studio. Jest dla klientów usługi Azure Machine Learning, którzy zainstalowali aplikację Workbench (wersja zapoznawcza) i/lub mają konta w wersji zapoznawczej eksperymentów i zarządzania modelami.


## <a name="what-changed"></a>Co się zmieniło?

Najnowsza wersja usługi Azure Machine Learning zawiera następujące funkcje:
+ [Uproszczony model zasobów platformy Azure](concept-azure-machine-learning-architecture.md).
+ [Nowy interfejs użytkownika portalu](how-to-track-experiments.md) służący do zarządzania eksperymentami i celami obliczeniowymi.
+ Nowy, bardziej wszechstronny <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>Języka Python .
+ Nowe, rozbudowane [rozszerzenie interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) na potrzeby uczenia maszynowego.

[Architektura](concept-azure-machine-learning-architecture.md) została zaprojektowana od nowa, aby ułatwić obsługę. Zamiast wielu zasobów i kont platformy Azure, będziesz potrzebować tylko aplikacji [Azure Machine Learning Workspace](concept-workspace.md). Obszary robocze można szybko tworzyć w witrynie [Azure Portal](how-to-manage-workspace.md). Dzięki użyciu obszaru roboczego wielu użytkowników może przechowywać cele obliczeniowe szkoleń i wdrożeń, eksperymenty dotyczące modeli, obrazy platformy Docker, wdrożone modele itd.

Bieżąca wersja udostępnia nowe, ulepszone klienty interfejsu wiersza polecenia i zestawu SDK, ale sama klasyczna aplikacja Workbench została wycofana z użytku. Eksperymentami można zarządzać na [pulpicie nawigacyjnym obszaru roboczego w studiu usługi Azure Machine Learning.](how-to-track-experiments.md#view-the-experiment-in-the-web-portal) Pulpit nawigacyjny umożliwia pobieranie historii, zarządzanie celami obliczeniowymi dołączonymi do obszaru roboczego, zarządzanie modelami i obrazami platformy Docker, a nawet wdrażanie usług internetowych.

<a name="timeline"></a>

## <a name="support-timeline"></a>Oś czasu pomocy technicznej

9 stycznia 2019 r. zakończono obsługę kont Workbench Machine Learning, Azure Machine Learning Experimentation i Model Management oraz skojarzonego z nimi zestawu SDK i interfejsu wiersza polecenia.

Wszystkie najnowsze możliwości są dostępne za pomocą tego <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK</a>, [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) i [portalu](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>A co z historią uruchamiania?

Starsze historie uruchamiania nie są już dostępne, ale nadal można wyświetlać uruchomienia w najnowszej wersji.

Historie uruchamiania są teraz nazywane **eksperymentami**. Eksperymenty modelu można zbierać i eksplorować je przy użyciu zestawu SDK, interfejsu wiersza polecenia lub studia usługi Azure Machine Learning.

Pulpit nawigacyjny obszaru roboczego w portalu jest obsługiwany tylko w przeglądarkach Microsoft Edge, Chrome i Firefox:

[![Portal internetowy](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Rozpocznij uczenie modeli i śledzenie historii uruchamiania przy użyciu nowego interfejsu wiersza polecenia i zestawu SDK. Możesz dowiedzieć się, jak z [samouczka: szkolenie modeli za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Czy projekty będą utrwalane?

Nie stracisz żadnego kodu ani wykonanej pracy. W starszej wersji projekty są jednostkami chmury z katalogiem lokalnym. W najnowszej wersji dołączasz katalogi lokalne do obszaru roboczego usługi Azure Machine Learning przy użyciu lokalnego pliku konfiguracyjnego. Zobacz [diagram przedstawiający najnowszą architekturę](concept-azure-machine-learning-architecture.md).

Większa część zawartości projektu znajduje się już na komputerze lokalnym. Wystarczy utworzyć w odpowiednim katalogu plik konfiguracji i odwołać się do niego w kodzie, aby nawiązać połączenie z obszarem roboczym. Aby nadal korzystać z katalogu lokalnego zawierającego pliki i skrypty, określ nazwę katalogu w `az ml project attach` poleceniu [Pythona "experiment.submit"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) lub za pomocą polecenia CLI.  Przykład:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Utwórz obszar roboczy,](how-to-manage-workspace.md) aby rozpocząć.

## <a name="what-about-my-registered-models-and-images"></a>Co się stanie z moimi zarejestrowanymi modelami i obrazami?

Modele zarejestrowane w starym rejestrze modeli trzeba migrować do nowego obszaru roboczego, aby nadal z nich korzystać. Aby przeprowadzić migrację modeli, pobierz modele i ponownie zarejestruj je w nowym obszarze roboczym.

Obrazów utworzonych w starym rejestrze obrazów nie można bezpośrednio migrować do nowego obszaru roboczego. W większości przypadków model można wdrożyć bez konieczności tworzenia obrazu. W razie potrzeby można utworzyć obraz dla modelu w nowym obszarze roboczym. Aby uzyskać więcej informacji, zobacz [Zarządzanie, rejestrowanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Co się stanie z wdrożonymi usługami internetowymi?

Teraz, gdy obsługa starego interfejsu wiersza polecenia została zakończona, nie możesz już ponownie wdrażać modeli ani zarządzać usługami internetowymi, które pierwotnie wdrożono za pomocą konta Zarządzania modelami. Jednak te usługi internetowe będą dalej działać tak długo, jak będzie obsługiwana usługa Azure Container Service (ACS).

W najnowszej wersji modele są wdrażane jako usługi internetowe w klastrach usługi Azure Container Instances (ACI) lub Azure Kubernetes Service (AKS). Można je również wdrażać w układach FPGA oraz w usłudze Azure IoT Edge.

Aby dowiedzieć się więcej, zobacz następujące artykuły:
+ [Where and how to deploy models (Gdzie i jak wdrażać modele)](how-to-deploy-and-where.md)
+ [Samouczek: Wdrażanie modeli za pomocą usługi Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [najnowszej architekturze usługi Azure Machine Learning.](concept-azure-machine-learning-architecture.md)

Aby zapoznać się z omówieniem usługi, przeczytaj [artykuł Co to jest usługa Azure Machine Learning?](overview-what-is-azure-ml.md).

Utwórz pierwszy eksperyment z preferowaną metodą:
  + [Korzystanie z notesów języka Python](tutorial-1st-experiment-sdk-setup.md)
  + [Użyj r Markdown](tutorial-1st-r-experiment.md) 
  + [Korzystanie ze zautomatyzowanego uczenia maszynowego](tutorial-designer-automobile-price-train-score.md) 
  + [Korzystanie z funkcji przeciągania & upuszczania projektanta](tutorial-first-experiment-automated-ml.md) 
  + [Użyj rozszerzenia ML do interfejsu wiersza polecenia](tutorial-train-deploy-model-cli.md)
