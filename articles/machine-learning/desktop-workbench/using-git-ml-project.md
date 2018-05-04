---
title: Repozytorium Git za pomocą usługi Azure Machine Learning Workbench projektu | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób korzystania z repozytorium Git w połączeniu z projektem Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 6d244e990552ac4899361360efb44be140b06fe8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Repozytorium Git za pomocą projektu Machine Learning Workbench
Dowiedz się, jak Azure Machine Learning Workbench używa Git do zapewnienia kontroli wersji i upewnij się, powtarzalności w eksperymencie analizy danych. Dowiedz się, jak można skojarzyć z chmurą repozytorium Git (repozytorium) projektu.

Machine Learning Workbench zaprojektowano pod kątem Integracja z usługą Git. Podczas tworzenia nowego projektu folderu projektu jest automatycznie "Git inicjowany" jako lokalne repozytorium Git. Drugi, ukryte lokalnego repozytorium Git, również zostanie utworzona z gałąź o nazwie AzureMLHistory /\<identyfikatora GUID projektu\>. Gałąź przechowuje informacje o zmiany folderu projektu dla każdego wykonania. 

Kojarzenie projektu usługi Azure Machine Learning z repozytorium Git umożliwia kontroli wersji automatyczne lokalnie i zdalnie. Repozytorium Git jest hostowany w programie Visual Studio Team Services (usługi Team Services). Ponieważ projekt uczenia maszynowego jest skojarzony z repozytorium Git, każdy, kto ma dostęp do zdalnego repozytorium Pobierz najnowsze kodu źródłowego do innego komputera (mobilnych).  

> [!NOTE]
> Usługi Team Services ma własną listy kontroli dostępu (ACL), która jest niezależna od usługi Azure Machine Learning eksperymenty. Dostęp użytkownika mogą się różnić od repozytorium Git do obszaru roboczego uczenia maszynowego lub projektu. Może być konieczne zarządzanie dostępem. 
> 
> Czy chcesz nadać elementu członkowskiego zespołu na poziomie kodu dostępu do projektu usługi Machine Learning lub po prostu udostępnić obszaru roboczego, należy udzielić odpowiednich uprawnień do dostępu do repozytorium Git programu Team Services. 

W celu zarządzania kontrolą wersji za pomocą narzędzia Git, możesz użyć gałęzi głównej lub utworzyć inne gałęzie w repozytorium. Można również użyć lokalnego repozytorium Git i wypychania do zdalnego repozytorium Git, jeśli jego obsługa została zainicjowana.

Ten diagram przedstawia relację między repozytorium Git programu Team Services i projektu usługi Machine Learning:

![Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

Aby rozpocząć korzystanie ze zdalnego repozytorium Git, wykonaj kroki opisane w poniższych sekcjach.

> [!NOTE]
> Obecnie uczenia maszynowego Azure obsługuje repozytoria Git tylko na kontach usługi Team Services. Obsługa dla repozytoriów Git ogólne, takie jak GitHub, jest planowane w przyszłości.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Krok 1. Utwórz konto Machine Learning eksperymenty
Utwórz konto Machine Learning eksperymenty i zainstalować aplikację usługi Azure Machine Learning Workbench. Aby uzyskać więcej informacji, zobacz [zainstalować i utworzyć szybkiego startu](../service/quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Krok 2. Tworzenie projektu zespołowego lub użyj istniejącego projektu zespołowego
W [portalu Azure](https://portal.azure.com/), tworzenie nowego projektu zespołowego:
1. Wybierz **+**.
2. Wyszukaj **projektu zespołowego**.
3. Wprowadź wymagane informacje:
    - **Nazwa**: Nazwa zespołu.
    - **Kontrola wersji**: Wybierz **Git**.
    - **Subskrypcja**: Wybierz subskrypcję, która ma konto Machine Learning eksperymenty.
    - **Lokalizacja**: najlepiej, wybierz region, który znajduje się w pobliżu zasobów Machine Learning eksperymenty.
4. Wybierz pozycję **Utwórz**. 

![Tworzenie projektu zespołowego w portalu Azure](media/using-git-ml-project/create_vsts_team.png)

Upewnij się, czy możesz zalogować się przy użyciu tego samego konta usługi Azure Active Directory (Azure AD), który umożliwia dostęp Machine Learning Workbench. W przeciwnym razie system nie może uzyskać dostępu Machine Learning Workbench przy użyciu poświadczeń usługi Azure AD. Wyjątkiem jest Jeśli za pomocą wiersza polecenia do tworzenia projektu uczenia maszynowego i podać osobisty token dostępu do repozytorium Git. Omówiono to bardziej szczegółowo w dalszej części tego artykułu.

Aby przejść bezpośrednio do projektu zespołowego, który został utworzony, należy użyć adresu URL https://\<Nazwa projektu zespołowego\>. visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Krok 3. Konfigurowanie projektu uczenia maszynowego i repozytorium Git

Aby skonfigurować projekt uczenia maszynowego, masz dwie opcje:
- Utwórz projekt uczenia maszynowego, który ma zdalnego repozytorium Git
- Skojarz istniejący projekt Machine Learning z repozytorium Git programu Team Services

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Utwórz projekt uczenia maszynowego, który ma zdalnego repozytorium Git
Otwórz Machine Learning Workbench i Utwórz nowy projekt. W **repozytorium Git** wprowadź adres URL repozytorium Git programu Team Services z kroku 2. Zwykle wygląda następująco: https://\<nazwa konta usługi Team Services\>.visualstudio.com/_git/\<nazwy projektu\>

![Utwórz projekt uczenia maszynowego, który ma repozytorium Git](media/using-git-ml-project/create_project_with_git_rep.png)

Można również utworzyć projekt za pomocą narzędzia wiersza polecenia platformy Azure (Azure CLI). Istnieje możliwość wprowadzania osobisty token dostępu. Uczenie maszynowe umożliwia dostęp do repozytorium Git zamiast przy użyciu poświadczeń usługi Azure AD ten token:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> Jeśli wybierzesz szablon projektu puste repozytorium Git, którego chcesz użyć mogą już mieć gałęzi głównej. Uczenie maszynowe klonów po prostu lokalnie gałęzi głównej. Dodaje aml_config folder i inne pliki metadanych projektu do folderu lokalnego projektu. 
>
> Jeśli wybierzesz każdego innego szablonu projektu, z repozytorium Git *nie* już gałęzi głównej. Jeśli tak, zostanie wyświetlony błąd. Alternatywą jest użycie `az ml project create` polecenie, aby utworzyć projekt, z `--force` przełącznika. To usuwa pliki w oryginalnej gałęzi głównej i zastępuje je nowe pliki w szablonie, który można wybrać.

Nowy projekt usługi Machine Learning jest tworzony, z włączoną integracją repozytorium Git zdalnego. Folder projektu jest zawsze Git zainicjowany jako lokalne repozytorium Git. Zdalny narzędzia Git jest ustawiona do zdalnego repozytorium Git programu Team Services, więc można wypychanie zatwierdzeń do zdalnego repozytorium Git.

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>Skojarz istniejący projekt Machine Learning z repozytorium Git programu Team Services
Tworzenie projektu usługi Machine Learning bez repozytorium Git programu Team Services i zależne od lokalnego repozytorium Git dla migawki historii wykonywania. Później z tym istniejący projekt uczenia maszynowego można skojarzyć repozytorium Git programu Team Services za pomocą następującego polecenia:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Można wykonać operacji aktualizacji repozytorium tylko na projekt uczenia maszynowego, który nie ma repozytorium Git skojarzonego z nim. Ponadto po repozytorium Git należy powiązać z uczenia maszynowego, nie można usunąć go.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Krok 4. Przechwyć migawka projektu w repozytorium Git
Wykonanie kilku skrypt będzie uruchamiany w projekcie i wprowadzić kilka zmian Between działa. Można to zrobić w aplikacji pulpitu lub z wiersza polecenia platformy Azure przy użyciu `az ml experiment submit` polecenia. Aby uzyskać więcej informacji, zobacz [samouczek klasyfikowania Iris](tutorial-classifying-iris-part-1.md). Przy każdym uruchomieniu przypadku dokonania zmian w żadnym pliku w folderze projektu migawki folderu cały projekt jest zadeklarowane i przypisany do zdalnego repozytorium Git w gałęzi o nazwie AzureMLHistory /\<identyfikatora GUID projektu\>. Aby wyświetlić gałęzie i zatwierdzeń, w tym AzureMLHistory /\<identyfikatora GUID projektu\> gałęzi, przejdź do adresu URL repozytorium Git programu Team Services. 

> [!NOTE] 
> Migawki zostaje zatwierdzona tylko przed rozpoczęciem wykonywania skryptu. Obecnie przedprodukcyjnym danych wykonywania lub wykonywania komórki notesu nie powoduje wyzwolenia migawki.

![Historia uruchomień gałęzi](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Najlepiej nie działają w historii gałęzi, używając polecenia usługi Git. Może on zakłócać historii uruchamiania. Zamiast tego użyj gałęzi głównej lub Utwórz inne gałęzie dla operacji Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Krok 5. Przywróć poprzednią migawkę projektu 
Aby przywrócić folder całego projektu do stanu poprzedniego historii wykonywania migawek w Machine Learning Workbench:
1. W działaniu paska (ikona klepsydry), wybierz **działa**.
2. W **Uruchom listy** wyświetlić, wybierz polecenie Uruchom, który chcesz przywrócić.
3. W **Uruchom szczegółów** widok, wybierz opcję **przywrócić**.

![Historia uruchomień gałęzi](media/using-git-ml-project/restore_project.png)

Opcjonalnie możesz użyć następujących poleceń w oknie wiersza polecenia platformy Azure Machine Learning Workbench:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Należy zachować ostrożność podczas uruchamiania tego polecenia. Migawki, która została podjęta, jeśli z określonym programem zostało rozpoczęte wykonywania tego polecenia zastępuje folderu całego projektu. Projekt pozostaje w bieżącej gałęzi. Oznacza to, że możesz **utracić wszystkie zmiany** w bieżącym folderze projektu.  

Można użyć narzędzia Git można przekazać zmian do bieżącej gałęzi, przed wykonaniem tej operacji.

## <a name="step-6-use-the-master-branch"></a>Krok 6. Użyj głównej gałęzi
Jednym ze sposobów uniknąć przypadkowej utraty aktualnego stanu projektu jest zatwierdzić projektu do głównej gałęzi repozytorium Git (lub oddziałów utworzony przez siebie). Można użyć narzędzia Git z wiersza polecenia lub ulubionego narzędzia Git klienta może działać w gałęzi głównej. Na przykład:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

Teraz można bezpiecznie przywrócić projektu do wcześniejszej migawki, wykonując krok 5. Możesz zawsze wrócić do zatwierdzenia, wprowadzone w gałęzi głównej.

## <a name="authentication"></a>Authentication
Jeśli użytkownik korzysta z funkcji Historia uruchomień w uczeniu maszynowym migawek projektu i je przywrócić, na, nie musisz martwić się o uwierzytelnianiu repozytorium Git. Uwierzytelnianie jest obsługiwane przez warstwę usługi Machine Learning eksperymenty.

Jednak użycie własnego narzędzia Git w celu zarządzania kontrolą wersji, należy do obsługi uwierzytelniania zdalnego repozytorium Git w usłudze Team Services. W uczeniu maszynowym zdalnego repozytorium Git jest dodawana do lokalnego repozytorium jako element zdalny narzędzia Git za pomocą protokołu HTTPS. Oznacza to, że w przypadku wysyłania polecenia usługi Git (na przykład wypychania lub ściągania) do komputera zdalnego, musisz podać nazwę użytkownika i hasło lub osobisty token dostępu. Aby utworzyć osobisty token dostępu w repozytorium Git programu Team Services, postępuj zgodnie z instrukcjami [osobisty token dostępu umożliwia uwierzytelnianie](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [do organizowania struktury projektu, użyj procesu nauki danych zespołu](how-to-use-tdsp-in-azure-ml.md).
