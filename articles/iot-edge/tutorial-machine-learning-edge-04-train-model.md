---
title: 'Samouczek: uczenie i wdrażanie modelu — Machine Learning na Azure IoT Edge'
description: W ramach tego samouczka nauczysz model uczenia maszynowego przy użyciu Azure Machine Learning a następnie Spakuj model jako obraz kontenera, który można wdrożyć jako moduł Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5c754373ba9437c631e62acbb5d6d246db4c862
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650762"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Samouczek: uczenie i wdrażanie modelu Azure Machine Learning

> [!NOTE]
> Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Jeśli ten artykuł został osiągnięty bezpośrednio, zachęcamy do rozpoczęcia od [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii w celu uzyskania najlepszych wyników.

W tym artykule wykonamy następujące zadania:

* Użyj Azure Notebooks do uczenia modelu uczenia maszynowego.
* Pakuj model szkolony jako obraz kontenera.
* Wdróż obraz kontenera jako moduł Azure IoT Edge.

Azure Notebooks korzystać z obszaru roboczego Azure Machine Learning, czyli podstawy do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.

Kroki opisane w tym artykule mogą być zwykle wykonywane przez analityków danych.

## <a name="set-up-azure-notebooks"></a>Skonfiguruj Azure Notebooks

Używamy Azure Notebooks do hostowania dwóch notesów Jupyter i plików pomocniczych. W tym miejscu utworzymy i skonfigurujemy projekt Azure Notebooks. Jeśli nie korzystasz z Jupyter i/lub Azure Notebooks, poniżej przedstawiono kilka dokumentów wprowadzających:

* **Szybki Start:** [Tworzenie i udostępnianie notesu](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Samouczek:** [Tworzenie i uruchamianie notesu Jupyter przy użyciu języka Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Korzystanie z notesów platformy Azure zapewnia spójne środowisko dla tego ćwiczenia.

> [!NOTE]
> Po skonfigurowaniu usługi Azure Notebooks można uzyskać dostęp z dowolnej maszyny. Podczas instalacji należy użyć maszyny wirtualnej tworzenia, która ma wszystkie pliki, które będą potrzebne.

### <a name="create-an-azure-notebooks-account"></a>Utwórz konto Azure Notebooks

Aby użyć Azure Notebooks, musisz utworzyć konto. Konta notesu platformy Azure są niezależne od subskrypcji platformy Azure.

1. Przejdź do [notesów platformy Azure](https://notebooks.azure.com).

1. Kliknij przycisk **Zaloguj** w prawym górnym rogu strony.

1. Zaloguj się przy użyciu konta służbowego (Azure Active Directory) lub konta osobistego (konto Microsoft).

1. Jeśli wcześniej nie korzystasz z Azure Notebooks, zostanie wyświetlony monit o przyznanie dostępu do aplikacji Azure Notebooks.

1. Utwórz identyfikator użytkownika dla Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Przekaż pliki notesu Jupyter

Będziemy przekazywać przykładowe pliki notesu do nowego projektu Azure Notebooks.

1. Na stronie użytkownika nowego konta wybierz pozycję **Moje projekty** z górnego paska menu.

1. W oknie dialogowym **Utwórz nowy projekt** Podaj **nazwę projektu** , która również automatycznie tworzy **Identyfikator projektu**.

1. Pozostaw opcję **publiczny** i **plik Readme** niezaznaczone, ponieważ nie ma potrzeby, aby projekt był publiczny lub miał plik Readme.

1. Wybierz pozycję **Utwórz**.

1. Wybierz pozycję **Przekaż** (ikona strzałki w górę) i wybierz pozycję **z komputera**.

1. Wybierz pozycję **Wybierz pliki**.

1. Przejdź do **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Zaznacz wszystkie pliki na liście, a następnie kliknij przycisk **Otwórz**.

1. Wybierz pozycję **Przekaż** , aby rozpocząć przekazywanie, a następnie wybierz pozycję **gotowe** po zakończeniu procesu.

### <a name="azure-notebook-files"></a>Pliki notesu platformy Azure

Przejrzyjmy pliki przekazane do projektu Azure Notebooks. Działania w tej części samouczka obejmują między innymi pliki notesów, które korzystają z kilku plików pomocniczych.

* **01 — turbofan\_regresji. ipynb:** Ten Notes używa obszaru roboczego usługi Machine Learning, aby utworzyć i uruchomić eksperyment uczenia maszynowego. W szerokim zakresie Notes wykonuje następujące czynności:

  1. Pobiera dane z konta usługi Azure Storage, które zostało wygenerowane przez zespół.
  1. Eksploruje i przygotowuje dane, a następnie używa danych do uczenia modelu klasyfikatora.
  1. Oceń model z eksperymentu przy użyciu zestawu danych testowych (test\_FD003. txt).
  1. Publikuje najlepszy model klasyfikatora w obszarze roboczym usługi Machine Learning.

* **02 — turbofan\_wdróż\_model. ipynb:** Ten Notes przyjmuje model utworzony w poprzednim notesie i używa go do utworzenia obrazu kontenera gotowego do wdrożenia na urządzeniu Azure IoT Edge. Notes wykonuje następujące czynności:

  1. Tworzy skrypt oceniania dla modelu.
  1. Tworzy obraz kontenera przy użyciu modelu klasyfikatora, który został zapisany w obszarze roboczym usługi Machine Learning.
  1. Wdraża obraz jako usługę sieci Web w wystąpieniu kontenera platformy Azure.
  1. Używa usługi sieci Web do walidacji modelu i obrazu działa zgodnie z oczekiwaniami. Sprawdzony obraz zostanie wdrożony na naszym urządzeniu IoT Edge w części [Tworzenie i wdrażanie niestandardowych modułów IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) w tym samouczku.

* **Test\_FD003. txt:** Ten plik zawiera dane, które będą używane jako nasz zestaw testów podczas weryfikacji przeszkolonego klasyfikatora. Wybrano użycie danych testowych, zgodnie z opisem dla oryginalnego konkursu, jako nasz zestaw testów dla uproszczenia.

* **Pozostałego czasu eksploatacji\_FD003. txt:** Ten plik zawiera pozostałego czasu eksploatacji dla ostatniego cyklu każdego urządzenia w pliku test\_FD003. txt. Zapoznaj się z plikami Readme. txt i uszkodzeniami modelowania propagacji. PDF w źródle C:\\\\IoTEdgeAndMlSample\\dane\\TurboFan, aby uzyskać szczegółowy opis danych.

* **Utils.py:** Zawiera zestaw funkcji narzędzia Python do pracy z danymi. Pierwszy Notes zawiera szczegółowy opis funkcji.

* **README.MD:** Plik Readme opisujący korzystanie z notesów.  

## <a name="run-azure-notebooks"></a>Uruchom Azure Notebooks

Po utworzeniu projektu można uruchomić notesy. 

1. Na stronie projektu wybierz pozycję **01-turbofan\_regresja. ipynb**.

    ![Wybierz pierwszy Notes do uruchomienia](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Jeśli zostanie wyświetlony monit, wybierz jądro Python 3,6 z okna dialogowego i wybierz pozycję **Ustaw jądro**.

1. Jeśli Notes jest wymieniony jako **niezaufany**, kliknij widżet **niezaufany** w prawym górnym rogu notesu. Po wyświetleniu okna dialogowego wybierz pozycję **Ufaj**.

1. W notesie przewiń w dół do komórki, która następuje po instrukcji **Set Global Properties** , i która zaczyna się od kodu `AZURE_SUBSCRIPTION_ID =` i wprowadź wartości dla subskrypcji, ustawień i zasobów platformy Azure.

    ![Ustawianie właściwości globalnych w notesie](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Uruchom tę komórkę, wybierając pozycję **Uruchom** na pasku narzędzi.

    Gdy komórka jest uruchomiona, wyświetla gwiazdkę między nawiasami kwadratowymi ([\*]). Po zakończeniu operacji komórki gwiazdka jest zastępowana liczbą, a odpowiednie dane wyjściowe mogą pojawić się. Komórki w notesie kompilują się sekwencyjnie i tylko jeden może być uruchomiony w danym momencie.

    Postępuj zgodnie z instrukcjami w notesie. Możesz również użyć opcji uruchamiania z menu **komórka** , `Ctrl` + `Enter` do uruchamiania komórki, a `Shift` + `Enter`, aby uruchomić komórkę i przejść do następnej komórki.

    > [!TIP]
    > Aby zapewnić spójne operacje na komórkach, należy unikać uruchamiania tego samego notesu z wielu kart w przeglądarce.

1. Przewiń w dół do komórki znajdującej się bezpośrednio po tekście przeglądowym **tworzenia obszaru roboczego** i uruchom tę komórkę. W danych wyjściowych komórki poszukaj linku, który nakazuje zalogowanie się w celu uwierzytelnienia. 

    ![Monit logowania w celu uwierzytelnienia urządzenia](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Otwórz link i wprowadź określony kod. Ta procedura logowania służy do uwierzytelniania notesu Jupyter w celu uzyskania dostępu do zasobów platformy Azure przy użyciu międzyplatformowego interfejsu wiersza polecenia Microsoft Azure.  

    ![Potwierdzenie uwierzytelniania aplikacji na urządzeniu](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. W tym momencie można uruchomić resztę komórek. Jest optymalne, aby uruchomić wszystkie komórki, aby kod w komórkach działał sekwencyjnie. Wybierz opcję **Uruchom wszystkie** z menu **komórki** . Przewiń kopię zapasową w notesie i sprawdź, jak są wykonywane operacje na komórkach.

    W sekcji **Eksploruj dane** można przejrzeć komórki w podsekcji **odczyty czujników i pozostałego czasu eksploatacji** , które renderują scatterplots pomiarów czujników.

    ![Odczyty czujników scatterplots](media/tutorial-machine-learning-edge-04-train-model/sensor-readings.png)

1. Zapisz Notes i wróć do strony projektu, klikając nazwę projektu w prawym górnym rogu notesu lub wracając do tyłu w przeglądarce.

1. Otwórz przystawkę **02-turbofan\_wdróż\_model. ipynb** i powtórz kroki opisane w tej procedurze, aby uruchomić drugi Notes.

1. Zapisz Notes i wróć do strony projektu, klikając nazwę projektu w prawym górnym rogu notesu lub wracając do tyłu w przeglądarce.

### <a name="verify-success"></a>Weryfikowanie sukcesu

Aby sprawdzić, czy notesy zostały ukończone pomyślnie, sprawdź, czy utworzono kilka elementów.

1. Na stronie projekt Azure Notebooks wybierz pozycję **Pokaż ukryte elementy** , aby nazwy elementów zaczynające się od okresu pojawiły się.

1. Sprawdź, czy zostały utworzone następujące pliki:

    | Plik | Opis |
    | --- | --- |
    | /.azureml/config.JSON./aml_config | Plik konfiguracji służący do tworzenia Obszar roboczy usługi Azure Machine Learning. |
    | ./aml_config/model_config. JSON | Plik konfiguracji, który będzie musiał wdrożyć model w obszarze roboczym **turbofanDemo** Machine Learning na platformie Azure. |
    | MyENV. yml| Zawiera informacje o zależnościach wdrożonego modelu Machine Learning.|

1. Sprawdź, czy w Azure Portal istnieje obszar roboczy **turboFanDemo** Machine Learning w grupie zasobów.

### <a name="debugging"></a>Debugowanie

Możesz umieścić instrukcje języka Python w notesie do debugowania, głównie polecenie `print()`. Jeśli widzisz zmienne lub obiekty, które nie są zdefiniowane, uruchom komórki, w których są one po raz pierwszy zadeklarowane lub utworzone.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto dwóch Jupyterych notesów uruchomionych w Azure Notebooks, aby użyć danych z urządzeń TurboFan do uczenia pozostałej klasyfikatora okresu użytkowania (pozostałego czasu eksploatacji) w celu zapisania klasyfikatora jako modelu, utworzenia obrazu kontenera oraz wdrożenia i przetestowania obrazu jako sieci Web SE rvice.

Przejdź do następnego artykułu, aby utworzyć urządzenie IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzenia IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
