---
title: Zespół Azure prowadzić do zespołu zestawu danych nauki procesu zadania — | Dokumentacja firmy Microsoft
description: Konspekt zadań dla zespołu prowadzić na projektu zespołowego analizy danych.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: d17c6c7543416d3f279b7e25152a421dd233a806
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="team-lead-tasks"></a>Zespół realizacji zadań

W tym temacie wymieniono zadania, które jest realizacji zespołu oczekiwano dla zespołu nauki ich danych. Celem jest ustalenie środowisko współpracy zespołu standaryzuje na [proces nauki danych zespołu](overview.md) (TDSP). TDSP jest metodologia nauki agile, iteracji danych do tworzenia rozwiązań analizy predykcyjnej i inteligentnego aplikacji wydajnie. Zaprojektowano go, aby pomóc w udoskonalaniu współpracy i uczenie się zespołu. Proces jest destylacji najlepszych rozwiązań i struktury z zarówno do firmy Microsoft oraz z branży, potrzebne do pomyślnego wykonania inicjatyw nauki danych pozwalające firmom w pełni wykorzystać zalety programów analytics. Konspekt ról pracowników i ich skojarzonych zadań, które są obsługiwane przez zespół nauki danych standaryzacji na ten proces dla [proces nauki danych zespołu ról i zadań](roles-tasks.md).

A **prowadzić zespołu** zarządza zespołu w jednostce nauki danych przedsiębiorstwa. Zespół składa się z wielu analityków danych. Dla danych nauki jednostki z mniejszą liczbą analityków danych **menedżera grupy** i **prowadzić zespołu** może być ta sama osoba lub można przekazać ich zadań surogatu. Ale samych zadań, nie należy zmieniać. Przepływ pracy dla zadań, które mają zostać wykonane przez zespół prowadzi do skonfigurowania takiego środowiska są przedstawione na poniższej ilustracji:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Zadania w blokach 1 i 2 rysunku są wymagane, jeśli używasz programu Visual Studio Team Services (VSTS) jako kod obsługi platformy i mają oddzielny zespół projektu dla własnego zespołu. Po wykonaniu tych zadań wszystkie repozytoria zespołu mogą być tworzone w ramach tego projektu zespołowego. 

Po kilku wymagania wstępne określone w sekcji następujące zadania są spełnione przez menedżera grupy, brak pięć podstawowych zadań (niektóre opcjonalnie), które zostaną wykonane w tym samouczku. Te zadania odpowiada main numerowane sekcje tego tematu:

1. Utwórz **projektu zespołowego** na serwerze programu VSTS grupy grupy i dwa repozytoria zespołu w projekcie:
    - **Repozytorium ProjectTemplate** 
    - **Repozytorium TeamUtilities**
2. Inicjatora zespołu **ProjectTemplate** repozytorium z **GroupProjectTemplate** repozytorium, który został ustawiony przez menedżera grupy. 
3. Tworzenie zespołu zasobów danych i ich analiza:
    - Narzędzia specyficzne dla zespołu, aby dodać **TeamUtilities** repozytorium. 
    - (Opcjonalnie) Utwórz **usługi Azure file storage** ma być używany do przechowywania zasobów danych, które mogą być przydatne dla całego zespołu. 
4. (Opcjonalnie) Zainstaluj usługi Azure file storage do **maszyny wirtualnej nauki danych** (DSVM) zespołu prowadzić i dodać zasobów danych.
5. Konfigurowanie **zabezpieczeniem** przez dodawanie członków zespołu i skonfiguruj swoje uprawnienia.

>[AZURE.NOTE] Firma Microsoft przedstawiają kroki niezbędne do konfigurowania środowiska zespołu TDSP przy użyciu programu VSTS w poniższych instrukcjach. Określono sposobu wykonywania tych zadań z programu VSTS, ponieważ sposób wprowadzania TDSP firmy Microsoft. Jeśli inny kod obsługujący platformy jest używany dla tej grupy, zadania, które muszą zostać wykonane przez realizacji zespołu zwykle nie należy zmieniać. Jednak sposób do wykonania tych zadań ma być różne.

