---
title: O usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa DevTest Labs może znacznie ułatwiają tworzenie, zarządzanie i monitorowanie maszyn wirtualnych platformy Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: e07149865d2dda52e33003964c2852a8aaccf76f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493199"
---
# <a name="about-azure-devtest-labs"></a>O usłudze Azure DevTest Labs
Usługa Azure DevTest Labs umożliwia deweloperom w zespołach efektywnie samodzielnie zarządzać maszyn wirtualnych (VM) i zasobów PaaS bez konieczności oczekiwania na zatwierdzenia.

DevTest Labs tworzy labs składające się z wstępnie skonfigurowanych zasad lub szablonów usługi Azure Resource Manager. Mają one wszystkie niezbędne narzędzia i oprogramowanie, które służy do tworzenia środowisk. W ciągu kilku minut, a nie godzin lub dni, może utworzyć środowiska.

Za pomocą usługi DevTest Labs, można sprawdzić najnowsze wersje aplikacji, wykonując następujące czynności:

- Szybko inicjować obsługę środowisk Windows i Linux przy użyciu szablonów i artefaktów.
- Łatwo Zintegruj swój potok wdrażania za pomocą usługi DevTest Labs, aby aprowizować środowiska na żądanie.
- Skalowanie w górę testy obciążeniowe, aprowizując wielu agentów testowych i tworząc wstępnie aprowizowane środowiska do szkolenia i pokazów.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Możliwości
DevTest Labs oferuje następujące funkcje dla deweloperów, pracy z maszynami wirtualnymi:

- Szybkie tworzenie maszyn wirtualnych, postępując zgodnie z mniej niż pięć prostych kroków.
- Wybierz z listy wyselekcjonowanych bazami maszyn wirtualnych, które są skonfigurowane, zatwierdzone i autoryzowane przez lider zespołu lub centralnej IT.
- Tworzenie maszyn wirtualnych ze wstępnie utworzonych obrazów niestandardowych, zawierających całe oprogramowanie i narzędzia są już zainstalowane. 
- Tworzenie maszyn wirtualnych z obrazów niestandardowych w połączeniu z najnowszych kompilacji oprogramowanie zainstalowane podczas tworzenia maszyn wirtualnych.
- Instalowanie artefaktów, które są wdrożone na maszynach wirtualnych one przeprowadzone rozszerzenia.
- Skonfigurować automatyczne zamykanie i auto-start harmonogramy na maszynach wirtualnych.
- Oświadczenie wstępnie utworzonych maszyn wirtualnych bez przechodzenia przez proces tworzenia.

DevTest Labs oferuje następujące funkcje dla deweloperów, Praca ze środowiskami PaaS:

- Usługa Resource Manager, aby szybko tworzyć środowiska PaaS, postępując zgodnie z mniej niż trzy proste kroki.
- Wybierz z wyselekcjonowaną listę szablonów usługi Resource Manager, które są skonfigurowane i autoryzowane przez lider zespołu lub centralnej IT.
- Przyspiesz pustą grupę zasobów (piaskownicy) przy użyciu szablonu usługi Resource Manager do eksplorowania platformy Azure w ramach laboratorium.

DevTest Labs umożliwia także centralne IT, aby kontrolować odpady, optymalizować koszty zasobów i pozostają w ramach budżetu, wykonując następujące czynności: 

- Ustawianie harmonogramów automatycznego zamykania i automatycznego uruchamiania na maszynach wirtualnych.
- Ustawianie zasad na liczbę maszyn wirtualnych, które użytkownicy mogą tworzyć.
- Ustawianie zasad, rozmiary maszyn wirtualnych i obrazy z galerii, do wyboru dla użytkowników.
- Śledzenie kosztów i ustawianie elementów docelowych dla labs.
- Otrzymywanie powiadomień wysokie koszty planowane w laboratoriach, aby można było podjąć odpowiednie działania.

DevTest Labs oferuje następujące korzyści podczas tworzenia, konfigurowania i zarządzania środowiskami w chmurze.

## <a name="control-costs-and-governance"></a>Kontrolowanie kosztów i nadzór
DevTest Labs ułatwia kontrolowanie kosztów, co umożliwia wykonywanie następujących zadań:

- Ustawianie zasad na laboratorium, takie jak liczba maszyn wirtualnych na użytkownika lub laboratorium. 
- Utwórz zasady, aby automatycznie zamykać i uruchamiać maszyny wirtualne.
- Śledź koszty zasobów maszyn wirtualnych i PaaS przetworzyliśmy wewnątrz laboratoria, aby nie przekraczając budżetu.
- Zachowaj w kontekście laboratoriów, więc nie możesz korzystać z zasobów poza ich.

## <a name="quickly-get-to-ready-to-test"></a>Szybki dostęp do gotowych do testowania
DevTest Labs umożliwia utworzenie wstępnie zainicjowana obsługa środowisk, które zawierają wszystko, czego potrzebuje Twój zespół do tworzenia i testowania aplikacji. Po prostu oświadczenia środowisk zainstalowaną ostatnią dobrą kompilacją aplikacji i rozpocząć pracę. Możesz też użyć kontenerów do tworzenia jeszcze szybciej, zorganizowane środowisko.

## <a name="create-once-use-everywhere"></a>Utwórz raz, używaj wszędzie
Przechwytuj i udostępniaj szablony środowisk PaaS i artefakty w ramach zespołu lub organizacji — wszystko to w kontroli źródła — aby łatwo tworzyć dla deweloperów i środowisk testowych.

## <a name="save-time-on-setup"></a>Oszczędzaj czas na temat instalacji  
Maszyny wirtualne IaaS i PaaS zasobów można łatwo utworzyć przy użyciu zestawu wstępnie skonfigurowanych zasobów.

## <a name="use-iaas-and-paas-resources"></a>Użyj zasobów IaaS i PaaS 
Deweloperzy mogą również paru zasobów PaaS, takich jak klastry usługi Azure Service Fabric funkcji Web Apps w usłudze Azure App Service i farmy programu SharePoint przy użyciu szablonów usługi Resource Manager. Aby rozpocząć pracę na PaaS w laboratorium, korzystanie z szablonów z repozytorium publicznego środowiska, lub Połącz laboratorium z repozytorium Git. Można także śledzić koszty na te zasoby, aby nie przekraczając budżetu.

## <a name="integrate-with-your-existing-toolchain"></a>Integracja z istniejącym łańcuchem narzędzi
Wstępnie przygotowane wtyczki lub interfejsu API, aby udostępniać projektowania i testowania bezpośrednio z narzędzia do preferowanego ciągłej integracji (CI), zintegrowane środowisko projektowe (IDE) lub zautomatyzowanego potoku wersji. Można również użyć kompleksowe narzędzie wiersza polecenia.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- Aby dowiedzieć się więcej o usłudze DevTest Labs, zobacz [DevTest Labs — pojęcia](devtest-lab-concepts.md).
- Aby uzyskać przewodnik z instrukcjami krok po kroku, zobacz [samouczka: Konfigurowanie laboratorium przy użyciu usługi Azure DevTest Labs](tutorial-create-custom-lab.md).


