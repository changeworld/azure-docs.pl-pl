---
title: W jaki sposób używać przebiegu historii i metryk modelu na platformie Azure w usłudze Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Przewodnik dla przy użyciu funkcji Azure Machine Learning Workbench w historii uruchamiania i metryk modelu
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 52b02460d444464211fc74c8982379424abebb5c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965521"
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Sposób użycia historię uruchomień i metryk modelu w aplikacji Azure Machine Learning Workbench

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Usługa Azure Machine Learning Workbench obsługuje eksperymentowania do nauki o danych za pośrednictwem jego **historii uruchamiania** i **metryk modelu** funkcji.
**Historia uruchamiania** udostępnia środki do śledzenia danych wyjściowych z eksperymentów uczenia maszynowego, a następnie umożliwia filtrowanie oraz porównanie ich wyników.
**Model metryk** mogą być rejestrowane z dowolnego punktu w skryptach, śledzenie, niezależnie od wartości są dla Ciebie najważniejsze w eksperymenty analizy danych.
W tym artykule opisano sposób efektywny używać tych funkcji, aby zwiększyć szybkość i jakość usługi eksperymentowanie do nauki o danych.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, musisz:
* [Tworzenie i instalowanie usługi Azure Machine Learning](quickstart-installation.md)
- [Tworzenie projektu](quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Omówienie interfejsu API rejestrowania uczenie Maszynowe systemu Azure
[Rejestrowanie interfejsu API uczenia Maszynowego Azure](reference-logging-api.md) jest dostępny za pośrednictwem **azureml.logging** modułu w języku Python, (który jest instalowany z aplikacji Azure ML Workbench.) Po zaimportowaniu tego modułu, można użyć **get_azureml_logger** metodę, aby utworzyć wystąpienie **rejestratora** obiektu.
Następnie należy użyć Rejestratora **dziennika** metodę, aby przechowywać pary klucz/wartość utworzony przez skryptów w języku Python.
Obecnie rejestrowanie metryki modelu skalarnych i typów list są obsługiwane jak pokazano.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Można łatwo użyć rejestratora w projektach aplikacji Azure ML Workbench, a w tym artykule dowiesz się, jak to zrobić.

## <a name="create-a-project-in-azure-ml-workbench"></a>Utwórz projekt w aplikacji Azure ML Workbench
Jeśli nie masz jeszcze projektu, możesz utworzyć je z [tworzenie i instalowanie Szybki Start](quickstart-installation.md) z **pulpit nawigacyjny projektu**, możesz otworzyć **iris_sklearn.py** skryptu () jak pokazano).

