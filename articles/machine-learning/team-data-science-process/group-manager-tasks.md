---
title: Menedżera grupy Team danych nauki proces zadania — Azure | Dokumentacja firmy Microsoft
description: Konspekt zadania z kierownikiem projektu zespołowego analizy danych.
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
ms.openlocfilehash: 9f9cdd52aff5445f64a4780d6e04590a06c21105
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="group-manager-tasks"></a>Menedżer grupy zadań

W tym wymieniono temat zadań, które Menedżer grupy oczekuje na zakończenie dla elementy / swojej organizacji analizy danych. Celem jest ustanowienie grupy współpracy środowisko standaryzuje na [proces nauki danych zespołu](overview.md) (TDSP). Konspekt ról pracowników i ich skojarzonych zadań, które są obsługiwane przez zespół nauki danych standaryzacji na ten proces dla [proces nauki danych zespołu ról i zadań](roles-tasks.md).

**Menedżera grupy** Menedżer jednostki analizy danych w przedsiębiorstwie. Jednostka nauki danych może mieć kilka zespołów, z których każdy działa na wielu projektów analizy danych w przypadkach biznesowych. Menedżer grupy może przekazać ich zadań surogatu, ale zadania związane z rolą są takie same. Istnieją sześć głównych zadań, jak pokazano na poniższym diagramie:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Firma Microsoft przedstawiają kroki niezbędne do konfigurowania środowiska grupy TDSP przy użyciu programu VSTS w postępuj zgodnie z instrukcjami. Określono sposobu wykonywania tych zadań z programu VSTS, ponieważ sposób wprowadzania TDSP firmy Microsoft. Jeśli inny kod obsługujący platformy jest używany dla tej grupy, zadania, które muszą zostać wykonane przez menedżera grupy zwykle nie należy zmieniać. Jednak sposób do wykonania tych zadań ma być różne.

1. Konfigurowanie **serwera Visual Studio Team Services (VSTS)** dla grupy.
2. Utwórz **projektu zespołowego grupy** na serwerze programu Visual Studio Team Services (dla użytkowników programu VSTS)
3. Utwórz **GroupProjectTemplate** repozytorium
4. Utwórz **GroupUtilities** repozytorium
5. Seed **GroupProjectTemplate** i **GroupUtilities** repozytoria dla serwera programu VSTS z zawartości z repozytoriami TDSP.
6. Konfigurowanie **kontroli bezpieczeństwa** dla członków zespołu do dostępu do GroupProjectTemplate i GroupUtilities repozytoriów.

Każdy z powyższych kroków opisano szczegółowo. Jednak najpierw, możemy zapoznanie się z skrótów i omówiono wymagania wstępne dotyczące pracy z repozytoriami.

### <a name="abbreviations-for-repositories-and-directories"></a>Skróty dla repozytoriów i katalogów

W tym samouczku używana skróconej nazwy katalogów i repozytoriów. Te definicje należy wykonać operacje między repozytoriów i katalogów. Ten element notation jest używany w następujących sekcjach:

- **G1**: repozytorium szablonu projektu opracowany i zarządzane przez zespół TDSP firmy Microsoft.
- **G2**: repozytorium narzędzia opracowane i zarządzane przez zespół TDSP firmy Microsoft.
- **R1**: GroupProjectTemplate repozytorium na Git skonfigurowaniu na serwerze programu VSTS grupy.
- **R2**: GroupUtilities repozytorium na Git skonfigurowaniu na serwerze programu VSTS grupy.
- **LG1** i **LG2**: katalogi lokalne na komputerze w klonowania odpowiednio G1 i G2.
- **LR1** i **LR2**: katalogi lokalne na komputerze w klonowania odpowiednio R1 i R2.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Wymagania wstępne w klonowania repozytoria i i wyewidencjonowywanie kontroli kodu
 
