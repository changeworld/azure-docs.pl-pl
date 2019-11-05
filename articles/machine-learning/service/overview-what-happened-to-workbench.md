---
title: Co się stało z aplikacją Machine Learning Workbench?
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat tego, co się stało z aplikacją Machine Learning Workbench, co zmieniło się w Azure Machine Learning i jaka jest oś czasu pomocy technicznej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 3c98da50d0c50a7fdb94f579444f3c09c1096d18
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476421"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Co się stało z aplikacją Azure Machine Learning Workbench?

Aplikacja Azure Machine Learning Workbench i niektóre inne wczesne funkcje były przestarzałe i zastąpione w wersji **2018 września** w celu zapewnienia lepszej [architektury](concept-azure-machine-learning-architecture.md).

Wersja zawiera wiele znaczących aktualizacji wprowadzonych w oparciu o opinie klientów, które mają na celu udoskonalenie środowiska użytkownika. Podstawowe funkcje z przebiegów eksperymentu w zakresie wdrażania modelu nie uległy zmianie. Ale teraz można użyć niezawodnego <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK języka Python</a>, zestawu R SDK i [interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) do wykonywania zadań i potoków uczenia maszynowego.

Większość artefaktów, które zostały utworzone we wcześniejszej wersji Azure Machine Learning są przechowywane w magazynie lokalnym lub w chmurze. Te artefakty nigdy nie zostaną usunięte.

Z tego artykułu dowiesz się, co się zmieniło i jak zmiany wpłynęły na zadania wykonywane obecnie przy użyciu aplikacji Azure Machine Learning Workbench i jej interfejsów API.

>[!Warning]
>Ten artykuł nie jest przeznaczony dla użytkowników środowiska Azure Machine Learning Studio. Dotyczy to Azure Machine Learning klientów, którzy zainstalowali aplikację Workbench (wersja zapoznawcza) i/lub mają konta eksperymentowanie i Zarządzanie modelami w wersji zapoznawczej.


## <a name="what-changed"></a>Co się zmieniło?

Najnowsza wersja Azure Machine Learning obejmuje następujące funkcje:
+ [Uproszczony model zasobów platformy Azure](concept-azure-machine-learning-architecture.md).
+ [Nowy interfejs użytkownika portalu](how-to-track-experiments.md) służący do zarządzania eksperymentami i celami obliczeniowymi.
+ Nowy, bardziej kompleksowy <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestaw SDK</a> języka Python.
+ Nowe, rozbudowane [rozszerzenie interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md) na potrzeby uczenia maszynowego.

[Architektura](concept-azure-machine-learning-architecture.md) została zaprojektowana od nowa, aby ułatwić obsługę. Zamiast wielu zasobów i kont platformy Azure, będziesz potrzebować tylko aplikacji [Azure Machine Learning Workspace](concept-workspace.md). Obszary robocze można szybko tworzyć w witrynie [Azure Portal](how-to-manage-workspace.md). Dzięki użyciu obszaru roboczego wielu użytkowników może przechowywać cele obliczeniowe szkoleń i wdrożeń, eksperymenty dotyczące modeli, obrazy platformy Docker, wdrożone modele itd.

