---
title: Użyj repozytorium Git z projektem aplikacji Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak używać repozytorium Git w połączeniu z projektem aplikacji Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 16c102641321117f4776d761aba6c2148d15f1f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995650"
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Użyj repozytorium Git z projektem aplikacji Machine Learning Workbench

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Dowiedz się, jak Azure Machine Learning Workbench używa Git, aby zapewnić kontrolę wersji i upewnij się, odtwarzalnych w eksperymencie do nauki o danych. Dowiedz się, jak skojarzyć projekt z repozytorium Git (repozytorium) w chmurze.

Usługa Machine Learning Workbench jest przeznaczona dla integrację z usługą Git. Podczas tworzenia nowego projektu w folderze projektu jest automatycznie "Git zainicjowana" jako lokalne repozytorium Git. Drugi, ukryte lokalnego repozytorium Git, również jest tworzony przy użyciu gałęzi o nazwie AzureMLHistory /\<identyfikator GUID projektu\>. Gałąź przechowuje informacje o zmiany w folderze projektu dla każdego wykonania. 

Kojarzenie projektu usługi Azure Machine Learning z repozytorium Git umożliwia kontroli wersji automatyczne, lokalnie i zdalnie. Repozytorium Git znajduje się w DevOps platformy Azure. Ponieważ projekt usługi Machine Learning jest skojarzony z repozytorium Git, każdy, kto ma dostęp do zdalnego repozytorium Pobierz najnowszego kodu źródłowego do innego komputera (mobilnych).  

> [!NOTE]
> DevOps platformy Azure ma swój własny listy kontroli dostępu (ACL), który jest niezależny od usługi eksperymentowanie w usłudze Machine Learning platformy Azure. Dostęp użytkownika mogą różnić się od repozytorium Git do obszaru roboczego usługi Machine Learning lub projektu. Może być konieczne zarządzanie dostępem. 
> 
> Czy chcesz nadać członek zespołu poziomie kodu dostępu do projektu usługi Machine Learning lub po prostu udostępnić obszar roboczy, musisz przyznać użytkownikowi odpowiednich uprawnień do dostępu do repozytorium DevOps Git platformy Azure. 

Aby zarządzać Kontrola wersji przy użyciu narzędzia Git, możesz użyliśmy gałęzi głównej, lub Utwórz inne gałęzie w repozytorium. Można również użyć lokalnego repozytorium Git i Wypchnij do zdalnego repozytorium Git, jeśli jest zainicjowany.

Ten diagram przedstawia relację między repozytorium DevOps Git platformy Azure i projekt usługi Machine Learning:

![Usługi Azure Machine Learning w usłudze Git](media/using-git-ml-project/aml_git.png)

Aby rozpocząć korzystanie ze zdalnego repozytorium Git, wykonaj kroki, które są opisane w poniższych sekcjach.

> [!NOTE]
> Obecnie usługa Azure Machine Learning obsługuje repozytoria Git tylko na organizacje DevOps platformy Azure.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Krok 1. Tworzenie konta eksperymentowanie w usłudze Machine Learning
Tworzenie konta eksperymentowanie w usłudze Machine Learning i zainstalować aplikację Azure Machine Learning Workbench. Aby uzyskać więcej informacji, zobacz [Instalowanie i Tworzenie szybkiego startu](quickstart-installation.md).

