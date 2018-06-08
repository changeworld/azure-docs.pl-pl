---
title: Zespół prowadzić do projektu zestawu danych nauki proces zadania — Azure | Dokumentacja firmy Microsoft
description: Konspekt zadań dla projektu prowadzić na projektu zespołowego analizy danych.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 58c5826240b7c49ba29c0d8e86a2896e3ce2f7f7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838402"
---
# <a name="project-lead-tasks"></a>Zadania realizacji projektu

Ten samouczek zawiera zadania, które jest realizacji projektu oczekiwano dla swojego zespołu projektu. Celem jest ustalenie środowisko współpracy zespołu standaryzuje na [proces nauki danych zespołu](overview.md) (TDSP). TDSP jest opracowanym przez firmę Microsoft, zapewniająca strukturalnych sekwencji działań do wykonania wydajne rozwiązania oparte na chmurze analizy predykcyjnej. Konspekt ról pracowników i ich skojarzonych zadań, które są obsługiwane przez zespół nauki danych standaryzacji na ten proces dla [proces nauki danych zespołu ról i zadań](roles-tasks.md).

A **projektu** zarządza codzienne działania analityków danych poszczególnych projektu nauki określonych danych. Przepływ pracy dla zadań, które mają zostać wykonane przez projekt prowadzi do skonfigurowania takiego środowiska są przedstawione na poniższej ilustracji:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

W tym temacie omówiono obecnie zadania 1,2 i 6 ten przepływ pracy dla projektu potencjalnych klientów.

>[AZURE.NOTE] Firma Microsoft przedstawiają kroki niezbędne do konfigurowania środowiska TDSP zespołu do projektu przy użyciu programu Visual Studio Team Services (VSTS) w poniższych instrukcjach. Określono sposobu wykonywania tych zadań z programu VSTS, ponieważ sposób wprowadzania TDSP firmy Microsoft. Użycie innej platformie hosting kod dla tej grupy zadań, które muszą zostać wykonane przez realizacji zespołu zwykle nie należy zmieniać. Jednak sposób do wykonania tych zadań ma być różne.


## <a name="repositories-and-directories"></a>Repozytoria i katalogów

W tym samouczku używana skróconej nazwy katalogów i repozytoriów. Te nazwy należy wykonać operacje między repozytoriów i katalogi. Ten element notation (R dla repozytoriów Git) i D dla katalogów lokalnych w sieci DSVM jest używany w następujących sekcjach:

- **R3**: zespół **ProjectTemplate** repozytorium na Git skonfigurował realizacji Twojego zespołu.
- **R5**: repozytorium projektu na Git konfiguracja dla projektu.
- **D3**: sklonowany katalogu lokalnego z R3.
- **D5**: sklonowany katalogu lokalnego z R5.


## <a name="0-prerequisites"></a>0. Wymagania wstępne

Wymagania wstępne są spełnione, wykonując zadania przydzielone do menedżera grupy opisane w temacie [grupy Menedżera zadań dla zespołu nauki danych](group-manager-tasks.md) i do zespołu realizacji opisane w temacie [zespołu realizacji zadań dla zespołu nauki danych](team-lead-tasks.md). 

Podsumowując, w tym miejscu, następujące wymagania muszą spełniać przed rozpoczęciem realizacji zadań zespołu: 

