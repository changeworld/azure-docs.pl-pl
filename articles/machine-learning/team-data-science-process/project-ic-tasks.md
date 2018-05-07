---
title: Zespół zadań w procesie nauki danych dla poszczególnych współautora - Azure | Dokumentacja firmy Microsoft
description: Konspekt zadania dla pojedynczych współautorem projektu zespołowego analizy danych.
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
ms.openlocfilehash: 4951d596f64a17f0bd47100c1fc81397dc5febbd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="individual-contributor-tasks"></a>Poszczególne zadania współautora

W tym temacie wymieniono zadania, które jest poszczególnych współautora oczekiwano dla zespołu nauki ich danych. Celem jest ustalenie środowisko współpracy zespołu standaryzuje na [proces nauki danych zespołu](overview.md) (TDSP). Konspekt ról pracowników i ich skojarzonych zadań, które są obsługiwane przez zespół nauki danych standaryzacji na ten proces dla [proces nauki danych zespołu ról i zadań](roles-tasks.md).

Zadania poszczególnych uczestników projektu (analityków danych), aby skonfigurować środowisko TDSP projektu są przedstawione w następujący sposób: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** repozytorium, który jest obsługę grupy, aby udostępniać przydatne narzędzia w całej grupy. 
- **TeamUtilities** repozytorium, że zespół jest specjalnie dla zespołu. 

Aby uzyskać instrukcje na temat sposobu wykonywania projektu analizy danych, w obszarze TDSP, zobacz [wykonywania danych nauki projekty](project-execution.md). 

>[AZURE.NOTE] Firma Microsoft przedstawiają kroki niezbędne do konfigurowania środowiska zespołu TDSP przy użyciu programu Visual Studio Team Services (VSTS) w poniższych instrukcjach. Określono sposobu wykonywania tych zadań z programu VSTS, ponieważ sposób wprowadzania TDSP firmy Microsoft. Użycie innej platformie hosting kod dla tej grupy zadań, które muszą zostać wykonane przez realizacji zespołu zwykle nie należy zmieniać. Jednak sposób do wykonania tych zadań ma być różne.


## <a name="repositories-and-directories"></a>Repozytoria i katalogów

W tym samouczku używana skróconej nazwy katalogów i repozytoriów. Te nazwy należy wykonać operacje między repozytoriów i katalogi. Ten element notation (**R** dla repozytoriów Git i **D** dla katalogów lokalnych w sieci DSVM) jest używany w następujących sekcjach:

- **R2**: GroupUtilities repozytorium na Git, który menedżera grupy został zainstalowany na serwerze programu VSTS grupy.
- **R4**: TeamUtilities repozytorium na Git, który skonfigurował realizacji Twojego zespołu.
- **R5**: repozytorium projektu na Git, który nie został skonfigurowany przez użytkownika realizacji projektu.
- **D2**: sklonowany katalogu lokalnego z R2.
- **D4**: sklonowany katalogu lokalnego z R4.
- **D5**: sklonowany katalogu lokalnego z R5.


## <a name="step-0-prerequisites"></a>Krok 0: wymagania wstępne

Wymagania wstępne są spełnione, wykonując przypisane do menedżera grupy opisane w temacie zadania [grupy Menedżera zadań dla zespołu nauki danych](group-manager-tasks.md). Podsumowując, w tym miejscu, następujące wymagania muszą zostać spełnione przed rozpoczęciem realizacji zadań zespołu: 
- Skonfigurował menedżera grupy **GroupUtilities** repozytorium (jeśli istnieje). 
- Skonfigurował realizacji Twojego zespołu **TeamUtilities** repozytorium (jeśli istnieje).
- Repozytorium projektu nie został skonfigurowany z realizacji projektu. 
- Możesz dodano do repozytorium projektu przez użytkownika realizacji projektu z uprawnieniami w klonowania z i Wypchnij wstecz do repozytorium projektu.

Druga Strona, **TeamUtilities** repozytorium wymagań wstępnych jest opcjonalna, w zależności od tego, czy zespół ma repozytorium narzędzia specyficzne dla zespołu. Jeśli żadnego z trzech wstępne nie została ukończona, skontaktuj się z Twojego zespołu kierownika, Twoje realizacji projektu lub ich delegatów, aby skonfigurować zgodnie z instrukcjami dla [prowadzić zespołu zadania dla zespołu nauki danych](team-lead-tasks.md) lub [ Realizacji zadania dla zespołu nauki danych w projekcie](project-lead-tasks.md).

- Git musi być zainstalowany na tym komputerze. Jeśli używasz maszyny wirtualnej nauki danych (DSVM) Git został wstępnie zainstalowany i jest gotowe. W przeciwnym razie zobacz [dodatku platform i narzędzi](platforms-and-tools.md#appendix).  
- Jeśli używasz **Windows DSVM**, musisz mieć [Git Menedżera poświadczeń (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) zainstalowana na tym komputerze. W pliku README.md, przewiń w dół do **Pobierz i zainstaluj** sekcji, a następnie kliknij przycisk *najnowszą wersję Instalatora*. Powoduje to przejście do najnowszej strony Instalatora. Pobierz Instalatora .exe w tym miejscu i uruchom go. 
- Jeśli używasz **Linux DSVM**, Utwórz klucz publiczny SSH na Twojej DSVM i dodaj go do serwera programu VSTS grupy. Aby uzyskać więcej informacji na temat SSH, zobacz **tworzenie publicznego klucza SSH** sekcji [dodatku platform i narzędzi](platforms-and-tools.md#appendix). 
- Jeśli potencjalnych klientów zespołu i/lub projektu utworzył niektóre usługi Azure file storage, który należy do zainstalowania programu DSVM, należy pobrać informacji o magazyn plików Azure z nich. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Krok 1: 3: klonowania grupy, zespołu i repozytoria projektu na komputerze lokalnym

W tej sekcji przedstawiono instrukcje dotyczące wykonywania zadań pierwsze trzy poszczególnych uczestników projektu: 

- Klonowanie **GroupUtilities** repozytorium R2 do D2
- Klonowanie **TeamUtilities** repozytorium R4 do D4 
- Klonowanie **projektu** repozytorium R5 do D5.

Na komputerze lokalnym, należy utworzyć katalog ***C:\GitRepos*** (dla systemu Windows) lub ***$home/GitRepos*** (forLinux), a następnie przejdź do tego katalogu. 

Uruchom jednego z następujących poleceń (na potrzeby system operacyjny) na potrzeby klonowania sieci **GroupUtilities**, **TeamUtilities**, i **projektu** repozytoria do katalogów na użytkownika komputer lokalny: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Upewnij się, zobacz trzy foldery w katalogu projektu.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Upewnij się, zobacz trzy foldery w katalogu projektu.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Krok 4-5: magazyn plików Azure instalacji do Twojej DSVM (opcjonalnie)

Aby Magazyn plików Azure instalacji do Twojej DSVM, zapoznaj się z instrukcjami w sekcji 4 [zespołu realizacji zadań dla zespołu nauki danych](team-lead-tasks.md)

## <a name="next-steps"></a>Kolejne kroki

Oto łącza do bardziej szczegółowe opisy ról i zadań zdefiniowanych przez proces nauki danych zespołu:

- [Zadania menedżera grupy dla zespołu nauki danych](group-manager-tasks.md)
- [Zespół realizacji zadania dla zespołu nauki danych](team-lead-tasks.md)
- [Zadania realizacji projektu dla zespołu nauki danych](project-lead-tasks.md)
- [Współautorzy poszczególnych projektu dla zespołu nauki danych](project-ic-tasks.md)

