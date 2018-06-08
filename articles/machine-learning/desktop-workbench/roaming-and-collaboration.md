---
title: Roaming i współpracy w konsoli usługi Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować roamingu i współpracy w Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: dd83fe3659779e7d49c36e6d906cc3b8019099a1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834214"
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming i współpracy w konsoli usługi Azure Machine Learning Workbench
W tym artykule opisano, jak można użyć usługi Azure Machine Learning Workbench do ustawiania projektów dla mobilnych między komputerami i współpracę z członków zespołu. 

Podczas tworzenia projektu usługi Azure Machine Learning, który ma zdalnego łącza (repozytorium) repozytorium Git metadanych projektu i migawki są przechowywane w chmurze. Łącze chmury umożliwia dostęp do projektu z innego komputera (mobilnych). Mogą również współpracować członkom zespołu, dając im dostęp do projektu. 

## <a name="prerequisites"></a>Wymagania wstępne
1. Zainstaluj aplikację Workbench uczenia maszynowego. Upewnij się, że masz dostęp do konta usługi Azure Machine Learning eksperymenty. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji](../service/quickstart-installation.md).

2. Dostępu [Visual Studio Team Services](https://www.visualstudio.com) (Team Services), a następnie utwórz projekt do repozytorium. Aby uzyskać więcej informacji, zobacz [przy użyciu repozytorium Git w usłudze Machine Learning Workbench projektu](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Utwórz nowy projekt uczenia maszynowego
Otwórz Machine Learning Workbench, a następnie utwórz nowy projekt (na przykład projekt o nazwie iris). W **adres URL repozytorium GIT Visualstudio.com** Wprowadź prawidłowy adres URL repozytorium Git programu Team Services. 

> [!IMPORTANT]
> Jeśli wybierzesz szablon projektu puste repozytorium Git, którego chcesz użyć mogą już mieć gałęzi głównej. Uczenie maszynowe klonów po prostu lokalnie gałęzi głównej. Dodaje aml_config folder i inne pliki metadanych projektu do folderu lokalnego projektu. 
>
> Jeśli wybierzesz każdego innego szablonu projektu, z repozytorium Git *nie* już gałęzi głównej. Jeśli tak, zostanie wyświetlony błąd. Alternatywą jest użycie `az ml project create` polecenie, aby utworzyć projekt, z `--force` przełącznika. To usuwa pliki w oryginalnej gałęzi głównej i zastępuje je nowe pliki w szablonie, który można wybrać.

Po utworzeniu projektu, należy przesłać kilka działa na wszystkie skrypty, które znajdują się w projekcie. Ta akcja zatwierdza stan projektu do gałęzi Historia uruchomień zdalnego repozytorium Git. 

> [!NOTE] 
> Tylko skrypt jest wykonywany wyzwalacz zatwierdzeń do gałęzi Historia uruchomień. Przygotowywanie danych wykonywania i notesu uruchamia nie wyzwolenia migawki projektu w gałęzi Historia uruchomień.

Jeśli skonfigurowano uwierzytelnianie Git, można także używać w gałęzi głównej. Alternatywnie można utworzyć nowej gałęzi. 

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

## <a name="roaming"></a>Mobilny
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Otwórz Machine Learning Workbench na drugim komputerze
Po repozytorium Git programu Team Services jest połączony z projektem, dostępne projektu iris z dowolnego komputera, który ma Machine Learning Workbench zainstalowane. 

Dostęp do projektu iris na innym komputerze, należy zalogować się do aplikacji przy użyciu tych samych poświadczeń, które pozwala utworzyć projekt. Należy również należeć do tego samego konta Machine Learning eksperymenty i obszar roboczy. Projekt iris alfabetycznie znajduje się z innymi projektami w obszarze roboczym. 

### <a name="download-the-project-on-a-second-computer"></a>Pobieranie projektu na innym komputerze
Gdy obszar roboczy jest otwarty na drugim komputerze, ikona obok projektu iris różni się od ikona typowy folder. Ikona pobierania wskazuje, że zawartość projektu jest w chmurze i czy projekt jest gotowe do pobrania na bieżącym komputerze. 

![Tworzenie projektu](./media/roaming-and-collaboration/downloadable-project.png)

Wybierz projekt iris, aby rozpocząć pobieranie. Po ukończeniu pobierania projekt jest gotowy do uzyskiwać na drugim komputerze. 

W systemie Windows, projekt znajduje się w C:\Users\\< nazwa_użytkownika\>\Documents\AzureML.

Na macOS, projekt znajduje się w /home/\<username \> /dokumenty/uczenie maszynowe Azure.

W przyszłym wydaniu planujemy ulepszanie funkcji, dzięki czemu można wybrać folder docelowy. 

> [!NOTE]
> Jeśli istnieje folder w katalogu uczenia maszynowego, który ma dokładnie samą nazwę jak projekt, nie powiedzie się pobieranie. Aby obejść ten problem, tymczasowo zmień nazwę istniejącego folderu.


### <a name="work-on-the-downloaded-project"></a>Pobrany projektu 
Nowo pobranego projektu odzwierciedla stan projektu ostatniego wykonywania w projekcie. Migawka stanu projektu jest automatycznie zatwierdzone do wykonywania historii gałęzi w repozytorium Git programu Team Services za każdym razem, gdy przesłać Uruchom. Migawki, która jest skojarzona z najnowszą Uruchom służy do tworzenia wystąpienia projektu na drugim komputerze. 
 

## <a name="collaboration"></a>Współpraca
Umożliwia współpracę z członków zespołu w projektach, które są połączone z repozytorium Git programu Team Services. Można przypisać uprawnienia dla użytkowników konto Machine Learning eksperymenty, obszar roboczy i projektu. Obecnie można wykonać polecenia usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure. Można również użyć [portalu Azure](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Użyj portalu Azure, aby dodać użytkowników](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Użyj wiersza polecenia, aby dodać użytkowników
Na przykład Alicja jest właścicielem iris projektu. Alicja chce udzielać dostępu do projektu z niego. 

Wybiera Alicja **pliku** menu, a następnie wybiera **wiersza polecenia** elementu menu. W projekcie iris zostanie otwarte okno wiersza polecenia. Alicja następnie można zdecydować, jakiego poziomu dostępu chce zapewnić do niego. Przyznaje ona uprawnienia, wykonując następujące polecenia:  

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

Po przypisania roli bezpośrednio lub przez dziedziczenie, Roberta, który można zobaczyć projektu w liście projektu Machine Learning Workbench. Bob może wymagać ponownego uruchomienia aplikacji, aby zobaczyć projektu. Roberta, który może pobierać projekt zgodnie z opisem w [mobilny](#roaming)i rozpocząć współpracę z Alicji. 

Historia uruchomień dla wszystkich użytkowników, którzy współpracować nad projektem dba o tej samej zdalnego repozytorium Git. Podczas Alicja Uruchom, Bob widzi Uruchom w sekcji Historia uruchomień projektu w aplikacji Machine Learning Workbench. Robert można także przywrócić projektu do stanu żadnych wykonywania uruchomień Alicja uruchomione w tym. 

Za pomocą udostępniania zdalnego repozytorium Git dla projektu, Alicja i Robert mogą również współpracować w gałęzi głównej. W razie potrzeby mogą także tworzyć osobiste gałęzie i umożliwia współpracę żądań ściągnięcia Git i scalenia. 

### <a name="use-the-azure-portal-to-add-users"></a>Użyj portalu Azure, aby dodać użytkowników
<a name="portal"></a>

Konta komputera Learning eksperymenty, obszarami roboczymi i projekty są zasoby usługi Azure Resource Manager. Aby przypisać role, można użyć **kontroli dostępu** łącze w [portalu Azure](https://portal.azure.com). 

Znajdź zasób, który chcesz dodać użytkowników przy użyciu **wszystkie zasoby** widoku. Wybierz **(IAM) kontroli dostępu** łącza, a następnie wybierz **dodawania użytkowników**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Przykładowy przepływ pracy współpracy
Aby zilustrować przepływu pracy współpracy, Przejdźmy przykładem. Pracownicy firmy Contoso Alicja i Robert chcesz współpracować nad projektem nauki danych przy użyciu Machine Learning Workbench. Tożsamość należą do tej samej dzierżawie Contoso usługi Azure Active Directory (Azure AD). Poniżej przedstawiono kroki Alicja i Robert wykonać:

1. Alicja tworzy pustego repozytorium Git w projekcie usługi Team Services. Projekt usługi Team Services musi należeć do subskrypcji Azure, która jest tworzona w ramach dzierżawy firmy Contoso w usłudze Azure AD. 

2. Alicja tworzy konto Machine Learning eksperymenty, obszar roboczy i projektu Machine Learning Workbench na swoim komputerze. Gdy użytkownik tworzy projekt, wprowadza tekst adres URL repozytorium Git programu Team Services.

3. Alicja zostanie uruchomiony do projektu. Użytkownik tworzy niektóre skrypty i wykonuje kilka działa. Przy każdym uruchomieniu migawki folderu cały projekt zostanie automatycznie przypisany jako zatwierdzenia Historia uruchomień gałęzi repozytorium Git programu Team Services, która tworzy Machine Learning Workbench.

4. Alicja jest wszystkiego o pracy w toku. Chce przekazać swoje zmiany w lokalnej gałęzi głównej, a następnie Wypchnij je do głównej gałęzi repozytorium Git programu Team Services. Po otwarciu projektu w Machine Learning Workbench użytkownik zostanie otwarte okno wiersza polecenia, a następnie wprowadza następujące polecenia:
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alicja dodaje Roberta do obszaru roboczego jako współautor. Użytkownik można to zrobić w portalu Azure lub za pomocą `az role assignment` polecenia, jak pokazano wcześniej. Alicja również przyznaje uprawnienia do repozytorium Git programu Team Services Bob odczytu/zapisu.

6. Roberta, który loguje się do Machine Learning Workbench na swój komputer. On widoczny obszar roboczy, który Alicja udostępnionych z nim. Programu project iris wymienionych w tym obszarze roboczym. 

7. Robert wybiera nazwę projektu. Projekt zostanie pobrana do komputera.
    * Pliki z pobranego projektu są kopią migawki najnowsze uruchomienia umożliwiającego jest rejestrowana w historii uruchamiania. Nie są one ostatniego zatwierdzenia dla gałęzi głównej.
    * Folder lokalny projektu ustawiono do głównej gałęzi, z nieprzygotowanych zmian.

8. Robert można przeglądać przebiegi, które zostały wykonane przez Alicji. Może on przywracaj migawek żadnych starszych uruchomień.

9. Robert chce pobrać najnowsze zmiany, które Alicja naciśnięty i ponownie uruchomić pracę w innej gałęzi. Machine Learning Workbench Robert otwiera okno wiersza polecenia i wykonuje następujące polecenia:

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Robert modyfikuje projektu i przesyła nowe przebiegi. Zmiany zostały wprowadzone w gałęzi Roberta. Uruchamia Roberta również staną się widoczne dla Alicji.

11. Robert jest gotowy do Wypchnij zmiany jego do zdalnego repozytorium Git. Aby uniknąć konfliktu z gałęzi głównej, gdzie działa Alicji, Roberta wypychanie pracy do nowej gałęzi zdalnej, która nosi również bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Poinformuj Alicja o chłodnych lewy nowy w jego kod, Roberta, który tworzy żądanie ściągnięcia na zdalnego repozytorium Git w gałęzi Roberta do głównej gałęzi. Alicja może następnie scalić żądania ściągnięcia w gałęzi głównej.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [przy użyciu repozytorium Git w usłudze Machine Learning Workbench projektu](using-git-ml-project.md).
