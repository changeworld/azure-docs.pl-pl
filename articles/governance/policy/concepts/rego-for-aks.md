---
title: Poznaj zasady platformy Azure dla usługi Azure Kubernetes
description: Dowiedz się, jak usługa Azure Policy używa usług Rego i Open Policy Agent do zarządzania klastrami w usłudze Azure Kubernetes.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372655"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Omówienie usługi Azure Policy dla usługi Azure Kubernetes Service

Usługa Azure Policy integruje się z [usługą Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) w celu zastosowania wymieńków i zabezpieczeń na dużą skalę w klastrach w sposób scentralizowany i spójny.
Rozszerzając użycie [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, _webhook kontrolera przyjęć_ dla [open policy agent](https://www.openpolicyagent.org/) (OPA), zasady platformy Azure umożliwia zarządzanie i raportowanie stanu zgodności zasobów platformy Azure i klastrów AKS z jednego miejsca.

> [!IMPORTANT]
> Usługa Azure Policy for AKS jest dostępna w wersji Zapoznawczej i obsługuje tylko wbudowane definicje zasad. Wbudowane zasady znajdują się w kategorii **Kubernetes.** **Egzekwuje** efekt polityki i powiązane zasady kategorii **usługi Kubernetes** są _przestarzałe_. Zamiast tego należy użyć zaktualizowanego [enforceopaconstraint](./effects.md#enforceopaconstraint) efekt.

> [!WARNING]
> Ta funkcja nie jest jeszcze dostępna we wszystkich regionach. Aby uzyskać stan wdrożenia, zobacz [Problemy z AKS — Zmiana podziału na dodatek zasad](https://github.com/Azure/AKS/issues/1529).

## <a name="overview"></a>Omówienie

Aby włączyć i używać usługi Azure Policy dla usługi AKS z klastrem AKS, należy podjąć następujące akcje:

- [Włączanie funkcji w wersji zapoznawczej](#opt-in-for-preview)
- [Instalowanie dodatku zasad platformy Azure](#installation-steps)
- [Przypisywanie definicji zasad dla aks](#built-in-policies)
- [Poczekaj na weryfikację](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Zapisz się do wersji zapoznawczej

Przed zainstalowaniem dodatku zasad platformy Azure lub włączenie dowolnej funkcji usługi subskrypcja musi włączyć dostawcę zasobów **Microsoft.ContainerService** i dostawcę zasobów **Microsoft.PolicyInsights,** a następnie uzyskać zgodę na dołączenie do wersji zapoznawczej. Aby dołączyć do wersji zapoznawczej, wykonaj następujące kroki w witrynie Azure portal lub za pomocą interfejsu wiersza polecenia platformy Azure:

- Azure Portal:

  1. Zarejestruj dostawców zasobów **Microsoft.ContainerService** i **Microsoft.PolicyInsights.** Aby uzyskać instrukcje, zobacz [Dostawcy zasobów i typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

     ![Wyszukiwanie zasad we wszystkich usługach](../media/rego-for-aks/search-policy.png)

  1. Wybierz **pozycję Dołącz podgląd** po lewej stronie strony Zasady platformy Azure.

     ![Dołącz do zasad dotyczących wersji zapoznawczej AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Wybierz wiersz subskrypcji, którą chcesz dodać do wersji zapoznawczej.

  1. Wybierz przycisk **Opt-in** u góry listy subskrypcji.

- Interfejsu wiersza polecenia platformy Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Dodatek usługi Azure Policy

_Dodatek zasad platformy Azure_ dla aplikacji Kubernetes łączy usługę Azure Policy z kontrolerem przyjęć gatekeeper. Dodatek, który jest zainstalowany w obszarze nazw _systemu kube,_ uchwala następujące funkcje:

- Sprawdza za pomocą usługi Azure Policy przydziały do klastra.
- Wdraża zasady w klastrze jako [szablon ograniczeń](https://github.com/open-policy-agent/gatekeeper#constraint-templates) i zasoby niestandardowe [ograniczeń.](https://github.com/open-policy-agent/gatekeeper#constraints)
- Raporty inspekcji i szczegóły zgodności z powrotem do usługi Azure Policy.

### <a name="installing-the-add-on"></a>Instalowanie dodatku

#### <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem dodatku w klastrze AKS należy zainstalować rozszerzenie wersji zapoznawczej. Ten krok jest wykonywany za pomocą interfejsu wiersza polecenia platformy Azure:

1. Jeśli zasady Gatekeeper v2 zostały zainstalowane, usuń dodatek za pomocą przycisku **Wyłącz** w klastrze AKS na stronie **Zasady (wersja zapoznawcza).**

1. Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.62 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Klaster AKS musi mieć wersję _1.14_ lub nowszą. Aby sprawdzić poprawność wersji klastra usługi AKS, użyj następującego skryptu:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Zainstaluj wersję _0.4.0_ rozszerzenia podglądu interfejsu wiersza polecenia platformy Azure dla usługi AKS: `aks-preview`

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Jeśli wcześniej zainstalowano rozszerzenie _aks-preview,_ zainstaluj wszystkie `az extension update --name aks-preview` aktualizacje za pomocą polecenia.

#### <a name="installation-steps"></a>Kroki instalacji

Po zakończeniu wymagań wstępnych zainstaluj dodatek zasad platformy Azure w klastrze AKS, który chcesz zarządzać.

- Portal Azure

  1. Uruchom usługę AKS w witrynie Azure portal, klikając **pozycję Wszystkie usługi,** a następnie wyszukując i wybierając **pozycję Usługi Kubernetes.**

  1. Wybierz jeden z klastrów AKS.

  1. Wybierz **pozycję Zasady (wersja zapoznawcza)** po lewej stronie strony usługi Kubernetes.

     ![Zasady z klastra AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na stronie głównej wybierz przycisk **Włącz dodatek.**

     ![Włączanie zasad platformy Azure dla dodatku AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Jeśli przycisk **Włącz dodatek** jest wyszarzony, subskrypcja nie została jeszcze dodana do wersji zapoznawczej. Aby uzyskać [w wersji zapoznawczej,](#opt-in-for-preview) zobacz Podglądu wymaganych kroków. Jeśli przycisk **Wyłącz** jest dostępny, Gatekeeper v2 jest nadal zainstalowany i musi zostać usunięty.

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Częstotliwość walidacji i raportowania

Dodatek sprawdza się za pomocą usługi Azure Policy w celu zmiany przydziałów zasad co 15 minut.
Podczas tego cyklu odświeżania dodatek sprawdza zmiany. Wyzwalacz tych zmian tworzy, aktualizuje lub usuwa szablony ograniczeń i ograniczenia.

> [!NOTE]
> Podczas gdy administrator klastra może mieć uprawnienia do tworzenia i aktualizowania szablonów ograniczeń i zasobów ograniczeń, nie są one obsługiwane scenariusze, ponieważ aktualizacje ręczne zostaną zastąpione.

Co 15 minut dodatek wymaga pełnego skanowania klastra. Po zebraniu szczegółów pełnego skanowania i wszelkich ocen w czasie rzeczywistym przez gatekeeper prób zmian w klastrze, dodatek raportuje wyniki z powrotem do usługi Azure Policy w celu uwzględnienia [szczegółów zgodności,](../how-to/get-compliance-data.md#portal) takich jak każde przypisanie zasad platformy Azure. Tylko wyniki dla aktywnych przypisań zasad są zwracane podczas cyklu inspekcji. Wyniki inspekcji mogą być również postrzegane jako [naruszenia](https://github.com/open-policy-agent/gatekeeper#audit) wymienione w polu stanu ograniczenia nie powiodło się.

## <a name="policy-language"></a>Język polityki

Struktura języka zasad platformy Azure do zarządzania kubernetes wynika z istniejących zasad. Efekt _EnforceOPAConstraint_ jest używany do zarządzania klastrami Kubernetes i pobiera szczegółowe właściwości specyficzne dla pracy z [frameworkiem ograniczeń OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint) i gatekeeperem w wersji 3. Aby uzyskać szczegółowe informacje i przykłady, zobacz [EnforceOPAConstraint](./effects.md#enforceopaconstraint) efekt.
  
W ramach _details.constraintTemplate_ i _details.constraint_ właściwości w definicji zasad, usługi Azure Policy przekazuje identyfikatory URI tych [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) do dodatku. Rego to język, który opa i gatekeeper wsparcia do sprawdzania poprawności żądania do klastra Kubernetes. Obsługując istniejący standard zarządzania usługą Kubernetes, usługa Azure Policy umożliwia ponowne użycie istniejących reguł i powiązanie ich z zasadami platformy Azure w celu zapewnienia ujednoliconego środowiska raportowania zgodności w chmurze. Aby uzyskać więcej informacji, zobacz [Co to jest Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Wbudowane zasady

Aby znaleźć wbudowane zasady zarządzania klastrem przy użyciu witryny Azure Portal, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure portal. Wybierz pozycję Wszystkie usługi w lewym okienku, a następnie wyszukaj i wybierz pozycję **Zasady**.

1. W lewym okienku strony Zasady platformy Azure wybierz pozycję **Definicje**.

1. Z listy rozwijanej Kategoria użyj pola wyboru dla wszystkich, aby wyczyścić filtr, a następnie wybierz pozycję **Kubernetes**.

1. Wybierz definicję zasad, a następnie wybierz przycisk **Przypisz.**

1. Ustaw **zakres** na grupę zarządzania, subskrypcję lub grupę zasobów klastra Kubernetes, w której będzie stosowane przypisanie zasad.

   > [!NOTE]
   > Podczas przypisywania definicji usługi Azure Policy dla usługi AKS **zakres** musi zawierać zasób klastra AKS.

1. Nadaj przypisaniu zasad **nazwę** i **opis,** których można łatwo zidentyfikować.

1. Ustaw [wymuszanie zasad](./assignment-structure.md#enforcement-mode) na jedną z poniższych wartości.

   - **Włączone** — wymuszanie zasad w klastrze. Żądania dopuszczenia kubernetes z naruszenia są odrzucane.
   
   - **Wyłączone** — nie wymuszaj zasad w klastrze. Prośby o dopuszczenie kubernetes z naruszeniami nie są odrzucane. Wyniki oceny zgodności są nadal dostępne. Podczas wdrażania nowych zasad do uruchamiania klastrów, _Wyłączone_ opcja jest przydatna do testowania zasad, jak żądania przyjęcia z naruszenia nie są odrzucane.

1. Wybierz **pozycję Dalej**.

1. Ustawianie **wartości parametrów**
   
   - Aby wykluczyć obszary nazw kubernetes z oceny zasad, określ listę obszarów nazw w **opcjach Wykluczenia obszaru nazw**. Zaleca się, aby wykluczyć: _kube-system_

1. Wybierz pozycję **Przegląd + utwórz**.

Alternatywnie użyj [przypisz zasad — Szybki](../assign-policy-portal.md) start portalu, aby znaleźć i przypisać zasady AKS. Wyszukaj definicję zasad kubernetes zamiast przykładowych "maszyn wirtualnych inspekcji".

> [!IMPORTANT]
> Wbudowane zasady w kategorii **Kubernetes** są tylko do użytku z AKS. Aby uzyskać listę wbudowanych zasad, zobacz [przykłady kubernetes](../samples/built-in-policies.md#kubernetes).

## <a name="logging"></a>Rejestrowanie

### <a name="azure-policy-add-on-logs"></a>Dzienniki dodatków zasad platformy Azure

Jako kontroler/kontener usługi Kubernetes zarówno dodatek zasad azure, jak i gatekeeper przechowują dzienniki w klastrze AKS. Dzienniki są widoczne na stronie **Insights** klastra AKS. Aby uzyskać więcej informacji, zobacz [Opis wydajności klastra AKS z usługą Azure Monitor dla kontenerów.](../../../azure-monitor/insights/container-insights-analyze.md)

## <a name="remove-the-add-on"></a>Usuwanie dodatku

Aby usunąć dodatek zasad platformy Azure z klastra AKS, użyj witryny Azure portal lub interfejsu wiersza polecenia platformy Azure:

- Portal Azure

  1. Uruchom usługę AKS w witrynie Azure portal, klikając **pozycję Wszystkie usługi,** a następnie wyszukując i wybierając **pozycję Usługi Kubernetes.**

  1. Wybierz klaster AKS, w którym chcesz wyłączyć dodatek zasad platformy Azure.

  1. Wybierz **pozycję Zasady (wersja zapoznawcza)** po lewej stronie strony usługi Kubernetes.

     ![Zasady z klastra AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na stronie głównej wybierz przycisk **Wyłącz dodatek.**

     ![Wyłączanie dodatku Azure Policy for AKS](../media/rego-for-aks/disable-policy-add-on.png)

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
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