## <a name="step-2-create-an-azure-devops-project-or-use-an-existing-project"></a>Krok 2. Utwórz projekt DevOps platformy Azure lub użyj istniejącego projektu
W [witryny Azure portal](https://portal.azure.com/), Utwórz nowy projekt:
1. Wybierz **+**.
2. Wyszukaj **projekt zespołowy**.
3. Wprowadź wymagane informacje:
    - **Nazwa**: Nazwa zespołu.
    - **Kontrola wersji**: Wybierz **Git**.
    - **Subskrypcja**: Wybierz subskrypcję, która ma konto eksperymentowanie w usłudze Machine Learning.
    - **Lokalizacja**: najlepiej, wybierz region, który znajduje się w pobliżu zasobów eksperymentowanie w usłudze Machine Learning.
4. Wybierz pozycję **Utwórz**. 

![Utwórz projekt w witrynie Azure portal](media/using-git-ml-project/create_vsts_team.png)

Upewnij się, że logujesz się przy użyciu tego samego konta usługi Azure Active Directory (Azure AD), która umożliwia dostęp do aplikacji Machine Learning Workbench. W przeciwnym razie system nie może uzyskać dostęp do Machine Learning Workbench przy użyciu poświadczeń usługi Azure AD. Wyjątkiem jest, jeśli możesz użyć wiersza polecenia, aby utworzyć projekt usługi Machine Learning i podać osobistego tokenu dostępu do dostępu do repozytorium Git. Omówimy to bardziej szczegółowo w dalszej części tego artykułu.

Aby przejść bezpośrednio do projektu, który został utworzony, należy użyć https:// adresu URL\<Nazwa projektu\>. visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Krok 3. Konfigurowanie projektu usługi Machine Learning i repozytorium Git

Aby skonfigurować projekt usługi Machine Learning, masz dwie opcje:
- Utwórz projekt usługi Machine Learning, która ma zdalnego repozytorium Git
- Kojarzenie istniejącego projektu usługi Machine Learning z repozytorium DevOps Git platformy Azure

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Utwórz projekt usługi Machine Learning, która ma zdalnego repozytorium Git
Otwórz aplikację Machine Learning Workbench i Utwórz nowy projekt. W **repozytorium Git** wprowadź adres URL repozytorium DevOps Git platformy Azure z kroku nr 2. Zazwyczaj wygląda następująco: https://\<DevOps platformy Azure, nazwę organizacji\>.visualstudio.com/_git/\<Nazwa projektu\>

![Utwórz projekt usługi Machine Learning, która ma repozytorium Git](media/using-git-ml-project/create_project_with_git_rep.png)

Można również utworzyć projekt za pomocą narzędzia wiersza polecenia platformy Azure (Azure CLI). Istnieje możliwość wprowadzania osobistego tokenu dostępu. Usługi Machine Learning umożliwia dostęp do repozytorium Git zamiast przy użyciu poświadczeń usługi Azure AD ten token:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Azure DevOps personal access token>
```

> [!IMPORTANT]
> Jeśli wybierzesz szablon pustego projektu, repozytorium Git, które użytkownik chce użyć może już gałęzi głównej. Uczenie maszynowe klony po prostu lokalnie gałęzi głównej. Dodaje aml_config folder i inne pliki metadanych projektu do folderu lokalnego projektu. 
>
> Jeśli wybierzesz każdego innego szablonu projektu, repozytorium Git *nie* jeszcze gałęzi głównej. Jeśli istnieje, zostanie wyświetlony błąd. Alternatywą jest użycie `az ml project create` polecenie, aby utworzyć projekt, za pomocą `--force` przełącznika. To spowoduje usunięcie plików w oryginalnym gałęzi głównej i zastępuje je nowe pliki w szablonie, który wybierzesz.

Nowy projekt usługi Machine Learning jest utworzone za pomocą zdalnego repozytorium integrację z usługą Git włączone. Folder projektu jest zawsze Git zainicjowane w formacie lokalnego repozytorium Git. Zdalny jest równa zdalnego repozytorium DevOps Git platformy Azure, dzięki czemu możesz wypychać zatwierdzenia do zdalnego repozytorium Git.

### <a name="associate-an-existing-machine-learning-project-with-an-azure-devops-git-repo"></a>Kojarzenie istniejącego projektu usługi Machine Learning z repozytorium DevOps Git platformy Azure
Można utworzyć projekt usługi Machine Learning, bez repozytorium DevOps Git platformy Azure i opierają się na lokalne repozytorium Git dla migawek historii uruchamiania. Później można skojarzyć repozytorium DevOps Git platformy Azure z tym istniejący projekt usługi Machine Learning za pomocą następującego polecenia:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Azure DevOps organization name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Można wykonać operacji repozytorium aktualizacji tylko na projekt usługi Machine Learning, która nie ma skojarzonych z nim repozytorium Git. Ponadto po użytkownik jest kojarzony repozytorium usługi Git przy użyciu uczenia maszynowego, nie możesz usunąć go.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Krok 4. Przechwytywanie migawki projektu, w repozytorium Git
Wykonywanie kilku przebiegów skrypt w projekcie, a następnie wprowadzić pewne zmiany w zakresie od uruchomienia. Można to zrobić w aplikacji komputerowej lub z wiersza polecenia platformy Azure przy użyciu `az ml experiment submit` polecenia. Aby uzyskać więcej informacji, zobacz [samouczek klasyfikowanie irysów](tutorial-classifying-iris-part-1.md). Dla każdego uruchomienia, jeśli dokonania zmian w dowolnym pliku w folderze projektu, migawka całego folderu projektu jest zatwierdzeniu i wypchnięciu do zdalnego repozytorium Git w gałęzi o nazwie AzureMLHistory /\<identyfikator GUID projektu\>. Aby wyświetlić gałęzie i zatwierdzenia, w tym AzureMLHistory /\<identyfikator GUID projektu\> gałęzi, przejdź do adresu URL repozytorium DevOps Git platformy Azure. 

> [!NOTE] 
> Migawka jest zatwierdzona tylko przed wykonaniem skryptu. Obecnie, przygotowywanie danych wykonywania lub wykonywania komórki notesu nie spowoduje wyzwolenia migawki.

![Gałąź historii uruchamiania](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Najlepiej nie działają w historii gałęzi przy użyciu poleceń usługi Git. Może on kolidować z historii uruchamiania. Zamiast tego użyj gałęzi głównej, lub Utwórz inne gałęzie operacji usługi Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Krok 5. Przywróć poprzednią migawkę projektu 
Aby przywrócić całego folderu projektu do stanu poprzedniego historii uruchamiania migawki w aplikacji Machine Learning Workbench:
1. W przypadku działania pasku (ikona klepsydry) wybierz **przebiegów**.
2. W **listy uruchomienia** wyświetlić, wybierz polecenie Uruchom, który chcesz przywrócić.
3. W **szczegółów uruchomienia** widoku, wybierz opcję **przywrócić**.

![Gałąź historii uruchamiania](media/using-git-ml-project/restore_project.png)

Możesz opcjonalnie użyć następujących poleceń w oknie wiersza polecenia platformy Azure Machine Learning Workbench:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Należy zachować ostrożność podczas uruchamiania tego polecenia. Wykonanie tego polecenia zastępuje całego folderu projektu z migawką, która została wykonana, jeśli z określonym programem zostało rozpoczęte. Projekt pozostaje w wersji current branch. Oznacza to, że możesz **utracić wszystkie zmiany** w bieżącym folderze projektu.  

Można zatwierdzić zmiany w gałęzi bieżącej przed wykonaniem tej operacji za pomocą narzędzia Git.

## <a name="step-6-use-the-master-branch"></a>Krok 6. Skorzystaj z gałęzi master
Jest jednym ze sposobów, aby uniknąć przypadkowej utraty aktualnego stanu projektu do zatwierdzenia z projektu do głównej gałęzi repozytorium Git (lub dowolnej innej gałęzi, utworzonego przez siebie). Można użyć narzędzia Git z poziomu wiersza polecenia lub ulubionego narzędzia klienta Git do pracy w gałęzi głównej. Na przykład:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Azure DevOps Git repo master branch.
$ git push origin master
```

Teraz można bezpiecznie przywrócić projektu do wcześniejszej migawki, wykonując krok 5. Należy zawsze możesz wrócić do zatwierdzenia, które zostały wprowadzone w gałęzi głównej.

## <a name="authentication"></a>Authentication
Jeśli polegać wyłącznie na funkcji historii uruchamiania w usłudze Machine Learning do robienia migawek projektu i przywracać je, nie trzeba martwić się o uwierzytelnianiu repozytorium Git. Uwierzytelnianie odbywa się przez warstwę usługi eksperymentowanie w usłudze Machine Learning.

Jednak jeśli używasz własnych narzędzi usługi Git do zarządzania kontroli wersji, należy obsługiwać uwierzytelnianie względem zdalnego repozytorium Git w DevOps platformy Azure. W usłudze Machine Learning zdalnego repozytorium Git jest dodawany do lokalnego repozytorium jako element zdalny narzędzia Git przy użyciu protokołu HTTPS. Oznacza to, że po wykonaniu polecenia Git (na przykład wypychania lub ściągania) do komputera zdalnego, należy podać swoją nazwę użytkownika i hasło lub osobistego tokenu dostępu. Aby utworzyć osobisty token dostępu w repozytorium DevOps Git platformy Azure, postępuj zgodnie z instrukcjami [Użyj osobistego tokenu dostępu do uwierzytelniania](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [umożliwia organizowanie do struktury projektu zespołu danych dla celów naukowych](how-to-use-tdsp-in-azure-ml.md).
