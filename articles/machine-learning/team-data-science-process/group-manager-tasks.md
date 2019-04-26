---
title: Zadania menedżera grupy danych dla celów naukowych zespołu
description: Zarys zadania dla menedżera grupy, w projekcie zespołowym do nauki o danych.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fb0482be1670a96befdd69a5356c9e21476d9f9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60305412"
---
# <a name="tasks-for-a-group-manager-on-a-data-science-team-project"></a>Zadania dla menedżera grupy, w projekcie zespołowym do nauki o danych

W tym temacie wymieniono, zadania, które Menedżer grupy oczekuje się, aby ukończyć w celu elementy / swojej organizacji do nauki o danych. Celem jest zapewnienie środowiska współpracy grupy, które standaryzuje na [zespołu danych dla celów naukowych](overview.md) (TDSP). Konspekt ról pracowników i ich skojarzone zadania, które są obsługiwane przez zespół do nauki o danych standaryzacji na temat tego procesu dla [zespołu danych dla celów naukowych role i zadania](roles-tasks.md).

**Menedżera grupy** jest kierownikiem jednostki do nauki o danych w przedsiębiorstwie. Jednostka analizy danych może mieć wielu zespołach, z których każdy działa w wielu projektach do nauki o danych w różnych firm branżowych. Menedżer grupy może przekazać swoje zadania, aby zastępczy, ale zadania związane z rolą są takie same. Istnieje sześć głównych zadań, jak pokazano na poniższym diagramie:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


> [!NOTE] 
> Firma Microsoft opisano kroki niezbędne do konfigurowania środowiska grupy przetwarzania TDSP w instrukcji przy użyciu usługi DevOps platformy Azure. Firma Microsoft umożliwia określenie sposobu wykonywania tych zadań, dzięki usługom DevOps platformy Azure, ponieważ jest to, jak wygląda implementacja przetwarzania TDSP w firmie Microsoft. Jeśli inny kod hostingu platformy jest używana dla tej grupy, zadania, które muszą zostać wykonane przez menedżera grupy zazwyczaj nie należy zmieniać. Ale sposobem wykonania tych zadań ma być inna.

1. Konfigurowanie **usługom DevOps platformy Azure** dla grupy.
2. Tworzenie **projekt grupy** w usługach infrastruktury DevOps platformy Azure (dla użytkowników usługi DevOps platformy Azure)
3. Tworzenie **GroupProjectTemplate** repozytorium
4. Tworzenie **GroupUtilities** repozytorium
5. Inicjator **GroupProjectTemplate** i **GroupUtilities** repozytoriów dla usługi Azure DevOps z zawartością z repozytoriów przetwarzania TDSP.
6. Konfigurowanie **środki kontroli bezpieczeństwa** członkowie zespołu mogli uzyskać dostęp do repozytoriów GroupProjectTemplate i GroupUtilities.

Każdy z poprzednich kroków został szczegółowo opisany. Ale najpierw możemy zapoznanie się z skrótów i omówiono wymagania wstępne dotyczące pracy z repozytoriami.

### <a name="abbreviations-for-repositories-and-directories"></a>Skróty dla repozytoriów i katalogów

Ten samouczek używa skrócone nazwy dla repozytoriów i katalogów. Te definicje należy wykonać operacje między repozytoria i katalogi. Notacja ta jest używana w następujących sekcjach:

- **G1**: Repozytorium szablonów projektu rozwinięte i zarządzane przez zespół przetwarzania TDSP przez firmę Microsoft.
- **G2**: Repozytorium narzędzia rozwinięte i zarządzane przez zespół przetwarzania TDSP przez firmę Microsoft.
- **R1**: Repozytorium GroupProjectTemplate w usłudze Git, możesz skonfigurować na serwerze grupy DevOps platformy Azure.
- **R2**: Repozytorium GroupUtilities w usłudze Git, możesz skonfigurować na serwerze grupy DevOps platformy Azure.
- **LG1** i **LG2**: Katalogi lokalne, na komputerze, klonowanie odpowiednio G1 i G2.
- **LR1** i **LR2**: Katalogi lokalne, na komputerze, klonowanie odpowiednio R1 i R2.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Wymagania wstępne dla klonowanie repozytoriów i sprawdzanie kodu wewnątrz i na zewnątrz

