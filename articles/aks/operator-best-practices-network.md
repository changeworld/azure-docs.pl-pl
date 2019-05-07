---
title: Operator najlepsze rozwiązania — łączność sieciową w usłudze Azure Kubernetes usługi (AKS)
description: Poznaj klastra operator najlepsze rozwiązania dotyczące zasobów sieci wirtualnej i połączeń w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: iainfou
ms.openlocfilehash: 2bdc18ba4dc77178d5fcc5d2ba6d89aa109d923c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074144"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące łączności sieciowej i zabezpieczeń w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) dla węzłów i aplikacji jest zapewniają łączność sieciową. Te zasoby sieci obejmują zakresy adresów IP, moduły równoważenia obciążenia i kontrolery transferu danych przychodzących. Do utrzymania wysokiej jakości usługi dla aplikacji, należy zaplanować, a następnie skonfigurować te zasoby.

Najlepsze rozwiązania dotyczące tej koncentruje się na łączności sieciowej i zabezpieczeń dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Porównaj wtyczki kubenet i wtyczki Azure CNI tryby sieci w usłudze AKS
> * Planowanie wymaganych adresów IP i łączność
> * Dystrybuowanie ruchu przy użyciu usługi równoważenia obciążenia, kontrolery transferu danych przychodzących lub zapory aplikacji sieci web (WAF)
> * Bezpieczne łączenie z węzłami klastra

## <a name="choose-the-appropriate-network-model"></a>Wybierz model odpowiedniej sieci

**Najważniejsze wskazówki** — w przypadku integracji z istniejącymi sieciami wirtualnymi i sieciami lokalnymi, użyj wtyczki Azure CNI sieci w usłudze AKS. Ten model sieci pozwala również mocniej, zasobów i formanty w środowisku przedsiębiorstwa.

Sieci wirtualne zapewniają podstawowej łączności dla węzłów AKS i klientom dostęp do aplikacji. Istnieją dwa różne sposoby wdrażania klastrów usługi AKS w sieciach wirtualnych:

* **Sieć z wtyczki Kubenet** — platforma Azure zarządza zasobami sieci wirtualnej w klastrze jest wdrażana i używa [wtyczki kubenet] [ kubenet] wtyczka platformy Kubernetes.
* **Sieć Azure CNI** — wdraża do istniejącej sieci wirtualnej i używa [wtyczki Azure Container Networking interfejsu (CNI)] [ cni-networking] wtyczka platformy Kubernetes. Zasobników otrzymują poszczególnych adresów IP, która może kierować do innych usług sieciowych lub zasobów lokalnych.

Container Networking interfejsu (CNI) jest protokołem neutralne, który pozwala wysyłać żądania do dostawcy sieci kontener środowiska uruchomieniowego. Wtyczki Azure CNI przypisywania adresów IP do zasobników i węzłów oraz udostępnia adres IP funkcji zarządzania (adresami IP IPAM) nawiązaniu połączenia z istniejącymi sieciami wirtualnymi platformy Azure. Każdy węzeł i zasobnika zasób otrzymuje adres IP w sieci wirtualnej platformy Azure, a nie dodatkowe routing jest potrzebne do komunikowania się z innych zasobów lub usług.

