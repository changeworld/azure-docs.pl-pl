---
title: Konfiguracja sieci w Azure usługa Kubernetes (AKS)
description: Informacje o konfiguracji sieci podstawowe i Zaawansowane w Azure usługa Kubernetes (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/07/2018
ms.author: marsma
ms.openlocfilehash: 818bae2e05f6a3256ccbf0cbcc901dd337b9a260
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Konfiguracja sieci w Azure usługa Kubernetes (AKS)

Podczas tworzenia klastra usługi Kubernetes Azure (AKS), można wybrać z dwóch opcji sieciowych: **podstawowe** lub **zaawansowane**.

## <a name="basic-networking"></a>Podstawowe operacje sieciowe

**Podstawowe** sieci opcji jest konfiguracja domyślna dla AKS tworzenia klastra. Konfigurację sieci klastra i jego stanowiskami są całkowicie zarządzane przez usługę Azure i jest odpowiednia w przypadku wdrożeń, które nie wymagają niestandardowej konfiguracji sieci wirtualnej. Nie masz kontrolę nad konfiguracji sieci, takie jak podsieci lub adresu IP adresów zakresów przypisana do klastra, po wybraniu sieci podstawowej.

Węzły w klastrze AKS skonfigurowany do użycia sieci podstawowej [kubenet] [ kubenet] Kubernetes wtyczki.

## <a name="advanced-networking"></a>Zaawansowane funkcje sieciowe

**Zaawansowane** sieci umieszcza Twojej stanowiskami w sieci wirtualnej platformy Azure (VNet) skonfigurowanego, zapewniając ich automatyczne połączenie z zasobami sieci wirtualnej i integracja z zaawansowanej zestaw możliwości tej oferty sieci wirtualnych.
Zaawansowane funkcje sieciowe jest obecnie dostępny tylko podczas wdrażania AKS klastrów w [portalu Azure] [ portal] lub za pomocą szablonu usługi Resource Manager.

W węzłach klastra AKS skonfigurowany do użycia sieci zaawansowane [interfejsu sieciowego kontenera platformy Azure (CNI)] [ cni-networking] Kubernetes wtyczki.

![Diagram przedstawiający dwa węzły z mostków łączenie każdego do jednej sieci wirtualnej Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Zaawansowane funkcje sieciowe

Zaawansowane funkcje sieciowe zapewnia następujące korzyści:

* Wdrażanie klastra AKS do istniejącej sieci wirtualnej, lub utworzyć nowej sieci wirtualnej i podsieci dla klastra.
* Każdy pod w klastrze ma przypisany adres IP w sieci wirtualnej i może komunikować się bezpośrednio z innych stanowiskami w klastrze i innych maszyn wirtualnych w sieci wirtualnej.
* Pod można łączyć z innymi usługami w sieci wirtualnej peered i sieciami lokalnymi za pośrednictwem ExpressRoute, jak i lokacja lokacja (S2S) połączenia sieci VPN. Stanowiskami są również dostępne z lokalnymi.
* Ujawnia usługi Kubernetes zewnętrznie lub wewnętrznie przez usługi równoważenia obciążenia Azure. Również funkcji sieci podstawowej.
* Stanowiskami w podsieci, które mają włączone punkty końcowe usługi można bezpiecznego połączenia do usług platformy Azure, na przykład usługi Azure Storage i bazy danych SQL.
* Użyj zdefiniowane przez użytkownika tras (przez) można kierować ruchem z stanowiskami do urządzenia wirtualnego sieci.
* Stanowiskami można uzyskać dostępu do zasobów w publicznej sieci Internet. Również funkcji sieci podstawowej.

> [!IMPORTANT]
> Każdy węzeł w klastrze AKS skonfigurowane zaawansowane funkcje sieciowe mogą obsługiwać maksymalnie **30 stanowiskami**. Każda sieć wirtualną udostępnionymi w celu użycia z wtyczki Azure CNI jest ograniczona do **4096 adresy IP** (/ 20).

## <a name="configure-advanced-networking"></a>Skonfiguruj zaawansowane funkcje sieciowe

Gdy użytkownik [utworzyć klaster AKS](kubernetes-walkthrough-portal.md) w portalu Azure, można skonfigurować dla zaawansowane funkcje sieciowe są następujące parametry:

**Sieć wirtualna**: sieci wirtualnej, w której chcesz wdrożyć w klastrze Kubernetes. Jeśli chcesz utworzyć nową sieć wirtualną dla klastra, wybierz *Utwórz nowy* i postępuj zgodnie z instrukcjami *Utwórz sieć wirtualną* sekcji.

**Podsieci**: podsieci w sieci wirtualnej, w której chcesz wdrożyć w klastrze. Jeśli chcesz utworzyć nową podsieć w sieci wirtualnej klastra, wybierz *Utwórz nowy* i postępuj zgodnie z instrukcjami *Utwórz podsieć* sekcji.

**Zakres adresów usługi Kubernetes**: zakres adresów IP dla usługi klastrowania Kubernetes adresów IP. Ten zakres nie może być w zakresie adresów IP sieci wirtualnej klastra.

**Adres IP usługi Kubernetes DNS**: adres IP dla klastra usługi DNS. Ten adres musi znajdować się w *Kubernetes usługi zakres adresów*.

**Mostek docker adres**: adres IP i maski podsieci, można przypisać do mostka Docker. Ten adres IP nie musi być w zakresie adresów IP sieci wirtualnej klastra.

Poniższy zrzut ekranu portalu Azure przedstawiono przykład konfigurowania tych ustawień podczas tworzenia klastra AKS:

![Zaawansowana konfiguracja sieci w portalu Azure][portal-01-networking-advanced]

## <a name="plan-ip-addressing-for-your-cluster"></a>Zaplanuj adresowanie IP dla klastra

Klastry skonfigurowano zaawansowane funkcje sieciowe wymagają dodatkowe planowanie. Rozmiar sieci wirtualnej i jej podsieci należy uwzględnić liczbę stanowiskami, które mają być uruchamiane jednocześnie w klastrze, a także wymagania dotyczące skalowania.

Adresy IP stanowiskami i węzły klastra są przypisywane z określonej podsieci w sieci wirtualnej. Każdy węzeł jest skonfigurowany z podstawowego adresu IP, adres IP sam węzeł i 30 dodatkowe adresy IP wstępnie skonfigurowane przez Azure CNI przypisanych do stanowiskami zaplanowane do węzła. Gdy skalowanie klastra, każdy węzeł podobnie jest skonfigurowany z adresów IP podsieci.

Jak wspomniano wcześniej, każdej sieci wirtualnej udostępnionymi w celu użycia z wtyczki Azure CNI jest ograniczona do **4096 adresy IP** (/ 20). Każdy węzeł w klastrze, który został skonfigurowany na potrzeby zaawansowane funkcje sieciowe mogą obsługiwać maksymalnie **30 stanowiskami**.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Następujące pytania i odpowiedzi mają zastosowanie do **zaawansowane** konfiguracji sieci.

* *Zaawansowane funkcje sieciowe można skonfigurować z wiersza polecenia platformy Azure?*

  Nie. Zaawansowane funkcje sieciowe jest obecnie dostępny tylko podczas wdrażania AKS klastrów w portalu Azure lub za pomocą szablonu usługi Resource Manager.

* *W mojej podsieci klastra można wdrożyć maszyn wirtualnych?*

  Nie. Wdrażanie maszyn wirtualnych w podsieci używane przez klaster Kubernetes nie jest obsługiwane. Można wdrożyć maszyn wirtualnych, w tej samej sieci wirtualnej, ale w innej podsieci.

* *Można skonfigurować zasady sieciowe na pod?*

  Nie. Zasady sieciowe na pod są obecnie obsługiwane.

* *Jest możliwe do węzła można skonfigurować maksymalną liczbę stanowiskami?*

  Domyślnie każdy węzeł może obsługiwać maksymalnie 30 stanowiskami. Wartość maksymalna obecnie można zmienić tylko przez modyfikację `maxPods` właściwości podczas wdrażania klastra przy użyciu szablonu usługi Resource Manager.

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
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[aks-ssh]: aks-ssh.md
