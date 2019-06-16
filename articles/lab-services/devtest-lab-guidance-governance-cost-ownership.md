---
title: Zarządzanie infrastrukturą usługi Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące ładu infrastruktury usługi Azure DevTest Labs.
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
ms.openlocfilehash: 7832691812d8f10342dc7df20a7cfab7265f2d9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775716"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Zarządzanie infrastruktury usługi Azure DevTest Labs — Zarządzanie kosztami i własność
Koszt i własności są podstawowe kwestie, należy wziąć pod uwagę tworzenia programowanie i testowanie środowisk. W tej sekcji znajdziesz informacje, które pomaga w optymalizacji kosztów i dopasuj własności w środowisku.

## <a name="optimize-for-cost"></a>Optymalizacja pod kątem kosztów

### <a name="question"></a>Pytanie
Jak można optymalizację kosztów w środowisku Moje DevTest Labs?

### <a name="answer"></a>Odpowiedź
Istnieje szereg wbudowanych funkcji DevTest Labs, którego można zoptymalizować koszt. Zobacz [usługa cost management, progi](devtest-lab-configure-cost-management.md) [, zasad i](devtest-lab-set-lab-policy.md) artykuły, aby ograniczyć działań użytkowników. 

Jak korzystać z usługi DevTest Labs w przypadku obciążeń tworzenia i testowania, można rozważyć wykorzystanie [korzyści z subskrypcji Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/), w ramach umowy Enterprise Agreement. Alternatywnie, jeśli jesteś klientem płatność zgodnie z rzeczywistym użyciem, warto wziąć pod uwagę [płatności — jako — możesz Przejdź DevTest oferty](https://azure.microsoft.com/offers/ms-azr-0023p/).

To podejście oferuje wiele korzyści:

- Specjalne niższe stawki i testowanie na maszynach wirtualnych Windows, usług cloud services, HDInsight, App Service i Logic Apps
- Atrakcyjnymi stawkami Enterprise Agreement (EA) w innych usługach platformy Azure
- Dostęp do specjalnych obrazów na potrzeby tworzenia/testowania w galerii, w tym obrazów systemu Windows 8.1 i Windows 10
 
Tylko aktywni subskrybenci programu Visual Studio (subskrypcje standardowe, rocznych subskrypcji chmury i miesięczne subskrypcje chmury) mogą korzystać z zasobów platformy Azure w obrębie przedsiębiorstwa subskrypcji i testowania aplikacji. Jednak użytkownicy końcowi mogą uzyskiwać dostęp aplikacji do przesłania opinii lub przeprowadzenia testów akceptacyjnych. Użycie zasobów w ramach tej subskrypcji jest ograniczone do tworzenia i testowania aplikacji bez gwarancji czasu dostępności.

Jeśli zdecydujesz się używać oferty DevTest, należy pamiętać, że ta korzyść jest przeznaczona wyłącznie do celów tworzenia i testowania aplikacji. Użycie w ramach subskrypcji nie jest objęte gwarantowaną finansowo umową SLA, z wyjątkiem korzystania z usługi Azure DevOps i platformy HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Zdefiniuj opartej na rolach dostęp do całej organizacji
### <a name="question"></a>Pytanie
Jak zdefiniować kontroli dostępu opartej na rolach dla moich DevTest Labs środowiska, aby upewnić się, że dział IT określają sposób, gdy deweloperzy i testowania można wykonać swoją pracę? 

### <a name="answer"></a>Odpowiedź
Istnieje szerokie wzorzec, jednak szczegóły zależy od organizacji.

Centralna IT powinien właścicielem tylko co jest potrzebne, a także umożliwiają zespołom projektu i aplikacji i mają wymagane poziom kontroli. Zwykle oznacza to, że centralne IT jest właścicielem subskrypcji i obsługuje podstawowe funkcje IT, takie jak konfiguracje sieci. Zbiór **właścicieli** dla subskrypcji powinna być niewielka. Właściciele, można wyznaczyć dodatkowych właścicieli, gdy istnieje potrzeba lub stosowanie zasad na poziomie subskrypcji, na przykład "Brak publicznego adresu IP".

Może to być podzbiór użytkowników, którzy wymagają dostępu do różnych subskrypcji, takie jak obsługa 1 lub 2 warstwy. W tym przypadku zaleca się tym użytkownikom nadać **Współautor** dostępu, tak aby mogli zarządzać zasobami, ale nie zapewnia użytkownikom dostęp lub dostosować zasady.

Zasób usługi DevTest Labs powinna być własnością właścicieli, którzy znajdują się blisko zespołu projektu/aplikacji. Jest to spowodowane zrozumieniu wymagań w zakresie maszyn i wymagane oprogramowanie. W większości organizacji właścicielem tego zasobu usługi DevTest Labs jest często realizacji projektu/programowania. Tego właściciela można zarządzać użytkownikami i zasadami w środowisku laboratoryjnym i może zarządzać wszystkich maszyn wirtualnych w środowisku laboratorium.

Członkowie zespołu projektu/aplikacji, należy dodać do roli usługi DevTest Labs użytkowników. Tacy użytkownicy umożliwia tworzenie maszyn wirtualnych (wbudowane z laboratorium i zasadami poziomu subskrypcji). Można również zarządzać maszyn wirtualnych. Nie mogą zarządzać maszyn wirtualnych, które należą do innych użytkowników.

Aby uzyskać więcej informacji, zobacz [szkielet platformy Azure w przedsiębiorstwach — narzucony nadzór subskrypcji](/azure/architecture/cloud-adoption/appendix/azure-scaffold) dokumentacji.


## <a name="next-steps"></a>Kolejne kroki
Zobacz [firmowe zasady i zgodność](devtest-lab-guidance-governance-policy-compliance.md).
