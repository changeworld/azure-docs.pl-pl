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
ms.openlocfilehash: c5514a43602106cf045b575d289e02b591468359
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561665"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Zarządzanie infrastrukturą usługi Azure DevTest Labs — zasoby
W tym artykule opisano, wyrównanie i zarządzanie zasobami dla laboratorium w Twojej organizacji. 

## <a name="align-within-an-azure-subscription"></a>Wyrównywanie w ramach subskrypcji platformy Azure 

### <a name="question"></a>Pytanie
Jak dopasować DevTest Labs zasobów w ramach subskrypcji platformy Azure?

### <a name="answer"></a>Odpowiedź
Przed rozpoczęciem organizacji za pomocą platformy Azure do tworzenia aplikacji głównej, oceniający IT powinny najpierw Dowiedz się, jak wprowadzenie możliwości w ramach ich ogólną portfolio usług. Obszary do przeglądu należy zająć się następujące kwestie:

- Jak zmierzyć ponoszenia kosztów związanych z cyklem życia tworzenia aplikacji?
- Jak organizacja dopasować usługę proponowane oferty z zasadami zabezpieczeń firmy? 
- Jest wymagany do oddzielnych środowisk deweloperskich i produkcyjnych segmentacji? 
- Formanty, które zostały wprowadzone dla długoterminowego łatwość zarządzania, stabilności i wzrostu?

