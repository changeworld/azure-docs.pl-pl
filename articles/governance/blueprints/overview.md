---
title: Omówienie usługi Azure Blueprints
description: Azure Blueprints to usługa platformy Azure, która umożliwia tworzenie, definiowanie i wdrażanie artefaktów w środowisku platformy Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: c7ffbe86407bc776870890e5b4151556f572832e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957517"
---
# <a name="what-is-azure-blueprints"></a>Czym jest usługa Azure Blueprints?

Usługę Azure Blueprints można porównać do planu, który pozwala inżynierowi lub architektowi naszkicować parametry projektu. Usługa ta umożliwia architektom chmury i centralnemu działowi IT zdefiniowanie powtarzalnego zestawu zasobów platformy Azure, który implementuje standardy, wzorce i wymagania organizacji oraz jest z nimi zgodny. Usługa Azure Blueprints umożliwia zespołom programistów szybkie aprowizowanie i wdrażanie nowych środowisk z przekonaniem, że zostały one utworzone zgodnie z zasadami organizacji oraz że zawierają zestaw wbudowanych składników — takich jak sieć — przyspieszających opracowywanie i dostarczanie.

Usługa Blueprints umożliwia deklaratywne organizowanie i wdrażanie wielu szablonów zasobów i innych artefaktów, takich jak:

- Przypisania ról
- Przypisania zasad
- Szablony usługi Azure Resource Manager
- Grupy zasobów

## <a name="how-it-is-different-from-resource-manager-templates"></a>W czym różni się ona od szablonów usługi Resource Manager

Usługa Blueprints została zaprojektowana w celu usprawnienia procesu _konfiguracji środowiska_, który oprócz wdrożeń szablonów usługi Resource Manager często zawiera zestaw grup zasobów, zasad i przypisań ról. Strategia usługi Blueprints to pakiet umożliwiający połączenie każdego z tych typów _artefaktów_ w jedną całość. Pakiety można tworzyć i kontrolować ich wersje — w tym za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania. Każdy pakiet jest ostatecznie przypisywany do subskrypcji w ramach jednej operacji, którą można poddawać inspekcji i śledzić.

Prawie wszystkie elementy możliwe do uwzględnienia podczas wdrażania za pomocą usługi Blueprints można wdrożyć przy użyciu szablonów usługi Resource Manager. Szablony usługi Resource Manager są jednak dokumentami, które nie istnieją natywnie na platformie Azure — każdy z nich jest przechowywany lokalnie lub w kontroli źródła. Szablon jest używany na potrzeby wdrożenia jednego lub większej liczby zasobów platformy Azure, ale po wdrożeniu zasobów połączenie z wykorzystanym szablonem i relacja z nim zostają utracone.

Dzięki użyciu usługi Blueprints relacja między definicją strategii (_to, co powinno zostać_ wdrożone) i przypisaniem strategii (_to, co zostało_ wdrożone) pozostaje niezmieniona. To połączenie umożliwia ulepszone śledzenie i inspekcję wdrożeń, możliwość jednoczesnego uaktualnienia wielu subskrypcji zarządzanych przez tę samą strategię i nie tylko.

Nie ma potrzeby dokonywania wyboru między szablonem usługi Resource Manager i strategią. Każda strategia może zawierać dowolną liczbę _artefaktów_ szablonu usługi Resource Manager. Oznacza to, że podejmowane poprzednio wysiłki mające na celu utworzenie i utrzymanie biblioteki szablonów usługi Resource Manager mogą być teraz wykorzystane w usłudze Blueprints.

## <a name="how-it-is-different-from-azure-policy"></a>W czym różni się ona od usługi Azure Policy

Strategia to pakiet lub kontener umożliwiający tworzenie specyficznych dla kontekstu zestawów standardów, wzorców i wymagań dotyczących implementacji usług Azure Cloud Services, zabezpieczeń i projektów, które mogą zostać ponownie użyte w celu zapewnienia spójności i zgodności.

[Zasady](../policy/overview.md) to system z domyślnym zezwalaniem i wyraźnym zabranianiem, który jest skoncentrowany na właściwościach wdrażanych i istniejących już zasobów. Obsługuje on zarządzanie IT poprzez zapewnienie, że zasoby w ramach subskrypcji są zgodne z wymaganiami i standardami.

