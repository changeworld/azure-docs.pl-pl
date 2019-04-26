---
title: Skalowanie infrastruktury usługi Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące skalowania w górę infrastruktury usługi Azure DevTest Labs.
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
ms.openlocfilehash: 25a088686c739c53feadd6354baf75f3147bdc33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561493"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Skalowanie infrastruktury usługi Azure DevTest Labs
Przed ich wprowadzeniem DevTest Labs na skalę przedsiębiorstwa, istnieje kilka punktów kluczowe decyzje. Zrozumienie tych decyzje na wysokim poziomie pomaga organizacji decyzji projektowych w przyszłości. Jednak te punkty powinna nie pozostałe organizacji uruchamianie weryfikacji koncepcji. Dostępne są następujące obszary trzy pierwsze, planowanie początkowej skalowanie w górę:

- Sieci i zabezpieczeń
- Topologia subskrypcji
- Role i obowiązki

## <a name="networking-and-security"></a>Sieci i zabezpieczeń
Sieci i zabezpieczeń są filary posiadać wszystkie organizacje. Podczas wdrażania całego przedsiębiorstwa wymaga dużo dokładniejszej analizy, istnieją zmniejszenie liczby wymagań, aby pomyślnie wykonać weryfikacji koncepcji. Kilka kluczowych obszarów fokus obejmują:

