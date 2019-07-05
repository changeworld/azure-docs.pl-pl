---
title: Rozproszone programowanie zespołowe zasobów usługi Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Przedstawiono najlepsze rozwiązania dotyczące konfigurowania środowiska deweloperskiego rozproszone i współpracy, do tworzenia zasobów usługi DevTest Labs.
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
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 8ffc8ed3f84284ff69e9515cba0982790b823a37
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543764"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Najlepsze rozwiązania dotyczące rozproszonego współpracy rozwoju i zasoby usługi Azure DevTest Labs
Programowanie zespołowe rozproszonych umożliwia różne zespoły lub inne osoby, rozwijania i utrzymywania podstawowy kod. Się powodzeniem, proces tworzenia aplikacji zależy od możliwości tworzenia, udostępniania i integrować informacje. Ta zasada tworzenia kluczy może służyć w usłudze Azure DevTest Labs. Istnieją różne typy zasobów w ramach laboratorium, które często są rozkładane między różnych labs w obrębie przedsiębiorstwa. Różnych typów zasobów koncentrują się na dwa obszary:

- Zasoby, które wewnętrznie są przechowywane w laboratorium (w oparciu o laboratorium)
- Zasoby, które są przechowywane w [zewnętrznych repozytoriów, które są podłączone do laboratorium](devtest-lab-add-artifact-repo.md) (na podstawie repozytorium kodu). 

W tym dokumencie opisano niektóre najlepsze rozwiązania, które umożliwiają współpracę i dystrybucję w wielu zespołach przy jednoczesnym zapewnieniu dostosowywania i jakość na wszystkich poziomach.

## <a name="lab-based-resources"></a>Zasoby oparte na laboratorium

### <a name="custom-virtual-machine-images"></a>Obrazy niestandardowych maszyn wirtualnych
Możesz mieć za wspólne źródło obrazów niestandardowych, które są wdrażane do laboratoriów w nocy. Aby uzyskać szczegółowe informacje, zobacz [fabrycznie obrazu](image-factory-create.md).    

### <a name="formulas"></a>Formuły
[Formuły](devtest-lab-manage-formulas.md) są specyficzne dla laboratorium i nie ma mechanizmu dystrybucji. Elementy członkowskie laboratorium do tworzenia formuł. 

## <a name="code-repository-based-resources"></a>Zasoby oparte na repozytorium kodu
Istnieją dwie różne funkcje, które zależą od repozytoriów kodu, artefakty i środowisk. W tym artykule pokazano, funkcje i jak najbardziej efektywny sposób skonfigurować repozytoriów i przepływ pracy umożliwia możliwość dostosowania dostępne artefaktów i środowisk na poziomie organizacji lub zespołu.  Ten przepływ pracy jest oparty na standardzie [strategii rozgałęziania Kontrola kodu źródłowego](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Kluczowe pojęcia
Źródła artefaktów informacje metadanych i skryptów. Informacje o źródle w środowiskach zawiera metadane i szablonów usługi Resource Manager za pomocą wszelkie pliki pomocnicze, takie jak skrypty programu PowerShell, DSC, skrypty, pliki Zip i tak dalej.  

### <a name="repository-structure"></a>Struktura repozytorium  
Najbardziej typowe konfiguracji kontroli kodu źródłowego (SCC) jest konfigurowanie struktury obejmujące wiele warstw, do przechowywania plików kodu (szablonów usługi Resource Manager, metadanych i skryptów), które są używane do w laboratorium. W szczególności utworzyć różnych repozytoriach, do przechowywania zasobów, które są zarządzane przez różne poziomy firmy:   

- Zasoby dla całej firmy.
- Business jednostkę/dzielenia-zasobów
- Zasoby specyficzne dla zespołu.

Każda z tych poziomów połączyć się z innym repozytorium, gdzie gałąź główna musi być jakości produkcji. [Gałęzie](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) byłoby w każdym repozytorium do tworzenia tych określonych zasobów (artefaktów i szablonów). Ta struktura jest optymalnie wyrównana z usługi DevTest Labs, jak można łatwo połączyć wiele repozytoriów i wielu oddziałów, w tym samym czasie do laboratoriów w organizacji. Nazwa repozytorium są objęte interfejsu użytkownika (UI), aby uniknąć nieporozumień, gdy istnieją identyczne nazwy, opisu i wydawcy.
     
Na poniższym diagramie przedstawiono dwa repozytoriów: repozytorium firmy, które jest konserwowane przez dział IT i repozytorium dzielenia utrzymywane przez zespół R & D.

![Przykładowe środowiska deweloperskiego podziału i współpracy](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Ta struktura warstwowej umożliwia tworzenie rozwiązań przy jednoczesnym zachowaniu wyższy poziom jakości w gałęzi głównej, gdy masz wiele repozytoriów podłączony do laboratorium umożliwia większą elastyczność.

## <a name="next-steps"></a>Kolejne kroki    
Zobacz następujące artykuły:

- Dodawanie repozytorium do laboratorium przy użyciu [witryny Azure portal](devtest-lab-add-artifact-repo.md) lub za pośrednictwem [szablonu usługi Azure Resource Management](add-artifact-repository.md)
- [Artefakty DevTest Labs](devtest-lab-artifact-author.md)
- [Środowiska usługi DevTest Labs](devtest-lab-create-environment-from-arm.md).
