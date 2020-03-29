---
title: Popularne scenariusze korzystania z laboratoriów DevTest Azure
description: Ten artykuł zawiera podstawowe scenariusze korzystania z usługi Azure DevTest Labs i dwie ogólne ścieżki, aby rozpocząć korzystanie z usługi w organizacji.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773812"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Popularne scenariusze korzystania z laboratoriów DevTest Azure
W zależności od potrzeb przedsiębiorstwa devtest labs można skonfigurować tak, aby spełniała różne wymagania.  W tym artykule omówiono popularne scenariusze. Każdy scenariusz obejmuje korzyści wniesione przy użyciu DevTest Labs i zasobów do zaimplementowania tych scenariuszy.  

- Komputery dla deweloperów
- Środowiska testowe
- Sesje szkoleniowe, praktyczne laboratoria i hackathony
- Dochodzenia w trybie piaskownicy
- Laboratorium na potrzeby zajęć

## <a name="developer-desktops"></a>Komputery dla deweloperów
Deweloperzy często mają różne wymagania dotyczące maszyn deweloperskich dla różnych projektów. W laboratoriach DevTest Labs deweloperzy mogą mieć dostęp do maszyn wirtualnych na żądanie, które są skonfigurowane tak, aby odpowiadały ich najbardziej typowym scenariuszom. DevTest Labs zapewnia następujące korzyści:

- Organizacje mogą zapewnić wspólne maszyny programistyczne zapewniające spójność między zespołami.
- Deweloperzy mogą szybko aprowizować swoje maszyny programistyczne na żądanie lub [ubiegać się o istniejącą wstępnie skonfigurowaną maszynę.](devtest-lab-add-claimable-vm.md)
- Deweloperzy mogą aprowizować zasoby w sposób samoobsługowy bez konieczności uprawnień na poziomie subskrypcji.
- It lub administratorzy mogą [wstępnie zdefiniować topologię sieci,](devtest-lab-configure-vnet.md) a deweloperzy mogą bezpośrednio z niej korzystać w prosty i intuicyjny sposób bez konieczności specjalnego dostępu.
- Deweloperzy mogą łatwo [dostosować](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) swoje maszyny programistyczne w razie potrzeby.
- Administratorzy mogą kontrolować koszty, zapewniając, że:
    - Deweloperzy [nie mogą uzyskać więcej maszyn wirtualnych](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) niż potrzebują do tworzenia
    - [Maszyny wirtualne są zamykane,](devtest-lab-set-lab-policy.md#set-auto-shutdown) gdy nie są używane
    - [Zezwalanie tylko na podzbiór rozmiarów wystąpień maszyny Wirtualnej](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) dla określonych laboratoriów
    - [Zarządzanie kosztami docelowymi i powiadomieniami](devtest-lab-configure-cost-management.md) dla każdego laboratorium.

Aby uzyskać więcej informacji, zobacz [Korzystanie z laboratoriów Azure DevTest dla deweloperów.](devtest-lab-developer-lab.md) 

## <a name="test-environments"></a>Środowiska testowe
Tworzenie środowisk testowych i zarządzanie nimi w przedsiębiorstwie może wymagać znacznego wysiłku. W laboratoriach DevTest Labs środowiska testowe można łatwo tworzyć, aktualizować lub duplikować. Umożliwia zespołom dostęp do w pełni skonfigurowanego środowiska, gdy jest to potrzebne. W tym scenariuszu DevTest Labs zapewnia następujące korzyści:

- Organizacje mogą zapewnić typowe środowiska testowe zapewniające spójność między zespołami.
- Testerzy mogą testować najnowszą wersję swojej aplikacji, szybko inicjując aprowizowanie środowisk systemu Windows i Linux przy użyciu szablonów wielokrotnego użycia.
- Administratorzy mogą połączyć laboratorium z platformą Azure DevOps, aby włączyć scenariusze DevOps
- Właściciele laboratoriów mogą kontrolować koszty, zapewniając, że:
    - [Maszyny wirtualne w środowiskach są zamykane,](devtest-lab-set-lab-policy.md#set-auto-shutdown) gdy nie są używane
    - [Zezwalanie tylko na podzbiór rozmiarów wystąpień maszyny Wirtualnej dla](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) określonych laboratoriów
    - [Zarządzanie kosztami docelowymi i powiadomieniami](devtest-lab-configure-cost-management.md) dla każdego laboratorium.

Aby uzyskać więcej informacji, zobacz [Korzystanie z laboratoriów azure devtest dla środowisk testowych maszyn wirtualnych i paaS.](devtest-lab-test-env.md)

## <a name="sandboxed-investigations"></a>Dochodzenia w trybie piaskownicy
Deweloperzy często badają różne technologie lub projektowanie infrastruktury. Domyślnie wszystkie środowiska utworzone za pomocą DevTest Labs są tworzone we własnej grupie zasobów. Użytkownik DevTest Labs pobiera tylko dostęp do odczytu do tych zasobów. Jednak dla deweloperów, którzy potrzebują większej kontroli, ustawienie w całym laboratorium można zaktualizować, aby dać [prawa współautora](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) do źródłowego devtest labs użytkownika dla każdego środowiska, które tworzą.  W laboratoriach DevTest Labs deweloperzy mogą automatycznie przyznać uprawnienia współautora do środowisk, które tworzą w laboratorium.  W tym scenariuszu deweloperzy mogą dodawać i/lub zmieniać zasoby platformy Azure, gdy są potrzebne do tworzenia lub testowania środowisk. Strona [koszt po zasobach](devtest-lab-configure-cost-management.md#view-cost-by-resource) umożliwia właścicielom laboratoriów śledzenie kosztów każdego środowiska używanego do badania.

Aby uzyskać więcej informacji, zobacz [Ustawianie praw dostępu do grupy zasobów środowiska](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Szkolenia, praktyczne laboratoria i hackathony 
Laboratorium w usłudze Azure DevTest Labs działa jako doskonały kontener dla działań przejściowych, takich jak warsztaty, praktyczne laboratoria, szkolenia lub hackathons.  Usługa umożliwia utworzenie laboratorium, w którym można podać niestandardowe szablony, których każdy stażysta może używać do tworzenia identycznych i odizolowanych środowisk do szkolenia. W tym scenariuszu DevTest Labs zapewnia następujące korzyści:

- [Zasady](devtest-lab-set-lab-policy.md) zapewniają, że stażyści otrzymują tylko liczbę zasobów, takich jak maszyny wirtualne, których potrzebują.
- Wstępnie skonfigurowane i utworzone maszyny są [zgłaszane](devtest-lab-add-claimable-vm.md) z pojedynczą akcją od stażysty.
- Laboratoria są udostępniane stażystom, uzyskując dostęp do [adresu URL laboratorium.](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)
- [Daty wygaśnięcia](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) na maszynach wirtualnych zapewniają, że maszyny są usuwane po ich nie są już potrzebne.
- Po zakończeniu szkolenia można łatwo [usunąć laboratorium](devtest-lab-delete-lab-vm.md#delete-a-lab) i wszystkie [powiązane zasoby.](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)

Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Azure DevTest Labs do szkolenia.](devtest-lab-training-lab.md)  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Dowód koncepcji a skalowane wdrożenie
Gdy zdecydujesz się eksplorować DevTest Labs, istnieją dwie ogólne ścieżki do przodu: Proof of Concept vs Scaled Deployment.  

**Skalowane wdrożenie** składa się z tygodni/miesięcy przeglądania i planowania z zamiarem wdrożenia DevTest Labs w całym przedsiębiorstwie, które ma setki lub tysiące deweloperów.

Wdrożenie **weryfikacji koncepcji** koncentruje się na skoncentrowanym wysiłku jednego zespołu w celu ustalenia wartości organizacyjnej. Chociaż może to być kuszące, aby myśleć o skalowane wdrożenie, podejście ma tendencję do niepowodzenia częściej niż dowód koncepcji opcji. W związku z tym zaleca się rozpoczęcie małych, uczyć się od pierwszego zespołu, powtórzyć to samo podejście z dwóch do trzech dodatkowych zespołów, a następnie zaplanować skalowanie wdrożenia na podstawie zdobytej wiedzy. Aby uzyskać pomyślny dowód koncepcji, zaleca się wybranie jednego lub dwóch zespołów i zidentyfikowanie ich scenariuszy (środowisko deweloperskie vs środowiska testowe), udokumentowanie ich bieżących przypadków użycia i wdrożenie laboratoriów DevTest.

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [DevTest Labs — pojęcia](devtest-lab-concepts.md)
- [DevTest Labs – często zadawane pytania](devtest-lab-faq.md)

