---
title: Najważniejsze rozwiązania dla deweloperów — zarządzanie zasobami w usługach Kubernetes platformy Azure (AKS)
description: Zapoznaj się z najlepszymi rozwiązaniami dla deweloperów aplikacji dotyczących zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 66676c90e73d1886c86d8afda8cbbecce239a005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259528"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące zarządzania zasobami przez deweloperów aplikacji w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS) istnieje kilka kluczowych obszarów, które należy wziąć pod uwagę. Sposób zarządzania wdrożeniami aplikacji może negatywnie wpłynąć na środowisko użytkownika końcowego usług, które świadczysz. Aby pomóc Ci odnieść sukces, należy pamiętać o niektórych sprawdzonych praktykach, które można wykonać podczas opracowywania i uruchamiania aplikacji w uaś według aksu.

W tym artykule najważniejsze wskazówki koncentruje się na jak uruchomić klastra i obciążeń z punktu widzenia dewelopera aplikacji. Aby uzyskać informacje na temat najlepszych rozwiązań administracyjnych, zobacz [Najważniejsze wskazówki dotyczące izolacji i zarządzania zasobami w usłudze Azure Kubernetes Service (AKS).][operator-best-practices-isolation] W tym artykule omówiono następujące zagadnienia:

> [!div class="checklist"]
> * Co to są żądania zasobów zasobu zasobu zasobu zasobu zasobu
> * Sposoby tworzenia i wdrażania aplikacji za pomocą funkcji Dev Spaces i Visual Studio Code
> * Jak korzystać `kube-advisor` z narzędzia, aby sprawdzić problemy z wdrożeniami

## <a name="define-pod-resource-requests-and-limits"></a>Definiowanie żądań i limitów zasobów zasobu zasobu zasobu zasobu zasobu zasobu

**Wskazówki dotyczące najlepszych praktyk** — ustaw żądania zasobników i limity dla wszystkich zasobników w manifestach YAML. Jeśli klaster AKS używa *przydziałów zasobów,* wdrożenie może zostać odrzucone, jeśli te wartości nie zostaną zdefiniowane.

Podstawowym sposobem zarządzania zasobami obliczeniowymi w klastrze AKS jest użycie żądań zasobników i limitów. Te żądania i limity niech Harmonogram Kubernetes wiedzieć, jakie zasoby obliczeniowe zasoby zasobnik powinien być przypisany.

* **Pod CPU/ Memory żądań** zdefiniować określoną ilość procesora CPU i pamięci, że zasobnik potrzebuje na bieżąco.
    * Gdy harmonogram Kubernetes próbuje umieścić zasobnik w węźle, żądania zasobnika są używane do określenia, który węzeł ma wystarczające zasoby dostępne do planowania.
    * Nie ustawienie żądania zasobnika spowoduje jego ustawienie domyślnie zdefiniowanego limitu.
    * Jest bardzo ważne, aby monitorować wydajność aplikacji, aby dostosować te żądania. Jeśli nie są składane niewystarczające żądania, aplikacja może odbierać wydajność obniżoną z powodu planowania węzła. Jeśli żądania są przeszacowane, aplikacja może mieć zwiększone trudności z uzyskaniem zaplanowanego.
* **Limity procesora/pamięci zasobników** to maksymalna ilość procesora i pamięci, z których może korzystać zasobnik. Limity te pomagają określić, które zasobniki powinny zostać zabite w przypadku niestabilności węzła z powodu niewystarczających zasobów. Bez odpowiednich limitów ustawione strąki zostaną zabite do czasu podniesienia ciśnienia zasobów.
    * Limity zasobników pomagają zdefiniować, kiedy zasobnik utracił kontrolę nad zużyciem zasobów. Po przekroczeniu limitu zasobnik jest priorytetem dla zabijania w celu utrzymania kondycji węzła i zminimalizowania wpływu na zasobników współużytkowanych węzła.
    * Nie ustawienie limitu zasobnika domyślnie do najwyższej dostępnej wartości w danym węźle.
    * Nie ustawiaj limitu zasobników wyższego niż obsługiwane przez węzły. Każdy węzeł AKS rezerwuje określoną ilość procesora CPU i pamięci dla podstawowych składników Kubernetes. Aplikacja może próbować zużywać zbyt wiele zasobów w węźle dla innych zasobników, aby pomyślnie uruchomić.
    * Ponownie, bardzo ważne jest, aby monitorować wydajność aplikacji w różnych porach dnia lub tygodnia. Określ, kiedy jest szczytowe zapotrzebowanie i wyrównaj limity zasobników do zasobów wymaganych do zaspokojenia maksymalnych potrzeb aplikacji.

W specyfikacji zasobnika jest **najlepszym rozwiązaniem i bardzo ważne** jest zdefiniowanie tych żądań i limitów na podstawie powyższych informacji. Jeśli te wartości nie zostaną uwzględnione, harmonogram usługi Kubernetes nie może uwzględniać zasobów wymaganych przez aplikacje do pomocy w podejmowaniu decyzji dotyczących planowania.

Jeśli harmonogram umieszcza zasobnika w węźle z niewystarczających zasobów, wydajność aplikacji zostanie obniżona. Zdecydowanie zaleca się, aby administratorzy klastrów ustawiali *przydziały zasobów* w obszarze nazw, który wymaga ustawiania żądań i limitów zasobów. Aby uzyskać więcej informacji, zobacz [Przydziały zasobów w klastrach AKS][resource-quotas].

Podczas definiowania żądania lub limitu procesora CPU wartość jest mierzona w jednostkach procesora CPU. 
* *Procesor 1.0* jest równy jednemu z podstawowych rdzeni wirtualnych procesorów w węźle. 
* Ten sam pomiar jest używany dla procesorów graficznych.
* Można zdefiniować ułamki mierzone w milikores. Na przykład *100m* jest *0,1* podstawowego rdzenia vCPU.

