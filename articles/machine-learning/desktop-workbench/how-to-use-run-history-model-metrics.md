---
title: Jak używać Uruchom historii i metryki modelu w systemie Azure uczenia maszynowego Workbench | Dokumentacja firmy Microsoft
description: Przewodnik dla historii uruchamiania i metryki modelu funkcje usługi Azure Machine Learning Workbench za pomocą
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: dddf6af506269c7f22cea69e39b55effa2f14d63
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Sposób użycia Historia uruchomień i metryki modelu w konsoli usługi Azure Machine Learning Workbench

Azure Machine Learning Workbench obsługuje eksperymenty analizy danych za pośrednictwem jego **Uruchom historii** i **metryki modelu** funkcji.
**Historia uruchomień** umożliwia śledzenie danych wyjściowych komputera uczenia eksperymenty, a następnie umożliwia filtrowanie oraz porównanie ich wyników.
**Model metryki** mogą być rejestrowane z dowolnego punktu skrypty, śledzenie, niezależnie od wartości są najważniejsze w eksperymentów analizy danych.
W tym artykule opisano sposób wprowadzania efektywne korzystanie z tych funkcji, aby zwiększyć szybkość i jakość Twojej eksperymenty analizy danych.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych ten przewodnik, musisz:
* [Utwórz i zainstaluj usługi Azure Machine Learning](../service/quickstart-installation.md)
- [Tworzenie projektu](../service/quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Przegląd interfejsu API usługi Azure ML rejestrowania
[Rejestrowania interfejsu API uczenia Maszynowego Azure](reference-logging-api.md) jest dostępny za pośrednictwem **azureml.logging** modułu w języku Python (który jest instalowany z Workbench uczenia Maszynowego Azure.) Po zaimportowaniu tego modułu, można użyć **get_azureml_logger** metody tworzenia wystąpienia **rejestratora** obiektu.
Następnie należy użyć Rejestratora **dziennika** metodę, aby przechowywać pary klucz wartość utworzonego przez skrypty języka Python.
Obecnie metryki model rejestrowania skalarnych i typów list są obsługiwane jak pokazano.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Jest łatwe w użyciu rejestratora w projektach usługi Azure ML Workbench, a w tym artykule przedstawiono sposób zrobić.

## <a name="create-a-project-in-azure-ml-workbench"></a>Tworzenie projektu na Workbench uczenia Maszynowego Azure
Jeśli nie masz już projekt, możesz utworzyć jedną z [Utwórz i zainstaluj szybkiego startu](../service/quickstart-installation.md) z **pulpit nawigacyjny projektu**, możesz otworzyć **iris_sklearn.py** skryptu () jak pokazano).

