---
title: Popularne scenariusze dotyczące korzystania z usługi Azure DevTest Labs
description: Ten artykuł zawiera podstawowe scenariusze przy użyciu usługi Azure DevTest Labs i dwie ścieżki głównej, aby rozpocząć korzystanie z usługi w Twojej organizacji.
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
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272390"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Popularne scenariusze dotyczące korzystania z usługi Azure DevTest Labs
W zależności od potrzeb organizacji aby spełnić różne wymagania można skonfigurować DevTest Labs.  W tym artykule omówiono popularne scenariusze. Każdy scenariusz obejmuje korzyści wprowadzone za pomocą usługi DevTest Labs i zasobów na potrzeby implementowania tych scenariuszy.  

- Komputery stacjonarne dla deweloperów
- Środowiska testowe
- Sesje szkoleniowe, praktyczne laboratoria oraz hackathonów
- Odpytywanie w trybie piaskownicy
- Laboratorium na potrzeby zajęć

## <a name="developer-desktops"></a>Komputery stacjonarne dla deweloperów
Deweloperzy często mają różne wymagania dotyczące komputerach deweloperskich dla różnych projektów. Za pomocą usługi DevTest Labs deweloperzy mają dostęp do maszyn wirtualnych na żądanie, które są skonfigurowane do potrzeb ich najbardziej typowych scenariuszy. DevTest Labs zapewnia następujące korzyści:

- Organizacje mogą przydzielać wspólnej komputerach deweloperskich, zapewnienie spójności między zespołami.
- Deweloperzy mogą szybko inicjować obsługę swoich komputerach deweloperskich na żądanie lub [oświadczenia istniejącej wstępnie skonfigurowane maszyny](devtest-lab-add-claimable-vm.md).
- Deweloperzy mogą aprowizować zasoby w taki sposób, Samoobsługowe bez uprawnienia na poziomie subskrypcji.
- IT lub Administratorzy mogą [Pre-define topologią sieci](devtest-lab-configure-vnet.md) i deweloperzy mogą bezpośrednio używać go w prosty i intuicyjny sposób bez konieczności specjalnego dostępu.
- Deweloperzy mogą łatwo [dostosować](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) ich rozwoju maszyn, zgodnie z potrzebami.
- Administratorzy mogą kontrolować koszty za zapewnienie, że:
    - Deweloperzy [nie można pobrać więcej maszyn wirtualnych](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) niż jest to wymagane do tworzenia aplikacji
    - [Maszyny wirtualne są zamykane](devtest-lab-set-lab-policy.md#set-auto-shutdown) kiedy nieużywany
    - Tylko [umożliwiając podzbiór rozmiarów wystąpień maszyn wirtualnych](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) do określonych laboratoriów
    - [Zarządzanie cele kosztów i powiadomienia](devtest-lab-configure-cost-management.md) w każdym środowisku laboratoryjnym.

Aby uzyskać dalsze informacje, zobacz [użycia usługi Azure DevTest Labs dla deweloperów](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Środowiska testowe
Tworzenie i zarządzanie nimi środowisk testowych w całym przedsiębiorstwie, może wymagać znaczących nakładów pracy. Za pomocą usługi DevTest Labs środowiska testowe mogą być łatwo utworzone, zaktualizowane lub zduplikowane. Umożliwia ona zespoły mogą uzyskać dostęp do w pełni skonfigurowane środowisko, gdy jest to konieczne. W tym scenariuszu laboratorium ma następujące zalety:

- Organizacje mogą przydzielać wspólnego środowiska testowe zapewnienie spójności między zespołami.
- Testerzy można przetestować najnowszą wersję aplikacji, szybko aprowizując środowiska Windows i Linux za pomocą szablonów wielokrotnego użytku.
- Administratorzy mogą połączyć z laboratorium do DevOps platformy Azure, która umożliwia realizację scenariuszy DevOps
- Właściciele laboratorium można kontrolować koszty za zapewnienie, że:
    - [Maszyny wirtualne w środowiskach są zamykane](devtest-lab-set-lab-policy.md#set-auto-shutdown) kiedy nieużywany
    - Tylko [umożliwiając podzbiór rozmiarów wystąpień maszyn wirtualnych dla](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) określonych labs
    - [Zarządzanie cele kosztów i powiadomienia](devtest-lab-configure-cost-management.md) w każdym środowisku laboratoryjnym.

Aby uzyskać więcej informacji, zobacz [środowisk testowych użycia usługi Azure DevTest Labs dla maszyn wirtualnych i PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Odpytywanie w trybie piaskownicy
Deweloperzy często badanie różnych technologii lub projektowania infrastruktury. Domyślnie wszystkie środowiska utworzone za pomocą usługi DevTest Labs są tworzone w swojej grupie zasobów. Użytkownik usługi DevTest Labs uzyskuje dostęp tylko do odczytu do tych zasobów. Jednak dla deweloperów, którzy muszą mieć większą kontrolę, można zaktualizować ustawienie poziomie laboratorium zapewnienie [uprawnienia współautora](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) użytkownikowi DevTest Labs źródłowy dla każdego środowiska, które tworzą.  Za pomocą usługi DevTest Labs deweloperzy mogą być podawane uprawnienia współautora automatycznie do środowisk, które tworzą w środowisku laboratoryjnym.  Ten scenariusz umożliwia deweloperom Dodawanie i/lub zmienić zasoby platformy Azure, zgodnie z zapotrzebowaniem w ich środowiskach deweloperskich lub testowania. [Koszty według zasobu](devtest-lab-configure-cost-management.md#view-cost-by-resource) strona pozwala na śledzenie kosztów dla każdego środowiska, używane do badania właścicielom laboratorium.

Aby uzyskać więcej informacji, zobacz [Ustawianie praw dostępu do grupy zasobów w środowisku](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Szkolenia, praktycznym laboratoriom i hackathonów 
Laboratorium Azure DevTest Labs działa jako kontener doskonałe przejściowy działań, takich jak warsztatów, warsztaty, szkoleniach lub hackathonów.  Usługa umożliwia tworzenie laboratorium, w którym mogą udostępniać szablony niestandardowe, które każdy szkoleniowych można użyć do utworzenia identyczne i wyizolowanego środowiska do trenowania. W tym scenariuszu laboratorium ma następujące zalety:

- [Zasady](devtest-lab-set-lab-policy.md) upewnij się, Zaleć pobierać tylko liczby zasobów, takich jak maszyny wirtualne, które są im niezbędne.
- Wstępnie skonfigurowany i utworzony maszyny są [zgłoszone](devtest-lab-add-claimable-vm.md) za pomocą jednej akcji z szkoleniowych.
- Labs są udostępniane przez uzyskanie dostępu do Zaleć [adres URL dla laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Daty wygaśnięcia](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) na maszynach wirtualnych upewnij się, że maszyny zostaną usunięte po są już potrzebne.
- Łatwo [Usuń laboratorium](devtest-lab-delete-lab-vm.md#delete-a-lab) i wszystkie [powiązane zasoby](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) po zakończeniu szkolenia.

Aby uzyskać więcej informacji, zobacz [użycia usługi Azure DevTest Labs do trenowania](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Weryfikacja koncepcji a skalowanego wdrożenia
Po podjęciu decyzji zapoznać się z usługi DevTest Labs, istnieją dwie ścieżki ogólnego do przodu: Weryfikacja koncepcji vs wdrożenia skalowany.  

A **skalowania wdrożenia** składa się z tygodni/miesięcy, przeglądanie i planowania z celem wdrażania w usłudze DevTest Labs w całym przedsiębiorstwie, który ma setki lub tysiące deweloperów.

A **weryfikacji koncepcji** wdrożenia koncentruje się na najwięcej starań jeden zespół w celu ustanowienia wartość organizacji. Mogą być kuszące myśleć skalowanego wdrożenia, to podejście jest zwykle częściej niż weryfikacji koncepcji opcji nie powiedzie się. Dlatego zaleca się, możesz zacząć od małej, ucz się pierwszym zespołem, powtórz tej samej metody, przy użyciu dwóch lub trzech dodatkowych zespołów, a następnie Zaplanuj wdrożenia skalowanego oparty na wiedzy uzyskanej. Pomyślnie weryfikację koncepcji zaleca się, czy możesz wybrać jednego lub dwóch zespołów i określenie ich scenariuszy (vs Środowisko deweloperskie środowisk testowych), dokumentów ich bieżącego przypadki użycia i wdrażanie DevTest Labs.

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły:

- [DevTest Labs — pojęcia](devtest-lab-concepts.md)
- [DevTest Labs — często zadawane pytania](devtest-lab-faq.md)

