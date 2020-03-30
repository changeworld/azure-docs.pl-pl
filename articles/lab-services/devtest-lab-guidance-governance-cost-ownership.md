---
title: Zarządzanie kosztami i własnością w laboratoriach devtest platformy Azure
description: Ten artykuł zawiera informacje, które pomagają zoptymalizować pod kątem kosztów i wyrównać własność w całym środowisku.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561658"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Zarządzanie infrastrukturą laboratoriów testów dewiacyjnych platformy Azure — zarządzanie kosztami i własnością
Koszty i własność są głównymi problemami, jeśli wziąć pod uwagę tworzenie środowisk deweloperskich i testowych. W tej sekcji znajdziesz informacje, które pomagają zoptymalizować pod kątem kosztów i wyrównać własność w całym środowisku.

## <a name="optimize-for-cost"></a>Optymalizacja pod kątem kosztów

### <a name="question"></a>Pytanie
Jak zoptymalizować pod kątem kosztów w moim środowisku DevTest Labs?

### <a name="answer"></a>Odpowiedź
Istnieje wiele wbudowanych funkcji DevTest Labs, które pomagają zoptymalizować pod kątem kosztów. Aby ograniczyć działania użytkowników, zobacz [artykuły dotyczące zarządzania kosztami, progów](devtest-lab-configure-cost-management.md) [i zasad.](devtest-lab-set-lab-policy.md) 

Podczas korzystania z DevTest Labs dla obciążeń deweloperskich i testowych można rozważyć wykorzystanie [korzyści subskrypcji deweloperów/testów przedsiębiorstwa](https://azure.microsoft.com/offers/ms-azr-0148p/)w ramach umowy Enterprise Agreement. Alternatywnie, jeśli jesteś klientem Pay as you Go, możesz rozważyć [ofertę Płatności zgodnie z rzeczywistym życzeniem.](https://azure.microsoft.com/offers/ms-azr-0023p/)

Takie podejście zapewnia wiele korzyści:

- Specjalne niższe stawki za dewelopera/testowania na maszynach wirtualnych systemu Windows, usługach w chmurze, usługi HDInsight, app service i aplikacjach logiki
- Stawki great enterprise agreement (EA) w innych usługach platformy Azure
- Dostęp do ekskluzywnych obrazów deweloperskich/testowych w galerii, w tym systemów Windows 8.1 i Windows 10
 
Tylko aktywni subskrybenci programu Visual Studio (subskrypcje standardowe, roczne subskrypcje w chmurze i miesięczne subskrypcje w chmurze) mogą korzystać z zasobów platformy Azure uruchomionych w ramach subskrypcji dewelopera/testowego przedsiębiorstwa. Jednak użytkownicy końcowi mogą uzyskać dostęp do aplikacji, aby przekazać opinię lub wykonać testy akceptacji. Korzystanie z zasobów w ramach tej subskrypcji jest ograniczone do tworzenia i testowania aplikacji i nie jest oferowana żadna gwarancja czasu pracy.

Jeśli zdecydujesz się użyć oferty DevTest, należy pamiętać, że ta korzyść jest wyłącznie do tworzenia i testowania aplikacji. Użycie w ramach subskrypcji nie zawiera umowy SLA wspieranej finansowo, z wyjątkiem korzystania z usług Azure DevOps i HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definiowanie dostępu opartego na rolach w całej organizacji
### <a name="question"></a>Pytanie
Jak zdefiniować kontrolę dostępu opartą na rolach dla moich środowisk DevTest Labs, aby upewnić się, że it może regulować, podczas gdy deweloperzy /test mogą wykonywać swoją pracę? 

### <a name="answer"></a>Odpowiedź
Istnieje szeroki wzorzec, jednak szczegóły zależą od organizacji.

Centralna dział IT powinna posiadać tylko to, co jest konieczne, i umożliwiać zespołom projektowym i zespołowym posiadanie wymaganego poziomu kontroli. Zazwyczaj oznacza to, że centralna firma IT jest właścicielem subskrypcji i obsługuje podstawowe funkcje IT, takie jak konfiguracje sieci. Zestaw **właścicieli** subskrypcji powinien być mały. Ci właściciele mogą wyznaczyć dodatkowych właścicieli, gdy zajdzie taka potrzeba, lub zastosować zasady na poziomie subskrypcji, na przykład "Brak publicznego adresu IP".

Może istnieć podzbiór użytkowników, którzy wymagają dostępu w ramach subskrypcji, takich jak obsługa warstwy 1 lub warstwy 2. W takim przypadku zaleca się przyznanie tym użytkownikom dostępu **współautora,** aby mogli zarządzać zasobami, ale nie zapewniali dostępu użytkowników ani nie dostosowywali zasad.

Zasób DevTest Labs powinien być własnością właścicieli, którzy znajdują się blisko zespołu projektu/aplikacji. To dlatego, że rozumieją swoje wymagania w zakresie maszyn i wymaganego oprogramowania. W większości organizacji właścicielem tego zasobu DevTest Labs jest zwykle potencjalny klient projektu/rozwoju. Ten właściciel może zarządzać użytkownikami i zasadami w środowisku laboratoryjnym i może zarządzać wszystkimi maszynami wirtualnymi w środowisku DevTest Labs.

Członkowie zespołu projektu/aplikacji powinny zostać dodane do roli Użytkownicy DevTest Labs. Ci użytkownicy mogą tworzyć maszyny wirtualne (zgodne z zasadami na poziomie laboratorium i subskrypcji). Mogą również zarządzać własnymi maszynami wirtualnymi. Nie mogą zarządzać maszynami wirtualnymi należącymi do innych użytkowników.

Aby uzyskać więcej informacji, zobacz [szkieletu przedsiębiorstwa platformy Azure — dokumentacja zarządzania prescriptive subskrypcji.](/azure/architecture/cloud-adoption/appendix/azure-scaffold)


## <a name="next-steps"></a>Następne kroki
Zobacz [Zasady korporacyjne i zgodność](devtest-lab-guidance-governance-policy-compliance.md).
