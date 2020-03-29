---
title: Zarządzanie infrastrukturą laboratoriów testów dewiacyjnych platformy Azure — zasób
description: Ten artykuł dotyczy wyrównywania zasobów dla laboratoriów DevTest w organizacji i zarządzania nimi.
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
ms.openlocfilehash: 8bb00c770c61a0a5462a01ae552bd7e40a7cdb36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470653"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Zarządzanie infrastrukturą laboratoriów testów dewiacyjnych platformy Azure — zasoby
Ten artykuł dotyczy wyrównywania zasobów dla laboratoriów DevTest labs w organizacji i zarządzania nimi. 

## <a name="align-within-an-azure-subscription"></a>Wyrównaj w ramach subskrypcji platformy Azure 

### <a name="question"></a>Pytanie
Jak wyrównać zasoby DevTest Labs w ramach subskrypcji platformy Azure?

### <a name="answer"></a>Odpowiedź
Zanim organizacja zacznie używać platformy Azure do ogólnego tworzenia aplikacji, planiści IT powinni najpierw przejrzeć sposób wprowadzenia możliwości jako części ich ogólnego portfolio usług. Obszary do przeglądu powinny dotyczyć następujących kwestii:

- Jak zmierzyć koszt związany z cyklem życia tworzenia aplikacji?
- W jaki sposób organizacja dostosowuje proponowaną ofertę usług do korporacyjnych zasad zabezpieczeń? 
- Czy segmentacja jest wymagana do oddzielenia środowisk deweloperskich i produkcyjnych? 
- Jakie kontrole są wprowadzane w celu długoterminowej łatwości zarządzania, stabilności i wzrostu?

