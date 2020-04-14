---
title: 'Samouczek: Trenuj i wdrażaj model przy użyciu rozszerzenia kodu programu Visual Studio'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak szkolić i wdrażać model klasyfikacji obrazów przy użyciu usługi TensorFlow i rozszerzenia kodu usługi Azure Machine Learning Visual Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: f793f8c4cb84f821c098cc5ce98e693d272e725f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272828"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Szkolenie i wdrażanie modelu tensorflow klasyfikacji obrazów przy użyciu rozszerzenia kodu usługi Azure Machine Learning Visual Studio

Dowiedz się, jak szkolić i wdrażać model klasyfikacji obrazów w celu rozpoznawania odręcznych numerów przy użyciu usługi TensorFlow i rozszerzenia kodu usługi Azure Machine Learning Visual Studio.

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Zrozumienie kodu
> * Tworzenie obszaru roboczego
> * Tworzenie eksperymentu
> * Konfigurowanie obiektów docelowych komputera
> * Uruchamianie pliku konfiguracyjnego
> * Szkolenie modelu
> * Zarejestruj model
> * Wdrażanie modelu

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli go nie masz, zarejestruj się, aby wypróbować [bezpłatną lub płatną wersję usługi Azure Machine Learning.](https://aka.ms/AMLFree)
- Zainstaluj [visual studio code](https://code.visualstudio.com/docs/setup/setup-overview), lekki, wieloplatformowy edytor kodu.
- Rozszerzenie kodu usługi Azure Machine Learning Studio Visual Studio. Instrukcje dotyczące instalacji można znaleźć w [samouczku rozszerzenia rozszerzenia usługi Azure Machine Learning Visual Studio Code](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Zrozumienie kodu

Kod dla tego samouczka używa TensorFlow do uczenia modelu uczenia maszynowego klasyfikacji obrazu, który kategoryzuje cyfry odręczne od 0-9. Czyni to poprzez utworzenie sieci neuronowej, która przyjmuje wartości pikseli 28 px x 28 px obrazu jako dane wejściowe i wyprowadza listę 10 prawdopodobieństw, po jednym dla każdej z cyfr klasyfikowane. Poniżej znajduje się przykład tego, jak wyglądają dane.  

![Cyfry MNIST](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Pobierz kod dla tego samouczka, pobierając i rozpakowywanie [vs code tools for AI repozytorium](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) w dowolnym miejscu na komputerze.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Pierwszą rzeczą, którą należy wykonać, aby utworzyć aplikację w usłudze Azure Machine Learning jest utworzenie obszaru roboczego. Obszar roboczy zawiera zasoby do szkolenia modeli, a także samych modeli przeszkolonych. Aby uzyskać więcej informacji, [zobacz, co to jest obszar roboczy](./concept-workspace.md). 

1. Na pasku aktywności Kod programu Visual Studio wybierz ikonę **platformy Azure,** aby otworzyć widok usługi Azure Machine Learning.
1. Kliknij prawym przyciskiem myszy subskrypcję platformy Azure i wybierz pozycję **Utwórz obszar roboczy**. 
    
    > [!div class="mx-imgBorder"]
    > ![Tworzenie obszaru roboczego](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Domyślnie jest generowana nazwa zawierająca datę i godzinę utworzenia. W polu wprowadzania tekstu zmień nazwę na "TeamWorkspace" i naciśnij **klawisz Enter**.
1. Wybierz **pozycję Utwórz nową grupę zasobów**. 
1. Nazwij swoją grupę zasobów "TeamWorkspace-rg" i naciśnij **klawisz Enter**. 
1. Wybierz lokalizację dla swojego obszaru roboczego. Zaleca się wybranie lokalizacji, która znajduje się najbliżej lokalizacji, którą zamierzasz wdrożyć model. Na przykład "Zachodnie stany USA 2".
1. Po wyświetleniu monitu o wybranie typu obszaru roboczego wybierz pozycję **Podstawowe,** aby utworzyć podstawowy obszar roboczy. Aby uzyskać więcej informacji na temat różnych ofert obszarów roboczych, zobacz [omówienie usługi Azure Machine Learning.](./overview-what-is-azure-ml.md#sku)

W tym momencie zostanie utworzone żądanie platformy Azure w celu utworzenia nowego obszaru roboczego na koncie. Po kilku minutach nowy obszar roboczy pojawi się w węźle subskrypcji. 

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

W obszarze roboczym można utworzyć co najmniej jeden eksperyment w celu śledzenia i analizowania poszczególnych przebiegów szkoleniowych modelu. Przebiegi można wykonać w chmurze platformy Azure lub na komputerze lokalnym.

1. Na pasku aktywności Kod programu Visual Studio wybierz ikonę **platformy Azure.** Pojawi się widok usługi Azure Machine Learning.
1. Rozwiń węzeł subskrypcji.
1. Rozwiń węzeł **TeamWorkspace.** 
1. Kliknij prawym przyciskiem myszy węzeł **Eksperymenty.**
1. Z menu kontekstowego **wybierz polecenie Utwórz eksperyment.**

    > [!div class="mx-imgBorder"]
    > ![Tworzenie eksperymentu](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Nazwij eksperyment "MNIST" i naciśnij **klawisz Enter,** aby utworzyć nowy eksperyment. 

Podobnie jak obszary robocze, żądanie jest wysyłane do platformy Azure w celu utworzenia eksperymentu z podanymi konfiguracjami. Po kilku minutach nowy eksperyment pojawi się w węźle *Eksperymenty* obszaru roboczego. 

## <a name="configure-compute-targets"></a>Konfigurowanie obiektów docelowych obliczeń

Obiekt docelowy obliczeń to zasób lub środowisko obliczeniowe, w którym uruchamiasz skrypty i wdrażasz wyszkolone modele. Aby uzyskać więcej informacji, zobacz [dokumentację celów obliczeniowych usługi Azure Machine Learning.](./concept-compute-target.md)

Aby utworzyć cel obliczeniowy:

1. Na pasku aktywności Kod programu Visual Studio wybierz ikonę **platformy Azure.** Pojawi się widok usługi Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace.** 
1. W obszarze węzła obszaru roboczego kliknij prawym przyciskiem myszy węzeł **Obliczeniowy** i wybierz polecenie **Utwórz obliczeń**. 

    > [!div class="mx-imgBorder"]
    > ![Tworzenie celu obliczeniowego](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Wybierz **opcję Obliczenia usługi Azure Machine Learning (AmlCompute)**. Usługa Azure Machine Learning Compute to infrastruktura obliczeniowa zarządzana, która umożliwia użytkownikowi łatwe tworzenie obliczeń jedno- lub wielowęzłowych, które mogą być używane z innymi użytkownikami w obszarze roboczym.
1. Wybierz rozmiar maszyny wirtualnej. Wybierz **Standard_F2s_v2** z listy opcji. Rozmiar maszyny Wirtualnej ma wpływ na czas potrzebny do szkolenia modeli. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
1. Nazwij swoje obliczenia "TeamWkspc-com" i naciśnij **klawisz Enter,** aby utworzyć swoje obliczenia.

    Plik pojawia się w programie VS Code z zawartością podobną do poniższej:

    ```json
    {
        "location": "westus2",
        "tags": {},
        "properties": {
            "computeType": "AmlCompute",
            "description": "",
            "properties": {
                "vmSize": "Standard_F2s_v2",
                "vmPriority": "dedicated",
                "scaleSettings": {
                    "maxNodeCount": 4,
                    "minNodeCount": 0,
                    "nodeIdleTimeBeforeScaleDown": 120
                },
                "userAccountCredentials": {
                    "adminUserName": "",
                    "adminUserPassword": "",
                    "adminUserSshPublicKey": ""
                },
                "subnetName": "",
                "vnetName": "",
                "vnetResourceGroupName": "",
                "remoteLoginPortPublicAccess": ""
            }
        }
    }
    ```

1. Po zakończeniu konfiguracji otwórz paletę poleceń, wybierając **polecenie Wyświetl > Paletę poleceń**.
1. Wprowadź następujące polecenie do palety poleceń, aby zapisać uruchomiony plik konfiguracyjny.

    ```text
    Azure ML: Save and Continue
    ```

Po kilku minutach nowy obiekt docelowy obliczeń pojawi się w węźle *Obliczeniowe* obszaru roboczego.

## <a name="create-a-run-configuration"></a>Tworzenie konfiguracji uruchamiania

Podczas przesyłania przebiegu szkolenia do obiektu docelowego obliczeń, należy również przesłać konfigurację potrzebną do uruchomienia zadania szkoleniowego. Na przykład skrypt, który zawiera kod szkolenia i zależności Pythona potrzebne do jego uruchomienia.

Aby utworzyć konfigurację uruchamiania:

1. Na pasku aktywności Kod programu Visual Studio wybierz ikonę **platformy Azure.** Pojawi się widok usługi Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace > Compute.** 
1. W obszarze węzła obliczeniowego kliknij prawym przyciskiem myszy węzeł obliczeniowy **TeamWkspc-com** i wybierz polecenie **Utwórz konfigurację uruchamiania**.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie konfiguracji uruchamiania](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Nazwij konfigurację biegu "MNIST-rc" i naciśnij **klawisz Enter,** aby utworzyć konfigurację uruchamiania.
1. Następnie wybierz pozycję **Utwórz nowe środowisko usługi Azure ML**. Środowiska definiują zależności wymagane do uruchamiania skryptów.
1. Nazwij swoje środowisko "MNIST-env" i naciśnij **klawisz Enter**.
1. Wybierz **plik zależności Conda** z listy.
1. Naciśnij **klawisz Enter,** aby przeglądać plik zależności Conda. W takim przypadku plik zależności `env.yml` jest plikiem wewnątrz `vscode-tools-for-ai/mnist-vscode-docs-sample` katalogu.

    Plik pojawia się w programie VS Code z zawartością podobną do poniższej:

    ```json
    {
        "name": "MNIST-env",
        "version": "1",
        "python": {
            "interpreterPath": "python",
            "userManagedDependencies": false,
            "condaDependencies": {
                "name": "vs-code-azure-ml-tutorial",
                "channels": [
                    "defaults"
                ],
                "dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip",
                    {
                        "pip": [
                            "azureml-defaults"
                        ]
                    }
                ]
            },
            "baseCondaEnvironment": null
        },
        "environmentVariables": {},
        "docker": {
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "baseDockerfile": null,
            "baseImageRegistry": {
                "address": null,
                "username": null,
                "password": null
            },
            "enabled": false,
            "arguments": []
        },
        "spark": {
            "repositories": [],
            "packages": [],
            "precachePackages": true
        },
        "inferencingStackVersion": null
    }
    ```

1. Po zakończeniu konfiguracji zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

1. Naciśnij **klawisz Enter,** aby przeglądać plik skryptu w celu uruchomienia na komputerze. W takim przypadku skrypt do szkolenia `train.py` modelu jest `vscode-tools-for-ai/mnist-vscode-docs-sample` plik wewnątrz katalogu.

    Plik o `MNIST-rc.runconfig` nazwie pojawia się w programie VS Code z zawartością podobną do poniższej:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "name": "MNIST-env",
            "version": "1",
            "python": {
                "interpreterPath": "python",
                "userManagedDependencies": false,
                "condaDependencies": {
                    "name": "vs-code-azure-ml-tutorial",
                    "channels": [
                        "defaults"
                    ],
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        "pip",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                },
                "baseCondaEnvironment": null
            },
            "environmentVariables": {},
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
                "baseDockerfile": null,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                },
                "enabled": false,
                "arguments": []
            },
            "spark": {
                "repositories": [],
                "packages": [],
                "precachePackages": true
            },
            "inferencingStackVersion": null
        },
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Po zakończeniu konfiguracji zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

Konfiguracja uruchamiania `MNIST-rc` jest dodawana w węźle obliczeniowym *TeamWkspc-com,* a konfiguracja `MNIST-env` środowiska jest dodawana w węźle *Środowiska.*

## <a name="train-the-model"></a>Uczenie modelu

Podczas procesu szkolenia model TensorFlow jest tworzony przez przetwarzanie danych szkoleniowych i wzorców uczenia osadzonych w nim dla każdej z odpowiednich cyfr klasyfikowanych. 

Aby uruchomić eksperyment usługi Azure Machine Learning:

1. Na pasku aktywności Kod programu Visual Studio wybierz ikonę **platformy Azure.** Pojawi się widok usługi Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **Eksperymenty > TeamWorkspace.** 
1. Kliknij prawym przyciskiem myszy eksperyment **MNIST.**
1. Wybierz **pozycję Uruchom eksperyment**.

    > [!div class="mx-imgBorder"]
    > ![Uruchamianie eksperymentu](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. Z listy opcji docelowych obliczeń wybierz cel **obliczeniowy TeamWkspc-com.**
1. Następnie wybierz konfigurację przebiegu **MNIST-rc.**
1. W tym momencie żądanie jest wysyłane do platformy Azure, aby uruchomić eksperyment na wybranym miejscu docelowym obliczeń w obszarze roboczym. Ten proces trwa kilka minut. Na ilość czasu do uruchomienia zadania szkoleniowego ma wpływ kilka czynników, takich jak typ obliczeń i rozmiar danych szkoleniowych. Aby śledzić postęp eksperymentu, kliknij prawym przyciskiem myszy bieżący węzeł uruchomienia i wybierz polecenie **Wyświetl przebieg w witrynie Azure portal**.
1. Po wyświetleniu okna dialogowego z prośbą o otwarcie zewnętrznej witryny sieci Web wybierz opcję **Otwórz**.

    > [!div class="mx-imgBorder"]
    > ![Śledzenie postępu eksperymentu](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Po zakończeniu szkolenia modelu etykieta stanu obok węzła uruchamiania jest aktualizowana na "Zakończone".

## <a name="register-the-model"></a>Rejestrowanie modelu

Teraz, po przeszkoleniu modelu, można zarejestrować go w obszarze roboczym. 

Aby zarejestrować model:

1. Na pasku aktywności Kod programu Visual Studio wybierz ikonę **platformy Azure.** Pojawi się widok usługi Azure Machine Learning.
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń **eksperymenty > teamworkspace > węzła MNIST.**
1. Pobierz wyniki modelu generowane na podstawie szkolenia modelu. Kliknij prawym przyciskiem myszy węzeł **Uruchom 1** uruchom i wybierz polecenie **Pobierz dane wyjściowe**. 

    > [!div class="mx-imgBorder"]
    > ![Pobieranie wyników modelu](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Wybierz katalog, w który chcesz zapisać pobrane dane wyjściowe. Domyślnie dane wyjściowe są umieszczane w katalogu aktualnie otwierany w programie Visual Studio Code.
1. Kliknij prawym przyciskiem myszy węzeł **Modele** i wybierz polecenie **Zarejestruj model**.

    > [!div class="mx-imgBorder"]
    > ![Zarejestruj model](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Nazwij swój model "MNIST-TensorFlow-model" i naciśnij **klawisz Enter**.
1. Model TensorFlow składa się z kilku plików. Wybierz **folder Modelu** jako format ścieżki modelu z listy opcji. 
1. Wybierz `azureml_outputs/Run_1/outputs/outputs/model` katalog.

    Plik zawierający konfiguracje modelu pojawia się w programie Visual Studio Code o podobnej zawartości do poniższej:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Po zakończeniu konfiguracji zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

Po kilku minutach model pojawia się w węźle *Modele.*

## <a name="deploy-the-model"></a>Wdrażanie modelu

W programie Visual Studio Code można wdrożyć model jako usługę sieci web do:

+ Wystąpienia kontenera platformy Azure (ACI).
+ Usługa Azure Kubernetes (AKS).

Nie trzeba tworzyć kontenera ACI, aby przetestować z wyprzedzeniem, ponieważ kontenery ACI są tworzone w razie potrzeby. Jednak należy skonfigurować klastry AKS z wyprzedzeniem. Aby uzyskać więcej informacji na temat opcji wdrażania, zobacz [wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md) .

Aby wdrożyć usługę sieci web jako usługę ACI:

1. Na pasku aktywności Kod programu Visual Studio wybierz ikonę **platformy Azure.** Pojawi się widok usługi Azure Machine Learning.
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace > Models.** 
1. Kliknij prawym przyciskiem myszy **model MNIST-TensorFlow** i wybierz polecenie **Wdrażanie usługi z modelu zarejestrowanego**.

    > [!div class="mx-imgBorder"]
    > ![Wdrażanie modelu](./media/tutorial-train-deploy-image-classification-model-vscode/deploy-model.png)

1. Wybierz **wystąpienie kontenera platformy Azure**.
1. Nazwij swoją usługę "mnist-tensorflow-svc" i naciśnij **klawisz Enter**.
1. Wybierz skrypt do uruchomienia w **Enter** kontenerze, naciskając klawisz Enter `score.py` w `mnist-vscode-docs-sample` polu wprowadzania i przeglądając plik w katalogu.
1. Podaj zależności potrzebne do uruchomienia **Enter** skryptu, naciskając klawisz `env.yml` Enter w `mnist-vscode-docs-sample` polu wprowadzania i przeglądając plik w katalogu.

    Plik zawierający konfiguracje modelu pojawia się w programie Visual Studio Code o podobnej zawartości do poniższej:

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

1. Po zakończeniu konfiguracji zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

W tym momencie żądanie jest wysyłane do platformy Azure, aby wdrożyć usługę sieci web. Ten proces trwa kilka minut. Po wdrożeniu nowa usługa pojawia się w *węźle Punkty końcowe.*

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z instrukcjami dotyczącymi uczenia z pomocą usługi Azure Machine Learning poza programem Visual Studio Code, zobacz [Samouczek: Trenuj modele za pomocą usługi Azure Machine Learning.](tutorial-train-models-with-aml.md)
* Aby zapoznać się z instrukcjami dotyczącymi lokalnego edytowania, uruchamiania i debugowania kodu, zobacz [samouczek python hello-world](https://code.visualstudio.com/docs/Python/Python-tutorial).