![Diagram przedstawiający dwa węzły o mostki łączenia każdego do pojedynczej sieci wirtualnej platformy Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

W przypadku większości wdrożeń produkcyjnych należy użyć wtyczki Azure CNI sieci. Ten model sieci umożliwia rozdzielenie kontroli i zarządzania zasobami. Z punktu widzenia zabezpieczeń często mają różne zespoły, zarządzanie i zabezpieczanie tych zasobów. Wtyczki Azure CNI sieci pozwala połączyć do istniejących zasobów platformy Azure, zasobów lokalnych lub innych usług, bezpośrednio za pomocą adresów IP przypisanych do każdego zasobników.

Gdy używasz wtyczki Azure CNI sieć, zasób sieci wirtualnej jest w oddzielnej grupie zasobów do klastra usługi AKS. Można delegować uprawnienia dla jednostki usługi AKS dostępu i zarządzanie tymi zasobami. Jednostki usługi używany przez klaster AKS musi mieć co najmniej [Współautor sieci](../role-based-access-control/built-in-roles.md#network-contributor) uprawnień w podsieci w ramach sieci wirtualnej. Jeśli chcesz zdefiniować [roli niestandardowej](../role-based-access-control/custom-roles.md) zamiast wbudowana rola Współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Aby uzyskać więcej informacji na temat delegowania nazwy głównej usługi AKS, zobacz [delegować dostęp do innych zasobów platformy Azure][sp-delegation].

Podobnie jak każdy węzeł i zasobnika otrzymują adres IP, należy zaplanować się zakresów adresów w podsieci usługi AKS. Podsieć musi być wystarczająco duży, aby przydzielać adresy IP dla każdego węzła, zasobników i zasobów sieciowych, które można wdrożyć. Każdy klaster AKS muszą być umieszczone w jej własnej podsieci. Aby umożliwić łączność z lokalnym lub w sieciach równorzędnych na platformie Azure, nie używaj zakresów adresów IP, które pokrywają się z istniejącymi zasobami sieciowymi. Istnieją domyślne limity liczby zasobników, z których każdy węzeł działa zarówno z wtyczki kubenet i wtyczki Azure CNI sieci. Do obsługi skalowania w górę zdarzeń lub Uaktualnianie klastra, należy również dodatkowe adresy IP do użycia w przypisanej podsieci. Ta dodatkowa przestrzeń adresowa jest szczególnie ważne, jeśli używasz kontenery systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS), jak te pule węzłów wymaga uaktualnienia do zastosowania najnowszych poprawek zabezpieczeń. Aby uzyskać więcej informacji w węzłach systemu Windows Server, zobacz [uaktualnienia pulę węzłów w usłudze AKS][nodepool-upgrade].

Do obliczania adresu IP wymagane, zobacz [wtyczki Azure CNI konfigurowania sieci w usłudze AKS][advanced-networking].

### <a name="kubenet-networking"></a>Wtyczki Kubenet sieci

Mimo że wtyczki kubenet nie wymagają skonfigurowania sieci wirtualnych, przed wdrożeniem klastra, są wady:

* Węzły i zasobników są umieszczane w różnych podsieciach IP. Przekazywanie zdefiniowaną przez użytkownika routingu (UDR) i adres IP jest używany do kierowania ruchem między zasobników i węzłów. Dodatkowe routingu może zmniejszyć wydajność sieci.
* Połączenia z istniejącej sieci lokalnej lub komunikację równorzędną z innymi sieciami wirtualnymi platformy Azure może być skomplikowane.

Wtyczki Kubenet jest odpowiedni dla małych obciążeń deweloperskich lub testowania, ponieważ nie trzeba utworzyć sieci wirtualnej i podsieci oddzielnie z klastra usługi AKS. Proste witryn sieci Web za pomocą o niewielkim ruchu, lub do przenoszenia obciążeń do kontenerów, również mogą korzystać z prostoty klastra AKS wdrożone z wtyczki kubenet w sieci. W przypadku większości wdrożeń produkcyjnych należy planowanie i używania wtyczki Azure CNI sieci. Możesz również [skonfigurować własnych zakresów adresów IP i sieci wirtualnych przy użyciu wtyczki kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Dystrybuuj ruch przychodzący

**Najważniejsze wskazówki** — aby dystrybuować ruch HTTP lub HTTPS do aplikacji, korzystanie z zasobów transferu danych przychodzących i kontrolerów. Ruch przychodzący kontrolery zapewniają dodatkowe funkcje za pośrednictwem modułu równoważenia obciążenia platformy Azure w regularnych i mogą być zarządzane jako natywne zasoby platformy Kubernetes.

Usługa Azure load balancer można dystrybuować ruch klientów do aplikacji w klastrze AKS, ale ma ograniczoną siebie dotyczące tego ruchu. Zasób modułu równoważenia obciążenia działa w warstwie 4 i dystrybuuje ruch w oparciu o protokół i porty. Większość aplikacji sieci web, które używają protokołu HTTP lub HTTPS, należy używać Kuberenetes transferu danych przychodzących zasobów i kontrolery, które działają w warstwie 7. Ruch przychodzący można dokonać dystrybucji ruchu w oparciu o adres URL aplikacji i dojścia przed zakończeniem protokołów TLS/SSL. Ta możliwość zmniejsza liczbę adresów IP, udostępnianie i mapowania. Z modułem równoważenia obciążenia każdej aplikacji zwykle wymaga publicznego adresu IP, przypisać i mapowany do usługi w klastrze AKS. Zasób transferu danych przychodzących pojedynczy adres IP można dystrybuować ruch do wielu aplikacji.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS](media/operator-best-practices-network/aks-ingress.png)

 Istnieją dwa składniki dla danych przychodzących:

 * Ruch przychodzący *zasobów*, i
 * Ruch przychodzący *kontrolera*

Zasób ruch przychodzący jest manifestem YAML `kind: Ingress` definiujący hosta, certyfikaty oraz reguły do kierowania ruchu do usług działających w klastrze AKS. Następujące manifest YAML przykład dystrybuować ruch na potrzeby *myapp.com* do jednej z dwóch usług *blogservice* lub *storeservice*. Klient zostaje skierowany do jednej usługi lub inny, zależnie od uzyskiwania dostępu do adresu URL.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Kontroler danych przychodzących jest demona, który działa w węźle usługi AKS i oczekuje na przychodzące żądania. Następnie dystrybucji ruchu na podstawie reguł zdefiniowanych w zasobie danych przychodzących. Najbardziej typowe kontrolera danych przychodzących jest oparty na [NGINX]. AKS nie ogranicza możliwości do określonego kontrolera, aby można było używać innych kontrolerów takich jak [rozkład][contour], [HAProxy][haproxy], lub [ Traefik][traefik].

Ruch przychodzący kontrolerów musi być zaplanowane w węźle systemu Linux. Węzły systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS) nie należy uruchamiać kontrolera danych przychodzących. Użyj selektora węzła w manifeście YAML lub wdrożenia wykresu Helm, aby wskazać, zasób powinien być uruchomiony w węźle opartych na systemie Linux. Aby uzyskać więcej informacji, zobacz [selektorów należy używać węzła do kontroli gdzie zaplanowane zasobników w usłudze AKS][concepts-node-selectors].

