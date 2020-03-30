---
title: Zadania dla zespołu prowadzącego w zespole zespołu ds.
description: Szczegółowy przewodnik po zadaniach dla kierownika zespołu w zespole zespołu proces nauki o danych
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864285"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Zadania dla zespołu prowadzą zespół zespołu ds.

W tym artykule opisano zadania, które *zespół prowadzić* kończy dla ich zespołu nauki o danych. Celem zespołu jest stworzenie środowiska zespołu współpracy, które standaryzuje [proces nauki o danych zespołu](overview.md) (TDSP). TDSP ma na celu poprawę współpracy i uczenia się zespołowego. 

TDSP to zwinna, iteracyjna metodologia nauki o danych, która skutecznie dostarcza rozwiązania do analizy predykcyjnej i inteligentne aplikacje. Proces destyluje najlepsze praktyki i struktury firmy Microsoft i branży.  Celem jest skuteczne wdrożenie inicjatyw analityki danych i pełne wykorzystanie korzyści płynących z ich programów analitycznych. Aby uzyskać zarys ról personelu i skojarzonych zadań dla zespołu nauki o danych standaryzujących na TDSP, zobacz [Role i zadania procesu nauki o danych zespołu](roles-tasks.md).

Kierownik zespołu zarządza zespołem składającym się z kilku analityków danych w jednostce do nauki o danych przedsiębiorstwa. W zależności od wielkości i struktury jednostki do nauki o danych [menedżer grupy](group-manager-tasks.md) i kierownik zespołu mogą być tą samą osobą lub mogą delegować swoje zadania do surogatów. Ale same zadania się nie zmieniają. 

Na poniższym diagramie przedstawiono przepływ pracy dla zadań, które zespół prowadzi w celu skonfigurowania środowiska zespołowego:

![Przepływ pracy zadania potencjalnego klienta zespołu](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Utwórz **projekt zespołowy** w organizacji grupy w usłudze Azure DevOps. 
  
1. Zmień nazwę domyślnego repozytorium zespołu na **Narzędzia zespołu**.
  
1. Utwórz nowe repozytorium **TeamTemplate** w projekcie zespołowym. 
  
1. Zaimportuj zawartość repozytoriów **narzędzi grupy** i **GroupProjectTemplate** do repozytoriów **narzędzi zespołu** i **teamtemplate.** 
  
1. Skonfiguruj **kontrolę zabezpieczeń,** dodając członków zespołu i konfigurując ich uprawnienia.
  
1. W razie potrzeby utwórz dane zespołu i zasoby analityczne:
   - Dodaj narzędzia specyficzne dla zespołu do repozytorium **TeamUtilities.** 
   - Utwórz **magazyn plików platformy Azure** do przechowywania zasobów danych, które mogą być przydatne dla całego zespołu. 
   - Zainstaluj magazyn plików platformy Azure na **maszynie wirtualnej** do nauki o danych (DSVM) zespołu i dodaj do niego zasoby danych.

Poniższy samouczek przechodzi przez kroki w szczegółach.

> [!NOTE] 
> W tym artykule użyto usługi Azure DevOps i DSVM do skonfigurowania środowiska zespołu TDSP, ponieważ tak jest, jak zaimplementować TDSP w firmie Microsoft. Jeśli twój zespół używa innych platform hostingu kodu lub deweloperów, zadania prowadzące zespół są takie same, ale sposób ich ukończenia może być inny.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że następujące zasoby i uprawnienia zostały skonfigurowane przez [menedżera grupy:](group-manager-tasks.md)

- **Organizacja** Azure DevOps dla twojej jednostki danych
- **Repozytoria Grupprojektu i** **narzędzia grupy** wypełnione zawartością repozytoriów **ProjectTemplate** i **Utilities** zespołu Microsoft TDSP
- Uprawnienia do konta organizacji do tworzenia projektów i repozytoriów dla zespołu

Aby móc klonować repozytoria i modyfikować ich zawartość na komputerze lokalnym lub maszynie DSVM lub skonfigurować magazyn plików platformy Azure i zamontować je w systemie DSVM, potrzebujesz następujących czynności:

- Subskrypcja platformy Azure.
- Git zainstalowany na komputerze. Jeśli używasz DSVM, Git jest wstępnie zainstalowany. W przeciwnym razie zobacz [załącznik Platformy i narzędzia](platforms-and-tools.md#appendix).
- Jeśli chcesz użyć DSVM, Windows lub Linux DSVM utworzone i skonfigurowane na platformie Azure. Aby uzyskać więcej informacji i instrukcji, zobacz [dokumentację maszyny wirtualnej do nauki o danych](/azure/machine-learning/data-science-virtual-machine/).
- W przypadku systemu Windows DSVM, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowany na komputerze. W pliku *README.md* przewiń w dół do sekcji **Pobierz i zainstaluj** i wybierz najnowszy **instalator**. Pobierz instalator *.exe* ze strony instalatora i uruchom go. 
- W przypadku systemu Linux DSVM klucz publiczny SSH skonfigurowany na maszynie DSVM i dodany w usłudze Azure DevOps. Aby uzyskać więcej informacji i instrukcji, zobacz sekcję **Tworzenie klucza publicznego SSH** w [dodatku Platformy i narzędzia](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Tworzenie projektu zespołowego i repozytoriów

W tej sekcji utworzysz następujące zasoby w organizacji Azure DevOps grupy:

- Projekt **MyTeam** w usłudze Azure DevOps
- Repozytorium **TeamTemplate**
- Repozytorium **Narzędzi zespołu**

Nazwy określone dla repozytoriów i katalogów w tym samouczku zakładają, że chcesz ustanowić oddzielny projekt dla własnego zespołu w ramach większej organizacji nauki o danych. Jednak cała grupa może zdecydować się na pracę w ramach jednego projektu utworzonego przez menedżera grupy lub administratora organizacji. Następnie wszystkie zespoły do nauki o danych tworzą repozytoria w ramach tego pojedynczego projektu. Ten scenariusz może być prawidłowy dla:
- Mała grupa do nauki o danych, która nie ma wielu zespołów do nauki o danych. 
- Większa grupa do nauki o danych z wieloma zespołami do nauki o danych, która mimo to chce zoptymalizować współpracę między zespołami za pomocą działań, takich jak planowanie sprintu na poziomie grupy. 

Jeśli zespoły zdecydują się na ich repozytoria specyficzne dla zespołu w ramach jednego projektu grupy, liderzy zespołu należy utworzyć repozytoria o nazwach takich jak * \<TeamName>Szablon* i * \<TeamName>Utilities*. Na przykład: *TeamATemplate* i *TeamAUtilities*. 

W każdym przypadku potencjalni klienci zespołu muszą poinformować członków zespołu, które repozytoria szablonów i narzędzi mają być skonfigurowane i klonowe. Potencjalni klienci projektu powinni wykonać [zadania prowadzące projekt dla zespołu do nauki o danych,](project-lead-tasks.md) aby utworzyć repozytoria projektów, czy to w ramach oddzielnych projektów, czy pojedynczego projektu. 

### <a name="create-the-myteam-project"></a>Tworzenie projektu MyTeam

Aby utworzyć oddzielny projekt dla swojego zespołu:

1. W przeglądarce sieci Web przejdź do strony głównej organizacji Azure DevOps grupy pod adresem URL *https:\//\<nazwa serwera>/\<nazwa organizacji>* i wybierz pozycję Nowy **projekt**. 
   
   ![Wybierz nowy projekt](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. W oknie dialogowym **Tworzenie projektu** wprowadź nazwę zespołu, na przykład *MyTeam*, w obszarze **Nazwa projektu**, a następnie wybierz pozycję **Zaawansowane**. 
   
1. W **obszarze Kontrola wersji**wybierz pozycję **Git**, a następnie w obszarze **Proces elementu roboczego**wybierz pozycję **Agile**. Następnie wybierz pozycję **Utwórz**. 
   
   ![Tworzenie projektu](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Zostanie otwarta strona Podsumowanie projektu **zespołowego** z adresem URL strony *https:\//\<nazwa serwera\<>/ nazwa organizacji>/\<nazwa zespołu>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Zmienianie nazwy domyślnego repozytorium MyTeam na Narzędzia zespołu

1. Na stronie **Podsumowanie** projektu **MyTeam** w obszarze **Jaką usługę chcesz zacząć?**, wybierz **pozycję Repozytorium**. 
   
   ![Wybierz repozytorium](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Na stronie Repozytorium **MyTeam** wybierz repozytorium **MyTeam** u góry strony, a następnie wybierz pozycję **Zarządzaj repozytoriami** z listy rozwijanej. 
   
   ![Wybierz pozycję Zarządzaj repozytoriami](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Na stronie **Ustawienia projektu** wybierz **...** obok repozytorium **MyTeam,** a następnie wybierz pozycję **Zmień nazwę repozytorium**. 
   
   ![Wybierz pozycję Zmień nazwę repozytorium](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. W okienku **wyboru Zmień nazwę repozytorium MyTeam** wprowadź pozycję *Narzędzia zespołu*, a następnie wybierz pozycję **Zmień nazwę**. 

### <a name="create-the-teamtemplate-repository"></a>Tworzenie repozytorium TeamTemplate

1. Na stronie **Ustawienia projektu** wybierz pozycję **Nowe repozytorium.** 
   
   ![Wybierz nowe repozytorium](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Możesz też wybrać **pozycję Repozytorium** z lewej strony **Podsumowanie** projektu **MyTeam,** wybrać repozytorium u góry strony, a następnie wybrać **pozycję Nowe repozytorium** z listy rozwijanej.
   
1. W oknie **dialogowym Tworzenie nowego repozytorium** upewnij się, że w obszarze **Typ**jest zaznaczona opcja **Git** . Wprowadź *teamTemplate* w obszarze **Nazwa repozytorium**, a następnie wybierz pozycję **Utwórz**.
   
   ![Tworzenie repozytorium](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Upewnij się, że można zobaczyć dwa repozytoria **TeamUtilities** i **TeamTemplate** na stronie ustawień projektu. 
   
   ![Dwa repozytoria drużynowe](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importowanie zawartości wspólnych repozytoriów grupy

Aby wypełnić repozytoria zespołu zawartością wspólnych repozytoriów grupy skonfigurowanych przez menedżera grupy:

1. Na stronie głównej projektu **MyTeam** wybierz **pozycję Repozytorium** w lewej nawigacji. Jeśli pojawi się komunikat, że szablon **MyTeam** nie został znaleziony, wybierz łącze w **przeciwnym, przejdź do domyślnego repozytorium TeamTemplate.** 
   
   Zostanie otwarte domyślne repozytorium **TeamTemplate.** 
   
1. Na stronie **Tablica składowa jest pusta,** wybierz pozycję **Importuj**. 
   
   ![Wybierz pozycję Importuj](./media/team-lead-tasks/import-repo.png)
   
1. W oknie **dialogowym Importowanie repozytorium Git** wybierz pozycję **Git** jako **typ źródła**i wprowadź adres URL wspólnego repozytorium szablonów grupy w obszarze **Clone URL**. Adres URL to *\//\<https: nazwa\<serwera>/ nazwa organizacji>/_git/nazwa\<repozytorium>*. Na przykład: *https:\//dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Wybierz pozycję **Import**. Zawartość repozytorium szablonów grupy są importowane do repozytorium szablonów zespołu. 
   
   ![Importowanie wspólnego repozytorium szablonów grupy](./media/team-lead-tasks/import-repo-2.png)
   
1. U góry strony **Repozytorium** projektu z listy rozwijanej i wybierz repozytorium **Narzędzi zespołu.**
   
1. Powtórz proces importowania, aby zaimportować zawartość wspólnego repozytorium narzędzi grupy, na przykład *narzędzia grupy,* do repozytorium **teamutilities.** 
   
Każdy z twoich dwóch repozytoriów zespołu zawiera teraz pliki z odpowiedniego wspólnego repozytorium grupy. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Dostosowywanie zawartości repozytoriów zespołu

Jeśli chcesz dostosować zawartość repozytoriów zespołu do konkretnych potrzeb zespołu, możesz to zrobić teraz. Można modyfikować pliki, zmieniać strukturę katalogów lub dodawać pliki i foldery.

Aby modyfikować, przekazywać lub tworzyć pliki lub foldery bezpośrednio w usłudze Azure DevOps:

1. Na stronie **Podsumowanie** projektu **Programu MyTeam** wybierz pozycję **Repozytoria**. 
   
1. U góry strony wybierz repozytorium, które chcesz dostosować.

1. W strukturze katalogu repozytorium przejdź do folderu lub pliku, który chcesz zmienić. 
   
   - Aby utworzyć nowe foldery lub pliki, zaznacz strzałkę obok **pozycji Nowy**. 
     
     ![Utwórz nowy plik](./media/team-lead-tasks/new-file.png)
     
   - Aby przekazać pliki, wybierz pozycję **Przekaż pliki**. 
     
     ![Przekazywanie plików](./media/team-lead-tasks/upload-files.png)
     
   - Aby edytować istniejące pliki, przejdź do pliku, a następnie wybierz pozycję **Edytuj**. 
     
     ![Edytowanie pliku](./media/team-lead-tasks/edit-file.png)
     
1. Po dodaniu lub edycji plików wybierz pozycję **Zatwierdź**.
   
   ![Zatwierdzanie zmian](./media/team-lead-tasks/commit.png)

Aby pracować z repozytoriami na komputerze lokalnym lub DSVM, najpierw skopiujesz lub *sklonujesz* repozytoria na komputerze lokalnym, a następnie zatwierdzasz i wypychasz zmiany do udostępnionych repozytoriów zespołu, 

Aby sklonować repozytoria:

1. Na stronie **Podsumowanie** projektu **MyTeam** wybierz pozycję **Repozytoria**, a u góry strony wybierz repozytorium, które chcesz sklonować.
   
1. Na stronie repozytorium wybierz pozycję **Klonuj** w prawym górnym rogu.
   
1. W oknie dialogowym **Klonowanie repozytorium** w **obszarze Wiersz polecenia**wybierz pozycję **HTTPS** dla połączenia HTTP lub **SSH** dla połączenia SSH i skopiuj adres URL klonu do schowka.
   
   ![Kopiuj adres URL klonowania](./media/team-lead-tasks/clone.png)
   
1. Na komputerze lokalnym utwórz następujące katalogi:
   
   - Dla systemu Windows: **C:\GitRepos\MyTeam**
   - Dla Linuksa, **$home/GitRepos/MyTeam** 
   
1. Zmień katalog utworzony.
   
1. W Git Bash uruchom `git clone <clone URL>`polecenie \<, gdzie klon URL> jest adresem URL skopiowanym z okna dialogowego **Klonowanie.**
   
   Na przykład użyj jednego z następujących poleceń, aby sklonować repozytorium **TeamUtilities** do katalogu *MyTeam* na komputerze lokalnym. 
   
   **Połączenie HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Połączenie SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Po dokonaniu zmian, które chcesz w lokalnym klon repozytorium, zatwierdzić i wypchnąć zmiany do repozytoriów zespołu udostępnionego. 

Uruchom następujące polecenia Git Bash z lokalnego katalogu **GitRepos\MyTeam\TeamTemplate** lub **GitRepos\MyTeam\TeamUtilities.**

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

## <a name="add-team-members-and-configure-permissions"></a>Dodawanie członków zespołu i konfigurowanie uprawnień

Aby dodać członków do zespołu:

1. W usłudze Azure DevOps na stronie głównej projektu **MyTeam** wybierz **ustawienia projektu** z lewej strony nawigacji. 
   
1. W ustawieniach **projektu** w lewo wybierz pozycję **Zespoły**, a następnie na stronie **Zespoły** wybierz **pozycję Zespół Zespołu MyTeam**. 
   
   ![Konfigurowanie zespołów](./media/team-lead-tasks/teams.png)
   
1. Na stronie **Profil zespołu** wybierz pozycję **Dodaj**.
   
   ![Dodaj do zespołu MyTeam](./media/team-lead-tasks/add-to-team.png)
   
1. W oknie dialogowym **Dodawanie użytkowników i grup** wyszukaj i wybierz członków, aby dodać do grupy, a następnie wybierz pozycję Zapisz **zmiany**. 
   
   ![Dodawanie użytkowników i grup](./media/team-lead-tasks/add-users.png)
   

Aby skonfigurować uprawnienia dla członków zespołu:

1. W obszarze **Ustawienia projektu** w lewo wybierz pozycję **Uprawnienia**. 
   
1. Na stronie **Uprawnienia** wybierz grupę, do której chcesz dodać członków. 
   
1. Na stronie dla tej grupy wybierz pozycję **Członkowie**, a następnie wybierz pozycję **Dodaj**. 
   
1. W wyskakującym okienku **Zaproś członków** wyszukaj i wybierz członków, aby dodać do grupy, a następnie wybierz pozycję **Zapisz**. 
   
   ![Udzielanie uprawnień członkom](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Tworzenie danych zespołu i zasobów analitycznych

Ten krok jest opcjonalny, ale udostępnianie danych i zasobów analitycznych całemu zespołowi ma korzyści z wydajności i kosztów. Członkowie zespołu mogą wykonywać swoje projekty na udostępnionych zasobach, oszczędzać na budżetach i wydajniej współpracować. Można utworzyć magazyn plików platformy Azure i zainstalować go na dsvm do udostępniania członkom zespołu. 

Aby uzyskać informacje dotyczące udostępniania zespołowi innych zasobów, takich jak klastry platformy Azure HDInsight Spark, zobacz [Platformy i narzędzia.](platforms-and-tools.md) Ten temat zawiera wskazówki z punktu widzenia nauki o danych dotyczące wybierania zasobów, które są odpowiednie dla Twoich potrzeb, oraz łącza do stron produktów i innych odpowiednich i przydatnych samouczków.

>[!NOTE]
> Aby uniknąć przesyłania danych między centrami danych, które mogą być powolne i kosztowne, upewnij się, że grupa zasobów platformy Azure, konto magazynu i dsvm są hostowane w tym samym regionie platformy Azure. 

### <a name="create-azure-file-storage"></a>Tworzenie magazynu plików platformy Azure

1. Uruchom następujący skrypt, aby utworzyć magazyn plików platformy Azure dla zasobów danych, które są przydatne dla całego zespołu. Skrypt monituje o informacje o subskrypcji platformy Azure, więc należy to zrobić gotowe do wprowadzenia. 

   - Na komputerze z systemem Windows uruchom skrypt z wiersza polecenia programu PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Na komputerze z systemem Linux uruchom skrypt z powłoki Linuksa:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Zaloguj się do konta platformy Microsoft Azure po wyświetleniu monitu i wybierz subskrypcję, której chcesz użyć.
   
1. Wybierz konto magazynu, którego chcesz użyć, lub utwórz nowe konto w ramach wybranej subskrypcji. Dla nazwy magazynu plików platformy Azure można używać małych liter, liczb i łączników.
   
1. Aby ułatwić montaż i udostępnianie magazynu, naciśnij klawisz Enter lub wprowadź *Y,* aby zapisać informacje o magazynie plików platformy Azure w pliku tekstowym w bieżącym katalogu. Możesz zaewidencjonować ten plik tekstowy do repozytorium **TeamTemplate,** najlepiej w obszarze **Dokumenty\DataDictionaries**, aby uzyskać do niego dostęp do wszystkich projektów w zespole. Informacje o plikach są również potrzebne do zainstalowania magazynu plików platformy Azure w systemie usługi Azure DSVM w następnej sekcji. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Instalowanie magazynu plików platformy Azure na komputerze lokalnym lub maszynie DSVM

1. Aby zainstalować magazyn plików platformy Azure na komputerze lokalnym lub maszynie DSVM, użyj następującego skryptu.
   
   - Na komputerze z systemem Windows uruchom skrypt z wiersza polecenia programu PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Na komputerze z systemem Linux uruchom skrypt z powłoki Linuksa:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Naciśnij klawisz Enter lub wprowadź *Y,* aby kontynuować, jeśli plik informacji o przechowywaniu plików platformy Azure został zapisany w poprzednim kroku. Wprowadź pełną ścieżkę i nazwę utworzonego pliku. 
   
   Jeśli nie masz pliku informacji o magazynie plików platformy Azure, wprowadź *n*i postępuj zgodnie z instrukcjami, aby wprowadzić subskrypcję, konto magazynu platformy Azure i informacje o magazynie plików platformy Azure.
   
1. Wprowadź nazwę dysku lokalnego lub dysku TDSP, aby zainstalować udział plików. Na ekranie wyświetlana jest lista istniejących nazw dysków. Podaj nazwę dysku, która jeszcze nie istnieje.
   
1. Upewnij się, że nowy dysk i pamięć masowa zostały pomyślnie zamontowane na komputerze.

## <a name="next-steps"></a>Następne kroki

Oto linki do szczegółowych opisów innych ról i zadań zdefiniowanych przez proces nauki o danych zespołu:

- [Zadania menedżera grupy dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania prowadzące projekt dla zespołu do nauki o danych](project-lead-tasks.md)
- [Zadania indywidualnego współautora projektu dla zespołu do nauki o danych](project-ic-tasks.md)
