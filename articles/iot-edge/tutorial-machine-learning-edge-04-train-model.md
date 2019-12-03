---
title: 'Samouczek: uczenie i wdrażanie modelu — Machine Learning na Azure IoT Edge'
description: W ramach tego samouczka nauczysz model uczenia maszynowego przy użyciu Azure Machine Learning a następnie Spakuj model jako obraz kontenera, który można wdrożyć jako moduł Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c0613d319c0c82fa61d75ed016d68d38dfcea8d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701823"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Samouczek: uczenie i wdrażanie modelu Azure Machine Learning

> [!NOTE]
> Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Jeśli ten artykuł został osiągnięty bezpośrednio, zachęcamy do rozpoczęcia od [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii w celu uzyskania najlepszych wyników.

W tym artykule używamy najpierw Azure Notebooks do uczenia modelu uczenia maszynowego przy użyciu Azure Machine Learning, a następnie pakowania tego modelu jako obrazu kontenera, który można wdrożyć jako moduł Azure IoT Edge. Azure Notebooks korzystać z Azure Machine Learning obszaru roboczego, który stanowi podstawę do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.

Działania w tej części samouczka są podzielone na dwa notesy.

* **01 — turbofan\_regresji. ipynb:** Ten Notes zawiera instrukcje dotyczące uczenia i publikowania modelu przy użyciu Azure Machine Learning. W szerokiej mierze czynności są następujące:

  1. Pobieranie, przygotowywanie i Eksplorowanie danych szkoleniowych
  2. Używanie obszaru roboczego usługi do tworzenia i uruchamiania eksperymentu uczenia maszynowego
  3. Oceń wyniki modelu z eksperymentu
  4. Publikowanie najlepszego modelu w obszarze roboczym usługi

* **02 — turbofan\_wdróż\_model. ipynb:** Ten Notes przyjmuje model utworzony w poprzednim notesie i używa go do utworzenia obrazu kontenera gotowego do wdrożenia na urządzeniu Azure IoT Edge.

  1. Utwórz skrypt oceniania dla modelu
  2. Tworzenie i publikowanie obrazu
  3. Wdrażanie obrazu jako usługi sieci Web w usłudze Azure Container instance
  4. Użyj usługi sieci Web, aby sprawdzić poprawność modelu i obraz działał zgodnie z oczekiwaniami

Kroki opisane w tym artykule mogą być zwykle wykonywane przez analityków danych.

## <a name="set-up-azure-notebooks"></a>Skonfiguruj Azure Notebooks

Używamy Azure Notebooks do hostowania dwóch notesów Jupyter i plików pomocniczych. W tym miejscu utworzymy i skonfigurujemy projekt Azure Notebooks. Jeśli nie korzystasz z Jupyter i/lub Azure Notebooks, poniżej przedstawiono kilka dokumentów wprowadzających:

* **Szybki Start:** [Tworzenie i udostępnianie notesu](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Samouczek:** [Tworzenie i uruchamianie notesu Jupyter przy użyciu języka Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Tak jak w przypadku maszyny wirtualnej dewelopera, korzystanie z notesów platformy Azure zapewnia spójne środowisko dla tego ćwiczenia.

> [!NOTE]
> Po skonfigurowaniu usługi Azure Notebooks można uzyskać dostęp z dowolnej maszyny. Podczas instalacji należy użyć maszyny wirtualnej deweloperskiej, która ma wszystkie pliki, które będą potrzebne.

### <a name="create-an-azure-notebooks-account"></a>Utwórz konto Azure Notebooks

Konta notesu platformy Azure są niezależne od subskrypcji platformy Azure. Aby użyć Azure Notebooks, musisz utworzyć konto.

1. Przejdź do [notesów platformy Azure](https://notebooks.azure.com).

2. Kliknij przycisk **Zaloguj** w prawym górnym rogu strony.

3. Zaloguj się przy użyciu konta służbowego (Azure Active Directory) lub konta osobistego (konto Microsoft).

4. Jeśli wcześniej nie korzystasz z Azure Notebooks, zostanie wyświetlony monit o przyznanie dostępu do aplikacji Azure Notebooks.

5. Utwórz identyfikator użytkownika dla Azure Notebooks.

### <a name="upload-jupyter-notebooks-files"></a>Przekazywanie plików notesów Jupyter

W tym kroku utworzymy nowy projekt Azure Notebooks i przekażesz do niego pliki. W odróżnieniu od plików, które przekazujemy:

* **01-turbofan\_regresja. ipynb**: plik notesu Jupyter, który przeprowadzi proces pobierania danych wygenerowanych przez zespół z konta usługi Azure Storage; Eksplorowanie i przygotowywanie danych do szkolenia klasyfikatora; uczenie modelu; Testowanie danych przy użyciu zestawu danych testowych znalezionego w pliku test\_FD003. txt; i na koniec zapisywania modelu klasyfikatora w obszarze roboczym usługi Machine Learning.

* **02 — turbofan\_wdróż\_model. ipynb:** Jupyter Notes, który przeprowadzi Cię przez proces używania modelu klasyfikatora zapisanego w obszarze roboczym usługi Machine Learning, aby utworzyć obraz kontenera. Po utworzeniu obrazu Notes przeprowadzi Cię przez proces wdrażania obrazu jako usługi sieci Web, aby można było sprawdzić, czy działa zgodnie z oczekiwaniami. Sprawdzony obraz zostanie wdrożony na naszym urządzeniu IoT Edge w części [Tworzenie i wdrażanie niestandardowych modułów IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) w tym samouczku.

* **Test\_FD003. txt:** Ten plik zawiera dane, które będą używane jako nasz zestaw testów podczas weryfikacji przeszkolonego klasyfikatora. Wybieramy użycie danych testowych zgodnie z opisem dla oryginalnego konkursu jako nasz zestaw testów dla uproszczenia przykładu.

* **Pozostałego czasu eksploatacji\_FD003. txt:** Ten plik zawiera pozostałego czasu eksploatacji dla ostatniego cyklu każdego urządzenia w pliku test\_FD003. txt. Zapoznaj się z plikami **README. txt** i **uszkodzeniami modelowania propagacji. PDF** w źródle C:\\\\IoTEdgeAndMlSample\\dane\\TurboFan, aby uzyskać szczegółowy opis danych.

* **Utils.py:** Zawiera zestaw funkcji narzędzia Python do pracy z danymi. Pierwszy Notes zawiera szczegółowy opis funkcji.

* **README.MD:** Plik Readme opisujący korzystanie z notesów.

Utwórz nowy projekt i przekaż pliki do notesu.

1. Wybierz pozycję **Moje projekty** na górnym pasku menu.

1. Wybierz pozycję **+ Nowy projekt**. Podaj nazwę i identyfikator. Nie ma potrzeby, aby projekt był publiczny lub miał plik Readme.

1. Wybierz pozycję **Przekaż** i wybierz opcję **z komputera**.

1. Wybierz pozycję **Wybierz pliki**.

1. Przejdź do **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Wybierz pozycję Wszystkie pliki, a następnie kliknij pozycję **Otwórz**.

1. Wybierz pozycję **Przekaż** , aby rozpocząć przekazywanie, a następnie wybierz pozycję **gotowe** po zakończeniu procesu.

## <a name="run-azure-notebooks"></a>Uruchom Azure Notebooks

Po utworzeniu projektu Uruchom w notesie **\_TurboFan regresję ipynb** .

1. Na stronie projekt TurboFan wybierz pozycję **01-turbofan\_regresja. ipynb**.

    ![Wybierz pierwszy Notes do uruchomienia](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Jeśli zostanie wyświetlony monit, wybierz jądro Python 3,6 z okna dialogowego i wybierz pozycję **Ustaw jądro**.

3. Jeśli Notes jest wymieniony jako **niezaufany**, kliknij widżet **niezaufany** w prawym górnym rogu notesu. Po wyświetleniu okna dialogowego wybierz pozycję **Ufaj**.

4. Postępuj zgodnie z instrukcjami w notesie.

    * `Ctrl + Enter` uruchamia komórkę.
    * `Shift + Enter` uruchamia komórkę i nawiguje do następnej komórki.
    * Gdy komórka jest uruchomiona, ma gwiazdkę między nawiasami kwadratowymi, takimi jak **[\*]** . Po zakończeniu gwiazdka zostanie zastąpiona liczbą i odpowiednie dane wyjściowe mogą pojawić się poniżej. Ponieważ komórki często kompilują się w pracy z poprzednimi, tylko jedna komórka może być uruchamiana w danym momencie.

5. Po zakończeniu pracy z **ipynbem 01-turbofan\_regresja** , Wróć do strony projektu.

6. Otwórz przystawkę **02-turbofan\_wdróż\_model. ipynb** i powtórz kroki opisane w tej sekcji, aby uruchomić drugi Notes.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto dwóch Jupyterych notesów uruchomionych w Azure Notebooks, aby użyć danych z urządzeń TurboFan do uczenia pozostałej klasyfikatora okresu użytkowania (pozostałego czasu eksploatacji) w celu zapisania klasyfikatora jako modelu, utworzenia obrazu kontenera oraz wdrożenia i przetestowania obrazu jako sieci Web SE rvice.

Przejdź do następnego artykułu, aby utworzyć urządzenie IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzenia IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
