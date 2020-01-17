---
title: 'Samouczek: Konfigurowanie rozszerzenia Visual Studio Code Azure Machine Learning'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować rozszerzenie Azure Machine Learning Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157612"
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
- Zainstaluj [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), lekki Edytor kodu dla wielu platform. 

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

1. Otwórz program Visual Studio Code.
1. Wybierz ikonę **rozszerzenia** na **pasku działania** , aby otworzyć widok rozszerzenia.
1. W widoku rozszerzeń Wyszukaj ciąg "Azure Machine Learning".
1. Wybierz pozycję **Zainstaluj**.

> [!NOTE]
> Alternatywnie możesz zainstalować rozszerzenie Azure Machine Learning za pośrednictwem Visual Studio Marketplace, [pobierając Instalatora bezpośrednio](https://aka.ms/vscodetoolsforai). 

Pozostała część kroków tego samouczka została przetestowana z **wersją 0.6.8** rozszerzenia.

## <a name="sign-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Aby udostępnić zasoby i uruchamiać obciążenia na platformie Azure, musisz zalogować się przy użyciu poświadczeń konta platformy Azure. Aby ułatwić zarządzanie kontami, Azure Machine Learning automatycznie instaluje rozszerzenie konta platformy Azure. Odwiedź następującą witrynę, aby [dowiedzieć się więcej o rozszerzeniu konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Otwórz paletę poleceń, wybierając pozycję **wyświetl > paleta poleceń** na pasku menu. 
1. Wprowadź polecenie "Azure: Zaloguj się" w polu tekstowym, aby rozpocząć proces logowania.

## <a name="run-a-script-in-azure"></a>Uruchamianie skryptu na platformie Azure

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
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
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

1. Wybierz pozycję **Prześlij eksperyment** , aby uruchomić eksperyment na platformie Azure. Spowoduje to wysłanie `train.py` i pliku konfiguracji do obszaru roboczego Azure Machine Learning. Zadanie szkolenia jest następnie uruchamiane w zasobie obliczeniowym na platformie Azure.
1. Po kilku minutach katalog o nazwie `output` jest tworzony lokalnie zawierający szkolony model TensorFlow.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: uczenie i wdrażanie modelu TensorFlow klasyfikacji obrazów przy użyciu rozszerzenia Visual Studio Code Azure Machine Learning](tutorial-train-deploy-image-classification-model-vscode.md).
