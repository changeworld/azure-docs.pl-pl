---
title: Skalowanie infrastruktury laboratoriów devtest azure
description: Ten artykuł zawiera wskazówki dotyczące skalowania infrastruktury laboratoriów Azure DevTest.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 3a48cef2210721bf7116b1c4ad1169779288f47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644838"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Skalowanie infrastruktury laboratoriów devtest azure
Przed wdrożeniem DevTest Labs w skali przedsiębiorstwa istnieje kilka kluczowych punktów decyzyjnych. Zrozumienie tych punktów decyzyjnych na wysokim poziomie pomaga organizacji w podejmowaniu decyzji projektowych w przyszłości. Jednak te punkty nie powinny powstrzymywać organizacji od rozpoczęcia weryfikacji koncepcji. Trzy najważniejsze obszary wstępnego planowania skalowania to:

- Sieć i bezpieczeństwo
- Topologia subskrypcji
- Role i obowiązki

## <a name="networking-and-security"></a>Sieć i bezpieczeństwo
Sieci i zabezpieczenia są podstawą dla wszystkich organizacji. Podczas gdy wdrożenie w całym przedsiębiorstwie wymaga znacznie głębszej analizy, istnieje zmniejszona liczba wymagań, aby pomyślnie wykonać dowód koncepcji. Kilka kluczowych obszarów zainteresowania to:

