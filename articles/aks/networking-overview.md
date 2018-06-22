---
title: Konfiguracja sieci w Azure usługa Kubernetes (AKS)
description: Informacje o konfiguracji sieci podstawowe i Zaawansowane w Azure usługa Kubernetes (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/15/2018
ms.author: marsma
ms.openlocfilehash: 207accc30e10c4e2bed5b713fc59e2f9ad86a876
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309848"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Konfiguracja sieci w Azure usługa Kubernetes (AKS)

Podczas tworzenia klastra usługi Kubernetes Azure (AKS), można wybrać z dwóch opcji sieciowych: **podstawowe** lub **zaawansowane**.

## <a name="basic-networking"></a>Podstawowe operacje sieciowe

**Podstawowe** sieci opcji jest konfiguracja domyślna dla AKS tworzenia klastra. Konfigurację sieci klastra i jego stanowiskami są całkowicie zarządzane przez usługę Azure i jest odpowiednia w przypadku wdrożeń, które nie wymagają niestandardowej konfiguracji sieci wirtualnej. Nie masz kontrolę nad konfiguracji sieci, takie jak podsieci lub adresu IP adresów zakresów przypisana do klastra, po wybraniu sieci podstawowej.

Węzły w klastrze AKS skonfigurowany do użycia sieci podstawowej [kubenet] [ kubenet] Kubernetes wtyczki.

## <a name="advanced-networking"></a>Zaawansowane funkcje sieciowe

**Zaawansowane** sieci umieszcza Twojej stanowiskami w sieci wirtualnej platformy Azure (VNet) skonfigurowanego, zapewniając ich automatyczne połączenie z zasobami sieci wirtualnej i integracja z zaawansowanej zestaw możliwości tej oferty sieci wirtualnych.
Zaawansowane funkcje sieciowe jest dostępne, gdy wdrażanie AKS klastry [portalu Azure][portal], Azure CLI lub za pomocą szablonu usługi Resource Manager.

W węzłach klastra AKS skonfigurowany do użycia sieci zaawansowane [interfejsu sieciowego kontenera platformy Azure (CNI)] [ cni-networking] Kubernetes wtyczki.

![Diagram przedstawiający dwa węzły z mostków łączenie każdego do jednej sieci wirtualnej Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Zaawansowane funkcje sieciowe

Zaawansowane funkcje sieciowe zapewnia następujące korzyści:

* Wdrażanie klastra AKS do istniejącej sieci wirtualnej, lub utworzyć nowej sieci wirtualnej i podsieci dla klastra.
* Każdy pod w klastrze ma przypisany adres IP w sieci wirtualnej i może komunikować się bezpośrednio z innych stanowiskami w klastrze, a inne węzły w sieci wirtualnej.
* Pod można łączyć z innymi usługami w sieci wirtualnej peered i sieciami lokalnymi za pośrednictwem ExpressRoute, jak i lokacja lokacja (S2S) połączenia sieci VPN. Stanowiskami są również dostępne z lokalnymi.
* Ujawnia usługi Kubernetes zewnętrznie lub wewnętrznie przez usługi równoważenia obciążenia Azure. Również funkcji sieci podstawowej.
* Stanowiskami w podsieci, które mają włączone punkty końcowe usługi można bezpiecznego połączenia do usług platformy Azure, na przykład usługi Azure Storage i bazy danych SQL.
* Użyj zdefiniowane przez użytkownika tras (przez) można kierować ruchem z stanowiskami do urządzenia wirtualnego sieci.
* Stanowiskami można uzyskać dostępu do zasobów w publicznej sieci Internet. Również funkcji sieci podstawowej.

> [!IMPORTANT]
> Każdy węzeł w klastrze AKS skonfigurowane zaawansowane funkcje sieciowe mogą obsługiwać maksymalnie **30 stanowiskami** po skonfigurowaniu przy użyciu portalu Azure.  Maksymalna wartość można zmienić tylko modyfikując właściwość maxPods podczas wdrażania klastra przy użyciu szablonu usługi Resource Manager. Każda sieć wirtualną udostępnionymi w celu użycia z wtyczki Azure CNI jest ograniczona do **4096 skonfigurowane adresy IP**.

## <a name="advanced-networking-prerequisites"></a>Zaawansowany wymagania wstępne dotyczące sieci

* Sieć wirtualna klastra AKS muszą zezwalać na wychodzące połączenie z Internetem.
* Nie należy tworzyć więcej niż jednego klastra AKS w tej samej podsieci.
* Zaawansowane funkcje sieciowe dla AKS nie obsługuje sieci wirtualnych, który korzystania ze stref DNS prywatnego Azure.
* AKS klastrów nie mogą używać `169.254.0.0/16`, `172.30.0.0/16`, lub `172.31.0.0/16` dla Kubernetes usługi zakresu adresów.
* Musi mieć nazwę główną usługi używane dla klastra AKS `Contributor` uprawnień do grupy zasobów, zawierającą istniejącej sieci wirtualnej.

## <a name="plan-ip-addressing-for-your-cluster"></a>Zaplanuj adresowanie IP dla klastra

Klastry skonfigurowano zaawansowane funkcje sieciowe wymagają dodatkowe planowanie. Rozmiar Twojej sieci wirtualnej i jej podsieci musi obsłużyć zarówno liczbę stanowiskami, który ma zostać uruchomiona, a także liczby węzłów klastra.

Adresy IP stanowiskami i węzły klastra są przypisywane z określonej podsieci w sieci wirtualnej. Każdy węzeł jest skonfigurowany z podstawowego adresu IP, który jest adres IP węzła i 30 dodatkowe adresy IP wstępnie skonfigurowane przez Azure CNI przypisanych do stanowiskami zaplanowane do węzła. Gdy skalowanie klastra, każdy węzeł podobnie jest skonfigurowany z adresów IP podsieci.

Plan adresów IP dla klastra AKS składa się z sieci wirtualnej, co najmniej jedną podsieć dla węzłów i stanowiskami, a dla zakresu adresów usługi Kubernetes.

| Zakres adresów / Azure zasobów | Ograniczenia i zmiana rozmiaru |
| --------- | ------------- |
| Sieć wirtualna | Sieć wirtualna Azure może być możliwie jak /8, ale mogły tylko 4096 skonfigurowane adresy IP. |
| Podsieć | Musi być wystarczająco duży, aby zmieścił się w węzłach i stanowiskami. Aby obliczyć rozmiar minimalny podsieci: (liczba węzłów) + (liczba węzłów * stanowiskami w każdym węźle). 50 węzłów klastra: (50) + (50 * 30) = 1550, musi być większy lub /21 podsieć. |
| Zakres adresów Kubernetes usługi | Ten zakres nie powinien używanych przez dowolny element sieci lub podłączone do tej sieci wirtualnej. Adres usługi CIDR musi być mniejsza niż /12. |
| Adres IP usługi Kubernetes DNS | Adres IP w ramach Kubernetes usługi zakres adresów, które będą używane przez odnajdywanie usługi klastrowania (kube dns). |
| Adres Mostek docker | Adres IP (w notacji CIDR) używane jako mostka Docker adresu IP na węzłach. Domyślnie 172.17.0.1/16. |

Jak wspomniano wcześniej, każdej sieci wirtualnej udostępnionymi w celu użycia z wtyczki Azure CNI jest ograniczona do **4096 skonfigurowane adresy IP**. Każdy węzeł w klastrze, który został skonfigurowany na potrzeby zaawansowane funkcje sieciowe mogą obsługiwać maksymalnie **30 stanowiskami**.

## <a name="deployment-parameters"></a>Parametry wdrożenia

Podczas tworzenia klastra AKS, można skonfigurować dla zaawansowane funkcje sieciowe są następujące parametry:

**Sieć wirtualna**: sieci wirtualnej, w której chcesz wdrożyć w klastrze Kubernetes. Jeśli chcesz utworzyć nową sieć wirtualną dla klastra, wybierz *Utwórz nowy* i postępuj zgodnie z instrukcjami *Utwórz sieć wirtualną* sekcji.

**Podsieci**: podsieci w sieci wirtualnej, w której chcesz wdrożyć w klastrze. Jeśli chcesz utworzyć nową podsieć w sieci wirtualnej klastra, wybierz *Utwórz nowy* i postępuj zgodnie z instrukcjami *Utwórz podsieć* sekcji.

**Zakres adresów usługi Kubernetes**: *Kubernetes usługi zakres adresów* jest zakres adresów IP, z którego adresy są przypisane do usługi Kubernetes w klastrze (Aby uzyskać więcej informacji dotyczących usług Kubernetes, zobacz [ Usługi] [ services] w dokumentacji Kubernetes).

Zakres adresów IP usługi Kubernetes:

* Nie może być w zakresie adresów IP sieci wirtualnej klastra
* Nie należy nakłada się żadnych innych sieci wirtualnych, z którym równorzędnymi użytkownikami klastra sieci wirtualnej
* Należy nie nakładać się na wszystkie adresy IP lokalnych

Może spowodować nieprzewidywalne zachowanie, jeśli są używane nakładających się zakresów IP. Na przykład jeśli pod próbuje uzyskać dostęp IP poza klastrem, a wykonywanej IP również być IP usługi, mogą pojawić błędy i nieprzewidywalne zachowanie.

**Adres IP usługi Kubernetes DNS**: adres IP dla klastra usługi DNS. Ten adres musi znajdować się w *Kubernetes usługi zakres adresów*.

**Mostek docker adres**: adres IP i maski podsieci, można przypisać do mostka Docker. Ten adres IP nie musi być w zakresie adresów IP sieci wirtualnej klastra.

## <a name="configure-networking---cli"></a>Konfigurowanie sieci — interfejsu wiersza polecenia

Podczas tworzenia klastra AKS z wiersza polecenia platformy Azure, można również skonfigurować zaawansowane funkcje sieciowe. Użyj następujących poleceń do utworzenia nowego klastra AKS z zaawansowanych funkcji sieci włączone.

Najpierw Pobierz identyfikator zasobu podsieci na istniejącą podsieć, do której dołączy AKS klastra:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Użyj [utworzyć az aks] [ az-aks-create] z `--network-plugin azure` argumentu, aby utworzyć klaster z zaawansowanych w sieci. Aktualizacja `--vnet-subnet-id` wartość Identyfikatora podsieci zebrane w poprzednim kroku:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Konfigurowanie sieci — portal

Poniższy zrzut ekranu portalu Azure przedstawiono przykład konfigurowania tych ustawień podczas tworzenia klastra AKS:

![Zaawansowana konfiguracja sieci w portalu Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Następujące pytania i odpowiedzi mają zastosowanie do **zaawansowane** konfiguracji sieci.

* *Zaawansowane funkcje sieciowe można skonfigurować z wiersza polecenia platformy Azure?*

  Nie. Zaawansowane funkcje sieciowe jest obecnie dostępny tylko podczas wdrażania AKS klastrów w portalu Azure lub za pomocą szablonu usługi Resource Manager.

* *W mojej podsieci klastra można wdrożyć maszyn wirtualnych?*

  Nie. Wdrażanie maszyn wirtualnych w podsieci używane przez klaster Kubernetes nie jest obsługiwane. Można wdrożyć maszyn wirtualnych, w tej samej sieci wirtualnej, ale w innej podsieci.

* *Można skonfigurować zasady sieciowe na pod?*

  Nie. Zasady sieciowe na pod są obecnie obsługiwane.

* *Jest możliwe do węzła można skonfigurować maksymalną liczbę stanowiskami?*

  Domyślnie każdy węzeł może obsługiwać maksymalnie 30 stanowiskami. Maksymalna wartość można zmienić tylko przez modyfikację `maxPods` właściwości podczas wdrażania klastra przy użyciu szablonu usługi Resource Manager.

* *Jak skonfigurować dodatkowe właściwości dla tej podsieci, utworzony podczas tworzenia klastra AKS? Na przykład punktów końcowych usługi.*

  Pełną listę właściwości dla sieci wirtualnej i podsieci, które utworzono podczas tworzenia klastra AKS można skonfigurować na stronie standardowa konfiguracja sieci wirtualnej w portalu Azure.

## <a name="next-steps"></a>Kolejne kroki

### <a name="networking-in-aks"></a>Sieć w AKS

Dowiedz się więcej o technologiach sieciowych w AKS zawierają następujące artykuły:

[Użyj statycznego adresu IP usługi równoważenia obciążenia Azure Kubernetes usługi (AKS)](static-ip.md)

[Ruch przychodzący HTTPS na usługi kontenera platformy Azure (AKS)](ingress.md)

[Użyj wewnętrznego modułu równoważenia obciążenia z usługi kontenera platformy Azure (AKS)](internal-lb.md)

### <a name="acs-engine"></a>Aparat ACS

[Aparat usługi kontenera platformy Azure (aparat ACS)] [ acs-engine] to projekt open source, który generuje szablony usługi Azure Resource Manager umożliwia wdrażanie klastrów włączone Docker na platformie Azure. Orchestrators Kubernetes, DC/OS i Swarm tryb oraz Swarm można wdrożyć z aparatem ACS.

Klastry Kubernetes, utworzone za pomocą aparatu ACS obsługują zarówno [kubenet] [ kubenet] i [Azure CNI] [ cni-networking] wtyczek. W efekcie podstawowe i zaawansowane scenariusze sieciowe są obsługiwane przez aparat ACS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: aks-ssh.md