![Uzyskiwanie dostępu do skryptu z karty pliki](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Ten skrypt można użyć jako przewodnik dla implementacji oczekiwanego metryki modelu logowanie uczenie Maszynowe Azure.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parametryzacja i dziennika modelu metryki ze skryptu
W **iris_sklearn.py** skryptu oczekiwanego wzorca do zaimportowania i konstrukcja rejestratora w języku Python zmniejsza się w następujących wierszach kodu.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Po utworzeniu można wywołać **dziennika** metoda ze wszystkimi para nazwa/wartość.

Po zakończeniu tworzenia często warto parametryzacja skryptów, dzięki czemu można przekazać wartości w za pomocą wiersza polecenia.
Poniższy przykład przedstawia sposób akceptowania parametry wiersza polecenia (jeśli istnieją), przy użyciu standardowych bibliotek języka Python.
Ten skrypt wymaga jednego parametru dla szybkości uregulowania (*reg*) używany do dopasowania modelu klasyfikacji w celu zwiększenia *dokładność* bez overfitting.
Te zmienne są następnie rejestrowane jako *szybkość uregulowania* i *dokładność* , dzięki czemu można łatwo zidentyfikować modelu o optymalnych wyników.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Biorąc te kroki w skryptach włączyć je w celu udostępnienia optymalne wykorzystanie **Uruchom historii**.

## <a name="launch-runs-from-project-dashboard"></a>Pulpit nawigacyjny projektu jest uruchamiane uruchamiania
Wracając do **pulpit nawigacyjny projektu**, możesz uruchomić **śledzonych Uruchom** wybierając **iris_sklearn.py** skryptu i wprowadzając **uregulowania szybkości**  parametru w **argumenty** polu edycji.

![wprowadzanie parametrów i uruchamianie przebiegów](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Ponieważ uruchamianie przebiegów śledzonych nie blokuje Workbench uczenia Maszynowego Azure, można uruchomić kilka równolegle.
Stan każdego śledzonego przebiegu jest widoczny w **panelu zadań** jak pokazano.

![śledzenie jest uruchamiany w panelu zadań](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Umożliwia to optymalne wykorzystanie zasobów bez konieczności uruchamiania kolei każdego zadania.

## <a name="view-results-in-run-history"></a>Wyświetl wyniki w historii wykonywania
Postęp i wyniki przebiegów śledzonych są dostępne do analizy w usłudze Azure ML Workbench **Uruchom historii**.
**Historia uruchomień** udostępnia trzy różne widoki:
- Pulpit nawigacyjny
- Szczegóły
- Porównanie

**Pulpitu nawigacyjnego** widok przedstawia dane przez wszystkie elementy z danego skryptu renderowane w formularzach zarówno graficznego i tabelarycznych.
**Szczegóły** widoku są wyświetlane wszystkie dane zostały wygenerowane z określonego działania danego skryptu, między innymi zarejestrowane metryki i pliki wyjściowe (takie jak renderowane geograficzne.) **Porównanie** widok wyników przebiegów dwóch lub trzech być wyświetlane side-by-side, w także w tym rejestrowane metryki plików wyjściowych.

Między osiem śledzone uruchomień **iris_sklearn.py**, wartości **szybkość uregulowania** parametru i **dokładność** wynik były rejestrowane ilustrujący sposób użycia Uruchom Widoki historii.

### <a name="run-history-dashboard"></a>Historia uruchomień pulpitu nawigacyjnego
Wyniki wszystkich uruchomień osiem są widoczne w **uruchomienia pulpitu nawigacyjnego historii**.
Jako **iris_sklearn.py** dzienniki *szybkość uregulowania* i *dokładność*, **uruchomienia pulpitu nawigacyjnego historii** Wyświetla wykresy te wartości przez domyślne.

![Historia uruchomień pulpitu nawigacyjnego](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

**Uruchomienia pulpitu nawigacyjnego historii** można dostosować tak, aby wartości zarejestrowane również występować w siatce.  Kliknięcie przycisku **dostosować** Wyświetla ikonę **dostosowanie widoku listy** dialogu, jak pokazano.

![Dostosowywanie Historia uruchomień pulpitu nawigacyjnego siatki](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Wszelkie zarejestrowane podczas śledzonych uruchamia wartości są dostępne do wyświetlania i wybierając **szybkość uregulowania** i **dokładność** dodaje je do siatki.

![zarejestrowane wartości w siatce dostosowane](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Jest łatwe do odnalezienia interesujące działa, ustawiając kursor nad punktów schematach organizacyjnych.  W takim przypadku Uruchom 7 zwróciło dobrą dokładność połączeniu z niskim czasu trwania.

![Znajdowanie interesujące Uruchom](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Kliknięcie punktu skojarzone z Uruchom 7 dowolnego wykresu lub łącze do uruchamiania 7 w Wyświetla siatki **Uruchom szczegóły historii**.

### <a name="run-history-details"></a>Szczegóły historii uruchomienia
W tym widoku są wyświetlane pełnych wyników 7 Uruchom oraz wszelkie artefakty utworzonego przez uruchomienie 7.

![Szczegóły historii uruchomienia](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

**Uruchom szczegóły historii** widoku oferuje także możliwość **Pobierz** wszystkie zapisywane pliki **. / generuje** folder (tych plików obsługiwanych przez usługi Azure ML Workbench Magazyn w chmurze Uruchom historii, który podlega innym artykule.)

Na koniec **Uruchom szczegóły historii** umożliwia przywracanie projektu stanu w czasie tego działania.
Kliknięcie przycisku **przywrócić** przycisk wyświetla dialogu potwierdzenie, jak pokazano.

![Potwierdzenie przywracania Uruchom](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

W przypadku potwierdzenia, pliki mogą zostać zastąpione lub usunięte, więc ostrożnie korzystaj z tej funkcji.

### <a name="run-history-comparison"></a>Uruchom porównania historii
Wybieranie dwóch lub trzech działa w **uruchomienia pulpitu nawigacyjnego historii** i klikając **porównania** oferuje możesz **Uruchom porównania historii** widoku.
Alternatywnie, klikając przycisk **porównania** i wybierając polecenie do uruchomienia w ramach **Uruchom szczegóły historii** widok również oferuje możesz **Uruchom porównania historii** widoku.
W obu przypadkach **Uruchom porównania historii** widoku umożliwia Zobacz wyniki zarejestrowane i artefakty dwóch lub trzech uruchomień obok siebie.

![Uruchom porównania historii](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Ten widok jest szczególnie przydatna w przypadku porównywania powierzchni, ale ogólnie rzecz biorąc, wszelkie właściwości uruchamia można porównać tutaj.

### <a name="command-line-interface"></a>Command Line Interface
Azure Machine Learning Workbench umożliwia również dostęp do historii Uruchom za pomocą jego **interfejsu wiersza polecenia**.
Aby uzyskać dostęp do **interfejsu wiersza polecenia**, kliknij przycisk **Otwórz okno wiersza polecenia** menu, jak pokazano.

![Otwórz wiersz polecenia](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Dostępne dla historii uruchamiania polecenia są dostępne za pośrednictwem `az ml history`, przy pomocy online, które są dostępne przez dodanie `-h` flagi.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Polecenia te zapewniają te same funkcje i zwracać tych samych danych, wyświetlany **Uruchom widoków historii**.
Na przykład wyniki ostatniego uruchomienia mogą być wyświetlane jako obiekt JSON.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
Ponadto wszystkie elementy na liście można wyświetlane w formacie tabelarycznym.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
**Interfejsu wiersza polecenia** jest alternatywnej ścieżki do potęgi Azure Machine Learning Workbench.

## <a name="next-steps"></a>Następne kroki
Te funkcje są dostępne ułatwić proces eksperymenty analizy danych.
Mamy nadzieję, ich użyteczne wyszukiwanie i znacznie o wyrażenie opinii.
Jest to po prostu naszych początkowej implementacji i mamy wiele ulepszeń planowane.
Mamy nadzieję na stale dostarczeniem ich Azure Machine Learning Workbench. 
