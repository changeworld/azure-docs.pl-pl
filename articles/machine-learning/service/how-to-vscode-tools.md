---
title: Użyj programu Visual Studio Code dla usługi machine learning
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
ms.openlocfilehash: 19873256f8253fff75cfd42df7b876106a9e98e5
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464787"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Rozpoczynanie pracy z usługą Azure Machine Learning dla programu Visual Studio Code

W tym artykule dowiesz się, jak za pomocą rozszerzenia dla usługi Azure Machine Learning dla programu Visual Studio Code uczenie i wdrażanie usługi machine learning i głębokiego uczenia modeli za pomocą usługi Azure Machine Learning w programie Visual Studio Code.

Usługa Azure Machine Learning zapewnia obsługę eksperymenty, w których uruchamiane lokalnie i zdalnego obliczeniowych elementów docelowych. Dla każdego eksperymentu można śledzenie bieżącego wielu uruchomień na potrzeby często iteracyjne spróbuj różnych technik, hiperparametrów i inne. Usługa Azure Machine Learning służy do śledzenia niestandardowe metryki i eksperymentować przebiegów, umożliwiając odtwarzaniem do nauki o danych i sprawdzalność.

Można także wdrożyć tych modeli do potrzeb testowania i produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

+ Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Spróbuj [bezpłatnej i płatnej wersji usługi Azure Machine Learning](https://aka.ms/AMLFree).

+ Musi być zainstalowany program Visual Studio Code. Visual Studio Code jest źródło lekki, ale Zaawansowany edytor kodu, działającą na pulpicie. Pochodzi z wbudowaną obsługą języka Python i innych językach programowania. Jeśli jeszcze nie zainstalowano programu Visual Studio Code, [Dowiedz się, jak](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Zainstaluj język Python w wersji 3.5 lub nowszej](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Zainstaluj rozszerzenie dla usługi Azure Machine Learning dla programu Visual Studio Code

Po zainstalowaniu rozszerzenia usługi Azure Machine Learning, dwa więcej rozszerzeń są instalowane automatycznie (Jeśli masz dostęp do Internetu). Są one [rozszerzenie Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) i [rozszerzenia Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Aby pracować z usługą Azure Machine Learning, należy włączyć Visual Studio Code w środowisko programistyczne języka Python, zintegrowane (IDE). Potrzebujesz rozszerzenia Microsoft Python, aby użyć [języka Python w programie Visual Studio Code](https://code.visualstudio.com/docs/languages/python). To rozszerzenie jest automatycznie instalowany z rozszerzeniem usługi Azure Machine Learning. Rozszerzenie sprawia, że Visual Studio Code doskonałym środowisku IDE i działa w dowolnym systemie operacyjnym, z różnymi rodzajami interpreterów języka Python. Rozszerzenia Microsoft Python używa pełnych możliwości programu Visual Studio Code, aby zapewnić, automatycznego uzupełniania, IntelliSense, Zaznaczanie błędów, debugowania i testowania jednostek. To rozszerzenie umożliwia również łatwo przełączać się między środowiskami Python, w tym wirtualnych i środowisk conda. Aby uzyskać więcej informacji dotyczących edytowania, uruchamiania i debugowania kodu w języku Python, zobacz [samouczek języka Python dla Witaj, świecie](https://code.visualstudio.com/docs/python/python-tutorial).

Aby zainstalować rozszerzenia usługi Azure Machine Learning:

1. Otwórz program Visual Studio Code.

1. W przeglądarce internetowej przejdź do [usługi Azure Machine Learning do rozszerzenia programu Visual Studio Code (wersja zapoznawcza)](https://aka.ms/vscodetoolsforai).

1. Na tej stronie sieci web wybierz **zainstalować**. 

1. Na karcie rozszerzenia, wybierz **zainstalować**.

1. Zostanie otwarta w programie Visual Studio Code powitalnej karta rozszerzenia, a symbol usługi Azure (opisanych na czerwono na poniższym zrzucie ekranu) jest dodawany do pasku działań.

   ![Usługa Azure ikonę na pasku działań programu Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. W oknie dialogowym wybierz **Sign In** i postępuj zgodnie z monitami, aby uwierzytelnić się za pomocą platformy Azure. 
   
   Rozszerzenie Azure Account programu, który został zainstalowany wraz z usługi Azure Machine Learning dla rozszerzenia programu Visual Studio Code, służy do uwierzytelniania przy użyciu konta platformy Azure. Aby uzyskać listę poleceń, zobacz stronę dla [rozszerzenie Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Zapoznaj się z [rozszerzenia IntelliCode dla programu Visual Studio Code (wersja zapoznawcza)](https://go.microsoft.com/fwlink/?linkid=2006060). Funkcja IntelliCode zapewnia zestaw funkcji, wspierane przez Sztuczną inteligencję dla technologii IntelliSense w języku Python, takich jak wnioskowanie najistotniejsze autocompletions, w oparciu o bieżący kontekst kodu.

## <a name="install-the-azure-machine-learning-sdk"></a>Zainstaluj aplikację Azure Machine Learning zestawu SDK

1. Upewnij się, że Python 3.5 lub nowszy jest zainstalowany i jest rozpoznawany przez Visual Studio Code. Jeśli zainstalujesz go teraz, ponowne uruchomienie programu Visual Studio Code i [wybierz interpreter języka Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. W oknie terminalu zintegrowanego Określ interpreter języka Python do użycia. Lub wybierz Enter, aby użyć interpreter języka Python usługi domyślne.

   ![Wybierz interpreter](./media/vscode-tools-for-ai/python.png)

1. W prawym dolnym rogu okna pojawi się powiadomienie, wskazujący, że zestaw SDK usługi Azure Machine Learning jest instalowany automatycznie. Nowo utworzony środowisko Python jest lokalny i prywatne i ma wymagania wstępne programu Visual Studio Code do pracy z usługą Azure Machine Learning.

   ![Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Rozpocznij pracę z usługą Azure Machine Learning

Przed rozpoczęciem szkolenia i wdrażania modeli uczenia maszynowego w programie Visual Studio Code, należy utworzyć [obszarze roboczym usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) w chmurze. Ten obszar roboczy będzie zawierać modeli i zasobów. 

Aby utworzyć obszar roboczy i dodać swoje pierwsze eksperymentalne rozwiązanie:

1. Na pasku działań programu Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

   [![Tworzenie obszaru roboczego](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Kliknij prawym przyciskiem myszy subskrypcji platformy Azure, a następnie wybierz pozycję **Utwórz obszar roboczy**. Zostanie wyświetlona lista. W przykładzie animowany obraz jest nazwa subskrypcji **bezpłatnej wersji próbnej**, a obszar roboczy jest **TeamWorkspace**. 

1. Wybierz grupę zasobów z listy lub Utwórz nową za pomocą kreatora w palecie poleceń.

1. W polu wpisz unikatowy i wyczyść nazwę nowego obszaru roboczego. W przykładowej ilustracji nosi nazwę obszaru roboczego **TeamWorkspace**.

1. Naciśnij klawisz Enter, aby utworzyć nowy obszar roboczy. Wygląda na to w drzewie poniżej nazwę subskrypcji.

1. Kliknij prawym przyciskiem myszy **eksperymentu** węzeł i wybierz polecenie **tworzenia eksperymentu** z menu kontekstowego.  Eksperymenty zachować informacje o przebiegów przy użyciu usługi Azure Machine Learning.

1. W polu wprowadź nazwę swojego eksperymentu. W przykładowe zrzuty ekranów, nosi nazwę eksperymentu **mnist ręcznie ZAPISANYCH**.
 
1. Naciśnij klawisz Enter, aby utworzyć nowy eksperyment. W drzewie poniżej nazwę obszaru roboczego pojawi się eksperymentu.

1. W obszarze roboczym, możesz kliknąć prawym przyciskiem myszy eksperyment, aby ustawić go jako **Active** eksperymentować. **Active** eksperymentu jest bieżącym eksperymentu. Twoje Otwórz folder w programie Visual Studio Code, który będzie połączony tego eksperymentu w chmurze. Ten folder powinien zawierać lokalnych skryptów w języku Python.

Teraz Twoje kluczowe metryki będą przechowywane w historii eksperymentu. Podobnie modele, które możesz później będzie można automatycznie przekazany do usługi Azure Machine Learning i przechowywane wraz ze swoją eksperymentu metryk i dzienników. 

[![Dołącz folderu w programie Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Tworzenie i zarządzanie nimi obliczeniowych elementów docelowych

Za pomocą usługi Azure Machine Learning dla programu Visual Studio Code możesz Przygotuj dane, uczyć modele i wdrażania ich zarówno lokalnie, a na zdalnym obliczeniowych elementów docelowych.

Rozszerzenie obsługuje kilku celów obliczeń zdalnego dla usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz pełną listę obsługiwanych [celów obliczeń dla usługi Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Tworzenie obliczeniowych elementów docelowych dla usługi Azure Machine Learning w programie Visual Studio Code

Aby utworzyć cel obliczenia:

1. Na pasku działań programu Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

2. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning. Na poniższej ilustracji przykład jest nazwa subskrypcji **bezpłatnej wersji próbnej**, a obszar roboczy jest **TeamWorkspace**. 

3. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **obliczenia** węzeł i wybierz polecenie **Tworzenie obliczeń**.

4. Wybierz typ docelowy obliczeń z listy. 

5. Na paletę poleceń Wybierz rozmiar maszyny wirtualnej.

6. Na paletę poleceń, w polu wprowadź nazwę obliczeniowego elementu docelowego. 

7. W pliku konfiguracji JSON, która zostanie otwarta na nowej karcie określ wszelkie właściwości zaawansowane. Można określić właściwości, takie jak maksymalnej liczby węzłów.

8. Po zakończeniu konfigurowania urządzenie docelowe obliczeń w prawym dolnym rogu okna wybierz **przesyłania**.

Oto przykład sposobu tworzenia obliczeniowych usługi Azure Machine Learning (AMLCompute):

[![Utwórz AML obliczeń w programie Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Plik konfiguracji przebiegu

Rozszerzenia programu Visual Studio Code automatycznie tworzy lokalne obliczeniowego elementu docelowego i wykonywania konfiguracji dla środowisk lokalnych i platformy docker na komputerze lokalnym. Można znaleźć plik konfiguracji uruchamiania w węźle docelowym skojarzonych obliczeń. 

## <a name="train-and-tune-models"></a>Szkolenie i dostrajanie modeli

Umożliwia usłudze Azure Machine Learning dla programu Visual Studio Code (wersja zapoznawcza) szybkie powtarzanie czynności w kodzie, krok po kroku i debugowania i użyć rozwiązania do kontroli kodu źródłowego. 

Aby uruchomić eksperyment lokalnie, za pomocą usługi Azure Machine Learning:

1. Na pasku działań programu Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

1. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning. 

1. W obszarze węzła obszaru roboczego, rozwiń węzeł **obliczenia** węzła i kliknij prawym przyciskiem myszy **konfiguracji uruchamiania** mocy obliczeniowej, którego chcesz użyć. 

1. Wybierz **uruchamiania eksperymentu**.

1. W Eksploratorze plików wybierz skrypt, który chcesz uruchomić. 

1. Wybierz **uruchamiania eksperymentu widoku** się zintegrowane portalu usługi Azure Machine Learning w celu monitorowania przebiegów i zobacz wytrenowane modele.

Poniżej przedstawiono przykładowy sposób uruchomić eksperyment lokalnie:

[![Lokalne uruchamianie eksperymentu](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Użyj zdalnego obliczeń dla eksperymentów w programie Visual Studio Code

Aby używać zdalnego obliczeniowego elementu docelowego dla szkolenia, musisz utworzyć plik konfiguracji przebiegu. Ten plik nakazuje usługi Azure Machine Learning nie tylko miejsca uruchomić eksperyment, ale również, jak przygotować środowisko.

#### <a name="the-conda-dependencies-file"></a>Pliku zależności conda

Domyślnie zostanie utworzony nowy środowiska conda, a instalacja zależności są zarządzane. Jednakże, należy określić zależności i ich wersji w *aml_config/conda_dependencies.yml* pliku. 

Poniższy fragment kodu z domyślnego *aml_config/conda_dependencies.yml* Określa `tensorflow=1.12.0`. Jeśli nie określisz wersji zależności, będą używane najnowszej wersji. Możesz dodać dodatkowe zależności w pliku konfiguracji.

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

Uruchamianie eksperymentu przy użyciu usługi Azure Machine Learning na zdalnym obliczeniowego elementu docelowego:

1. Na pasku działań programu Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

1. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning. 

1. W oknie edytora, kliknij prawym przyciskiem myszy skrypt w języku Python, a następnie wybierz **AML: Uruchom jako eksperyment w usłudze Azure**. 

1. Na paletę poleceń Wybierz obliczeniowego elementu docelowego. 

1. Na paletę poleceń, w polu wprowadź nazwę konfiguracji uruchamiania. 

1. Edytuj *conda_dependencies.yml* plik, aby określić zależności środowiska uruchomieniowego eksperymentu. Następnie w prawym dolnym rogu okna wybierz **przesyłania**. 

1. Wybierz **uruchamiania eksperymentu widoku** się zintegrowane portalu usługi Azure Machine Learning w celu monitorowania przebiegów i zobacz wytrenowane modele.

Poniżej przedstawiono przykładowy sposób uruchomić eksperyment na zdalnym obliczeniowego elementu docelowego:

[![Uruchamianie eksperymentu w zdalnym elemencie docelowym](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Wdrażanie modeli i zarządzania nimi
W usłudze Azure Machine Learning można wdrażać i zarządzać modeli w chmurze i na urządzeniach brzegowych usługi machine learning. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Zarejestruj swój model usługi Azure Machine Learning z programu Visual Studio Code

Skoro już uczony model, należy zarejestrować go w obszarze roboczym. Można śledzić i wdrażać modele zarejestrowane.

Aby zarejestrować model:

1. Na pasku działań programu Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

1. W widoku drzewa rozwiń Twojej subskrypcji platformy Azure i obszar roboczy usługi Azure Machine Learning.

1. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **modeli** i wybierz polecenie **zarejestrować Model**.

1. Na paletę poleceń, w polu wprowadź nazwę modelu. 

1. Z listy wybierz, czy można przekazać **pliku modelu** (dla pojedynczego modeli) lub **folder modelu** (w przypadku modeli z wielu plików, takich jak TensorFlow). 

1. Wybieranie pliku lub folderu.

1. Po zakończeniu konfigurowania właściwości modelu, w prawym dolnym rogu okna wybierz **przesyłania**. 

Poniżej przedstawiono przykładowy sposób zarejestrować model usługi Azure Machine Learning:

[![Rejestrowanie modelu do AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Wdrażanie usługi z programu Visual Studio Code

W programie Visual Studio Code możesz wdrożyć usługę sieci web w taki sposób, aby:
+ Usługa Azure Container Instances (ACI) do testowania.
+ Usługa Azure Kubernetes Service (AKS) w środowisku produkcyjnym.

Nie należy do utworzenia kontenera usługi ACI do testowania z wyprzedzeniem, ponieważ kontenerów usługi ACI są tworzone na bieżąco. Jednak należy wcześniej skonfigurować klastry usługi AKS. Aby uzyskać więcej informacji, zobacz [Wdrażaj modele za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

Wdrażanie usługi sieci web:

1. Na pasku działań programu Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlona na pasku bocznym usługi Azure Machine Learning.

1. W widoku drzewa rozwiń subskrypcji platformy Azure i obszaru roboczego usługi Azure Machine Learning.

1. W obszarze węzła obszaru roboczego, należy rozwinąć **modeli** węzła.

1. Kliknij prawym przyciskiem myszy modelu, który chcesz wdrożyć, a następnie wybierz pozycję **wdrażanie usługi z modelu zarejestrowany** z menu kontekstowego.

1. Na paletę poleceń Wybierz docelowego obliczeń, który chcesz wdrożyć. 

1. Na palecie poleceń w polu wprowadź nazwę dla tej usługi.  

1. Na paletę poleceń Wybierz klawisz Enter na klawiaturze, aby wyszukać i wybierz plik skryptu.

1. Na paletę poleceń Wybierz klawisz Enter na klawiaturze, aby wyszukać i wybierz plik zależności conda.

1. Po zakończeniu konfigurowania właściwości usługi, w prawym dolnym rogu okna wybierz **przesyłania** do wdrożenia. W pliku właściwości usługi można określić plik lokalny platformy docker lub pliku schema.json.

Teraz jest wdrożona usługa sieci web.

Poniżej przedstawiono przykładowy sposób wdrażania usługi sieci web:

[![Wdrażanie usługi sieci web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Używanie skrótów klawiaturowych

Dostęp do funkcji usługi Azure Machine Learning w programie Visual Studio Code, można użyć klawiatury. Najważniejsze skrótu klawiaturowego wiedzieć, jest Ctrl + Shift + P, który wyświetla paletę poleceń. Z palety poleceń masz dostęp do wszystkich funkcji programu Visual Studio Code, w tym skrótów klawiaturowych dla typowych operacji.

[![Skróty klawiaturowe dla usługi Azure Machine Learning dla programu Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać wskazówki dotyczące sposobu jego trenowanie za pomocą usługi Azure Machine Learning poza programem Visual Studio Code, zobacz [samouczka: Szkolenie modeli za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md).
* Aby uzyskać wskazówki dotyczące sposobu edytowania, uruchamiania i debugowania kodu lokalnie, zobacz [samouczek języka Python dla Witaj, świecie](https://code.visualstudio.com/docs/python/python-tutorial).
