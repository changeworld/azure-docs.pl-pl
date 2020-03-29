---
title: Zadania dla prowadzącego projekt w procesie nauki o danych zespołu
description: Szczegółowy przewodnik po zadaniach dla potencjalnego klienta projektu w zespole zespołu ds.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714418"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Zadania prowadzące projekt w procesie nauki o danych zespołu

W tym artykule opisano zadania, które *kończy potencjalny klient projektu* w celu skonfigurowania repozytorium dla zespołu projektu w procesie nauki o danych [zespołu](overview.md) (TDSP). TDSP to struktura opracowana przez firmę Microsoft, która zapewnia uporządkowaną sekwencję działań w celu efektywnego wykonywania opartych na chmurze rozwiązań analizy predykcyjnej. TDSP ma na celu poprawę współpracy i uczenia się zespołowego. Aby uzyskać zarys ról personelu i skojarzonych zadań dla zespołu nauki o danych standaryzujących na TDSP, zobacz [Role i zadania procesu nauki o danych zespołu](roles-tasks.md).

Kierownik projektu zarządza codziennymi działaniami poszczególnych analityków danych w ramach określonego projektu do nauki o danych w TDSP. Na poniższym diagramie przedstawiono przepływ pracy dla zadań głównych projektu:

![Przepływ pracy zadania potencjalnego klienta projektu](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

W tym samouczku opisano krok 1: Tworzenie repozytorium projektu i krok 2: Repozytorium projektu seed z repozytorium projecttemplate zespołu. 

Dla krok 3: Tworzenie elementu roboczego funkcji dla projektu i krok 4: Dodawanie wątków dla faz projektu, zobacz [Zwinne opracowywanie projektów do nauki o danych](agile-development.md).

Dla krok 5: Tworzenie i dostosowywanie zasobów magazynu/analizy i udostępniania, jeśli to konieczne, zobacz [Tworzenie danych zespołu i zasobów analitycznych](team-lead-tasks.md#create-team-data-and-analytics-resources).

W przypadku kroku 6: Konfigurowanie kontroli zabezpieczeń repozytorium projektu, zobacz [Dodawanie członków zespołu i konfigurowanie uprawnień](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> W tym artykule użyto usługi Azure Repos do skonfigurowania projektu TDSP, ponieważ tak jest, jak implementować TDSP w firmie Microsoft. Jeśli zespół używa innej platformy hostingowej kodu, zadania prowadzące projekt są takie same, ale sposób ich ukończenia może być inny.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [menedżer grupy](group-manager-tasks.md) i [kierownik zespołu](team-lead-tasks.md) skonfigurowali następujące zasoby i uprawnienia:

- **Organizacja** Azure DevOps dla twojej jednostki danych
- Projekt **zespołowy** dla zespołu do nauki o danych
- **Repozytoria** szablonów i narzędzi zespołu
- **Uprawnienia** do konta instytucji do tworzenia i edytowania repozytoriów dla projektu

Aby sklonować repozytoria i zmodyfikować zawartość na komputerze lokalnym lub maszynie wirtualnej do nauki o danych (DSVM) lub skonfigurować magazyn plików platformy Azure i zamontować ją w systemie DSVM, należy również wziąć pod uwagę tę listę kontrolną:

- Subskrypcja platformy Azure.
- Git zainstalowany na komputerze. Jeśli używasz DSVM, Git jest wstępnie zainstalowany. W przeciwnym razie zobacz [załącznik Platformy i narzędzia](platforms-and-tools.md#appendix).
- Jeśli chcesz użyć DSVM, Windows lub Linux DSVM utworzone i skonfigurowane na platformie Azure. Aby uzyskać więcej informacji i instrukcji, zobacz [dokumentację maszyny wirtualnej do nauki o danych](/azure/machine-learning/data-science-virtual-machine/).
- W przypadku systemu Windows DSVM, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowany na komputerze. W pliku *README.md* przewiń w dół do sekcji **Pobierz i zainstaluj** i wybierz najnowszy **instalator**. Pobierz instalator *.exe* ze strony instalatora i uruchom go. 
- W przypadku systemu Linux DSVM klucz publiczny SSH skonfigurowany na maszynie DSVM i dodany w usłudze Azure DevOps. Aby uzyskać więcej informacji i instrukcji, zobacz sekcję **Tworzenie klucza publicznego SSH** w [dodatku Platformy i narzędzia](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Tworzenie repozytorium projektu w projekcie zespołowym

Aby utworzyć repozytorium projektów w projekcie **MyTeam** zespołu:

1. Przejdź do strony **podsumowanie** projektu zespołu pod adresem *https:\//\<\<nazwa serwera>/\<nazwa organizacji>/ nazwa zespołu>*, na przykład **https:\//dev.azure.com/DataScienceUnit/MyTeam**, i wybierz **Repozytorium** z lewej nawigacji. 
   
1. Wybierz nazwę repozytorium u góry strony, a następnie wybierz pozycję **Nowe repozytorium** z listy rozwijanej.
   
   ![Wybierz nowe repozytorium](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. W oknie **dialogowym Tworzenie nowego repozytorium** upewnij się, że w obszarze **Typ**jest zaznaczona opcja **Git** . Wprowadź *DSProject1* w obszarze **Nazwa repozytorium**, a następnie wybierz pozycję **Utwórz**.
   
   ![Tworzenie repozytorium](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Upewnij się, że możesz zobaczyć nowe repozytorium **DSProject1** na stronie ustawień projektu. 
   
   ![Repozytorium projektu w ustawieniach projektu](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importowanie szablonu zespołu do repozytorium projektów

Aby wypełnić repozytorium projektu zawartością repozytorium szablonów zespołu:

1. Na stronie **Podsumowanie** projektu zespołu wybierz **pozycję Repozytoria** w lewej nawigacji. 
   
1. Wybierz nazwę repozytorium u góry strony i wybierz **DSProject1** z listy rozwijanej.
   
1. Na stronie **DSProject1 jest pusta,** wybierz pozycję **Importuj**. 
   
   ![Wybierz pozycję Importuj](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. W oknie **dialogowym Importowanie repozytorium Git** wybierz pozycję **Git** jako **typ źródła**i wprowadź adres URL repozytorium **TeamTemplate** w obszarze **Clone URL**. Adres URL to *\//\<https: nazwa\<serwera>/ nazwa\<organizacji>/ nazwa zespołu>/_git/\<nazwa repozytorium szablonu zespołu>*. Na przykład: **https:\//dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Wybierz pozycję **Import**. Zawartość repozytorium szablonów zespołu są importowane do repozytorium projektu. 
   
   ![Importowanie repozytorium szablonów zespołu](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Jeśli chcesz dostosować zawartość repozytorium projektu do określonych potrzeb projektu, możesz dodawać, usuwać lub modyfikować pliki i foldery repozytorium. Możesz pracować bezpośrednio w repozytorium platformy Azure lub sklonować repozytorium na komputerze lokalnym lub maszynie DSVM, wprowadzać zmiany i zatwierdzać i wypychać aktualizacje do udostępnionego repozytorium projektu. Postępuj zgodnie z instrukcjami w [Dostosowywanie zawartości repozytoriów zespołu](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Następne kroki

Oto linki do szczegółowych opisów innych ról i zadań zdefiniowanych przez proces nauki o danych zespołu:

- [Zadania menedżera grupy dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania zespołu Lead dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania poszczególnych współautorów dla zespołu do nauki o danych](project-ic-tasks.md)
