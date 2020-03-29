---
title: Zadania dla indywidualnego współautora w procesie nauki o danych zespołu
description: Szczegółowy przewodnik po zadaniach dla indywidualnego współautora w projekcie zespołu do nauki o danych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721255"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Zadania dla indywidualnego współautora w procesie nauki o danych zespołu

W tym temacie opisano zadania wykonywane przez *poszczególnych współautorów* w celu skonfigurowania projektu w [procesie nauki o danych zespołu](overview.md) (TDSP). Celem jest praca w środowisku zespołowym współpracy, który standaryzuje na TDSP. TDSP ma na celu poprawę współpracy i uczenia się zespołowego. Aby uzyskać zarys ról personelu i skojarzonych z nimi zadań obsługiwanych przez zespół analityki danych standaryzujący w programie TDSP, zobacz [Role i zadania procesu nauki o danych zespołowych](roles-tasks.md).

Na poniższym diagramie przedstawiono zadania, które projektu poszczególnych współautorów (analityków danych) wykonać, aby skonfigurować ich środowiska zespołu. Aby uzyskać instrukcje dotyczące sposobu wykonywania projektu nauki o danych w ramach TDSP, zobacz [Wykonywanie projektów do nauki o danych.](project-execution.md) 

![Zadania poszczególnych współautorów](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** jest repozytorium, które zespół projektu utrzymuje do udostępniania szablonów projektu i zasobów.
- **Narzędzia zespołu** to repozytorium narzędzi, które twój zespół przechowuje specjalnie dla twojego zespołu. 
- **Narzędzia grupy** to repozytorium, które grupa przechowuje w celu udostępniania przydatnych narzędzi w całej grupie. 

> [!NOTE] 
> W tym artykule użyto usługi Azure Reppos i maszyny wirtualnej do nauki o danych (DSVM) w celu skonfigurowania środowiska TDSP, ponieważ tak jest, jak zaimplementować TDSP w firmie Microsoft. Jeśli zespół korzysta z innych platform hostingowych lub deweloperskich, poszczególne zadania współautora są takie same, ale sposób ich ukończenia może być inny.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że następujące zasoby i uprawnienia zostały skonfigurowane przez [menedżera grupy,](group-manager-tasks.md) [kierownika zespołu](team-lead-tasks.md)i [potencjalnego kierownika projektu:](project-lead-tasks.md)

- **Organizacja** Azure DevOps dla jednostki do nauki o danych
- **Repozytorium projektów** skonfigurowane przez potencjalny klient projektu do udostępniania szablonów i zasobów projektu
- **Repozytoria narzędzi grupowych** i **narzędzi zespołowych** utworzone przez menedżera grupy i kierownika zespołu, jeśli dotyczy
- **Magazyn plików** platformy Azure skonfigurowany dla zasobów udostępnionych dla zespołu lub projektu, jeśli dotyczy
- **Uprawnienia** do klonowania i wypychania do repozytorium projektów 

Aby sklonować repozytoria i zmodyfikować zawartość na komputerze lokalnym lub maszynie DSVM lub zainstalować magazyn plików platformy Azure na maszynie DSVM, należy wziąć pod uwagę tę listę kontrolną:

- Subskrypcja platformy Azure.
- Git zainstalowany na komputerze. Jeśli używasz DSVM, Git jest wstępnie zainstalowany. W przeciwnym razie zobacz [załącznik Platformy i narzędzia](platforms-and-tools.md#appendix).
- Jeśli chcesz użyć DSVM, Windows lub Linux DSVM utworzone i skonfigurowane na platformie Azure. Aby uzyskać więcej informacji i instrukcji, zobacz [dokumentację maszyny wirtualnej do nauki o danych](/azure/machine-learning/data-science-virtual-machine/).
- W przypadku systemu Windows DSVM, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowany na komputerze. W pliku *README.md* przewiń w dół do sekcji **Pobierz i zainstaluj** i wybierz najnowszy **instalator**. Pobierz instalator *.exe* ze strony instalatora i uruchom go. 
- W przypadku systemu Linux DSVM klucz publiczny SSH skonfigurowany na maszynie DSVM i dodany w usłudze Azure DevOps. Aby uzyskać więcej informacji i instrukcji, zobacz sekcję **Tworzenie klucza publicznego SSH** w [dodatku Platformy i narzędzia](platforms-and-tools.md#appendix). 
- Informacje o magazynie plików platformy Azure dla dowolnego magazynu plików platformy Azure, który należy zainstalować w systemie DSVM. 

## <a name="clone-repositories"></a>Repozytoria klonów

Aby pracować z repozytoriami lokalnie i wypychać zmiany do udostępnionego zespołu i repozytoriów projektów, najpierw skopiuj lub *sklonujesz* repozytoria na komputerze lokalnym. 

1. W programie Azure DevOps przejdź do strony podsumowania projektu zespołu pod adresem *https:\//\<nazwa serwera>/\<nazwa organizacji>/\<nazwa zespołu>*, na przykład **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Wybierz **pozycję Repozytoria** w lewej nawigacji, a u góry strony wybierz repozytorium, które chcesz sklonować.
   
1. Na stronie repozytorium wybierz pozycję **Klonuj** w prawym górnym rogu.
   
1. W oknie dialogowym **Klonowanie repozytorium** wybierz pozycję **HTTPS** dla połączenia HTTP lub **SSH** dla połączenia SSH i skopiuj adres URL klonu w **obszarze Wiersz polecenia** do schowka.
   
   ![Repo klonowania](./media/project-ic-tasks/clone.png)
   
1. Na komputerze lokalnym lub dsvm, utwórz następujące katalogi:
   
   - Dla systemu Windows: **C:\GitRepos**
   - Dla Linuksa: **$home/GitRepos**
   
1. Zmień katalog utworzony.
   
1. W Git Bash uruchom `git clone <clone URL>` polecenie dla każdego repozytorium, które chcesz sklonować. 
   
   Na przykład następujące polecenie klonuje repozytorium **TeamUtilities** do katalogu *MyTeam* na komputerze lokalnym. 
   
   **Połączenie HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Połączenie SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Upewnij się, że foldery sklonowanych repozytoriów są widoczne w katalogu projektów lokalnych.
   
   ![Trzy foldery lokalnego repozytorium](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Instalowanie magazynu plików platformy Azure w systemie DSVM

Jeśli twój zespół lub projekt ma zasoby udostępnione w magazynie plików platformy Azure, zainstaluj magazyn plików na komputerze lokalnym lub maszynie DSVM. Postępuj zgodnie z instrukcjami dotyczącymi [magazynu plików platformy Mount Azure na komputerze lokalnym lub urządzeniu DSVM.](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)

## <a name="next-steps"></a>Następne kroki

Oto linki do szczegółowych opisów innych ról i zadań zdefiniowanych przez proces nauki o danych zespołu:

- [Zadania menedżera grupy dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania zespołu Lead dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania prowadzące projekt dla zespołu do nauki o danych](project-lead-tasks.md)

