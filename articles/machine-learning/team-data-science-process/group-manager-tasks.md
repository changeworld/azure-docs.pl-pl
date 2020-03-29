---
title: Zadania menedżera grupy team data science process
description: Postępuj zgodnie z tym szczegółowym instruktażem zadań, które menedżer grupy kończy w projekcie zespołu nauki o danych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721357"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Zadania menedżera grupy team data science process

W tym artykule opisano zadania wykonywane przez *menedżera grupy* dla organizacji do nauki o danych. Menedżer grupy zarządza całą jednostką do nauki o danych w przedsiębiorstwie. Jednostka do nauki o danych może mieć kilka zespołów, z których każdy pracuje nad wieloma projektami nauki o danych w różnych branżach biznesowych. Celem menedżera grupy jest ustanowienie środowiska grupy współpracy, które standaryzuje [proces nauki o danych zespołu](overview.md) (TDSP). Aby uzyskać zarys wszystkich ról personelu i skojarzonych zadań obsługiwanych przez zespół analityki danych standaryzujący w programie TDSP, zobacz [Role i zadania procesu nauki o danych zespołowych](roles-tasks.md).

Na poniższym diagramie przedstawiono sześć głównych zadań konfiguracji menedżera grupy. Menedżerowie grup mogą delegować swoje zadania do surogatów, ale zadania skojarzone z rolą nie zmieniają się.

![Zadania menedżera grupy](./media/group-manager-tasks/tdsp-group-manager.png)

1. Konfigurowanie **organizacji Azure DevOps** dla tej grupy.
2. Utwórz domyślny **projekt GroupCommon** w organizacji Azure DevOps.
3. Utwórz repozytorium **GroupProjectTemplate** w usłudze Azure Reppose.
4. Utwórz repozytorium **narzędzi grupy** w repozytorium platformy Azure.
5. Zaimportuj zawartość repozytoriów **ProjectTemplate** i **Utilities** zespołu Microsoft TDSP do wspólnych repozytoriów grupy.
6. Skonfiguruj **członkostwo** i **uprawnienia** dla członków zespołu, aby uzyskać dostęp do grupy.

Poniższy samouczek przechodzi przez kroki w szczegółach. 

> [!NOTE] 
> W tym artykule użyto usługi Azure DevOps do skonfigurowania środowiska grupy TDSP, ponieważ tak jest, jak zaimplementować TDSP w firmie Microsoft. Jeśli grupa korzysta z innych platform hostingowych lub programistycznych, zadania Menedżera grupy są takie same, ale sposób ich ukończenia może być inny.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Tworzenie organizacji i projektu w usłudze Azure DevOps

