---
title: Artykuł zawierający samouczek dotyczący funkcji w wersji zapoznawczej usługi Azure Machine Learning — interfejs wiersza polecenia | Microsoft Docs
description: Ten samouczek zawiera szczegółowe omówienie wszystkich kroków wymaganych w celu przeprowadzenia klasyfikacji irysów w całości z poziomu interfejsu wiersza polecenia.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: 2d7965c0028e8f2e82db2e3ddd4eed6e20f4f443
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Samouczek: klasyfikowanie irysów przy użyciu interfejsu wiersza polecenia
Usługi Azure Machine Learning (wersja zapoznawcza) stanowią zintegrowane, kompleksowe rozwiązanie do przetwarzania danych i do przeprowadzania zaawansowanych analiz. Jest ono przeznaczone dla profesjonalnych analityków, którzy będą z niego korzystać w celu przygotowywania danych, opracowywania eksperymentów i wdrażania modeli na skalę chmury.

Z tego samouczka dowiesz się, jak używać narzędzi interfejsu wiersza polecenia (CLI) w wersji zapoznawczej usługi Azure Machine Learning do wykonywania następujących czynności: 
> [!div class="checklist"]
> * Konfigurowanie konta eksperymentowania i tworzenie obszaru roboczego
> * Tworzenie projektu
> * Przysyłanie eksperymentu do wielu obliczeniowych elementów docelowych
> * Podwyższanie poziomu i rejestrowanie uczonego modelu
> * Wdrażanie usługi internetowej, aby oceniać nowe dane

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:
- Dostęp do subskrypcji platformy Azure i uprawnienia do tworzenia zasobów w danej subskrypcji. 
  
  Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Aplikacja Azure Machine Learning Workbench zainstalowana zgodnie z opisem w sekcji [Szybki Start: instalowanie i uruchamianie usług Azure Machine Learning](quickstart-installation.md). 

  >[!IMPORTANT]
  >Nie należy tworzyć kont usługi Azure Machine Learning, ponieważ zrobimy to z użyciem interfejsu wiersza polecenia w tym artykule.
 
## <a name="getting-started"></a>Wprowadzenie
Interfejs wiersza polecenia (CLI) usługi Azure Machine Learning umożliwia wykonywanie wszystkich zadań wymaganych do pełnego przepływu pracy analizy danych. Dostęp do narzędzi interfejsu wiersza polecenia można uzyskać na kilka sposobów:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Sposób 1. Uruchamianie interfejsu wiersza polecenia usługi Azure ML z poziomu okna dialogowego logowania aplikacji Azure ML Workbench
Jeśli uruchamiasz aplikację Azure ML Workbench i logujesz się po raz pierwszy, a nie masz jeszcze dostępu do konta eksperymentowania, zostanie wyświetlony następujący ekran:

![nie odnaleziono konta](media/tutorial-iris-azure-cli/no_account_found.png)

Kliknij link **Command Line Window (okno wiersza polecenia)** w oknie dialogowym, aby uruchomić okno wiersza polecenia.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Sposób 2. Uruchamianie interfejsu wiersza polecenia usługi Azure ML z poziomu aplikacji Azure ML Workbench
Jeśli masz już dostęp do konta eksperymentowania, możesz się pomyślnie zalogować. Następnie możesz otworzyć okno wiersza polecenia, klikając menu **Plik** --> **Otwórz wiersz polecenia**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Sposób 3. Włączanie interfejsu wiersza polecenia usługi Azure ML w dowolnym oknie wiersza polecenia
Możesz także włączyć interfejs wiersza polecenia usługi Azure ML w dowolnym oknie wiersza polecenia. Aby to zrobić, otwórz okno polecenia i wpisz następujące polecenia:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Aby wprowadzić tą zmianę na trwałe, możesz użyć polecenia `SETX` w systemie Windows. W systemie macOS, możesz użyć polecenia `setenv`.

>[!TIP]
>Interfejs wiersza polecenia usługi Azure można włączyć w ulubionym oknie terminalu, ustawiając wcześniej wymienione zmienne środowiskowe.

