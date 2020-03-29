---
title: Zwinne opracowywanie projektów do nauki o danych - Team Data Science Process
description: Wykonaj projekt nauki o danych w sposób systematyczny, kontrolowany przez wersję i współpracę w ramach zespołu projektowego przy użyciu procesu nauki o danych zespołu.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722105"
---
# <a name="agile-development-of-data-science-projects"></a>Zwinne opracowywanie projektów do nauki o danych

W tym dokumencie opisano, jak deweloperzy mogą wykonywać projekt nauki o danych w sposób systematyczny, kontrolowany przez wersję i współpracy w ramach zespołu projektu przy użyciu [procesu nauki o danych zespołu](overview.md) (TDSP). TDSP to struktura opracowana przez firmę Microsoft, która zapewnia uporządkowaną sekwencję działań w celu efektywnego wykonywania opartych na chmurze rozwiązań analizy predykcyjnej. Aby uzyskać zarys ról i zadań obsługiwanych przez zespół analityki danych standaryzujący w programie TDSP, zobacz [Role i zadania procesu nauki o danych zespołowych.](roles-tasks.md) 

Ten artykuł zawiera instrukcje dotyczące: 

- Wykonaj *planowanie sprintu* dla elementów roboczych zaangażowanych w projekt.
- Dodawanie *elementów roboczych* do sprintów.
- Tworzenie i używanie *szablonu elementu roboczego pochodzenia agile,* który specjalnie dostosowuje się do etapów cyklu życia TDSP.

Poniższe instrukcje opisują kroki potrzebne do skonfigurowania środowiska zespołu TDSP przy użyciu usłudze Azure Boards i Azure Repos w usłudze Azure DevOps. Instrukcje używają usługi Azure DevOps, ponieważ tak jest implementowanie TDSP w firmie Microsoft. Jeśli grupa używa innej platformy hostingowej kodu, zadania prowadzące zespół zazwyczaj nie zmieniają się, ale sposób wykonania zadań jest inny. Na przykład łączenie elementu pracy z gałęzią Git może nie być taka sama z usługą GitHub, jak w przypadku aplikacji Repo platformy Azure.

Na poniższym rysunku przedstawiono typowy przepływ pracy planowania sprintu, kodowania i kontroli źródła dla projektu nauki o danych:

