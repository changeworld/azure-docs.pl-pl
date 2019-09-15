---
title: Używanie Visual Studio Code do uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zainstalować Azure Machine Learning Visual Studio Code i utworzyć prosty eksperyment w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ronglu
author: ronglums
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 0507080a390a4bb9f981b0d911961337e9b2c62a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997177"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Wprowadzenie do Azure Machine Learning dla Visual Studio Code

W tym artykule dowiesz się, jak za pomocą rozszerzenia Azure Machine Learning Visual Studio Code do uczenia i wdrażania modeli uczenia maszynowego i uczenia głębokiego.

[Azure Machine Learning](overview-what-is-azure-ml.md) zapewnia obsługę eksperymentów wykonywanych lokalnie i na zdalnych celach obliczeniowych. Dla każdego eksperymentu można śledzenie bieżącego wielu uruchomień na potrzeby często iteracyjne spróbuj różnych technik, hiperparametrów i inne. Usługa Azure Machine Learning służy do śledzenia niestandardowe metryki i eksperymentować przebiegów, umożliwiając odtwarzaniem do nauki o danych i sprawdzalność.

Możesz również wdrożyć te modele dla potrzeb testowania i produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

+ Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

+ Należy zainstalować Visual Studio Code. Visual Studio Code to lekki, ale zaawansowany edytor kodu źródłowego, który działa na pulpicie. Zawiera wbudowaną obsługę języka Python i innych języków programowania. Jeśli jeszcze nie zainstalowano Visual Studio Code, Dowiedz się, [jak to zrobić](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Zainstaluj środowisko Python 3,5 lub nowsze](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Zainstaluj rozszerzenie dla Azure Machine Learning dla Visual Studio Code

Po zainstalowaniu rozszerzenia Azure Machine Learning są automatycznie instalowane dwa rozszerzenia (Jeśli masz dostęp do Internetu). Są to [rozszerzenia konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) i [rozszerzenie Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Aby można było korzystać z Azure Machine Learning, należy włączyć Visual Studio Code w środowisku deweloperskim zintegrowanym z językiem Python (IDE). Do korzystania z języka [Python w Visual Studio Code](https://code.visualstudio.com/docs/languages/python)jest potrzebne rozszerzenie Microsoft Python. To rozszerzenie jest automatycznie instalowane z rozszerzeniem Azure Machine Learning. Rozszerzenie zapewnia Visual Studio Code doskonałego środowiska IDE i działa w dowolnym systemie operacyjnym z różnymi interpreterami języka Python. Rozszerzenie Microsoft Python używa wszystkich możliwości Visual Studio Code w celu zapewnienia autouzupełniania, IntelliSense, Zaznaczanie błędów, debugowania i testowania jednostkowego. Rozszerzenie pozwala także łatwo przełączać się między środowiskami Python, w tym środowiskami wirtualnymi i Conda. Aby uzyskać więcej informacji na temat edytowania, uruchamiania i debugowania kodu w języku Python, zobacz [samouczek Python Hello-World](https://code.visualstudio.com/docs/python/python-tutorial).

Aby zainstalować rozszerzenie Azure Machine Learning:

1. Otwórz program Visual Studio Code.

1. W przeglądarce internetowej przejdź do [Azure Machine Learning dla rozszerzenia Visual Studio Code (wersja zapoznawcza)](https://aka.ms/vscodetoolsforai).

1. Na tej stronie sieci Web wybierz pozycję **Zainstaluj**. 

1. Na karcie rozszerzenie wybierz pozycję **Zainstaluj**.

1. Zostanie otwarta karta powitalna rozszerzenia w Visual Studio Code, a na pasku działania zostanie dodany symbol platformy Azure (przedstawiony na czerwono na poniższym zrzucie ekranu).

   ![Ikona platformy Azure na pasku działania Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. W oknie dialogowym wybierz pozycję **Zaloguj** i postępuj zgodnie z monitami, aby uwierzytelnić się na platformie Azure. 
   
   Rozszerzenie konta platformy Azure, które zostało zainstalowane wraz z Azure Machine Learning dla rozszerzenia Visual Studio Code, ułatwia uwierzytelnianie przy użyciu konta platformy Azure. Aby zapoznać się z listą poleceń, zobacz stronę [rozszerzenia konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Sprawdź [rozszerzenie rozszerzenia intellicode dla Visual Studio Code (wersja zapoznawcza)](https://go.microsoft.com/fwlink/?linkid=2006060). Rozszerzenia intellicode zawiera zestaw funkcji z możliwością wbudowania w języku Python, na przykład wnioskowanie najbardziej odpowiednich Autouzupełniania na podstawie bieżącego kontekstu kodu.

## <a name="install-the-azure-machine-learning-sdk"></a>Instalowanie zestawu SDK Azure Machine Learning

1. Upewnij się, że język Python 3,5 lub nowszy jest zainstalowany i jest rozpoznawany przez Visual Studio Code. Jeśli ją teraz zainstalujesz, uruchom ponownie Visual Studio Code i [Wybierz interpreter języka Python](https://code.visualstudio.com/docs/python/python-tutorial).

1. W oknie zintegrowanych terminali Określ interpreter języka Python, który ma być używany. Lub wybierz klawisz ENTER, aby użyć domyślnego interpretera języka Python.

   ![Wybierz interpreter](./media/vscode-tools-for-ai/python.png)

1. W prawym dolnym rogu okna pojawi się powiadomienie wskazujące, że [zestaw Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) jest instalowany automatycznie. Nowo utworzone środowisko Python jest lokalne i prywatne i ma Visual Studio Code wymagania wstępne dotyczące pracy z Azure Machine Learning.

   ![Instalowanie zestawu SDK Azure Machine Learning dla języka Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Rozpocznij pracę z usługą Azure Machine Learning

Przed rozpoczęciem szkoleń i wdrażania modeli uczenia maszynowego w Visual Studio Code należy utworzyć [obszar roboczy Azure Machine Learning](concept-workspace.md) w chmurze. Ten obszar roboczy będzie zawierać modele i zasoby. 

Aby utworzyć obszar roboczy i dodać pierwszy eksperyment:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlony pasek boczny Azure Machine Learning.

   [![Tworzenie obszaru roboczego](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Kliknij prawym przyciskiem myszy subskrypcję platformy Azure i wybierz pozycję **Utwórz obszar roboczy**. Zostanie wyświetlona lista. W przykładowym obrazie animowanym nazwa subskrypcji to **bezpłatna wersja próbna**, a obszar roboczy to **TeamWorkspace**. 

1. Wybierz grupę zasobów z listy lub Utwórz nową przy użyciu kreatora w palecie poleceń.

1. W polu wpisz unikatową i niepowtarzalną nazwę nowego obszaru roboczego. W przykładowym obrazie obszar roboczy ma nazwę **TeamWorkspace**.

1. Wybierz klawisz ENTER, aby utworzyć nowy obszar roboczy. Jest on wyświetlany w drzewie, poniżej nazwy subskrypcji.

1. Kliknij prawym przyciskiem myszy węzeł eksperymenty, a następnie wybierz polecenie **Utwórz eksperyment** z menu kontekstowego.  Eksperymenty śledzą przebiegów wykonywanych przy użyciu Azure Machine Learning.

1. W polu Wprowadź nazwę dla eksperymentu. W przykładowych zrzutach ekranu eksperyment ma nazwę **mnist ręcznie**.
 
1. Wybierz klawisz ENTER, aby utworzyć nowy eksperyment. Eksperyment zostanie wyświetlony w drzewie poniżej nazwy obszaru roboczego.

1. W obszarze roboczym można kliknąć prawym przyciskiem myszy eksperyment, aby ustawić go jako **aktywny** eksperyment. **Aktywny** eksperyment jest bieżącym eksperymentem. Folder otwarty w Visual Studio Code zostanie połączony z tym eksperymentem w chmurze. Ten folder powinien zawierać lokalne skrypty języka Python.

Teraz Twoje kluczowe metryki będą przechowywane w historii eksperymentu. Podobnie pouczenie modeli zostanie automatycznie przekazane do Azure Machine Learning i zapisane wraz z metrykami i dziennikami eksperymentów. 

[![Dołączanie folderu w Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Tworzenie i zarządzanie nimi obliczeniowych elementów docelowych

Za pomocą Azure Machine Learning dla Visual Studio Code można przygotować swoje dane, przeszkolić modele i wdrożyć je lokalnie i na zdalnych celach obliczeniowych.

Rozszerzenie obsługuje kilka zdalnych obiektów docelowych obliczeń dla Azure Machine Learning. Aby uzyskać więcej informacji, zapoznaj się z pełną listą obsługiwanych [elementów docelowych obliczeń dla Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Utwórz cele obliczeniowe dla Azure Machine Learning w Visual Studio Code

Aby utworzyć obiekt docelowy obliczeń:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlony pasek boczny Azure Machine Learning.

2. W widoku drzewa rozwiń swoją subskrypcję platformy Azure i obszar roboczy Azure Machine Learning. W poniższym przykładowym obrazie nazwa subskrypcji to **bezpłatna wersja próbna**, a obszar roboczy to **TeamWorkspace**. 

3. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **obliczenia** węzeł i wybierz polecenie **Tworzenie obliczeń**.

4. Wybierz typ docelowy obliczeń z listy. 

5. W palecie poleceń wybierz rozmiar maszyny wirtualnej.

6. W palecie poleceń w polu Wprowadź nazwę obiektu docelowego obliczeń. 

7. W pliku konfiguracji JSON, który zostanie otwarty na nowej karcie, określ wszelkie zaawansowane właściwości. Można określić właściwości, takie jak Maksymalna liczba węzłów.

8. Po zakończeniu konfigurowania obiektu docelowego obliczeń w prawym dolnym rogu okna wybierz pozycję **Prześlij**.

Oto przykład sposobu tworzenia Azure Machine Learning COMPUTE (AMLCompute):

[![Tworzenie AML obliczeń w Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Plik konfiguracji uruchamiania

Visual Studio Code rozszerzenie automatycznie tworzy lokalne miejsce docelowe obliczeń i uruchamia konfiguracje dla środowisk lokalnych i platformy Docker na komputerze lokalnym. Pliki konfiguracji uruchamiania można znaleźć w obszarze skojarzony węzeł docelowy obliczeń. 

## <a name="train-and-tune-models"></a>Szkolenie i dostrajanie modeli

Użyj Azure Machine Learning dla Visual Studio Code (wersja zapoznawcza), aby szybko wykonać iterację kodu, przeszukiwać i debugować, a następnie używać rozwiązania do kontroli kodu źródłowego. 

Aby uruchomić eksperyment lokalnie przy użyciu Azure Machine Learning:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlony pasek boczny Azure Machine Learning.

1. W widoku drzewa rozwiń swoją subskrypcję platformy Azure i obszar roboczy Azure Machine Learning. 

1. W obszarze węzła obszaru roboczego rozwiń węzeł **obliczenia** i kliknij prawym przyciskiem myszy **konfigurację** obliczeń, której chcesz użyć. 

1. Wybierz **uruchamiania eksperymentu**.

1. W Eksploratorze plików wybierz skrypt, który chcesz uruchomić. 

1. Wybierz pozycję **Wyświetl eksperyment uruchamiaj** , aby wyświetlić zintegrowany Portal Azure Machine Learning, aby monitorować przebiegi i przeglądać przeszkolone modele.

Oto przykład sposobu uruchamiania eksperymentu lokalnie:

[![Uruchamianie eksperymentu lokalnie](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Użyj zdalnych obliczeń dla eksperymentów w Visual Studio Code

Aby użyć zdalnego celu obliczeń do szkolenia, należy utworzyć plik konfiguracji uruchomieniowej. Ten plik nakazuje usługi Azure Machine Learning nie tylko miejsca uruchomić eksperyment, ale również, jak przygotować środowisko.

#### <a name="the-conda-dependencies-file"></a>Pliku zależności conda

Domyślnie tworzone jest nowe środowisko Conda i zarządzane są zależności instalacji. Należy jednak określić zależności i ich wersje w pliku *aml_config/conda_dependencies. yml* . 

Poniższy fragment kodu z domyślnej *aml_config/conda_dependencies. yml* określa `tensorflow=1.12.0`. Jeśli nie określisz wersji zależności, zostanie użyta Najnowsza wersja. Możesz dodać dodatkowe zależności w pliku konfiguracji.

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

Aby uruchomić eksperyment z Azure Machine Learning na zdalnym miejscu docelowym obliczeń:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlony pasek boczny Azure Machine Learning.

1. W widoku drzewa rozwiń swoją subskrypcję platformy Azure i obszar roboczy Azure Machine Learning. 

1. W oknie edytora kliknij prawym przyciskiem myszy skrypt w języku Python, a **następnie wybierz pozycję AML: Uruchom jako eksperyment na platformie**Azure. 

1. W palecie poleceń wybierz element docelowy obliczeń. 

1. W palecie poleceń w polu Wprowadź nazwę konfiguracji uruchomienia. 

1. Edytuj plik *conda_dependencies. yml* , aby określić zależności środowiska uruchomieniowego eksperymentu. Następnie w prawym dolnym rogu okna wybierz pozycję **Prześlij**. 

1. Wybierz pozycję **Wyświetl eksperyment uruchamiaj** , aby wyświetlić zintegrowany Portal Azure Machine Learning, aby monitorować przebiegi i przeglądać przeszkolone modele.

Oto przykład sposobu uruchamiania eksperymentu na zdalnym miejscu docelowym obliczeń:

[![Uruchamianie eksperymentu na zdalnym miejscu docelowym](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Wdrażanie modeli i zarządzania nimi
W Azure Machine Learning można wdrożyć modele uczenia maszynowego i zarządzać nimi w chmurze i na krawędzi. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Zarejestruj model, aby Azure Machine Learning z Visual Studio Code

Teraz, gdy masz już szkolony model, możesz zarejestrować go w obszarze roboczym. Można śledzić i wdrażać zarejestrowane modele.

Aby zarejestrować model:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlony pasek boczny Azure Machine Learning.

1. W widoku drzewa rozwiń swoją subskrypcję platformy Azure i obszar roboczy Azure Machine Learning.

1. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **modeli** i wybierz polecenie **zarejestrować Model**.

1. W palecie poleceń w polu Wprowadź nazwę modelu. 

1. Z listy wybierz, czy chcesz przekazać **plik modelu** (dla pojedynczych modeli) czy **folder modelu** (dla modeli z wieloma plikami, takimi jak TensorFlow). 

1. Wybieranie pliku lub folderu.

1. Po zakończeniu konfigurowania właściwości modelu w prawym dolnym rogu okna wybierz pozycję **Prześlij**. 

Oto przykład sposobu zarejestrowania modelu w celu Azure Machine Learning:

[![Rejestrowanie modelu w usłudze AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Wdróż usługę z Visual Studio Code

W Visual Studio Code można wdrożyć usługę sieci Web w programie:
+ Azure Container Instances (ACI) do testowania.
+ Usługa Azure Kubernetes Service (AKS) dla środowiska produkcyjnego.

Nie musisz tworzyć kontenera ACI, aby przetestować z wyprzedzeniem, ponieważ kontenery ACI są tworzone na bieżąco. Należy jednak skonfigurować klastry AKS z wyprzedzeniem. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

Aby wdrożyć usługę sieci Web:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlony pasek boczny Azure Machine Learning.

1. W widoku drzewa rozwiń swoją subskrypcję platformy Azure i obszar roboczy Azure Machine Learning.

1. W obszarze węzła obszaru roboczego, należy rozwinąć **modeli** węzła.

1. Kliknij prawym przyciskiem myszy Model, który chcesz wdrożyć, a następnie wybierz polecenie **Wdróż usługę z zarejestrowanego modelu** z menu kontekstowego.

1. W palecie poleceń wybierz obiekt docelowy obliczeń, który ma zostać wdrożony. 

1. W palecie poleceń w polu Wprowadź nazwę dla tej usługi.  

1. W palecie poleceń wybierz klawisz Enter na klawiaturze, aby wyszukać i wybrać plik skryptu.

1. W palecie poleceń wybierz klawisz Enter na klawiaturze, aby wyszukać i wybrać plik zależności Conda.

1. Po zakończeniu konfigurowania właściwości usługi w prawym dolnym rogu okna wybierz pozycję **Prześlij** do wdrożenia. W pliku właściwości usługi można określić lokalny plik platformy Docker lub plik Schema. JSON.

Teraz jest wdrożona usługa sieci web.

Oto przykład sposobu wdrażania usługi sieci Web:

[![Wdrażanie usługi sieci Web](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Korzystanie ze skrótów klawiaturowych

Możesz użyć klawiatury, aby uzyskać dostęp do funkcji Azure Machine Learning w Visual Studio Code. Najważniejszym skrótem klawiaturowym do znajomości jest Ctrl + Shift + P, który wyświetla paletę poleceń. W palecie poleceń masz dostęp do wszystkich funkcji Visual Studio Code, w tym skrótów klawiaturowych dla najczęściej używanych operacji.

[![Skróty klawiaturowe dla Azure Machine Learning Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Następne kroki

* Aby poznać wskazówki dotyczące uczenia się z Azure Machine Learning poza Visual Studio Code, zobacz [samouczek: Uczenie modeli przy](tutorial-train-models-with-aml.md)użyciu Azure Machine Learning.
* Aby zapoznać się z przewodnikiem dotyczącym sposobu edytowania, uruchamiania i debugowania kodu lokalnie, zobacz [samouczek Python Hello-World](https://code.visualstudio.com/docs/python/python-tutorial).
