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
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561459"
---
# <a name="about-azure-devtest-labs"></a>O usłudze Azure DevTest Labs
Usługa Azure DevTest Labs umożliwia deweloperom w zespołach efektywnie samodzielnie zarządzać maszyn wirtualnych (VM) i zasobów PaaS bez konieczności oczekiwania na zatwierdzenia.

DevTest Labs tworzy labs składające się z wstępnie skonfigurowanych zasad lub szablonów usługi Azure Resource Manager. Mają one wszystkie niezbędne narzędzia i oprogramowanie, które służy do tworzenia środowisk. W ciągu kilku minut, a nie godzin lub dni, może utworzyć środowiska.

Za pomocą usługi DevTest Labs, można sprawdzić najnowsze wersje aplikacji, wykonując następujące czynności:

- Szybko inicjować obsługę środowisk Windows i Linux przy użyciu szablonów i artefaktów.
- Łatwo Zintegruj swój potok wdrażania za pomocą usługi DevTest Labs, aby aprowizować środowiska na żądanie.
- Skalowanie w górę testy obciążeniowe, aprowizując wielu agentów testowych i tworząc wstępnie aprowizowane środowiska do szkolenia i pokazów.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Możliwości
DevTest Labs oferuje następujące funkcje dla deweloperów, pracy z maszynami wirtualnymi:

- Szybkie tworzenie maszyn wirtualnych, postępując zgodnie z mniej niż pięć prostych kroków.
- Wybierz z listy wyselekcjonowanych bazami maszyn wirtualnych, które są skonfigurowane, zatwierdzone i autoryzowane przez lider zespołu lub centralnej IT.
- Tworzenie maszyn wirtualnych ze wstępnie utworzonych obrazów niestandardowych, zawierających całe oprogramowanie i narzędzia są już zainstalowane. 
- Tworzenie maszyn wirtualnych z formuły, które są zasadniczo niestandardowych obrazów, w połączeniu z najnowszych kompilacji oprogramowania, który jest instalowany, gdy maszyny wirtualne są tworzone. 
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

## <a name="cost-control-and-governance"></a>Kontrola kosztów i nadzór
DevTest Labs ułatwia kontrolowanie kosztów, co umożliwia wykonywanie następujących zadań:

- [Ustawianie zasad na laboratoriów](devtest-lab-get-started-with-lab-policies.md), takie jak liczba maszyn wirtualnych na użytkownika lub laboratorium. 
- Tworzenie [zasad w celu automatycznego wyłączania](devtest-lab-set-lab-policy.md) i uruchamiania maszyn wirtualnych.
- Śledź koszty zasobów maszyn wirtualnych i PaaS przetworzyliśmy wewnątrz labs w celu pozostania w [budżetu](devtest-lab-configure-cost-management.md).
- Zachowaj w kontekście laboratoriów, więc nie możesz korzystać z zasobów poza ich.

## <a name="quickly-get-to-ready-to-test"></a>Szybki dostęp do gotowych do testowania
DevTest Labs umożliwia utworzenie wstępnie zainicjowana obsługa środowisk, które zawierają wszystko, czego potrzebuje Twój zespół do tworzenia i testowania aplikacji. Po prostu [oświadczenia środowiska](devtest-lab-add-claimable-vm.md) zainstalowaną ostatnią dobrą kompilacją aplikacji i rozpoczęcie pracy. Możesz też użyć kontenerów do tworzenia jeszcze szybciej, zorganizowane środowisko.

## <a name="create-once-use-everywhere"></a>Utwórz raz, używaj wszędzie
Przechwytuj i udostępniaj PaaS [szablonów środowiska](devtest-lab-create-environment-from-arm.md) i [artefaktów](add-artifact-repository.md) w ramach zespołu lub organizacji — wszystko to w kontroli źródła — łatwe tworzenie dla deweloperów i środowisk testowych.

## <a name="worry-free-self-service"></a>Samoobsługa bez zmartwień
DevTest Labs umożliwia usługi deweloperów i testerów do szybkiego i łatwego [Tworzenie maszyn wirtualnych IaaS](devtest-lab-add-vm.md) i [zasobów PaaS](devtest-lab-create-environment-from-arm.md) przy użyciu zestawu wstępnie skonfigurowanych zasobów.

## <a name="use-iaas-and-paas-resources"></a>Użyj zasobów IaaS i PaaS 
Deweloperzy mogą również paru zasobów PaaS, takich jak klastry usługi Azure Service Fabric funkcji Web Apps w usłudze Azure App Service i farmy programu SharePoint przy użyciu szablonów usługi Resource Manager. Aby rozpocząć usłudze PaaS w laboratorium, należy użyć szablonów z [repozytorium publicznego środowiska](devtest-lab-configure-use-public-environments.md) lub [laboratorium nawiązać połączenie z repozytorium Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Można także śledzić koszty na te zasoby, aby nie przekraczając budżetu.

## <a name="integrate-with-your-existing-toolchain"></a>Integracja z istniejącym łańcuchem narzędzi
Użyj wstępnie utworzonych wtyczki lub interfejsu API do aprowizacji środowisk projektowania i testowania bezpośrednio z preferowanego [narzędzia do ciągłej integracji (CI)](devtest-lab-integrate-ci-cd-vsts.md), zintegrowane środowisko projektowe (IDE) lub zautomatyzowanego potoku wersji. Można również użyć kompleksowe narzędzie wiersza polecenia.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- Aby dowiedzieć się więcej o usłudze DevTest Labs, zobacz [DevTest Labs — pojęcia](devtest-lab-concepts.md).
- Aby uzyskać przewodnik z instrukcjami krok po kroku, zobacz [samouczka: Konfigurowanie laboratorium przy użyciu usługi Azure DevTest Labs](tutorial-create-custom-lab.md).