Bieżąca wersja udostępnia nowe, ulepszone klienty interfejsu wiersza polecenia i zestawu SDK, ale sama klasyczna aplikacja Workbench została wycofana z użytku. Eksperymenty mogą być zarządzane za pomocą [pulpitu nawigacyjnego obszaru roboczego w programie Azure Machine Learning Studio](how-to-track-experiments.md#view-the-experiment-in-the-web-portal). Pulpit nawigacyjny umożliwia pobieranie historii, zarządzanie celami obliczeniowymi dołączonymi do obszaru roboczego, zarządzanie modelami i obrazami platformy Docker, a nawet wdrażanie usług internetowych.

<a name="timeline"></a>

## <a name="support-timeline"></a>Oś czasu pomocy technicznej

9 stycznia 2019 r. zakończyła się obsługa kont aplikacji Machine Learning Workbench, Eksperymentowania w usłudze Azure Machine Learning i Zarządzania modelami oraz skojarzonego z nimi zestawu SDK i interfejsu wiersza polecenia.

Wszystkie najnowsze możliwości są dostępne za pomocą tego <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestawu SDK</a>, [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) i [portalu](how-to-manage-workspace.md).

## <a name="what-about-run-histories"></a>A co z historią uruchamiania?

Starsze historie uruchamiania nie są już dostępne, ale nadal można wyświetlać uruchomienia w najnowszej wersji.

Historie uruchamiania są teraz nazywane **eksperymentami**. Można zbierać eksperymenty modelu i eksplorować je za pomocą zestawu SDK, interfejsu wiersza polecenia lub Azure Machine Learning Studio.

Pulpit nawigacyjny obszaru roboczego w portalu jest obsługiwany tylko w przeglądarkach Microsoft Edge, Chrome i Firefox:

[Portal ![online](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Rozpocznij uczenie modeli i śledzenie historii uruchamiania przy użyciu nowego interfejsu wiersza polecenia i zestawu SDK. Możesz dowiedzieć się, jak za pomocą [samouczka: uczenie modeli za pomocą Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="can-i-still-prep-data"></a>Czy nadal można przygotowywać dane?

Istniejących plików przygotowywania danych nie można przenosić do najnowszej wersji, ponieważ nie ma już aplikacji Machine Learning Workbench. Można jednak nadal przygotowywać zestaw danych dowolnego rozmiaru do modelowania.

W przypadku zestawów danych o dowolnym rozmiarze można użyć [pakietu przygotowywania danych dla Azure Machine Learning](https://aka.ms/data-prep-sdk) , aby szybko przygotować dane przed modelem, pisząc kod języka Python.

## <a name="will-projects-persist"></a>Czy projekty będą utrwalane?

Nie stracisz żadnego kodu ani wykonanej pracy. W starszej wersji projekty są jednostkami chmury z katalogiem lokalnym. W najnowszej wersji dołączysz katalogi lokalne do obszaru roboczego Azure Machine Learning przy użyciu lokalnego pliku konfiguracji. Zobacz [diagram przedstawiający najnowszą architekturę](concept-azure-machine-learning-architecture.md).

Większa część zawartości projektu znajduje się już na komputerze lokalnym. Wystarczy utworzyć w odpowiednim katalogu plik konfiguracji i odwołać się do niego w kodzie, aby nawiązać połączenie z obszarem roboczym. Aby nadal korzystać z lokalnego katalogu zawierającego pliki i skrypty, należy określić nazwę katalogu w poleceniu ["eksperyment. Submit"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python lub przy użyciu polecenia `az ml project attach`  Na przykład:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Utwórz obszar roboczy](how-to-manage-workspace.md) , aby rozpocząć.

## <a name="what-about-my-registered-models-and-images"></a>Co się stanie z moimi zarejestrowanymi modelami i obrazami?

Modele zarejestrowane w starym rejestrze modeli trzeba migrować do nowego obszaru roboczego, aby nadal z nich korzystać. Aby przeprowadzić migrację modeli, pobierz modele i ponownie zarejestruj je w nowym obszarze roboczym.

Obrazów utworzonych w starym rejestrze obrazu nie można bezpośrednio migrować do nowego obszaru roboczego. W większości przypadków model można wdrożyć bez konieczności tworzenia obrazu. W razie potrzeby można utworzyć obraz dla modelu w nowym obszarze roboczym. Aby uzyskać więcej informacji, zobacz artykuł [Zarządzanie, rejestrowanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Co się stanie z wdrożonymi usługami internetowymi?

Teraz, gdy obsługa starego interfejsu wiersza polecenia została zakończona, nie możesz już ponownie wdrażać modeli ani zarządzać usługami internetowymi, które pierwotnie wdrożono za pomocą konta Zarządzania modelami. Jednak te usługi internetowe będą dalej działać tak długo, jak będzie obsługiwana usługa Azure Container Service (ACS).

W najnowszej wersji modele są wdrażane jako usługi internetowe w klastrach usługi Azure Container Instances (ACI) lub Azure Kubernetes Service (AKS). Można je również wdrażać w układach FPGA oraz w usłudze Azure IoT Edge.

Aby dowiedzieć się więcej, zobacz następujące artykuły:
+ [Where and how to deploy models (Gdzie i jak wdrażać modele)](how-to-deploy-and-where.md)
+ [Samouczek: Wdrażanie modeli przy użyciu Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="what-about-the-old-sdk-and-cli"></a>Co stanie się ze starym zestawem SDK i interfejsem wiersza polecenia?

Zobacz powyższą [oś czasu](#timeline). Zalecamy rozpoczęcie tworzenia nowych eksperymentów i modeli przy użyciu najnowszego zestawu SDK lub interfejsu wiersza polecenia.

Korzystając z nowego zestawu SDK języka Python w najnowszej wersji, można korzystać z Azure Machine Learning w dowolnym środowisku języka Python. Dowiedz się, jak zainstalować najnowszy <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">zestaw SDK</a>. Można również użyć [zaktualizowanego rozszerzenia interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md) z bogatym zestawem poleceń `az ml`, aby pracować z usługą w dowolnym środowisku wiersza polecenia, w tym w usłudze Azure Cloud Shell.

## <a name="what-about-visual-studio-code-tools-for-ai"></a>A co z narzędziami Visual Studio Code Tools for AI?

W najnowszej wersji nazwa rozszerzenia została zmieniona na Azure Machine Learning dla programu Visual Studio Code, a rozszerzenie zostało rozbudowane i ulepszone w celu umożliwienia współpracy ze wspomnianymi powyżej nowymi funkcjami.

[![Azure Machine Learning Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)](./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Co się stanie z pakietami domen?

Pakietów domen funkcji przetwarzania obrazów, analizy tekstu i prognozowania nie można używać z najnowszą wersją usługi Azure Machine Learning. Można jednak nadal kompilować i uczyć modele przetwarzania obrazów, analizy tekstu oraz prognozowania przy użyciu najnowszego zestawu <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a> języka Python dla usługi Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

Poznaj [najnowszą architekturę Azure Machine Learning](concept-azure-machine-learning-architecture.md).

Aby zapoznać się z omówieniem usługi, przeczytaj artykuł [co to jest Azure Machine Learning?](overview-what-is-azure-ml.md).

Utwórz pierwszy eksperyment przy użyciu preferowanej metody:
  + [Korzystanie z notesów Python do uczenia & Wdrażanie modeli ML](tutorial-1st-experiment-sdk-setup.md)
  + [Używanie R Markdown do uczenia & wdrażania modeli ML]( tutorial-1st-r-experiment.md) 
  + [Używanie automatycznej uczenia maszynowego do uczenia & Wdrażanie modeli ML](ui-tutorial-automobile-price-train-score.md) 
  + [Korzystanie z funkcji upuszczania & przez projektanta w celu uczenia & wdrożenia](tutorial-first-experiment-automated-ml.md) 
  + [Korzystanie z interfejsu wiersza polecenia Machine Learning w celu uczenia i wdrożenia modelu](tutorial-train-deploy-model-cli.md)