- Twoje **serwera programu VSTS grupy** (lub konta grupy na niektórych hosting kod platformy) nie został skonfigurowany przez menedżera grupy.
- Twoje **TeamProjectTemplate repozytorium** (R3) nie został skonfigurowany przy użyciu tego konta grupy przez realizacji Twojego zespołu na platformie hosting kod ma być używany.
- Nastąpiło **autoryzowany** przez użytkownika realizacji zespołu można utworzyć repozytoria na Twoim koncie grupy dla zespołu.
- Git musi być zainstalowany na tym komputerze. Jeśli używasz maszyny wirtualnej nauki danych (DSVM) Git został wstępnie zainstalowany i jest gotowe. W przeciwnym razie zobacz [dodatku platform i narzędzi](platforms-and-tools.md#appendix).  
- Jeśli używasz **Windows DSVM**, musisz mieć [Git Menedżera poświadczeń (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowana na tym komputerze. W pliku README.md, przewiń w dół do **Pobierz i zainstaluj** sekcji, a następnie kliknij przycisk *najnowszą wersję Instalatora*. Powoduje to przejście do najnowszej strony Instalatora. Pobierz Instalatora .exe w tym miejscu i uruchom go. 
- Jeśli używasz **Linux DSVM**, Utwórz klucz publiczny SSH na Twojej DSVM i dodaj go do serwera programu VSTS grupy. Aby uzyskać więcej informacji na temat SSH, zobacz **tworzenie publicznego klucza SSH** sekcji [dodatku platform i narzędzi](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Utworzyć repozytorium projektu (R5)

- Zaloguj się do serwera programu VSTS grupy na *https://\<nazwa serwera programu VSTS\>. visualstudio.com*. 
- W obszarze **ostatnie projekty i zespoły**, kliknij przycisk **Przeglądaj**. Okno, który powoduje wyświetlenie listy wszystkich projektów zespołowych na serwerze programu VSTS. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Kliknij nazwę projektu zespołowego, w którym chcesz utworzyć repozytorium projektu. W tym przykładzie kliknij **MyTeam**. 
- Następnie kliknij przycisk **Nawigacja** kierowany do strony głównej projektu zespołowego **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Kliknij przycisk **współpraca na kod** kierowany do strony głównej git projektu zespołowego.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Kliknij strzałkę w dół w lewym górnym rogu i wybierz **+ nowe repozytorium**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- W **utworzyć nowe repozytorium** okna, wprowadź nazwę projektu repozytorium git. Upewnij się, że wybrano **Git** jako typ repozytorium. W tym przykładzie używamy nazwy *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Aby utworzyć użytkownika ***DSProject1*** projekt repozytorium git, kliknij przycisk **Utwórz**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Repozytorium projektu DSProject1 inicjatora

Zadania w tym miejscu jest inicjatora **DSProject1** projektu repozytorium (R5) z repozytorium szablonu projektu zespołowego (R3). Procedury obsługi używa katalogów D3 i D5 w sieci lokalnej DSVM jako pośrednie przemieszczania witryny. Podsumowując, jest ścieżka rozmieszczania: D3 -> R3 -> D5 -> R5.

Jeśli musisz dostosować Twojej **DSProject1** repozytorium projektu, aby spełniać niektóre określonych potrzeb projektu, możesz to zrobić w przedostatni kroku procedury. Poniżej przedstawiono podsumowanie kroków używany do generowania zawartości **DSProject1** repozytorium projektu. Poszczególne kroki odpowiadają podpunkty w procedurze obsługi:

- Klonowanie zespołu projektu szablonu repozytorium do katalogu lokalnego: team R3 - sklonować z -> D3 lokalnego.
- Klonowanie repozytorium DSProject1 do katalogu lokalnego: team R5 - sklonować z -> D5 lokalnego.
- Skopiuj zawartość szablonu projektu sklonowany zespołu do klonowania lokalnego repozytorium DSProject1: D3 — zawartość jest kopiowana do -> D5.
- (Opcjonalnie) Dostosowywanie D5 lokalnego.
- Zawartość przekazywana DSProject1 lokalnego do zespołu repozytoria: D5 — zawartość dodać do -> zespołu R5.


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Klonowanie zespołu projektu szablonu repozytorium (R3) do katalogu (D3) na komputerze lokalnym.

Na komputerze lokalnym należy utworzyć katalog:

- *C:\GitRepos\MyTeamCommon* dla systemu Windows 
- *$home/GitRepos/MyTeamCommon* dla systemu Linux

Przejdź do tego katalogu. Następnie uruchom następujące polecenie, aby klonowanie repozytorium szablonu projektu zespołowego na komputerze lokalnym. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Jeśli używasz programu VSTS jako platformy obsługi kodu zazwyczaj *HTTPS URL repozytorium szablonu projektu zespołowego* jest:

 ***https://\<nazwa serwera programu VSTS\>.visualstudio.com/\<nazwę projektu zespołowego\>/_git/\<nazwę repozytorium szablonu projektu zespołowego\>***. 

W tym przykładzie mamy:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Jeśli używasz programu VSTS jako platformy obsługi kodu zazwyczaj *SSH adres URL repozytorium szablonu projektu zespołowego* jest:

***SSH: / /\<nazwa serwera programu VSTS\>@\<nazwa serwera programu VSTS\>.visualstudio.com:22/\<Your Nazwa projektu zespołowego > /_git/\<nazwę repozytorium szablonu projektu zespołowego \>.*** 

W tym przykładzie mamy:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Klonuj repozytorium DSProject1 (R5) do katalogu (D5) na komputerze lokalnym

Zmień katalog na **GitRepos**, i uruchom następujące polecenie, aby klonowanie repozytorium projektu na komputerze lokalnym. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Jeśli używasz programu VSTS jako platformy obsługi kodu zazwyczaj _HTTPS URL repozytorium projektu_ jest ***https://\<nazwa serwera programu VSTS\>.visualstudio.com/\<Your zespołu Nazwa projektu > /_git/ < nazwę repozytorium projektu\>***. W tym przykładzie mamy ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Jeśli używasz programu VSTS jako platformy obsługi kodu zazwyczaj _SSH adres URL repozytorium projektu_ jest _ssh: / / < nazwa serwera programu VSTS\>@< nazwa serwera programu VSTS\>.visualstudio.com:22/<Your Team Project Name> / \_git / < nazwę repozytorium projektu\>. W tym przykładzie mamy ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Skopiuj zawartość D3 do D5 

Teraz w lokalnym komputerze, należy skopiować zawartość _D3_ do _D5_, z wyjątkiem metadanych git w katalogu .git. Następujące skrypty będzie wykonywać zadania. Upewnij się, że wpisz poprawny i pełny ścieżek do katalogów. Folder źródłowy jest dla swojego zespołu (_D3_); folder docelowy jest dla projektu (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Teraz można zobaczyć w _DSProject1_ folderu, wszystkie pliki (z wyjątkiem .git) są kopiowane z _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Teraz można zobaczyć w _DSProject1_ folderu, wszystkie pliki (z wyjątkiem metadanych w .git) są kopiowane z _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Dostosowywanie D5, jeśli zachodzi konieczność (opcjonalnie)

Jeśli projekt wymaga niektórych określonych katalogów lub dokumenty, innych niż te, które można pobrać z szablonu projektu zespołowego (skopiowany do katalogu D5 w poprzednim kroku), można dostosować zawartość D5 teraz. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>Dodaj zawartość DSProject1 w D5 do R5 na serwerze programu VSTS grupy

Teraz musisz wypychania zawartości **_DSProject1_** do _R5_ repozytorium w projekcie zespołowym na serwerze programu VSTS tej grupy. 


- Przejdź do katalogu **D5**. 
- Użyj następujących poleceń git można dodać zawartości **D5** do **R5**. Polecenia są takie same dla systemów z systemami Windows i Linux. 
    
    Dodaj git stan git.
    git commit -m "wypychane z win DSVM" git wypychania
    
- Zatwierdź zmiany i wypychania. 

>[AZURE.NOTE] Jeśli po raz pierwszy, zatwierdzenia w repozytorium Git, należy skonfigurować parametry globalne *user.name* i *user.email* przed uruchomieniem `git commit` polecenia. Uruchom następujące dwa polecenia:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Jeśli są zobowiązuje się do wielu repozytoriów narzędzia Git, użyj taką samą nazwę i adres e-mail we wszystkich z nich. Przy użyciu tej samej nazwy i adresu e-mail jest wygodne później podczas tworzenia pulpitów nawigacyjnych usługi Power BI do śledzenia działań Git na wielu repozytoriów.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Tworzenie i zainstalować magazyn plików Azure jako zasoby projektu (opcjonalne)

Jeśli chcesz utworzyć magazyn plików Azure udostępniania danych, takich jak projekt danych pierwotnych lub funkcje generowane dla projektu, tak aby wszystkie członkowie projektu mają dostęp do tej samej zestawów danych z wielu DSVMs postępuj zgodnie z instrukcjami w sekcji 3 i 4 [ Zespół realizacji zadań dla zespołu nauki danych](team-lead-tasks.md). 


## <a name="next-steps"></a>Kolejne kroki

Oto łącza do bardziej szczegółowe opisy ról i zadań zdefiniowanych przez proces nauki danych zespołu:

- [Zadania menedżera grupy dla zespołu nauki danych](group-manager-tasks.md)
- [Zespół realizacji zadania dla zespołu nauki danych](team-lead-tasks.md)
- [Zadania realizacji projektu dla zespołu nauki danych](project-lead-tasks.md)
- [Współautorzy poszczególnych projektu dla zespołu nauki danych](project-ic-tasks.md)