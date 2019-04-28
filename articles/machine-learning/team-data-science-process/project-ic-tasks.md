---
title: Zadania dla poszczególnych współautora w procesie nauki o danych zespołu
description: Zarys zadania dla poszczególnych współautorem projektu zespołowego do nauki o danych.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a52907fa6c0e2483479031fbb3d1ad68a121d95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043356"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Zadania dla poszczególnych współautora w procesie nauki o danych zespołu

W tym temacie wymieniono zadania, które jest pojedynczy Współautor oczekiwano dla swojego zespołu do nauki o danych. Celem jest zapewnienie środowiska zespół współpracowników, standardowego na [zespołu danych dla celów naukowych](overview.md) (TDSP). Konspekt ról pracowników i ich skojarzone zadania, które są obsługiwane przez zespół do nauki o danych standaryzacji na temat tego procesu dla [zespołu danych dla celów naukowych role i zadania](roles-tasks.md).

Zadania poszczególnych uczestników projektu (analitykom danych), aby skonfigurować środowisko przetwarzania TDSP dla projektu są przedstawione w następujący sposób: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** to repozytorium, że jest utrzymywanie grupy, aby udostępniać przydatne narzędzia w całej grupie. 
- **TeamUtilities** jest repozytorium, którego Twój zespół jest utrzymanie specjalnie dla Twojego zespołu. 

Aby uzyskać instrukcje na temat sposobu wykonywania projektu nauki o danych, w ramach przetwarzania TDSP, zobacz [wykonywania danych do analizy projektów](project-execution.md). 

>[AZURE.NOTE] Firma Microsoft opisano kroki niezbędne do skonfigurowania środowiska TDSP środowisko pracy dla zespołu za pomocą DevOps platformy Azure w poniższych instrukcjach. Firma Microsoft umożliwia określenie sposobu wykonywania tych zadań, za pomocą DevOps platformy Azure, ponieważ jest to, jak wygląda implementacja przetwarzania TDSP w firmie Microsoft. Jeśli innej platformie hosting kodu jest używany dla tej grupy, zadania, które muszą zostać wykonane przez lider zespołu, zazwyczaj nie należy zmieniać. Ale sposobem wykonania tych zadań ma być inna.


## <a name="repositories-and-directories"></a>Repozytoria i katalogi

Ten samouczek używa skrócone nazwy dla repozytoriów i katalogów. Nazwy te ułatwiają wykonaj operacje między repozytoria i katalogi. Ten zapis (**R** dla repozytoriów Git i **D** dla katalogi lokalne na maszyny wirtualnej DSVM) jest używany w następujących sekcjach:

- **R2**: Repozytorium GroupUtilities w usłudze Git, który Menedżer grupy został skonfigurowany na serwerze grupy DevOps platformy Azure.
- **R4**: Repozytorium TeamUtilities w usłudze Git, który skonfigurował Twoje lider zespołu.
- **R5**: Repozytorium projektu w usłudze Git, który został skonfigurowany przez użytkownika kierownika projektu.
- **D2**: Sklonowany katalog lokalny z R2.
- **D4**: Sklonowany katalog lokalny z R4.
- **D5**: Sklonowany katalog lokalny z R5.


## <a name="step-0-prerequisites"></a>Step-0: Wymagania wstępne

Wymagania wstępne są spełnione, wykonując zadania przydzielone do swojego przełożonego grupy opisane w temacie [menedżera grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md). Aby podsumować zapotrzebowanie w tym miejscu, następujące wymagania muszą zostać spełnione przed przystąpieniem do wykonywania zadań kierownik zespołu: 
- Menedżer grupy został skonfigurowany **GroupUtilities** repozytorium (jeśli istnieje). 
- Skonfigurował Twoje lider zespołu **TeamUtilities** repozytorium (jeśli istnieje).
- Twojego kierownika projektu skonfigurował repozytorium projektu. 
- Dodano Cię do repozytorium projektu przez Twojego kierownika projektu z uprawnieniami do klonowania z i wypchnięcia z powrotem do repozytorium projektu.

