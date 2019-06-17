---
title: Deweloper najlepsze rozwiązania — Zarządzanie zasobami w usłudze Azure Kubernetes usługi (AKS)
description: Dowiedz się, Deweloper aplikacji najlepsze rozwiązania dotyczące zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: zarhoads
ms.openlocfilehash: 69f60036bd718264174bf1befe832305e250e77c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073948"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dla deweloperów aplikacji do zarządzania zasobami w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS), istnieje kilka kluczowe obszary, które należy wziąć pod uwagę. Jak zarządzać wdrożenia aplikacji może niekorzystnie wpłynąć na środowisko użytkownika końcowego usługi, które należy podać. Ułatwiające pomyślnie, należy pamiętać, niektóre najlepsze rozwiązania można wykonać podczas tworzenia i uruchamiania aplikacji w usłudze AKS.

Najlepsze rozwiązania dotyczące tej koncentruje się na sposób uruchamiania klastra i obciążeń usługi z perspektywy dewelopera aplikacji. Aby uzyskać informacji na temat administracyjne najlepszych rozwiązań, zobacz [klastra operator najlepsze rozwiązania dotyczące izolacji i zarządzanie zasobami w usłudze Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. W tym artykule dowiesz się:

> [!div class="checklist"]
> * Jakie są limity i żądania zasobu pod
> * Sposoby tworzenia i wdrażania aplikacji przy użyciu standardowego miejsca do magazynowania i Visual Studio Code
> * Jak używać `kube-advisor` narzędzia pod kątem problemów z wdrożeniami

## <a name="define-pod-resource-requests-and-limits"></a>Definiowanie zasobników zasobów i limity

**Najważniejsze wskazówki** — Ustaw zasobnika żądań i ograniczenia dotyczące wszystkich zasobników w manifestach Twojego kodu YAML. Jeśli AKS klastra używa *limity przydziałów zasobów*, wdrożenie, mogą zostać odrzucone, jeśli nie zdefiniowano tych wartości.

Podstawowy sposób zarządzania zasobami obliczeniowymi w ramach klastra usługi AKS jest do używania zasobników żądań i limitów. Te żądania i limity umożliwiają wiedzieć, jakie obliczenia zasoby, które powinny być przypisane zasobnik harmonogram Kubernetes.

* **Zasobnik żądań** zdefiniować określoną ilość procesora CPU i pamięci, która musi zasobnik. Żądania te powinny być ilość zasobów obliczeniowych zasobnik wymaga, aby zapewnić akceptowalny poziom wydajności.
    * Gdy harmonogram Kubernetes próbuje umieścić zasobnik w węźle, żądania zasobnika są używane do określenia, który węzeł ma wystarczające zasoby dostępne.
    * Monitorowanie wydajności aplikacji w taki sposób, aby dostosować te żądania, aby upewnić się, że nie Definiuj mniej zasobów, które są wymagane do obsługi akceptowalny poziom wydajności.
* **Zasobnik limity** są maksymalną ilość zasobów Procesora i pamięci, można użyć zasobnik. Te limity uniemożliwiać jednego lub dwóch zasobników braków biorąc zbyt dużo czasu Procesora i pamięci z węzła. Ten scenariusz może zmniejszyć wydajność węzła i innymi zasobników, działających na nim.
    * Nie należy ustawiać zasobnika limitu większego od węzłów może obsługiwać. Każdy węzeł usługi AKS rezerwuje określoną ilość czasu Procesora i pamięci dla podstawowych składników platformy Kubernetes. Aplikacja może próbować używać zbyt wiele zasobów w węźle na potrzeby innych zasobników pomyślnie uruchomić.
    * Ponownie monitorowanie wydajności aplikacji w różnym czasie w ciągu dnia lub tygodnia. Określić, kiedy jest szczytowego zapotrzebowania i Dopasuj limity zasobników, aby zasoby wymagane do potrzeb aplikacji.

W Twoim kryteriom zasobnika jest najlepszym rozwiązaniem jest określenie tych żądań i ograniczeń. Jeśli nie podasz tych wartości, harmonogram Kubernetes nie zrozumieć, jakie zasoby są potrzebne. Harmonogram może zaplanować zasobnik w węźle nie posiadają wystarczających zasobów w celu zapewnienia wydajności aplikacji dopuszczalne. Administrator klastra może ustawić *limity przydziałów zasobów* w przestrzeni nazw, która wymaga ustawienia zasobów i limity. Aby uzyskać więcej informacji, zobacz [klastrów limity przydziałów zasobów w usłudze AKS][resource-quotas].

Podczas definiowania żądania procesora CPU lub limit, wartość jest mierzoną w jednostkach procesora CPU. *1.0* procesora CPU jest równa podstawowy wirtualny rdzeniu Procesora w węźle. Tej samej jednostki miary jest używany dla procesorów GPU. Można również zdefiniować ułamkowe żądania lub limit, zazwyczaj w millicpu. Na przykład *100 mln* jest *0,1* podstawowych rdzeni procesorów wirtualnych.

W poniższym przykładzie podstawowe dla pojedynczy zasobnik NGINX, żąda zasobnik *100 mln* czasu procesora CPU i *128Mi* pamięci. Limity zasobów dla zasobnik są ustawione na *250 mln* procesora CPU i *256Mi* pamięci:

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

Aby uzyskać więcej informacji dotyczących pomiarów zasobów i przydziałów, zobacz [Zarządzanie zasoby obliczeniowe dla kontenerów][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Programowanie i debugowanie aplikacji w ramach klastra usługi AKS

**Najważniejsze wskazówki** — zespoły programistyczne powinien wdrażanie i debugowanie względem klastra usługi AKS przy użyciu standardowego miejsca do magazynowania. Ten model programowania gwarantuje, że mechanizmy kontroli dostępu opartej na rolach, sieci lub potrzeb dotyczących magazynowania są wykonywane przed wdrożeniem aplikacji do środowiska produkcyjnego.

Za pomocą usługi Azure Dev miejsca do magazynowania tworzyć, debugować i testować aplikacje bezpośrednio w odniesieniu do klastra usługi AKS. Deweloperzy w zespole współpracują w celu tworzenia i testowania w całym cyklu życia aplikacji. Można nadal używać istniejących narzędzi, takich jak Visual Studio lub Visual Studio Code. Rozszerzenie jest zainstalowane dla deweloperów miejsca do magazynowania, która daje możliwość uruchamiania i debugowania aplikacji w klastrze AKS:

![Debugowanie aplikacji w klastrze AKS przy użyciu standardowego miejsca do magazynowania](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Ten zintegrowany proces projektowania i testowania spacjami Dev ogranicza potrzebę dla środowisk testowych lokalnych, takich jak [minikube][minikube]. Zamiast tego twórz i testujemy współpracę z klastra usługi AKS. Ten klaster może być zabezpieczone i izolowane, jak wspomniano w poprzedniej sekcji przy użyciu przestrzeni nazw do izolowania logicznie klastra. Gdy Twoje aplikacje są gotowe do wdrożenia do środowiska produkcyjnego, możesz skutecznie wdrażać jako rozwoju zostało zrobione przed rzeczywistym klastrem AKS.

Azure Dev spacji jest przeznaczony do użytku z aplikacjami, przeznaczonych dla systemu Linux zasobników i węzłów.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Użyj rozszerzenia programu Visual Studio Code dla rozwiązania Kubernetes

**Najważniejsze wskazówki** -instalacji i używania manifesty rozszerzenia programu VS Code dla platformy Kubernetes podczas pisania kodu YAML. Rozszerzenie służy również do wdrożenia zintegrowanego rozwiązania, które mogą pomóc właścicieli aplikacji, które rzadko wchodzić w interakcje z klastrem usługi AKS.

[Rozszerzenia programu Visual Studio Code dla rozwiązania Kubernetes] [ vscode-kubernetes] ułatwia opracowywanie i wdrażanie aplikacji w usłudze AKS. Rozszerzenie udostępnia funkcję intellisense dla zasobów platformy Kubernetes oraz wykresów rozwiązania Helm i szablony. Można również przeglądać, wdrażanie i edytować zasoby platformy Kubernetes z programu VS Code. Rozszerzenie również umożliwia sprawdzenie funkcji intellisense dla żądania zasobu lub ogranicza ustawiany w specyfikacji zasobnika:

![Rozszerzenia kodu VS dla rozwiązania Kubernetes ostrzeżenia dotyczące brakujących limity pamięci](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Regularne sprawdzanie problemów z aplikacjami przy użyciu klastra kubernetes w usłudze advisor

**Najważniejsze wskazówki** -regularnie uruchomić najnowszą wersję `kube-advisor` narzędzia typu open source do wykrywania problemów w klastrze. Jeśli zastosujesz limity przydziałów zasobów w istniejącym klastrze usługi AKS, uruchom `kube-advisor` najpierw po to, aby znaleźć zasobników, które nie mają żądania zasobów i ograniczeń.

[Klastra kubernetes w usłudze advisor] [ kube-advisor] narzędzie jest skojarzone projekt typu open source AKS, które skanuje klastra Kubernetes i raporty dotyczące problemów, które znajdzie. Jest jeden wyboru przydatne do identyfikowania zasobników, które nie mają i limity zasobów w miejscu.

Narzędzia klastra kubernetes w usłudze advisor mogą być przedstawione na żądanie zasobów i limity Brak w aplikacji PodSpecs dla Windows, a także aplikacje dla systemu Linux, ale samo narzędzie klastra kubernetes w usłudze klasyfikatora musi być zaplanowane na zasobnik systemu Linux. Można zaplanować zasobnika do uruchamiania na pulę węzłów przy użyciu określonego systemu operacyjnego [selektor węzła] [ k8s-node-selector] w zasobniku konfiguracji.

W klastrze AKS, który hostuje wiele zespołów deweloperów i aplikacji może być trudne do śledzenia zasobników, bez tych zasobów żądania i ogranicza zestaw. Najlepszym rozwiązaniem jest regularne uruchamianie `kube-advisor` w klastrach usługi AKS.

## <a name="next-steps"></a>Kolejne kroki

Najlepsze rozwiązania dotyczące tej koncentruje się na sposób uruchamiania klastra i obciążeń usługi z punktu widzenia operator klastra. Aby uzyskać informacji na temat administracyjne najlepszych rozwiązań, zobacz [klastra operator najlepsze rozwiązania dotyczące izolacji i zarządzanie zasobami w usłudze Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

Aby zaimplementować niektórych z tych najlepszych rozwiązań, zobacz następujące artykuły:

* [Programowanie przy użyciu standardowego miejsca do magazynowania][dev-spaces]
* [Problemy związane z klastra kubernetes w usłudze advisor][aks-kubeadvisor]

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
