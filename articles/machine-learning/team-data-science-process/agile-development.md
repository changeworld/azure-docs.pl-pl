---
title: Programowanie metodą agile projektów do nauki o danych — danych zespołu dla celów naukowych
description: Jak deweloperów, można wykonać projektu nauki o danych w systematyczne, niepoddany kontroli wersji i sposób współpracy w ramach zespołu projektu przy użyciu procesu do nauki o danych zespołu.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: adf713fc3f875168f99b302b0a9affef88e8414f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60327907"
---
# <a name="agile-development-of-data-science-projects"></a>Programowanie metodą agile projektów do nauki o danych

W tym dokumencie opisano, jak deweloperzy można wykonać projektu nauki o danych w systematyczne, niepoddany kontroli wersji i sposób współpracy w ramach zespołu projektu przy użyciu [zespołu danych dla celów naukowych](overview.md) (TDSP). Przetwarzania TDSP jest opracowanym przez firmę Microsoft, który zawiera structured sekwencja działań do wykonania wydajne rozwiązania oparte na chmurze analizy predykcyjnej. Konspekt ról pracowników i ich skojarzonych zadań, które są obsługiwane przez zespół do nauki o danych standaryzacji na temat tego procesu można zobaczyć [zespołu danych dla celów naukowych role i zadania](roles-tasks.md). 

Ten artykuł zawiera instrukcje dotyczące: 

