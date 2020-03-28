---
title: 'Samouczek: Szkolenie i wdrażanie modelu — uczenie maszynowe w usłudze Azure IoT Edge'
description: W tym samouczku będzie szkolić model uczenia maszynowego przy użyciu usługi Azure Machine Learning, a następnie pakiet modelu jako obraz kontenera, który można wdrożyć jako moduł usługi Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57630b789233dd23e61398f445b434e4ba08b48e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80236020"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Samouczek: Szkolenie i wdrażanie modelu usługi Azure Machine Learning

> [!NOTE]
> Ten artykuł jest częścią serii samouczka na temat korzystania z usługi Azure Machine Learning w usłudze IoT Edge. Jeśli dotarłeś do tego artykułu bezpośrednio, zachęcamy do rozpoczęcia [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii, aby uzyskać najlepsze wyniki.

W tym artykule wykonujemy następujące zadania:

* Użyj notesów platformy Azure, aby trenować model uczenia maszynowego.
* Pakiet przeszkolony model jako obraz kontenera.
* Wdrażanie obrazu kontenera jako modułu usługi Azure IoT Edge.

Notesy platformy Azure korzystają z obszaru roboczego usługi Azure Machine Learning, podstawowych bloków używanych do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.

Kroki opisane w tym artykule mogą być zazwyczaj wykonywane przez analityków danych.

## <a name="set-up-azure-notebooks"></a>Konfigurowanie notesów platformy Azure

Używamy notesów platformy Azure do obsługi dwóch notesów Jupyter i plików pomocniczych. W tym miejscu tworzymy i konfigurujemy projekt notesów platformy Azure. Jeśli nie używano usługi Jupyter i/lub notesów platformy Azure, oto kilka dokumentów wprowadzających:

* **Szybki start:** [tworzenie notesu i udostępnianie go](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Samouczek:** [Tworzenie i uruchamianie notesu Jupytera za pomocą języka Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Korzystanie z notesów platformy Azure zapewnia spójne środowisko dla wykonywania.

> [!NOTE]
> Po skonfigurowaniu usługi Azure Notebooks można uzyskać dostęp z dowolnego komputera. Podczas instalacji należy użyć maszyny Wirtualnej rozwoju, która ma wszystkie pliki, które będą potrzebne.

### <a name="create-an-azure-notebooks-account"></a>Tworzenie konta notesów platformy Azure

Aby korzystać z notesów platformy Azure, należy utworzyć konto. Konta usługi Azure Notebook są niezależne od subskrypcji platformy Azure.

1. Przejdź do [notesów platformy Azure](https://notebooks.azure.com).

1. Kliknij **pozycję Zaloguj** się w prawym górnym rogu strony.

1. Zaloguj się za pomocą konta służbowego (usługi Azure Active Directory) lub konta osobistego (konto Microsoft).

1. Jeśli nie używano wcześniej notesów platformy Azure, zostanie wyświetlony monit o udzielenie dostępu dla aplikacji Notesy platformy Azure.

1. Utwórz identyfikator użytkownika dla notesów platformy Azure.

### <a name="upload-jupyter-notebook-files"></a>Prześlij pliki notesu Jupyter

Będziemy przekazywać przykładowe pliki notesu do nowego projektu notesów platformy Azure.

1. Na stronie użytkownika nowego konta wybierz pozycję **Moje projekty** z górnego paska menu.

1. Dodaj nowy projekt, wybierając **+** przycisk.

1. W oknie dialogowym **Tworzenie nowego projektu** podaj nazwę **projektu**. 

1. Pozostaw **publiczne** i **README** niezaznaczone, ponieważ nie ma potrzeby, aby projekt był publiczny lub miał readme.

1. Wybierz **pozycję Utwórz**.

1. Wybierz **pozycję Przekaż** (ikona strzałki w górę) i wybierz pozycję Z **komputera**.

1. Wybierz **pozycję Wybierz pliki**.

1. Przejdź do **pozycji C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Zaznacz wszystkie pliki na liście i kliknij przycisk **Otwórz**.

1. Zaznacz pole **Ufam zawartości tych plików.**

1. Wybierz **pozycję Przekaż,** aby rozpocząć przesyłanie, a następnie wybierz pozycję **Gotowe** po zakończeniu procesu.

### <a name="azure-notebook-files"></a>Pliki notesu platformy Azure

Przejrzyj pliki przekazane do projektu notesów platformy Azure. Działania w tej części samouczka obejmują dwa pliki notesu, które używają kilku plików pomocniczych.

* **01-turbowentylator\_regresja.ipynb:** Ten notes używa obszaru roboczego usługi uczenia maszynowego do tworzenia i uruchamiania eksperymentu uczenia maszynowego. Ogólnie rzecz biorąc, notes wykonuje następujące czynności:

  1. Pobiera dane z konta usługi Azure Storage, który został wygenerowany przez wiązki przewodów wiązki urządzeń.
  1. Eksploruje i przygotowuje dane, a następnie używa danych do szkolenia modelu klasyfikatora.
  1. Oceń model z eksperymentu przy użyciu\_testowego zestawu danych (Test FD003.txt).
  1. Publikuje najlepszy model klasyfikatora w obszarze roboczym usługi uczenie maszynowe.

* **02-turbowentylator\_wdrożyć\_model.ipynb:** Ten notes przyjmuje model utworzony w poprzednim notesie i używa go do utworzenia obrazu kontenera gotowego do wdrożenia na urządzeniu usługi Azure IoT Edge. Notes wykonuje następujące czynności:

  1. Tworzy skrypt oceniania dla modelu.
  1. Tworzy obraz kontenera przy użyciu modelu klasyfikatora, który został zapisany w obszarze roboczym usługi uczenie maszynowe.
  1. Wdraża obraz jako usługę sieci web w wystąpieniu kontenera platformy Azure.
  1. Używa usługi sieci web do sprawdzania poprawności modelu i obrazu pracy zgodnie z oczekiwaniami. Zweryfikowany obraz zostanie wdrożony na naszym urządzeniu usługi IoT Edge w części [tworzenia i wdrażania niestandardowych modułów usługi IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) w tym samouczku.

* **Test\_FD003.txt:** Ten plik zawiera dane, których użyjemy jako nasz zestaw testów podczas sprawdzania poprawności naszego przeszkolonego klasyfikatora. Postanowiliśmy wykorzystać dane testowe, zgodnie z pierwotnym konkursem, jako nasz zestaw testowy dla jego prostoty.

* **RUL\_FD003.txt:** Ten plik zawiera pozostały okres użytkowania (RUL) dla\_ostatniego cyklu każdego urządzenia w pliku Test FD003.txt. Szczegółowe wyjaśnienie danych można znaleźć w plikach readme.txt i\\\\Damage Propagation Modeling.pdf\\\\w plikach C: source IoTEdgeAndMlSample.

* **Utils.py:** Zawiera zestaw funkcji narzędzia Języka Python do pracy z danymi. Pierwszy notes zawiera szczegółowe wyjaśnienie funkcji.

* **README.md:** Readme opisujące korzystanie z notesów.  

## <a name="run-azure-notebooks"></a>Uruchamianie notesów platformy Azure

Teraz, gdy projekt jest tworzony, można uruchomić notesy. 

1. Na stronie projektu wybierz **01-turbofan\_regression.ipynb**.

    ![Wybierz pierwszy notes do uruchomienia](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Jeśli notes jest wymieniony jako **Nieu zaufany,** kliknij widżet **Nie zaufane** w prawym górnym rogu notesu. Po utworzeniu okna dialogowego wybierz pozycję **Zaufaj**.

1. Aby uzyskać najlepsze wyniki, przeczytaj dokumentację dla każdej komórki i uruchom ją indywidualnie. Wybierz **pozycję Uruchom** na pasku narzędzi. Później, znajdziesz to wskazane, aby uruchomić wiele komórek. Można pominąć ostrzeżenia dotyczące uaktualniania i wycofania.

    Gdy komórka jest uruchomiona, wyświetla gwiazdkę między nawiasami\*kwadratowymi ([ ]). Po zakończeniu operacji komórki gwiazdka jest zastępowana liczbą i mogą pojawić się odpowiednie dane wyjściowe. Komórki w notesie budują się sekwencyjnie i tylko jedna może być uruchomiona jednocześnie.

    Można również użyć opcji uruchamiania `Ctrl`  +  `Enter` z menu `Shift`  +  `Enter` **Komórka,** aby uruchomić komórkę i uruchomić komórkę i przejść do następnej komórki.

    > [!TIP]
    > W przypadku spójnych operacji na komórkach należy unikać uruchamiania tego samego notesu z wielu kart w przeglądarce.

1. W komórce, która jest zgodna z **instrukcjami Ustaw właściwości globalne,** zapisuj w wartościach subskrypcji platformy Azure, ustawień i zasobów. Następnie uruchom komórkę.

    ![Ustawianie właściwości globalnych w notesie](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. W komórce, która została przesuń do **szczegółów obszaru roboczego,** poszukaj łącza, które nakazuje zalogowanie się w celu uwierzytelnienia:

    ![Monit logowania o uwierzytelnieniu urządzenia](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Otwórz łącze i wprowadź określony kod. Ta procedura logowania uwierzytelnia notes Jupyter, aby uzyskać dostęp do zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Microsoft Azure.  

    ![Uwierzytelnij aplikację na potwierdzeniu urządzenia](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. W komórce **poprzedzającej Eksplorowanie wyników,** skopiuj wartość z identyfikatora uruchomienia i wklej ją dla identyfikatora uruchomienia w komórce, która następuje **Reconstitute run**.

   ![Kopiowanie identyfikatora uruchomienia między komórkami](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Uruchom pozostałe komórki w notesie.

1. Zapisz notes i wróć do strony projektu.

1. Otwórz **\_02-turbofan\_wdrożyć model.ipynb** i uruchomić każdą komórkę. Aby uwierzytelnić się w komórce następującej po **konfigurowaniu obszaru roboczego,** należy się zalogować.

1. Zapisz notes i wróć do strony projektu.

### <a name="verify-success"></a>Weryfikowanie sukcesu

Aby sprawdzić, czy notesy zostały pomyślnie ukończone, sprawdź, czy utworzono kilka elementów.

1. Na stronie projektu notesów platformy Azure wybierz pozycję **Pokaż ukryte elementy,** aby wyświetlić nazwy elementów, które rozpoczynają się od kropki.

1. Sprawdź, czy utworzono następujące pliki:

    | Plik | Opis |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Plik konfiguracji używany do tworzenia obszaru roboczego usługi Azure Machine Learning. |
    | ./aml_config/model_config.json | Plik konfiguracyjny, który będzie nam potrzebny do wdrożenia modelu w obszarze roboczym **turbofanDemo** Machine Learning na platformie Azure. |
    | myenv.yml| Zawiera informacje o zależnościach dla wdrożonego modelu usługi Machine Learning.|

1. Sprawdź, czy utworzono następujące zasoby platformy Azure. Niektóre nazwy zasobów są dołączane z losowymi znakami.

    | Zasób platformy Azure | Nazwa |
    | --- | --- |
    | Obszar roboczy uczenia maszynowego | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Aplikacje Insights | turbofaninsightxxxxxxxx |
    | Usługa Key Vault | turbofankeyvaultbxxxxxxxx |
    | Magazyn | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Debugowanie

Instrukcje języka Python można wstawić do notesu `print()` do debugowania, takie jak polecenie, aby wyświetlić wartości. Jeśli widzisz zmienne lub obiekty, które nie są zdefiniowane, uruchom komórki, w których są one najpierw zadeklarowane lub skomunikowane.

Może być konieczne usunięcie wcześniej utworzonych plików i zasobów platformy Azure, jeśli trzeba ponownie ponawiać notesy.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyliśmy dwóch notesów Jupyter działających w notesach platformy Azure do używania danych z urządzeń turbowentylatorowych do szkolenia klasyfikatora pozostałego okresu użytkowania (RUL), zapisywania klasyfikatora jako modelu, tworzenia obrazu kontenera oraz wdrażania i testowania obrazu jako sieci Web Usługi.

Przejdź do następnego artykułu, aby utworzyć urządzenie IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzenia IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
