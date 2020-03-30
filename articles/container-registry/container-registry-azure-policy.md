---
title: Zgodność przy użyciu zasad platformy Azure
description: Przypisywanie wbudowanych zasad w zasadach platformy Azure w celu inspekcji zgodności rejestrów kontenerów platformy Azure
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330740"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Zgodność inspekcji rejestrów kontenerów platformy Azure przy użyciu zasad platformy Azure

[Zasady platformy Azure](../governance/policy/overview.md) to usługa na platformie Azure używana do tworzenia, przypisywania i zarządzania zasadami. Te zasady wymuszają różne reguły i efekty dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług.

W tym artykule przedstawiono wbudowane zasady (w wersji zapoznawczej) dla rejestru kontenerów platformy Azure. Użyj tych zasad do inspekcji nowych i istniejących rejestrów pod kątem zgodności.

Korzystanie z zasad platformy Azure nie pobiera żadnych opłat.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="built-in-policy-definitions"></a>Wbudowane definicje zasad

Następujące wbudowane definicje zasad są specyficzne dla rejestru kontenerów platformy Azure:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Zobacz też wbudowaną definicję zasad [sieciowych: [Podgląd] Rejestr kontenerów powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

## <a name="assign-policies"></a>Przypisywanie zasad

* Przypisz zasady przy użyciu [witryny Azure portal,](../governance/policy/assign-policy-portal.md) [interfejsu wiersza polecenia platformy Azure,](../governance/policy/assign-policy-azurecli.md) [szablonu Usługi Resource Manager](../governance/policy/assign-policy-template.md)lub zestawów SDK zasad platformy Azure.
* Zakres przypisania zasad do grupy zasobów, subskrypcji lub [grupy zarządzania platformy Azure](../governance/management-groups/overview.md). Przypisania zasad rejestru kontenerów mają zastosowanie do istniejących i nowych rejestrów kontenerów w zakresie.
* Włącz lub wyłącz [wymuszanie zasad](../governance/policy/concepts/assignment-structure.md#enforcement-mode) w dowolnym momencie.

> [!NOTE]
> Po przypisaniu lub zaktualizowaniu zasad, zajmuje trochę czasu, aby przypisanie do zasobów w zdefiniowanym zakresie. Zobacz informacje o [wyzwalaczach oceny zasad](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Przegląd zgodności zasad

Uzyskaj dostęp do informacji o zgodności generowanych przez przypisania zasad przy użyciu witryny Azure portal, narzędzi wiersza polecenia platformy Azure lub zestawów SDK zasad platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Otrzymywowanie danych dotyczących zgodności zasobów platformy Azure](../governance/policy/how-to/get-compliance-data.md).

Gdy zasób jest niezgodny, istnieje wiele możliwych przyczyn. Aby ustalić przyczynę lub znaleźć odpowiedzialną zmianę, zobacz [Określanie niezgodności](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Zgodność z zasadami w portalu:

1. Wybierz **pozycję Wszystkie usługi**i wyszukaj pozycję **Zasady**.
1. Wybierz **opcję Zgodność**.
1. Użyj filtrów, aby ograniczyć stany ![zgodności lub](./media/container-registry-azure-policy/azure-policy-compliance.png)wyszukać zasady Zgodność zasad w portalu .
1. Wybierz zasady, aby przejrzeć zagregowane szczegóły zgodności i zdarzenia. W razie potrzeby wybierz określony rejestr zgodności zasobów.

### <a name="policy-compliance-in-the-azure-cli"></a>Zgodność zasad w wierszu polecenia platformy Azure

Można również użyć interfejsu wiersza polecenia platformy Azure, aby uzyskać dane zgodności. Na przykład użyj polecenia [listy przypisań zasad az](/cli/azure/policy/assignment#az-policy-assignment-list) w wierszu polecenia polecenia, aby uzyskać identyfikatory zasad zasad zasad usługi Azure Container Registry, które są stosowane:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Przykładowe dane wyjściowe:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Następnie uruchom [listę stanu zasad AZ,](/cli/azure/policy/state#az-policy-state-list) aby zwrócić stan zgodności w formacie JSON dla wszystkich zasobów w określonym identyfikatorze zasad:

```azurecli
az policy state list \
  --resource <policyID>
```

Lub uruchom [listę stanu zasad az,](/cli/azure/policy/state#az-policy-state-list) aby zwrócić stan zgodności w formacie JSON określonego zasobu rejestru, takiego jak *myregistry:*

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [definicjach](../governance/policy/concepts/definition-structure.md) i [efektach](../governance/policy/concepts/effects.md) zasad platformy Azure

* Tworzenie [niestandardowej definicji zasad](../governance/policy/tutorials/create-custom-policy-definition.md)

* Dowiedz się więcej o [możliwościach nadzoru](../governance/index.yml) na platformie Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/