![Uzyskiwanie dostępu do skryptu z karty pliki](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Ten skrypt można użyć jako wskazówki oczekiwanego implementacji modelu metryki rejestrowania w usłudze Azure ML.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parametryzacja i rejestrowanie metryki modelu ze skryptu
W **iris_sklearn.py** skryptu, oczekiwany wzorzec do zaimportowania i konstrukcji rejestratora w języku Python można ograniczyć do następujących wierszy kodu.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Po utworzeniu możesz wywołać **dziennika** metody z dowolnym pary nazwa/wartość.

Po zakończeniu tworzenia jest często przydatne próby parametryzacji skryptów, tak aby wartości mogą być przekazywane za pośrednictwem wiersza polecenia.
Poniższy przykład pokazuje, jak akceptuje parametry wiersza polecenia (jeśli istnieją), przy użyciu standardowych bibliotek języka Python.
Ten skrypt przyjmuje jeden parametr dla współczynnika uregulowania (*reg*) używany do dopasowania model klasyfikacji w ramach działań zmierzających do zwiększenia *dokładność* bez overfitting.
Te zmienne są następnie rejestrowane jako *współczynnika uregulowania* i *dokładność* , dzięki czemu można łatwo zidentyfikować modelu przy użyciu optymalnych wyników.

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

Wykonanie tych czynności w skryptach włączyć je w celu optymalnego wykorzystania **historii uruchamiania**.

## <a name="launch-runs-from-project-dashboard"></a>Uruchamiania jest uruchamiane z poziomu pulpitu nawigacyjnego projektu
Wracając do **pulpit nawigacyjny projektu**, możesz uruchomić **wykonywania śledzonego** , wybierając **iris_sklearn.py** skryptu i wprowadzając **współczynnika uregulowania**  parametru w **argumenty** pole edycji.

![wprowadzanie parametrów i uruchamianie przebiegów](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Ponieważ uruchamianie przebiegów śledzonych nie są blokowane w aplikacji Azure ML Workbench, niektóre można uruchamiać równolegle.
Stan każdego uruchomienia śledzonych jest widoczny w **panelu zadań** jak pokazano.

![śledzenie jest uruchamiany w panelu zadania](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Umożliwia to optymalne wykorzystanie zasobów bez konieczności każde zadanie do uruchomienia w seryjny.

## <a name="view-results-in-run-history"></a>Wyświetl wyniki w historii uruchamiania
Postęp i wyniki śledzonych przebiegi są dostępne do analizy w usłudze Azure ML Workbench **historii uruchamiania**.
**Historia uruchamiania** udostępnia trzy różne widoki:
- Pulpit nawigacyjny
- Szczegóły
- porównanie

**Pulpit nawigacyjny** widok przedstawia dane we wszystkich przebiegach aktualizacji wszystkich danego skryptu renderowane w formularzach zarówno graficzny, jak i tabelarycznym.
**Szczegóły** widoku są wyświetlane wszystkie dane uzyskane z określonego przebiegu danego skryptu, łącznie z zarejestrowanych metryk i pliki wyjściowe (takie jak renderowania, które.) **Porównania** widok wyników dwa lub trzy uruchomienia, być wyświetlane side-by-side, w dotyczy to również zarejestrowanych metryk plików wyjściowych.

W ośmiu śledzone uruchomień **iris_sklearn.py**, wartości **współczynnika uregulowania** parametru i **dokładność** wynik zostały zarejestrowane do ilustrują sposób korzystania z przebiegu Widoki historii.

### <a name="run-history-dashboard"></a>Pulpit nawigacyjny historii uruchamiania
Wyniki wszystkich przebiegów osiem są widoczne w **pulpit nawigacyjny historii uruchamiania**.
Jako **iris_sklearn.py** dzienniki *współczynnika uregulowania* i *dokładność*, **pulpit nawigacyjny historii uruchamiania** Wyświetla wykresy dla tych wartości, Domyślnie.

![pulpit nawigacyjny historii uruchamiania](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

**Pulpit nawigacyjny historii uruchamiania** można dostosować tak, aby rejestrowane wartości również są wyświetlane w siatce.  Klikając **dostosować** wyświetlona ikona **dostosowanie widoku listy** dialogu, jak pokazano.

![Dostosowywanie siatki pulpit nawigacyjny historii uruchamiania](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Rejestrowane podczas przebiegów śledzonych wartości są dostępne do wyświetlania i wybierania **współczynnika uregulowania** i **dokładność** dodaje je do siatki.

![zarejestrowane wartości w siatce niestandardowe](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Jest to łatwe do znalezienia interesujących przebiegów, ustawiając kursor nad punktów na wykresach.  W takim przypadku Uruchom 7 zwróciło dobrą dokładność połączone za pomocą niski czasu trwania.

![znajdowanie interesujących uruchamiania](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Kliknięcie punktu skojarzone z 7 uruchamianie w dowolnym wykresie lub link do uruchomienia 7 w Wyświetla siatkę **szczegóły historii przebiegu**.

### <a name="run-history-details"></a>Szczegóły historii uruchamiania
Z poziomu tego widoku pełnych wyników 7 uruchomienia oraz wszelkie artefakty produkowane przez uruchomienie 7 są wyświetlane.

![Szczegóły historii uruchamiania](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

**Szczegóły historii przebiegu** widoku oferuje również możliwość **Pobierz** wszystkie pliki zapisane **. / wyjścia** folder (te pliki są wspierane przez usługi Azure ML Workbench Magazyn w chmurze dla historii uruchamiania, który podlega innym artykule.)

Na koniec **szczegóły historii przebiegu** udostępnia środki do Przywróć swój projekt jego stan w czasie tego działania.
Klikając **przywrócić** przycisk powoduje wyświetlenie dialogu potwierdzenie, jak pokazano.

![Upewnij się, przywracania, uruchom](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Jeśli potwierdzona, pliki zostaną zastąpione lub usunięte, więc ostrożne korzystanie z tej funkcji.

### <a name="run-history-comparison"></a>Uruchom porównanie historii
Wybierz dwa lub trzy działa w **pulpit nawigacyjny historii uruchamiania** i klikając **porównania** oferuje **Uruchom porównanie historii** widoku.
Ewentualnie klikając **porównania** i wybierając polecenie Uruchom w ramach **szczegóły historii przebiegu** widok również oferuje **Uruchom porównanie historii** widoku.
W obu przypadkach **Uruchom porównanie historii** widoku zapewnia to, aby zobaczyć wyniki rejestrowane i artefakty dwa lub trzy uruchomienia obok siebie.

![Uruchom porównanie historii](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Ten widok jest szczególnie przydatna podczas porównywania powierzchnie, ale ogólnie rzecz biorąc, dowolne właściwości uruchomienia można porównać w tym miejscu.

### <a name="command-line-interface"></a>Command Line Interface
Usługa Azure Machine Learning Workbench umożliwia również dostęp do historii uruchamiania za pośrednictwem jego **interfejs wiersza polecenia**.
Aby uzyskać dostęp do **interfejs wiersza polecenia**, kliknij przycisk **Otwórz wiersz polecenia** menu, jak pokazano.

![Otwórz okno wiersza polecenia](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Dostępne dla historii uruchamiania polecenia są dostępne za pośrednictwem `az ml history`, przy pomocy online, które są dostępne, dodając `-h` flagi.
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
Te polecenia zapewnia te same funkcje i zwracają te same dane, które są wyświetlane **widoków historii uruchamiania**.
Na przykład wyniki ostatniego przebiegu, mogą być wyświetlane jako obiekt JSON.
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
Ponadto listę wszystkich przebiegów mogą wyświetlane w formacie tabelarycznym.
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
**Interfejs wiersza polecenia** jest alternatywną ścieżkę pozwalającą na dostęp do możliwości usługi Azure Machine Learning Workbench.

## <a name="next-steps"></a>Następne kroki
Te funkcje są dostępne na potrzeby procesu eksperymentowania do nauki o danych.
Mamy nadzieję, że znajdowanie musiały być przydatne i znacznie wdzięczni za Twoją opinię.
Jest to po prostu Nasze wstępne wdrożenie, a następnie mamy wiele ulepszeń planowane.
Chętnie ciągłe dostarczanie ich do usługi Azure Machine Learning Workbench. 
