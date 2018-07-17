---
title: Konfiguracja sieci w usłudze Azure Kubernetes Service (AKS)
description: Więcej informacji o konfiguracji sieci podstawowe i Zaawansowane w usłudze Azure Kubernetes Service (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/15/2018
ms.author: marsma
ms.openlocfilehash: da78d388c8e9fc9684942342f48902c2a248e3b1
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072303"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Konfiguracja sieci w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia klastra usługi Azure Kubernetes Service (AKS), możesz wybrać spośród dwóch opcji sieci: **podstawowe** lub **zaawansowane**.

## <a name="basic-networking"></a>Podstawowe operacje sieciowe

**Podstawowe** sieci — opcja jest domyślnie skonfigurowany do tworzenia klastra AKS. Konfigurację sieci klastra i jego zasobników odbywa się całkowicie na platformie Azure i jest odpowiednia w przypadku wdrożeń, które nie wymagają niestandardowej konfiguracji sieci wirtualnej. Nie masz kontrolę nad konfiguracją sieci, takie jak podsieci lub adres IP zakresów przypisane do klastra, po wybraniu podstawowe operacje sieciowe.

Węzły w klastrze AKS skonfigurowany dla podstawowych sieci [wtyczki kubenet] [ kubenet] wtyczka platformy Kubernetes.

## <a name="advanced-networking"></a>Zaawansowane funkcje sieciowe

**Zaawansowane** sieci umieszcza zasobników w usługi Azure Virtual Network (VNet), można skonfigurować, dostarczając im automatyczne połączenie z zasobami sieci wirtualnej i integracji bogaty zbiór możliwości tę ofertę sieci wirtualnych.
Zaawansowane funkcje sieciowe jest dostępna podczas wdrażania usługi AKS klastrów przy użyciu [witryny Azure portal][portal], wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Resource Manager.

Węzły w klastrze AKS skonfigurowane dla zaawansowanych sieci [wtyczki Azure Container Networking interfejsu (CNI)] [ cni-networking] wtyczka platformy Kubernetes.

![Diagram przedstawiający dwa węzły o mostki łączenia każdego do pojedynczej sieci wirtualnej platformy Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Zaawansowane funkcje sieciowe

Zaawansowane funkcje sieciowe zapewnia następujące korzyści:

* Wdrażanie klastra usługi AKS w istniejącej sieci wirtualnej lub utworzyć nowe sieci wirtualnej i podsieci dla klastra.
* Każdy zasobnik w klastrze ma przypisany adres IP w sieci wirtualnej i może komunikować się bezpośrednio z innymi zasobników w klastrze i innych węzłów w sieci wirtualnej.
* Zasobnik można połączyć z innymi usługami w wirtualnej sieci równorzędnej i sieciami lokalnymi za pośrednictwem usługi ExpressRoute i lokacja lokacja (S2S) połączenia sieci VPN. Zasobniki są również dostępne ze środowiska lokalnego.
* Udostępnianie zewnętrznie lub wewnętrznie usługa Kubernetes za pomocą usługi Azure Load Balancer. Także funkcja podstawowe operacje sieciowe.
* Zasobników w podsieci, które mają włączone punkty końcowe usługi można bezpiecznie łączyć się z usługami platformy Azure, na przykład Azure Storage i bazą danych SQL.
* Użyj trasy zdefiniowane przez użytkownika (UDR) do kierowania ruchu z zasobników do wirtualnego urządzenia sieciowego.
* Zasobników dostęp do zasobów w publicznym Internecie. Także funkcja podstawowe operacje sieciowe.

> [!IMPORTANT]
> Każdy węzeł w klastrze AKS skonfigurowany na potrzeby zaawansowanego sieci może obsługiwać maksymalnie **30 zasobników** w przypadku skonfigurowania w witrynie Azure portal.  Maksymalna wartość można zmienić tylko przez modyfikację właściwości maxPods w przypadku wdrażania klastra za pomocą szablonu usługi Resource Manager. Każda sieć wirtualna aprowizowanych dla użycia za pomocą wtyczki wtyczki Azure CNI jest ograniczona do **4096 skonfigurowane adresy IP**.

## <a name="advanced-networking-prerequisites"></a>Zaawansowane sieci wymagań wstępnych

* Sieć wirtualną dla klastra AKS muszą zezwalać na łączność z Internetem ruchu wychodzącego.
* Nie należy tworzyć więcej niż jednego klastra AKS w tej samej podsieci.
* Zaawansowane funkcje sieciowe dla usługi AKS nie obsługuje sieci wirtualnych korzystających z platformy Azure prywatne strefy DNS.
* Nie wolno korzystać z klastrów AKS `169.254.0.0/16`, `172.30.0.0/16`, lub `172.31.0.0/16` dla rozwiązania Kubernetes usługi zakresu adresów.
* Musi mieć nazwę główną usługi używane dla klastra usługi AKS `Contributor` uprawnienia do grupy zasobów zawierające istniejącej sieci wirtualnej.

## <a name="plan-ip-addressing-for-your-cluster"></a>Planowanie adresowania IP dla klastra

Klastrach skonfigurowanych przy użyciu zaawansowane funkcje sieciowe wymagają dodatkowego planowania. Rozmiar Twojej sieci wirtualnej i jej podsieci musi obsłużyć zarówno liczbę zasobników, które mają być uruchamiane, jak i liczby węzłów klastra.

Adresy IP dla zasobników i węzły klastra są przypisywane z określonej podsieci w sieci wirtualnej. Każdy węzeł jest skonfigurowany za pomocą podstawowego adresu IP, czyli adres IP węzła i 30 dodatkowych adresów IP wstępnie skonfigurowane przez wtyczki Azure CNI, które są przypisane do zasobników zaplanowane do węzła. Gdy możesz skalować w klastrze, każdy węzeł podobnie jest skonfigurowany z adresów IP z podsieci.

Planowanie adres IP klastra AKS składa się z siecią wirtualną, co najmniej jedną podsieć dla węzłów i zasobników i zakres adresów usługi Kubernetes.

| Zakres adresów / Azure zasobów | Limity i zmianę rozmiaru |
| --------- | ------------- |
| Sieć wirtualna | Sieci wirtualnej platformy Azure może być tak duże jak /8, ale może być tylko 4096 skonfigurowano adresów IP. |
| Podsieć | Musi być wystarczająco duży, aby pomieścić węzłów i zasobników. Aby obliczyć rozmiar minimalny podsieci: (liczba węzłów) + (liczba węzłów * zasobników na węzeł). W przypadku klastra 50 węzła: (50) + (50 * 30) = 1550, podsieć musi być /21 lub większy. |
| Zakres adresów usługi Kubernetes | Ten zakres nie należy używanych przez dowolny element sieci lub podłączone do tej sieci wirtualnej. Usługa adres CIDR musi być mniejszy niż /12. |
| Adres IP usługi DNS platformy Kubernetes | Adres IP w ramach rozwiązania Kubernetes usługi zakres adresów, który będzie używany przez odnajdywanie usługi klastrowania (klastra kubernetes w usłudze dns). |
| Adres mostka platformy docker | Adres IP (w notacji CIDR), używane jako mostka platformy Docker adresu IP w węzłach. Domyślnie 172.17.0.1/16. |

Jak wspomniano wcześniej, każda sieć wirtualna aprowizowanych dla użycia za pomocą wtyczki wtyczki Azure CNI jest ograniczona do **4096 skonfigurowane adresy IP**. Każdy węzeł w klastrze, który został skonfigurowany na potrzeby zaawansowanego sieci może obsługiwać maksymalnie **30 zasobników**.

## <a name="deployment-parameters"></a>Parametry wdrożenia

Podczas tworzenia klastra usługi AKS, można skonfigurować, aby uzyskać zaawansowane funkcje sieciowe są następujące parametry:

**Sieć wirtualna**: sieci wirtualnej, w której chcesz wdrożyć klaster usługi Kubernetes. Aby utworzyć nową sieć wirtualną dla klastra, należy zaznaczyć *Utwórz nową* i postępuj zgodnie z instrukcjami w *Utwórz sieć wirtualną* sekcji.

**Podsieci**: podsieci w sieci wirtualnej, w której chcesz wdrożyć w klastrze. Jeśli chcesz utworzyć nową podsieć w sieci wirtualnej dla klastra, wybierz opcję *Utwórz nową* i postępuj zgodnie z instrukcjami w *Utwórz podsieć* sekcji.

**Zakres adresów usługi platformy Kubernetes**: *zakresu adresów usługi platformy Kubernetes* jest zakresem adresów IP, z którego adresy są przypisane do usługi Kubernetes w klastrze (Aby uzyskać więcej informacji na temat usługi Kubernetes, zobacz [ Usługi] [ services] w dokumentacji usługi Kubernetes).

Zakres adresów IP usługi Kubernetes:

* Nie należy do zakresu adresów IP w sieci wirtualnej klastra
* Nie może pokrywać z innymi sieciami wirtualnymi za pomocą którego prowadzi komunikację równorzędną sieci wirtualnej klastra
* Nie nakładać się na wszystkie adresy IP w środowisku lokalnym

Jeśli nakładających się zakresów adresów IP są używane, może spowodować nieprzewidywalne zachowanie. Na przykład jeśli Zasobnik podejmie próbę dostępu do adresu IP spoza klastra, który IP również ma miejsce IP usługi, można napotkać nieprzewidywalne zachowanie i błędów.

**Adres IP usługi DNS platformy Kubernetes**: adres IP usługi DNS klastra. Adres ten musi być *zakresu adresów usługi platformy Kubernetes*.

**Adres mostka platformy docker**: adres IP i maska sieci można przypisać do mostka platformy Docker. Ten adres IP nie musi być w zakresie adresów IP w sieci wirtualnej klastra.

## <a name="configure-networking---cli"></a>Konfigurowanie sieci — interfejs wiersza polecenia

Podczas tworzenia klastra usługi AKS przy użyciu wiersza polecenia platformy Azure, można również skonfigurować zaawansowane funkcje sieciowe. Użyj następujących poleceń, aby utworzyć nowy klaster AKS za pomocą zaawansowanych funkcji sieciowych włączone.

Najpierw Pobierz identyfikator zasobu podsieci dla podsieci istniejących, do której zostaną dołączone klaster AKS:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Użyj [tworzenie az aks] [ az-aks-create] polecenia `--network-plugin azure` argumentu, aby utworzyć klaster z zaawansowany siecią. Aktualizacja `--vnet-subnet-id` wartość Identyfikatora podsieci zebranych w poprzednim kroku:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Konfigurowanie sieci — portal

Poniższy zrzut ekranu z witryny Azure portal przedstawia przykład Konfigurowanie tych ustawień podczas tworzenia klastra AKS:

![Zaawansowana konfiguracja sieci w witrynie Azure portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Zestaw poniższych pytań i odpowiedzi dotyczą **zaawansowane** konfiguracji sieci.

* *W mojej podsieci klastra można wdrożyć maszyny wirtualne?*

  Nie. Wdrażanie maszyn wirtualnych w podsieci używane przez klaster Kubernetes nie jest obsługiwane. Maszyny wirtualne można wdrażać w tej samej sieci wirtualnej, ale w innej podsieci.

* *Można skonfigurować zasad sieciowych na zasobnik?*

  Nie. Zasady sieciowe na zasobnik są obecnie obsługiwane.

* *Maksymalna liczba zasobników jest możliwy do wdrożenia na węzeł można skonfigurować?*

  Domyślnie każdy węzeł może obsługiwać maksymalnie 30 zasobników. Maksymalna wartość można zmienić tylko przez modyfikację `maxPods` właściwości w przypadku wdrażania klastra za pomocą szablonu usługi Resource Manager.

* *Jak skonfigurować dodatkowe właściwości dla podsieci, utworzonego podczas tworzenia klastra AKS Na przykład punktów końcowych usługi.*

  Pełną listę właściwości dla sieci wirtualnej i podsieci, które możesz utworzyć podczas tworzenia klastra AKS można skonfigurować w standardowej strony konfiguracji sieci wirtualnej w witrynie Azure portal.

## <a name="next-steps"></a>Kolejne kroki

### <a name="networking-in-aks"></a>Obsługa sieci w usłudze AKS

Dowiedz się więcej na temat sieci w usłudze AKS w następujących artykułach:

[Używanie statycznego adresu IP z modułem równoważenia obciążenia Azure Kubernetes Service (AKS)](static-ip.md)

[Ruch przychodzący protokołu HTTPS w usłudze Azure Container Service (AKS)](ingress.md)

[Użyj wewnętrznego modułu równoważenia obciążenia za pomocą usługi Azure Container Service (AKS)](internal-lb.md)

### <a name="acs-engine"></a>Aparat ACS

[Aparat usługi kontenera platformy Azure (aparat ACS)] [ acs-engine] to projekt typu open source, który generuje szablony usługi Azure Resource Manager umożliwia wdrażanie klastrów z obsługą platformy Docker na platformie Azure. Kubernetes, DC/OS, Swarm tryb i Swarm koordynatorów można wdrożyć przy użyciu aparatu usługi ACS.

Klastry Kubernetes, utworzone za pomocą aparatu usługi ACS obsługują zarówno [wtyczki kubenet] [ kubenet] i [wtyczki Azure CNI] [ cni-networking] wtyczek. W efekcie podstawowe i zaawansowane scenariusze sieciowe są obsługiwane przez aparat ACS.

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
