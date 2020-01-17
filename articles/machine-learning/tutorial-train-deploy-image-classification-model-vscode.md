---
title: 'Samouczek: uczenie i wdrażanie modelu TensorFlow klasyfikacji obrazów przy użyciu rozszerzenia Visual Studio Code Azure Machine Learning'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak nauczyć i wdrożyć model klasyfikacji obrazów przy użyciu TensorFlow i rozszerzenia Visual Studio Code Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 899681f2bb9c3ef2a0368015a58db30a843738f5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157556"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Uczenie i wdrażanie modelu TensorFlow klasyfikacji obrazów przy użyciu rozszerzenia Visual Studio Code Azure Machine Learning

Dowiedz się, jak szkolić model klasyfikacji obrazów, aby rozpoznawać numery ręczne przy użyciu TensorFlow i rozszerzenia Visual Studio Code Azure Machine Learning.

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Zrozumienie kodu
> * Tworzenie obszaru roboczego
> * Tworzenie eksperymentu
> * Konfigurowanie obiektów docelowych komputera
> * Uruchamianie pliku konfiguracji
> * Trenowanie modelu
> * Zarejestruj model
> * Wdrażanie modelu

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz takiego konta, zarejestruj się, aby wypróbować [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
- Zainstaluj [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), lekki Edytor kodu dla wielu platform.
- Azure Machine Learning Studio Visual Studio Code rozszerzenia. Instrukcje dotyczące instalacji znajdują się w [samouczku Azure Machine Learning instalatora Visual Studio Code rozszerzenia](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Zrozumienie kodu

Kod w tym samouczku używa TensorFlow do uczenia modelu uczenia maszynowego klasyfikacji obrazów, który określa kategorie cyfr odpisanych od 0-9. Robi to przez utworzenie sieci neuronowych, która przyjmuje wartości pikseli 28 px x 28 pikseli jako dane wejściowe i wyprowadza listę 10 prawdopodobieństw, jeden dla każdej z nich. Poniżej znajduje się przykład, jak wyglądają dane.  

![Cyfry MNIST ręcznie](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Pobierz kod dla tego samouczka, pobierając i rozpakowywanie [vs Code narzędzia dla REPOZYTORIUM AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) w dowolnym miejscu na komputerze.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Pierwszą czynnością, którą należy wykonać w celu skompilowania aplikacji w Azure Machine Learning jest utworzenie obszaru roboczego. Obszar roboczy zawiera zasoby służące do uczenia modeli oraz samych szkolonych modeli. Aby uzyskać więcej informacji, zobacz [co to jest obszar roboczy](./concept-workspace.md). 

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** , aby otworzyć widok Azure Machine Learning.
1. Kliknij prawym przyciskiem myszy subskrypcję platformy Azure i wybierz pozycję **Utwórz obszar roboczy**. 
    
    > [!div class="mx-imgBorder"]
    > ![Tworzenie obszaru roboczego](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Domyślnie jest generowana nazwa zawierająca datę i godzinę utworzenia. W palecie poleceń Zmień nazwę na "TeamWorkspace" i naciśnij klawisz **Enter**.
1. Wybierz pozycję **Utwórz nową grupę zasobów** w palecie poleceń. 
1. Wprowadź ciąg "TeamWorkspace-RG" w polu tekstowym paleta poleceń i naciśnij klawisz **Enter**. 
1. W palecie poleceń wybierz lokalizację obszaru roboczego. Zalecane jest wybranie lokalizacji znajdującej się najbliżej lokalizacji planowanej do wdrożenia modelu. W takim przypadku wybierz pozycję **zachodnie stany USA 2**.
1. Po wyświetleniu monitu o wybranie jednostki SKU obszaru roboczego wybierz pozycję **podstawowa** , aby utworzyć podstawowy obszar roboczy. Aby uzyskać więcej informacji na temat różnych ofert obszaru roboczego, zobacz [Azure Machine Learning Omówienie](./overview-what-is-azure-ml.md#sku).

W tym momencie żądanie do platformy Azure jest tworzone w celu utworzenia nowego obszaru roboczego na koncie. Po kilku minutach nowy obszar roboczy zostanie wyświetlony w węźle subskrypcja. 

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

Co najmniej jedno eksperymenty można utworzyć w obszarze roboczym, aby śledzić i analizować poszczególne przebiegi szkoleniowe modelu. Uruchomienia można wykonać w chmurze platformy Azure lub na komputerze lokalnym.

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning.
1. Rozwiń węzeł subskrypcji.
1. Rozwiń węzeł **TeamWorkspace** . 
1. Kliknij prawym przyciskiem myszy węzeł **eksperymenty** .
1. Wybierz pozycję **Utwórz eksperyment** z menu kontekstowego.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie eksperymentu](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. W wierszu polecenia palety poleceń Nazwij eksperyment "MNIST ręcznie" i naciśnij klawisz **Enter** , aby utworzyć nowy eksperyment. 

Podobnie jak obszary robocze, żądanie jest wysyłane do platformy Azure w celu utworzenia eksperymentu z podaną konfiguracją. Po kilku minutach nowy eksperyment zostanie wyświetlony w węźle *eksperymenty* w obszarze roboczym. 

## <a name="configure-compute-targets"></a>Skonfiguruj cele obliczeń

Obiekt docelowy obliczeń jest zasobem obliczeniowym lub środowiskiem, w którym uruchamiane są skrypty i wdrażają przeszkolone modele. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą programu Azure Machine Learning COMPUTE targets](./concept-compute-target.md).

Aby utworzyć obiekt docelowy obliczeń:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace** . 
1. W węźle obszaru roboczego kliknij prawym przyciskiem myszy **obliczenia** węzeł i wybierz polecenie **Tworzenie obliczeń**. 

    > [!div class="mx-imgBorder"]
    > ![utworzyć elementu docelowego obliczeń](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Wybierz pozycję **Azure Machine Learning COMPUTE (AmlCompute)** . Azure Machine Learning COMPUTE to infrastruktura obliczeniowa, która umożliwia użytkownikowi łatwe tworzenie obliczeń pojedynczego lub wielowęzłowego, które mogą być używane z innymi użytkownikami w obszarze roboczym.
1. Wybierz rozmiar maszyny wirtualnej. W wierszu polecenia wybierz pozycję **Standard_F2s_v2**. Rozmiar maszyny wirtualnej ma wpływ na ilość czasu potrzebną do uczenia modeli. Aby uzyskać więcej informacji na temat rozmiarów maszyny wirtualnej, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
1. W wierszu polecenia palety poleceń Nazwij obliczenia "TeamWkspc-com" i naciśnij klawisz **Enter** , aby utworzyć obliczenia.

Po kilku minutach nowy obiekt docelowy obliczeń zostanie wyświetlony w węźle *obliczenia* obszaru roboczego.

## <a name="create-a-run-configuration"></a>Utwórz konfigurację uruchamiania

Po przesłaniu szkolenia do elementu docelowego obliczeń należy również przesłać konfigurację wymaganą do uruchomienia zadania szkoleniowego. Na przykład skrypt zawierający kod szkoleniowy i zależności języka Python, które są niezbędne do jego uruchomienia.

Aby utworzyć konfigurację uruchamiania:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace** . 
1. W węźle obszar roboczy kliknij prawym przyciskiem myszy węzeł obliczeniowy **TeamWkspc-com** i wybierz polecenie **Utwórz konfigurację uruchamiania**.

    > [!div class="mx-imgBorder"]
    > ![utworzyć konfigurację uruchamiania](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. W wierszu polecenia palety poleceń Nazwij konfigurację uruchamiania "MNIST ręcznie-RC" i naciśnij klawisz **Enter** , aby utworzyć obliczenia.
1. Następnie wybierz pozycję **TensorFlow szkolenie z jednego węzła** jako typ zadania szkoleniowego.
1. Naciśnij klawisz **Enter** , aby przeglądać plik skryptu, który ma zostać uruchomiony w ramach obliczeń. W takim przypadku skrypt służący do uczenia modelu to plik `train.py` wewnątrz katalogu `vscode-tools-for-ai/mnist-vscode-docs-sample`.
1. Wprowadź następujące polecenie w wierszu paleta poleceń, aby określić wymagane pakiety.
    
    ```text
    pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
    ```
    
    Plik o nazwie `MNIST-rc.runconfig` pojawia się w VS Code z zawartością podobną do poniższej:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "python": {
                "userManagedDependencies": false,
                "condaDependencies": {
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                }
            },
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:0.2.4",
                "enabled": true,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                }
            }
        },
        "nodeCount": 1,
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Po spełnieniu konfiguracji, Otwórz paletę poleceń, wybierając pozycję **wyświetl > palecie poleceń**.
1. Wprowadź następujące polecenie w palecie poleceń, aby zapisać plik konfiguracji uruchomieniowej.

    ```text
    Azure ML: Save and Continue
    ```

Konfiguracja przebiegu `MNIST-rc` jest dodawana w węźle obliczeniowym *TeamWkspc-com* .

## <a name="train-the-model"></a>Trenowanie modelu

W trakcie procesu szkolenia model TensorFlow jest tworzony przez przetwarzanie danych szkoleniowych i wzorców uczenia osadzonych w nim dla każdej z odpowiednich cyfr, które są klasyfikowane. 

Aby uruchomić eksperyment Azure Machine Learning:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace > eksperymenty** . 
1. Kliknij prawym przyciskiem myszy eksperyment **mnist ręcznie** .
1. Wybierz **uruchamiania eksperymentu**.

    > [!div class="mx-imgBorder"]
    > ![uruchomić eksperyment](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. W palecie poleceń wybierz element docelowy obliczeń **TeamWkspc-com** .
1. Następnie wybierz konfigurację przebiegu **mnist ręcznie-RC** .
1. W tym momencie żądanie jest wysyłane do platformy Azure w celu uruchomienia eksperymentu w wybranym miejscu docelowym obliczeń w obszarze roboczym. Ten proces trwa kilka minut. W czasie wykonywania zadania szkolenia ma wpływ kilka czynników, takich jak typ obliczeń i rozmiar danych szkoleniowych. Aby śledzić postęp eksperymentu, kliknij prawym przyciskiem myszy bieżący węzeł przebiegu i wybierz polecenie **Wyświetl Uruchom w Azure Portal**.
1. Gdy zostanie wyświetlone okno dialogowe z prośbą o otwarcie zewnętrznej witryny sieci Web, wybierz pozycję **Otwórz**.

    > [!div class="mx-imgBorder"]
    > ![śledzenie postępu eksperymentu](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Gdy model jest gotowy do szkolenia, etykieta stanu obok węzła uruchamiania aktualizuje do "ukończone".

## <a name="register-the-model"></a>Rejestrowanie modelu

Teraz, gdy masz już szkolony model, możesz zarejestrować go w obszarze roboczym. 

Aby zarejestrować model:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning.
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace > eksperymenty > mnist ręcznie** .
1. Pobierz dane wyjściowe modelu wygenerowane na podstawie szkolenia modelu. Kliknij prawym przyciskiem myszy węzeł uruchamiania **1** i wybierz polecenie **Pobierz dane wyjściowe**. 

    > [!div class="mx-imgBorder"]
    > ![Pobierz dane wyjściowe modelu](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Wybierz katalog, do którego mają zostać zapisane pobrane dane wyjściowe. Domyślnie dane wyjściowe są umieszczane w katalogu aktualnie otwartym w Visual Studio Code.
1. Kliknij prawym przyciskiem myszy węzeł **modele** i wybierz polecenie **zarejestruj model**.

    > [!div class="mx-imgBorder"]
    > ![zarejestrować model](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. W palecie poleceń Nazwij model "MNIST ręcznie-TensorFlow-model" i naciśnij klawisz **Enter**.
1. Model TensorFlow składa się z kilku plików. Wybierz pozycję **folder modelu** jako format ścieżki modelu w palecie poleceń. 
1. Wybierz katalog `azureml_outputs/Run_1/outputs/Run_1/outputs/outputs/model`.

    Plik zawierający konfiguracje modelu jest wyświetlany w Visual Studio Code z podobną zawartością do poniższego:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Po zakończeniu konfiguracji Zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

Po kilku minutach model zostanie wyświetlony w węźle *modele* .

## <a name="deploy-the-model"></a>Wdrażanie modelu

W Visual Studio Code można wdrożyć model jako usługę sieci Web, aby:

+ Azure Container Instances (ACI).
+ Usługa Azure Kubernetes Service (AKS).

Nie musisz tworzyć kontenera ACI, aby przetestować z wyprzedzeniem, ponieważ kontenery ACI są tworzone w razie potrzeby. Należy jednak skonfigurować klastry AKS z wyprzedzeniem. Aby uzyskać więcej informacji na temat opcji wdrażania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md) .

Aby wdrożyć usługę sieci Web jako ACI:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning.
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **modele > TeamWorkspace** . 
1. Kliknij prawym przyciskiem myszy **model mnist ręcznie-TensorFlow** i wybierz polecenie **Wdróż usługę z zarejestrowanego modelu**.

    > [!div class="mx-imgBorder"]
    > ![Wdrażanie modelu](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. W palecie poleceń wybierz pozycję **Azure Container Instances**.
1. Nazwij usługę "mnist ręcznie-tensorflow-svc" i naciśnij klawisz **Enter** w palecie poleceń.
1. Wybierz skrypt do uruchomienia w kontenerze, naciskając klawisz **Enter** w palecie poleceń i przeglądając plik `score.py` w katalogu `mnist-vscode-docs-sample`.
1. Podaj zależności, które są konieczne do uruchomienia skryptu, naciskając klawisz **Enter** w palecie poleceń i przeglądając plik `env.yml` w katalogu `mnist-vscode-docs-sample`.

    Plik zawierający konfiguracje modelu jest wyświetlany w Visual Studio Code z podobną zawartością do poniższego:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```
1. Po zakończeniu konfiguracji Zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

W tym momencie żądanie jest wysyłane do platformy Azure w celu wdrożenia usługi sieci Web. Ten proces trwa kilka minut. Po wdrożeniu Nowa usługa zostanie wyświetlona w węźle *punkty końcowe* .

## <a name="next-steps"></a>Następne kroki

* Aby poznać wskazówki dotyczące uczenia się z Azure Machine Learning poza Visual Studio Code, zobacz [Samouczek: uczenie modeli przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).
* Aby zapoznać się z przewodnikiem dotyczącym sposobu edytowania, uruchamiania i debugowania kodu lokalnie, zobacz [samouczek Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).