## <a name="repositories-and-directories"></a>Repozytoria i katalogów

W tym temacie używa skróconej nazwy katalogów i repozytoriów. Te nazwy należy wykonać operacje między repozytoriów i katalogi. Ten element notation (**R** dla repozytoriów Git i **D** dla katalogów lokalnych w sieci DSVM) jest używany w następujących sekcjach:

- **R1**: **GroupProjectTemplate** repozytorium na Git, które menedżera grupy skonfigurowane na serwerze programu VSTS grupy.
- **R3**: zespół **ProjectTemplate** repozytorium na skonfigurowaniu Git.
- **R4**: **TeamUtilities** repozytorium na skonfigurowaniu Git.
- **D1**: katalog lokalny klonować z R1 i skopiowane do D3.
- **D3**: katalog lokalny sklonowany z R3, dostosowywanie i skopiowane do R3.
- **D4**: katalog lokalny sklonowany z R4, dostosowywanie i skopiowane do R4.

Nazwy określone dla repozytoriów i katalogi, w tym samouczku zostały dołączone przy założeniu, że z celem jest ustanowienie oddzielny zespół projektu dla zespołu w większej grupy analizy danych. Jednak inne opcje są otwarty jako realizacji zespołu:

- Całej grupy można utworzyć projektu zespołowego pojedynczego. Następnie wszystkie projekty z wszystkich zespołów nauki danych będzie w tym projekcie zespołowym pojedynczego. Można to osiągnąć, możesz wyznaczyć administratorowi git wykonaj te instrukcje do utworzenia projektu zespołowego pojedynczego. W tym scenariuszu może być nieprawidłowy, na przykład dla:
    -  grupy nauki niewielkie zbiory danych, która nie ma kilka zespołów nauki danych 
    -  większe danych nauki grupę o kilka zespołów analizy danych, który chce jednak zoptymalizować współpraca z zespołem między z działania, takie jak planowanie przebiegu na poziomie grupy. 
- Zespoły mogą wybrać szablony specyficzne dla zespołu projektu i narzędzi specyficznych dla zespołu w projekcie zespołowym jednego dla całej grupy. W takim przypadku liderów zespołu należy utworzyć repozytoria szablonu projektu zespołowego i/lub repozytoriów narzędzia zespołu w ramach tego samego projektu zespołowego. Nazwa te repozytoria *< TeamName\>ProjectTemplate* i *< TeamName\>narzędzia*, na przykład *TeamJohnProjectTemplate*i *TeamJohnUtilities*. 

W każdym przypadku zespołu potencjalnych klientów należy umożliwić członków zespołu wiedzieć, które repozytoria szablonu i narzędzia do przyjęcia podczas konfigurowania i klonowanie repozytoria projektu i narzędzi. Projekt potencjalnych klientów należy stosować [projektu zadania dla zespołu nauki danych](project-lead-tasks.md) utworzyć repozytoria projektu, czy w ramach projektów oddzielnego zespołu lub jeden zespół projektu. 


## <a name="0-prerequisites"></a>0. Wymagania wstępne

Wymagania wstępne są spełnione, wykonując przypisane do menedżera grupy opisane w temacie zadania [grupy Menedżera zadań dla zespołu nauki danych](group-manager-tasks.md). Podsumowując, w tym miejscu, następujące wymagania muszą spełniać przed rozpoczęciem realizacji zadań zespołu: 

