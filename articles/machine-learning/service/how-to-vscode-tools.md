---
title: Użyj programu Visual Studio Code za pomocą
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zainstalować usługi Azure Machine Learning dla programu Visual Studio Code i tworzenie prostego eksperymentu w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818753"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Rozpoczynanie pracy z usługą Azure Machine Learning dla programu Visual Studio Code

W tym artykule nauczysz się używać **usługi Azure Machine Learning dla programu Visual Studio Code** rozszerzenia uczenie i wdrażanie usługi machine learning i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning w programie Visual Studio Code (VS Code).

Usługa Azure Machine Learning zapewnia obsługę przeprowadzania eksperymentów, lokalnie i na zdalnym obliczeniowych elementów docelowych. Dla każdego eksperymentu można śledzenie bieżącego wielu uruchomień na potrzeby często iteracyjne spróbuj różnych technik, hiperparametrów i inne. Usługa Azure Machine Learning służy do śledzenia niestandardowe metryki i eksperymentować przebiegów, umożliwiając odtwarzaniem do nauki o danych i sprawdzalność.

I wdrożeniem tych modeli do potrzeb testowania i produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

+ Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

+ Musi być zainstalowany program Visual Studio Code. Program VS Code jest źródło lekki, ale Zaawansowany edytor kodu, działającą na pulpicie. Pochodzi z wbudowaną obsługą języka Python i innych.  [Dowiedz się, jak zainstalować program VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Zainstaluj język Python 3.5 lub nowszej](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Instalowanie usługi Azure Machine Learning dla rozszerzenia programu VS Code

Po zainstalowaniu **usługi Azure Machine Learning** rozszerzenia dwóch więcej rozszerzeń są automatycznie instalowane (Jeśli masz dostęp do Internetu). Są one [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozszerzenia i [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) rozszerzenia

Aby pracować z usługą Azure Machine Learning, musimy włączyć program VS Code w środowisko IDE języka Python. Praca z [języka Python w programie Visual Studio Code](https://code.visualstudio.com/docs/languages/python), wymaga rozszerzenia Microsoft Python, który zostanie automatycznie zainstalowana z rozszerzeniem usługi Azure Machine Learning. Rozszerzenie sprawia, że program VS Code doskonałym środowisku IDE i działa w dowolnym systemie operacyjnym z różnymi rodzajami interpreterów języka Python. Wykorzystuje wszystkie mocy programu VS Code w celu zapewnienia Autouzupełnianie i technologii IntelliSense, Zaznaczanie błędów, debugowania i testowania jednostkowego, wraz z możliwością łatwego przełączania się między środowiskami Python, w tym wirtualnych i środowisk conda. Zapoznaj się z tego przewodnika, edytowania, uruchomiona i debugowanie kodu w języku Python, zobacz [samouczek języka Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial)

**Aby zainstalować rozszerzenia usługi Azure Machine Learning:**

1. Uruchom program VS Code.

1. W przeglądarce odwiedź stronę: [Usługa Azure Machine Learning dla programu Visual Studio Code (wersja zapoznawcza)](https://aka.ms/vscodetoolsforai) rozszerzenia

1. Na tej stronie internetowej kliknij **zainstalować**. 

1. Na karcie rozszerzenia kliknij **zainstalować**.

1. Zapraszamy zostanie otwarta karta w programie VS Code dla rozszerzenia, a symbol usługi Azure (opisanych w czerwonym prostokątem na ilustracji poniżej) jest dodawany do pasku działań.

   ![Usługa Azure ikona na pasku działań programu Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. W oknie dialogowym kliknij **Sign In** i postępuj zgodnie z wyświetlanymi na ekranie po wyświetleniu monitu uwierzytelniania w systemie Azure. 
   
   Rozszerzenie Azure Account programu, który został zainstalowany wraz z usługi Azure Machine Learning dla rozszerzenia programu VS Code, służy do uwierzytelniania przy użyciu konta platformy Azure. Zapoznać się z listą poleceń w [rozszerzenie Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) strony.

> [!Tip] 
> Zapoznaj się z [rozszerzenia IntelliCode dla programu VS Code (wersja zapoznawcza)](https://go.microsoft.com/fwlink/?linkid=2006060). Funkcja IntelliCode zapewnia zestaw funkcji, wspierane przez Sztuczną inteligencję dla technologii IntelliSense w języku Python, takich jak wnioskowanie najistotniejsze automatycznego uzupełniania oparciu o bieżący kontekst kodu.

## <a name="azure-ml-sdk-installation"></a>Zestaw SDK usługi Azure ML instalacji

1. Upewnij się, że języka Python w wersji 3.5 lub nowszej jest zainstalowane i rozpoznawane przez program VS Code. Jeśli zainstalujesz go teraz, uruchom ponownie program VS Code i wybierz interpreter języka Python, zgodnie z instrukcjami przedstawionymi w https://code.visualstudio.com/docs/python/python-tutorial.

1. W oknie terminalu zintegrowanego, należy określić interpreter języka Python do użycia lub mogą trafić **Enter** do użycia z Twojej domyślnej interpreter języka Python.

   ![Wybierz interpreter](./media/vscode-tools-for-ai/python.png)

1. W prawym dolnym rogu okna wyświetlane powiadomienie wskazujące, że zestaw SDK usługi Azure ML jest instalowany automatycznie.    Lokalne prywatne środowisko Python jest tworzony, który ma wymagania wstępne programu Visual Studio Code do pracy z usługą Azure Machine Learning.

   ![Zainstaluj zestaw SDK usługi Azure Machine Learning dla języka Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Rozpocznij pracę z usługą Azure Machine Learning

Przed rozpoczęciem szkolenia i wdrażania modeli uczenia maszynowego przy użyciu programu VS Code, należy utworzyć [obszarze roboczym usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) w chmurze, które zawierają modeli i zasobów. Dowiedz się, jak utworzyć i Utwórz swoje pierwsze eksperymentalne rozwiązanie w tym obszarze roboczym.

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

   [![install](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Kliknij prawym przyciskiem myszy subskrypcji platformy Azure, a następnie wybierz pozycję **Utwórz obszar roboczy**. Zostanie wyświetlona lista. W animowany obraz bezpłatna wersja próbna jest nazwa subskrypcji i obszar roboczy jest "TeamWorkspace". 

1. Wybierz istniejącą grupę zasobów z listy lub Utwórz nowe konto, za pomocą kreatora w palecie poleceń.

1. W polu wpisz unikatowy i wyczyść nazwę nowego obszaru roboczego. Zrzuty ekranu obszar roboczy ma nazwę "TeamWorkspace".

1. Naciśnij klawisz enter, i zostanie utworzony nowy obszar roboczy. Wygląda na to w drzewie poniżej nazwę subskrypcji.

1. Kliknij prawym przyciskiem myszy w węźle eksperymentu, a następnie wybierz **tworzenia eksperymentu** z menu kontekstowego.  Eksperymenty zachować informacje o przebiegów przy użyciu usługi Azure Machine Learning.

1. W polu wprowadź nazwę swojego eksperymentu. Zrzuty ekranu eksperymentu nosi nazwę "Mnist ręcznie ZAPISANYCH".
 
1. Naciśnij klawisz enter i zostanie utworzony nowy eksperyment. Wygląda na to w drzewie poniżej nazwę obszaru roboczego.

1. Można kliknij prawym przyciskiem myszy na eksperyment w obszarze roboczym i wybierz pozycję "Ustaw jako aktywny eksperymentu". **"Aktywny"** eksperymentu jest eksperyment aktualnie używasz, i folderu Otwórz w programie VS Code zostanie połączony z tego eksperymentu w chmurze. Ten folder powinien zawierać lokalnych skryptów w języku Python.

   Teraz każdy eksperymentu działa z eksperymentu, więc wszystkie kluczowe metryki będą zapisywane w historii eksperymentu i modeli, które możesz później uzyskać automatycznie przekazany do usługi Azure Machine Learning i przechowywane w usłudze Dzienniki i metryki eksperymentu.

   [![Dołącz folderu w programie VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Tworzenie i zarządzanie nimi obliczeniowych elementów docelowych

Za pomocą usługi Azure Machine Learning dla programu VS Code możesz Przygotuj dane, uczyć modele i wdrażania ich zarówno lokalnie, a na zdalnym obliczeniowych elementów docelowych.

To rozszerzenie obsługuje kilka elementów docelowych obliczeniowej zdalnego dla usługi Azure Machine Learning. Zobacz [pełną listę obsługiwanych obliczeniowych elementów docelowych](how-to-set-up-training-targets.md) dla usługi Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Tworzenie obliczeniowych elementów docelowych dla usługi Azure Machine Learning w programie VS Code

**Aby utworzyć cel obliczenia:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

2. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning. W animowany obraz bezpłatna wersja próbna jest nazwa subskrypcji i obszar roboczy jest "TeamWorkspace". 

3. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **obliczenia** węzeł i wybierz polecenie **Tworzenie obliczeń**.

4. Wybierz typ docelowy obliczeń z listy. 

5. W palecie poleceń Wybierz rozmiar maszyny wirtualnej.

6. W palecie poleceń wprowadź nazwę dla elementu docelowego obliczeń w polu. 

7. Określ wszelkie właściwości zaawansowane w pliku konfiguracji JSON, który zostanie otwarty na nowej karcie. Można określić właściwości, takie jak maksymalnej liczby węzłów.

8. Po zakończeniu konfigurowania obliczeniowego elementu docelowego, kliknij przycisk **przesyłania** w prawym dolnym rogu ekranu.

Oto przykład tworzenia Azure obliczeniowego usługi Machine Learning (AMLCompute): [![Utwórz AML obliczeń w programie VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Plik "konfiguracji uruchamiania"

Rozszerzenie programu VS Code zostanie automatycznie utworzyć cel obliczenia lokalne i uruchom konfiguracje dla Twojego **lokalnego** i **docker** środowisk na komputerze lokalnym. Pliki konfiguracji uruchamiania można znaleźć w węźle docelowym skojarzonych obliczeń. 

## <a name="train-and-tune-models"></a>Szkolenie i dostrajanie modeli

Użyj usługi Azure Machine Learning dla programu VS Code (wersja zapoznawcza), szybkie powtarzanie czynności w kodzie, krok po kroku i debugowania i korzystania z rozwiązania kontroli kodu źródłowego wybranego. 

**Aby uruchomić eksperyment lokalnie za pomocą usługi Azure Machine Learning:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

1. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning. 

1. W węźle obszaru roboczego rozwiń **obliczenia** węzła i kliknij prawym przyciskiem myszy **konfiguracji uruchamiania** obliczeń, który chcesz użyć. 

1. Wybierz **uruchamiania eksperymentu**.

1. Wybierz skrypt do uruchomienia w Eksploratorze plików. 

1. Kliknij przycisk **uruchamiania eksperymentu widoku** się zintegrowane portalu usługi Azure Machine Learning w celu monitorowania przebiegów i zobacz wytrenowane modele.

Poniżej przedstawiono przykład uruchamiania eksperymentu lokalnie: [![Uruchomienie eksperymentu lokalnie](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Użyj zdalnego obliczeń dla eksperymentów w programie VS Code

Aby używać zdalnego obliczeniowego elementu docelowego podczas szkolenia, musisz utworzyć plik konfiguracji przebiegu. Ten plik nakazuje usługi Azure Machine Learning nie tylko miejsca uruchomić eksperyment, ale również, jak przygotować środowisko.

#### <a name="the-conda-dependencies-file"></a>Pliku zależności conda

Domyślnie zostanie utworzony nowy środowiska conda i zależności instalacji są zarządzane. Jednakże, należy określić zależności i ich wersji w `aml_config/conda_dependencies.yml` pliku. 

Jest to fragment z domyślną aml_config/conda_dependencies.yml. Na przykład można określić "tensorflow = 1.12.0' jak pokazano poniżej. Jeśli nie określisz wersji zależności, najnowsza wersja będzie używany.  
Możesz dodać dodatkowe zależności w pliku konfiguracji.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**Uruchamianie eksperymentu przy użyciu usługi Azure Machine Learning na zdalnym obliczeniowego elementu docelowego:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

1. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning. 

1. Kliknij prawym przyciskiem myszy nad skryptem języka python w oknie edytora, a następnie wybierz pozycję **AML: Uruchom jako eksperyment w usłudze Azure**. 

1. W palecie poleceń Wybierz obliczeniowego elementu docelowego. 

1. W palecie poleceń wprowadź nazwę konfiguracji uruchamiania w polu. 

1. Edytuj plik conda_dependencies.yml, aby określić zależności środowiska uruchomieniowego eksperymentu, a następnie kliknij przycisk **przesyłania** w prawym dolnym rogu ekranu. 

1. Kliknij przycisk **uruchamiania eksperymentu widoku** się zintegrowane portalu usługi Azure Machine Learning w celu monitorowania przebiegów i zobacz wytrenowane modele.

Poniżej przedstawiono przykład uruchamiania eksperymentów na zdalnym obliczeniowego elementu docelowego: [![Uruchamianie eksperymentu w zdalnym elemencie docelowym](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Wdrażanie modeli i zarządzania nimi
Usługę Azure Machine Learning umożliwia wdrażanie i zarządzanie nimi modeli w chmurze i na urządzeniach brzegowych usługi machine learning. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Zarejestruj swój model usługi Azure Machine Learning z programu VS Code

Teraz, gdy masz uczony model, należy zarejestrować go w obszarze roboczym.
Zarejestrowane modele można śledzić i wdrożony.

**Aby zarejestrować model:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

1. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning.

1. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **modeli** i wybierz polecenie **zarejestrować Model**.

1. W palecie poleceń wprowadź nazwę modelu, w tym polu. 

1. Z listy wybierz, czy chcesz przekazać **pliku modelu** (dla pojedynczego modeli) **folder modelu** (w przypadku modeli z wielu plików, takich jak Tensorflow). 

1. Wybieranie pliku lub folderu.

1. Po zakończeniu konfigurowania właściwości modelu, kliknij przycisk **przesyłania** w prawym dolnym rogu ekranu. 

Oto przykład rejestrowanie przez model AML: [![Rejestrowanie modelu do AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>Wdrażanie usługi z programu VS Code

Program VS Code można wdrożyć usługę sieci web w taki sposób, aby:
+ Wystąpienia kontenerów platformy Azure (ACI): do testowania
+ Usługa Azure Kubernetes Service (AKS): w środowisku produkcyjnym 

Nie trzeba utworzyć kontener usługi ACI, aby przetestować z wyprzedzeniem, ponieważ są one tworzone na bieżąco. Jednak klastrów AKS należy wcześniej skonfigurować. 

Dowiedz się więcej o [wdrażanie za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md) ogólnie rzecz biorąc.

**Wdrażanie usługi sieci web:**

1. Kliknij ikonę platformy Azure, w pasku działań programu Visual Studio Code. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

1. W widoku drzewa rozwiń subskrypcji platformy Azure i obszaru roboczego usługi Azure Machine Learning.

1. W obszarze węzła obszaru roboczego, należy rozwinąć **modeli** węzła.

1. Kliknij prawym przyciskiem myszy modelu, który chcesz wdrożyć, a następnie wybierz **wdrażanie usługi z modelu zarejestrowany** polecenia z menu kontekstowego.

1. W palecie poleceń Wybierz obliczeniowego elementu docelowego, do której ma zostać wdrożony na liście. 

1. W palecie poleceń wprowadź nazwę dla tej usługi, w tym polu.  

1. W palecie poleceń naciśnij klawisz Enter na klawiaturze, aby przeglądać i wybrać plik skryptu.

1. W palecie poleceń naciśnij klawisz Enter na klawiaturze, aby przeglądać i wybrać pliku zależności conda.

1. Po zakończeniu konfigurowania właściwości usługi, kliknij przycisk **przesyłania** w prawym dolnym rogu ekranu, aby wdrożyć. W tym pliku właściwości usługi można określić w lokalnym pliku platformy Docker lub pliku schema.json, który chcesz użyć.

Teraz jest wdrożona usługa sieci web.

Oto przykład wdrażanie usługi sieci web: [![Wdrażanie usługi sieci web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Używanie skrótów klawiaturowych

Najbardziej programu VS Code funkcje usługi Azure Machine Learning w programie VS Code są dostępne z klawiatury. Najważniejsze kombinację klawiszy, aby wiedzieć, jest Ctrl + Shift + P, co umożliwia wyświetlenie paletę poleceń. W tym miejscu możesz mieć dostęp do wszystkich funkcji programu VS Code, w tym skrótów klawiaturowych dla typowych operacji.

[![Skróty klawiaturowe dla usługi Azure Machine Learning dla programu VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie szkolenie przy użyciu usługi Machine Learning spoza programu VS Code, przeczytaj [samouczka: Szkolenie modeli za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).

Omówienie edytowania, uruchamiania i debugowania kodu lokalnie, zobacz [samouczek języka Python Hello World](https://code.visualstudio.com/docs/python/python-tutorial)