**Najpierw zalecana praktyka** jest Przegląd taksonomii platformy Azure organizacji gdzie omówiono podziałów między środowiskami produkcyjnym i tworzenia subskrypcji. Na poniższym diagramie sugerowane taksonomii umożliwia separacji logicznej środowisk projektowania i testowania i produkcji. W przypadku tej metody organizacja może wprowadzić kody rozliczeń śledzenie kosztów skojarzonych z każdym środowisku oddzielnie. Aby uzyskać więcej informacji, zobacz [narzucony nadzór subskrypcji](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Ponadto można użyć [Azure tagi](../azure-resource-manager/resource-group-using-tags.md) porządkowanie zasobów na potrzeby śledzenia i rozliczania.

**Drugi zalecana praktyka** jest umożliwienie DevTest subskrypcji w portalu Azure Enterprise. Umożliwia ona organizacjom systemami operacyjnymi klienta, które nie są zwykle dostępne w ramach subskrypcji platformy Azure w przedsiębiorstwach. Następnie należy użyć oprogramowania korporacyjnego, w którym płacisz tylko za zasoby obliczeniowe i nie nie martw się o licencjonowaniu. Zapewnia, że opłata wyznaczonym usług, w tym galerii obrazów w modelu IaaS, takich jak Microsoft SQL Server, opiera się na użycie tylko. Można znaleźć szczegółowe informacje o subskrypcji usługi Azure DevTest [tutaj](https://azure.microsoft.com/offers/ms-azr-0148p/) dla klientów z umową Enterprise Agreement (EA) i [tutaj](https://azure.microsoft.com/offers/ms-azr-0023p/) dla klientów, płatność zgodnie z rzeczywistym użyciem.

![Wyrównanie zasobów z subskrypcji](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Ten model zawiera organizacji elastyczność umożliwiającą wdrażanie usługi Azure DevTest Labs na dużą skalę. Organizacja może obsługiwać setki labs różnych jednostek biznesowych przy użyciu 100 do 1000 maszyn wirtualnych uruchamianych równolegle. Jego promuje pojęcie to rozwiązanie laboratorium scentralizowane przedsiębiorstwa, które mogą udostępniać te same zasady zarządzania konfiguracją i opcji zabezpieczeń.

Ten model gwarantuje również, że organizacja nie wyczerpują swoje limity zasobów skojarzonych z subskrypcją platformy Azure. Aby uzyskać szczegółowe informacje o limity usług i subskrypcji, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md). DevTest Labs, proces tworzenia może zużywać dużą liczbę grup zasobów. Możesz poprosić limity można zwiększyć za pośrednictwem żądania pomocy technicznej w ramach subskrypcji usługi Azure DevTest. Zasoby w ramach subskrypcji w środowisku produkcyjnym nie dotyczy wzrostem subskrypcji rozwoju w użyciu. Aby uzyskać więcej informacji na temat skalowania usługi DevTest Labs, zobacz [skalowanie limity przydziału i ograniczenia w usłudze DevTest Labs](devtest-lab-scale-lab.md).

Typowe poziomu limit subskrypcji, która musi zostać uwzględnione jest jak przypisań zakresu adresów IP sieci są przydzielane do obsługi środowiska produkcyjnego i tworzenia subskrypcji. Te przypisania należy uwzględnić na rozwój wraz z upływem czasu (w przypadku połączeń lokalnych lub innej sieci topologię, która wymaga zarządzanie ich stosu sieciowego, zamiast używać domyślnej implementacji platformy Azure w przedsiębiorstwie). Zalecaną praktyką jest, a mają kilka sieci wirtualne, które mają duże prefiksu adresu IP przypisane i podzielona przez wiele podsieci dużych nie mają wiele sieci wirtualnych o małych podsieci. Na przykład 10 subskrypcji można zdefiniować 10 sieciami wirtualnymi (po jednym dla każdej subskrypcji). Wszystkie warsztaty, które nie wymagają izolacji można udostępnić tej samej podsieci w sieci wirtualnej dla subskrypcji.

## <a name="maintain-naming-conventions"></a>Obsługa konwencji nazewnictwa

### <a name="question"></a>Pytanie
Jak zachować konwencję nazewnictwa w całej mojego środowiska DevTest Labs?

### <a name="answer"></a>Odpowiedź
Możesz chcieć rozszerzanie bieżących konwencjach nazewnictwa przedsiębiorstwa do operacji na platformie Azure, dzięki czemu będą zgodne w środowisku laboratorium.

Podczas wdrażania usługi DevTest Labs, zaleca się, że masz określone zasady począwszy od. Możesz wdrożyć te zasady przez centralną skryptów i szablonów JSON, aby wymusić spójność. Nadawanie zasadom nazw może być implementowany przez zasady usługi Azure stosowane na poziomie subskrypcji. Aby uzyskać przykłady kodu JSON usługi Azure Policy, zobacz [przykładów usługi Azure Policy](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Liczba użytkowników na laboratorium i laboratoriów w ramach organizacji

### <a name="question"></a>Pytanie 
Jak określić współczynnik użytkowników na laboratorium oraz ogólną liczbę labs potrzebnych w organizacji?

### <a name="answer"></a>Odpowiedź
Zaleca się, że jednostek biznesowych i grupy deweloperów, które są skojarzone z tym samym projekcie rozwoju są skojarzone z tym samym środowisku laboratoryjnym. Umożliwia ona te same typy zasad, obrazy i zamykania zasad mają być stosowane do obu grup. 

Również może być konieczne należy rozważyć granicach geograficznych. Na przykład deweloperzy we wschodnim regionie Północ Stanów Zjednoczonych (US) mogą używać laboratorium aprowizowane w wschodnie stany USA 2. Ponadto deweloperzy w Dallas, Teksas i Warszawie mogą być kierowani do użycia zasobu w południowo-środkowych stanów USA. W przypadku wspólnego nakładu pracy z firm zewnętrznych, można można przypisać do laboratorium, który nie jest używany przez deweloperów wewnętrznego. 

Możesz także użyć laboratorium dla określonego projektu w obrębie projektów DevOps platformy Azure. Następnie możesz zastosować zabezpieczeń przy użyciu określonej grupy usługi Azure Active Directory, która zezwala na dostęp do obu zestawów zasobów. Sieci wirtualnej przypisany do laboratorium może być inny granic skonsolidować użytkowników.

## <a name="deletion-of-resources"></a>Usuwanie zasobów

### <a name="question"></a>Pytanie
Jak można zapobiec usuwania zasobów w ramach laboratorium?

### <a name="answer"></a>Odpowiedź
Firma Microsoft zaleca, ustawić odpowiednie uprawnienia na poziomie laboratorium, tak aby tylko autoryzowani użytkownicy mogą usunąć zasoby lub zmienić zasady laboratorium. Deweloperzy powinny zostać umieszczone w ramach **DevTest Labs użytkowników** grupy. Developer potencjalny klient lub kierownik infrastruktury powinny być **DevTest Labs właściciela**. Zaleca się, że masz tylko dwóch właścicieli laboratorium. Ta zasada rozszerza kierunku repozytorium kodu, aby uniknąć uszkodzenia. Zastosowań laboratorium ma prawa do korzystania z zasobów, ale nie można zaktualizować zasad laboratorium. Zobacz następujący artykuł, który wyświetla listę ról i uprawnień, które każda grupa wbudowana ma w ramach laboratorium: [Dodawanie właścicieli i użytkowników w usłudze Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Przenieś laboratorium do innej grupy zasobów 

### <a name="question"></a>Pytanie
Przenieś laboratorium do innej grupy zasobów jest obsługiwana?

### <a name="answer"></a>Odpowiedź
Tak. Przejdź do strony grupy zasobów, z poziomu strony głównej dla swojego laboratorium. Następnie wybierz **przenieść** na pasku narzędzi i wybierz laboratorium, którą chcesz przenieść do innej grupy zasobów. Po utworzeniu laboratorium, grupa zasobów jest tworzone automatycznie dla Ciebie. Jednak można przenieść laboratorium do innej grupy zasobów, która jest zgodna z konwencjami nazewnictwa przedsiębiorstwa. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz [Zarządzanie kosztami i własność](devtest-lab-guidance-governance-cost-ownership.md).
