---
title: Informacje o laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak devtest labs może ułatwić tworzenie maszyn wirtualnych platformy Azure, zarządzanie nimi i monitorowanie ich
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561459"
---
# <a name="about-azure-devtest-labs"></a>Informacje o usłudze Azure DevTest Labs
Laboratorium usługi Azure DevTest labs umożliwia deweloperom w zespołach efektywne samodzielne zarządzanie maszynami wirtualnymi (VM) i zasobami PaaS bez oczekiwania na zatwierdzenia.

DevTest Labs tworzy laboratoria składające się ze wstępnie skonfigurowanych baz lub szablonów usługi Azure Resource Manager. Mają one wszystkie niezbędne narzędzia i oprogramowanie, których można użyć do tworzenia środowisk. Środowiska można tworzyć w ciągu kilku minut, w przeciwieństwie do godzin lub dni.

Za pomocą DevTest Labs, można przetestować najnowsze wersje aplikacji, wykonując następujące zadania:

- Szybkie udostępnianie środowiska systemu Windows i Linux przy użyciu szablonów i artefaktów wielokrotnego użycia.
- Łatwo zintegruj swój potok wdrażania za pomocą usługi DevTest Labs, aby aprowizować środowiska na żądanie.
- Skalowanie w górę testowania obciążenia przez inicjowanie obsługi administracyjnej wielu agentów testowych i tworzenie wstępnie aprowizowanych środowisk dla szkoleń i wersji demonstracyjnych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Możliwości
DevTest Labs udostępnia następujące możliwości deweloperom pracującym z maszynami wirtualnymi:

- Szybko twórz maszyny wirtualne, wykonując mniej niż pięć prostych kroków.
- Wybierz z wyselekcjonowanej listy baz maszyn wirtualnych, które są skonfigurowane, zatwierdzone i autoryzowane przez kierownika zespołu lub centralną firmę IT.
- Twórz maszyny wirtualne z wstępnie utworzonych obrazów niestandardowych, które mają zainstalowane wszystkie oprogramowanie i narzędzia. 
- Tworzenie maszyn wirtualnych z formuł, które są zasadniczo niestandardowe obrazy w połączeniu z najnowszymi kompilacjami oprogramowania, które jest instalowane podczas tworzenia maszyn wirtualnych. 
- Zainstaluj artefakty, które są rozszerzeniami wdrożonymi na maszynach wirtualnych po ich zainicjowaniu obsługi administracyjnej.
- Ustaw harmonogramy automatycznego zamykania i automatycznego uruchamiania na maszynach wirtualnych.
- Odbierz wstępnie utworzoną maszynę wirtualną bez przechodzenia przez proces tworzenia.

DevTest Labs oferuje następujące możliwości deweloperom pracującym ze środowiskami PaaS:

- Za pomocą Menedżera zasobów można szybko tworzyć środowiska PaaS, wykonując mniej niż trzy proste kroki.
- Wybierz z wyselekcjonowanej listy szablonów Menedżera zasobów, które są skonfigurowane i autoryzowane przez kierownika zespołu lub centralną firmę IT.
- Rozdziel pustą grupę zasobów (piaskownicy) przy użyciu szablonu Usługi Resource Manager do eksplorowania platformy Azure w kontekście laboratorium.

DevTest Labs umożliwia również centralne dział it do kontroli odpadów, optymalizacji kosztów zasobów i pobyt w budżetach, wykonując następujące zadania:  

- Ustawianie harmonogramów automatycznego zamykania i automatycznego uruchamiania na maszynach wirtualnych.
- Ustawianie zasad dotyczących liczby maszyn wirtualnych, które użytkownicy mogą tworzyć.
- Ustawianie zasad dotyczących rozmiarów maszyn wirtualnych i obrazów galerii, które użytkownicy wybierają.
- Śledzenie kosztów i wyznaczanie celów w laboratoriach.
- Uzyskiwanie powiadomień o wysokich przewidywanych kosztach dla laboratoriów, dzięki czemu można podjąć niezbędne działania.

