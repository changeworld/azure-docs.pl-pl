---
title: Wspólne kodowanie z Git - Team Data Science Process
description: Jak wykonać wspólne tworzenie kodu dla projektów do nauki o danych przy użyciu git z elastycznego planowania.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721901"
---
# <a name="collaborative-coding-with-git"></a>Wspólne kodowanie przy użyciu narzędzia Git

W tym artykule opisano, jak używać git jako struktury rozwoju kodu współpracy dla projektów nauki o danych. W tym artykule opisano, jak połączyć kod w repozytorium platformy Azure z elementami roboczymi [deweloperów agile](agile-development.md) w usłudze Azure Boards, jak przeglądać kod oraz jak tworzyć i scalać żądania ściągania zmian.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Łączenie elementu pracy z gałęzią repozytorium platformy Azure 

Usługa Azure DevOps zapewnia wygodny sposób łączenia wątku użytkownika usługi Azure Boards lub elementu roboczego zadania z gałęzią repozytorium Git repozytorium usługi Azure Repos. Historię użytkownika lub zadanie można połączyć bezpośrednio z kodem z nim skojarzonym. 

Aby połączyć element roboczy z nową gałęzią, wybierz wielokropek **akcji** (**...**) obok elementu roboczego, a następnie w menu kontekstowym przewiń do i wybierz pozycję **Nowa gałąź**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

W oknie **dialogowym Tworzenie gałęzi** podaj nową nazwę oddziału i podstawowe repozytorium i gałąź Git repozytorium i gałęzie repozytorium i gałęzi repozytorium i gałęzi repozytorium i repozytorium azure repozytorium. Repozytorium podstawowe musi znajdować się w tym samym projekcie Azure DevOps jako element pracy. Gałąź podstawową może być gałęzią główną lub inną istniejącą gałęzią. Wybierz **pozycję Utwórz gałąź**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Można również utworzyć nową gałąź za pomocą następującego polecenia Git bash w systemie Windows lub Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Jeśli nie określisz \<podstawowej nazwy gałęzi>, nowa gałąź jest oparta na `master`programie . 

Aby przełączyć się do gałęzi roboczej, uruchom następujące polecenie: 

```bash
git checkout <working branch name>
```

Po przełączeniu do gałęzi pracy, można rozpocząć tworzenie kodu lub artefaktów dokumentacji, aby zakończyć element pracy. Uruchamianie `git checkout master` przełącza cię `master` z powrotem do gałęzi.

Jest dobrą praktyką, aby utworzyć gałąź Git dla każdego elementu pracy wątku użytkownika. Następnie dla każdego elementu roboczego zadania można utworzyć gałąź na podstawie gałęzi historii użytkownika. Organizowanie gałęzi w hierarchii, która odpowiada relacji Historia użytkownika-zadanie, gdy masz wiele osób pracujących nad różnymi wątkami użytkownika dla tego samego projektu lub w różnych zadaniach dla tego samego wątku użytkownika. Można zminimalizować konflikty, mając każdy członek zespołu pracy w innej gałęzi lub na inny kod lub inne artefakty podczas udostępniania gałęzi. 

Na poniższym diagramie przedstawiono zalecaną strategię rozgałęzienia dla TDSP. Może nie być potrzebna tyle gałęzi, ile pokazano w tym miejscu, zwłaszcza gdy tylko jedna lub dwie osoby pracują nad projektem lub tylko jedna osoba pracuje nad wszystkimi zadaniami wątku użytkownika. Ale oddzielenie gałęzi rozwoju od gałęzi głównej jest zawsze dobrą praktyką i może pomóc zapobiec przerywaniu gałęzi wydania przez działania programistyczne. Aby uzyskać pełny opis modelu gałęzi Git, zobacz [Pomyślny model rozgałęzienia Gita](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Można również połączyć element pracy z istniejącą gałęzią. Na stronie **Szczegóły** elementu roboczego wybierz pozycję **Dodaj łącze**. Następnie wybierz istniejącą gałąź, z którymi chcesz połączyć element roboczy, a następnie wybierz przycisk **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Praca nad gałęzią i zatwierdzanie zmian 

Po wkonaniu zmian dla elementu pracy, takich jak dodanie pliku skryptu Języka R do `script` gałęzi komputera lokalnego, można zatwierdzić zmianę z gałęzi lokalnej do gałęzi działającej nadrzędnej przy użyciu następujących poleceń Git bash:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Tworzenie żądania ściągnięcia

Po co najmniej jednym zatwierdzeniu i wypychaniu, gdy będziesz gotowy do scalenia bieżącej gałęzi roboczej z jej gałęzią podstawową, możesz utworzyć i przesłać *żądanie ściągania* w usłudze Azure Repos. 

Na stronie głównej projektu azure DevOps wskaż**żądania ściągnięcia** **repos** > w lewej nawigacji. Następnie wybierz jeden z **przycisków Nowe żądanie ściągnięcia** lub Utwórz łącze **Żądania ściągnięcia.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Na ekranie **Nowe żądanie ściągnięcia,** jeśli to konieczne, przejdź do repozytorium i gałęzi Git, do której chcesz scalić zmiany. Dodaj lub zmień inne informacje. W obszarze **Recenzenci**dodaj nazwy recenzentów, a następnie wybierz pozycję **Utwórz**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Przeglądanie i scalanie

Po utworzeniu żądania ściągnięcia recenzenci otrzymują powiadomienie e-mail, aby przejrzeć żądanie ściągnięcia. Recenzenci sprawdzić, czy zmiany działają i sprawdzić zmiany z żądaniem, jeśli to możliwe. Recenzenci mogą dokonywać komentarzy, żądać zmian oraz zatwierdzać lub odrzucać żądanie ściągnięcia na podstawie ich oceny. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Po zatwierdzeniu zmian przez recenzentów ty lub inna osoba z uprawnieniami do scalania może scalić gałąź roboczą z gałęzią bazową. Wybierz **pozycję Zakończ**, a następnie wybierz pozycję Zakończ **scalanie** w oknie dialogowym Pełne żądanie **ściągnięcia.** Po scaleniu można usunąć gałązę roboczą. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Upewnij się, że żądanie jest oznaczone jako **ZAKOŃCZONE**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Po powrocie do **repozytorium** w lewej nawigacji, widać, że zostałeś przełączony do gałęzi głównej od czasu usunięcia `script` gałęzi.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Można również użyć następujących poleceń Git `script` bash, aby scalić gałązę roboczą z gałęzią podstawową i usunąć gałązę roboczą po scaleniu:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Następne kroki

[Wykonywanie zadań do nauki o danych](execute-data-science-tasks.md) pokazuje, jak używać narzędzi do wykonywania kilku typowych zadań do nauki o danych, takich jak interaktywne eksploracja danych, analiza danych, raportowanie i tworzenie modelu.

[Przykładowe wskazówki](walkthroughs.md) zawierają listę instruktajni określonych scenariuszy z łączami i opisami miniatur. Połączone scenariusze ilustrują sposób łączenia narzędzi i usług w chmurze i lokalnie w przepływy pracy lub potoki w celu tworzenia inteligentnych aplikacji. 

