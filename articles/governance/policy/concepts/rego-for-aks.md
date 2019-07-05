---
title: Dowiedz się, jak inspekcji zawartości maszyny wirtualnej
description: Dowiedz się, jak usługi Azure Policy używa Rego i otwórz agenta zasad do zarządzania klastrami, w usłudze Azure Kubernetes Service.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453907"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Omówienie zasad platformy Azure dla usługi Azure Kubernetes Service

Usługa Azure Policy integruje się z [usługi Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) do zastosowania enforcements na dużą skalę i zabezpieczenia w klastrach w sposób scentralizowany, spójne.
Za korzystanie z rozszerzania [strażnika](https://github.com/open-policy-agent/gatekeeper), _elementu webhook kontrolera dopuszczenie_ dla [Otwórz agenta zasad](https://www.openpolicyagent.org/) (Nieprzez), usługa Azure Policy umożliwia zarządzanie i raport dotyczący zgodności stan zasobów platformy Azure i klastry usługi AKS w jednym miejscu.

> [!NOTE]
> Usługa Azure Policy for AKS jest w ograniczonej wersji zapoznawczej i obsługuje tylko wbudowane definicje zasad.

## <a name="overview"></a>Omówienie

Aby włączyć i używać usługi Azure Policy dla usługi AKS przy użyciu klastra usługi AKS, wykonaj następujące czynności:

- [Zoptymalizowany pod kątem — funkcji w wersji zapoznawczej](#opt-in-for-preview)
- [Zainstaluj dodatek usługi Azure Policy](#installation-steps)
- [Przypisywanie definicji zasad dla usługi AKS](#built-in-policies)
- [Poczekaj, aż sprawdzanie poprawności](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Zoptymalizowany pod kątem w wersji zapoznawczej

Przed zainstalowaniem dodatku zasad platformy Azure lub włączenie funkcji usługi, należy włączyć swoją subskrypcję **Microsoft.ContainerService** dostawcy zasobów i **Microsoft.PolicyInsights**dostawcy zasobów, a następnie zostać zatwierdzone do dołączenia do korzystania z wersji zapoznawczej. Aby przyłączyć się korzystania z wersji zapoznawczej, wykonaj następujące kroki, w witrynie Azure portal lub za pomocą wiersza polecenia platformy Azure:

- Witryna Azure Portal:

  1. Zarejestruj **Microsoft.ContainerService** i **Microsoft.PolicyInsights** dostawców zasobów. Aby uzyskać instrukcje, zobacz [dostawcy zasobów i ich typy](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

     ![Wyszukaj zasady wszystkie usługi](../media/rego-for-aks/search-policy.png)

  1. Wybierz **Dołącz Podgląd** po lewej stronie strony usługi Azure Policy.

     ![Dołącz zasady dla usługi AKS (wersja zapoznawcza)](../media/rego-for-aks/join-aks-preview.png)

  1. Wybierz wiersz dotyczący subskrypcji, który chcesz dodać do wersji zapoznawczej.

  1. Wybierz **zgłoszenie zgody na uczestnictwo w** znajdujący się u góry listy subskrypcji.

- Interfejs wiersza polecenia platformy Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Usługa Azure Policy dodatku

_Dodatek zasad usługi Azure_ dla rozwiązania Kubernetes nawiązanie połączenia z usługi Azure Policy kontrolera dopuszczenie strażnika. Dodatku, który jest instalowany w _usługi azure policy_ przestrzeni nazw, enacts następujące funkcje:

- Wyszukuje przypisania do klastra usługi AKS przy użyciu usługi Azure Policy
- Pliki do pobrania i buforuje szczegóły zasad, w tym _rego_ definicji zasad jako **configmaps**
- Uruchamia sprawdzanie zgodności pełne skanowanie w klastrze usługi AKS
- Raporty, inspekcje i szczegóły zgodności z powrotem do usługi Azure Policy

### <a name="installing-the-add-on"></a>Instalowanie dodatku

#### <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem dodatku w klastrze AKS, można zainstalować rozszerzenia w wersji zapoznawczej. Ten krok jest przeprowadzane za pomocą wiersza polecenia platformy Azure:

1. Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.62 lub później zainstalowane i skonfigurowane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Klaster AKS musi być w wersji _1.10_ lub nowszej. Aby sprawdzić poprawność używana wersja klastra AKS, użyj następującego skryptu:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Zainstaluj wersję _0.4.0_ z wiersza polecenia platformy Azure rozszerzenie w wersji zapoznawczej dla usługi AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Jeśli wcześniej zainstalowano _podglądu usługi aks_ rozszerzenia należy zainstalować pakiet dowolne aktualizacje przy użyciu `az extension update --name aks-preview` polecenia.

#### <a name="installation-steps"></a>Kroki instalacji

Po wykonaniu wymagań wstępnych, należy zainstalować dodatek usługi Azure Policy w klastrze AKS, którą chcesz zarządzać.

- Azure Portal

  1. Uruchamianie usługi AKS w witrynie Azure portal, klikając **wszystkich usług**, wyszukiwanie i wybieranie **usługi Kubernetes**.

  1. Wybierz jeden z klastry usługi AKS.

  1. Wybierz **zasady (wersja zapoznawcza)** w lewej części strony usługi Kubernetes.

     ![Zasady z klastra usługi AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na stronie głównej wybierz **włączyć dodatek** przycisku.

     ![Włączanie usługi Azure Policy w przypadku usługi AKS dodatku](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Jeśli **włączyć dodatek** przycisk będzie szary, subskrypcji nie został jeszcze dodany do korzystania z wersji zapoznawczej. Zobacz [zgłoszenie zgody na uczestnictwo w wersji zapoznawczej](#opt-in-for-preview) wymagane czynności.

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Sprawdzanie poprawności i częstotliwość raportowania

Dodatek sprawdza się przy użyciu usługi Azure Policy zmiany przypisania zasad co 5 minut. Podczas tego cyklu odświeżania dodatku powoduje usunięcie wszystkich _configmaps_ w _usługi azure policy_ utworzy ponownie obszar nazw _configmaps_ do użytku programu GateKeeper.

> [!NOTE]
> Gdy _klastra Administrator_ może mieć uprawnień do _usługi azure policy_ przestrzeni nazw, nie zaleca się lub wprowadzać zmiany do przestrzeni nazw. Wszelkie zmiany dokonane ręcznie zostaną utracone podczas cyklu odświeżania.

Co 5 minut, dodatek wymaga pełnego skanowania klastra. Po zebraniu informacji pełnego skanowania i wszelkie ocen w czasie rzeczywistym przez strażnika próba zmiany do klastra, dodatek raportuje wyniki ją do usługi Azure Policy do włączenia [szczegóły zgodności](../how-to/get-compliance-data.md) żadnych zasad platformy Azure, takich jak przypisanie. Tylko aktywne zasady przydziałów są zwracane podczas cyklu inspekcji.

## <a name="policy-language"></a>Język zasad

Struktura języka usługi Azure Policy do zarządzania usługi AKS jest zgodna z istniejących zasad. Efekt _EnforceRegoPolicy_ służy do zarządzania klastrami usługi AKS i przyjmuje _szczegóły_ właściwości specyficzne dla pracy z Nieprzez i strażnika. Aby uzyskać szczegółowe informacje i przykłady, zobacz [EnforceRegoPolicy](effects.md#enforceregopolicy) efekt.

Jako część _details.policy_ właściwości w definicji zasad usługi Azure Policy przekazuje identyfikator URI zasad rego do dodatku. Rego to język, który Nieprzez strażnika obsługę i sprawdzanie poprawności lub mutować żądanie w klastrze usługi Kubernetes. Dzięki obsłudze istniejące standardy, do zarządzania usługi Kubernetes, usługi Azure Policy umożliwia ponowne używanie istniejących reguł i skojarzyć je z usługą Azure Policy pod kątem zgodności chmury ujednoliconego środowiska tworzenia raportów. Aby uzyskać więcej informacji, zobacz [co to jest Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Wbudowane zasady

Aby znaleźć wbudowanych zasad do zarządzania usługi AKS przy użyciu witryny Azure portal, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure portal. Wybierz **wszystkich usług** w okienku po lewej stronie i następnie wyszukaj i wybierz **zasad**.

1. W okienku po lewej stronie Azure Policy wybierz **definicje**.

1. W polu listy rozwijanej Kategoria **Zaznacz wszystko** wyczyścić filtr, a następnie wybierz pozycję **usługi Kubernetes**.

1. Wybierz definicję zasad, a następnie wybierz **przypisać** przycisku.

> [!NOTE]
> Podczas przypisywania zasad platformy Azure dla definicji usługi AKS **zakres** musi zawierać zasobu klastra usługi AKS.

Możesz też skorzystać [przypisywanie zasad — Portal](../assign-policy-portal.md) szybkiego startu, aby znaleźć i przypisz zasady usługi AKS. Wyszukiwanie definicji zasad Kubernetes zamiast przykładowych "inspekcji maszyny wirtualne".

## <a name="logging"></a>Rejestrowanie

### <a name="azure-policy-add-on-logs"></a>Dzienniki dodatek zasad platformy Azure

Jako rozwiązanie Kubernetes kontrolera/kontenera dodatek usługi Azure Policy zachowuje dzienniki w klastrze AKS. Dzienniki są widoczne w **Insights** strony klastra AKS. Aby uzyskać więcej informacji, zobacz [zrozumieć AKS klastra wydajność dzięki usłudze Azure Monitor kontenery](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Dzienniki programu gateKeeper

Aby włączyć dzienniki programu GateKeeper dla nowych żądań zasobów, wykonaj kroki opisane w [włączyć i przejrzeć dzienniki węzła głównego, w usłudze AKS rozwiązania Kubernetes](../../../aks/view-master-logs.md).
Poniżej przedstawiono przykładowe zapytanie, aby wyświetlić zdarzenia odmowy dla nowych żądań zasobów:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Aby wyświetlić dzienniki z kontenerów strażnika, wykonaj kroki opisane w [włączyć i przejrzeć dzienniki węzła głównego, w usłudze AKS rozwiązania Kubernetes](../../../aks/view-master-logs.md) i sprawdź _apiserver klastra kubernetes w usłudze_ opcji **konfiguracjiustawieńdiagnostyki** okienka.

## <a name="remove-the-add-on"></a>Usuń dodatek

Aby usunąć dodatek usługi Azure Policy z klastra usługi AKS, użyj portalu Azure lub interfejsu wiersza polecenia platformy Azure:

- Azure Portal

  1. Uruchamianie usługi AKS w witrynie Azure portal, klikając **wszystkich usług**, wyszukiwanie i wybieranie **usługi Kubernetes**.

  1. Wybierz klastra usługi AKS, w którym chcesz wyłączyć dodatek usługi Azure Policy.

  1. Wybierz **zasady (wersja zapoznawcza)** w lewej części strony usługi Kubernetes.

     ![Zasady z klastra usługi AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na stronie głównej wybierz **wyłączyć dodatek** przycisku.

     ![Wyłącz usługi Azure Policy w przypadku usługi AKS dodatku](../media/rego-for-aks/disable-policy-add-on.png)

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md).
- Przejrzyj [strukturę definicji usługi Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Zrozumienie sposobu [programowe tworzenie zasad](../how-to/programmatically-create.md).
- Dowiedz się, jak [Pobierz dane zgodności](../how-to/getting-compliance-data.md).
- Dowiedz się, jak [korygowanie niezgodnych zasobów](../how-to/remediate-resources.md).
- Przejrzyj grupy zarządzania jest [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../management-groups/index.md).