DevTest Labs zapewnia następujące korzyści w tworzeniu, konfigurowaniu i zarządzaniu środowiskami w chmurze.

## <a name="cost-control-and-governance"></a>Kontrola kosztów i zarządzanie
DevTest Labs ułatwia kontrolowanie kosztów, umożliwiając wykonywanie następujących zadań:

- [Ustaw zasady w laboratoriach,](devtest-lab-get-started-with-lab-policies.md)takie jak liczba maszyn wirtualnych na użytkownika lub na laboratorium. 
- Tworzenie [zasad, aby automatycznie zamykać](devtest-lab-set-lab-policy.md) i uruchamiać maszyny wirtualne.
- Śledzenie kosztów na maszynach wirtualnych i zasobach PaaS rozkręca się w laboratoriach, aby utrzymać się w [budżecie.](devtest-lab-configure-cost-management.md)
- Pozostań w kontekście swoich laboratoriów, aby nie rozkręcać zasobów poza nimi.

## <a name="quickly-get-to-ready-to-test"></a>Szybkie przechodzenie do gotowych do testów
DevTest Labs umożliwia tworzenie wstępnie aprowizowanych środowisk wyposażonych we wszystko, czego zespół potrzebuje do tworzenia i testowania aplikacji. Po prostu [zdaj sobie prawa do środowisk,](devtest-lab-add-claimable-vm.md) w których zainstalowana jest ostatnia dobra kompilacja aplikacji i rozpocznij pracę. Możesz też używać kontenerów do jeszcze szybszego i szczuplejszego tworzenia środowiska.

## <a name="create-once-use-everywhere"></a>Utwórz raz, używaj wszędzie
Przechwytywanie i udostępnianie [szablonów środowiska](devtest-lab-create-environment-from-arm.md) PaaS i [artefaktów](add-artifact-repository.md) w zespole lub organizacji — wszystkie w kontroli źródła — w celu łatwego tworzenia środowisk deweloperskich i testowych.

## <a name="worry-free-self-service"></a>Bezproblemowa samoobsługa
Laboratorium DevTest umożliwia deweloperom i testerom szybkie i łatwe tworzenie zasobów [maszyn wirtualnych IaaS](devtest-lab-add-vm.md) i [PaaS](devtest-lab-create-environment-from-arm.md) przy użyciu zestawu wstępnie skonfigurowanych zasobów.

## <a name="use-iaas-and-paas-resources"></a>Korzystanie z zasobów IaaS i PaaS 
Deweloperzy mogą również uruchamiać zasoby PaaS, takie jak klastry sieci szkieletowej usług Azure, funkcja aplikacji sieci Web usługi Azure App Service i farmy programu SharePoint przy użyciu szablonów Menedżera zasobów. Aby rozpocząć pracę na PaaS w laboratoriach, użyj szablonów z [repozytorium środowiska publicznego](devtest-lab-configure-use-public-environments.md) lub [połącz laboratorium z własnym repozytorium Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Możesz również śledzić koszty tych zasobów, aby utrzymać się w budżecie.

## <a name="integrate-with-your-existing-toolchain"></a>Integracja z istniejącym pęskiem narzędzi
Użyj wstępnie wyprodukowanych wtyczek lub interfejsu API do aprowizowania środowisk deweloperskich/testowych bezpośrednio z [preferowanego narzędzia ciągłej integracji (CI),](devtest-lab-integrate-ci-cd-vsts.md)zintegrowanego środowiska programistycznego (IDE) lub potoku automatycznego uwalniania. Można również użyć kompleksowego narzędzia wiersza polecenia.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- Aby dowiedzieć się więcej o laboratoriach DevTest, zobacz [Pojęcia devtest labs](devtest-lab-concepts.md).
- Aby uzyskać instruktaż z instrukcjami krok po kroku, zobacz [Samouczek: Konfigurowanie laboratorium przy użyciu usługi Azure DevTest Labs](tutorial-create-custom-lab.md).