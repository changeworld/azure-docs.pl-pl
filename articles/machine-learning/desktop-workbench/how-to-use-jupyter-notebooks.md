---
title: Jak używać aplikacji Jupyter Notebooks w usłudze Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące korzystania z funkcji notesu Jupyter w usłudze Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 712cdaa65487620b2f8af4a0ad57c01c24b9a965
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649963"
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Korzystanie z notesów Jupyter w usłudze Azure Machine Learning Workbench

Usługa Azure Machine Learning Workbench obsługuje eksperymentowania do nauki o danych za pomocą integracji z notesów programu Jupyter. W tym artykule opisano sposób efektywny używać tej funkcji w celu zwiększenia szybkości i jakości usługi eksperymentowanie do nauki o danych.

## <a name="prerequisites"></a>Wymagania wstępne
- [Tworzenie konta usługi Azure Machine Learning i zainstalować aplikację Azure Machine Learning Workbench](../service/quickstart-installation.md).
- Należy zapoznać się z [notesu programu Jupyter](http://jupyter.org/). Ten artykuł nie jest temat nauki służące Jupyter.

## <a name="jupyter-notebook-architecture"></a>Architektura notesu programu Jupyter
Na wysokim poziomie notesu programu Jupyter architektura obejmuje trzy składniki. Każdy można uruchomić w środowiskach obliczeniowej:

- **Klient**: otrzymuje dane wejściowe użytkownika i wyświetla renderowania danych wyjściowych.
- **Serwer**: serwer sieci Web, który jest hostem notesu (pliki .ipynb).
- **Jądra**: środowiska uruchomieniowego, w których wykonanie notesu się dzieje w komórkach.

Aby uzyskać więcej informacji można znaleźć w oficjalnej [dokumentacji Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Poniższy diagram przedstawia sposób mapowania tej architektury jądra, serwerów i klientów ze składnikami w usłudze Azure Machine Learning:

![Architektura notesu programu Jupyter](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Jądra w notesach Azure Machine Learning Workbench
Można uzyskać dostęp do różnych jądra w programie Azure Machine Learning Workbench, definiowanie konfiguracje wykonywania i obliczeniowych elementów docelowych w `aml_config` folder w projekcie. Dodawanie nowemu celowi obliczenia wydając `az ml computetarget attach` polecenie jest odpowiednikiem dodawania nowego jądra.

>[!NOTE]
>Przegląd [konfigurowania Azure usługi Machine Learning eksperymentowania](experimentation-service-configuration.md) szczegółowe informacje na temat konfiguracji uruchamiania i celów obliczeń.

### <a name="kernel-naming-convention"></a>Konwencja nazewnictwa jądra
Usługa Azure Machine Learning Workbench generuje niestandardowy jądra programu Jupyter. Te jądra są nazywane  *\<Nazwa projektu > \<Uruchom nazwy konfiguracji >*. Na przykład, jeśli masz konfigurację uruchomieniową o nazwie _docker-python_ w projekcie o nazwie _myIris_, Azure Machine Learning udostępnia jądra o nazwie *myIris docker-python.* Ustaw działającego jądra w notesie Jupyter **jądra** menu w **jądra zmiany** podmenu. Nazwa działającego jądra pojawia się na końcu z prawej strony paska menu.
 
Obecnie usługa Azure Machine Learning Workbench obsługuje następujące typy jądra.

### <a name="local-python-kernel"></a>Local Python kernel
To jądro Python obsługuje wykonywanie na komputerach lokalnych. Jest zintegrowany z obsługą historii uruchamiania usługi Azure Machine Learning. Nazwa jądra jest zwykle *my_project_name lokalnego.*

>[!NOTE]
>Nie należy używać jądra Python 3. Jest jądra autonomicznych, dostarczone przez Jupyter domyślnie i nie jest zintegrowane z funkcjami usługi Azure Machine Learning. Na przykład `%azureml` Jupyter magic zwracają błędy "nie znaleziono". 

### <a name="python-kernel-in-docker-local-or-remote"></a>Jądra języka Python na platformie Docker (lokalne i zdalne)
Ten jądra Python jest uruchamiany w kontenerze platformy Docker na komputerze lokalnym lub na zdalnej maszynie wirtualnej systemu Linux (VM). Nazwa jądra jest zwykle *my_project platformy docker.* Skojarzone `docker.runconfig` plik ma `Framework` pola `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Jądra PySpark na platformie Docker (lokalne i zdalne)
Ta jądra PySpark wykonuje skrypty w kontekście usługi Spark uruchomionej w kontenerze platformy Docker, na komputerze lokalnym lub na zdalnej maszynie Wirtualnej z systemem Linux. Nazwa jądra jest zwykle *my_project platformy docker.* Skojarzone `docker.runconfig` plik ma `Framework` pola `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Jądra PySpark w klastrze usługi Azure HDInsight
Ta jądra jest uruchamiany w zdalny klaster usługi Azure HDInsight, przypisana jako obliczeniowego elementu docelowego w projekcie. Nazwa jądra jest zwykle *my_project my_hdi.* 

>[!IMPORTANT]
>W `.compute` pliku HDI obliczeniowego elementu docelowego, musisz zmienić `yarnDeployMode` pole `client` (wartość domyślna to `cluster`) do użycia tego jądra. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Uruchamiania serwera programu Jupyter, z usługi Azure Machine Learning Workbench
Z usługi Azure Machine Learning Workbench, można uzyskać dostęp do notesów za pośrednictwem **notesów** kartę. _Klasyfikowanie irysów_ przykładowy projekt zawiera `iris.ipynb` przykładowy notes.

![Karta notesów](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Po otwarciu Notes w usłudze Azure Machine Learning Workbench pojawi się w osobnej karcie dokumentu w **tryb podglądu**. To jest widok tylko do odczytu, która nie wymaga używania serwera programu Jupyter i jądra.

![Notes (wersja zapoznawcza)](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Wybieranie **uruchom serwer** przycisk uruchamia serwer Jupyter i przełącza Notes w **tryb edycji**. Znany interfejs użytkownika notesu programu Jupyter, pojawi się osadzony w aplikacji Workbench. Można teraz ustawić jądra z **jądra** menu i uruchomić sesję notesu interakcyjnego. 

>[!NOTE]
>Za pomocą jądra inną niż lokalna może zająć minutę lub dwie do uruchomienia, jeśli jest używany po raz pierwszy. Można wykonać `az ml experiment prepare` polecenia z okna interfejsu wiersza polecenia, aby przygotować obliczeniowego elementu docelowego, aby znacznie szybciej uruchamianiu jądra po obliczeniowego elementu docelowego jest gotowa.

![Tryb edycji](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Jest to w pełni interaktywne środowisko notesu programu Jupyter. Wszystkie operacje regularne Notes i skróty klawiaturowe, które są obsługiwane z tego okna, z wyjątkiem niektórych operacji na plikach, które może odbywać się za pośrednictwem aplikacji Workbench **notesów** kartę i **pliku** kartę.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Uruchamiania serwera programu Jupyter, z poziomu wiersza polecenia
Możesz także uruchomić sesję Notes, wysyłając `az ml notebook start` z okna wiersza polecenia:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Twoja przeglądarka domyślna automatycznie otwiera się z serwerem programu Jupyter, wskazując do katalogu głównego projektu. Aby otworzyć inne okna przeglądarki, lokalnie, można użyć adresu URL i tokenu wyświetlane w oknie interfejsu wiersza polecenia. 

![Pulpit nawigacyjny projektu](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Teraz możesz wybrać `.ipynb` pliku notesu go otworzyć, ustaw jądra (jeśli go nie ustawiono) i uruchomić sesję interaktywne.

![Pulpit nawigacyjny projektu](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Użyj magic polecenia do zarządzania eksperymentami

Możesz użyć [polecenia magicznego](http://ipython.readthedocs.io/en/stable/interactive/magics.html) w komórkach usługi Notes, aby śledzić historię wykonywania i zapisać dane wyjściowe, takie jak modele lub zestawów danych.

Aby śledzić notesu poszczególne komórki przebiegów, należy użyć `%azureml history on` magic polecenia. Po włączeniu historii Każde uruchomienie komórce pojawi się jako wpis w historii uruchamiania:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Aby wyłączyć komórki uruchomić śledzenia, należy użyć `%azureml history off` magic polecenia.

Możesz użyć `%azureml upload` magic polecenie, aby zapisać model i pliki danych z przebieg. Zapisane obiekty są wyświetlane jako dane wyjściowe w widoku historii uruchamiania:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Dane wyjściowe muszą zostać zapisane w folderze o nazwie *danych wyjściowych.*

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się, jak użyć notesu programu Jupyter, zobacz [Jupyter oficjalną dokumentacją](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Aby lepiej zrozumieć środowisko wykonawcze eksperymentowanie w usłudze Azure Machine Learning, zobacz [konfigurowania Azure usługi Machine Learning eksperymentowania](experimentation-service-configuration.md).

