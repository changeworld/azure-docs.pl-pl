---
title: Omówienie zasad platformy Azure
description: Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie definicji zasad oraz zarządzanie nimi w środowisku platformy Azure.
ms.date: 11/25/2019
ms.topic: overview
ms.openlocfilehash: db6a7c592213b0ef8a17466300c37c859e96476b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484023"
---
# <a name="what-is-azure-policy"></a>Co to jest Azure Policy?

Governance validates that your organization can achieve its goals through effective and efficient use of IT. W tym celu jest tworzone jasne połączenie między celami biznesowymi i projektami IT.

Czy w firmie występuje znacząca liczba problemów związanych z IT, które wydają się nie do rozwiązania? Dobre zarządzanie IT obejmuje planowanie inicjatyw i określanie priorytetów na poziomie strategicznym w celu ułatwienia zarządzania i rozwiązywania problemów. Ta strategiczna potrzeba jest realizowana przy użyciu usługi Azure Policy.

Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie zasad oraz zarządzanie nimi. Te zasady wymuszają różne reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług. Usługa Azure Policy spełnia to wymaganie, oceniając zasoby pod kątem niezgodności z przypisanymi zasadami. Może na przykład występować zasada dopuszczająca tylko określony rozmiar jednostki SKU maszyn wirtualnych w środowisku. Po wdrożeniu tej zasady nowe i istniejące zasoby są oceniane pod kątem zgodności. Użycie odpowiedniego typu zasad umożliwia zapewnienie zgodności istniejących zasobów. W dalszej części tej dokumentacji bardziej szczegółowo omówiono tworzenie i implementowanie zasad za pomocą usługi Azure Policy.

> [!IMPORTANT]
> Ocena zgodności w usłudze Azure Policy jest teraz dostępna w przypadku wszystkich przypisań niezależnie od warstwy cenowej. Jeśli przydziały nie pokazują danych zgodności, upewnij się, że subskrypcja została zarejestrowana w obrębie dostawcy zasobów Microsoft.PolicyInsights.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Czym się to różni od RBAC?

There are a few key differences between Azure Policy and role-based access control (RBAC). RBAC koncentruje się na działaniach użytkownika w różnych zakresach. Użytkownik może zostać dodany do roli współautora dla grupy zasobów, aby mógł wprowadzać zmiany w tej grupie zasobów. Azure Policy focuses on resource properties during deployment and for already existing resources. Azure Policy controls properties such as the types or locations of resources. Unlike RBAC, Azure Policy is a default allow and explicit deny system.

### <a name="rbac-permissions-in-azure-policy"></a>Uprawnienia RBAC w usłudze Azure Policy

Usługa Azure Policy ma kilka uprawnień, znanych jako operacje, w ramach dwóch dostawców zasobów:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Wiele wbudowanych ról udziela uprawnień zasobom usługi Azure Policy. The **Resource Policy Contributor** role includes most Azure Policy operations. **Właściciel** ma pełne uprawnienia. Both **Contributor** and **Reader** can use all read Azure Policy operations, but **Contributor** can also trigger remediation.

