---
title: Wspólne kodowanie przy użyciu narzędzia Git - zespołu danych dla celów naukowych
description: Jak przeprowadzić tworzenie współpracy kodu dla projektów do nauki o danych przy użyciu narzędzia Git przy użyciu elastycznego planowania.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 551d0cd149c4d1555a40ccf0d7baeff97c6809c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60336380"
---
# <a name="collaborative-coding-with-git"></a>Wspólne kodowanie przy użyciu narzędzia Git

W tym artykule opisano sposób wykonywania tworzenie współpracy kodu dla projektów do nauki o danych przy użyciu narzędzia Git jako architektura deweloperska udostępnionego kodu. Omówiono łączenie ich kodowania działań do pracy, planowane w [programowanie metodą Agile](agile-development.md) oraz sposób przeglądami kodu.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Łącze elementu roboczego z gałęzi Git 

Usługom DevOps platformy Azure zapewnia wygodny sposób łączenia elementu roboczego (wątku lub zadania) z gałęzi Git. Dzięki temu można połączyć z wątku lub zadania bezpośrednio w kodzie skojarzonych z nim. 

Aby połączyć element roboczy do nowej gałęzi, kliknij dwukrotnie element roboczy, a następnie w oknie podręcznym kliknij **Utwórz nową gałąź** w obszarze **+ Dodaj link**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Podaj informacje w tej nowej gałęzi, takich jak nazwa gałęzi, podstawowy repozytorium Git i gałęzi. Repozytorium Git, wybierany musi być repozytorium, w tym samym projekcie, który należy do elementu roboczego. Podstawowy gałęzi może być głównej gałęzi lub istniejącej gałęzi.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Dobrym rozwiązaniem jest utworzenie gałęzi Git dla każdego elementu roboczego scenariusza. Następnie dla każdego elementu roboczego zadanie można utworzyć gałąź na podstawie historii gałęzi. Organizowanie gałęzie w ten sposób hierarchiczne, który odnosi się do relacji historii zadań jest przydatne w przypadku, gdy masz wiele osób pracuje z różnych wątków, z tym samym projekcie lub masz wiele osób pracuje z różnych zadań w tym samym wątku. Gdy każdy członek zespołu działa na inną gałąź i każdy element członkowski działa na różnych kodów lub inne artefakty, podczas udostępniania gałąź, można zminimalizować konflikty. 

Poniższy obraz przedstawia zalecane strategii rozgałęziania dla przetwarzania TDSP. Nie trzeba wiele gałęzi, jak przedstawiono poniżej, szczególnie jeśli masz tylko jedną lub dwie osoby, które działa na tym samym projekcie lub tylko jedna osoba działa we wszystkich zadaniach wątku. Ale Programowanie gałęzi z gałęzią master jest zawsze dobrym rozwiązaniem. Może to pomóc zapobiec przez działań programistycznych w gałęzi wydania. Bardziej szczegółowy opis modelu gałęzi Git można znaleźć w [pomyślne Git rozgałęzianie modelu](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Aby przełączyć się do gałęzi, którą chcesz pracować, uruchom następujące polecenie w poleceniu powłoki (Windows lub Linux). 

    git checkout <branch name>

Zmiana *< Nazwa gałęzi\>*  do **wzorca** przełączników z powrotem do **wzorca** gałęzi. Po przełączeniu się do gałęzi roboczej, możesz rozpocząć pracę na ten element roboczy, tworzenia artefaktów kodu lub dokumentacji, potrzebne do ukończenia elementu. 

Można także połączyć element roboczy do istniejącej gałęzi. W **szczegółów** strony elementu roboczego, zamiast klikać **Utwórz nową gałąź**, możesz kliknąć pozycję **+ Dodaj link**. Wybierz gałąź, który chcesz połączyć element roboczy. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Można również utworzyć nowej gałęzi w Git Bash poleceń. Jeśli < nazwa podstawowa gałęzi\> brakuje, < Nowa nazwa gałęzi\> opiera się na _wzorca_ gałęzi. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Praca w gałęzi i zatwierdzić zmiany 

Teraz załóżmy, że niektóre zmiany do *danych\_pozyskiwania* gałęzi dla elementu roboczego, takie jak dodanie pliku języka R w gałęzi na komputerze lokalnym. Możesz zatwierdzić pliku R dodanego do gałęzi dla tego elementu roboczego, pod warunkiem znajdujesz się w tej gałęzi w powłoce Git przy użyciu następujących poleceń Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Utwórz żądanie ściągnięcia w usługach infrastruktury DevOps platformy Azure 

Gdy jesteś gotowy po kilku zatwierdzeń i wypchnięcia, można scalić gałąź bieżącą gałęzią podstawowy, możesz przesłać **żądania ściągnięcia** w usługach infrastruktury DevOps platformy Azure. 

Przejdź na stronę główną projektu, a następnie kliknij przycisk **kodu**. Wybierz gałąź do scalenia i nazwę repozytorium Git, który chcesz scalić gałęzi do. Następnie kliknij przycisk **żądań ściągnięcia**, kliknij przycisk **nowe żądanie ściągnięcia** utworzyć recenzję żądania ściągnięcia, zanim do jego podstawowej gałęzi scaleniu pracy w gałęzi.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Wypełnij niektóre opis tego żądania ściągnięcia, Dodaj recenzentów i wysłać go.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Przegląd i scalanie 

Po utworzeniu żądania ściągnięcia recenzentów powiadomienie e-mail do przeglądania żądań ściągnięcia. Recenzenci konieczne Sprawdź, czy zmiany działają lub nie oraz przetestować zmiany, dodając osoby żądającej, jeśli jest to możliwe. Oparte na ich ocenę, recenzenci mogli zatwierdzać lub odrzucać żądania ściągnięcia. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Po wykonaniu czynności przeglądu gałęzi roboczej jest scalany jego podstawowej gałęzi, klikając **Complete** przycisku. Można usunąć gałęzi roboczej, po jego został scalony. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Upewnij się, w lewym górnym rogu, żądanie jest oznaczony jako **UKOŃCZONO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Gdy wrócisz do repozytorium, w obszarze **kodu**, pojawi się informacja, że użytkownik został przełączony do głównej gałęzi.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Następujące polecenia Git służy również do scalania gałęzi roboczej do jego podstawowej gałęzi i usuwania gałęzi roboczej po scaleniu:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Kolejne kroki

[Wykonywanie zadań do nauki o danych](execute-data-science-tasks.md) pokazuje, jak wykonać kilka typowych zadań do nauki o danych takich jak interaktywną eksplorację, analizę danych, raportowania i tworzenia modelu za pomocą narzędzi.

Wskazówki, które przedstawiają wszystkie kroki procesu **konkretnych scenariuszy** znajdują się także. Wymieniono i połączone z opisami miniatur w [przykładowe przewodniki](walkthroughs.md) artykułu. One ilustrują sposób łączenia chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