Uwzględnienie zasad w strategii umożliwia nie tylko tworzenie odpowiedniego wzorca lub projektu podczas przypisywania strategii, ale również zapewnia, że w środowisku mogą być dokonywane tylko zatwierdzone lub oczekiwane zmiany gwarantujące przyszłą zgodność z intencją strategii.

Zasady mogą być dołączane jako jedne z wielu _artefaktów_ w definicji strategii. Strategie obsługują również używanie parametrów z zasadami i inicjatywami.

## <a name="blueprint-definition"></a>Definicja strategii

Strategia składa się z _artefaktów_. Usługa Blueprints obsługuje obecnie następujące zasoby jako artefakty:

|Zasób  | Opcje hierarchii| Opis  |
|---------|---------|---------|
|Grupy zasobów     | Subskrypcja | Umożliwia utworzenie nowej grupy zasobów do użytku przez inne artefakty w ramach strategii.  Te zastępcze grupy zasobów umożliwiają organizowanie zasobów dokładnie w taką strukturę, jaka jest pożądana. Udostępniają one ogranicznik zakresu na potrzeby uwzględnionych zasad oraz artefakty przypisania roli, jak również szablony usługi Azure Resource Manager.         |
|Szablon usługi Azure Resource Manager      | Grupa zasobów | Te szablony mogą służyć do tworzenia złożonych środowisk, takich jak farma programu SharePoint, konfiguracja stanu usługi Azure Automation lub obszar roboczy usługi Log Analytics. |
|Przypisanie zasad     | Subskrypcja, grupa zasobów | Umożliwia przypisanie zasad lub inicjatywy do grupy zarządzania bądź subskrypcji, do której przypisano strategię. Zasady lub inicjatywa musi zawierać się w zakresie strategii (w grupie zarządzania strategii lub poniżej niej). Jeśli zasady lub inicjatywa ma parametry, można je przypisać podczas tworzenia strategii bądź podczas jej przypisywania.       |
|Przypisanie roli   | Subskrypcja, grupa zasobów | Dodawanie istniejącego użytkownika lub grupy do wbudowanej roli w celu zagwarantowania, że odpowiednie osoby zawsze będą mieć odpowiedni dostęp do zasobów. Przypisania ról mogą być definiowane dla całej subskrypcji lub mogą być zagnieżdżone w konkretnej grupie zasobów uwzględnionej w strategii. |

### <a name="blueprints-and-management-groups"></a>Strategie i grupy zarządzania

Podczas tworzenia definicji strategii należy zdefiniować miejsce, w którym strategia zostanie zapisana. Obecnie strategie można zapisywać tylko w [grupie zarządzania](../management-groups/overview.md), do której użytkownik ma dostęp jako **Współautor**. Strategia będzie wtedy mogła zostać przypisana do dowolnego elementu podrzędnego (grupy zarządzania lub subskrypcji) tej grupy zarządzania.

> [!IMPORTANT]
> Jeśli nie masz dostępu do żadnej grupy zarządzania ani do żadnej skonfigurowanej grupy zarządzania, po załadowaniu listy definicji strategii okaże się, że żadna grupa nie jest dostępna i kliknięcie pozycji **Zakres** spowoduje otwarcie okna z ostrzeżeniem o pobieraniu grup zarządzania. Aby rozwiązać ten problem, upewnij się, że subskrypcja, do której masz odpowiedni dostęp, jest częścią [grupy zarządzania](../management-groups/overview.md).

### <a name="blueprint-parameters"></a>Parametry strategii

Usługa Blueprints może przekazywać parametry do zasad/inicjatywy lub szablonu usługi Azure Resource Manager.
Gdy dowolny _artefakt_ jest dodawany do strategii, autor może zdecydować o udostępnieniu zdefiniowanej wartości dla każdego przypisania strategii lub zezwoleniu, aby każde przypisanie strategii udostępniało wartość w czasie przypisywania. Dzięki tej elastyczności można zdefiniować wstępnie ustaloną wartość dla wszystkich zastosowań strategii lub umożliwić podjęcie decyzji w czasie przypisywania.