Jeśli żadna z wbudowanych ról nie ma wymaganych uprawnień, należy utworzyć [rolę niestandardową](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definicja zasad

Proces tworzenia i implementowania zasad w usłudze Azure Policy rozpoczyna się od utworzenia definicji zasad. Każda definicja zasad zawiera warunki, w jakich zasady są wymuszane. Zawiera także zdefiniowany efekt, który występuje w przypadku spełnienia warunków.

Usługa Azure Policy oferuje kilka wbudowanych zasad, które są domyślnie dostępne. Na przykład:

- **Allowed Storage Account SKUs**: Determines if a storage account being deployed is within a set of SKU sizes. Jej efektem jest odrzucanie wszystkich kont magazynu, które nie są zgodne z zestawem zdefiniowanych rozmiarów SKU.
- **Allowed Resource Type**: Defines the resource types that you can deploy. Jej efektem jest odrzucanie wszystkich zasobów, które nie należą do tej zdefiniowanej listy.
- **Allowed Locations**: Restricts the available locations for new resources. Jej efekt jest używany do wymuszania wymagań dotyczących zgodności obszarów geograficznych.
- **Allowed Virtual Machine SKUs**: Specifies a set of virtual machine SKUs that you can deploy.
- **Add a tag to resources**: Applies a required tag and its default value if it's not specified by the deploy request.
- **Enforce tag and its value**: Enforces a required tag and its value to a resource.
- **Not allowed resource types**: Prevents a list of resource types from being deployed.

Aby móc zaimplementować te definicje zasad (wbudowane i niestandardowe), musisz je przypisać. Dowolną z tych zasad można przypisać za pośrednictwem witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Ocena zasad odbywa się przy użyciu kilku różnych akcji, takich jak przypisanie zasad lub aktualizacje zasad. Aby uzyskać pełną listę, zobacz [Wyzwalacze oceny zasad](./how-to/get-compliance-data.md#evaluation-triggers).

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z tematem [Policy Definition Structure](./concepts/definition-structure.md) (Struktura definicji zasad).

## <a name="policy-assignment"></a>Przypisywanie zasad

Przypisywanie zasad to definicja zasad, która została przypisana do określonego zakresu. Zakresem tym może być zarówno [grupa zarządzania](../management-groups/overview.md), jak i grupa zasobów. Termin *zakres* odnosi się do wszystkich grup zasobów, subskrypcji i grup zarządzania, do których przypisano definicję zasad. Przypisania zasad są dziedziczone przez wszystkie zasoby podrzędne. To rozwiązanie oznacza, że zastosowanie zasad do grupy zasobów powoduje zastosowanie ich również do zasobów w tej grupie zasobów. Z przypisania zasad można jednak wyłączyć zakres podrzędny.

Na przykład przy zakresie subskrypcji można określić zasady, które zapobiegają tworzeniu zasobów sieciowych. Można wyłączyć grupę zasobów w ramach subskrypcji, która jest przeznaczona dla infrastruktury sieciowej. Następnie dostęp do tej grupy zasobów sieciowych można przyznać użytkownikom, którym powierzono tworzenie zasobów sieciowych.

In another example, you might want to assign a resource type allow list policy at the management group level. Następnie można przypisać mniej ograniczające zasady (zezwalające na większą liczbę typów zasobów) w podrzędnej grupie zarządzania lub nawet bezpośrednio w subskrypcji. Jednak ten przykład nie działa, ponieważ zasady to system z wyraźnym zabranianiem. Zamiast tego należy wykluczyć podrzędną grupę zarządzania lub subskrypcję z przypisania zasad na poziomie grupy zarządzania. Następnie można przypisać mniej ograniczające zasady na poziomie podrzędnej grupy zarządzania lub subskrypcji. Jeśli w wyniku zasad następuje odmowa zasobu, jedynym sposobem na zezwolenie na zasób jest zmodyfikowanie zasad odmowy.

Dodatkowe informacje na temat ustawiania definicji zasad i przypisań za pomocą portalu zamieszczono w artykule [Tworzenie przypisania zasad w celu identyfikowania niezgodnych zasobów w środowisku platformy Azure](assign-policy-portal.md). Dostępne są również instrukcje dotyczące korzystania z programu [PowerShell](assign-policy-powershell.md) i [interfejsu wiersza polecenia platformy Azure](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parametry zasad

Parametry zasad ułatwiają zarządzanie zasadami przez redukowanie liczby definicji zasad, które należy utworzyć. Podczas tworzenia definicji zasad można zdefiniować parametry, które czynią je bardziej ogólnymi. Następnie można użyć tej definicji zasad ponownie w różnych scenariuszach. Polega to na przekazaniu innych wartości podczas przypisywania definicji zasad. Można na przykład określić jeden zestaw lokalizacji dla subskrypcji.

Parametry są definiowane podczas tworzenia definicji zasad. Jeśli parametr jest zdefiniowany, otrzymuje nazwę i opcjonalnie wartość. Na przykład można zdefiniować parametr dla zasad o nazwie *location*. Następnie podczas przypisywania zasad można przydzielić mu różne wartości, takie jak *EastUS* i *WestUS*.

Dodatkowe informacje na temat parametrów zasad zamieszczono w artykule [Struktura definicji — parametry](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definicja inicjatywy

Definicja inicjatywy to kolekcja definicji zasad dostosowanych w celu osiągnięcia jednego ogólnego celu. Definicje inicjatyw upraszczają przypisywanie definicji zasad i zarządzanie nimi. Upraszczają działania przez grupowanie zestawu zasad w ramach pojedynczego elementu. Można na przykład utworzyć inicjatywę o nazwie **Włączanie monitorowania w Azure Security Center**, której celem jest monitorowanie wszystkich dostępnych zaleceń dotyczących zabezpieczeń w Azure Security Center.

> [!NOTE]
> The SDK, such as Azure CLI and Azure PowerShell, use properties and parameters named **PolicySet** to refer to initiatives.

W ramach tej inicjatywy mogą występować definicje zasad, takie jak:

- **Monitorowanie nieszyfrowanej bazy danych SQL w Security Center**  — do monitorowania niezaszyfrowanych baz danych i serwerów SQL.
- **Monitorowanie luk w zabezpieczeniach systemu operacyjnego w usłudze Security Center** — do monitorowania serwerów, które nie spełniają wymagań skonfigurowanego punktu odniesienia.
- **Monitorowanie brakującej ochrony punktów końcowych w Security Center** — do monitorowania serwerów bez zainstalowanego agenta chroniącego punkty końcowe.

## <a name="initiative-assignment"></a>Przypisanie inicjatywy

Podobnie jak przypisanie zasad, przypisanie inicjatywy to definicja inicjatywy przypisana do określonego zakresu. Przypisania inicjatyw ograniczają potrzebę tworzenia różnych definicji inicjatyw dla każdego zakresu. Zakresem tym również może być zarówno grupa zarządzania, jak i grupa zasobów.

Każdą inicjatywę można przypisać do różnych zakresów. Jedna inicjatywa może być przypisana zarówno do subskrypcji **subscriptionA**, jak i **subscriptionB**.

## <a name="initiative-parameters"></a>Parametry inicjatywy

Podobnie jak parametry zasad, parametry inicjatywy upraszczają zarządzanie inicjatywą przez ograniczenie nadmiarowości. Parametry inicjatywy to parametry używane przez definicje zasad w ramach tej inicjatywy.

Na przykład masz definicję inicjatywy **initiativeC** oraz definicje zasad **policyA** i **policyB**. Każda z nich oczekuje innego typu parametru:

| Zasady | Nazwa parametru |Typ parametru  |Uwaga |
|---|---|---|---|
| policyA | allowedLocations | tablica  |Ten parametr oczekuje listy ciągów dla wartości, ponieważ typ parametru został zdefiniowany jako tablica |
| policyB | allowedSingleLocation |string |Ten parametr oczekuje jednego słowa dla wartości, ponieważ typ parametru został zdefiniowany jako ciąg |

W tym scenariuszu podczas definiowania parametrów inicjatywy **initiativeC** dostępne są trzy opcje:

- Użycie parametrów definicji zasad w ramach tej inicjatywy: w tym przykładzie *allowedLocations* i *allowedSingleLocation* stają się parametrami inicjatywy dla **initiativeC**.
- Przekaż wartości parametrom definicji zasad w ramach tej definicji inicjatywy. In this example, you can provide a list of locations to **policyA's parameter – allowedLocations** and **policyB's parameter – allowedSingleLocation**. Wartości można przekazać również podczas przypisywania tej inicjatywy.
- Podaj listę opcji *value*, które mogą być używane podczas przypisywania tej inicjatywy. Podczas przypisywania tej inicjatywy odziedziczone parametry z definicji zasad w ramach tej inicjatywy mogą zawierać jedynie wartości z tej dostarczonej listy.

W przypadku tworzenia opcji wartości w definicji inicjatywy nie można wprowadzić innej wartości w trakcie przypisywania inicjatywy, ponieważ nie jest ona częścią listy.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximum count of Azure Policy objects

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Zalecenia dotyczące zarządzania zasadami

Poniżej przedstawiono kilka wskazówek i porad, które warto uwzględnić:

- Rozpocznij od efektu inspekcji zamiast efektu odrzucenia, aby śledzić wpływ definicji zasad na zasoby w Twoim środowisku. Jeśli masz już skrypty umożliwiające automatyczne skalowanie swoich aplikacji, skonfigurowanie efektu odrzucenia może negatywnie wpłynąć na zdefiniowane wcześniej zadania automatyzacji.

- Podczas tworzenia definicji i przypisań należy zwrócić uwagę na hierarchie organizacyjne. Firma Microsoft zaleca tworzenie definicji wyższym poziomie, takim jak poziom grupy zarządzania lub subskrypcji. Następnie utwórz przypisanie na następnym poziomie podrzędnym. Jeśli utworzysz definicję na poziomie grupy zarządzania, można ograniczyć zakres przypisania do subskrypcji lub grupy zasobów w tej grupie zarządzania.

- Firma Microsoft zaleca tworzenie i przypisywanie definicji inicjatyw, nawet w przypadku pojedynczych definicji zasad.
  Na przykład możesz mieć definicję zasad *policyDefA* utworzoną w ramach definicji inicjatywy *initiativeDefC*. Jeśli zdecydujesz się na utworzenie w późniejszym czasie kolejnej definicji zasad *policyDefB* o celach podobnych do *policyDefA*, możesz dodać ją do definicji *initiativeDefC* i śledzić je razem.

- Po utworzeniu przypisania inicjatywy definicje zasad dodane do inicjatywy również stają się częścią przypisań tej inicjatywy.

- Podczas oceny przypisania inicjatywy oceniane są również wszystkie zasady w ramach tej inicjatywy.
  Jeśli konieczne jest indywidualne ocenianie zasad, lepszym rozwiązaniem jest nieuwzględnianie ich w inicjatywie.

## <a name="video-overview"></a>Omówienie wideo

Poniższe omówienie usługi Azure Policy dotyczy kompilacji 2018. Aby pobrać slajdy lub klip wideo, odwiedź stronę [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Zarządzanie środowiskiem platformy Azure przy użyciu usługi Azure Policy) w witrynie Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już podstawowe informacje na temat usługi Azure Policy i kluczowych pojęć, oto zalecane kolejne kroki:

- [Assign a policy definition using the portal](./assign-policy-portal.md).
- [Assign a policy definition using the Azure CLI](./assign-policy-azurecli.md).
- [Assign a policy definition using PowerShell](./assign-policy-powershell.md).