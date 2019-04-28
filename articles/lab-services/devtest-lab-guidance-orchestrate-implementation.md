---
title: Organizowanie wdrażania usługi Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące organizowanie wdrożenia usługi Azure DevTest Labs w Twojej organizacji.
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
ms.openlocfilehash: e0ac09a68bda539fe7abd05fce1739d1a58a3c99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127348"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Organizowanie wdrażania usługi Azure DevTest Labs
Ten artykuł zawiera zalecane podejście do szybkiego wdrażania i stosowania usługi Azure DevTest Labs. Poniższa ilustracja kładzie nacisk całego procesu jako wskazówki podczas obserwowania elastyczność do obsługi różnych wymagania branżowe i scenariuszy.

![Kroki dotyczące wdrażania usługi Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Założenia
W tym artykule założono, że następujące elementy w miejscu przed wdrożeniem pilotażu DevTest Labs:

- **Subskrypcja platformy Azure**: Zespołu pilotażowego ma dostęp do wdrażania zasobów w subskrypcji platformy Azure. W przypadku obciążeń tylko tworzenia i testowania aplikacji, zaleca się wybranie oferty Enterprise DevTest dodatkowe dostępnych obrazów i niższe stawki na maszynach wirtualnych Windows.
- **Dostęp do lokalnego**: Jeśli to konieczne, dostępu do lokalnego została już skonfigurowana. Dostępu do lokalnego może się odbywać za pośrednictwem połączenia sieci VPN typu lokacja lokacja lub Expressroute. Połączeń za pomocą usługi Express Route zazwyczaj może trwać wiele tygodni ustanowić, zaleca się mieć Express Route w miejscu przed rozpoczęciem projektu.
- **Pilotażowy zespoły**: Zespoły projektu początkowego projektowania, które korzysta z usługi DevTest Labs wykryto wraz z odpowiednich rozwoju lub testowania działania i określać wymagania/celów/wyznaczone cele dotyczące tych zespołów.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Punkt kontrolny 1: Ustanów sieciowego początkowej topologii i projekt
Pierwszy obszar koncentracji uwagi podczas wdrażania rozwiązania w usłudze Azure DevTest Labs jest ustanowienie planowane łączności dla maszyn wirtualnych. Poniższe kroki przedstawiają niezbędnych procedur:

1. Zdefiniuj **początkowej zakresów adresów IP** są przypisane do subskrypcji usługi DevTest Labs na platformie Azure. Ten krok wymaga prognozowania oczekiwanego użycia w wielu maszyn wirtualnych, tak, aby można było zapewnić dużą wystarczająco dużo bloku na przyszłe rozszerzenia.
2. Identyfikowanie **metody żądanego dostępu** w laboratoria DevTest Lab (na przykład dostęp zewnętrznych / wewnętrzny). Kluczowym punktem, w tym kroku ma na celu określenie, czy maszyny wirtualne mają publiczne adresy IP (czyli dostępne z Internetu, bezpośrednio).
3. Identyfikowanie i ustanowić **metody łączności** z pozostałą częścią platformy Azure i w chmurze środowisko lokalne. Jeśli włączono wymuszone routing z usługą Express Route, jest prawdopodobne, że maszyny wirtualne muszą odpowiednie konfiguracje, aby przechodzić przez zapory firmowej.
4. Jeśli maszyny wirtualne mają być **przyłączonych do domeny**, określić, czy dołączyć domeny oparte na chmurze (usługi katalogu usługi AAD na przykład) lub w domenie lokalnej. Dla lokalnego należy określić które jednostki organizacyjnej (OU) w usłudze active directory, który łączy się maszyn wirtualnych. Ponadto upewnij się, że użytkownicy mają dostęp do przyłączenia (lub utworzyć konto usługi, który ma możliwość tworzenia rekordów komputera w domenie)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Punkt kontrolny 2: Wdrażanie pilotażowe laboratorium
Po umieszczeniu topologii sieci w miejscu laboratorium pierwszy/projekt pilotażowy można utworzyć, wykonując następujące kroki:

1. Tworzenie środowiska usługi DevTest Labs początkowej (instrukcje krok po kroku można znaleźć [tutaj](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md))
2. Określ dopuszczalny rozmiar obrazów maszyn wirtualnych i rozmiarach do użytku z programem lab. Zdecyduj, czy obrazy niestandardowe można przekazać na platformę Azure do użycia z usługą DevTest Labs.
3. Bezpieczny dostęp do laboratorium, tworząc początkowej ról Base dostęp do formantów (RBAC) dla lab (laboratorium właścicieli i użytkowników laboratorium). Zalecane jest użycie kont synchronizacja usługi active directory z usługą Azure Active Directory dla tożsamości z usługą DevTest Labs.
4. Konfigurowanie usługi DevTest Labs korzystania z zasad, takie jak harmonogramy, koszt zarządzania, przejęcia maszyn wirtualnych, niestandardowych obrazów i formuł.
5. Należy ustanowić repozytorium online, takich jak Azure repozytoriów/Git.
6. Zdecyduj, przy użyciu repozytoriów publicznych lub prywatnych lub kombinację obu tych elementów. Organizowanie szablonów JSON dla wdrożeń i sustainment długoterminowego.
7. Jeśli to konieczne, należy utworzyć niestandardowe artefakty. Ten krok jest opcjonalny. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Punkt kontrolny 3: Dokumentacji, pomocy technicznej, Dowiedz się więcej i poprawy
Początkowej zespołów pilotowego może wymagać szczegółowe pomocy technicznej dla klientów zaczynających pracę. Użyj swojego środowiska, aby zapewnić dokumentację i pomoc techniczna jest w miejscu na potrzeby ciągłego wdrażania usługi Azure DevTest Labs.

1. Wprowadzenie zespołów pilotowego do swoich nowych zasobów DevTest Labs (pokazy, dokumentacja)
2. Na podstawie doświadczeń zespołów pilotowego, planowanie i dostarczaj dokumentację, zgodnie z potrzebami
3. Formalnego proces dołączania nowe zespoły (Tworzenie i konfigurowanie laboratoriów, zapewniając dostęp, itp)
4. Oparte na początkowej pobór, upewnij się, że oryginalna prognozy przestrzeni adresów IP nadal jest uzasadnione i dokładne
5. Upewnij się, zostały ukończone odpowiednie przeglądy zgodności i zabezpieczeń

## <a name="next-steps"></a>Kolejne kroki
Zobacz następny artykuł w tej serii: [Zarządzanie infrastrukturą usługi Azure DevTest Labs](devtest-lab-guidance-governance-resources.md)
