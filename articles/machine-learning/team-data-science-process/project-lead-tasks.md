---
title: Zadania w projekcie przewodzenia w procesie nauki o danych zespołu
description: Konspekt zadań, które prowadzą projektu oczekuje się zakończyć w projekcie zespołowym do nauki o danych.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 00b1b58a39724951f2d5e4e688df8eb178654bbb
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952841"
---
# <a name="tasks-for-the-project-lead-in-the-team-data-science-process"></a>Zadania w projekcie przewodzenia w procesie nauki o danych zespołu

W tym samouczku wymieniono zadania, które jest kierownika projektu oczekuje się ukończyć w celu ich zespołu projektu. Celem jest zapewnienie środowiska zespół współpracowników, standardowego na [zespołu danych dla celów naukowych](overview.md) (TDSP). Przetwarzania TDSP jest opracowanym przez firmę Microsoft, który zawiera structured sekwencja działań do wykonania wydajne rozwiązania oparte na chmurze analizy predykcyjnej. Konspekt ról pracowników i ich skojarzone zadania, które są obsługiwane przez zespół do nauki o danych standaryzacji na temat tego procesu dla [zespołu danych dla celów naukowych role i zadania](roles-tasks.md).

A **projektu** zarządza codziennych działań poszczególnych naukowców pracujących nad projektem nauki o danych z konkretnych. Przepływ pracy dla zadań, które mają zostać wykonane przez Liderzy projektów, do skonfigurowania takiego środowiska są przedstawione na poniższym rysunku:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

W tym temacie omówiono obecnie zadania 1,2 i 6 tego przepływu pracy dla projektu potencjalnych klientów.

> [!NOTE]
> Firma Microsoft opisano kroki niezbędne do konfigurowania środowiska TDSP zespołu dla projektu DevOps platformy Azure w poniższych instrukcjach. Firma Microsoft umożliwia określenie sposobu wykonywania tych zadań, za pomocą DevOps platformy Azure, ponieważ jest to, jak wygląda implementacja przetwarzania TDSP w firmie Microsoft. Jeśli innej platformie hosting kodu jest używany dla tej grupy, zadania, które muszą zostać wykonane przez lider zespołu, zazwyczaj nie należy zmieniać. Ale sposobem wykonania tych zadań ma być inna.


## <a name="repositories-and-directories"></a>Repozytoria i katalogi

Ten samouczek używa skrócone nazwy dla repozytoriów i katalogów. Nazwy te ułatwiają wykonaj operacje między repozytoria i katalogi. Ten zapis (R dla repozytoriów Git) i D katalogi lokalne na maszyny wirtualnej DSVM jest używany w następujących sekcjach:

- **R3**: Zespół **ProjectTemplate** repozytorium w usłudze Git skonfigurował Twoje lider zespołu.
- **R5**: Repozytorium projektu w usłudze Git, możesz skonfigurować dla Twojego projektu.
- **D3**: Sklonowany katalog lokalny z R3.
- **D5**: Sklonowany katalog lokalny z R5.


## <a name="0-prerequisites"></a>0. Wymagania wstępne

Wymagania wstępne są spełnione, wykonując zadania przydzielone do swojego przełożonego grupy opisane w temacie [menedżera grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md) i do zespołu potencjalny klient, opisane w temacie [zadań kierownik zespołu dla zespołu do nauki o danych](team-lead-tasks.md). 

Aby podsumować zapotrzebowanie w tym miejscu, następujące wymagania muszą spełniać przed przystąpieniem do wykonywania zadań kierownik zespołu: 

