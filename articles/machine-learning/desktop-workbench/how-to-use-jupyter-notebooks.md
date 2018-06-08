---
title: Jak korzystać z notesów Jupyter w Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Przewodnik dla za pomocą funkcji Azure Machine Learning Workbench notesu Jupyter
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 07076a1d551d4dfb513deabaedb650a3262bbeab
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830993"
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Użyj notesów Jupyter w Workbench uczenie maszynowe Azure

Azure Machine Learning Workbench obsługuje eksperymenty nauki interaktywnych danych przy użyciu integracji z notesów Jupyter. W tym artykule opisano sposób efektywnie wykorzystać tę funkcję, aby zwiększyć szybkość i jakość eksperymenty nauki Twoje dane interakcyjne.

## <a name="prerequisites"></a>Wymagania wstępne
- [Tworzenie konta usługi Azure Machine Learning i instalacja usługi Azure Machine Learning Workbench](../service/quickstart-installation.md).
- Należy zapoznać się z [notesu Jupyter](http://jupyter.org/). W tym artykule nie jest o poznanie Jupyter.

## <a name="jupyter-notebook-architecture"></a>Architektura notesu Jupyter
Na wysokim poziomie architektura notesu Jupyter zawiera trzy składniki. Każdy można uruchomić w środowiskach różnych obliczeniowych:

- **Klient**: otrzymuje dane wejściowe użytkownika i wyświetla renderowania danych wyjściowych.
- **Serwer**: serwer sieci Web obsługujący notesu (pliki .ipynb).
- **Jądra**: środowisko uruchomieniowe, w których wykonanie notesu sytuacji komórek.

Aby uzyskać więcej informacji można znaleźć w oficjalnej [dokumentacji Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Poniższy diagram przedstawia sposób mapowania tego klienta, serwera i jądra architektury składników w usłudze Azure Machine Learning:

![Architektura notesu Jupyter](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Jądra w notesach Azure Machine Learning Workbench
Obliczeniowe elementy docelowe w i dostępne, definiując konfiguracje wykonywania różnych jądra w konsoli usługi Azure Machine Learning Workbench `aml_config` folder w projekcie. Dodanie nowego miejsca docelowego obliczeń wysyłając `az ml computetarget attach` polecenie jest odpowiednikiem Dodawanie nowych jądra.

>[!NOTE]
>Przegląd [Konfigurowanie Azure Machine Learning eksperymenty usługi](experimentation-service-configuration.md) więcej informacji na temat uruchamiania konfiguracji i obliczeniowe elementy docelowe.

### <a name="kernel-naming-convention"></a>Konwencja nazewnictwa jądra
Azure Machine Learning Workbench generuje niestandardowych jądra Jupyter. Te jądra są nazywane  *\<Nazwa projektu > \<Uruchom Nazwa konfiguracji >*. Na przykład, jeśli masz konfigurację uruchomieniową o nazwie _docker python_ w projekcie o nazwie _myIris_, Azure Machine Learning udostępnianych jądra o nazwie *myIris docker-python.* Ustaw uruchomionych jądra notesu Jupyter **jądra** menu w **jądra zmiany** podmenu. Nazwa uruchomionych jądra pojawia się po prawej stronie paska menu.
 
Obecnie usługa Azure Machine Learning Workbench obsługuje następujące typy jądra.

### <a name="local-python-kernel"></a>Local Python kernel
To jądra Python obsługuje wykonywanie na maszynach lokalnych. Jest zintegrowany z obsługą Azure Machine Learning Uruchom historii. Nazwa jądra jest zwykle *my_project_name lokalnego.*

>[!NOTE]
>Nie należy używać jądra 3 języka Python. Jest domyślnie dostępne przez Jupyter jądra autonomicznej i nie jest zintegrowane z funkcjami usługi Azure Machine Learning. Na przykład `%azureml` Jupyter magic zwracają błędy "nie została odnaleziona". 

### <a name="python-kernel-in-docker-local-or-remote"></a>Python jądra w Docker (lokalne i zdalne)
Ta jądra Python działa w kontenerze Docker na komputerze lokalnym lub zdalnym maszyny wirtualnej systemu Linux (VM). Nazwa jądra jest zwykle *my_project docker.* Skojarzony `docker.runconfig` plik ma `Framework` pole `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Jądro PySpark w Docker (lokalne i zdalne)
Ta jądra PySpark wykonuje skrypty w kontekście Spark uruchomionych w kontenerze Docker na komputerze lokalnym lub zdalnym maszyny wirtualnej systemu Linux. Nazwa jądra jest zwykle *my_project docker.* Skojarzony `docker.runconfig` plik ma `Framework` pole `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Jądro PySpark w klaster Azure HDInsight
Działa to jądra w zdalny klaster Azure HDInsight dołączony jako cel obliczeniowe dla projektu. Nazwa jądra jest zwykle *my_project my_hdi.* 

>[!IMPORTANT]
>W `.compute` pliku HDI obliczeniowe docelowej, musisz zmienić `yarnDeployMode` do `client` (wartość domyślna to `cluster`) do użycia tego jądra. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Uruchom serwer Jupyter z usługi Azure Machine Learning Workbench
Azure Machine Learning Workbench dostępne notesów za pośrednictwem **notesów** kartę. _Klasyfikowania Iris_ zawiera przykładowy projekt `iris.ipynb` przykładowy notes.

![Karta notesów](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Po otwarciu Notes w Azure Machine Learning Workbench pojawi się w osobnej karcie dokumentu w **trybu podglądu**. To jest widoku tylko do odczytu, która nie wymaga uruchomiony serwer Jupyter i jądra.

![Podgląd notesu](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Wybieranie **uruchamianie serwera notesu** przycisk uruchamiania serwera Jupyter i zmienia notesu do **trybu edycji**. Znanego interfejsu użytkownika notesu Jupyter wydaje się być osadzony w Workbench. Teraz możesz ustawić jądra z **jądra** menu i uruchomić sesję interakcyjne notesu. 

>[!NOTE]
>Z jądra nie lokalnego może zająć minutę lub dwie można uruchomić, jeśli jest używany po raz pierwszy. Możesz wykonać `az ml experiment prepare` polecenie w oknie interfejsu wiersza polecenia, aby przygotować docelowy obliczeniowe dzięki jądra rozpoczyna się znacznie szybciej, po przygotowaniu jest element docelowy obliczeń.

![Tryb edycji](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Jest to całkowicie interakcyjny środowisko notesu Jupyter. Wszystkie operacje regularne notesu i skróty klawiaturowe, które są obsługiwane z tego okna, z wyjątkiem niektórych operacji plików, które może odbywać się za pośrednictwem Workbench **notesów** kartę i **pliku** kartę.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Uruchom serwer Jupyter z wiersza polecenia
Można również uruchomić sesję notesu wysyłając `az ml notebook start` w oknie wiersza polecenia:
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
Przeglądarka domyślna automatycznie otwiera z serwerem Jupyter wskazaniu katalogu macierzystego projektu. Aby otworzyć inne okna przeglądarki lokalnie, można użyć adresu URL i token wyświetlane w oknie interfejsu wiersza polecenia. 

![pulpit nawigacyjny projektu](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Można teraz wybrać `.ipynb` pliku notesu go otworzyć, ustaw jądra (Jeśli nie została ustawiona) i uruchomić sesję interaktywnego.

![pulpit nawigacyjny projektu](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Umożliwia zarządzanie eksperymenty poleceń magicznych

Można użyć [Magiczna polecenia](http://ipython.readthedocs.io/en/stable/interactive/magics.html) w komórkach z notesu śledzenie historii wykonywania i zapisywanie danych wyjściowych, takich jak modele ani zestawów danych.

Aby śledzić poszczególne notesu komórki działa, należy użyć `%azureml history on` Magiczna polecenia. Po włączeniu historii uruchomienia każdej komórki pojawia się jako wpis w historii uruchamiania:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Aby wyłączyć komórki uruchomić śledzenia, należy użyć `%azureml history off` Magiczna polecenia.

Można użyć `%azureml upload` Magiczna polecenie, aby zapisać pliki modelu i dane z Twojego uruchamiania. Zapisane obiekty są wyświetlane jako dane wyjściowe w widoku Historia uruchomień:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Dane wyjściowe musi zostać zapisany w folderze o nazwie *danych wyjściowych.*

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się, jak użyć notesu Jupyter, zobacz [Jupyter oficjalnej dokumentacji](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Aby lepiej zrozumieć środowiska wykonawczego eksperymenty uczenia maszynowego Azure, zobacz [Konfigurowanie Azure Machine Learning eksperymenty usługi](experimentation-service-configuration.md).

