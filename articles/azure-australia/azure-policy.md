---
title: Zgodność ze standardami Azure Policy i planami platformy Azure
description: Zapewnienie zgodności i egzekwowanie zabezpieczeń w ramach planów Azure Policy i platformy Azure dla instytucji rządowych Australii w odniesieniu do usług ASD ISM i podstawowych 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571746"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Zgodność ze standardami Azure Policy i planami platformy Azure

Wyzwanie wymuszania ładu w środowisku IT, niezależnie od tego, czy jest to środowisko lokalne, natywne w chmurze czy hybrydowe, istnieje dla wszystkich organizacji. Konieczne jest zapewnienie niezawodnej struktury zarządzania technicznego, aby zapewnić, że środowisko Microsoft Azure jest zgodne z wymaganiami dotyczącymi projektowania, regulacji i zabezpieczeń.

W przypadku australijskich instytucji rządowych, są one kluczowymi kontrolami, które należy wziąć pod uwagę podczas oceniania ryzyka, w [australijskiej cybernetycznymi zabezpieczeń informacji](https://acsc.gov.au/infosec/ism/index.htm) (ISM). Większość kontroli szczegółowych w ramach ISM wymaga, aby stosowanie zarządzania technicznego było skutecznie zarządzane i wymuszane. Ważne jest posiadanie odpowiednich narzędzi do szacowania i wymuszania konfiguracji w swoich środowiskach.

Microsoft Azure oferuje dwie bezpłatne usługi, które ułatwiają rozwiązywanie problemów, Azure Policy i plany platformy Azure.

## <a name="azure-policy"></a>Azure Policy

Azure Policy umożliwia stosowanie elementów technicznych nadzoru IT organizacji. Azure Policy zawiera stale rosnącą bibliotekę wbudowanych zasad. Każda zasada wymusza reguły i efekty dotyczące zasobów platformy Azure.

Po przypisaniu zasad do zasobów można ocenić ogólną zgodność z tymi zasadami i skorygować je w razie potrzeby.

Ta biblioteka wbudowanych zasad platformy Azure umożliwia organizacjom szybkie wymuszanie typów formantów znalezionych w ACSC ISM. Przykłady kontrolek obejmują:

* Monitorowanie maszyn wirtualnych pod kątem brakujących aktualizacji systemu
* Inspekcja kont z podniesionymi uprawnieniami do uwierzytelniania wieloskładnikowego
* Identyfikowanie nieszyfrowanych baz danych SQL
* Monitorowanie użycia niestandardowej kontroli dostępu opartej na rolach (RBAC) na platformie Azure
* Ograniczanie regionów świadczenia usługi Azure, w których można tworzyć zasoby

Jeśli kontrolki ładu lub regulacyjne nie są spełnione przez wbudowaną definicję Azure Policy, można utworzyć i przypisać definicję niestandardową. Wszystkie definicje Azure Policy są zdefiniowane w formacie JSON i są zgodne ze standardową [strukturą definicji](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure). Istniejące definicje Azure Policy mogą być również duplikowane i używane do tworzenia podstaw definicji zasad niestandardowych.

Przypisywanie poszczególnych zasad platformy Azure do zasobów, szczególnie w złożonych środowiskach lub w środowiskach z rygorystycznymi wymaganiami prawnymi, może stworzyć duże obciążenie dla administratorów. Aby pomóc w tym wyzwaniom, zestaw zasad platformy Azure można zgrupować razem w celu utworzenia inicjatywy Azure Policy. Inicjatywy zasad są używane do łączenia powiązanych zasad platformy Azure, które są stosowane razem jako Grupa, stanowią podstawę określonego celu zabezpieczeń lub zgodności. Firma Microsoft dodaje wbudowane definicje inicjatyw Azure Policy, w tym definicje zaprojektowane w celu spełnienia określonych wymagań prawnych:

![Inicjatywy dotyczące zasad zgodności z przepisami](media/regulatory-initiatives.png)

Wszystkie zasady i inicjatywy platformy Azure są przypisywane do zakresu przypisania. Ten zakres jest definiowany w ramach subskrypcji platformy Azure, grupy zarządzania platformy Azure lub poziomów grupy zasobów platformy Azure. Po przypisaniu wymaganych zasad platformy Azure lub inicjatyw zasad organizacja będzie mogła wymuszać wymagania dotyczące konfiguracji dla wszystkich nowo utworzonych zasobów platformy Azure.

Przypisanie nowego Azure Policy lub inicjatywy nie wpłynie na istniejące zasoby platformy Azure. Azure Policy może; jednak umożliwienie organizacji wyświetlania zgodności istniejących zasobów platformy Azure. Wszystkie zasoby, które zostały zidentyfikowane jako niezgodne, mogą zostać skorygowane według uznania organizacji

### <a name="azure-policy-and-initiatives-in-action"></a>Azure Policy i inicjatywy w działaniu

Dostępne wbudowane definicje Azure Policy i inicjatywy można znaleźć w węźle Definicja w sekcji zasady w Azure Portal:

![Wbudowane definicje Azure Policy](media/policy-definitions.png)

Korzystając z biblioteki wbudowanych definicji, można szybko wyszukiwać zasady spełniające wymagania organizacyjne, przeglądać definicję zasad i przypisywać zasady do odpowiednich zasobów. Na przykład ISM wymaga uwierzytelniania wieloskładnikowego (MFA) dla wszystkich użytkowników uprzywilejowanych i dla wszystkich użytkowników mających dostęp do ważnych repozytoriów danych. W Azure Policy można wyszukać "MFA" w definicji Azure Policy:

![Zasady usługi Azure MFA](media/mfa-policies.png)

Po zidentyfikowaniu odpowiednich zasad należy przypisać zasady do żądanego zakresu. Jeśli nie ma żadnych wbudowanych zasad spełniających Twoje wymagania, możesz zduplikować istniejące zasady i wprowadzić wymagane zmiany:

![Zduplikowane istniejące Azure Policy](media/duplicate-policy.png)

Firma Microsoft udostępnia również kolekcję przykładów Azure Policy w witrynie [GitHub](https://github.com/Azure/azure-policy) jako "Szybki Start", które umożliwiają tworzenie niestandardowych zasad platformy Azure. Te przykłady zasad można skopiować bezpośrednio do edytora Azure Policy w Azure Portal.

Podczas tworzenia inicjatyw Azure Policy można sortować listę dostępnych definicji zasad, zarówno wbudowanych, jak i niestandardowych, dodając wymagane definicje.

Na przykład można przeszukać listę dostępnych definicji Azure Policy dla wszystkich zasad związanych z maszynami wirtualnymi z systemem Windows. Następnie te definicje do inicjatywy zaprojektowano w celu wymuszenia zastosowania zalecanych praktyk związanych z ograniczaniem funkcjonalności maszyny wirtualnej:

![Lista zasad platformy Azure](media/initiative-definitions.png)

Podczas przypisywania Azure Policy lub inicjatywy zasad do zakresu przypisania możliwe jest wykluczenie zasobów platformy Azure z efektów tych zasad, wykluczając usługę Azure Grupy zarządzania lub grupy zasobów platformy Azure.

### <a name="real-time-enforcement-and-compliance-assessment"></a>Ocena zgodności i wymuszania w czasie rzeczywistym

Azure Policy skanowania zgodności zasobów platformy Azure w zakresie są podejmowane w przypadku spełnienia następujących warunków:

* Po przypisaniu inicjatywy Azure Policy lub Azure Policy
* Po zmianie zakresu istniejącej Azure Policy lub inicjatywy
* Na żądanie za pośrednictwem interfejsu API do maksymalnie 10 skanów na godzinę
* Co 24 godziny — zachowanie domyślne

Skanowanie pod kątem zgodności z zasadami dla pojedynczego zasobu platformy Azure jest podejmowane 15 minut od wprowadzenia zmiany w zasobie.

Przegląd Azure Policy zgodności zasobów można sprawdzić w ramach Azure Portal za pośrednictwem pulpitu nawigacyjnego zgodność zasad:

![Azure Policy oceny zgodności](media/simple-compliance.png)

Ogólna wartość procentowa zgodności zasobów to zagregowana zgodność wszystkich wdrożonych zasobów w zakresie ze wszystkimi przypisanymi zasadami platformy Azure. Umożliwia to zidentyfikowanie zasobów w środowisku, które nie są zgodne, i opracowanie planu, aby najlepiej skorygować te zasoby.

Pulpit nawigacyjny zgodności zasad zawiera również historię zmian dla każdego zasobu. Jeśli zasób zostanie zidentyfikowany, ponieważ nie jest już zgodny z przypisanymi zasadami, a automatyczne korygowanie nie jest włączone, można zobaczyć, kto wprowadził zmianę, co zostało zmienione i kiedy zmiany zostały wprowadzone do tego zasobu.

## <a name="azure-blueprints"></a>Azure Blueprints

Plany platformy Azure zwiększają możliwości Azure Policy, łącząc je z:

* Kontrola dostępu oparta na rolach platformy Azure
* Grupy zasobów platformy Azure
* [Szablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

Plany umożliwiają tworzenie projektów środowiska służących do wdrażania zasobów platformy Azure z szablonów Menedżer zasobów, konfigurowania RBAC i wymuszania i inspekcji konfiguracji przez przypisanie Azure Policy. Plany tworzą edytowalny i ponownie wdrażany szablon środowiska. Po utworzeniu planu można go następnie przypisać do subskrypcji platformy Azure. Po przypisaniu zostaną utworzone wszystkie zasoby platformy Azure zdefiniowane w planie oraz zastosowane zasady platformy Azure. Wdrożenie i konfiguracja zasobów zdefiniowanych w Azure Blueprint można monitorować z poziomu konsoli Azure Blueprint w Azure Portal.

Edytowane plany platformy Azure należy ponownie opublikować w Azure Portal. Za każdym razem, gdy plan jest ponownie publikowany, numer wersji planu jest zwiększany. Numer wersji umożliwia określenie, która konkretna wersja planu została wdrożona w ramach subskrypcji platformy Azure organizacji. W razie potrzeby można zaktualizować aktualnie przypisaną wersję planu do najnowszej wersji.

Zasoby wdrożone za pomocą Azure Blueprint można skonfigurować przy użyciu [blokad zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) w czasie wdrażania. Blokady zasobów uniemożliwiają przypadkowe modyfikację lub usunięcie zasobów.

Firma Microsoft opracowuje Azure Blueprint szablonów dla różnych branż i wymogów prawnych. Bieżącą bibliotekę dostępnych definicji Azure Blueprint można wyświetlić na stronie Azure Portal lub [strategia zabezpieczeń i zgodności z przepisami platformy Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) w portalu zaufania usługi.

### <a name="azure-blueprint-artifacts"></a>Azure Blueprint artefakty

Aby utworzyć Azure Blueprint, możesz zacząć od pustego szablonu planu lub użyć jednego z istniejących przykładowych planów jako punktu początkowego. Do planu, który zostanie skonfigurowany jako część wdrożenia, można dodać artefakty:

![Azure Blueprint artefakty](media/blueprint-artifacts.png)

Te artefakty mogą obejmować grupę zasobów i zasoby platformy Azure oraz powiązane Azure Policy i inicjatywy dotyczące zasad, aby wymusić zgodność konfiguracji wymaganej przez środowisko zgodnie z wymaganiami prawnymi, na przykład kontrolami ISM do ograniczania funkcjonalności systemu.

Każdy z tych artefaktów można również skonfigurować za pomocą parametrów. Te wartości są udostępniane, gdy plan został przypisany do subskrypcji platformy Azure i wdrożony. Parametry umożliwiają utworzenie jednego planu i użycie go do wdrożenia zasobów w różnych środowiskach bez konieczności edytowania bazowego planu.

Firma Microsoft opracowuje Azure PowerShell i poleceń cmdlet interfejsu wiersza polecenia, aby tworzyć plany platformy Azure i zarządzać nimi z zamiarem, że plan może być konserwowany i wdrażany przez organizację za pośrednictwem potoku ciągłej integracji/ciągłego dostarczania.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak można wymusić zarządzanie i zabezpieczenia przy użyciu planów Azure Policy i platformy Azure. Teraz, gdy masz już więcej informacji, Dowiedz się, jak używać poszczególnych usług w bardziej szczegółowy sposób:

* [Przegląd Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
* [Przegląd planów platformy Azure](https://azure.microsoft.com/services/blueprints/)
* [Przykłady dla usługi Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Repozytorium przykładów Azure Policy](https://github.com/Azure/azure-policy)
* [Struktura definicji Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Efekty Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
