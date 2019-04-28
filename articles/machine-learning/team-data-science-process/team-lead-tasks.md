---
title: Zadania dla zespołu prowadzić w zespole procesu do nauki o danych zespołu
description: Zarys zadania, które zespół prowadzić w projekcie zespołowym do nauki o danych oczekuje się, aby ukończyć dla zespołu do nauki o danych.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 45be3d7f865c7b72ae62efbf99dbbb4594b1846f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812070"
---
# <a name="tasks-for-the-team-lead-in-the-team-data-science-process-team"></a>Zadania dla zespołu prowadzić w zespole procesu do nauki o danych zespołu

W tym temacie wymieniono zadania, które jest lider zespołu oczekiwano dla swojego zespołu do nauki o danych. Celem jest zapewnienie środowiska zespół współpracowników, standardowego na [zespołu danych dla celów naukowych](overview.md) (TDSP). Przetwarzanie TDSP jest metodologia nauki o danych agile, iteracyjne wydajnie dostarczać rozwiązania do analizy predykcyjnej i inteligentnych aplikacji. Zaprojektowano go, aby poprawić współpracę i szkolenia zespołu. Ten proces jest destylacji najlepsze rozwiązania i struktury zarówno firmy Microsoft, a także z branży służące do pomyślnego wdrożenia inicjatyw do nauki o danych, aby pomóc firmom w pełni korzystać z zalet ich programy analizy. Konspekt ról pracowników i ich skojarzone zadania, które są obsługiwane przez zespół do nauki o danych standaryzacji na temat tego procesu dla [zespołu danych dla celów naukowych role i zadania](roles-tasks.md).

A **lider zespołu** zarządza zespołu w jednostce do nauki o danych przedsiębiorstwa. Zespół składa się z wielu analityków danych. Jednostki do nauki o danych z mniejszą liczbą analitykom danych **menedżera grupy** i **lider zespołu** może to być ta sama osoba lub można przekazać jej zadania zastępcze. Ale nie zmieniaj samych zadań. Przepływ pracy dla zadań do wykonania przez zespół potencjalnych klientów w celu skonfigurowania takiego środowiska są przedstawione na poniższym rysunku:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Zadania w blokach po 1 i 2 ilustracji są wymagane, jeśli używasz DevOps platformy Azure jako platformy hostingowej kodu i chcesz mieć oddzielnego projektu DevOps platformy Azure dla zespołu. Po wykonaniu tych zadań wszystkie repozytoria zespołu mogą być tworzone w ramach tego projektu. 

Po kilku wymagań wstępnych dotyczących zadania określone w poniższej sekcji są spełnione przez menedżera grupy istnieją pięciu głównych zadań (Niektóre opcjonalne), należy wykonać w ramach tego samouczka. Zadania te odpowiadają main numerowane sekcjach tego tematu:

1. Tworzenie **projektu** na usługom DevOps platformy Azure grupy, grupy i dwa repozytoriami zespołu w projekcie:
    - **ProjectTemplate repozytorium** 
    - **TeamUtilities repozytorium**
2. Zapełnić zespół **ProjectTemplate** repozytorium z **GroupProjectTemplate** repozytorium, który został skonfigurowany przez menedżera grupy. 
3. Utwórz zespół data i analizy zasobów:
    - Dodawanie narzędzi specyficznych dla zespołu do **TeamUtilities** repozytorium. 
    - (Opcjonalnie) Tworzenie **usługi Azure file storage** ma być używany do przechowywania zasobów danych, które mogą być przydatne dla całego zespołu. 
4. (Opcjonalnie) Usługa Azure file storage, aby zainstalować **maszyny wirtualnej do nauki o danych** (DSVM) zespołu potencjalny klient i Dodaj zasoby danych na nim.
5. Konfigurowanie **zabezpieczeniem** przez dodawanie członków zespołu i skonfiguruj swoje uprawnienia.

