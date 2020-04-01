---
title: 'Samouczek: Konfigurowanie rozszerzenia kodu programu Visual Studio'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować rozszerzenie usługi Visual Studio Code Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: 583071ee22e4fb9cffc741520b1583790002a5bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77604873"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Konfigurowanie rozszerzenia kodu programu Azure Machine Learning Visual Studio

Dowiedz się, jak zainstalować i uruchomić skrypty przy użyciu rozszerzenia kodu usługi Azure Machine Learning Visual Studio.

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Instalowanie rozszerzenia kodu programu Azure Machine Learning Visual Studio
> * Zaloguj się na swoje konto platformy Azure za pomocą kodu programu Visual Studio
> * Uruchom przykładowy skrypt za pomocą rozszerzenia usługi Azure Machine Learning

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli go nie masz, zarejestruj się, aby wypróbować [bezpłatną lub płatną wersję usługi Azure Machine Learning.](https://aka.ms/AMLFree)
- Program Visual Studio Code. Jeśli go nie masz, [zainstaluj go](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

1. Otwórz program Visual Studio Code.
1. Wybierz ikonę **Rozszerzenia** na **pasku aktywności,** aby otworzyć widok Rozszerzenia.
1. W widoku Rozszerzenia wyszukaj hasło "Azure Machine Learning".
1. Wybierz pozycję **Zainstaluj**.

    > [!div class="mx-imgBorder"]
    > ![Instalowanie rozszerzenia kodu usługi Azure Machine Learning VS](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Alternatywnie można zainstalować rozszerzenie usługi Azure Machine Learning za pośrednictwem portalu Visual Studio Marketplace, [pobierając instalator bezpośrednio.](https://aka.ms/vscodetoolsforai) 

Pozostałe kroki w tym samouczku zostały przetestowane w **wersji 0.6.8** rozszerzenia.

## <a name="sign-in-to-your-azure-account"></a>Logowanie się do konta platformy Azure

Aby aprowizować zasoby i uruchamiać obciążenia na platformie Azure, musisz zalogować się przy użyciu poświadczeń konta platformy Azure. Aby pomóc w zarządzaniu kontami, usługa Azure Machine Learning automatycznie instaluje rozszerzenie konta platformy Azure. Odwiedź następującą witrynę, aby [dowiedzieć się więcej o rozszerzeniu konta platformy Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

1. Otwórz paletę poleceń, wybierając **polecenie Wyświetl > paletę poleceń** z paska menu. 
1. Wprowadź polecenie "Azure: Zaloguj się" do palety poleceń, aby rozpocząć proces logowania.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Uruchamianie skryptu szkoleniowego modelu uczenia maszynowego na platformie Azure

Teraz, gdy zalogowano się do platformy Azure przy użyciu poświadczeń konta, użyj kroków w tej sekcji, aby dowiedzieć się, jak użyć rozszerzenia do uczenia modelu uczenia maszynowego.

1. Pobierz i rozpakuj [repozytorium VS Code Tools for AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) w dowolnym miejscu na komputerze.
1. Otwórz `mnist-vscode-docs-sample` katalog w programie Visual Studio Code.
1. Wybierz ikonę **platformy Azure** na pasku aktywności.
1. Wybierz ikonę **Uruchom eksperyment** u góry widoku usługi Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Uruchom eksperyment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Po rozwinięciu palety poleceń postępuj zgodnie z instrukcjami.

    1. Wybierz swoją subskrypcję platformy Azure.
    1. Wybieranie **opcji Utwórz nowy obszar roboczy usługi Azure ML**
    1. Wybierz typ zadania **Szkolenia jednowęzłowego TensorFlow.**
    1. Wprowadź `train.py` jako skrypt do pociągu. Jest to plik, który zawiera kod do modelu uczenia maszynowego, który kategoryzuje obrazy cyfr odręcznych.
    1. Określ następujące pakiety jako wymagania do uruchomienia.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. W tym momencie w edytorze tekstu pojawia się plik konfiguracyjny podobny do poniższego. Konfiguracja zawiera informacje wymagane do uruchomienia zadania szkoleniowego, takie jak plik, który zawiera kod do szkolenia modelu i wszelkie zależności języka Python określone w poprzednim kroku.

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

1. Gdy konfiguracja jest zadowalająca, prześlij eksperyment, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Submit Experiment
    ```

    Spowoduje to `train.py` wysłanie pliku i konfiguracji do obszaru roboczego usługi Azure Machine Learning. Zadanie szkoleniowe jest następnie uruchamiane na zasobie obliczeniowym na platformie Azure.

### <a name="track-the-progress-of-the-training-script"></a>Śledzenie postępów skryptu szkoleniowego

Uruchomienie skryptu może potrwać kilka minut. Aby śledzić jego postępy:

1. Wybierz ikonę **platformy Azure** z paska aktywności.
1. Rozwiń węzeł subskrypcji.
1. Rozwiń węzeł aktualnie uruchomionego eksperymentu. Znajduje się ona `{workspace}/Experiments/{experiment}` wewnątrz węzła, w którym wartości dla obszaru roboczego i eksperymentu są takie same jak właściwości zdefiniowane w pliku konfiguracyjnym.
1. Wszystkie przebiegi eksperymentu są wymienione, a także ich stan. Aby uzyskać najnowszy stan, kliknij ikonę odświeżania u góry widoku usługi Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Śledź postęp eksperymentu](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Pobierz przeszkolony model

Po zakończeniu uruchamiania eksperymentu dane wyjściowe są przeszkolonym modelem. Aby pobrać dane wyjściowe lokalnie:

1. Kliknij prawym przyciskiem myszy najnowsze uruchomienie i wybierz **polecenie Pobierz dane wyjściowe**.

    > [!div class="mx-imgBorder"]
    > ![Pobierz przeszkolony model](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Wybierz lokalizację, w której chcesz zapisać dane wyjściowe.
1. Folder z nazwą biegu jest pobierany lokalnie. Przejdź do tej strony.
1. Pliki modelu znajdują `outputs/outputs/model` się wewnątrz katalogu.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Szkolenie i wdrażanie modelu tensorflow klasyfikacji obrazu przy użyciu rozszerzenia kodu usługi Azure Machine Learning Visual Studio](tutorial-train-deploy-image-classification-model-vscode.md).
