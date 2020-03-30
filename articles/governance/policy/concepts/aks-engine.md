---
title: Poznaj zasady platformy Azure dla aparatu AKS
description: Dowiedz się, jak usługa Azure Policy używa customresourceDefinitions i Open Policy Agent z Gatekeeper w wersji 3 do zarządzania klastrami za pomocą aparatu AKS.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436423"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Poznaj zasady platformy Azure dla aparatu AKS

Usługa Azure Policy integruje się z [usługą AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md)— systemem zapewniającym wygodne narzędzia do szybkiego uruchamiania samodzielnie zarządzanego klastra kubernetes na platformie Azure. Ta integracja umożliwia wymuszanie na dużą skalę i zabezpieczenia w klastrach zarządzanych samodzielnie przez aparat AKS w sposób scentralizowany i spójny. Rozszerzając korzystanie z [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (beta), _webhook kontrolera wstępu_ dla usługi Kubernetes, usługa Azure Policy umożliwia zarządzanie i raportowanie stanu zgodności zasobów platformy Azure i klastrów samodzielnie zarządzanych usługi AKS Engine z jednego miejsca.

> [!NOTE]
> Usługa Azure Policy for AKS Engine jest w publicznej wersji zapoznawczej i nie ma umowy SLA. Gatekeeper v3 jest w wersji beta i jest obsługiwany przez społeczność open source. Usługa obsługuje tylko wbudowane definicje zasad i jeden klaster aparatu AKS dla każdej grupy zasobów skonfigurowanych za pomocą jednostki usługi.

> [!IMPORTANT]
> Aby uzyskać obsługę zasad platformy Azure dla aparatu AKS, aparatu AKS lub gatekeeper v3, utwórz [nowy problem](https://github.com/Azure/aks-engine/issues/new/choose) w repozytorium GitHub aparatu AKS.

## <a name="overview"></a>Omówienie

Aby włączyć i używać usługi Azure Policy for AKS Engine za pomocą samodzielnie zarządzanego klastra Kubernetes na platformie Azure, należy podjąć następujące akcje:

- [Wymagania wstępne](#prerequisites)
- [Instalowanie dodatku zasad platformy Azure](#installing-the-add-on)
- [Przypisywanie definicji zasad dla aparatu AKS](#built-in-policies)
- [Poczekaj na weryfikację](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem dodatku zasad platformy Azure lub włączenie dowolnej funkcji usługi subskrypcja musi włączyć dostawcę zasobów **Microsoft.PolicyInsights** i utworzyć przypisanie roli dla podmiotu usługi klastrowania. 

1. Aby włączyć dostawcę zasobów, wykonaj kroki opisane w [obszarze Dostawcy zasobów i typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lub uruchom polecenie interfejsu wiersza polecenia platformy Azure CLI lub azure PowerShell:

   - Interfejs wiersza polecenia platformy Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Tworzenie przypisania roli dla jednostki usługi klastrowania

   - Jeśli nie znasz identyfikatora głównej aplikacji usługi klastrowania, poszukaj go za pomocą następującego polecenia.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Przypisz przypisanie roli "Moduł zapisujący dane usługi policy insights (wersja zapoznawcza)" do identyfikatora głównej aplikacji usługi klastrowania (wartość _aadClientID_ z poprzedniego kroku) za pomocą interfejsu wiersza polecenia platformy Azure. Zamień `<subscriptionId>` na swój `<aks engine cluster resource group>` identyfikator subskrypcji i z grupą zasobów, w skład grupy zasobów, w skład grupy kubernetes zarządzany przez aparat AKS.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Dodatek usługi Azure Policy

_Dodatek zasad platformy Azure_ dla aplikacji Kubernetes łączy usługę Azure Policy z kontrolerem przyjęć gatekeeper. Dodatek, który jest zainstalowany w obszarze nazw _systemu kube,_ uchwala następujące funkcje:

- Sprawdza za pomocą zasad platformy Azure przydziały do klastra aparatu AKS
- Pobieranie i instalowanie szczegółów zasad, szablonów ograniczeń i ograniczeń
- Uruchamia pełną kontrolę zgodności skanowania w klastrze aparatu AKS
- Raporty inspekcji i szczegóły zgodności z powrotem do zasad platformy Azure

### <a name="installing-the-add-on"></a>Instalowanie dodatku

Po zakończeniu wymagań wstępnych można zainstalować dodatek zasad platformy Azure. Instalacja może być podczas tworzenia lub aktualizacji cyklu aparatu AKS lub jako niezależna akcja w istniejącym klastrze.

- Instalacja podczas tworzenia lub aktualizacji cyklu

  Aby włączyć dodatek zasad platformy Azure podczas tworzenia nowego klastra zarządzanego samodzielnie lub jako aktualizację istniejącego klastra, dołącz definicję klastra właściwości **dodatków** dla aparatu AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Aby uzyskać więcej informacji na temat, zobacz zewnętrzny przewodnik [AKS Engine definicji klastra](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Instalowanie w istniejącym klastrze za pomocą wykresów helm

  Aby przygotować klaster i zainstalować dodatek, należy wykonać następujące czynności:

  1. Zainstaluj strażnika w przestrzeni nazw systemu gatekeeper.Install Gatekeeper to the _gatekeeper-system_ namespace.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Dodaj _etykietę płaszczyzny sterowania_ do _kube-system_. Ta etykieta nie obejmuje inspekcji zasobników i usług _systemu kube_ przez gatekeeper i dodatek zasad platformy Azure.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synchronizuj dane kubernetes (obszar nazw, zasobnik, przychył się, usługa) z opa.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Aby uzyskać więcej informacji, zobacz [OPA — replikowanie danych](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Dodaj repozytorium zasad platformy Azure do helma.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Aby uzyskać więcej informacji, zobacz [Wykres steru - Przewodnik szybki start](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Zainstaluj dodatek za pomocą wykresu helm. Zamień `<subscriptionId>` na swój `<aks engine cluster resource group>` identyfikator subskrypcji i z grupą zasobów, w skład grupy zasobów, w skład grupy kubernetes zarządzany przez aparat AKS.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Aby uzyskać więcej informacji na temat instalowania dodatku Helm Chart, zobacz [definicję wykresu helm dodatku usługi Azure Policy w](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) usłudze GitHub.

     > [!NOTE]
     > Ze względu na relację między dodatkiem usługi Azure Policy a identyfikatorem grupy zasobów usługa Azure Policy obsługuje tylko jeden klaster aparatu AKS dla każdej grupy zasobów.

Aby sprawdzić, czy instalacja dodatku zakończyła się pomyślnie i czy zasobnik _zasad platformy Azure_ jest uruchomiony, uruchom następujące polecenie:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Częstotliwość walidacji i raportowania

Dodatek sprawdza się za pomocą zasad platformy Azure w celu zmiany przydziałów zasad co 5 minut. Podczas tego cyklu odświeżania dodatek sprawdza zmiany. Wyzwalacz tych zmian tworzy, aktualizuje lub usuwa szablony ograniczeń i ograniczenia.

> [!NOTE]
> Administrator _klastra_ może mieć uprawnienia do wprowadzania zmian w szablonach ograniczeń i ograniczeniach, nie jest zalecane ani obsługiwane wprowadzanie zmian w szablonach ograniczeń lub ograniczeniach utworzonych przez usługę Azure Policy. Wszelkie wprowadzone ręczne zmiany zostaną utracone podczas cyklu odświeżania.

Co 5 minut dodatek wymaga pełnego skanowania klastra. Po zebraniu szczegółów pełnego skanowania i wszelkich ocen w czasie rzeczywistym przez gatekeeper prób zmian w klastrze, dodatek raportuje wyniki z powrotem do zasad platformy Azure w celu uwzględnienia [w szczegóły zgodności,](../how-to/get-compliance-data.md) takie jak każde przypisanie zasad platformy Azure. Tylko wyniki dla aktywnych przypisań zasad są zwracane podczas cyklu inspekcji. Wyniki inspekcji mogą być również postrzegane jako naruszenia wymienione w polu stanu ograniczenia nie powiodło się.

## <a name="policy-language"></a>Język polityki

Struktura języka zasad platformy Azure do zarządzania aparatem AKS jest zgodna z istniejącymi zasadami. Efekt _EnforceOPAConstraint_ jest używany do zarządzania klastrami aparatu AKS i pobiera _szczegółowe_ właściwości specyficzne dla pracy z [frameworkiem ograniczeń OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint) i gatekeeper v3. Aby uzyskać szczegółowe informacje i przykłady, zobacz [EnforceOPAConstraint](effects.md#enforceopaconstraint) efekt.

W ramach _details.constraintTemplate_ i _details.constraint_ właściwości w definicji zasad, usługi Azure Policy przekazuje identyfikatory URI tych [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) do dodatku. Rego to język, który opa i gatekeeper wsparcia do sprawdzania poprawności żądania do klastra Kubernetes. Obsługując istniejący standard zarządzania usługą Kubernetes, usługa Azure Policy umożliwia ponowne użycie istniejących reguł i powiązanie ich z zasadami platformy Azure w celu zapewnienia ujednoliconego środowiska raportowania zgodności w chmurze. Aby uzyskać więcej informacji, zobacz [Co to jest Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Wbudowane zasady

Aby znaleźć wbudowane zasady zarządzania klastrem usługi AKS Engine przy użyciu witryny Azure portal, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure portal. Wybierz **pozycję Wszystkie usługi** w lewym okienku, a następnie wyszukaj i wybierz pozycję **Zasady**.

1. W lewym okienku strony Zasady platformy Azure wybierz pozycję **Definicje**.

1. Z listy rozwijanej Kategoria użyj pola **wyboru dla wszystkich,** aby wyczyścić filtr, a następnie wybierz pozycję **Kubernetes**.

1. Wybierz definicję zasad, a następnie wybierz przycisk **Przypisz.**

> [!NOTE]
> Podczas przypisywania definicji usługi Azure Policy dla aparatu AKS **zakres** musi być grupą zasobów klastra aparatu AKS.

Alternatywnie użyj [przypisywania zasad — Szybki](../assign-policy-portal.md) start portalu, aby znaleźć i przypisać zasady aparatu AKS. Wyszukaj definicję zasad aparatu AKS zamiast przykładowych "maszyn wirtualnych inspekcji".

> [!IMPORTANT]
> Wbudowane zasady w kategorii **Kubernetes** są używane tylko z aparatem AKS.

## <a name="logging"></a>Rejestrowanie

### <a name="azure-policy-add-on-logs"></a>Dzienniki dodatków zasad platformy Azure

Jako kontroler/kontener usługi Kubernetes dodatek zasad platformy Azure przechowuje dzienniki w klastrze aparatu AKS.

Aby wyświetlić dzienniki dodatków usługi `kubectl`Azure Policy, należy użyć:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Dzienniki strażnika

Gatekeeper pod, _gatekeeper-controller-manager-0_, jest `gatekeeper-system` `kube-system` zwykle w obszarze nazw lub, ale może być w innej przestrzeni nazw w zależności od sposobu jego wdrożenia.

Aby wyświetlić dzienniki strażnika, należy użyć: `kubectl`

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Aby uzyskać więcej informacji, zobacz [Debugowanie Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) w dokumentacji OPA.

## <a name="remove-the-add-on"></a>Usuwanie dodatku

Aby usunąć dodatek zasad platformy Azure i gatekeeper z klastra aparatu AKS, należy użyć metody, która jest zgodna z sposób instalowany dodatek:

- Jeśli jest zainstalowany przez ustawienie **właściwości addons** w definicji klastra dla aparatu AKS:

  Ponownie rozmieszczaj definicję klastra do aparatu AKS po zmianie właściwości **dodatków** dla _zasad azure_ na false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Jeśli jest zainstalowany z wykresami helm:

  1. Usuwanie starych ograniczeń

     Obecnie mechanizm odinstalowywania usuwa tylko system Gatekeeper, nie usuwa żadnych _zasobów ConstraintTemplate_, _Constraint_ani _Config,_ które zostały utworzone przez użytkownika, ani nie usuwa towarzyszących im _identyfikatorów CRD._

     Gdy gatekeeper jest uruchomiony, można usunąć niechciane ograniczenia przez:

     - Usuwanie wszystkich wystąpień zasobu ograniczenia
     - Usuwanie zasobu _ConstraintTemplate,_ który powinien automatycznie wyczyścić _crd_
     - Usunięcie zasobu _Config_ powoduje usunięcie finalizatorów z synchronizowanych zasobów

  1. Odinstalowywanie dodatku zasad platformy Azure
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Odinstaluj strażnika
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dane diagnostyczne zbierane przez dodatek usługi Azure Policy

Dodatek zasad platformy Azure dla usługi Kubernetes zbiera ograniczone dane diagnostyczne klastra. Te dane diagnostyczne są istotnymi danymi technicznymi związanymi z oprogramowaniem i wydajnością. Jest on używany w następujący sposób:

- Dbaj o aktualną datę dodatku zasad platformy Azure
- Utrzymuj bezpieczne, niezawodne i wydajne dodatki zasad platformy Azure
- Ulepszanie dodatku zasad platformy Azure — dzięki zbiorczej analizie użycia dodatku

Informacje zebrane przez dodatek nie są danymi osobowymi. Obecnie zbierane są następujące szczegóły:

- Wersja dodatku usługi Azure Policy
- Typ klastra
- Region klastra
- Grupa zasobów klastra
- Identyfikator zasobu klastra
- Identyfikator subskrypcji klastra
- System operacyjny klastra (przykład: Linux)
- Miasto klastra (przykład: Seattle)
- Stan lub prowincja klastra (przykład: Waszyngton)
- Kraj lub region klastra (przykład: Stany Zjednoczone)
- Wyjątki/błędy napotkane przez dodatek usługi Azure Policy podczas instalacji agenta podczas oceny zasad
- Liczba zasad strażnika nieinstalowanych przez dodatek usługi Azure Policy

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj [strukturę definicji usługi Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).