- **Subskrypcja platformy Azure** — w celu wdrożenia usługi DevTest Labs, musi mieć dostęp do subskrypcji platformy Azure przy użyciu odpowiednich uprawnień do tworzenia zasobów. Istnieją różne sposoby, aby uzyskać dostęp do subskrypcji platformy Azure, łącznie z umową Enterprise Agreement i płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji o uzyskiwaniu dostępu do subskrypcji platformy Azure, zobacz [licencjonowania platformy Azure dla przedsiębiorstw](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Dostęp do zasobów lokalnych** — niektóre organizacje wymagają ich zasobów w usłudze DevTest Labs mają dostęp do zasobów lokalnych. Jest wymagane bezpieczne połączenie ze środowiska lokalnego do platformy Azure. Dlatego jest ważne, aby ustawić w górę/Konfigurowanie połączenia sieci VPN lub Expressroute przed rozpoczęciem pracy. Aby uzyskać więcej informacji, zobacz [omówienie sieci wirtualnych](../virtual-network/virtual-networks-overview.md).
- **Dodatkowe wymagania dotyczące zabezpieczeń** — inne wymagania dotyczące zabezpieczeń, takie jak zasady maszyny, dostęp do publicznych adresów IP w celu nawiązywania połączenia z Internetu są scenariusze, które mogą wymagać przejrzenia przed wdrożeniem weryfikacji koncepcji. 

## <a name="subscription-topology"></a>Topologia subskrypcji
Topologia subskrypcji jest krytycznie ważnym założeniem projektowym w przypadku wdrażania usługi DevTest Labs przedsiębiorstwa. Jednak nie jest wymagane do zestalenia podczas podejmowania wszystkich decyzji do czasu, po zakończeniu etapu weryfikacji koncepcji. Podczas obliczania liczby subskrypcji, wymagane do wdrożenia dla przedsiębiorstwa, są dwoma skrajnymi poziomami: 

- Jedna subskrypcja dla całej organizacji
- Subskrypcji na użytkownika

Następnie możemy wyróżnić profesjonaliści każde podejście.

### <a name="one-subscription"></a>Jedna subskrypcja
Często podejście jedna subskrypcja nie jest zarządzany w dużych przedsiębiorstw. Jednak ograniczenie liczby subskrypcji ma następujące zalety:

- **Prognozowanie** kosztów dla przedsiębiorstw.  Budżetowania staje się znacznie łatwiejsze, w ramach jednej subskrypcji, ponieważ wszystkie zasoby znajdują się w jednej puli. To podejście umożliwia prostsze podejmowania decyzji w przypadku sprawowanie kontroli kosztów miary w danym momencie w okresie rozliczeniowym.
- **Możliwości zarządzania** maszyn wirtualnych, artefakty, formuły, konfiguracja sieci, uprawnienia, zasady, łatwiej jest itp., ponieważ wszystkie aktualizacje są wymagane tylko w jednej subskrypcji, w przeciwieństwie do wprowadzania aktualizacji w wielu subskrypcjach.
- **Sieć** nakład pracy jest znacznie uproszczone w ramach jednej subskrypcji dla przedsiębiorstw, w których łączność lokalna jest wymagany. Łączenie sieci wirtualnych między subskrypcjami (model piasty i szprych) jest wymagany w przypadku dodatkowych subskrypcji, co wymaga dodatkowej konfiguracji, zarządzania, przestrzenie adresów IP, itp.
- **Zespołowej** jest łatwiejsze, gdy wszyscy działa w tej samej subskrypcji — na przykład łatwiej Przypisz ponownie Maszynę wirtualną do współpracownika, udostępnianie zasobów zespołu, itp.

### <a name="subscription-per-user"></a>Subskrypcji na użytkownika
Oddzielnej subskrypcji na użytkownika zapewnia możliwości równy do alternatywnego spektrum. Masz wiele subskrypcji zalety:

- **Azure skalowanie przydziały** nie będą utrudniać przyjęcia. Na przykład w trakcie tworzenia tej dokumentacji, które platforma Azure zezwoli 200 kont magazynu na subskrypcję. Istnieją operacyjnej przydziały dla większości usług platformy Azure (wiele można dostosować, niektóre nie). W tym modelu subskrypcji na użytkownika jest mało prawdopodobne, osiągnięciu większości przydziałów. Aby uzyskać więcej informacji na temat bieżące limity przydziału skalowania platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- **Obciążenia zwrotne u** do grup lub poszczególnych deweloperów stają się znacznie łatwiejsze, umożliwiając organizacjom w celu uwzględnienia kosztów za pomocą ich bieżącego modelu.
- **Własność i uprawnień** usługi DevTest Labs środowiska są proste. Oferuje deweloperom dostęp na poziomie subskrypcji i są odpowiedzialne za wszystko w tym konfiguracji sieci, zasad laboratorium i zarządzania maszyną Wirtualną w 100%.

W przedsiębiorstwie może być za mało ograniczeń skrajnymi poziomami spektrum. W związku z tym konieczne może konfigurować subskrypcje w sposób, który znajduje się w trakcie ekstremalnymi te. Najlepszym rozwiązaniem jest celem organizacja powinna dotyczyć pełnić minimalnej liczby subskrypcji możliwe pamiętając o tym, wymuszania funkcji, które zwiększają łączna liczba subskrypcji. Aby przypomnę topologii subskrypcji ma kluczowe znaczenie dla wdrożenia w przedsiębiorstwie usługi DevTest Labs, ale nie powinno opóźniać weryfikacji koncepcji. Istnieją dodatkowe informacje w [nadzoru](devtest-lab-guidance-governance-policy-compliance.md) artykuł na temat wyboru szczegółowości subskrypcji i laboratorium w organizacji.

## <a name="roles-and-responsibilities"></a>Role i obowiązki
DevTest Labs weryfikacji koncepcji ma trzech głównych ról z określonymi obowiązkami — właściciel subskrypcji, właściciel usługi DevTest Labs, użytkownik usługi DevTest Labs i opcjonalnie współautora.

- **Właściciel subskrypcji** — właściciel subskrypcji ma wystarczające uprawnienia do administrowania subskrypcji platformy Azure łącznie z przypisaniem użytkowników, Zarządzanie zasadami, tworzenie i Zarządzanie topologią sieci żądanie zwiększenia limitu przydziału, itp. Więcej informacji znajduje się w [tym artykule](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **Właściciel usługi DevTest Labs** — właściciel laboratorium ma pełny dostęp administracyjny do laboratorium. Ta osoba jest odpowiedzialny za dodawanie/usuwanie użytkowników, zarządzania ustawieniami kosztów, ustawienia ogólne lab i inne zadania oparte na maszynę Wirtualną/artefaktu. Właściciel laboratorium ma również wszystkie prawa użytkownik usługi DevTest Labs.
- **Użytkownik usługi DevTest Labs** — użytkownik usługi DevTest Labs mogą tworzyć i wykorzystywać maszyn wirtualnych w środowisku laboratoryjnym. Osoby te mają niektóre minimalne możliwości administracyjnych na maszynach wirtualnych, które tworzą (start/stop/delete/Konfigurowanie maszyn wirtualnych). Użytkownicy nie mogą zarządzać maszynami wirtualnymi w innych użytkowników.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następny artykuł w tej serii: [Organizowanie wdrażania usługi Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)