- Git musi być zainstalowany na tym komputerze. Jeśli używasz maszyny wirtualnej do nauki o danych (DSVM) wstępnie zainstalowane narzędzia Git, i jest gotowe. W przeciwnym razie zobacz [dodatku platformami i narzędziami](platforms-and-tools.md#appendix).
- Jeśli używasz **Windows DSVM**, musisz mieć [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowana na tym komputerze. W pliku README.md, przewiń w dół do **Pobierz i zainstaluj** sekcji, a następnie kliknij przycisk *najnowszą wersję Instalatora*. Ten krok umożliwia przejście do najnowszych strony Instalatora. Pobierz Instalator .exe, w tym miejscu i uruchom go.
- Jeśli używasz **Linux maszyny wirtualnej DSVM**, utworzyć klucz publiczny SSH na maszyny wirtualnej DSVM i dodać go do grupy usług DevOps platformy Azure. Aby uzyskać więcej informacji na temat protokołu SSH, zobacz **utworzyć publiczny klucz SSH** sekcji [dodatku platformami i narzędziami](platforms-and-tools.md#appendix).


## <a name="1-create-account-on-azure-devops-services"></a>1. Tworzenie konta w usługach Azure DevOps

Usługom DevOps platformy Azure obsługuje następujące repozytoriów:

- **grupy typowych repozytoriów**: Repozytoria ogólnego przeznaczenia, które może zostać przyjęta przez wiele zespołów w obrębie grupy dla wielu projektów do nauki o danych. Na przykład *GroupProjectTemplate* i *GroupUtilities* repozytoriów.
- **zespół repozytoriów**:  Repozytoriów dla określonych zespołów w obrębie grupy. Te repozytoria są specyficzne dla potrzeb zespołu i może być przyjęty przez wiele projektów wykonywane przez tego zespołu, ale nie ogólne wystarczająco użyteczne do wielu zespołów w obrębie grupy do nauki o danych.
- **Projekt repozytoriów**: Dostępne dla konkretnych projektów repozytoriów. Takie repozytoriów może nie być wystarczająco ogólne, by były przydatne do wielu projektów wykonywane przez zespół, a wiele zespołów w grupie do nauki o danych.


### <a name="setting-up-the-azure-devops-services-sign-into-your-microsoft-account"></a>Konfigurowanie logowania usługi DevOps platformy Azure do swojego konta Microsoft

Przejdź do [Visual Studio online](https://www.visualstudio.com/), kliknij przycisk **Zaloguj** w prawym górnym rogu i zaloguj się do swojego konta Microsoft.

![1](./media/group-manager-tasks/login.PNG)

Jeśli nie masz konta Microsoft, kliknij przycisk **Zarejestruj się teraz** do utworzenia konta Microsoft, a następnie zaloguj się przy użyciu tego konta.

Jeśli Twoja organizacja ma subskrypcję Visual Studio/MSDN, kliknij zielony **Zaloguj się przy użyciu swojego konta firmowego lub szkolnego** pole i zaloguj się przy użyciu poświadczeń skojarzonych z tą subskrypcją.

![2](./media/group-manager-tasks/signin.PNG)



Po zalogowaniu kliknij **Create New Account** w prawym górnym rogu, jak pokazano na poniższej ilustracji:

![3](./media/group-manager-tasks/create-account-1.PNG)

Wprowadź informacje dla usług infrastruktury DevOps platformy Azure, którą chcesz utworzyć w **Utwórz konto** kreatora z następującymi wartościami:

- **Adres URL serwera**: Zastąp *mysamplegroup* swoją własną *nazwy serwera*. Adres URL serwera będzie: *https://\<servername\>. visualstudio.com*.
- **Zarządzaj kodem przy użyciu:** Wybierz  **_Git_**.
- **Nazwa projektu:** Wprowadź *GroupCommon*.
- **Organizuj pracę przy użyciu:** Wybierz *Agile*.
- **Hostować Twoje projekty w:** Wybierz lokalizację geograficzną. W tym przykładzie Wybraliśmy *południowo-środkowe stany USA*.

![4](./media/group-manager-tasks/fill-in-account-information.png)

> [!NOTE] 
> Jeśli zobaczysz następujące okno podręczne, po kliknięciu **Utwórz nowe konto**, a następnie kliknij przycisk **zmiany szczegółów** do wyświetlania wszystkich pól, które są wyszczególnione.

![5](./media/group-manager-tasks/create-account-2.png)


Kliknij pozycję **Kontynuuj**.

## <a name="2-groupcommon-project"></a>2. GroupCommon projektu

**GroupCommon** strony (*https://\<servername\>.visualstudio.com/GroupCommon*) jest otwierane po utworzeniu usługi Azure DevOps.

![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Utwórz repozytorium GroupUtilities (R2)

Aby utworzyć **GroupUtilities** repozytorium (R2) w obszarze usługom DevOps platformy Azure:

- Aby otworzyć **Utwórz nowe repozytorium** kreatora, kliknij przycisk **nowe repozytorium** na **kontroli wersji** karty Projekt.

  ![7](./media/group-manager-tasks/create-grouputilities-repo-1.png)

- Wybierz *Git* jako **typu**, a następnie wprowadź *GroupUtilities* jako **nazwa**, a następnie kliknij przycisk **Utwórz**.

  ![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)

Teraz powinny zostać wyświetlone dwa repozytoriów Git **GroupProjectTemplate** i **GroupUtilities** w lewej kolumnie **kontroli wersji** strony:

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Utwórz repozytorium GroupProjectTemplate (R1)

Ustawienia repozytoriów dla serwera grupy DevOps platformy Azure składa się z dwóch zadań:

- Zmień nazwę domyślną **GroupCommon** repozytorium***GroupProjectTemplate***.
- Tworzenie **GroupUtilities** repozytorium na usługom DevOps platformy Azure w ramach projektu **GroupCommon**.

Instrukcje dotyczące pierwszego zadania znajdują się w tej sekcji po uwagi na konwencjach nazewnictwa lub nasze repozytoria i katalogi. Instrukcje dotyczące drugie zadanie podrzędne znajdują się w poniższej sekcji w kroku 4.

### <a name="rename-the-default-groupcommon-repository"></a>Zmiana nazwy repozytorium GroupCommon domyślne

Aby zmienić nazwę domyślną **GroupCommon** repozytorium jako *GroupProjectTemplate* (określanych jako **R1** w ramach tego samouczka):

- Kliknij przycisk **współpraca nad kodem** na **GroupCommon** strony projektu. Spowoduje to przejście do domyślnej strony repozytorium Git projektu **GroupCommon**. Obecnie to repozytorium Git jest pusty.

  ![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)

- Kliknij przycisk **GroupCommon** w lewym górnym rogu (wyróżnioną na poniższej ilustracji czerwoną otoczkę) na stronie repozytorium Git **GroupCommon** i wybierz **Zarządzaj repozytoriami**(wyróżnioną zielone pole na poniższej ilustracji). Ta procedura powoduje wyświetlenie **Panelu sterowania**.
- Wybierz **kontroli wersji** karty Projekt.

  ![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Kliknij przycisk **...**  po prawej stronie **GroupCommon** repozytorium na lewym panelu, a następnie wybierz pozycję **zmiana nazwy repozytorium**.

  ![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)

- W **zmiana nazwy repozytorium GroupCommon** kreatora wprowadź POP, *GroupProjectTemplate* w **nazwę repozytorium** , a następnie kliknij przycisk **Zmień nazwę** .

  ![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-azure-devops-services"></a>5. Zapełnić R1 & R2 repozytoriów na usługom DevOps platformy Azure

Na tym etapie procedury umieszczenia *GroupProjectTemplate* (R1) i *GroupUtilities* repozytoriów (R2), które skonfigurowano w poprzedniej sekcji. Te repozytoria są zasilany z ***ProjectTemplate*** (**G1**) i ***narzędzia*** (**G2**) repozytoria, które są zarządzane przez Firmy Microsoft dla zespołu danych dla celów naukowych. Gdy zostanie ukończona rozmieszczania to:

- repozytorium R1 będzie to miało tego samego zestawu katalogów i szablonów dokumentów, które obsługuje G1
- repozytorium R2 będzie zawierać zestaw narzędzi do analizy danych opracowane przez firmę Microsoft.

Procedura rozmieszczania używa katalogów w lokalnej maszyny wirtualnej DSVM jako pośredniego tymczasowej witryny. Poniżej przedstawiono kroki, a następnie w tej sekcji:

- G1 & G2 - sklonowany do -> LG1 & LG2
- R1 & R2 — sklonowany do -> LR1 & LR2
- LG1 & LG2 - LR1 & LR2 -> pliki kopiowane do
- (Opcjonalnie) Dostosowywanie LR1 & LR2
- LR1 & LR2 — zawartość, Dodaj do -> R1 & R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Klonowanie repozytoriów G1 & G2 do lokalnej maszyny wirtualnej DSVM

W tym kroku klonowania repozytorium Team Data Science naukowych ProjectTemplate (G1) i programy narzędziowe (G2) z repozytoriami GitHub przetwarzania TDSP do folderów w lokalnej maszyny wirtualnej DSVM LG1 oraz LG2:

- Utwórz katalog jako katalog główny do hostowania wszystkie klony w repozytoriów.
  -  W przypadku maszyny wirtualnej DSVM Windows, Utwórz katalog *C:\GitRepos\TDSPCommon*.
  -  W przypadku systemu Linux maszyny wirtualnej DSVM, Utwórz katalog *GitRepos\TDSPCommon* w katalogu macierzystym.

- Uruchom następujący zestaw poleceń z *GitRepos\TDSPCommon* katalogu.

  `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
  `git clone https://github.com/Azure/Azure-TDSP-Utilities`

  ![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Korzystając z naszych nazwy skróconej repozytorium, to co te skrypty zostały osiągnięte:
    - G1 — zostały sklonowane do -> LG1
    - G2 - sklonowany w -> LG2
- Po ukończeniu klonowania powinno być możliwe wyświetlić dwa katalogi _ProjectTemplate_ i _narzędzia_w obszarze **GitRepos\TDSPCommon** katalogu.

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Klonowanie repozytoriów R1 & R2 do lokalnej maszyny wirtualnej DSVM

W tym kroku można sklonować repozytorium GroupProjectTemplate (R1) i GroupUtilities repozytorium (R2) na katalogi lokalne (nazywana LR1 i LR2, odpowiednio) w obszarze **GitRepos\GroupCommon** na maszyny wirtualnej DSVM.

- Aby uzyskać adresy URL R1 i R2 repozytoriów, przejdź do swojej **GroupCommon** strony głównej w usługach infrastruktury DevOps platformy Azure. Ma to zazwyczaj adres URL *https://\<Your Azure DevOps usług Name\>.visualstudio.com/GroupCommon*.
- Kliknij przycisk **kodu**.
- Wybierz **GroupProjectTemplate** i **GroupUtilities** repozytoriów. Skopiuj i Zapisz każdego z adresów URL (protokół HTTPS dla Windows; SSH w systemie Linux) z **adres URL klonowania** element pozycji do użycia w następujących skryptów:

  ![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Zmień na **GitRepos\GroupCommon** katalogu usługi Windows lub Linux maszyny wirtualnej DSVM i uruchom jeden z następujących zestawów poleceń w celu sklonowania R1 i R2 do tego katalogu.

Poniżej przedstawiono skrypty Windows i Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

> [!NOTE] 
> Oczekuje się komunikaty ostrzegawcze LR1 i LR2 są puste.

- Korzystając z naszych nazwy skróconej repozytorium, to co te skrypty zostały osiągnięte:
    - R1 - sklonowany w -> LR1
    - R2 — zostały sklonowane do -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Umieszczenia Twojego GroupProjectTemplate (LR1) i GroupUtilities (LR2)

Następnie na komputerze lokalnym, skopiuj zawartość ProjectTemplate i programy narzędziowe katalogów (z wyjątkiem metadanych w katalogach .git) w obszarze GitRepos\TDSPCommon do GroupProjectTemplate i GroupUtilities katalogów w ramach **GitRepos\ GroupCommon**. Poniżej przedstawiono dwa zadania do wykonania w tym kroku:

- Skopiuj pliki w GitRepos\TDSPCommon\ProjectTemplate (**LG1**) do GitRepos\GroupCommon\GroupProjectTemplate (**LR1**)
- Skopiuj pliki w GitRepos\TDSPCommon\Utilities (**LG2** do GitRepos\GroupCommon\Utilities (**LR2**).

Aby osiągnąć te dwa zadania, uruchom następujące skrypty w konsoli programu PowerShell (Windows) lub konsoli skrypt powłoki (Linux). Monit wejściowy pełne ścieżki LG1 LR1, LG2 oraz LR2. Ścieżki, które należy wprowadzić są weryfikowane. Jeśli wprowadzona katalogu, który nie istnieje, zostanie wyświetlony monit ponownie dane wejściowe.

    # Windows DSVM
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Teraz możesz zobaczyć, że pliki w katalogach LG1 i LG1 (z wyjątkiem plików w katalogu .git) zostały skopiowane do LR1 i LR2, odpowiednio.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)

Teraz widać, że pliki w dwóch folderach (z wyjątkiem plików w katalogu .git) są kopiowane do GroupProjectTemplate i GroupUtilities odpowiednio.

![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Korzystając z naszych nazwy skróconej repozytorium, to co te skrypty zostały osiągnięte:
    - LG1 — pliki kopiowane do LR1 ->
    - LG2 — pliki kopiowane do LR2 ->

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Opcję, aby dostosować zawartość LR1 & LR2
    
Jeśli chcesz dostosować zawartość LR1 i LR2 w celu spełnienia specyficznych potrzeb swojej grupy to etapie procedury, gdzie, który jest odpowiedni. Można modyfikować dokumenty szablonu, zmienić strukturę katalogu i dodawać istniejące narzędzia utworzonego w grupie lub które są przydatne dla całej grupy.

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Dodaj zawartość w LR1 & LR2 R1 & R2 na serwerze grupy

Teraz należy dodać zawartość w LR1 i LR2 repozytoriów R1 i R2. Poniżej przedstawiono git poleceń powłoki bash, które można uruchomić w programie Windows PowerShell lub systemu Linux.

Uruchom następujące polecenia w katalogu GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

Opcja -m pozwala określić komunikat dla zatwierdzeń usługi git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Aby zobaczyć, że w danej grupie usługom DevOps platformy Azure, w repozytorium GroupProjectTemplate pliki są synchronizowane natychmiast.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Na koniec zmień **GitRepos\GroupCommon\GroupUtilities** katalogu i uruchom ten sam zestaw git bash poleceń:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

> [!NOTE] 
> Jeśli po raz pierwszy, zatwierdzenia do repozytorium Git, należy skonfigurować parametry globalne *user.name* i *user.email* przed uruchomieniem `git commit` polecenia. Uruchom dwa poniższe polecenia:
>
>  `git config --global user.name <your name>`  
>  `git config --global user.email <your email address>`
>
> Jeśli są zobowiązuje się do wielu repozytoriów Git, należy użyć takiej samej nazwie i adres e-mail, gdy zdecydujesz się na każdym z nich. Przy użyciu tej samej nazwy i adresu e-mail okazuje się wygodne później podczas tworzenia pulpitów nawigacyjnych usługi Power BI, aby śledzić działania usługi Git na wiele repozytoriów.


- Korzystając z naszych nazwy skróconej repozytorium, to co te skrypty zostały osiągnięte:
    - LR1 — zawartość, Dodaj do -> R1
    - LR2 — zawartość, Dodaj do -> R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Dodaj członków grupy na serwerze grupy

Na stronie głównej grupy usługi Azure DevOps usługi firmy, kliknij przycisk **ikonę koła zębatego** obok swojej nazwy użytkownika w prawym górnym rogu wybierz **zabezpieczeń** kartę. Możesz dodawać członków do grupy w tym miejscu przy użyciu różnych uprawnień.

![24](./media/group-manager-tasks/add_member_to_group.PNG)


## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do bardziej szczegółowy opis ról i zadań zdefiniowanych przez zespół danych dla celów naukowych:

- [Menedżer grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania kierownik zespołu dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania potencjalnych klientów dla zespołu do nauki o danych w projekcie](project-lead-tasks.md)
- [Poszczególnych uczestników projektu dla zespołu do nauki o danych](project-ic-tasks.md)