- Twoje **grupie usługom DevOps platformy Azure** (lub konta grupy na niektórych hosting kodu platformy) został skonfigurowany przez menedżera grupy.
- Twoje **repozytorium TeamProjectTemplate** (R3) został skonfigurowany w ramach konta usługi grupy przez Twoje lider zespołu na hosting kodu platformy, która ma być używany.
- Nastąpiło **autoryzacji** przez Twoje lider zespołu do tworzenia repozytoriów na Twoim koncie grupy dla Twojego zespołu.
- Git musi być zainstalowany na tym komputerze. Jeśli używasz maszyny wirtualnej do nauki o danych (DSVM) wstępnie zainstalowane narzędzia Git, i jest gotowe. W przeciwnym razie zobacz [dodatku platformami i narzędziami](platforms-and-tools.md#appendix).  
- Jeśli używasz **Windows DSVM**, musisz mieć [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowana na tym komputerze. W pliku README.md, przewiń w dół do **Pobierz i zainstaluj** sekcji, a następnie kliknij przycisk *najnowszą wersję Instalatora*. Spowoduje to przejście do najnowszych strony Instalatora. Pobierz Instalator .exe, w tym miejscu i uruchom go. 
- Jeśli używasz **Linux maszyny wirtualnej DSVM**, utworzyć klucz publiczny SSH na maszyny wirtualnej DSVM i dodać go do grupy usług DevOps platformy Azure. Aby uzyskać więcej informacji na temat protokołu SSH, zobacz **utworzyć publiczny klucz SSH** sekcji [dodatku platformami i narzędziami](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Tworzenie repozytorium projektu (R5)

- Zaloguj się do usługi grupy usługi Azure DevOps w *https://\<nazwa usługi DevOps platformy Azure\>. visualstudio.com*. 
- W obszarze **ostatnie projekty i zespoły**, kliknij przycisk **Przeglądaj**. Okno, które pojawia się lista wszystkich projektów w usłudze Azure Services metodyki DevOps. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Kliknij nazwę projektu, w którym chcesz utworzyć repozytorium projektu. W tym przykładzie kliknij **MyTeam**. 
- Następnie kliknij przycisk **Navigate** były kierowane do strony głównej projektu **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Kliknij przycisk **współpraca nad kodem** były kierowane do strony głównej usługi git projektu.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Kliknij strzałkę w dół, w lewym górnym rogu, a następnie wybierz pozycję **+ nowe repozytorium**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- W **Utwórz nowe repozytorium** okna, wprowadź nazwę repozytorium git projektu. Upewnij się, że wybrano **Git** jako typ repozytorium. W tym przykładzie używamy nazwy *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Aby utworzyć swoje ***DSProject1*** projektu z repozytorium git, kliknij przycisk **Utwórz**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Inicjator DSProject1 repozytorium projektu

Przedstawione tutaj zadanie podrzędne jest inicjator **DSProject1** repozytorium projektu (R5) z repozytorium szablonów projektu (R3). Procedura rozmieszczania używa katalogów D3 i D5 na lokalnej maszyny wirtualnej DSVM jako pośredniego tymczasowej witryny. Podsumowanie rozmieszczania ścieżka jest: R3 -> D3 -> D5 -> R5.

Jeśli trzeba dostosować swoje **DSProject1** repozytorium projektu w celu spełnienia określonych niektóre projektu potrzeb, możesz to zrobić w przedostatni kroku procedury. Poniżej przedstawiono podsumowanie kroków używany do generowania zawartości **DSProject1** repozytorium projektu. Poszczególne kroki odpowiadają podsekcje rozmieszczania procedury:

- Klonowanie projektu szablonu repozytorium do katalogu lokalnego: team R3 - sklonowany do -> lokalne D3.
- Klonuj repozytorium DSProject1 do katalogu lokalnego: team R5 - sklonowany do -> D5 lokalnego.
- Skopiuj zawartość szablonu projektu sklonowany do lokalnego klona repozytorium DSProject1:  D3 — zawartość jest kopiowana do -> D5.
- (Opcjonalnie) Dostosowywanie D5 lokalnego.
- Wypchnij lokalną zawartość DSProject1 do repozytoriami zespołu: D5 - do -> zespół R5 dodać zawartość.


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Sklonuj repozytorium (R3) szablonu projektu do katalogu (D3) na komputerze lokalnym.

Na komputerze lokalnym Utwórz katalog:

- *C:\GitRepos\MyTeamCommon* dla Windows 
- *$home/GitRepos/MyTeamCommon* dla systemu Linux

Przejdź do tego katalogu. Następnie uruchom następujące polecenie, aby sklonować repozytorium szablonów projektu na komputer lokalny. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Jeśli używasz DevOps platformy Azure jako platformy hostowania kodu, zazwyczaj *HTTPS URL repozytorium szablonów projektu* jest:

 ***https://\<nazwa usługi DevOps platformy Azure\>.visualstudio.com/\<Nazwa projektu\>/_git/\<nazwę repozytorium szablonów projektu\>***. 

W tym przykładzie mamy:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Jeśli używasz DevOps platformy Azure jako platformy hostowania kodu, zazwyczaj *SSH adres URL repozytorium szablonów projektu* jest:

***SSH: / /\<nazwa usługi DevOps platformy Azure\>\@\<nazwa usługi DevOps platformy Azure\>.visualstudio.com:22/\<Your Project Name > /_git/\<szablonu projektu Nazwa repozytorium\>.*** 

W tym przykładzie mamy:

***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Sklonuj repozytorium DSProject1 (R5) do katalogu (D5) na komputerze lokalnym

Zmień katalog na **GitRepos**, i uruchom następujące polecenie, aby sklonować repozytorium projektu na komputer lokalny. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Jeśli używasz DevOps platformy Azure jako platformy hostowania kodu, zazwyczaj _HTTPS URL repozytorium projektu_ jest ***https://\<nazwa usługi DevOps platformy Azure\>.visualstudio.com/\<Your Project Name > /_git/ < Nazwa repozytorium projektu\>***. W tym przykładzie mamy ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Jeśli używasz DevOps platformy Azure jako platformy hostowania kodu, zazwyczaj _SSH adres URL repozytorium projektu_ jest _ssh: / / < nazwa usługi DevOps platformy Azure\>@< nazwa usługi DevOps platformy Azure\>.visualstudio.com:22/ < Your Project Name\>/\_git / < Nazwa repozytorium projektu\>. W tym przykładzie mamy ***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Skopiuj zawartość D3 do D5 

Teraz na komputerze lokalnym, należy skopiować zawartość _D3_ do _D5_, z wyjątkiem metadanych usługi git w katalogu .git. Poniższe skrypty będzie wykonywać zadania. Upewnij się, że wpisz poprawny i pełnej ścieżki do katalogów. Folder źródłowy jest dla zespołu (_D3_); folder docelowy jest w projekcie (_D5_).    

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


### <a name="customize-d5-if-you-need-to-optional"></a>Dostosowywanie D5, jeśli potrzebujesz (opcjonalnie)

Jeśli projekt wymaga niektórych określonych katalogów lub dokumentów, inne niż te, który jest pobierany z szablonu projektu (skopiowany do katalogu D5 w poprzednim kroku), można dostosować zawartość D5 teraz. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>Dodaj zawartość DSProject1 w D5 R5 Twoich usług grupy usługi Azure DevOps

Teraz należy wypychać zawartość w **_DSProject1_** do _R5_ repozytorium w projekcie w usługach infrastruktury DevOps platformy Azure w danej grupie. 


- Przejdź do katalogu **D5**. 
- Użyj następujących poleceń git, aby dodać zawartość w **D5** do **R5**. Polecenia są takie same dla systemów Windows i Linux. 
    
    Dodaj stan usługi git w usłudze git.
    wypchnięcia narzędzia git "wypychania z win DSVM" -m zatwierdzania git
    
- Zatwierdź zmianę i wypychania. 

> [!NOTE]
> Jeśli po raz pierwszy, zatwierdzenia do repozytorium Git, należy skonfigurować parametry globalne *user.name* i *user.email* przed uruchomieniem `git commit` polecenia. Uruchom dwa poniższe polecenia:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Jeśli są zobowiązuje się do wielu repozytoriów Git, użyj taką samą nazwę i adres e-mail dla wszystkich z nich. Przy użyciu tej samej nazwy i adresu e-mail okazuje się wygodne później podczas tworzenia pulpitów nawigacyjnych usługi Power BI, aby śledzić działania usługi Git na wiele repozytoriów.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Tworzenie i instalowanie usługi Azure file storage jako zasoby projektu (opcjonalnie)

Jeśli chcesz utworzyć usługi Azure file storage, aby udostępniać dane, takie jak projekt nieprzetworzone dane lub funkcje generowane dla projektu, tak aby wszystkie elementy członkowskie projektu mają dostęp do tych samych zestawów danych z wielu maszyny postępuj zgodnie z instrukcjami w sekcji 3 i 4 [ Zespół realizacji zadań dla zespołu do nauki o danych](team-lead-tasks.md). 


## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do bardziej szczegółowy opis ról i zadań zdefiniowanych przez zespół danych dla celów naukowych:

- [Menedżer grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania kierownik zespołu dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania potencjalnych klientów dla zespołu do nauki o danych w projekcie](project-lead-tasks.md)
- [Poszczególnych uczestników projektu dla zespołu do nauki o danych](project-ic-tasks.md)