>[AZURE.NOTE] Firma Microsoft opisano kroki niezbędne do skonfigurowania środowiska TDSP środowisko pracy dla zespołu za pomocą DevOps platformy Azure w poniższych instrukcjach. Firma Microsoft umożliwia określenie sposobu wykonywania tych zadań, za pomocą DevOps platformy Azure, ponieważ jest to, jak wygląda implementacja przetwarzania TDSP w firmie Microsoft. Jeśli inny kod hostingu platformy jest używana dla tej grupy, zadania, które muszą zostać wykonane przez lider zespołu, zazwyczaj nie należy zmieniać. Ale sposobem wykonania tych zadań ma być inna.

## <a name="repositories-and-directories"></a>Repozytoria i katalogi

W tym temacie używany skrócone nazwy dla repozytoriów i katalogów. Nazwy te ułatwiają wykonaj operacje między repozytoria i katalogi. Ten zapis (**R** dla repozytoriów Git i **D** dla katalogi lokalne na maszyny wirtualnej DSVM) jest używany w następujących sekcjach:

- **R1**: **GroupProjectTemplate** repozytorium w usłudze Git, który Menedżer grupy skonfigurowany na serwerze grupy DevOps platformy Azure.
- **R3**: Zespół **ProjectTemplate** repozytorium w usłudze Git, możesz skonfigurować.
- **R4**: **TeamUtilities** repozytorium w usłudze Git, możesz skonfigurować.
- **D1**: Katalog lokalny sklonować z R1 i skopiować D3.
- **D3**: Katalog lokalny sklonować z R3, dostosowywanie i kopiowane z powrotem do R3.
- **D4**: Katalog lokalny sklonować z R4, dostosowywanie i kopiowane z powrotem do R4.

Nazwy określone dla repozytoriów i katalogi, w tym samouczku zostały dołączone przy założeniu, że Twoje celem jest ustanowienie oddzielnego projektu zespołu w obrębie większej grupy do nauki o danych. Jednak inne opcje są otwarte dla Ciebie jako lider zespołu:

- Cała grupa można wybrać opcję utworzenia pojedynczego projektu. Wszystkie projekty z wszystkich zespołów do nauki o danych będzie w ramach tego pojedynczego projektu. Aby to osiągnąć, można wyznaczyć administratorowi usługi git, wykonaj następujące instrukcje do utworzenia pojedynczego projektu. Ten scenariusz może być prawidłowy, na przykład:
    -  grupy do nauki o danych w małych, która nie ma wiele zespołów do nauki o danych 
    -  większej danych do analizy grupy z wielu zespołów do nauki o danych, niemniej chce, aby zoptymalizować współpracę między zespołami dzięki takim działaniom jak planowanie sprintu na poziomie grupy. 
- Zespoły mogą wybrać opcję Szablony specyficzne dla zespołu projektu lub narzędzi specyficznych dla zespołu w ramach pojedynczego projektu dla całej grupy. W tym przypadku Liderzy zespołów, należy utworzyć repozytoria szablonu projektu i/lub repozytoriów narzędzia zespołu, w tym samym projekcie. Nazwy tych repozytoriów *< TeamName\>ProjectTemplate* i *< TeamName\>narzędzia*, na przykład *TeamJohnProjectTemplate*i *TeamJohnUtilities*. 

W każdym przypadku zespołów podać tę informację członków zespołu, wiadomo, które szablon i programy narzędziowe repozytoria do przyjęcia, gdy są one Konfigurowanie i klonowania repozytoriów projektu i narzędzia. Liderzy projektu należy stosować [projektu zadań dla zespołu do nauki o danych](project-lead-tasks.md) do utworzenia projektu repozytoriów, czy w ramach oddzielnych projektów lub pojedynczego projektu. 


## <a name="0-prerequisites"></a>0. Wymagania wstępne

Wymagania wstępne są spełnione, wykonując zadania przydzielone do swojego przełożonego grupy opisane w temacie [menedżera grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md). Aby podsumować zapotrzebowanie w tym miejscu, następujące wymagania muszą spełniać przed przystąpieniem do wykonywania zadań kierownik zespołu: 