![Zespołowe przetwarzanie danych dla celów naukowych](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Typy elementów roboczych

W ramach planowania sprintu TDSP istnieją cztery często używane typy *elementów roboczych:* *Funkcje,* *Historie użytkowników,* *Zadania*i *Błędy.* Zaległości dla wszystkich elementów pracy jest na poziomie projektu, a nie na poziomie repozytorium Git. 

Oto definicje typów elementów roboczych:

- **Funkcja**: Funkcja odpowiada zaangażowaniu projektu. Różne kontakty z klientem są różne funkcje i najlepiej jest wziąć pod uwagę różne fazy projektu jako różne funkcje. Jeśli wybierzesz schemat, taki jak * \<ClientName\<>- EngagementName>* do nazwy funkcji, można łatwo rozpoznać kontekst projektu i zaangażowania z samych nazw.
  
- **Historia użytkownika:** Historie użytkownika są elementami roboczymi potrzebnymi do ukończenia funkcji end-to-end. Przykłady historii użytkownika obejmują:
  - Pobieranie danych 
  - Eksplorowanie danych 
  - Generowanie obiektów
  - Tworzenie modeli
  - Operacjonalizowanie modeli 
  - Ponowne szkolenie modeli
  
- **Zadanie**: Zadania są przypisywanymi elementami roboczymi, które należy wykonać, aby ukończyć określony wątek użytkownika. Na przykład zadania w *danych otrzymuuj* historię użytkownika mogą być następujące:
  - Uzyskaj poświadczenia programu SQL Server
  - Przekazywanie danych do magazynu danych SQL
  
- **Błąd:** Błędy są problemy w istniejącym kodzie lub dokumentów, które muszą zostać naprawione, aby zakończyć zadanie. Jeśli błędy są spowodowane przez brakujące elementy robocze, mogą eskalować do historie użytkownika lub zadania. 

Analitycy danych mogą czuć się bardziej komfortowo przy użyciu elastycznego szablonu, który zastępuje funkcje, historie użytkownika i zadania etapami cyklu życia tdsp i podnajem. Aby utworzyć szablon agile, który jest specjalnie wyrównyany z etapami cyklu życia TDSP, zobacz [Używanie szablonu roboczego programu Agile TDSP](#set-up-agile-dsp-6).

> [!NOTE]
> TDSP pożycza pojęcia funkcji, historie użytkownika, zadania i błędy z zarządzania kodem oprogramowania (SCM). Pojęcia TDSP mogą się nieznacznie różnić od ich konwencjonalnych definicji SCM.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Planowanie sprintów

Wielu analityków danych zajmuje się wieloma projektami, co może potrwać miesiące i przebiegać w różnym tempie. Planowanie sprintu jest przydatne do ustalania priorytetów projektu oraz planowania i alokacji zasobów. W usłudze Azure Boards można łatwo tworzyć, zarządzać i śledzić elementy robocze dla projektów i przeprowadzać planowanie sprintu, aby upewnić się, że projekty posuwają się do przodu zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat planowania sprintu, zobacz [Sprinty Scrum](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Aby uzyskać więcej informacji na temat planowania sprintu w usłudze Azure Boards, zobacz [Przypisywanie elementów zaległości do sprintu](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Dodawanie obiektu do zaległości 

Po utworzeniu repozytorium kodu projektu i projektu można dodać funkcję do zaległości, aby reprezentować pracę dla projektu.

1. Na stronie projektu wybierz **pozycję Zaległości tablic** > **w** lewej nawigacji. 
   
1. Jeśli na karcie **Zaległości,** jeśli typem elementu roboczego na górnym pasku są **wątki,** rozwijane i wybierz pozycję **Funkcje**. Następnie wybierz **pozycję Nowy element roboczy.**
   
   ![Wybierz nowy element pracy](./media/agile-development/2-sprint-team-overview.png)
   
1. Wprowadź tytuł funkcji, zazwyczaj nazwę projektu, a następnie wybierz pozycję **Dodaj do góry**. 
   
   ![Wprowadzanie tytułu i wybieranie opcji Dodaj do góry](./media/agile-development/3-sprint-team-add-work.png)
   
1. Z listy **Zaległości** wybierz i otwórz nową funkcję. Wypełnij opis, przypisz członka zespołu i ustaw parametry planowania. 
   
   Funkcję można również połączyć z repozytorium kodu repozytorium repozytorium repozytorium repozytorium usługi Azure, wybierając pozycję **Dodaj łącze** w sekcji **Deweloper.** 
   
   Po edycji funkcji wybierz pozycję **Zapisz & Zamknij**.
   
   ![Edytuj operację i wybierz pozycję Zapisz & Zamknij](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Dodawanie wątku użytkownika do funkcji 

W obszarze Funkcja można dodać historie użytkownika, aby opisać główne kroki potrzebne do ukończenia projektu. 

Aby dodać nową relację użytkownika do funkcji:

1. Na karcie **Zaległości** wybierz **+** po lewej stronie funkcji. 
   
   ![Dodawanie nowej historii użytkownika w obszarze Funkcja](./media/agile-development/4-sprint-add-story.png)
   
1. Nadaj historii użytkownika tytuł i edytuj szczegóły, takie jak przypisanie, stan, opis, komentarze, planowanie i priorytet. 
   
   Historię użytkownika można również połączyć z gałęzią repozytorium kodu repozytorium repozytorium repozytorium repozytorium repozytorium usługi Azure, wybierając **pozycję Dodaj łącze** w sekcji **Deweloper.** Wybierz repozytorium i gałąź, z którą chcesz połączyć element roboczy, a następnie wybierz przycisk **OK**.
   
   ![Dodaj link](./media/agile-development/5-sprint-edit-story.png)
   
1. Po zakończeniu edytowania wątku użytkownika wybierz pozycję **Zapisz & Zamknij**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Dodawanie zadania do relacji użytkownika 

Zadania są szczegółowe kroki, które są potrzebne do ukończenia każdego wątku użytkownika. Po zakończeniu wszystkich zadań wątku użytkownika, historia użytkownika powinna być również ukończona. 

Aby dodać zadanie do wątku **+** użytkownika, zaznacz obok elementu Historia użytkownika i wybierz pozycję **Zadanie**. Wypełnij tytuł i inne informacje w zadaniu.

![Dodawanie zadania do relacji użytkownika](./media/agile-development/7-sprint-add-task.png)

Po utworzeniu funkcji, wątków użytkownika i zadań można je wyświetlić w **widokach Zaległości** lub **Tablice,** aby śledzić ich stan.

![Widok zaległości](./media/agile-development/8-sprint-backlog-view.png)

![Widok tablic](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Używanie elastycznego szablonu roboczego TDSP

Analitycy danych mogą czuć się bardziej komfortowo przy użyciu elastycznego szablonu, który zastępuje funkcje, historie użytkownika i zadania etapami cyklu życia tdsp i podnajem. W usłudze Azure Boards można utworzyć szablon agile, który używa etapów cyklu życia TDSP do tworzenia i śledzenia elementów roboczych. Poniższe kroki obejmują konfigurowanie szablonu procesu agile specyficzne dla nauki o danych i tworzenie elementów roboczych do nauki o danych na podstawie szablonu.

### <a name="set-up-an-agile-data-science-process-template"></a>Konfigurowanie szablonu procesu nauki o danych agile

1. Na stronie głównej organizacji usługi Azure DevOps wybierz **pozycję Ustawienia organizacji** z lewej strony nawigacji. 
   
1. W **ustawieniach organizacji** nawigacja w lewo w obszarze **Tablice**wybierz pozycję **Proces**. 
   
1. W okienku **Wszystkie procesy** wybierz **...** obok **pozycji Agile**, a następnie wybierz pozycję **Utwórz proces dziedziczony**.
   
   ![Tworzenie procesu dziedziczonego z agile](./media/agile-development/10-settings.png) 
   
1. W oknie dialogowym **Tworzenie dziedziczonego z programu Agile** wprowadź nazwę *AgileDataScienceProcess*i wybierz pozycję **Utwórz proces**.
   
   ![Tworzenie procesu AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. W **obszarze Wszystkie procesy**wybierz nowy **program AgileDataScienceProcess**. 
   
1. Na karcie **Typy elementów roboczych** wyłącz opcję **Epic**, **Feature**, **User Story**i **Task,** zaznaczając **...** obok każdego elementu, a następnie wybierając **pozycję Wyłącz**. 
   
   ![Wyłączanie typów elementów roboczych](./media/agile-development/12-disable.png)
   
1. W **obszarze Wszystkie procesy**wybierz kartę **Poziomy zaległości.** W obszarze Zaległości **portfeli**wybierz **...** obok **pozycji Epic (wyłączone),** a następnie wybierz pozycję **Edytuj/Zmień nazwę**. 
   
1. W oknie dialogowym **Edytowanie poziomu zaległości:**
   1. Pod **nazwą**, zastąpić **Epic** z *TDSP Projects*. 
   1. W obszarze **Typy elementów pracy na tym poziomie zaległości**wybierz pozycję Nowy typ elementu **pracy**, wprowadź pozycję *Projekt TDSP*i wybierz pozycję **Dodaj**. 
   1. W obszarze **Domyślny typ elementu pracy**— rozwijane i wybierz pozycję **Projekt TDSP**. 
   1. Wybierz **pozycję Zapisz**.
   
   ![Ustawianie poziomu zaległości portfela](./media/agile-development/13-rename.png)  
   
1. Wykonaj te same kroki, aby zmienić nazwę **funkcji** na *etapy TDSP*i dodaj następujące nowe typy elementów roboczych:
   
   - *Zrozumienie biznesowe*
   - *Pozyskiwanie danych*
   - *Modelowanie*
   - *wdrażania*
   
1. W obszarze **Zaległości wymagań**zmień nazwę **wątków** na *Podświetlenia TDSP*, dodaj nowy typ elementu roboczego *Podnapięcie TDSP*i ustaw domyślny typ elementu roboczego na **Podszepię TDSP**.
   
1. W obszarze **Zaległości iteracji**dodaj nowy typ elementu roboczego *TDSP Task*i ustaw go jako domyślny typ elementu roboczego. 
   
Po wykonaniu kroków poziomy zaległości powinny wyglądać następująco:
   
 ![Poziomy zaległości szablonu TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Tworzenie elementów roboczych procesu nauki o agile data science

Szablon procesu nauki o danych służy do tworzenia projektów TDSP i śledzenia elementów roboczych, które odpowiadają etapom cyklu życia TDSP.

1. Na stronie głównej organizacji programu Azure DevOps wybierz pozycję **Nowy projekt**. 
   
1. W oknie dialogowym **Tworzenie nowego projektu** nadaj projektowi nazwę, a następnie wybierz pozycję **Zaawansowane**. 
   
1. W obszarze **Proces elementu roboczego**rozwijane i **wybieranie pozycji AgileDataScienceProcess**, a następnie wybierz pozycję **Utwórz**.
   
   ![Tworzenie projektu TDSP](./media/agile-development/15-newproject.png)
   
1. W nowo utworzonym projekcie wybierz **pozycję Zaległości zarządów** > **Backlogs** w lewej nawigacji.
   
1. Aby uczynić projekty TDSP widocznymi, wybierz ikonę **Konfiguruj ustawienia zespołu.** Na ekranie **Ustawienia** zaznacz pole wyboru **Projekty TDSP,** a następnie wybierz pozycję **Zapisz i zamknij**.
   
   ![Pole wyboru Wybierz pozycję Projekty TDSP](./media/agile-development/16-enabledsprojects1.png)
   
1. Aby utworzyć projekt TDSP specyficzny dla nauki o danych, wybierz pozycję **Projekty TDSP** na górnym pasku, a następnie wybierz pozycję **Nowy element roboczy**. 
   
1. W wyskakującym okienku nadaj elementowi roboczemu programu TDSP Project nazwę i wybierz pozycję **Dodaj do góry**.
   
   ![Tworzenie elementu roboczego projektu do nauki o danych](./media/agile-development/17-dsworkitems0.png)
   
1. Aby dodać element pracy w ramach projektu **+** TDSP, wybierz następny do projektu, a następnie wybierz typ elementu pracy do utworzenia. 
   
   ![Wybierz typ elementu roboczego nauki o danych](./media/agile-development/17-dsworkitems1.png)
   
1. Wypełnij szczegóły w nowym elemencie roboczym i wybierz pozycję **Zapisz & Zamknij**.
   
1. Kontynuuj wybieranie **+** symboli obok elementów roboczych, aby dodać nowe etapy, podgrupy i zadania TDSP. 
   
Oto przykład, jak elementy robocze projektu do nauki o danych powinny być wyświetlane w widoku **zaległości:**

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Następne kroki

[Wspólne kodowanie z Git](collaborative-coding-with-git.md) opisuje, jak wykonać wspólne tworzenie kodu dla projektów nauki o danych przy użyciu git jako struktury rozwoju udostępnionego kodu i jak połączyć te działania kodowania do pracy planowanej z procesem agile.

[Przykładowe wskazówki](walkthroughs.md) zawierają listę instruktajni określonych scenariuszy z łączami i opisami miniatur. Połączone scenariusze ilustrują sposób łączenia narzędzi i usług w chmurze i lokalnie w przepływy pracy lub potoki w celu tworzenia inteligentnych aplikacji.
  
Dodatkowe zasoby dotyczące zwinnych procesów:

- [Proces Agile](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Zwinne typy elementów roboczych procesu i przepływ pracy](/azure/devops/boards/work-items/guidance/agile-process-workflow)