- Git musi być zainstalowany na tym komputerze. Jeśli używasz maszyny wirtualnej nauki danych (DSVM) Git został wstępnie zainstalowany i jest gotowe. W przeciwnym razie zobacz [dodatku platform i narzędzi](platforms-and-tools.md#appendix).  
- Jeśli używasz **Windows DSVM**, musisz mieć [Git Menedżera poświadczeń (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowana na tym komputerze. W pliku README.md, przewiń w dół do **Pobierz i zainstaluj** sekcji, a następnie kliknij przycisk *najnowszą wersję Instalatora*. W tym kroku prowadzi do najnowszych strony Instalatora. Pobierz Instalatora .exe w tym miejscu i uruchom go. 
- Jeśli używasz **Linux DSVM**, Utwórz klucz publiczny SSH na Twojej DSVM i dodaj go do serwera programu VSTS grupy. Aby uzyskać więcej informacji na temat SSH, zobacz **tworzenie publicznego klucza SSH** sekcji [dodatku platform i narzędzi](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-vsts-server"></a>1. Tworzenie konta na serwerze programu VSTS

Serwer programu VSTS obsługuje repozytoria następujące:

- **grupy wspólne repozytoria**: repozytoria ogólnego przeznaczenia, które mogą zostać zaakceptowany przez kilka zespołów w obrębie grupy dla wielu projektów analizy danych. Na przykład *GroupProjectTemplate* i *GroupUtilities* repozytoriów.
- **zespół repozytoria**: repozytoria dla zespołów określonych w obrębie grupy. Te repozytoriów są specyficzne dla potrzeba zespołu i może być przyjęta przez wiele projektów wykonywane przez tego zespołu, ale nie ogólne wystarczająco powinna być użyteczna, aby kilka zespołów w obrębie grupy analizy danych. 
- **repozytoria projektu**: repozytoria dostępne dla określonych projektów. Takie repozytoria nie może być ogólne, aby była przydatna do wielu projektów wykonywane przez zespół, a także aby kilka zespołów w grupie analizy danych.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Konfigurowanie serwera programu VSTS logowania na koncie Microsoft
    
Przejdź do [Visual Studio online](https://www.visualstudio.com/), kliknij przycisk **Zaloguj** w prawym górnym rogu i zaloguj się do swojego konta Microsoft. 
    
![1](./media/group-manager-tasks/login.PNG)

Jeśli nie masz konta Microsoft, kliknij przycisk **Zamów teraz** do utworzenia konta Microsoft, a następnie zaloguj się przy użyciu tego konta. 

Jeśli Twoja organizacja ma subskrypcję programu Visual Studio/MSDN, kliknij zielony **Zaloguj się przy użyciu konta służbowego** pole i zaloguj się przy użyciu poświadczeń skojarzonych z tą subskrypcją. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Po zalogowaniu, kliknij przycisk **utworzyć nowe konto** w prawym górnym rogu, jak pokazano na poniższej ilustracji:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Wprowadź informacje dla serwera programu VSTS, który ma zostać utworzony w **tworzenia konta** kreatora z następującymi wartościami: 

- **Adres URL serwera**: Zastąp *mysamplegroup* własnymi *nazwy serwera*. Adres URL serwera ma być: *https://\<servername\>. visualstudio.com*. 
- **Zarządzanie za pomocą kodu:** wybierz  **_Git_**.
- **Nazwa projektu:** Enter *GroupCommon*. 
- **Organizowanie pracy przy użyciu:** wybierz *Agile*.
- **Hostuj swoje projekty:** wybierz lokalizację geograficzną. W tym przykładzie wybrany *południowo-środkowe stany*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Jeśli zobacz następujące okno podręczne, po kliknięciu **Utwórz nowe konto**, a następnie kliknij przycisk **zmienić szczegóły** do wyświetlenia wszystkich pól, które są wymienione.

![5](./media/group-manager-tasks/create-account-2.png)


Kliknij przycisk **kontynuować**. 

## <a name="2-groupcommon-team-project"></a>2. GroupCommon zespołu projektu

**GroupCommon** strony (*https://\<servername\>.visualstudio.com/GroupCommon*) zostanie otwarty po utworzeniu serwera usługi VSTS.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Tworzenie repozytorium GroupUtilities (R2)

Aby utworzyć **GroupUtilities** repozytorium (R2) w ramach programu VSTS serwera:

- Aby otworzyć **utworzyć nowe repozytorium** kreatora, kliknij przycisk **nowego repozytorium** na **kontroli wersji** kartę projektu zespołowego. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Wybierz *Git* jako **typu**i wprowadź *GroupUtilities* jako **nazwa**, a następnie kliknij przycisk **Utwórz**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Teraz powinny być widoczne dwa repozytoria Git **GroupProjectTemplate** i **GroupUtilities** w lewej kolumnie **kontroli wersji** strony: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Tworzenie repozytorium GroupProjectTemplate (R1)

Instalator repozytoriów dla serwera grupy VSTS składa się z dwóch zadań:

- Zmień nazwę domyślnego **GroupCommon** repozytorium***GroupProjectTemplate***.
- Utwórz **GroupUtilities** repozytorium na serwerze programu VSTS w ramach projektu zespołowego **GroupCommon**. 

Instrukcje dotyczące pierwszego zadania znajdują się w tej sekcji po uwagi dotyczące konwencji nazewnictwa lub naszych repozytoria i katalogów. Instrukcje dotyczące drugiego zadania znajdują się w poniższej sekcji w kroku 4.

### <a name="rename-the-default-groupcommon-repository"></a>Zmień nazwę domyślnego repozytorium GroupCommon

Aby zmienić domyślne **GroupCommon** repozytorium jako *GroupProjectTemplate* (określanych jako **R1** w tym samouczku):
    
- Kliknij przycisk **współpraca na kod** na **GroupCommon** strony projektu zespołowego. Powoduje to przejście do domyślnej strony repozytorium Git projektu zespołowego **GroupCommon**. To repozytorium Git jest obecnie pusta. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Kliknij przycisk **GroupCommon** w lewym górnym rogu (wyróżniony z czerwonym prostokątem na poniższym rysunku) na stronie repozytorium Git **GroupCommon** i wybierz **Zarządzanie repozytoria**(wyróżniony zielonym pole na poniższej ilustracji). Ta procedura powoduje wyświetlenie **Panelu sterowania**. 
- Wybierz **kontroli wersji** kartę projektu zespołowego. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Kliknij przycisk **...**  z prawej strony **GroupCommon** repozytorium na lewym panelu, a następnie wybierz **repozytorium zmiany nazwy**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- W **Zmień nazwę repozytorium GroupCommon** kreatora, który POP, wprowadź *GroupProjectTemplate* w **nazwę repozytorium** , a następnie kliknij przycisk **Zmień nazwę** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. Inicjatora R1 & repozytoriów R2 na serwerze programu VSTS

Na tym etapie procedury inicjatora *GroupProjectTemplate* (R1) i *GroupUtilities* repozytoria (R2), które można skonfigurować w poprzedniej sekcji. Te repozytoria są obsługiwany z ***ProjectTemplate*** (**G1**) i ***narzędzia*** (**G2**) repozytoriów, które są zarządzane przez Microsoft Team procesu nauki danych. Gdy zostanie ukończona to wstępne wypełnianie:

- repozytorium R1 ma ten sam zestaw katalogów i szablonów dokumentów, które jest G1
- repozytorium R2 będzie zawierać zestaw narzędzi analizy danych opracowaną przez firmę Microsoft.

Procedury obsługi używa katalogi w sieci lokalnej DSVM jako pośrednie przemieszczania witryny. Poniżej przedstawiono kroki zostały wykonane w tej sekcji:

- G1 & G2 - sklonować z -> LG1 & LG2
- R1 i R2 - sklonować z -> LR1 & LR2
- LG1 & LG2 - LR1 & LR2 -> pliki kopiowane do
- (Opcjonalnie) dostosowywania LR1 & LR2
- LR1 & LR2 — zawartość dodać do -> R1 i R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Klonowanie G1 & G2 repozytoria do sieci lokalnej DSVM

W tym kroku można sklonować repozytorium Team danych nauki procesu (TDSP) ProjectTemplate (G1) i narzędzia (G2) z repozytoriami github TDSP do folderów w sieci lokalnej DSVM jako LG1 i LG2:

- Utwórz katalog jako katalog główny do obsługi sieci klony repozytoriów. 
    -  W DSVM systemu Windows, należy utworzyć katalog *C:\GitRepos\TDSPCommon*. 
    -  W Linux DSVM należy utworzyć katalog *GitRepos\TDSPCommon* w katalogu macierzystym. 

- Uruchom następujący zestaw poleceń z *GitRepos\TDSPCommon* katalogu.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Za pomocą naszego nazwy skróconej repozytorium, to co te skrypty zostały osiągnięte: 
    - LG1 -> G1 - zostały sklonowane do
    - LG2 -> G2 - zostały sklonowane do
- Po ukończeniu klonowania powinno być możliwe wyświetlić dwa katalogi _ProjectTemplate_ i _narzędzia_w obszarze **GitRepos\TDSPCommon** katalogu. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Klonowanie repozytoria R1 i R2 do sieci lokalnej DSVM

W tym kroku sklonować repozytorium GroupProjectTemplate (R1) i GroupUtilities repozytorium (R2) w katalogach lokalnych (określanych jako LR1 i LR2, odpowiednio) w obszarze **GitRepos\GroupCommon** na Twojej DSVM.

- Aby uzyskać adresy URL repozytoriów R1 i R2, przejdź do Twojej **GroupCommon** strony głównej na VSTS. Ma to zazwyczaj adres URL *https://\<Your Server VSTS Name\>.visualstudio.com/GroupCommon*. 
- Kliknij przycisk **kod**. 
- Wybierz **GroupProjectTemplate** i **GroupUtilities** repozytoriów. Skopiuj i Zapisz każdy z adresów URL (HTTPS dla systemu Windows; SSH w systemie Linux) z **adres URL klonowania służący** element z kolei do użycia w następujące skrypty:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Zmiany w **GitRepos\GroupCommon** katalogu systemu Windows lub Linux DSVM oraz wykonywania jednej z następujących zestawów poleceń sklonować R1 i R2 do tego katalogu.
        
Poniżej przedstawiono skryptów systemu Windows i Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Oczekuje się komunikaty ostrzegawcze LR1 i LR2 są puste.    

- Za pomocą naszego nazwy skróconej repozytorium, to co te skrypty zostały osiągnięte: 
    - R1 - zostały sklonowane do -> LR1
    - LR2 -> R2 — zostały sklonowane do   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Inicjatora GroupProjectTemplate (LR1), a GroupUtilities (LR2)

Następnie w komputerze lokalnym, skopiuj zawartość katalogów ProjectTemplate i narzędzia (z wyjątkiem metadanych w katalogach .git) w obszarze GitRepos\TDSPCommon do katalogów GroupProjectTemplate i GroupUtilities w obszarze **GitRepos\ GroupCommon**. Poniżej przedstawiono dwa zadania do wykonania w tym kroku:

- Skopiuj pliki w GitRepos\TDSPCommon\ProjectTemplate (**LG1**) do GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Skopiuj pliki w GitRepos\TDSPCommon\Utilities (**LG2** do GitRepos\GroupCommon\Utilities (**LR2**). 

Do osiągnięcia tych dwóch zadań, należy uruchomić następujące skrypty w konsoli programu PowerShell (Windows) lub konsoli skryptu powłoki (Linux). Zostanie wyświetlony monit wejściowy pełnej ścieżki do LG1, LR1 LG2 i LR2. Ścieżek, które możesz wpisać są weryfikowane. Jeśli możesz wpisać katalogu, który nie istnieje, zostanie wyświetlona prośba o wprowadź go ponownie. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Teraz można zobaczyć, czy plików w katalogach LG1 i LG1 (z wyjątkiem plików w katalogu .git) zostały skopiowane do LR1 i LR2, odpowiednio.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Teraz możesz sprawdzić, czy pliki w dwóch folderach (z wyjątkiem plików w katalogu .git) są kopiowane do GroupProjectTemplate i GroupUtilities odpowiednio.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Za pomocą naszego nazwy skróconej repozytorium, to co te skrypty zostały osiągnięte:
    - LG1 - LR1 -> pliki kopiowane do
    - LG2 - LR2 -> pliki kopiowane do

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Opcję, aby dostosować zawartość LR1 & LR2
    
Jeśli chcesz dostosować zawartość LR1 i LR2 w celu spełnienia specyficznych potrzeb Twojej grupy, to jest na etapie procedury, w przypadku, gdy jest odpowiedni. Można modyfikować dokumenty szablonu, zmień strukturę katalogu i Dodaj istniejącego narzędzia, która opracowała grupy lub które są przydatne dla całej grupy. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Dodaj zawartość w LR1 & LR2 R1 & R2 na serwerze grupy

Teraz musisz dodać zawartość w LR1 i LR2 do repozytoriów R1 i R2. Oto git bash poleceń, które można uruchomić w środowisku Windows PowerShell lub Linux. 

Uruchom następujące polecenia w katalogu GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

Opcja -m pozwala określić komunikat dla Twojego zatwierdzenia git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Widać, że w serwerze programu VSTS tej grupy, w repozytorium GroupProjectTemplate pliki są synchronizowane natychmiast.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Na koniec zmień **GitRepos\GroupCommon\GroupUtilities** katalogu i uruchom ten sam zestaw git bash polecenia:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Jeśli po raz pierwszy, zatwierdzenia w repozytorium Git, należy skonfigurować parametry globalne *user.name* i *user.email* przed uruchomieniem `git commit` polecenia. Uruchom następujące dwa polecenia:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Jeśli są zobowiązuje się do wielu repozytoriów narzędzia Git, należy użyć tej samej nazwy i adresu e-mail po zatwierdzeniu do każdego z nich. Przy użyciu tej samej nazwy i adresu e-mail jest wygodne później podczas tworzenia pulpitów nawigacyjnych usługi Power BI do śledzenia działań Git na wielu repozytoriów.


- Za pomocą naszego nazwy skróconej repozytorium, to co te skrypty zostały osiągnięte:
    - LR1 — zawartość dodać do -> R1
    - LR2 — zawartość dodać do -> R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Dodaj członków grupy na serwerze grupy

Na serwerze programu VSTS grupy strony głównej, kliknij **koło zębate ikonę** obok swojej nazwy użytkownika w prawym górnym rogu wybierz **zabezpieczeń** kartę. Możesz dodawać członków do tej grupy, w tym miejscu przy użyciu różnych uprawnień.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Kolejne kroki

Oto łącza do bardziej szczegółowe opisy ról i zadań zdefiniowanych przez proces nauki danych zespołu:

- [Zadania menedżera grupy dla zespołu nauki danych](group-manager-tasks.md)
- [Zespół realizacji zadania dla zespołu nauki danych](team-lead-tasks.md)
- [Zadania realizacji projektu dla zespołu nauki danych](project-lead-tasks.md)
- [Współautorzy poszczególnych projektu dla zespołu nauki danych](project-ic-tasks.md)