## <a name="step-1-log-in-to-azure"></a>Krok 1. Zaloguj się do platformy Azure.
Pierwszym krokiem jest otwarcie interfejsu wiersza polecenia z poziomu aplikacji AMLWorkbench (Plik > Otwórz wiersz polecenia). Dzięki temu mamy pewność, że środowisko python jest poprawne i że polecenia interfejsu wiersza polecenia usługi ML są dostępne. 

Teraz możesz ustawić poprawny kontekst w interfejsie wiersza polecenia, aby uzyskać dostęp do zasobów usługi Azure i nimi zarządzać.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Krok 2. Tworzenie nowego konta eksperymentowania i obszaru roboczego usługi Azure Machine Learning

W tym kroku utworzysz nowe konto eksperymentowania i nowy obszar roboczy. Aby uzyskać więcej informacji o kontach eksperymentowania i obszarach roboczych, zobacz [Pojęcia dotyczące usługi Azure Machine Learning](overview-general-concepts.md).

> [!NOTE]
> Konta eksperymentowania wymagają konta magazynu, które jest używane do przechowywania danych wyjściowych z przebiegów eksperymentu. Nazwa konta magazynu na platformie Azure musi być globalnie unikatowa, ponieważ jest z nią skojarzony adres URL. Jeśli nie określisz istniejącego konta magazynu, do utworzenia nowego konta magazynu zostanie użyta nazwa konta eksperymentowania. Upewnij się, że użyta nazwa jest unikatowa. W przeciwnym wypadku zostanie wyświetlony komunikat o błędzie, np. _„Konto magazynu o nazwie \<nazwa_konta_magazynu> jest już zajęte.”_ Możesz również użyć argumentu `--storage`, aby podać istniejące konto magazynu.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Krok 2.a (opcjonalnie) Udostępnienie obszaru roboczego współpracownikowi
W tym miejscu możesz sprawdzić, jak udostępnić obszar roboczy współpracownikowi. Kroki, jakie należy wykonać w celu udostępnienia konta eksperymentowania lub projektu, są takie same. Zmienić należy jedynie sposób uzyskania identyfikatora zasobu platformy Azure.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> Wartość `bob@contoso.com` w poleceniu powyżej musi być prawidłową tożsamością usługi Azure AD w katalogu, do którego należy bieżąca subskrypcja.

## <a name="step-3-create-a-new-project"></a>Krok 3. Tworzenie nowego projektu
Naszym kolejnym krokiem będzie utworzenie nowego projektu. Istnieje kilka sposobów, aby rozpocząć pracę z nowym projektem.

### <a name="create-a-new-blank-project"></a>Tworzenie nowego pustego projektu

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Tworzenie nowego projektu za pomocą domyślnego szablonu projektu
Możesz utworzyć nowy projekt za pomocą domyślnego szablonu.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Tworzenie nowego projektu skojarzonego z repozytorium Git w chmurze
Możesz utworzyć nowy projekt skojarzony z repozytorium Git usługi VSTS (Visual Studio Team Service). Za każdym razem, gdy przesyłany jest eksperyment, migawka całego folderu projektu jest przekazywana do zdalnego repozytorium Git. Zobacz [Używanie repozytorium Git z projektem aplikacji Azure Machine Learning Workbench](using-git-ml-project.md), aby uzyskać więcej informacji.

> [!NOTE]
> Usługa Azure Machine Learning obsługuje tylko puste repozytoria Git utworzone w usłudze VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Jeśli wyświetla się błąd „Adres URL repozytorium może być nieprawidłowy lub użytkownik może nie mieć dostępu”, możesz utworzyć token zabezpieczający w usłudze VSTS (w menu _Zabezpieczenia_, _Dodaj osobiste tokeny dostępu_) i użyć argumentu `--vststoken` podczas tworzenia projektu. 

### <a name="sample_create"></a>Tworzenie nowego projektu na podstawie projektu przykładowego
W tym przykładzie utworzysz nowy projekt, używając jako szablonu projektu przykładowego.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Po utworzeniu projektu użyj polecenia `cd`, aby przejść do katalogu projektu.