1. czy **planowania sprintu** dla elementów roboczych, które są zaangażowane w projekcie.<br> Jeśli jesteś zaznajomiony z planowania sprintu, można znaleźć szczegółowe informacje ogólne i [tutaj](https://en.wikipedia.org/wiki/Sprint_(software_development) "tutaj"). 
2. **Dodawanie elementów roboczych** do sprintów. 

> [!NOTE]
> Opisano kroki niezbędne do skonfigurowania środowiska TDSP środowisko pracy dla zespołu za pomocą usługi DevOps platformy Azure w poniższy zestaw instrukcji. Określają sposób wykonywania tych zadań dzięki usługom DevOps platformy Azure, ponieważ jest to sposób implementacji przetwarzania TDSP w firmie Microsoft.  Jeśli zdecydujesz się używać usługom DevOps platformy Azure, elementy (3) i (4) na poprzedniej liście są korzyści, które otrzymujesz w sposób naturalny. Jeśli inny kod hostingu platformy jest używana dla tej grupy, zadania, które muszą zostać wykonane przez lider zespołu, zazwyczaj nie należy zmieniać. Ale sposobem wykonania tych zadań ma być inna. Na przykład element w sekcji 6, **połączyć element roboczy z gałęzi Git**, może być bardzo proste, ponieważ jest w usługach infrastruktury DevOps platformy Azure.
>
>

Na poniższym rysunku przedstawiono typowy sprint, planowanie, kodowanie i przepływu pracy kontroli źródła zaangażowane we wdrażaniu projektu nauki o danych:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

W przetwarzania TDSP przebiegu framework planowania, istnieją cztery typy często używanych **elementów roboczych**: **Funkcja**, **historii użytkownika**, **zadań**, i **usterki**. Każdy projekt obsługuje pojedynczą Zaległość dla wszystkich elementów roboczych. Nie występuje Zaległość na poziomie repozytorium Git w projekcie. Oto ich definicje:

- **Funkcja**: Funkcja odnosi się do zaangażowania projektu. Różne engagements za pomocą klienta są traktowane jako różne funkcje. Podobnie najlepiej należy wziąć pod uwagę poszczególne etapy projektu za pomocą klienta jako różne funkcje. Jeśli wybierzesz takie jak schemat ***ClientName EngagementName*** nazwę funkcji, następnie możesz łatwo rozpoznać kontekst projektu/zaangażowania nazw samodzielnie.
- **Historia**: Wątki są elementy pracy, które są potrzebne do ukończenia funkcji (projekt) end-to-end. Przykłady scenariuszy:
    - Pobieranie danych 
    - Eksplorowanie danych 
    - Generowanie funkcji
    - Tworzenie modeli
    - Operacjonalizacji modeli 
    - Ponowne trenowanie modeli
- **Zadanie**: Zadania są można przypisać kod lub elementy robocze dokumentu lub innych działań, które należy wykonać w celu przeprowadzenia określonego wątku. Na przykład zadania w wątku *pobieranie danych* może być:
    -  Pobieranie poświadczeń programu SQL Server 
    -  Przekazywanie danych do usługi SQL Data Warehouse. 
- **Błąd**: Błędy zwykle odnoszą się do poprawki, które są potrzebne do istniejącego kodu lub dokument, które są wykonywane podczas wykonywania zadania. Jeśli ten błąd jest spowodowany przez brak etapów lub zadania, odpowiednio, wartość można przekazać do wątku lub zadania. 

> [!NOTE]
> Pojęcia są pobierają funkcji, historii, zadania i usterki z zarządzania przy użyciu oprogramowania kodu (SCM), który ma być używany w zakresie analizy danych. Może być różnią się nieco od ich poziomu konwencjonalnego definicje SCM.
>
>

> [!NOTE]
> Analitycy danych mogą bez obaw więcej przy użyciu szablonu usługi agile, w szczególności pasującą przy użyciu etapy cyklu życia przetwarzania TDSP. Mając to na uwadze został utworzony szablon planowania sprint pochodne Agile, gdzie Epiki, historie itd. są zastępowane przez etapy cyklu życia przetwarzania TDSP lub substages. Aby uzyskać instrukcje dotyczące sposobu tworzenia szablonu agile, zobacz [Konfigurowanie agile danych dla celów naukowych w programie Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Planowanie przebiegu 

Planowanie sprintu jest przydatna do priorytetyzacji projekt i Planowanie zasobów i alokacji. Wielu analityków, którzy mają do czynienia z wieloma projektami, z których każdy może zająć miesiące, aby zakończyć. Projekty często realizowane na różne tępy. W usługach infrastruktury DevOps platformy Azure można łatwo tworzyć, zarządzać i śledzenia elementów roboczych w projekcie i postępowania sprint planowanie w celu zapewnienia przechodzenia do przodu, zgodnie z oczekiwaniami projektów. 

Postępuj zgodnie z [ten link](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) instrukcje krok po kroku na sprint planowania w usługom DevOps platformy Azure. 


## 3. <a name='AddFeature-3'></a>Dodaj funkcję  

Po utworzeniu repozytorium projektu w projekcie, przejdź do zespołu **Przegląd** strony, a następnie kliknij przycisk **zarządzać pracą**.

![2](./media/agile-development/2-sprint-team-overview.png)

Aby włączyć funkcję do zaległości, kliknij przycisk **zaległości** --> **funkcji** --> **New**, typ w funkcji **tytuł**(zazwyczaj nazwa projektu), a następnie kliknij przycisk **Dodaj** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Kliknij dwukrotnie funkcji, który został utworzony. Podaj opis, przypisać członków zespołu dla tej funkcji i zestawu parametrów dla tej funkcji planowania. 

Możesz również połączyć tę funkcję do repozytorium projektu. Kliknij przycisk **Dodaj link** w obszarze **rozwoju** sekcji. Po zakończeniu edycji tę funkcję, kliknij przycisk **Zapisz i Zamknij** aby wyjść.


## 4. <a name='AddStoryunderfeature-4'></a>Dodaj wątek w ramach funkcji 

W ramach funkcji scenariuszy można dodać do opisano najważniejsze czynności wymagane do zakończenia projektu (funkcja). Aby dodać nowego wątku, kliknij **+** Zaloguj się na lewo od funkcji w widoku listy prac.  

![4](./media/agile-development/4-sprint-add-story.png)

Można edytować szczegóły wątku, takich jak stan, opis, komentarze, planowania i priorytet w oknie podręcznym.

![5](./media/agile-development/5-sprint-edit-story.png)

Ten wątek można połączyć istniejące repozytorium, klikając **+ Dodaj link** w obszarze **rozwoju**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Dodaj zadanie do wątku 

Zadania są określone szczegółowy opis kroków, które są potrzebne do ukończenia każdego wątku. Po ukończeniu wszystkich zadań wątku wątku zbyt należy wykonać. 

Aby dodać zadanie do wątku, kliknij przycisk **+** logowania obok elementu scenariusza, wybierz opcję **zadań**, a następnie wprowadź szczegółowe informacje tego zadania w oknie podręcznym.

![7](./media/agile-development/7-sprint-add-task.png)

Po utworzeniu funkcji, scenariuszy i zadań, można wyświetlić je w **zaległości** lub **tablicy** widoków, aby śledzić jego stan.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Konfigurowanie szablonu pracy Agile przetwarzania TDSP w programie Visual Studio Online

W tym artykule opisano sposób konfigurowania szablonu procesu agile danych do analizy, który używa etapy cyklu życia nauki o danych przetwarzania TDSP i śledzenie elementów roboczych w programie Visual Studio Online (vso). Kroki przedstawione poniżej przewodnik przykład konfigurowania analizy dotyczące danych agile szablonu procesu *AgileDataScienceProcess* i pokazują, jak utworzyć elementy robocze do nauki o danych na podstawie szablonu.

### <a name="agile-data-science-process-template-setup"></a>Ustawienia szablonu procesu do nauki o danych agile

1. Przejdź do strony głównej serwera, **Konfiguruj** -> **procesu**.

    ![10](./media/agile-development/10-settings.png) 

2. Przejdź do **wszystkie procesy** -> **procesy**w obszarze **Agile** i kliknij pozycję **Utwórz dziedziczone procesu**. Następnie umieść nazwę procesu "AgileDataScienceProcess" i kliknij przycisk **tworzenia procesu**.

    ![11](./media/agile-development/11-agileds.png)

3. W obszarze **AgileDataScienceProcess** -> **typów elementów roboczych** pozycję Wyłącz **Epic**, **funkcji**,  **Scenariusz użycia**, i **zadań** elementów roboczych według **Konfiguracja -> Wyłącz**

    ![12](./media/agile-development/12-disable.png)

4. Przejdź do **AgileDataScienceProcess** -> **poziomy zaległości** kartę. Zmień nazwę "Epiki" do "Przetwarzania TDSP projektów", klikając **Konfiguruj** -> **edycji/Rename**. W tym samym oknie dialogowym kliknij **+ nowy typ elementu roboczego** w "Projektu nauki o danych" i ustaw wartość **domyślnego typu elementu roboczego** "Przetwarzania TDSP projektu" 

    ![13](./media/agile-development/13-rename.png)  

5. Podobnie, Zmień nazwę zaległości "Funkcji" na "Etapy przetwarzania TDSP" i dodaj następujące polecenie, aby **typ elementu roboczego nowych**:

    - Poznawanie firmy
    - Pozyskiwanie danych
    - Modelowanie
    - Wdrożenie

6. Zmień nazwę "Scenariusz użycia" na "TDSP Substages" przy użyciu domyślnego typu elementu roboczego typu nowo utworzony "przetwarzania TDSP Substage".

7. Ustaw "Zadania" do nowo utworzonych typu elementu roboczego "Task przetwarzania TDSP" 

8. Po wykonaniu tych kroków poziomów zaległości powinien wyglądać następująco:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Utwórz elementy robocze do nauki o danych

Po utworzeniu szablonu procesu do nauki o danych można tworzyć i śledzenie elementów roboczych do nauki o danych, które odpowiadają przetwarzania TDSP cyklu życia.

1. Podczas tworzenia nowego projektu, wybierz pozycję "Agile\AgileDataScienceProcess" jako **elementów roboczych procesu**:

    ![15](./media/agile-development/15-newproject.png)

2. Przejdź do nowo utworzonego projektu, a następnie kliknij **pracy** -> **zaległości**.

3. Ustawienie widoczności "Projekty przetwarzania TDSP", klikając **Konfiguruj ustawienia zespołu** i sprawdź "Projekty przetwarzania TDSP"; następnie zapisz.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Teraz możesz rozpocząć tworzenie elementów roboczych określonych do nauki o danych.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Poniżej przedstawiono przykładowy wygląd elementy robocze projektu nauki o danych:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Kolejne kroki

[Wspólne kodowanie przy użyciu narzędzia Git](collaborative-coding-with-git.md) opisuje czy tworzenie współpracy kodu dla projektów do nauki o danych przy użyciu narzędzia Git jako architektura deweloperska udostępnionego kodu oraz łączenie ich kodowania działań do pracy, planowane z procesu agile.

Poniżej przedstawiono dodatkowe linki do zasobów na procesów zwinnego wytwarzania oprogramowania.

- Procesu programowania zwinnego   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Typy elementów roboczych procesu agile i przepływ pracy   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Wskazówki, które przedstawiają wszystkie kroki procesu **konkretnych scenariuszy** znajdują się także. Wymieniono i połączone z opisami miniatur w [przykładowe przewodniki](walkthroughs.md) artykułu. One ilustrują sposób łączenia chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 