- Twoje **serwera programu VSTS grupy** (lub konta grupy na inny kod, hosting platformy) nie został skonfigurowany przez menedżera grupy.
- Twoje **GroupProjectTemplate repozytorium** (R1) nie został skonfigurowany na Twoim koncie grupy przez menedżera grupy kod obsługi platform, które ma być używany.
- Nastąpiło **autoryzowany** Twojego konta grupy można utworzyć repozytoria dla zespołu.
- Git musi być zainstalowany na tym komputerze. Jeśli używasz maszyny wirtualnej nauki danych (DSVM) Git został wstępnie zainstalowany i jest gotowe. W przeciwnym razie zobacz [dodatku platform i narzędzi](platforms-and-tools.md#appendix).  
- Jeśli używasz **Windows DSVM**, musisz mieć [Git Menedżera poświadczeń (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowana na tym komputerze. W pliku README.md, przewiń w dół do **Pobierz i zainstaluj** sekcji, a następnie kliknij przycisk *najnowszą wersję Instalatora*. Powoduje to przejście do najnowszej strony Instalatora. Pobierz Instalatora .exe w tym miejscu i uruchom go. 
- Jeśli używasz **Linux DSVM**, Utwórz klucz publiczny SSH na Twojej DSVM i dodaj go do serwera programu VSTS grupy. Aby uzyskać więcej informacji na temat SSH, zobacz **tworzenie publicznego klucza SSH** sekcji [dodatku platform i narzędzi](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Tworzenie projektu zespołowego i repozytoriów

Jeśli używasz programu VSTS jako kod obsługujący platformę dla wersji i współpracy, należy wykonać ten krok. Ta sekcja zawiera utworzeniu trzech artefaktów na serwerze programu VSTS grupy:

- **MyTeam** projektu programu VSTS
- **MyProjectTemplate** repozytorium (**R3**) na Git
- **MyTeamUtilities** repozytorium (**R4**) na Git

### <a name="create-the-myteam-project"></a>Utwórz projekt MyTeam

- Przejdź do strony głównej serwera programu VSTS z grupy pod adresem URL `https://<VSTS Server Name\>.visualstudio.com`. 
- Kliknij przycisk **nowy** do utworzenia projektu zespołowego. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Tworzenie zespołu projektu okna prośba o wprowadź nazwę projektu (**MyTeam** w tym przykładzie). Upewnij się, że wybrano **Agile** jako **szablonu procesu** i **Git** jako **kontroli wersji**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Kliknij przycisk **Tworzenie projektu**. Twój projekt zespołowy **MyTeam** jest tworzony w mniej niż minutę. 

- Po projektu zespołowego **MyTeam** jest tworzony, kliknij przycisk **przejdź do projektu** przycisk, aby być kierowany do strony głównej projektu zespołowego. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Jeśli widzisz **Gratulacje!** Okno podręczne, kliknij przycisk **Dodaj kod** (przycisk w czerwonym prostokątem). W przeciwnym razie kliknij przycisk **kod** (w polu żółty). Dzięki temu do strony repozytorium Git projektu zespołowego. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Tworzenie repozytorium MyProjectTemplate (R3) na Git

- Na stronie repozytorium Git projektu zespołowego, kliknij strzałkę w dół obok nazwy repozytorium **MyTeam**i wybierz **Zarządzanie repozytoria...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Na **kontroli wersji** kartę projektu zespołowego w Panelu sterowania kliknij **MyTeam**, a następnie wybierz pozycję **zmiany nazwy repozytorium...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Wprowadź nazwę nowego repozytorium w **Zmień nazwę repozytorium MyTeam** okna. W tym przykładzie *MyTeamProjectTemplate*. Użytkownik może wyglądać mniej więcej tak *< nazwa Twojego zespołu\>ProjectTemplate*. Kliknij przycisk **zmienić** aby kontynuować.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Tworzenie repozytorium MyTeamUtilities (R4) na Git

- Aby utworzyć nowe repozytorium *< nazwa Twojego zespołu\>narzędzia* w obszarze projektu zespołowego, kliknij **nowego repozytorium...**  na **kontroli wersji** kartę Twój projekt zespołowy w Panelu sterowania.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- W **utworzyć nowe repozytorium** oknie wyskakującym, podaj nazwę dla tego repozytorium. W tym przykładzie nazywamy go jako *MyTeamUtilities*, która jest **R4** w naszym notacji. Wybierz przypominać *< nazwa Twojego zespołu\>narzędzia*. Upewnij się, że wybrano **Git** dla **typu**. Następnie kliknij przycisk **Utwórz** aby kontynuować.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Upewnij się, że widoczny dwóch nowych repozytoriów Git utworzone na podstawie projektu zespołowego **MyTeam**. W tym przykładzie: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. Inicjatora repozytoria ProjectTemplate i TeamUtilities Twojego zespołu

Procedury obsługi używa katalogi w sieci lokalnej DSVM jako pośrednie przemieszczania witryny. Jeśli musisz dostosować Twojej **ProjectTemplate** i **TeamUtilities** repozytoriów, aby spełniać niektóre określonych potrzeb zespołu, możesz to zrobić w przedostatni kroku procedury. Poniżej przedstawiono podsumowanie kroków używany do generowania zawartości **MyTeamProjectTemplate** i **MyTeamUtilities** repozytoria dla zespołu analizy danych. Poszczególne kroki odpowiadają podpunkty w procedurze obsługi:

- Klonowanie repozytorium grupy do katalogu lokalnego: team R1 - sklonować z -> lokalnego D1
- Klonowanie repozytoriami zespołu w lokalnych katalogach: team R3 & R4 - sklonować z -> D3 & D4 lokalnego
- Skopiuj zawartość szablonu projektu grupy do folderu lokalnego zespołu: D1 — zawartość jest kopiowana do -> D3
- (Opcjonalnie) dostosowywania D3 & D4 lokalnego
- Wypychania zawartości katalogu lokalnego do zespołu repozytoria: D3 & D4 — zawartość dodać do -> zespołu R3 & R4


### <a name="initialize-the-team-repositories"></a>Inicjowanie repozytoria zespołu

W tym kroku należy zainicjować repozytorium szablonu projektu zespołowego z repozytorium szablonu projektu grupy:

- **MyTeamProjectTemplate** repozytorium (**R3**) z Twojej **GroupProjectTemplate** (**R1**) repozytorium


### <a name="clone-group-repositories-into-local-directories"></a>Klonowanie repozytoria grupy do katalogów lokalnych

Aby rozpocząć tej procedury:

- Tworzenie katalogów na komputerze lokalnym:
    - Aby uzyskać **Windows**: **C:\GitRepos\GroupCommon** i **C:\GitRepos\MyTeam**
    - Aby uzyskać **Linux**: **GitRepos\GroupCommon** i **GitRepos\MyTeam** w katalogu macierzystego 
- Przejdź do katalogu **GitRepos\GroupCommon**.
- Uruchom następujące polecenie, w systemie operacyjnym komputera lokalnego.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Te polecenia clone Twojej **GroupProjectTemplate** repozytorium (R1) na serwerze programu VSTS grupy do katalogu lokalnego w **GitRepos\GroupCommon** na komputerze lokalnym. Po zakończeniu klonowania katalogu **GroupProjectTemplate** (D1) jest tworzony w katalogu **GitRepos\GroupCommon**. W tym miejscu przyjęto założenie, utworzony za pomocą Menedżera grupy projektu zespołowego **GroupCommon**i **GroupProjectTemplate** repozytorium jest w tym projekcie zespołowym. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Klonowanie repozytoriami zespołu w katalogach lokalnych

Te polecenia clone Twojej **MyTeamProjectTemplate** (R3) i **MyTeamUtilities** repozytoriów (R4) w projekcie zespołowym **MyTeam** na serwerze programu VSTS grupy, aby  **MyTeamProjectTemplate** (D3) i **MyTeamUtilities** katalogów (D4) w **GitRepos\MyTeam** na komputerze lokalnym. 

- Przejdź do katalogu **GitRepos\MyTeam**
- Uruchom następujące polecenia, zgodnie z potrzebami, w systemie operacyjnym komputera lokalnego. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Po zakończeniu klonowania dwa katalogi **MyTeamProjectTemplate** (D3) i **MyTeamUtilities** (D4) są tworzone w katalogu **GitRepos\MyTeam**. Firma Microsoft ma zakłada, że w tym miejscu nazwę projektu zespołowego repozytoria szablonu i narzędzia **MyTeamProjectTemplate** i **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>Skopiuj zawartość szablonu projektu grupy do katalogu lokalnego zespołu projektu szablonu

Aby skopiować zawartość lokalnej **GroupProjectTemplate** (D1) folderu do lokalnej **MyTeamProjectTemplate** (D3), uruchom jedno z poniższych skryptów powłoki: 

#### <a name="from-the-powershell-command-line-for-windows"></a>Z wiersza polecenia dla systemu Windows PowerShell       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>W przypadku systemu Linux **DSVM systemu Linux**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Skrypty wykluczać zawartość katalogu .git. Skrypty monit o podanie **ukończyć ścieżki** D1 katalog źródłowy i katalog docelowy D3.
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Dostosowywanie z szablonu projektu zespołowego lub narzędzia team (opcjonalnie)

Dostosowywanie programu **MyTeamProjectTemplate** (D3) i **MyTeamUtilities** (D4), jeśli to konieczne, na tym etapie procesu instalacji. 

- Jeśli chcesz dostosować zawartość D3 do potrzeb zespołu, można zmodyfikować dokumentów szablon lub zmień struktury katalogów.

- Jeśli zespół opracowała niektóre narzędzia, które chcesz udostępnić całe zespołowi, skopiować i wkleić te narzędzia directory D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Wypychania zawartości katalogu lokalnego do zespołu repozytoria

Aby dodać zawartość w katalogach lokalnych (opcjonalnie dostosowane) D3 i D4 do repozytoriów zespołu R3 i R4, uruchom następujące git bash poleceń z poziomu konsoli programu Windows PowerShell lub z poziomu powłoki systemu Linux. Uruchom polecenia z **GitRepos\MyTeam\MyTeamProjectTemplate** katalogu.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Pliki w repozytorium MyTeamProjectTemplate serwera programu VSTS tej grupy są synchronizowane niemal natychmiast po uruchomieniu tego skryptu.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Teraz uruchom ten sam zestaw cztery polecenia usługi git z **GitRepos\MyTeam\MyTeamUtilities** katalogu. 

> [AZURE.NOTE]Jeśli po raz pierwszy, zatwierdzenia w repozytorium Git, należy skonfigurować parametry globalne *user.name* i *user.email* przed uruchomieniem `git commit` polecenia. Uruchom następujące dwa polecenia:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Jeśli są zobowiązuje się do wielu repozytoriów narzędzia Git, należy użyć tej samej nazwy i adresu e-mail po zatwierdzeniu do każdego z nich. Przy użyciu tej samej nazwy i adresu e-mail jest wygodne później podczas tworzenia pulpitów nawigacyjnych usługi Power BI do śledzenia działań Git na wielu repozytoriów.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Tworzenie zespołu danych i ich analiza zasobów (opcjonalnie)

Udostępnianie danych i ich analiza zasobów z zespołem całego ma korzyści w zakresie wydajności i kosztów: członków zespołu można wykonać swoje projekty do zasobów udostępnionych, Zapisz budżetów i efektywniej współpracować. W tej sekcji udostępniamy instrukcje dotyczące sposobu tworzenia usługi Azure file storage. W następnej sekcji udostępniamy instrukcje na temat sposobu magazyn plików Azure instalacji na komputerze lokalnym. Aby uzyskać dodatkowe informacje na temat udostępniania inne zasoby, takie jak dane nauki maszynach wirtualnych platformy Azure, Azure Spark w usłudze Hdinsight, zobacz [platform i narzędzi](platforms-and-tools.md). Ten temat zawiera wskazówki z punktu widzenia nauki dane dotyczące wybierania zasobów, które są odpowiednie do potrzeb i łączy do stron produktu i innych samouczków odpowiednie i użyteczne, które firma Microsoft opublikowane.

>[AZURE.NOTE] Aby uniknąć danych transmisji między centrami danych, które mogą być powolne i kosztowne, upewnij się, że grupa zasobów, konto magazynu i maszyny Wirtualnej platformy Azure (np. DSVM) znajdują się w tym samym centrum danych Azure. 

Uruchom następujące skrypty w celu utworzenia usługi Azure file storage dla zespołu. Magazyn plików Azure dla swojego zespołu może służyć do przechowywania zasobów danych, które są przydatne w przypadku całego zespołu. Skrypty monit Azure informacji konto i subskrypcja, więc z tych poświadczeń gotowa do przejścia. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Utwórz magazyn plików Azure przy użyciu programu PowerShell z systemu Windows

Uruchom ten skrypt z programu PowerShell wiersza polecenia:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Zaloguj się do swojego konta Microsoft Azure, po wyświetleniu monitu:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Wybierz subskrypcję platformy Azure, którego chcesz użyć:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Wybierz konto magazynu, które będzie używać lub Utwórz nową w ramach wybranej subskrypcji:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Wprowadź nazwę usługi Azure file storage do utworzenia. Tylko małe przypadek liter, cyfr i - akceptowane są:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

W celu ułatwienia instalowania i udostępniania tego magazynu, po jego utworzeniu, Zapisz informacje magazynu plików na platformę Azure do pliku tekstowego, a następnie Zanotuj ścieżkę do lokalizacji. W szczególności należy zainstalować z usługi Azure file storage na maszynach wirtualnych Azure w następnej sekcji tego pliku. 

Jest dobrą praktyką jest zaewidencjonować ten plik do repozytorium ProjectTemplate Twojego zespołu. Firma Microsoft zaleca, aby umieścić w katalogu **Docs\DataDictionaries**. W związku z tym ten zasób danych są dostępne dla wszystkich projektów w zespół. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Utwórz magazyn plików Azure przy użyciu skryptu systemu Linux

Uruchom ten skrypt z poziomu powłoki systemu Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Zaloguj się do swojego konta Microsoft Azure, postępując zgodnie z instrukcjami na ekranie:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Wybierz subskrypcję platformy Azure, którego chcesz używać:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Wybierz konto magazynu, które będzie używać lub Utwórz nową w ramach wybranej subskrypcji:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Wprowadź nazwę usługi Azure file storage można utworzyć, tylko małych liter, cyfr i - akceptowane są:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

W celu ułatwienia uzyskiwania dostępu do tego magazynu, po jego utworzeniu, Zapisz informacje magazynu plików na platformę Azure do pliku tekstowego, a następnie Zanotuj ścieżkę do lokalizacji. W szczególności należy zainstalować z usługi Azure file storage na maszynach wirtualnych Azure w następnej sekcji tego pliku.

Jest dobrą praktyką jest zaewidencjonować ten plik do repozytorium ProjectTemplate Twojego zespołu. Firma Microsoft zaleca, aby umieścić w katalogu **Docs\DataDictionaries**. W związku z tym ten zasób danych są dostępne dla wszystkich projektów w zespół. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Magazyn plików Azure instalacji (opcjonalnie)

Po pomyślnym utworzeniu usługi Azure file storage może być instalowany na komputerze lokalnym przy użyciu jednej z poniższych skryptów programu PowerShell lub Linux.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Magazyn plików Azure instalacji przy użyciu programu PowerShell z systemu Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Należy zalogować się w pierwszym, jeśli użytkownik nie zalogował się. 

Kliknij przycisk **Enter** lub **y** aby kontynuować, gdy zostanie wyświetlony monit, jeśli masz informacje plików na platformę Azure magazynu plików, a następnie wprowadź ***ukończyć ścieżkę i nazwę** tworzenia w pliku poprzedniego kroku. Informacje można zainstalować usługi Azure file storage jest do odczytu bezpośrednio z pliku, a dane są przejść do następnego kroku.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Jeśli nie masz plik zawierający informacje magazyn plików na platformę Azure, na końcu tej sekcji znajdują się instrukcje wprowadzania informacji z klawiatury.

Następnie zostanie wyświetlona prośba o wprowadzenie nazwy dysku, które mają zostać dodane do maszyny wirtualnej. Lista istniejącej nazwy dysków są drukowane na ekranie. Należy podać nazwę dysku, który nie istnieje już na liście.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Upewnij się, że nowy dysk F został pomyślnie zainstalowany na tym komputerze.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Jak ręcznie wprowadzić informacje o magazyn plików Azure:** , jeśli nie ma danych magazyn plików Azure w pliku tekstowym, możesz postępuj zgodnie z instrukcjami na ekranie następujące do typu w subskrypcji wymagane konta magazynu i Azure informacje o pliku magazynu:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Typ w subskrypcji platformy Azure. nazwa, wybierz magazyn kont, których tworzone jest magazyn plików Azure, i wpisz nazwę magazynu plików na platformę Azure:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Magazyn plików Azure instalacji przy użyciu skryptu systemu Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Należy zalogować się w pierwszym, jeśli użytkownik nie zalogował się. 

Kliknij przycisk **Enter** lub **y** aby kontynuować, gdy zostanie wyświetlony monit, jeśli masz informacje plików na platformę Azure magazynu plików, a następnie wprowadź ***ukończyć ścieżkę i nazwę** tworzenia w pliku poprzedniego kroku. Informacje można zainstalować usługi Azure file storage jest do odczytu bezpośrednio z pliku, a dane są przejść do następnego kroku.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Następnie zostanie wyświetlona prośba o wprowadzenie nazwy dysku, które mają zostać dodane do maszyny wirtualnej. Lista istniejącej nazwy dysków są drukowane na ekranie. Należy podać nazwę dysku, który nie istnieje już na liście.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Upewnij się, że nowy dysk F został pomyślnie zainstalowany na tym komputerze.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Jak ręcznie wprowadzić informacje o magazyn plików Azure:** , jeśli nie ma danych magazyn plików Azure w pliku tekstowym, możesz postępuj zgodnie z instrukcjami na ekranie następujące do typu w subskrypcji wymagane konta magazynu i Azure informacje o pliku magazynu:

- Dane wejściowe **n**.
- Wybierz indeks Nazwa subskrypcji, w którym magazyn plików Azure została utworzona w poprzednim kroku:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Wybierz konto magazynu w ramach Twojej subskrypcji i typ w nazwie magazyn plików Azure:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Wprowadź nazwę dysku, które mają zostać dodane do komputera, które powinny się różnić od wszelkie istniejące:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Skonfiguruj zasady kontroli zabezpieczeń 

Na serwerze programu VSTS grupy strony głównej, kliknij **koło zębate ikonę** obok swojej nazwy użytkownika w prawym górnym rogu wybierz **zabezpieczeń** kartę. Możesz dodawać członków do zespołu w tym miejscu przy użyciu różnych uprawnień.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Kolejne kroki

Oto łącza do bardziej szczegółowe opisy ról i zadań zdefiniowanych przez proces nauki danych zespołu:

- [Zadania menedżera grupy dla zespołu nauki danych](group-manager-tasks.md)
- [Zespół realizacji zadania dla zespołu nauki danych](team-lead-tasks.md)
- [Zadania realizacji projektu dla zespołu nauki danych](project-lead-tasks.md)
- [Współautorzy poszczególnych projektu dla zespołu nauki danych](project-ic-tasks.md)