- **Subskrypcja platformy Azure** — aby wdrożyć laboratoria DevTest, musisz mieć dostęp do subskrypcji platformy Azure z odpowiednimi prawami do tworzenia zasobów. Istnieje wiele sposobów uzyskiwania dostępu do subskrypcji platformy Azure, w tym umowy Enterprise Agreement i Pay As You Go. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do subskrypcji platformy Azure, zobacz [Licencjonowanie platformy Azure dla przedsiębiorstwa.](https://azure.microsoft.com/pricing/enterprise-agreement/)
- **Dostęp do zasobów lokalnych** — niektóre organizacje wymagają, aby ich zasoby w laboratoriach DevTest Labs miały dostęp do zasobów lokalnych. Potrzebne jest bezpieczne połączenie ze środowiska lokalnego z platformą Azure. Dlatego ważne jest, aby skonfigurować/skonfigurować połączenie VPN lub Express Route przed rozpoczęciem pracy. Aby uzyskać więcej informacji, zobacz [omówienie sieci wirtualnych](../virtual-network/virtual-networks-overview.md).
- **Dodatkowe wymagania dotyczące zabezpieczeń** — inne wymagania dotyczące zabezpieczeń, takie jak zasady komputera, dostęp do publicznych adresów IP, łączenie się z Internetem to scenariusze, które mogą wymagać przejrzenia przed wdrożeniem dowodu koncepcji. 

## <a name="subscription-topology"></a>Topologia subskrypcji
Topologia subskrypcji jest krytyczną kwestią projektowania podczas wdrażania DevTest Labs w przedsiębiorstwie. Nie jest jednak wymagane, aby ugruntować wszystkie decyzje dopiero po dowodu koncepcji został zakończony. Podczas oceny liczby subskrypcji wymaganych dla implementacji przedsiębiorstwa istnieją dwie skrajności: 

- Jedna subskrypcja dla całej organizacji
- Subskrypcja na użytkownika

Następnie zwracamy uwagę na zalety każdego podejścia.

### <a name="one-subscription"></a>Jedna subskrypcja
Często podejście jednej subskrypcji nie jest możliwe do zarządzania w dużym przedsiębiorstwie. Jednak ograniczenie liczby subskrypcji zapewnia następujące korzyści:

- **Prognozowanie** kosztów dla przedsiębiorstw.  Budżetowanie staje się znacznie łatwiejsze w jednej subskrypcji, ponieważ wszystkie zasoby znajdują się w jednej puli. Takie podejście pozwala na prostsze podejmowanie decyzji o tym, kiedy należy wykonywać środki kontroli kosztów w danym momencie w cyklu rozliczeniowym.
- **Możliwość zarządzania** maszyn wirtualnych, artefakty, formuły, konfiguracja sieci, uprawnienia, zasady, itp jest łatwiejsze, ponieważ wszystkie aktualizacje są wymagane tylko w jednej subskrypcji, w przeciwieństwie do dokonywania aktualizacji w wielu subskrypcjach.
- **Wysiłek sieciowy** jest znacznie uproszczony w jednej subskrypcji dla przedsiębiorstw, w których wymagana jest łączność lokalna. Łączenie sieci wirtualnych między subskrypcjami (model szprychy koncentratora) jest wymagane z dodatkowymi subskrypcjami, co wymaga dodatkowej konfiguracji, zarządzania, przestrzeni adresów IP itp.
- **Współpraca zespołowa** jest łatwiejsza, gdy wszyscy pracują w tej samej subskrypcji — na przykład łatwiej jest ponownie przypisać maszynę wirtualną do współpracownika, udostępnić zasoby zespołu itp.

### <a name="subscription-per-user"></a>Subskrypcja na użytkownika
Oddzielna subskrypcja na użytkownika zapewnia równe szanse dla alternatywnego spektrum. Korzyści z posiadania wielu subskrypcji obejmują:

- **Przydziały skalowania platformy Azure** nie będą utrudniać przyjęcia. Na przykład od tego pisania platformy Azure zezwala 200 kont magazynu na subskrypcję. Istnieją przydziały operacyjne dla większości usług na platformie Azure (wiele można dostosować, niektóre nie mogą). W tym modelu subskrypcji na użytkownika jest bardzo mało prawdopodobne, że większość przydziałów zostaną osiągnięte. Aby uzyskać więcej informacji na temat bieżących przydziałów skalowania platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- **Obciążenia zwrotne** dla grup lub poszczególnych deweloperów stają się znacznie łatwiejsze, umożliwiając organizacjom rozliczanie kosztów przy użyciu ich bieżącego modelu.
- **Własność & uprawnienia** środowisk DevTest Labs są proste. Dajesz deweloperom dostęp na poziomie subskrypcji i są w 100% odpowiedzialni za wszystko, w tym konfigurację sieci, zasady laboratorium i zarządzanie maszynami wirtualnymi.

W Enterprise, może być wystarczająco dużo ograniczeń na skrajności widma. W związku z tym może być konieczne skonfigurowanie subskrypcji w sposób, który mieści się w środku tych skrajności. Najlepszym rozwiązaniem jest, aby organizacja używała minimalnej liczby subskrypcji, ponieważ jest to możliwe, pamiętając o funkcjach wymuszania, które zwiększają całkowitą liczbę subskrypcji. Aby powtórzyć, topologia subskrypcji ma kluczowe znaczenie dla wdrożenia w przedsiębiorstwie DevTest Labs, ale nie należy opóźniać weryfikacji koncepcji. Istnieją dodatkowe szczegóły w artykule [nadzoru](devtest-lab-guidance-governance-policy-compliance.md) na temat sposobu decydowania o subskrypcji i szczegółowej części laboratorium w organizacji.

## <a name="roles-and-responsibilities"></a>Role i obowiązki
Dowód koncepcji DevTest Labs ma trzy podstawowe role ze zdefiniowanymi obowiązkami — właściciel subskrypcji, właściciel DevTest Labs, użytkownik DevTest Labs i opcjonalnie współautor.

- **Właściciel subskrypcji** — właściciel subskrypcji ma prawa do administrowania subskrypcją platformy Azure, w tym przypisywania użytkowników, zarządzania zasadami, tworzenia & zarządzania topologią sieci, żądania zwiększenia przydziału itp. Aby uzyskać więcej informacji, zobacz [ten artykuł](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **DevTest Labs właściciel** — DevTest Labs właściciel ma pełny dostęp administracyjny do laboratorium. Ta osoba jest odpowiedzialna za dodawanie/usuwanie użytkowników, zarządzanie ustawieniami kosztów, ogólne ustawienia laboratorium i inne zadania oparte na maszynach wirtualnych/artefaktach. Właściciel laboratorium ma również wszystkie prawa użytkownika DevTest Labs.
- **DevTest Labs użytkownika** — DevTest Labs użytkownik może tworzyć i korzystać z maszyn wirtualnych w laboratorium. Osoby te mają pewne minimalne możliwości administracyjne na maszynach wirtualnych, które tworzą (start/stop/delete/configure their VMs). Użytkownicy nie mogą zarządzać maszynami wirtualnymi innych użytkowników.

## <a name="next-steps"></a>Następne kroki
Zobacz następny artykuł z tej serii: [Zaaranżuj implementację platformy Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)