W poniższym podstawowym przykładzie dla pojedynczego zasobnika NGINX zasobnik żąda *100m* czasu procesora CPU i *128Mi* pamięci. Limity zasobów dla zasobnika są ustawione na *250m* cpu i *256Mi* pamięci:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Aby uzyskać więcej informacji na temat pomiarów i przydziałów zasobów, zobacz [Zarządzanie zasobami obliczeniowymi kontenerów][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Tworzenie i debugowanie aplikacji względem klastra AKS

**Wskazówki dotyczące najlepszych rozwiązań** — zespoły programistyczne należy wdrożyć i debugować względem klastra AKS przy użyciu obszarów deweloperów. Ten model programistyczny zapewnia, że formanty dostępu oparte na rolach, potrzeby sieci lub magazynu są implementowane przed wdrożeniem aplikacji w procesach produkcyjnych.

Za pomocą usługi Azure Dev Spaces można tworzyć, debugować i testować aplikacje bezpośrednio względem klastra AKS. Deweloperzy w zespole współpracują ze sobą w celu tworzenia i testowania w całym cyklu życia aplikacji. Można nadal korzystać z istniejących narzędzi, takich jak Visual Studio lub Visual Studio Code. Rozszerzenie jest zainstalowane dla dev spaces, który daje możliwość uruchamiania i debugowania aplikacji w klastrze AKS:

![Debugowanie aplikacji w klastrze AKS ze spacjami deweloperów](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Ten zintegrowany proces rozwoju i testowania z dev spaces zmniejsza zapotrzebowanie na lokalne środowiska testowe, takie jak [minikube][minikube]. Zamiast tego należy opracować i przetestować na klastrze AKS. Ten klaster może być zabezpieczony i odizolowany, jak wspomniano w poprzedniej sekcji dotyczącej używania obszarów nazw do logicznego izolowania klastra. Gdy aplikacje są gotowe do wdrożenia w procesach produkcyjnych, można śmiało wdrożyć, jak rozwój został wykonany na całym klastrze AKS.

Usługa Azure Dev Spaces jest przeznaczona do użytku z aplikacjami uruchamiającymi się w zasobnikach i węzłach systemu Linux.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Używanie rozszerzenia kodu programu Visual Studio dla usługi Kubernetes

**Wskazówki dotyczące najlepszych praktyk** — instalowanie i używanie rozszerzenia kodu programu VS dla aplikacji Kubernetes podczas pisania manifestów YAML. Można również użyć rozszerzenia dla zintegrowanego rozwiązania wdrożeniowego, które może pomóc właścicielom aplikacji, które rzadko wchodzą w interakcje z klastrem AKS.

[Rozszerzenie kodu programu Visual Studio dla usługi Kubernetes][vscode-kubernetes] ułatwia tworzenie i wdrażanie aplikacji w u usługi AKS. Rozszerzenie zapewnia intellisense dla zasobów Kubernetes i helm wykresy i szablony. Można również przeglądać, wdrażać i edytować zasoby kubernetes z poziomu programu VS Code. Rozszerzenie zapewnia również intellisense sprawdzić żądania zasobów lub limity są ustawiane w specyfikacji zasobu:

![Rozszerzenie kodu VS dla aplikacji Kubernetes ostrzeżenie o brakujących limitów pamięci](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Regularnie sprawdzaj problemy z aplikacją z kube-advisor

**Wskazówki dotyczące najlepszych praktyk** — `kube-advisor` regularnie uruchamiaj najnowszą wersję narzędzia open source w celu wykrywania problemów w klastrze. Jeśli zastosujesz przydziały zasobów do istniejącego `kube-advisor` klastra usługi AKS, uruchom najpierw, aby znaleźć zasobniki, które nie mają zdefiniowanych żądań zasobów i limitów.

Narzędzie [kube-advisor][kube-advisor] jest skojarzonym projektem open source usługi AKS, który skanuje klaster Kubernetes i raportuje problemy, które znajdzie. Jednym z przydatnych kontroli jest zidentyfikowanie zasobników, które nie mają żądań zasobów i limitów w miejscu.

Narzędzie kube-advisor może raportować żądanie zasobów i limity brakujące w PodSpecs dla aplikacji Windows, a także aplikacji Linux, ale samo narzędzie kube-advisor musi być zaplanowane na zasobniku Linux. Można zaplanować zasobnik do uruchomienia w puli węzłów z określonego systemu operacyjnego przy użyciu [selektora węzłów][k8s-node-selector] w konfiguracji zasobnika.

W klastrze AKS, który obsługuje wiele zespołów programistycznych i aplikacji, może być trudne do śledzenia zasobników bez tych żądań zasobów i limitów ustawionych. Najlepszym rozwiązaniem jest regularne `kube-advisor` uruchamianie klastrów usługi AKS.

## <a name="next-steps"></a>Następne kroki

W tym artykule najważniejsze wskazówki koncentruje się na jak uruchomić klastra i obciążeń z punktu widzenia operatora klastra. Aby uzyskać informacje na temat najlepszych rozwiązań administracyjnych, zobacz [Najważniejsze wskazówki dotyczące izolacji i zarządzania zasobami w usłudze Azure Kubernetes Service (AKS).][operator-best-practices-isolation]

Aby zaimplementować niektóre z tych najlepszych rozwiązań, zobacz następujące artykuły:

* [Programowanie za pomocą usługi Dev Spaces][dev-spaces]
* [Sprawdź, czy nie występują problemy z kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