Druga Strona, **TeamUtilities** repozytorium, wymagań wstępnych jest opcjonalna w zależności od tego, czy Twój zespół ma repozytorium narzędzia specyficzne dla zespołu. Jeśli dowolne inne trzy wstępnie wymagane składniki nie zostanie zakończone, skontaktuj się z Twojej lider zespołu, Twoje Kierownik projektu lub ich delegatów, aby ustawić go tak, postępując zgodnie z instrukcjami dotyczącymi [lider zespołu zadań dla zespołu do nauki o danych](team-lead-tasks.md) lub [ Zadania potencjalnych klientów dla zespołu do nauki o danych w projekcie](project-lead-tasks.md).

- Git musi być zainstalowany na tym komputerze. Jeśli używasz maszyny wirtualnej do nauki o danych (DSVM) wstępnie zainstalowane narzędzia Git, i jest gotowe. W przeciwnym razie zobacz [dodatku platformami i narzędziami](platforms-and-tools.md#appendix).  
- Jeśli używasz **Windows DSVM**, musisz mieć [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowana na tym komputerze. W pliku README.md, przewiń w dół do **Pobierz i zainstaluj** sekcji, a następnie kliknij przycisk *najnowszą wersję Instalatora*. Spowoduje to przejście do najnowszych strony Instalatora. Pobierz Instalator .exe, w tym miejscu i uruchom go. 
- Jeśli używasz **Linux maszyny wirtualnej DSVM**, utworzyć klucz publiczny SSH na maszyny wirtualnej DSVM i dodać go do grupy usług DevOps platformy Azure. Aby uzyskać więcej informacji na temat protokołu SSH, zobacz **utworzyć publiczny klucz SSH** sekcji [dodatku platformami i narzędziami](platforms-and-tools.md#appendix). 
- Jeśli potencjalny klient projekt i/lub zespół utworzył niektóre usługi Azure file storage, który chcesz zainstalować maszyny wirtualnej DSVM, należy uzyskać informacje o magazynu plików platformy Azure z nich. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Krok 1-3: Grupa klonów, zespół i repozytoria projektu na komputerze lokalnym

Ta sekcja zawiera instrukcje dotyczące kończenia zadań pierwszych trzech poszczególnych uczestników projektu: 

- Klonuj **GroupUtilities** repozytorium R2 D2
- Klonuj **TeamUtilities** repozytorium R4 – D4 
- Klonuj **projektu** repozytorium R5 D5.

Na komputerze lokalnym Utwórz katalog ***C:\GitRepos*** (dla Windows) lub ***$home/GitRepos*** (forLinux), a następnie przejdź do tego katalogu. 

Uruchom jeden z następujących poleceń (odpowiednio dla swojego systemu operacyjnego), aby sklonować swoje **GroupUtilities**, **TeamUtilities**, i **projektu** repozytoria do katalogów na użytkownika komputer lokalny: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Upewnij się, że trzy foldery w katalogu projektu.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Upewnij się, że trzy foldery w katalogu projektu.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Krok 4-5: Instalowanie usługi Azure file storage, aby maszyny wirtualnej DSVM (opcjonalnie)

Do instalacji usługi Azure file storage do maszyny wirtualnej DSVM, należy zapoznać się z instrukcjami w sekcji 4 [zadań kierownik zespołu dla zespołu do nauki o danych](team-lead-tasks.md)

## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do bardziej szczegółowy opis ról i zadań zdefiniowanych przez zespół danych dla celów naukowych:

- [Menedżer grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania kierownik zespołu dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania potencjalnych klientów dla zespołu do nauki o danych w projekcie](project-lead-tasks.md)
- [Poszczególnych uczestników projektu dla zespołu do nauki o danych](project-ic-tasks.md)