> [!NOTE]
> Strategia może mieć własne parametry, ale obecnie można je tworzyć tylko w przypadku, kiedy strategia jest generowana w interfejsie API REST, a nie za pomocą portalu.

Aby uzyskać więcej informacji, zobacz [parametry strategii](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publikowanie strategii

Kiedy strategia jest tworzona po raz pierwszy, przyjmuje się, że jest w trybie **wersji roboczej**. Kiedy jest gotowa do przypisania, należy ją **opublikować**. Publikowanie wymaga zdefiniowania ciągu **wersji** (liter, cyfr i łączników o maksymalnej długości 20 znaków) wraz z opcjonalnymi **uwagami dotyczącymi zmian**.
Dzięki zastosowaniu **wersji** przyszłe zmiany wprowadzone w strategii mogą być traktowane jako osobne wersje z możliwością ich przypisania. Oznacza to również, że różne **wersje** tej samej strategii można przypisać do jednej subskrypcji. Jeśli w strategii zostaną dokonane dodatkowe zmiany, **opublikowana** **wersja** będzie nadal istniała niezależnie od **nieopublikowanych zmian**. Po zakończeniu wprowadzania zmian zaktualizowana strategia jest **publikowana** jako nowa unikatowa **wersja**, którą teraz również można przypisać.

## <a name="blueprint-assignment"></a>Przypisywanie strategii

Każdą **opublikowaną** **wersję** strategii można przypisać do istniejącej subskrypcji. W portalu domyślną **wersją** strategii jest ta, która została **opublikowana** jako ostatnia. Jeśli istnieją parametry artefaktów (lub parametry strategii), te parametry zostaną zdefiniowane w procesie przypisania.

## <a name="permissions-in-azure-blueprints"></a>Uprawnienia w usłudze Azure Blueprints

Aby móc korzystać ze strategii, użytkownik musi być autoryzowany za pośrednictwem [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md). Aby użytkownik mógł tworzyć strategie, jego konto musi mieć następujące uprawnienia:

- `Microsoft.Blueprint/blueprints/write` — tworzenie definicji strategii
- `Microsoft.Blueprint/blueprints/artifacts/write` — tworzenie artefaktów w definicji strategii
- `Microsoft.Blueprint/blueprints/versions/write` — publikowanie strategii

Aby użytkownik mógł usuwać strategie, jego konto musi mieć następujące uprawnienia:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Ponieważ definicje strategii są tworzone w grupie zarządzania, uprawnienia definicji strategii muszą zostać przyznane w zakresie grupy zarządzania lub muszą być dziedziczone w zakresie grupy zarządzania.

Aby użytkownik mógł przypisywać strategie lub anulować ich przypisanie, jego konto musi mieć następujące uprawnienia:

- `Microsoft.Blueprint/blueprintAssignments/write` — przypisywanie strategii
- `Microsoft.Blueprint/blueprintAssignments/delete` — anulowanie przypisania strategii

> [!NOTE]
> Przypisania strategii są tworzone w ramach subskrypcji, dlatego uprawnienia przypisywania strategii lub anulowania takiego przypisania muszą zostać przyznane w zakresie subskrypcji bądź muszą być dziedziczone w zakresie subskrypcji.

Te uprawnienia są zawarte w roli **Właściciel** oraz, z wyjątkiem uprawnień do przypisywania strategii, są również zawarte w rolach **Współautor**. Jeśli te wbudowane role nie spełniają wymagań dotyczących zabezpieczeń, należy rozważyć utworzenie [roli niestandardowej](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Jednostka usługi Azure Blueprint wymaga roli **Właściciel** dla przypisanej subskrypcji, aby można było włączyć wdrażanie. W przypadku korzystania z portalu ta rola jest automatycznie przyznawana i odwoływana dla wdrożenia. W przypadku korzystania z interfejsu API REST ta rola musi zostać ręcznie przyznana, ale jest automatycznie odwoływana po zakończeniu wdrożenia.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie strategii — portal](create-blueprint-portal.md)
- [Tworzenie strategii — interfejs API REST](create-blueprint-rest-api.md)