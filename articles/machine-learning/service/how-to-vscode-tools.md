---
title: Używanie Visual Studio Code do uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zainstalować Azure Machine Learning Visual Studio Code i utworzyć eksperyment w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: a93c71a97cdb1f6296919a248cf7ef545f7b307f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269236"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Wprowadzenie do Azure Machine Learning dla Visual Studio Code

W tym artykule dowiesz się, jak używać rozszerzenia **Visual Studio Code Azure Machine Learning** do uczenia i wdrażania modeli uczenia maszynowego.

[Usługa Azure Machine Learning](overview-what-is-azure-ml.md) usprawnia kompilowanie, uczenie i wdrażanie modeli uczenia maszynowego.
+ W przypadku szkoleń zapewnia ona obsługę lokalnego i zdalnego uruchamiania eksperymentów. Dla każdego eksperymentu można rejestrować niestandardowe metryki wielu przebiegów, aby dostrajać parametry
+ Możesz również użyć usługi Azure Machine Learning, aby łatwo wdrożyć modele uczenia maszynowego na potrzeby testowania i produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

+ Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

+ Zainstaluj [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), lekki Edytor kodu, który działa w systemach Windows, Mac i Linux.

+ [Zainstaluj środowisko Python 3,5 lub nowsze](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Po zainstalowaniu rozszerzenia Azure Machine Learning są automatycznie instalowane dwa rozszerzenia. Są to [rozszerzenia konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) i [rozszerzenie Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). Aby uzyskać więcej informacji na temat używania rozszerzenia Python do edytowania, uruchamiania i debugowania kodu w języku Python, zobacz [samouczek języka Python Hello — World](https://code.visualstudio.com/docs/Python/Python-tutorial).

Aby zainstalować rozszerzenie Azure Machine Learning:

1. Otwórz program Visual Studio Code.

1. Przejdź do karty rozszerzenia i wyszukaj ciąg "Azure Machine Learning".

1. Na karcie rozszerzenie wybierz pozycję **Zainstaluj**.

1. Zostanie otwarta karta powitalna rozszerzenia w Visual Studio Code, a na pasku działania zostanie dodany symbol platformy Azure (na czerwono na poniższym zrzucie ekranu).

   ![Ikona platformy Azure na pasku działania Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. W oknie dialogowym wybierz pozycję **Zaloguj** i postępuj zgodnie z monitami, aby uwierzytelnić się na platformie Azure.

   Rozszerzenie konta platformy Azure, które zostało zainstalowane wraz z Azure Machine Learning dla rozszerzenia Visual Studio Code, ułatwia uwierzytelnianie przy użyciu konta platformy Azure. Aby zapoznać się z listą poleceń, zobacz stronę [rozszerzenia konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!TIP]
> Możesz również pobrać Instalatora rozszerzenia bezpośrednio z [Azure Machine Learning dla rozszerzenia Visual Studio Code (wersja zapoznawcza)](https://aka.ms/vscodetoolsforai).

## <a name="quickstart-with-azure-machine-learning"></a>Szybki Start z Azure Machine Learning
Istnieje wiele sposobów uruchamiania skryptów szkoleniowych przy użyciu usługi Azure Machine Learning. Jeśli dopiero zaczynasz pracę, najpierw zapoznaj się z tematem, jak szybko przesłać skrypt szkoleniowy do uruchomienia na platformie Azure.

Jeśli masz już doświadczenie z Azure Machine Learning pojęciami i chcesz uzyskać więcej szczegółów na temat sposobu zarządzania nimi i używania ich z rozszerzeniem, zobacz sekcję Azure Machine Learning, aby uzyskać szczegółowe informacje na temat [vs Code](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) poniżej.

## <a name="run-an-existing-python-training-script-in-azure"></a>Uruchamianie istniejącego skryptu szkoleniowego języka Python na platformie Azure
Jeśli masz istniejący skrypt szkoleniowy, rozszerzenie Azure Machine Learning dla VS Code nie tylko zapewnia doskonałe możliwości edytowania, debugowania i zarządzania źródła, ale ułatwia uruchamianie i przechowywanie metryk dla skryptu na platformie Azure.

Zacznijmy. Możesz użyć własnego skryptu szkoleniowego, jeśli jest gotowy, lub sklonować przykładowe [repozytorium programu vscode-Tools-for-AI](https://github.com/microsoft/vscode-tools-for-ai). Jest to publiczne repozytorium do zgłaszania problemów dotyczących tego rozszerzenia. Zawiera on również mały **mnist ręcznie** przykładowy folder, który będzie używany w tym przykładzie.

1. Otwórz folder **mnist ręcznie** w vs Code.

1. Utwórz nowe środowisko języka Python przy użyciu ulubionego pakietu środowiska wirtualnego lub Anaconda i zainstaluj pakiety TensorFlow i numpy.

1. Wybierz nowe środowisko utworzone jako interpreter języka Python w lewym dolnym rogu paska stanu VS Code.

1. Otwórz program **Train.py** i uruchom go, otwierając debuger i naciskając przycisk Run (lub po prostu naciskając klawisz F5).

   [![Uruchom szkolenie MNIST ręcznie](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

Jeśli wszystko jest poprawnie zainstalowane, skrypt zostanie uruchomiony i utworzysz model TensorFlow w folderze Outputs.

[![Pokaż model TensorFlow](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

Teraz, gdy wiesz, że skrypt działa prawidłowo, Uruchommy go na platformie Azure!

Można to łatwo zrobić bez dodatkowych modyfikacji **Train.py**. Jednak za pomocą zaledwie kilku prostych zmian można korzystać z Azure Machine Learning, aby automatycznie śledzić ważne metryki dotyczące poszczególnych przebiegów szkoleniowych.

### <a name="make-azure-aware-of-your-run-metrics"></a>Weź pod uwagę platformę Azure
Aby zmodyfikować projekt w taki sposób, aby platforma Azure mogła mieć świadomość ważnych informacji w swoich uruchomieniach:

1. Utwórz plik o nazwie **amlrun.py** w tym samym folderze co **Train.py**

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. Zaimportuj plik amlrun w **Train.py**

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. Inicjowanie obiektu Run w **Train.py**

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Rejestruj metryki na platformie Azure za pomocą funkcji run. log ():

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Uruchamianie skryptu na platformie Azure
To wszystko! Teraz po prostu Użyj rozszerzenia, aby uruchomić skrypt w chmurze! Należy zauważyć, że następujące instruktażowe wideo pobierają postanowienie dotyczące kompresowania czasu potrzebnego do utworzenia nowego obszaru roboczego usługi Azure ML i obliczeń, jak również czasu potrzebnego do uruchomienia skryptu szkoleniowego.

   [![Rozpocznij eksperyment dotyczący usługi Azure ML](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

Po kliknięciu przycisku Uruchom eksperyment należy odpowiedzieć na pytania w następujący sposób:

1. Wybieranie Twojej subskrypcji platformy Azure
1. Wybierz, aby utworzyć *Nowy* obszar roboczy usługi Azure ml
1. Wybierz zestaw wstępnie skonfigurowanych szablonów, aby zainicjować środowisko Python dla przebiegów. Szablony udostępniają punkt początkowy oraz ustawienia dołączania dla:
    1. **PyTorch**, **TensorFlow**lub **Scikit — uczenie się**
    1. Szkolenie dotyczące **pojedynczych** lub **rozproszonych** obliczeń
    1. **Ogólne** dla środowisk niestandardowych
1. Upewnij się, że lista elementów PIP i Conda została zakończona dla skryptu przez dodanie pakietów, które nie są uwzględnione w szablonie.
1. Przejrzyj domyślne nazwy i specyfikacje dla przebiegu eksperymentu i kliknij link **Prześlij eksperyment** w pliku JSON. Plik JSON nie zostanie zapisany, ponieważ umożliwia przejrzenie lub zmianę ustawień eksperymentu przed przesłaniem.
1. Wykorzystaj i Wyłącz, gdy rozszerzenie ustawi wszystko za Ciebie i uruchomi swój skrypt.

    [![Uczenie w chmurze](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

W ciągu kilku sekund użytkownik zostanie powiadomiony o tym, że eksperyment został przesłany na platformę Azure, w którym można wyświetlić postęp w Azure Portal przez kliknięcie linku **Wyświetl przebieg eksperymentu** w powiadomieniu o vs Code lub wewnątrz vs Code po naciśnięciu przycisku Odśwież na karcie Azure.

W tej chwili wyświetlanie metryk przebiegu jest obsługiwane tylko w Azure Portal. Link **Obejrzyj uruchomienie eksperymentu** wymieniony powyżej spowoduje przejście do przebiegu, w którym zostaną wyświetlone zarejestrowane metryki.
[![Testowanie przebiegu w portalu](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Szczegółowe Azure Machine Learning z VS Code

W powyższym instruktażu wprowadziliśmy eksperyment, postępując zgodnie z najłatwiej ścieżką. Ponieważ można zauważyć, że rozszerzenie minimalizuje czynności, które należy wykonać, aby uruchomić eksperyment. W tej sekcji omówiono sposób zarządzania wszystkimi Azure Machine Learning pojęciami indywidualnymi, zapewniając maksymalną kontrolę.

Przed rozpoczęciem szkoleń i wdrażania modeli uczenia maszynowego w Visual Studio Code należy utworzyć [obszar roboczy Azure Machine Learning](concept-workspace.md) w chmurze. Ten obszar roboczy będzie zawierać modele i zasoby.

### <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlony pasek boczny Azure Machine Learning.

    [![Tworzenie obszaru roboczego](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. Kliknij prawym przyciskiem myszy subskrypcję platformy Azure i wybierz pozycję **Utwórz obszar roboczy**. Domyślnie jest generowana nazwa zawierająca datę i godzinę utworzenia. Zmień nazwę na **TeamWorkspace** i naciśnij klawisz ENTER.

1. Wybierz grupę zasobów z listy, Jeśli wiesz, że chcesz wybrać lub utworzyć nową. W przypadku tworzenia nowego elementu wybierz lokalizację, która znajduje się najbliżej lokalizacji planowanej do wdrożenia modelu. W takim przypadku wybieramy **zachodnie stany USA 2**.

1. Po naciśnięciu klawisza ENTER Azure Machine Learning otrzyma żądanie utworzenia Twojego obszaru roboczego. Użytkownik zostanie powiadomiony o procesie w obszarze powiadomień Visual Studio Code.

1. Rozwiń węzeł subskrypcja, aby znaleźć nowo utworzony obszar roboczy.

### <a name="create-an-experiment"></a>Tworzenie eksperymentu
Co najmniej jedno eksperymenty można utworzyć w obszarze roboczym, aby śledzić i analizować poszczególne przebiegi szkoleniowe modelu. Uruchomienia można wykonać w chmurze platformy Azure lub na komputerze lokalnym.

1. Rozwiń obszar roboczy **TeamWorkspace** . Kliknij prawym przyciskiem myszy węzeł **eksperymenty** , a następnie wybierz polecenie **Utwórz eksperyment** z menu kontekstowego.

1. W wierszu polecenia wprowadź nazwę eksperymentu. W przykładowych zrzutach ekranu eksperyment ma nazwę **mnist ręcznie**.

1. Wybierz klawisz ENTER, aby utworzyć nowy eksperyment. Nowy eksperyment zostanie wyświetlony w drzewie jako element podrzędny węzła **eksperymenty** .

1. W obszarze roboczym można kliknąć prawym przyciskiem myszy eksperyment, aby ustawić go jako **aktywny** eksperyment. **Aktywne** eksperymenty są połączone z tym folderem w chmurze do folderu, który jest obecnie otwarty w Visual Studio Code. Ten folder powinien zawierać lokalne skrypty języka Python. Po ustawieniu aktywnego eksperymentu kluczowe metryki dla wszystkich przebiegów szkoleniowych będą przechowywane w ramach eksperymentu, niezależnie od tego, gdzie są wykonywane.

    [![Tworzenie eksperymentu](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>Tworzenie i zarządzanie nimi obliczeniowych elementów docelowych

Za pomocą Azure Machine Learning dla Visual Studio Code można przygotować swoje dane, przeszkolić modele i wdrożyć je lokalnie i na zdalnych celach obliczeniowych.

Rozszerzenie obsługuje kilka zdalnych obiektów docelowych obliczeń dla Azure Machine Learning. Aby uzyskać więcej informacji, zapoznaj się z pełną listą obsługiwanych [elementów docelowych obliczeń dla Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Utwórz cele obliczeniowe dla Azure Machine Learning w Visual Studio Code

Aby utworzyć obiekt docelowy obliczeń:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure. Zostanie wyświetlony pasek boczny Azure Machine Learning.

1. W widoku drzewa rozwiń swoją subskrypcję platformy Azure i obszar roboczy Azure Machine Learning.

1. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **obliczenia** węzeł i wybierz polecenie **Tworzenie obliczeń**.

1. Wybierz typ docelowy obliczeń z listy.

1. W wierszu polecenia Wybierz rozmiar maszyny wirtualnej. Można filtrować obliczenia z tekstem, na przykład "GPU".

1. W wierszu polecenia wybierz nazwę obiektu docelowego obliczeń.

1. Po wprowadzeniu nazwy, obliczenia zostaną utworzone przy użyciu parametrów domyślnych. Aby zmienić parametry, kliknij prawym przyciskiem myszy nowe obliczenie i wybierz polecenie **Edytuj obliczenia**.

1. W wyświetlonym formacie JSON wprowadź żądane zmiany, a następnie kliknij pozycję "Zapisz i Kontynuuj" CodeLens (za pomocą klawiatury, naciśnij **klawisze Ctrl-Shift-p** , aby wywołać paletę poleceń i uruchomić **maszynę Azure ml: Zapisz i Kontynuuj** — polecenie)

Oto przykład sposobu tworzenia i edytowania Azure Machine Learning COMPUTE (AMLCompute):

[![Tworzenie AML obliczeń w Visual Studio Code](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Plik konfiguracji uruchamiania

Aby uruchomić Azure Machine Learning eksperymentu w obliczeniach, należy odpowiednio skonfigurować obliczenia. Plik konfiguracji przebiegu jest mechanizmem, za pomocą którego jest określone to środowisko.

Oto przykład sposobu tworzenia konfiguracji uruchamiania dla AmlCompute utworzonych powyżej.

[![Tworzenie konfiguracji uruchamiania dla obliczeń](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

Aby można było uruchamiać eksperymenty z platformą Azure ML na komputerze lokalnym, jest nadal wymagany plik konfiguracji uruchomieniowej. Podczas tworzenia konfiguracji lokalnego uruchomienia środowisko Python zostanie domyślnie użyte w ścieżce do interpretera, który został ustawiony w VS Code.

### <a name="train-and-tune-models"></a>Szkolenie i dostrajanie modeli

Za pomocą rozszerzenia usługi Azure ML dla VS Code istnieje wiele sposobów uruchamiania skryptu szkoleniowego w eksperymentie.

1. Kliknij prawym przyciskiem myszy skrypt szkoleniowy **i wybierz pozycję Azure ml: Uruchom jako eksperyment na platformie Azure**
1. Kliknij ikonę Uruchom eksperyment na pasku narzędzi.
1. Kliknij prawym przyciskiem myszy węzeł Konfiguracja uruchomienia.
1. Użyj palety poleceń vs Code, aby wykonać **platformę Azure ml: Uruchom eksperyment**

Aby uruchomić eksperyment Azure Machine Learning:

1. Na pasku działania Visual Studio Code wybierz ikonę platformy Azure.

1. W widoku drzewa rozwiń swoją subskrypcję platformy Azure i obszar roboczy Azure Machine Learning.

1. W obszarze węzła obszaru roboczego rozwiń węzeł **eksperymenty** , a następnie kliknij prawym przyciskiem myszy eksperyment, który chcesz uruchomić.

1. Wybierz **uruchamiania eksperymentu**.

1. Wybierz nazwę pliku języka Python, który chcesz uruchomić, aby szkolić model i naciśnij klawisz ENTER w celu przesłania przebiegu. Uwaga: Wybrany plik musi znajdować się w folderze, który jest obecnie otwarty w VS Code.

1. Po przesłaniu przebiegu zostanie wyświetlony **węzeł uruchomieniowy** poniżej wybranego eksperymentu. Ten węzeł służy do monitorowania stanu przebiegów. Uwaga: Może być konieczne okresowe odświeżanie okna w celu wyświetlenia najnowszego stanu.

Oto przykład sposobu uruchamiania eksperymentu w przypadku obliczeń utworzonych wcześniej:

[![Uruchamianie eksperymentu lokalnie](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Wdrażanie modeli i zarządzania nimi
W Azure Machine Learning można wdrożyć modele uczenia maszynowego i zarządzać nimi w chmurze i na krawędzi.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Zarejestruj model, aby Azure Machine Learning z Visual Studio Code

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

[![Rejestrowanie modelu w usłudze AML](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Wdróż usługę z Visual Studio Code

W Visual Studio Code można wdrożyć usługę sieci Web w programie:
+ Azure Container Instances (ACI) do testowania.
+ Usługa Azure Kubernetes Service (AKS) dla środowiska produkcyjnego.

Nie musisz tworzyć kontenera ACI, aby przetestować z wyprzedzeniem, ponieważ kontenery ACI są tworzone w razie potrzeby. Należy jednak skonfigurować klastry AKS z wyprzedzeniem. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

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

[![Wdrażanie usługi sieci Web](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Eksperymentowanie z dodatkowymi funkcjami

Korzystając z palety poleceń, można uzyskać dostęp do wielu funkcji Azure Machine Learning w Visual Studio Code. Aby wywołać paletę poleceń, naciśnij klawisze Ctrl + Shift + P. W tym miejscu możesz wyszukać dodatkowe funkcje usługi Azure ML rozszerzenia.

[![Skróty klawiaturowe dla Azure Machine Learning Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Następne kroki

* Aby poznać wskazówki dotyczące uczenia się z Azure Machine Learning poza Visual Studio Code, zobacz [samouczek: Uczenie modeli przy](tutorial-train-models-with-aml.md)użyciu Azure Machine Learning.
* Aby zapoznać się z przewodnikiem dotyczącym sposobu edytowania, uruchamiania i debugowania kodu lokalnie, zobacz [samouczek Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).