- Twoje **grupie usługom DevOps platformy Azure** (lub konta grupy na innego kodu, hostingu platformy) został skonfigurowany przez menedżera grupy.
- Twoje **repozytorium GroupProjectTemplate** (R1) zostało skonfigurowane na Twoim koncie grupy przez menedżera grupy kod hostingu platformy, które planujesz używać.
- Nastąpiło **autoryzacji** na Twoim koncie grupy, aby utworzyć repozytoriów dla Twojego zespołu.
- Git musi być zainstalowany na tym komputerze. Jeśli używasz maszyny wirtualnej do nauki o danych (DSVM) wstępnie zainstalowane narzędzia Git, i jest gotowe. W przeciwnym razie zobacz [dodatku platformami i narzędziami](platforms-and-tools.md#appendix).  
- Jeśli używasz **Windows DSVM**, musisz mieć [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowana na tym komputerze. W pliku README.md, przewiń w dół do **Pobierz i zainstaluj** sekcji, a następnie kliknij przycisk *najnowszą wersję Instalatora*. Spowoduje to przejście do najnowszych strony Instalatora. Pobierz Instalator .exe, w tym miejscu i uruchom go. 
- Jeśli używasz **Linux maszyny wirtualnej DSVM**, utworzyć klucz publiczny SSH na maszyny wirtualnej DSVM i dodać go do grupy usług DevOps platformy Azure. Aby uzyskać więcej informacji na temat protokołu SSH, zobacz **utworzyć publiczny klucz SSH** sekcji [dodatku platformami i narzędziami](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-project-and-repositories"></a>1. Tworzenie projektu i repozytoriów

Ten krok należy wykonać, jeśli używasz DevOps platformy Azure jako kod hostingu platforma umożliwiająca przechowywanie wersji i współpracy. Ta sekcja zawiera można tworzyć trzy artefaktów w usługom DevOps platformy Azure, grupy:

- **MyTeam** projektu DevOps platformy Azure
- **MyProjectTemplate** repozytorium (**R3**) w usłudze Git
- **MyTeamUtilities** repozytorium (**R4**) w usłudze Git

### <a name="create-the-myteam-project"></a>Utwórz projekt MyTeam

- Przejdź do swojej grupy głównej usługom DevOps platformy Azure pod adresem URL `https://<Azure DevOps Services Name\>.visualstudio.com`. 
- Kliknij przycisk **New** do tworzenia projektu. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Okno projektu Utwórz prosi o wprowadź nazwę projektu (**MyTeam** w tym przykładzie). Upewnij się, że wybrano **Agile** jako **szablonu procesu** i **Git** jako **kontroli wersji**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Kliknij przycisk **Tworzenie projektu**. Projekt **MyTeam** jest tworzony w mniej niż 1 minuta. 

- Po elemencie project **MyTeam** jest utworzony, kliknij polecenie **przejdź do projektu** przycisk, aby przejść do strony głównej projektu. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Jeśli widzisz **Gratulacje!** okna podręcznego kliknij **Dodaj kod** (przycisk z czerwonym prostokątem). W przeciwnym razie kliknij przycisk **kodu** (w żółte pole). To kieruje użytkownika do strony repozytorium Git projektu. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Utwórz repozytorium MyProjectTemplate (R3) w usłudze Git

- Na stronie repozytorium Git projektu, kliknij przycisk strzałki w dół obok nazwy repozytorium **MyTeam**i wybierz **Zarządzaj repozytoriami...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Na **kontroli wersji** karty Projekt w Panelu sterowania kliknij **MyTeam**, a następnie wybierz **zmiana nazwy repozytorium...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Wprowadź nazwę nowego repozytorium w **zmiana nazwy repozytorium MyTeam** okna. W tym przykładzie *MyTeamProjectTemplate*. Możesz wybrać podobny *< Twoja nazwa zespołu\>ProjectTemplate*. Kliknij przycisk **Zmień nazwę** aby kontynuować.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Utwórz repozytorium MyTeamUtilities (R4) w usłudze Git

- Aby utworzyć nowe repozytorium *< nazwa Twojego zespołu\>narzędzia* w ramach projektu, kliknij przycisk **nowe repozytorium...**  na **kontroli wersji** karty Panelu sterowania projektu.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- W **Utwórz nowe repozytorium** okna, które się pojawi, podaj nazwę dla tego repozytorium. W tym przykładzie użyjemy nazwy jako *MyTeamUtilities*, czyli **R4** w naszym notacji. Wybierz podobny *< Twoja nazwa zespołu\>narzędzia*. Upewnij się, że wybrano **Git** dla **typu**. Następnie kliknij przycisk **Utwórz** aby kontynuować.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Upewnij się, że dwa nowe repozytoria Git utworzone w ramach projektu **MyTeam**. W tym przykładzie: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-projecttemplate-and-teamutilities-repositories"></a>2. Zapełnić ProjectTemplate i TeamUtilities repozytoriów

Procedura rozmieszczania używa katalogów w lokalnej maszyny wirtualnej DSVM jako pośredniego tymczasowej witryny. Jeśli trzeba dostosować swoje **ProjectTemplate** i **TeamUtilities** repozytoriów w celu spełnienia określonych niektóre zespół wymaga, możesz to zrobić w przedostatni kroku procedury. Poniżej przedstawiono podsumowanie kroków używany do generowania zawartości **MyTeamProjectTemplate** i **MyTeamUtilities** repozytoriów dla zespołu do nauki o danych. Poszczególne kroki odpowiadają podsekcje rozmieszczania procedury:

- Klonuj repozytorium grupy do katalogu lokalnego: team R1 - sklonowany do -> lokalne D1
- Sklonuj swoje repozytoria zespołu w katalogach lokalnych: team R3 & R4 - sklonowany do -> lokalne D3 & D4
- Skopiuj zawartość szablonu projektu grupy do folderu lokalnego zespołu:  D1 - zawartość jest kopiowana do -> D3
- (Opcjonalnie) Dostosowywanie lokalnego D3 & D4
- Przenieś lokalny katalog zawartości do zespołu repozytoriów: D3 & D4 — zawartość, Dodaj do -> zespół R3 & R4


### <a name="initialize-the-team-repositories"></a>Inicjowanie repozytoriami zespołu

W tym kroku należy zainicjować repozytorium szablonów projektu z repozytorium szablonów projektu grupy:

- **MyTeamProjectTemplate** repozytorium (**R3**) z Twojego **GroupProjectTemplate** (**R1**) repozytorium


### <a name="clone-group-repositories-into-local-directories"></a>Klonowanie repozytoriów grupy w katalogach lokalnych

Aby rozpocząć tej procedury:

- Utwórz katalog na komputerze lokalnym:
    - Aby uzyskać **Windows**: **C:\GitRepos\GroupCommon** i **C:\GitRepos\MyTeam**
    - Aby uzyskać **Linux**: **GitRepos\GroupCommon** i **GitRepos\MyTeam** w katalogu macierzystym 
- Przejdź do katalogu **GitRepos\GroupCommon**.
- Uruchom następujące polecenie, w systemie operacyjnym komputera lokalnego.

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Klonowanie tych poleceń Twoje **GroupProjectTemplate** repozytorium (R1) na usługi grupy usługi Azure DevOps do katalogu lokalnego w **GitRepos\GroupCommon** na komputerze lokalnym. Po zakończeniu klonowania katalogu **GroupProjectTemplate** (D1) jest tworzony w katalogu **GitRepos\GroupCommon**. Tutaj przyjęto założenie, że projekt utworzony menedżera grupy **GroupCommon**i **GroupProjectTemplate** repozytorium znajduje się w tym projekcie. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Sklonuj swoje repozytoria zespołu w katalogach lokalnych

Klonowanie tych poleceń Twoje **MyTeamProjectTemplate** (R3) i **MyTeamUtilities** (R4) repozytoriów w projekcie **MyTeam** na swojej grupy metodyka DevOps usług systemu Azure **MyTeamProjectTemplate** (D3) i **MyTeamUtilities** katalogów (D4) w **GitRepos\MyTeam** na komputerze lokalnym. 

- Przejdź do katalogu **GitRepos\MyTeam**
- Uruchom następujące polecenia, zgodnie z potrzebami w systemie operacyjnym komputera lokalnego. 

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Po zakończeniu klonowania dwa katalogi **MyTeamProjectTemplate** (D3) i **MyTeamUtilities** (D4) są tworzone w katalogu **GitRepos\MyTeam**. Firma Microsoft ma zakłada, że w tym miejscu Nazwa projektu szablonu i programy narzędziowe repozytoriów **MyTeamProjectTemplate** i **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-project-template-directory"></a>Skopiuj zawartość szablonu projektu grupy do katalogu lokalnego projektu szablonu

Aby skopiować zawartość lokalnej **GroupProjectTemplate** folder (D1) do lokalnej **MyTeamProjectTemplate** (D3), uruchom jedno z poniższych skryptów powłoki: 

#### <a name="from-the-powershell-command-line-for-windows"></a>Z wiersza polecenia dla Windows PowerShell       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>W przypadku systemu Linux **DSVM systemu Linux**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Skrypty wykluczać zawartość katalogu .git. Skrypty monit **ukończyć ścieżki** do katalogu źródłowego D1 i katalog docelowy D3.
        

### <a name="customize-your-project-template-or-team-utilities-optional"></a>Dostosowywanie Twojego projektu szablonu lub zespołu narzędzia (opcjonalnie)

Dostosuj swoje **MyTeamProjectTemplate** (D3) i **MyTeamUtilities** (D4), jeśli to konieczne, na tym etapie procesu instalacji. 

- Jeśli chcesz dostosować zawartość D3 do potrzeb Twojego zespołu, możesz modyfikować dokumenty szablonu, lub zmienić strukturę katalogów.

- Jeśli Twój zespół został opracowany niektóre narzędzia, które chcesz udostępnić całego zespołu, skopiuj i Wklej do tych narzędzi do katalogu D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Wypchnij zawartość katalogu lokalnego do zespołu repozytoriów

Aby dodać zawartość (opcjonalnie dostosowane) katalogów lokalnych D3 i D4 do repozytoriami zespołu R3 i R4, uruchom następujące usługi git poleceń powłoki bash z poziomu konsoli programu Windows PowerShell lub z poziomu powłoki systemu Linux. Uruchom polecenia z **GitRepos\MyTeam\MyTeamProjectTemplate** katalogu.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Pliki w repozytorium MyTeamProjectTemplate usługom DevOps platformy Azure w swojej grupy są synchronizowane prawie od razu po uruchomieniu tego skryptu.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Teraz uruchomić ten sam zestaw cztery polecenia git z **GitRepos\MyTeam\MyTeamUtilities** katalogu. 

> [AZURE.NOTE]Jeśli po raz pierwszy, zatwierdzenia do repozytorium Git, należy skonfigurować parametry globalne *user.name* i *user.email* przed uruchomieniem `git commit` polecenia. Uruchom dwa poniższe polecenia:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Jeśli są zobowiązuje się do wielu repozytoriów Git, należy użyć takiej samej nazwie i adres e-mail, gdy zdecydujesz się na każdym z nich. Przy użyciu tej samej nazwy i adresu e-mail okazuje się wygodne później podczas tworzenia pulpitów nawigacyjnych usługi Power BI, aby śledzić działania usługi Git na wiele repozytoriów.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Tworzenie zespołu data i analizy zasobów (opcjonalnie)

Współużytkowanie zasobów danych i analizy całego zespołu ma zalety wydajności i kosztów: członków zespołu można wykonać swoje projekty ze współużytkowanych zasobów, Zapisz budżetów i bardziej efektywna współpraca. W tej sekcji udostępniamy instrukcje dotyczące sposobu tworzenia usługi Azure file storage. W następnej sekcji firma Microsoft zapewnia instrukcje dotyczące sposobu instalacji w usłudze Azure file storage na komputer lokalny. Aby uzyskać dodatkowe informacje na temat udostępniania innych zasobów, takich jak Azure maszynami wirtualnymi analizy danych, Azure HDInsight Spark klastrów, zobacz [platformami i narzędziami](platforms-and-tools.md). Ten temat zawiera wskazówki z punktu widzenia do nauki o danych na temat wybierania zasobów, które są odpowiednie do potrzeb i linki do stron produktu i innych stosownych i przydatne samouczków, które opublikowaliśmy.

>[AZURE.NOTE] Aby uniknąć między przesyłania danych centra danych, które mogą być powolne i kosztowne, upewnij się, że grupa zasobów, konto magazynu i maszyn wirtualnych platformy Azure (np. maszyny wirtualnej DSVM) znajdują się w tym samym centrum danych platformy Azure. 

Uruchom następujące skrypty w celu utworzenia usługi Azure file storage dla Twojego zespołu. Usługi Azure file storage dla Twojego zespołu może służyć do przechowywania zasobów danych, które są przydatne dla całego zespołu. Skrypty monit swoje informacje dotyczące platformy Azure konto i subskrypcję, więc mieć tych poświadczeń, które są gotowe do wprowadzania. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Tworzenie usługi Azure file storage przy użyciu programu PowerShell z Windows

Uruchom ten skrypt z programu PowerShell wiersza polecenia:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Zaloguj się do konta usługi Microsoft Azure, po wyświetleniu monitu:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Wybierz subskrypcję platformy Azure, którego chcesz użyć:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Wybierz konto magazynu, które będzie używać lub utworzyć nowy katalog w ramach wybranej subskrypcji:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Wprowadź nazwę usługi Azure file storage, aby utworzyć. Tylko małe litery i cyfry zamierzone, Zapisz i - są akceptowane:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Aby ułatwić zainstalowanie i udostępnianie tego magazynu, po jego utworzeniu, Zapisz informacje magazynu plików platformy Azure do pliku tekstowego, a następnie Zanotuj ścieżkę do lokalizacji. W szczególności należy ten plik, aby zainstalować usługi Azure file storage, aby maszynach wirtualnych platformy Azure, w następnej sekcji. 

Jest dobrą praktyką, aby sprawdzić, w tym pliku tekstowego do repozytorium ProjectTemplate. Firma Microsoft zaleca się umieszczenie w katalogu **Docs\DataDictionaries**. W związku z tym ten zasób danych są dostępne dla wszystkich projektów zespołu. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Tworzenie usługi Azure file storage przy użyciu skryptu systemu Linux

Uruchom ten skrypt z poziomu powłoki systemu Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Zaloguj się do konta usługi Microsoft Azure, postępując zgodnie z instrukcjami na tym ekranie:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Wybierz subskrypcję platformy Azure, którego chcesz używać:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Wybierz konto magazynu, które będzie używać lub utworzyć nowy katalog w ramach wybranej subskrypcji:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Wprowadź nazwę usługi Azure file storage, aby utworzyć, tylko małe litery, cyfry i - są akceptowane:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

W celu ułatwienia, uzyskiwania dostępu do tego magazynu, po jego utworzeniu, Zapisz informacje magazynu plików platformy Azure do pliku tekstowego, a następnie Zanotuj ścieżkę do lokalizacji. W szczególności należy ten plik, aby zainstalować usługi Azure file storage, aby maszynach wirtualnych platformy Azure, w następnej sekcji.

Jest dobrą praktyką, aby sprawdzić, w tym pliku tekstowego do repozytorium ProjectTemplate. Firma Microsoft zaleca się umieszczenie w katalogu **Docs\DataDictionaries**. W związku z tym ten zasób danych są dostępne dla wszystkich projektów zespołu. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Instalowanie usługi Azure storage plik (opcjonalnie)

Po pomyślnym utworzeniu usługi Azure file storage mogą być instalowane na komputerze lokalnym przy użyciu jednego z poniższych skryptów programu PowerShell lub Linux.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Instalowanie usługi Azure file storage przy użyciu programu PowerShell z Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Zostanie wyświetlony monit do logowania się w pierwszym, jeśli użytkownik nie zalogował się. 

Kliknij przycisk **Enter** lub **y** aby kontynuować, gdy zostanie wyświetlony monit, czy masz plików platformy Azure informacje o magazynu plików, a następnie wprowadź ***ukończyć ścieżkę i nazwę** pliku, możesz utworzyć w poprzedniego kroku. Informacje o instalacji usługi Azure file storage jest do odczytu bezpośrednio z pliku, a dane są gotowe przejść do następnego kroku.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Jeśli nie masz plik zawierający informacje o magazynu plików platformy Azure, na końcu tej sekcji znajdują się instrukcje wprowadzania z klawiatury informacji.

Następnie zostanie wyświetlony monit, wprowadź nazwę dysku, który ma zostać dodany do maszyny wirtualnej. Lista istniejących nazw dysków, wydrukowaniu na ekranie. Należy podać nazwę dysku, który jeszcze nie istnieje na liście.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Upewnij się, że nowy dysk F został pomyślnie zainstalowany na komputerze.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Jak ręcznie wprowadzić informacje magazynu plików platformy Azure:** Jeśli nie masz dane magazynu plików platformy Azure w pliku tekstowym, możesz postępuj zgodnie z instrukcjami na kolejnym ekranie wpisać wymagane subskrypcji, konto magazynu oraz informacje dotyczące magazynu plików platformy Azure:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Typ subskrypcji platformy Azure nazwę, wybierz konto magazynu usługi Azure file storage jest tworzona, i wpisz nazwę magazynu plików platformy Azure:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Instalowanie usługi Azure file storage przy użyciu skryptu systemu Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Zostanie wyświetlony monit do logowania się w pierwszym, jeśli użytkownik nie zalogował się. 

Kliknij przycisk **Enter** lub **y** aby kontynuować, gdy zostanie wyświetlony monit, czy masz plików platformy Azure informacje o magazynu plików, a następnie wprowadź ***ukończyć ścieżkę i nazwę** pliku, możesz utworzyć w poprzedniego kroku. Informacje o instalacji usługi Azure file storage jest do odczytu bezpośrednio z pliku, a dane są gotowe przejść do następnego kroku.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Następnie zostanie wyświetlony monit, wprowadź nazwę dysku, który ma zostać dodany do maszyny wirtualnej. Lista istniejących nazw dysków, wydrukowaniu na ekranie. Należy podać nazwę dysku, który jeszcze nie istnieje na liście.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Upewnij się, że nowy dysk F został pomyślnie zainstalowany na komputerze.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Jak ręcznie wprowadzić informacje magazynu plików platformy Azure:** Jeśli nie masz dane magazynu plików platformy Azure w pliku tekstowym, możesz postępuj zgodnie z instrukcjami na kolejnym ekranie wpisać wymagane subskrypcji, konto magazynu oraz informacje dotyczące magazynu plików platformy Azure:

- Dane wejściowe **n**.
- Wybierz indeks nazwę subskrypcji, w której usługi Azure file storage został utworzony w poprzednim kroku:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Wybierz konto magazynu w ramach Twojej subskrypcji i typu w nazwie magazynu plików platformy Azure:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Wprowadź nazwę dysku, które mają zostać dodane do swojej maszyny, które powinny się różnić od wszelkie istniejące:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Konfigurowanie zasad kontroli zabezpieczeń 

Na stronie głównej grupy usługi Azure DevOps usługi firmy, kliknij przycisk **ikonę koła zębatego** obok swojej nazwy użytkownika w prawym górnym rogu wybierz **zabezpieczeń** kartę. Możesz dodawać członków do zespołu w tym miejscu przy użyciu różnych uprawnień.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do bardziej szczegółowy opis ról i zadań zdefiniowanych przez zespół danych dla celów naukowych:

- [Menedżer grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania kierownik zespołu dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania potencjalnych klientów dla zespołu do nauki o danych w projekcie](project-lead-tasks.md)
- [Poszczególnych uczestników projektu dla zespołu do nauki o danych](project-ic-tasks.md)