Istnieje wiele scenariuszy dla danych przychodzących, w tym następujące przewodniki z instrukcjami:

* [Tworzenie kontrolera podstawowego transferu danych przychodzących za pomocą połączenia z siecią zewnętrzną][aks-ingress-basic]
* [Tworzenie kontrolera danych przychodzących, korzystającą z sieci prywatne, wewnętrzne i adres IP][aks-ingress-internal]
* [Tworzenie kontrolera transferu danych przychodzących, która korzysta z certyfikatów protokołu TLS][aks-ingress-own-tls]
* Utwórz kontroler danych przychodzących, który używa umożliwia szyfrowanie, aby automatycznie wygenerować certyfikaty protokołu TLS [za pomocą dynamicznego publicznego adresu IP] [ aks-ingress-tls] lub [ze statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Bezpieczny ruch z zapory aplikacji sieci web (WAF)

**Najważniejsze wskazówki** — w celu skanowania ruch przychodzący na wypadek potencjalnych ataków, takich jak korzystać z zapory aplikacji sieci web (WAF) [zapory aplikacji internetowych Barracuda dla platformy Azure] [ barracuda-waf] lub usługi Azure Application Gateway. Te bardziej zaawansowanych zasobów sieciowych również może kierować ruchem poza tylko połączeń HTTP i HTTPS lub podstawowe kończenia żądań SSL.

Kontroler danych przychodzących, która dystrybuuje ruch do usług i aplikacji jest zwykle zasobem platformy Kubernetes w klastrze AKS. Kontroler działa jako demon w węźle usługi AKS i zużywa niektóre węzła zasoby, takie jak procesor CPU, pamięci i przepustowość sieci. W dużych środowisk często chcesz odciążyć niektóre tego routingu ruchu lub zakończenia protokołu TLS do zasobu sieciowego poza klastrem usługi AKS. Ponadto chcesz przeprowadzić skanowanie ruch przychodzący na wypadek potencjalnych ataków.

![Zapory aplikacji sieci web (WAF), takie jak bramy aplikacji platformy Azure mogą chronić i dystrybucji ruchu do klastra usługi AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Zapora aplikacji sieci web (WAF) zapewnia dodatkową warstwę zabezpieczeń, filtrując ruch przychodzący. Otwórz sieci Web aplikacji Security Project (OWASP) zawiera zestaw reguł do obserwacji atakami, takimi jak wielu lokacji skryptów lub skażające pliku cookie. [Usługa Azure Application Gateway] [ app-gateway] (obecnie w wersji zapoznawczej w usłudze AKS) jest zapory aplikacji sieci Web, które można zintegrować z klastrami AKS, aby zapewnić te funkcje zabezpieczeń, zanim ruch trafia z klastrem AKS i aplikacjami. Inne rozwiązania innych firm również wykonywać te funkcje, dzięki temu można nadal używać istniejących inwestycji i doświadczenia w danego produktu.

Zasobów obciążenia równoważenia lub ruch przychodzący w dalszym ciągu uruchamiać w klastrze usługi AKS w taki sposób, aby uściślić Dystrybucja ruchu. App Gateway mogą centralnie zarządzane jako kontroler danych przychodzących, podając definicję zasobu. Aby rozpocząć pracę, [utworzyć kontroler danych przychodzących z bram aplikacji][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Sterowanie przepływem ruchu za pomocą zasad sieciowych

**Najważniejsze wskazówki** — zasady sieciowe umożliwiają blokują lub zezwalają na ruch do zasobników. Domyślnie cały ruch jest dozwolony między zasobników w ramach klastra. Aby zwiększyć bezpieczeństwo należy zdefiniować reguły, które ograniczają komunikację pod.

Zasady sieci jest funkcją Kubernetes, która umożliwia sterowanie przepływem ruchu między zasobników. Istnieje możliwość blokują lub zezwalają na ruch na podstawie ustawień, takich jak przypisać etykiety, przestrzeń nazw lub ruchu sieciowego port. Użycie zasad sieciowych umożliwia natywnych dla chmury sterowanie przepływem ruchu. Zgodnie z zasobników są tworzone dynamicznie w klastrze AKS, zasady wymagane sieciowe mogą być automatycznie stosowane. Nie należy używać kontroli ruchu pod do zasobników, należy użyć zasad sieciowych grup zabezpieczeń sieci platformy Azure.

Aby użyć zasad sieciowych, można włączyć tę funkcję podczas tworzenia klastra usługi AKS. Nie można włączyć zasad sieciowych w istniejącym klastrze usługi AKS. Planuj z wyprzedzeniem upewnij się, możesz włączyć zasad sieciowych w klastrach i można ich używać, zgodnie z potrzebami. Zasady sieci należy używać tylko dla węzłów opartych na systemie Linux i zasobników w usłudze AKS.

Zasady sieci jest tworzona jako zasób usługi Kubernetes za pomocą YAML manifest. Zasady są stosowane do zasobników zdefiniowane, a następnie transferu danych przychodzących lub wychodzących reguły określają, jak ruch może przepływać. Poniższy przykład dotyczy zasobników za pomocą zasad sieciowych *aplikacji: zaplecza* etykietę stosowaną do nich. Reguła ruchu przychodzącego następnie tylko zezwala na ruch z zasobników z *aplikacji: frontonu* etykiety:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Aby rozpocząć korzystanie z zasad, zobacz [bezpieczny ruch między zasobników za pomocą zasad sieciowych w usłudze Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Bezpiecznie łącz się z węzłami przez host bastionu

**Najważniejsze wskazówki** — nie zezwalaj na połączenie zdalne do węzłów AKS. Utwórz host bastionu lub szybkiego dostępu w sieci wirtualnej zarządzania w oknie. Umożliwia bezpieczne kierować ruch do klastra usługi AKS w taki sposób, aby zadań zarządzania zdalnego hostem bastionu.

Można wykonać większość operacji w usłudze AKS za pomocą narzędzia do zarządzania platformy Azure lub za pośrednictwem serwera interfejsu API rozwiązania Kubernetes. Węzłów AKS nie są podłączone do publicznej sieci internet i są dostępne tylko w sieci prywatnej. Łączenie z węzłami i przeprowadzania konserwacji lub rozwiązywania problemów, kierowanie połączeń za pośrednictwem hostem bastionu też przeskoczyć pole. Ten host powinien być w oddzielnym zarządzania sieci wirtualnej, która bezpiecznie jest połączona z siecią wirtualną klastra AKS.

![Łączenie z węzłami usługi AKS przy użyciu hostem bastionu lub przejść pole](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Sieć zarządzania hostem bastionu powinien zostać zabezpieczony, zbyt. Użyj [usługi Azure ExpressRoute] [ expressroute] lub [bramy sieci VPN] [ vpn-gateway] nawiązać połączenie z siecią lokalną i kontrolować dostęp przy użyciu zabezpieczeń sieci grupy.

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na łączności sieciowej i zabezpieczeń. Aby uzyskać więcej informacji na temat podstawy sieci na platformie Kubernetes, zobacz [sieci pojęcia związane z aplikacjami w usłudze Azure Kubernetes Service (AKS)][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool