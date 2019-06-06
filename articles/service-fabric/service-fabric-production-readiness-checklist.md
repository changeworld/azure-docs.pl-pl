---
title: Lista kontrolna dotycząca usługi Azure Service Fabric produkcji gotowości | Dokumentacja firmy Microsoft
description: Przygotowanie aplikacji usługi Service Fabric i klastra w środowisku produkcyjnym przez następujące najlepsze rozwiązania.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/05/2019
ms.author: aljo
ms.openlocfilehash: a75b02b8173507a28204a3ec2030ce7ed9838495
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729860"
---
# <a name="production-readiness-checklist"></a>Lista kontrolna gotowości do produkcji

Jest gotowy do zastąpienia jest przesyłany ruch produkcyjny aplikacji i klastra? Uruchamiając i testując aplikację i klaster nie musi oznaczać, że jest gotowa do użytku w środowisku produkcyjnym. Zachowaj aplikacji i klastra z systemem sprawnie, korzystając z poniższej listy kontrolnej. Zdecydowanie zaleca się te elementy powinny zostać zaznaczone. Oczywiście można użyć alternatywnych rozwiązań dla konkretnego elementu wiersza (na przykład własnych struktur diagnostyki).


## <a name="prerequisites-for-production"></a>Wymagania wstępne dla środowiska produkcyjnego
1. Usługa Azure Service Fabric najlepsze rozwiązania: [Projekt aplikacji](./service-fabric-best-practices-applications.md), [zabezpieczeń](./service-fabric-best-practices-security.md), [sieć](./service-fabric-best-practices-networking.md), [do planowania wydajności i skalowania](./service-fabric-best-practices-capacity-scaling.md), [infrastruktura jako kod](./service-fabric-best-practices-infrastructure-as-code.md), i [monitorowania i diagnostyki](./service-fabric-best-practices-monitoring.md). 
1. Implementuj konfiguracji zabezpieczeń elementów Reliable Actors przy użyciu aktorów model programowania
1. Dla klastrów liczących więcej niż 20 rdzeni lub 10 węzłów utworzyć typ dedykowanych węzła podstawowego dla usług systemowych. Dodaj [ograniczeniami dotyczącymi umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) zarezerwować typu węzła podstawowego dla usług systemowych.
1. Za pomocą D2v2 wyższej wersji jednostki SKU dla typu węzła podstawowego. Zalecane jest pobranie jednostki SKU o co najmniej 50 GB dysk twardy pojemności.
1. Musi być klastrów produkcyjnych [bezpiecznego](service-fabric-cluster-security.md). Na przykład Konfigurowanie zabezpieczonego klastra zobacz [szablonu klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Użyj nazwy pospolite certyfikatów i certyfikaty z podpisem własnym należy unikać.
1. Dodaj [ograniczenia zasobów dotyczące kontenerów i usług](service-fabric-resource-governance.md), dzięki czemu nie zajmują więcej niż 75% węzła zasobów. 
1. Zrozumienie i ustaw [poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Typy węzłów obciążeń stanowych zaleca trwałości Silver lub wyższego poziomu. Podstawowy typ węzła powinny mieć poziom trwałości, ustaw Silver lub wyższej.
1. Informacje, a następnie wybierz [poziom niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) typu węzła. Zaleca się niezawodność Silver lub wyższego.
1. Obciążenia i skalowanie Testuj obciążenia w taki sposób, aby zidentyfikować [wymagania dotyczące pojemności](service-fabric-cluster-capacity.md) dla klastra. 
1. Usługi i aplikacje są monitorowane i dzienniki aplikacji są generowane i przechowywane w usłudze alertów. Na przykład zobacz [Dodawanie rejestrowania aplikacji usługi Service Fabric](service-fabric-how-to-diagnostics-log.md) i [monitorowania kontenerów za pomocą usługi Azure Monitor dzienników](service-fabric-diagnostics-oms-containers.md).
1. Klaster jest monitorowana przy użyciu alertów (na przykład za pomocą [dzienniki usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)). 
1. Podstawowa infrastruktura zestawu skalowania maszyn wirtualnych odbywa się za pomocą alertów (na przykład za pomocą [dzienniki usługi Azure Monitor](service-fabric-diagnostics-oms-agent.md).
1. Klaster ma [głównego i dodatkowego certyfikatu](service-fabric-cluster-security-update-certs-azure.md) zawsze (dzięki czemu można nie uzyskać blokady).
1. Obsługa osobne klastry do rozwoju, przejściowe i produkcyjne. 
1. [Uaktualnienia aplikacji](service-fabric-application-upgrade.md) i [klastra uaktualnień](service-fabric-tutorial-upgrade-cluster.md) są testowane w rozwoju i najpierw przemieszczania klastrów. 
1. Wyłączanie automatycznych uaktualnień w klastrach produkcyjnych i włącz ją dla rozwoju i przemieszczania klastrów (Wycofaj w razie potrzeby). 
1. Ustanowić cel punktu odzyskiwania (RPO) dla usługi i konfigurowanie [proces odzyskiwania po awarii](service-fabric-disaster-recovery.md) i przetestować działanie.
1. Planowanie [skalowanie](service-fabric-cluster-scaling.md) klastra ręcznie lub programowo.
1. Planowanie [poprawek](service-fabric-patch-orchestration-application.md) węzły klastra. 
1. Tak, aby najnowsze zmiany są testowane stale, należy ustanowić potoku CI/CD. Na przykład za pomocą [DevOps platformy Azure](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) lub [usługi Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Programowanie i przemieszczania klastrów w ramach obciążenia za pomocą testów [usługa analizy błędów](service-fabric-testability-overview.md) i wywoływać kontrolowanego [chaos](service-fabric-controlled-chaos.md). 
1. Planowanie [skalowanie](service-fabric-concepts-scalability.md) aplikacji. 


Jeśli używasz modelu programowania usług Reliable Services usługi Service Fabric lub elementów Reliable Actors następujące elementy muszą zostać zaznaczone:
1. Uaktualnianie aplikacji podczas programowania lokalnego, aby sprawdzić, czy kodu usługi jest zapewniane token anulowania w `RunAsync` metody i zamykanie odbiorników komunikacji niestandardowych.
1. Należy unikać [typowych pułapek](service-fabric-work-with-reliable-collections.md) przy użyciu niezawodnych kolekcji.
1. Monitorowanie wydajności pamięci środowiska .NET CLR liczników podczas uruchamiania testów obciążenia i sprawdź, czy wysoki stopień wyrzucania elementów bezużytecznych lub wzrost braków sterty.
1. Obsługa kopia zapasowa offline [usług Reliable Services i Reliable Actors](service-fabric-reliable-services-backup-restore.md) i przetestuj proces przywracania.
1. Liczba wystąpień usługi podstawowej maszyny wirtualnej NodeType najlepiej powinien być równy co najmniej w warstwie niezawodność klastrów; zawiera warunki, w stosownych przypadkach przekracza minimalne warstwy: tymczasowo po pionowo skalowanie podstawowe elementy NodeType maszyn wirtualnych skalowania Ustaw jednostki SKU.

## <a name="optional-best-practices"></a>Opcjonalne najlepszych rozwiązań

Mimo że powyższe listy warunków wstępnych, aby przejść do środowiska produkcyjnego, należy również rozważyć następujące elementy:
1. Podłącz do [model kondycji usługi Service Fabric](service-fabric-health-introduction.md) ocenę kondycji wbudowanych i raportowania.
1. Wdrażanie niestandardowego strażnika, który jest monitorowanie aplikacji i raporty [obciążenia](service-fabric-cluster-resource-manager-metrics.md) dla [równoważenia zasobów](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Kolejne kroki
* [Wdrażanie klastra usługi Service Fabric Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Wdrażanie klastra usługi Service Fabric systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.
