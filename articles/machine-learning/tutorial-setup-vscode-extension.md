---
title: 'Samouczek: Konfigurowanie rozszerzenia Visual Studio Code'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować rozszerzenie Azure Machine Learning Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: 583071ee22e4fb9cffc741520b1583790002a5bf
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604873"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Konfigurowanie rozszerzenia Visual Studio Code Azure Machine Learning

Dowiedz się, jak instalować i uruchamiać skrypty przy użyciu rozszerzenia Azure Machine Learning Visual Studio Code.

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Instalowanie rozszerzenia Azure Machine Learning Visual Studio Code
> * Zaloguj się do konta platformy Azure z poziomu Visual Studio Code
> * Użyj rozszerzenia Azure Machine Learning, aby uruchomić przykładowy skrypt

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz takiego konta, zarejestruj się, aby wypróbować [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
- Program Visual Studio Code. Jeśli go nie masz, [Zainstaluj go](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

1. Otwórz program Visual Studio Code.
1. Wybierz ikonę **rozszerzenia** na **pasku działania** , aby otworzyć widok rozszerzenia.
1. W widoku rozszerzeń Wyszukaj ciąg "Azure Machine Learning".
1. Wybierz pozycję **Zainstaluj**.

    > [!div class="mx-imgBorder"]
    > ![zainstalować rozszerzenia VS Code Azure Machine Learning](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Alternatywnie możesz zainstalować rozszerzenie Azure Machine Learning za pośrednictwem Visual Studio Marketplace, [pobierając Instalatora bezpośrednio](https://aka.ms/vscodetoolsforai). 

Pozostała część kroków tego samouczka została przetestowana z **wersją 0.6.8** rozszerzenia.

## <a name="sign-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Aby udostępnić zasoby i uruchamiać obciążenia na platformie Azure, musisz zalogować się przy użyciu poświadczeń konta platformy Azure. Aby ułatwić zarządzanie kontami, Azure Machine Learning automatycznie instaluje rozszerzenie konta platformy Azure. Odwiedź następującą witrynę, aby [dowiedzieć się więcej o rozszerzeniu konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Otwórz paletę poleceń, wybierając pozycję **wyświetl > paleta poleceń** na pasku menu. 
1. Wprowadź polecenie "Azure: Sign in" w palecie poleceń, aby uruchomić proces logowania.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Uruchamianie skryptu szkoleniowego modelu uczenia maszynowego na platformie Azure

Po zalogowaniu się do platformy Azure przy użyciu poświadczeń konta wykonaj kroki opisane w tej sekcji, aby dowiedzieć się, jak używać rozszerzenia do uczenia modelu uczenia maszynowego.

1. Pobierz i rozpakuj [vs Code narzędzia dla REPOZYTORIUM AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) w dowolnym miejscu na komputerze.
1. Otwórz katalog `mnist-vscode-docs-sample` w Visual Studio Code.
1. Na pasku działania wybierz ikonę **platformy Azure** .
1. Wybierz ikonę **Uruchom eksperyment** w górnej części widoku Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Uruchom eksperyment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Gdy zostanie rozwinięta paleta poleceń, postępuj zgodnie z monitami.

    1. Wybierz swoją subskrypcję platformy Azure.
    1. Wybierz pozycję **Utwórz nowy obszar roboczy usługi Azure ml**
    1. Wybierz typ zadania **szkoleniowego TensorFlow z jednym węzłem** .
    1. Wprowadź `train.py` jako skrypt do uczenia. Jest to plik, który zawiera kod do modelu uczenia maszynowego, który klasyfikuje obrazy cyfr odręcznych.
    1. Określ następujące pakiety jako wymagania do uruchomienia.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. W tym momencie plik konfiguracji podobny do przedstawionego poniżej pojawia się w edytorze tekstu. Konfiguracja zawiera informacje wymagane do uruchomienia zadania szkoleniowego, takie jak plik zawierający kod służący do uczenia modelu i wszystkie zależności języka Python określone w poprzednim kroku.

    ```json
    {
        "workspace": "WS01311608",
        "resourceGroup": "WS01311608-rg1",
        "location": "South Central US",
        "experiment": "WS01311608-exp1",
        "compute": {
            "name": "WS01311608-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS01311608-com1-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Gdy Twoja konfiguracja zostanie zazadowalająca, Prześlij eksperyment, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Submit Experiment
    ```

    Spowoduje to wysłanie `train.py` i pliku konfiguracji do obszaru roboczego Azure Machine Learning. Zadanie szkolenia jest następnie uruchamiane w zasobie obliczeniowym na platformie Azure.

### <a name="track-the-progress-of-the-training-script"></a>Śledź postęp skryptu szkoleniowego

Uruchamianie skryptu może potrwać kilka minut. Aby śledzić postęp:

1. Na pasku działania wybierz ikonę **platformy Azure** .
1. Rozwiń węzeł subskrypcji.
1. Rozwiń węzeł aktualnie działającego eksperymentu. Ta lokalizacja znajduje się w węźle `{workspace}/Experiments/{experiment}`, w którym wartości obszaru roboczego i eksperymentu są takie same jak właściwości zdefiniowane w pliku konfiguracji.
1. Wszystkie uruchomienia eksperymentu są wymienione na liście, a także ich stan. Aby uzyskać najnowszy stan, kliknij ikonę odświeżania w górnej części widoku Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![śledzenie postępu eksperymentu](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Pobierz przeszkolony model

Po zakończeniu eksperymentu dane wyjściowe są modelem szkolonym. Aby pobrać dane wyjściowe lokalnie:

1. Kliknij prawym przyciskiem myszy najnowszy przebieg i wybierz pozycję **Pobierz dane wyjściowe**.

    > [!div class="mx-imgBorder"]
    > ![pobrać przeszkolony model](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Wybierz lokalizację, w której mają zostać zapisane dane wyjściowe.
1. Folder o nazwie przebiegu jest pobierany lokalnie. Przejdź do tej strony.
1. Pliki modelu znajdują się w katalogu `outputs/outputs/model`.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: uczenie i wdrażanie modelu TensorFlow klasyfikacji obrazów przy użyciu rozszerzenia Visual Studio Code Azure Machine Learning](tutorial-train-deploy-image-classification-model-vscode.md).