1. Przejdź do [visualstudio.microsoft.com](https://visualstudio.microsoft.com), wybierz pozycję **Zaloguj się w** prawym górnym rogu i zaloguj się do swojego konta Microsoft. 
   
   ![Logowanie się do konta Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Jeśli nie masz konta Microsoft, wybierz pozycję **Zarejestruj się teraz**, utwórz konto Microsoft i zaloguj się przy użyciu tego konta. Jeśli twoja organizacja ma subskrypcję programu Visual Studio, zaloguj się przy użyciu poświadczeń dla tej subskrypcji.
   
1. Po zalogowaniu się w prawym górnym rogu na stronie Azure DevOps wybierz pozycję **Utwórz nową organizację**.
   
   ![Tworzenie nowej organizacji](./media/group-manager-tasks/create-organization.png)
   
1. Jeśli zostanie wyświetlony monit o wyrażenie zgody na Warunki korzystania z usługi, Zasady zachowania poufności informacji i Kodeks postępowania, wybierz opcję **Kontynuuj.**
   
1. W oknie dialogowym rejestracji nazwij organizację Programu Azure DevOps i zaakceptuj przypisanie regionu hosta lub rozwijano i wybierz inny region. Następnie wybierz pozycję **Kontynuuj**. 

1. W obszarze **Tworzenie projektu do rozpoczęcia**wprowadź pozycję *GroupCommon*, a następnie wybierz pozycję **Utwórz projekt**. 
   
   ![Tworzenie projektu](./media/group-manager-tasks/create-project.png)

Zostanie otwarta strona **Podsumowanie** projektu **GroupCommon.** Adres URL strony to *https:\//\<nazwa serwera\<>/ nazwa organizacji>/GroupCommon*.

![Strona podsumowania projektu](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Konfigurowanie wspólnych repozytoriów grupy

Repozytorium platformy Azure obsługuje następujące typy repozytoriów dla twojej grupy:

- **Wspólne repozytoria**grup: Repozytoria ogólnego przeznaczenia, które wiele zespołów w jednostce do nauki o danych może przyjąć dla wielu projektów do nauki o danych. 
- **Repozytoria zespołów:** Repozytoria dla określonych zespołów w jednostce do nauki o danych. Te repozytoria są specyficzne dla potrzeb zespołu i mogą być używane dla wielu projektów w tym zespole, ale nie są wystarczająco ogólne, aby być używane w wielu zespołach w jednostce do nauki o danych.
- **Repozytoria projektów:** Repozytoria dla określonych projektów. Takie repozytoria mogą nie być wystarczająco ogólne dla wielu projektów w zespole lub dla innych zespołów w jednostce do nauki o danych.

Aby skonfigurować wspólne repozytoria grupy w projekcie, należy: 
- Zmienianie nazwy domyślnego repozytorium **GroupCommon** na **Platformę GroupProjectTemplate**
- Tworzenie nowego repozytorium **narzędzi grupy**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Zmienianie nazwy domyślnego repozytorium projektu na platformę GroupProjectTemplate

Aby zmienić nazwę domyślnego repozytorium projektu **GroupCommon** na **Platformę Projektu GroupProjectTemplate:**

1. Na stronie **Podsumowanie** projektu **GroupCommon** wybierz pozycję **Repozytoria**. Ta akcja prowadzi do domyślnego repozytorium **GroupCommon** projektu GroupCommon, który jest obecnie pusty.
   
1. U góry strony rozwijana jest strzałka obok **pozycji GroupCommon** i wybierz pozycję **Zarządzaj repozytoriami**.
   
   ![Zarządzanie repozytoriami](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na stronie **Ustawienia projektu** wybierz **pozycję ...** obok pozycji **GroupCommon**, a następnie wybierz pozycję **Zmień nazwę repozytorium**. 
   
   ![Wybierz... a następnie wybierz pozycję Zmień nazwę repozytorium](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. W okienku **wyboru Zmień nazwę repozytorium GroupCommon** wprowadź *platformę GroupProjectTemplate*, a następnie wybierz pozycję **Zmień nazwę**. 
   
   ![Zmienianie nazwy repozytorium](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Tworzenie repozytorium narzędzia grupy

Aby utworzyć repozytorium **Narzędzia grupowe:**

1. Na stronie **Podsumowanie** projektu **GroupCommon** wybierz pozycję **Repozytoria**. 
   
1. U góry strony w dół strzałka obok **pozycji GroupProjectTemplate** i wybierz pozycję **Nowe repozytorium**.
   
   ![Wybierz nowe repozytorium](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. W oknie **dialogowym Tworzenie nowego repozytorium** wybierz pozycję **Git** jako **typ**, wprowadź *narzędzia grupy* jako **nazwę repozytorium,** a następnie wybierz pozycję **Utwórz**.
   
   ![Tworzenie repozytorium narzędzi grupy](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na stronie **Ustawienia projektu** wybierz **pozycję Repozytoria** w obszarze **Repozytoria** w lewej nawigacji, aby wyświetlić dwa repozytoria grup: **GroupProjectTemplate** i **GroupUtilities**.
   
   ![Dwa repozytoria grupowe](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importowanie repozytoriów zespołu Microsoft TDSP

W tej części samouczka należy zaimportować zawartość repozytoriów **ProjectTemplate** i **Utilities** zarządzanych przez zespół Microsoft TDSP do repozytoriów **GroupProjectTemplate** i **GroupUtilities.** 

Aby zaimportować repozytoria zespołu TDSP:

1. Na stronie głównej projektu **GroupCommon** wybierz **pozycję Repozytoria** w lewej nawigacji. Zostanie otwarte domyślne repozytorium **GroupProjectTemplate.** 
   
1. Na stronie **GroupProjectTemplate jest pusta** strona wybierz pozycję **Importuj**. 
   
   ![Wybierz pozycję Importuj](./media/group-manager-tasks/import-repo.png)
   
1. W oknie **dialogowym Importowanie repozytorium Git** wybierz pozycję **Git** jako **typ źródła**i wprowadź *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* dla adresu URL **klonowania**. Następnie wybierz pozycję **Importuj**. Zawartość repozytorium ProjectTemplate zespołu Microsoft TDSP są importowane do repozytorium GroupProjectTemplate. 
   
   ![Importowanie repozytorium zespołu TDSP firmy Microsoft](./media/group-manager-tasks/import-repo-2.png)
   
1. U góry strony **Repozytorium** rozwijana i wybierz repozytorium **narzędzi grupy.**
   
1. Powtórz proces importowania, aby zaimportować zawartość repozytorium **narzędzi** zespołu Microsoft TDSP, *https:\//github.com/Azure/Azure-TDSP-Utilities.git*, do repozytorium **narzędzi grupy.** 
   
Każde z twoich dwóch repozytoriów grup zawiera teraz wszystkie pliki, z wyjątkiem tych w katalogu *.git,* z odpowiedniego repozytorium zespołu Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Dostosowywanie zawartości repozytoriów grup

Jeśli chcesz dostosować zawartość repozytoriów grup do określonych potrzeb grupy, możesz to zrobić teraz. Można zmodyfikować pliki, zmienić strukturę katalogów lub dodać pliki opracowane przez grupę lub przydatne dla grupy.

### <a name="make-changes-in-azure-repos"></a>Wprowadzanie zmian w repo platformy Azure

Aby dostosować zawartość repozytorium:

1. Na stronie **Podsumowanie** projektu **GroupCommon** wybierz pozycję **Repozytoria**. 
   
1. U góry strony wybierz repozytorium, które chcesz dostosować.

1. W strukturze katalogu repozytorium przejdź do folderu lub pliku, który chcesz zmienić. 
   
   - Aby utworzyć nowe foldery lub pliki, zaznacz strzałkę obok **pozycji Nowy**. 
     
     ![Utwórz nowy plik](./media/group-manager-tasks/new-file.png)
     
   - Aby przekazać pliki, wybierz pozycję **Przekaż pliki**. 
     
     ![Przekazywanie plików](./media/group-manager-tasks/upload-files.png)
     
   - Aby edytować istniejące pliki, przejdź do pliku, a następnie wybierz pozycję **Edytuj**. 
     
     ![Edytowanie pliku](./media/group-manager-tasks/edit-file.png)
     
1. Po dodaniu lub edycji plików wybierz pozycję **Zatwierdź**.
   
   ![Zatwierdzanie zmian](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Wprowadzanie zmian za pomocą komputera lokalnego lub dsvm

Jeśli chcesz wprowadzić zmiany przy użyciu komputera lokalnego lub DSVM i wypchnąć zmiany do repozytoriów grup, upewnij się, że masz wymagania wstępne do pracy z git i dsvms:

- Subskrypcja platformy Azure, jeśli chcesz utworzyć DSVM.
- Git zainstalowany na komputerze. Jeśli używasz DSVM, Git jest wstępnie zainstalowany. W przeciwnym razie zobacz [załącznik Platformy i narzędzia](platforms-and-tools.md#appendix).
- Jeśli chcesz użyć DSVM, Windows lub Linux DSVM utworzone i skonfigurowane na platformie Azure. Aby uzyskać więcej informacji i instrukcji, zobacz [dokumentację maszyny wirtualnej do nauki o danych](/azure/machine-learning/data-science-virtual-machine/).
- W przypadku systemu Windows DSVM, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowany na komputerze. W pliku *README.md* przewiń w dół do sekcji **Pobierz i zainstaluj** i wybierz najnowszy **instalator**. Pobierz instalator *.exe* ze strony instalatora i uruchom go. 
- W przypadku systemu Linux DSVM klucz publiczny SSH skonfigurowany na maszynie DSVM i dodany w usłudze Azure DevOps. Aby uzyskać więcej informacji i instrukcji, zobacz sekcję **Tworzenie klucza publicznego SSH** w [dodatku Platformy i narzędzia](platforms-and-tools.md#appendix). 

Najpierw skopiuj lub *sklonuj* repozytorium na komputerze lokalnym. 
   
1. Na stronie **Podsumowanie** projektu **GroupCommon** wybierz pozycję **Repozytoria**, a u góry strony wybierz repozytorium, które chcesz sklonować.
   
1. Na stronie repozytorium wybierz pozycję **Klonuj** w prawym górnym rogu.
   
1. W oknie dialogowym **Klonowanie repozytorium** wybierz pozycję **HTTPS** dla połączenia HTTP lub **SSH** dla połączenia SSH i skopiuj adres URL klonu w **obszarze Wiersz polecenia** do schowka.
   
   ![Repo klonowania](./media/group-manager-tasks/clone.png)
   
1. Na komputerze lokalnym utwórz następujące katalogi:
   
   - Dla systemu Windows: **C:\GitRepos\GroupCommon**
   - Dla Linuksa, **$/GitRepos/GroupCommon** w katalogu domowym 
   
1. Zmień katalog utworzony.
   
1. W Git Bash uruchom polecenie`git clone <clone URL>.`
   
   Na przykład, którekolwiek z następujących poleceń klonuje repozytorium **narzędzi grupy** do katalogu *GroupCommon* na komputerze lokalnym. 
   
   **Połączenie HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Połączenie SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Po dokonaniu zmian, które chcesz w lokalnym klon repozytorium, można wypchnąć zmiany do wspólnej repozytoriów grupy udostępnionej. 

Uruchom następujące polecenia Git Bash z lokalnego katalogu **GroupProjectTemplate** lub **GroupUtilities.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Jeśli po raz pierwszy zaobserwujesz się do repozytorium Git, może być konieczne skonfigurowanie `git commit` globalnych parametrów *user.name* i *user.email* przed uruchomieniem polecenia. Uruchom następujące dwa polecenia:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Jeśli zobowiązujesz się do kilku repozytoriów Git, użyj tej samej nazwy i adresu e-mail dla wszystkich z nich. Używanie tej samej nazwy i adresu e-mail jest wygodne podczas tworzenia pulpitów nawigacyjnych usługi Power BI w celu śledzenia działań Git w wielu repozytoriach.

## <a name="add-group-members-and-configure-permissions"></a>Dodawanie członków grupy i konfigurowanie uprawnień

Aby dodać członków do grupy:

1. W usłudze Azure DevOps na stronie głównej projektu **GroupCommon** wybierz **ustawienia projektu** z lewej strony nawigacji. 
   
1. W ustawieniach **projektu** w lewo wybierz **pozycję Zespoły**, a następnie na stronie **Zespoły** wybierz **groupcommon team**. 
   
   ![Konfigurowanie zespołów](./media/group-manager-tasks/teams.png)
   
1. Na stronie **Profil zespołu** wybierz pozycję **Dodaj**.
   
   ![Dodaj do GroupCommon Team](./media/group-manager-tasks/add-to-team.png)
   
1. W oknie dialogowym **Dodawanie użytkowników i grup** wyszukaj i wybierz członków, aby dodać do grupy, a następnie wybierz pozycję Zapisz **zmiany**. 
   
   ![Dodawanie użytkowników i grup](./media/group-manager-tasks/add-users.png)
   

Aby skonfigurować uprawnienia dla elementów członkowskich:

1. W obszarze **Ustawienia projektu** w lewo wybierz pozycję **Uprawnienia**. 
   
1. Na stronie **Uprawnienia** wybierz grupę, do której chcesz dodać członków. 
   
1. Na stronie dla tej grupy wybierz pozycję **Członkowie**, a następnie wybierz pozycję **Dodaj**. 
   
1. W wyskakującym okienku **Zaproś członków** wyszukaj i wybierz członków, aby dodać do grupy, a następnie wybierz pozycję **Zapisz**. 
   
   ![Udzielanie uprawnień członkom](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Następne kroki

Oto linki do szczegółowych opisów innych ról i zadań w procesie nauki o danych zespołu:

- [Zadania zespołu Lead dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania prowadzące projekt dla zespołu do nauki o danych](project-lead-tasks.md)
- [Zadania indywidualnego współautora projektu dla zespołu do nauki o danych](project-ic-tasks.md)