**Pierwszą zalecaną praktyką** jest przegląd taksonomii platformy Azure organizacji, w której przedstawiono podziały między subskrypcjami produkcyjnymi i deweloperskimi. Na poniższym diagramie sugerowana taksonomia pozwala na logiczne oddzielenie środowisk rozwoju/testowania i produkcji. Dzięki takiemu podejściu organizacja może wprowadzić kody rozliczeń, aby śledzić koszty skojarzone z każdym środowiskiem oddzielnie. Aby uzyskać więcej informacji, zobacz [Nakazowe zarządzanie subskrypcjami](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Ponadto można użyć [tagów platformy Azure](../azure-resource-manager/management/tag-resources.md) do organizowania zasobów do celów śledzenia i rozliczeń.

**Drugą zalecaną praktyką** jest włączenie subskrypcji DevTest w witrynie Azure Enterprise Portal. Umożliwia organizacji uruchamianie klienckich systemów operacyjnych, które zazwyczaj nie są dostępne w subskrypcji platformy Azure dla przedsiębiorstw. Następnie użyj oprogramowania dla przedsiębiorstw, w którym płacisz tylko za obliczenia i nie martw się o licencjonowanie. Zapewnia, że rozliczenia za wyznaczone usługi, w tym obrazy galerii w iaaS, takich jak Microsoft SQL Server, opiera się tylko na zużycie. Szczegółowe informacje o subskrypcji usługi Azure DevTest można znaleźć [tutaj](https://azure.microsoft.com/offers/ms-azr-0148p/) dla klientów umowy Enterprise Agreement (EA) i [tutaj](https://azure.microsoft.com/offers/ms-azr-0023p/) dla klientów Pay as you Go.

![Wyrównanie zasobów z subskrypcjami](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Ten model zapewnia organizacji elastyczność wdrażania platformy Azure DevTest Labs na dużą skalę. Organizacja może obsługiwać setki laboratoriów dla różnych jednostek biznesowych z 100 do 1000 maszyn wirtualnych działających równolegle. Promuje pojęcie scentralizowanego rozwiązania laboratorium przedsiębiorstwa, które może dzielić te same zasady zarządzania konfiguracją i kontroli zabezpieczeń.

Ten model zapewnia również, że organizacja nie wyczerpuje swoich limitów zasobów skojarzonych z ich subskrypcją platformy Azure. Aby uzyskać szczegółowe informacje na temat limitów subskrypcji i usług platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md). DevTest Labs proces inicjowania obsługi administracyjnej może zużywać dużą liczbę grup zasobów. Można zażądać limitów, które mają zostać zwiększone za pomocą żądania pomocy technicznej w subskrypcji usługi Azure DevTest. Zasoby w ramach subskrypcji produkcyjnej nie są zagrożone, jak subskrypcja rozwoju rośnie w użyciu. Aby uzyskać więcej informacji na temat skalowania laboratoriów DevTest, zobacz [Skalowanie przydziałów i limitów w laboratoriach DevTest](devtest-lab-scale-lab.md).

Typowy limit poziomu subskrypcji, który musi być uwzględniony, to sposób przydzielania przypisania zakresu adresów IP sieci do obsługi zarówno subskrypcji produkcyjnych, jak i deweloperskich. Te przypisania powinny uwzględniać wzrost w czasie (przy założeniu łączności lokalnej lub innej topologii sieci, która wymaga od przedsiębiorstwa zarządzania stosem sieciowym zamiast domyślnego wykonania platformy Azure). Zalecaną praktyką jest mieć kilka sieci wirtualnych, które mają duży prefiks adresu IP przypisane i podzielone z wielu dużych podsieci, a nie mieć wiele sieci wirtualnych z małych podsieci. Na przykład w przypadku 10 subskrypcji można zdefiniować 10 sieci wirtualnych (po jednej dla każdej subskrypcji). Wszystkie laboratoria, które nie wymagają izolacji, mogą współużytkować tę samą podsiecię w sieci wirtualnej subskrypcji.

## <a name="maintain-naming-conventions"></a>Obsługa konwencji nazewnictwa

### <a name="question"></a>Pytanie
Jak utrzymywać konwencję nazewnictwa w moim środowisku DevTest Labs?

### <a name="answer"></a>Odpowiedź
Można rozszerzyć bieżące konwencje nazewnictwa przedsiębiorstwa do operacji platformy Azure i uczynić je spójne w środowisku DevTest Labs.

Podczas wdrażania DevTest Labs, zaleca się, że masz określone zasady uruchamiania. Te zasady można wdrożyć za pomocą skryptu centralnego i szablonów JSON, aby wymusić spójność. Zasady nazewnictwa można zaimplementować za pomocą zasad platformy Azure stosowanych na poziomie subskrypcji. W przypadku przykładów JSON dla zasad platformy Azure zobacz [przykłady zasad platformy Azure](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Liczba użytkowników w laboratorium i laboratoriach na organizację

### <a name="question"></a>Pytanie 
Jak określić stosunek użytkowników do laboratorium i ogólną liczbę laboratoriów potrzebnych w organizacji?

### <a name="answer"></a>Odpowiedź
Zaleca się, że jednostki biznesowe i grupy programistyczne, które są skojarzone z tym samym projektem rozwoju są skojarzone z tego samego laboratorium. Umożliwia stosowanie tych samych typów zasad, obrazów i zasad zamknięcia systemu w obu grupach. 

Konieczne może być również rozważenie granic geograficznych. Na przykład deweloperzy w północno-wschodnich Stanach Zjednoczonych (USA) mogą używać laboratorium aprowizacji we wschodnim us2. Deweloperzy w Dallas w Teksasie i Denver w stanie Kolorado mogą zostać skierowani do wykorzystania zasobu w południowo-środkowym usa. Jeśli istnieje wspólny wysiłek z zewnętrzną stroną trzecią, mogą one być przypisane do laboratorium, które nie jest używane przez wewnętrznych deweloperów. 

Można również użyć laboratorium dla określonego projektu w ramach projektów usługi Azure DevOps. Następnie należy zastosować zabezpieczenia za pośrednictwem określonej grupy usługi Azure Active Directory, która umożliwia dostęp do obu zestawów zasobów. Sieć wirtualna przypisana do laboratorium może być inną granicą konsolidacji użytkowników.

## <a name="deletion-of-resources"></a>Usuwanie zasobów

### <a name="question"></a>Pytanie
Jak możemy zapobiec usuwaniu zasobów w laboratorium?

### <a name="answer"></a>Odpowiedź
Zaleca się ustawienie odpowiednich uprawnień na poziomie laboratorium, tak aby tylko autoryzowani użytkownicy mogli usuwać zasoby lub zmieniać zasady laboratorium. Deweloperzy powinni być umieszczane w grupie **Użytkownicy DevTest Labs.** Głównym deweloperem lub potencjalnym klientem infrastruktury powinien być **DevTest Labs Owner**. Zaleca się, że masz tylko dwóch właścicieli laboratorium. Ta zasada rozciąga się na repozytorium kodu, aby uniknąć uszkodzenia. Zastosowania laboratoryjne mają prawa do korzystania z zasobów, ale nie można zaktualizować zasad laboratoryjnych. Zobacz następujący artykuł, który zawiera listę ról i praw, które każda wbudowana grupa ma w laboratorium: [Dodawanie właścicieli i użytkowników w laboratorium DevTest Azure](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Przenoszenie laboratorium do innej grupy zasobów 

### <a name="question"></a>Pytanie
Czy jest obsługiwane przeniesienie laboratorium do innej grupy zasobów?

### <a name="answer"></a>Odpowiedź
Tak. Przejdź do strony Grupa zasobów ze strony głównej laboratorium. Następnie wybierz pozycję **Przenieś** na pasku narzędzi i wybierz laboratorium, które chcesz przenieść do innej grupy zasobów. Podczas tworzenia laboratorium grupa zasobów jest tworzona automatycznie. Można jednak przenieść laboratorium do innej grupy zasobów, która jest zgodna z konwencjami nazewnictwa przedsiębiorstwa. 

## <a name="next-steps"></a>Następne kroki
Zobacz [Zarządzanie kosztami i własnością](devtest-lab-guidance-governance-cost-ownership.md).
