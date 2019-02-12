---
title: Rozpoczynanie pracy z usługą za pomocą usługi Azure DevTest Labs
description: Ten artykuł zawiera podstawowe scnearios przy użyciu usługi Azure DevTest Labs i dwie ścieżki głównej, aby rozpocząć korzystanie z usługi w Twojej organizacji.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: christianreddington
manager: femila
editor: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: chredd
ms.reviewer: anthdela,juselph
ms.openlocfilehash: dfb73778a9d30ba50e69bff6414381235ad8b1d1
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005237"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Rozpoczynanie pracy z usługą za pomocą usługi Azure DevTest Labs
Po podjęciu decyzji zapoznać się z usługi DevTest Labs, istnieją dwie ścieżki ogólnego do przodu — vs weryfikacji koncepcji wdrożenia skalowany. 

A **skalowania wdrożenia** składa się z tygodni/miesięcy, przeglądanie i planowania z celem wdrażania w usłudze DevTest Labs w całym przedsiębiorstwie, który ma setki lub tysiące deweloperów. 

A **weryfikacji koncepcji wdrożenia** koncentruje się na najwięcej starań jeden zespół w celu ustanowienia wartość organizacji. Mogą być kuszące myśleć skalowanego wdrożenia, to podejście jest zwykle częściej niż weryfikacji koncepcji opcji nie powiedzie się. Dlatego zaleca się, możesz zacząć od małej, ucz się pierwszym zespołem, powtórz tej samej metody, przy użyciu dwóch lub trzech dodatkowych zespołów, a następnie Zaplanuj wdrożenia skalowanego oparty na wiedzy uzyskanej. Pomyślnie weryfikację koncepcji zaleca się, czy możesz wybrać jednego lub dwóch zespołów i określenie ich scenariuszy (vs Środowisko deweloperskie środowisk testowych), dokumentów ich bieżącego przypadki użycia i wdrażanie DevTest Labs. 

## <a name="scenarios"></a>Scenariusze
Istnieją trzy podstawowe scenariusze dla implementacji usługi DevTest Labs: 

- Komputery stacjonarne dla deweloperów
- Środowiska testowe
- Laboratoria/szkolenia/maraton programistyczny

## <a name="developer-desktops"></a>Komputery stacjonarne dla deweloperów
Deweloperzy często mają różne wymagania dotyczące komputerach deweloperskich dla różnych projektów. Za pomocą usługi DevTest Labs deweloperzy mają dostęp do maszyn wirtualnych na żądanie, które są wstępnie skonfigurowane do potrzeb ich najbardziej typowych scenariuszy. DevTest Labs zapewnia następujące korzyści:

- Organizacje mogą przydzielać wspólnego środowisk programowania i testowania zapewnienie spójności między zespołami
- Deweloperzy mogą szybko inicjować obsługę swoich komputerach deweloperskich na żądanie
- Deweloperzy mogą aprowizować zasoby w taki sposób, Samoobsługowe bez uprawnienia na poziomie subskrypcji
- IT lub Administratorzy wstępnie mogą definiować topologię sieci i deweloperom bezpośrednio jej wykorzystania w sposób prosty i intuicyjny bez żadnych szczególnych dostępu do
- Deweloperzy będą mogli łatwo dostosować swoich komputerach deweloperskich zgodnie z potrzebami
- Administratorzy mogą kontrolować koszty za zapewnienie, że:
    - Deweloperzy nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane do tworzenia aplikacji
    - Maszyny wirtualne zostaną wyłączone w dół, gdy nieużywany
    - Pozwalając tylko podzbiór rozmiarów wystąpień maszyn wirtualnych dla konkretnych obciążeń

## <a name="test-environments"></a>Środowiska testowe
Tworzenie i zarządzanie nimi środowisk testowych w całym przedsiębiorstwie, może wymagać znaczących nakładów pracy. Z usługą DevTest Labs środowiska testowe mogą być łatwo utworzone, zaktualizowane lub zduplikować, co pozwala zespołom dostęp do w pełni skonfigurowane środowisko, gdy jest to konieczne. W tym scenariuszu laboratorium ma następujące zalety:

- Testerzy można przetestować najnowszą wersję aplikacji, szybko aprowizując środowiska Windows i Linux przy użyciu szablonów i artefaktów.
- Testerzy można zwiększać ich testy obciążeniowe, aprowizując wielu agentów testowych
- Administratorzy mogą laboratorium łączyć się z DevOps platformy Azure, która umożliwia realizację scenariuszy DevOps
- Administratorzy mogą kontrolować koszty za zapewnienie, że:
    - Testerzy nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane
    - Maszyny wirtualne zostaną wyłączone w dół, gdy nieużywany
    - Pozwalając tylko podzbiór rozmiarów wystąpień maszyn wirtualnych dla konkretnych obciążeń

## <a name="labs-workshops-trainings-and-hackathons"></a>Laboratoria, warsztaty, szkolenia i hackathonów  
Laboratorium Azure DevTest Labs działa jako kontener doskonałe przejściowy działań, takich jak warsztatów, warsztaty, szkolenia i hackathonów. Usługa umożliwia tworzenie laboratorium, w którym mogą udostępniać szablony niestandardowe, które każdy szkoleniowych można użyć do utworzenia identyczne i wyizolowanego środowiska do trenowania. Można zastosować zasad w celu zapewnienia środowiska szkoleń są dostępne dla każdego szkoleniowych, tylko wtedy, gdy są potrzebne i zawiera za mało zasobów — takich jak virtual machines — wymagana na szkolenia. Na koniec laboratorium mogą łatwo udostępniać za pomocą Zaleć, które mogą oni uzyskać dostęp za pomocą jednego kliknięcia. Klasa został zakończony, po łatwo usunąć laboratorium i wszystkie pokrewne zasoby.


## <a name="next-steps"></a>Kolejne kroki
Zobacz następny artykuł w tej serii: [Skalowanie w górę wdrożenia usługi DevTest Labs](devtest-lab-guidance-scale.md)
