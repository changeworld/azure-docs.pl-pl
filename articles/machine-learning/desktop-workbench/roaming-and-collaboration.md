---
title: Roaming i współpraca w usłudze Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować roaming i współpraca w aplikacji Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 0abc5e34d2bfa1cf2a9fc0569831e21ed295891c
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296503"
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming i współpraca w usłudze Azure Machine Learning Workbench
W tym artykule opisano, jak można użyć usługi Azure Machine Learning Workbench do ustawiania projektów dla mobilnych między komputerami i Współpracuj z członkami zespołu. 

Podczas tworzenia projektu usługi Azure Machine Learning, który ma zdalnego łącza (repozytorium) repozytorium Git projektu metadanych i migawki są przechowywane w chmurze. Link chmury umożliwia dostęp do projektu za pomocą innego komputera (mobilnych). Możesz także współpracować z członkami zespołu, dając im dostęp do projektu. 

## <a name="prerequisites"></a>Wymagania wstępne
1. Zainstaluj aplikację Workbench uczenia maszynowego. Upewnij się, że masz dostęp do konta eksperymentowanie w usłudze Machine Learning platformy Azure. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji](../service/quickstart-installation.md).

2. Dostęp do [DevOps platformy Azure](https://www.visualstudio.com) , a następnie utwórz projekt do repozytorium. Aby uzyskać więcej informacji, zobacz [przy użyciu repozytorium Git z projektem aplikacji Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Utwórz nowy projekt usługi Machine Learning
Otwórz aplikację Machine Learning Workbench, a następnie utwórz nowy projekt (na przykład projekt o nazwie iris). W **adres URL repozytorium GIT Visualstudio.com** Wprowadź prawidłowy adres URL repozytorium DevOps Git platformy Azure. 

> [!IMPORTANT]
> Jeśli wybierzesz szablon pustego projektu, repozytorium Git, które użytkownik chce użyć może już gałęzi głównej. Uczenie maszynowe klony po prostu lokalnie gałęzi głównej. Dodaje aml_config folder i inne pliki metadanych projektu do folderu lokalnego projektu. 
>
> Jeśli wybierzesz każdego innego szablonu projektu, repozytorium Git *nie* jeszcze gałęzi głównej. Jeśli istnieje, zostanie wyświetlony błąd. Alternatywą jest użycie `az ml project create` polecenie, aby utworzyć projekt, za pomocą `--force` przełącznika. To spowoduje usunięcie plików w oryginalnym gałęzi głównej i zastępuje je nowe pliki w szablonie, który wybierzesz.

Po utworzeniu projektu, przesłać kilka uruchomień w wszystkie skrypty, które należą do projektu. Ta akcja zatwierdzenia stanu projektu do gałęzi historii uruchamiania zdalnego repozytorium Git. 

> [!NOTE] 
> Tylko skrypt jest uruchamiany wyzwalacz zatwierdzenia do gałęzi historii uruchamiania. Przeznaczonego do przygotowania danych i wykonywanie notesu uruchomienia nie wyzwolić tworzenie migawek projektu w gałęzi historii uruchamiania.

Jeśli po skonfigurowaniu uwierzytelniania usługi Git, można także używać w gałęzi głównej. Lub możesz utworzyć nową gałąź. 

Przykład: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Otwórz aplikację Machine Learning Workbench na drugim komputerze
Po repozytorium DevOps Git platformy Azure został połączony z projektem, projektu iris w ramach są dostępne z dowolnego komputera, który ma Machine Learning Workbench zainstalowana. 

Aby uzyskać dostęp do projektu iris w ramach na innym komputerze, możesz Zaloguj się do aplikacji przy użyciu tych samych poświadczeń, których użyto podczas tworzenia projektu. Musisz także należeć do tego samego konta eksperymentowanie w usłudze Machine Learning i obszaru roboczego. Projektu iris w kolejności alfabetycznej znajduje się z innymi projektami, w obszarze roboczym. 

### <a name="download-the-project-on-a-second-computer"></a>Pobieranie projektu na drugim komputerze
Po otwarciu na drugim komputerze roboczym ikony obok projektu iris w ramach różni się od ikonę typowy folder. Ikona pobierania wskazuje, czy zawartości projektu w chmurze i że projekt jest gotowe do pobrania na bieżącym komputerze. 

![Tworzenie projektu](./media/roaming-and-collaboration/downloadable-project.png)

Wybierz projekt iris, aby rozpocząć pobieranie. Po zakończeniu pobierania projektu jest gotowy do można uzyskać dostępu do na drugim komputerze. 

W Windows, projekt znajduje się w C:\Users\\< nazwa_użytkownika\>\Documents\AzureML.

W systemie macOS projektu znajduje się w /home/\<username \> /dokumenty/usługi Azure ml.

W przyszłej wersji planujemy ulepszanie funkcji, aby wybrać folder docelowy. 

> [!NOTE]
> Jeśli masz folder w katalogu usługi Machine Learning, który ma dokładnie samą nazwę jak projektu, pliki do pobrania kończy się niepowodzeniem. Aby obejść ten problem, tymczasowo zmień nazwę istniejącego folderu.


### <a name="work-on-the-downloaded-project"></a>Pracować nad projektem pobrany 
Nowo pobranego projektu odzwierciedla stan projektu w ostatnim uruchomieniu w projekcie. Migawka stanu projektu jest automatycznie zatwierdzone w gałęzi historii uruchamiania w repozytorium DevOps Git platformy Azure, za każdym razem, gdy prześlesz przebiegu. Migawkę, która jest skojarzona z najnowszą wykonywania jest używana do utworzenia wystąpienia projektu na drugim komputerze. 
 

## <a name="collaboration"></a>Współpraca
Możesz współpracować z członkami zespołu nad projektami, które są połączone z repozytorium DevOps Git platformy Azure. Uprawnienia można przypisać do użytkowników dla konta eksperymentowania w usłudze Machine Learning, obszaru roboczego i projektu. Obecnie można wykonać polecenia usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure. Można również użyć [witryny Azure portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [użyj witryny Azure portal, aby dodać użytkowników](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Użyj wiersza polecenia, aby dodać użytkowników
Na przykład Alicja jest właściciel projektu iris. Alice chce udostępnić uprawnienia dostępu do projektu przy użyciu Roberta. 

Wybiera Alicja **pliku** menu, a następnie wybiera **polecenia** elementu menu. Otwiera okno wiersza polecenia z projektu iris. Alicja możesz zdecydować, jaki poziom dostępu chce zapewnić do niego. Użytkownik przyznaje uprawnienia, wykonując następujące polecenia:  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

Po przypisaniu roli bezpośrednio lub przez dziedziczenie, Robert zobaczyć projektu na liście projektu Machine Learning Workbench. Bob może być konieczne ponowne uruchomienie aplikacji, aby zobaczyć projektu. Bob może następnie pobrać projektu, zgodnie z opisem w [roamingu](#roaming)i rozpocząć współpracę z Alicji. 

Historii uruchamiania dla wszystkich użytkowników, którzy współpracować nad projektem jest zaangażowana w tym samym zdalnego repozytorium Git. Gdy Alicja przebiegu, Robert można zobaczyć przebiegu w sekcji historii uruchamiania projektu w aplikacji Machine Learning Workbench. Bob można przywrócić projektu do stanu Każde uruchomienie, w tym uruchomień Alicja pracę. 

Udostępniając zdalnego repozytorium Git dla projektu, Alice i Bob mogą także współpracować w gałęzi głównej. Jeśli to konieczne, mogą także tworzyć gałęzie osobistych i użyć i scalenia żądania ściągnięcia usługi Git do współpracy. 

### <a name="use-the-azure-portal-to-add-users"></a>Użyj witryny Azure portal, aby dodać użytkowników
<a name="portal"></a>

Konta eksperymentowanie w usłudze Learning komputera, obszary robocze oraz projekty są zasobami usługi Azure Resource Manager. Aby przypisać role, można użyć **kontroli dostępu** łącze w [witryny Azure portal](https://portal.azure.com). 

Znajdź zasób, który chcesz dodać użytkowników za pomocą **wszystkie zasoby** widoku. Wybierz **kontrola dostępu (IAM)** połączyć, a następnie wybierz pozycję **dodawania użytkowników**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Przykładowy przepływ pracy z współpracy
Aby zilustrować przepływu pracy zespołowej, Przejdźmy przykładem. Pracownicy firmy Contoso, Alice i Bob będzie prowadzona współpraca nad projektem nauki o danych za pomocą aplikacji Machine Learning Workbench. Swoją tożsamość, należą do tej samej dzierżawie Contoso usługi Azure Active Directory (Azure AD). Poniżej przedstawiono kroki, Alice i Bob wykonać:

1. Gdy Alicja tworzy puste repozytorium Git projektu DevOps platformy Azure. Projekt DevOps platformy Azure powinna mieć subskrypcję platformy Azure, który jest tworzony w dzierżawie firmy Contoso w usłudze Azure AD. 

2. Gdy Alicja tworzy konta eksperymentowanie w usłudze Machine Learning, obszaru roboczego i projektu aplikacji Machine Learning Workbench na swoim komputerze. Gdy użytkownik tworzy projekt, wprowadzi adres URL repozytorium DevOps Git platformy Azure.

3. Alicja zostanie uruchomiony do pracy nad projektem. Użytkownik tworzy niektóre skrypty i wykonuje kilka uruchomień. Dla każdego uruchomienia migawka całego folderu projektu zostanie automatycznie przypisany jako zatwierdzenia gałęzi historii uruchamiania w repozytorium DevOps Git platformy Azure, który tworzy Machine Learning Workbench.

4. Alicja jest zadowolony z pracy w toku. Chce zatwierdzić swoje zmiany w lokalnej gałęzi głównej, a następnie Wypychaj je do głównej gałęzi repozytorium DevOps Git platformy Azure. Po otwarciu projektu w aplikacji Machine Learning Workbench ona zostanie otwarte okno wiersza polecenia, a następnie wprowadź następujące polecenia:
    
    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Azure DevOps.
    $ git push
    ```

5. Anna dodaje Roberta do obszaru roboczego jako współautora. Marcela można to zrobić w witrynie Azure portal lub za pomocą `az role assignment` polecenia, jak pokazano wcześniej. Alicja udziela również Bob odczytu/zapisu uprawnienia do repozytorium DevOps Git platformy Azure.

6. Bob loguje się do aplikacji Machine Learning Workbench na swoim komputerze. Obszar roboczy, który Alicja udostępnionego przez sumita programu. Widzi projektu iris w ramach tego obszaru roboczego na liście. 

7. Bob wybiera nazwę projektu. Projekt zostanie pobrana do komputera.
    * Pliki z pobranego projektu są kopii migawki wykonywania najnowsze, są rejestrowane w historii uruchamiania. Nie są one ostatniego zatwierdzenia w gałęzi głównej.
    * Folder lokalny projekt jest ustawiony na gałąź główna nieprzygotowanych zmian.

8. Przeglądać przebiegi, które zostały wykonane przez Alice Bob. ADAM można przywrócić migawki żadnych starszych przebiegów.

9. Robert chce, aby pobrać najnowsze zmiany, które wypchnięcie przez Alice, a następnie rozpocząć pracę w innej gałęzi. W aplikacji Machine Learning Workbench Robert otwiera okno wiersza polecenia i wykonuje następujące polecenia:

    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Azure DevOps Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob modyfikuje projekt, a także przesyła nowe przebiegi. Zmiany zostaną wprowadzone w gałęzi Roberta. Działa przez Boba również stają się widoczne dla Alicji.

11. Robert jest gotowy wypchnąć swoje zmiany do zdalnego repozytorium Git. Aby uniknąć konfliktu z gałęzią master, gdzie działa Alice Bob wypycha swojej pracy do nowej gałęzi zdalnej, która jest również określany bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Azure DevOps Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Poinformuj Alicja o ciekawe nowe rozwiązanie działało w swoim kodzie, Bob tworzy żądania ściągnięcia w zdalnym repozytorium Git z gałęzi bob do głównej gałęzi. Alicja może następnie scalania żądania ściągnięcia z gałęzią główną.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [przy użyciu repozytorium Git z projektem aplikacji Machine Learning Workbench](using-git-ml-project.md).