## <a name="step-4-run-the-training-experiment"></a>Krok 4. Uruchomienie eksperymentu szkolenia 
Kolejne kroki zakładają, że posiadasz projekt zawierający próbkę irysów (zobacz [Tworzenie nowego projektu na podstawie próbki online](#sample_create)).

### <a name="prepare-your-environment"></a>Przygotowywanie środowiska 
Dla próbki irysów należy zainstalować pakiet matplotlib.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Przesyłanie eksperymentu

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Wykonywanie iteracji eksperymentu z malejącymi współczynnikami uregulowania
Z pewną dozą kreatywności można łatwo stworzyć skrypt w języku Python, który przesyła eksperymenty z różnymi współczynnikami uregulowania. (Może być konieczna edycja pliku w celu wskazania ścieżki projektu).

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Krok 5. Widok historii uruchamiania
Następujące polecenie wyświetla listę wszystkich wykonywanych wcześniej przebiegów. 

```azure-cli
$ az ml history list -o table
```
Uruchomienie poprzedzającego polecenia wyświetli listę wszystkich przebiegów należących do tego projektu. Wyświetlone zostają także dane dotyczące dokładności i współczynnika uregulowania. Dzięki temu można łatwo zidentyfikować najlepszy przebieg na liście.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Krok 5.a Wyświetlenie załącznika utworzonego przez dany przebieg 
Aby wyświetlić załącznik skojarzony z danym przebiegiem, można użyć polecenia informacji o historii przebiegów. Znajdź identyfikator przebiegu wymienionego na liście.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Aby pobrać artefakty z przebiegu, można użyć poniższego polecenia:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Krok 6. Podwyższenie poziomu artefaktów przebiegu 
Jeden z przebiegów ma lepszą wartość pola powierzchni pod krzywą (AUC), więc powinien być użyty przy tworzeniu usługi internetowej przyznawania ocen do wdrożenia w środowisku produkcyjnym. Aby to zrobić, należy najpierw podwyższyć poziom artefaktów do elementu zawartości.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Spowoduje to utworzenie folderu `assets` w katalogu projektu zawierającego plik `model.pkl.link`. Ten plik linku jest używany jako odwołanie do elementu zawartości o podwyższonym poziomie.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Krok 7. Pobieranie plików, które mają zostać zoperacjonalizowane
Pobierz model o podwyższonym poziomie, aby móc użyć go do utworzenia usługi internetowej prognozowania. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>Krok 8. Konfigurowanie środowiska zarządzania modelami 
Utwórz środowisko do wdrażania usług internetowych. Możesz uruchomić usługi internetowe na komputerze lokalnym przy użyciu narzędzia Docker. Możesz też wdrożyć je w ramach klastra usługi ACS pod kątem operacji o dużej skali. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Krok 9. Tworzenie konta zarządzania modelami 
Konto zarządzania modelami jest wymagane do wdrażania i śledzenia modeli w środowisku produkcyjnym. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Krok 10. Tworzenie usługi internetowej
Utwórz usługę internetową, która za pomocą wdrożonego modelu zwraca prognozę. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>Krok 11. Uruchamianie usługi internetowej
Przy użyciu identyfikatora usługi internetowej z danych wyjściowych poprzedniego kroku wywołaj usługę internetową i przetestuj ją. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>Krok 12. Usuwanie wszystkich zasobów 
Zakończmy ten samouczek, usuwając wszystkie zasoby, które utworzyliśmy, chyba że planujemy nadal ich używać. 

Aby to zrobić, usuń grupę zasobów zawierającą te zasoby. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Następne kroki
W tym samouczku nauczyliśmy się używać usługi Azure Machine Learning do wykonywania następujących czynności: 
> [!div class="checklist"]
> * Konfigurowanie konta eksperymentowania, tworzenie obszaru roboczego
> * Tworzenie projektów
> * Przesyłanie eksperymentów do wielu obliczeniowych elementów docelowych
> * Podwyższanie poziomu i rejestrowanie uczonego modelu
> * Tworzenie konta zarządzania modelami do zarządzania modelami
> * Tworzenie środowiska do wdrażania usług internetowych
> * Wdrażanie usług internetowych i ocenianie z użyciem nowych danych
