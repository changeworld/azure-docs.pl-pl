---
title: Rozproszone wspólne opracowywanie zasobów usługi Azure DevTest Labs
description: Zawiera najlepsze rozwiązania dotyczące konfigurowania rozproszonego i wspólnego środowiska programistycznego w celu opracowania zasobów DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170119"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Najważniejsze wskazówki dotyczące rozproszonego i opartego na współpracy tworzenia zasobów platformy Azure DevTest Labs
Rozproszone wspólne tworzenie umożliwia różnym zespołom lub osobom tworzenie i utrzymywanie bazy kodu. Aby odnieść sukces, proces rozwoju zależy od możliwości tworzenia, udostępniania i integrowania informacji. Ta zasada tworzenia kluczy może służyć w ramach usługi Azure DevTest Labs. Istnieje kilka typów zasobów w laboratorium, które są powszechnie dystrybuowane między różnymi laboratoriami w przedsiębiorstwie. Różne typy zasobów są skoncentrowane na dwóch obszarach:

- Zasoby, które są wewnętrznie przechowywane w laboratorium (oparte na laboratorium)
- Zasoby, które są przechowywane w [zewnętrznych repozytoriach, które są połączone z laboratorium](devtest-lab-add-artifact-repo.md) (oparte na repozytorium kodu). 

W tym dokumencie opisano niektóre najlepsze rozwiązania, które umożliwiają współpracę i dystrybucję między wieloma zespołami przy jednoczesnym zapewnieniu dostosowania i jakości na wszystkich poziomach.

## <a name="lab-based-resources"></a>Zasoby oparte na laboratorium

### <a name="custom-virtual-machine-images"></a>Niestandardowe obrazy maszyn wirtualnych
Możesz mieć wspólne źródło obrazów niestandardowych, które są wdrażane w laboratoriach w trybie nocnym. Aby uzyskać szczegółowe informacje, zobacz [Fabryka obrazów](image-factory-create.md).    

### <a name="formulas"></a>Formuły
[Formuły](devtest-lab-manage-formulas.md) są specyficzne dla laboratorium i nie mają mechanizmu dystrybucji. Członkowie laboratorium wykonują wszystkie opracowania formuł. 

## <a name="code-repository-based-resources"></a>Zasoby oparte na repozytorium kodu
Istnieją dwie różne funkcje, które są oparte na repozytoriach kodu, artefakty i środowiska. W tym artykule oprzemy się funkcje i jak najbardziej efektywnie skonfigurować repozytoria i przepływ pracy, aby umożliwić możliwość dostosowania dostępnych artefaktów i środowisk na poziomie organizacji lub zespołu.  Ten przepływ pracy jest oparty na standardowej [strategii rozgałęzienia kontroli kodu źródłowego](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Kluczowe pojęcia
Informacje źródłowe dla artefaktów obejmują metadane, skrypty. Informacje źródłowe dla środowisk obejmują metadane i szablony Menedżera zasobów z dowolnymi plikami pomocniczymi, takimi jak skrypty programu PowerShell, skrypty DSC, pliki Zip i tak dalej.  

### <a name="repository-structure"></a>Struktura repozytorium  
Najbardziej typową konfiguracją kontroli kodu źródłowego (SCC) jest skonfigurowanie wielowarstwowej struktury do przechowywania plików kodu (szablonów Menedżera zasobów, metadanych i skryptów), które są używane w laboratoriach. W szczególności należy utworzyć różne repozytoria do przechowywania zasobów, które są zarządzane przez różne poziomy działalności:   

- Zasoby dla całej firmy.
- Zasoby jednostki biznesowej/działu
- Zasoby specyficzne dla zespołu.

Każdy z tych poziomów łączy się z innym repozytorium, w którym gałąź główna musi być wysokiej jakości produkcji. [Gałęzie](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) w każdym repozytorium będzie do tworzenia tych określonych zasobów (artefakty lub szablony). Ta struktura jest dobrze zgodna z DevTest Labs, ponieważ można łatwo połączyć wiele repozytoriów i wiele gałęzi w tym samym czasie do laboratoriów organizacji. Nazwa repozytorium znajduje się w interfejsie użytkownika (UI), aby uniknąć nieporozumień, gdy istnieją identyczne nazwy, opis i wydawca.
     
Na poniższym diagramie przedstawiono dwa repozytoria: repozytorium firmy obsługiwane przez dział IT oraz repozytorium podziału obsługiwane przez dział R&D.

![Przykładowe środowisko dystrybucji i rozwoju współpracy](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Ta warstwowa struktura umożliwia rozwój przy zachowaniu wyższego poziomu jakości w gałęzi głównej, a wiele repozytoriów połączonych z laboratorium pozwala na większą elastyczność.

## <a name="next-steps"></a>Następne kroki    
Zobacz następujące artykuły:

- Dodawanie repozytorium do laboratorium przy użyciu [witryny Azure portal](devtest-lab-add-artifact-repo.md) lub [szablonu usługi Azure Resource Management](add-artifact-repository.md)
- [Artefakty Laboratoriów DevTest](devtest-lab-artifact-author.md)
- [Środowiska DevTest Labs](devtest-lab-create-environment-from-arm.md).
