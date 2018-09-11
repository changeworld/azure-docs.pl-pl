---
title: Lista kontrolna dotycząca usługi Azure Service Fabric produkcji gotowości | Dokumentacja firmy Microsoft
description: Przygotowanie aplikacji usługi Service Fabric i klastra w środowisku produkcyjnym przez następujące najlepsze rozwiązania.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: subramar
ms.openlocfilehash: 6ebe83794f23ed3cf637f668d2810b9ba7521084
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295874"
---
# <a name="production-readiness-checklist"></a>Lista kontrolna gotowości do produkcji

Jest gotowy do zastąpienia jest przesyłany ruch produkcyjny aplikacji i klastra? Uruchamiając i testując aplikację i klaster nie musi oznaczać, że jest gotowa do użytku w środowisku produkcyjnym. Zachowaj aplikacji i klastra z systemem sprawnie, korzystając z poniższej listy kontrolnej. Zdecydowanie zaleca się te elementy powinny zostać zaznaczone. Oczywiście można użyć alternatywnych rozwiązań dla konkretnego elementu wiersza (na przykład własnych struktur diagnostyki).


## <a name="pre-requisites-for-production"></a>Wymagania wstępne dla środowiska produkcyjnego

1. Dla klastrów liczących więcej niż 20 rdzeni lub 10 węzłów utworzyć typ dedykowanych węzła podstawowego dla usług systemowych. Dodaj [ograniczeniami dotyczącymi umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) zarezerwować typu węzła podstawowego dla usług systemowych. 
2. Za pomocą D2v2 wyższej wersji jednostki SKU dla typu węzła podstawowego. Zalecane jest pobranie jednostki SKU o co najmniej 50 GB dysk twardy pojemności.
2. Musi być klastrów produkcyjnych [bezpiecznego](service-fabric-cluster-security.md). Na przykład Konfigurowanie zabezpieczonego klastra zobacz [szablonu klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Użyj nazwy pospolite certyfikatów i certyfikaty z podpisem własnym należy unikać.
4. Dodaj [ograniczenia zasobów dotyczące kontenerów i usług](service-fabric-resource-governance.md), dzięki czemu nie zajmują więcej niż 75% węzła zasobów. 
5. Zrozumienie i ustaw [poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Typy węzłów obciążeń stanowych zaleca trwałości Silver lub wyższego poziomu. Podstawowy typ węzła powinny mieć poziom trwałości, ustaw Silver lub wyższej.
6. Informacje, a następnie wybierz [poziom niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) typu węzła. Zaleca się niezawodność Silver lub wyższego.
7. Obciążenia i skalowanie Testuj obciążenia w taki sposób, aby zidentyfikować [wymagania dotyczące pojemności](service-fabric-cluster-capacity.md) dla klastra. 
8. Usługi i aplikacje są monitorowane i dzienniki aplikacji są generowane i przechowywane w usłudze alertów. Na przykład zobacz [Dodawanie rejestrowania aplikacji usługi Service Fabric](service-fabric-how-to-diagnostics-log.md) i [monitorowania kontenerów za pomocą usługi Log Analytics](service-fabric-diagnostics-oms-containers.md).
9. Klaster jest monitorowana przy użyciu alertów (na przykład za pomocą [OMS](service-fabric-diagnostics-event-analysis-oms.md)). 
10. Podstawowa infrastruktura zestawu skalowania maszyn wirtualnych odbywa się za pomocą alertów (na przykład za pomocą [usługi Log Analytics](service-fabric-diagnostics-oms-agent.md).
11. Klaster ma [głównego i dodatkowego certyfikatu](service-fabric-cluster-security-update-certs-azure.md) zawsze (dzięki czemu można nie uzyskać blokady).
12. Obsługa osobne klastry do rozwoju, przejściowe i produkcyjne. 
13. [Uaktualnienia aplikacji](service-fabric-application-upgrade.md) i [klastra uaktualnień](service-fabric-tutorial-upgrade-cluster.md) są testowane w rozwoju i najpierw przemieszczania klastrów. 
14. Wyłączanie automatycznych uaktualnień w klastrach produkcyjnych i włącz ją dla rozwoju i przemieszczania klastrów (Wycofaj w razie potrzeby). 
15. Ustanowić cel punktu odzyskiwania (RPO) dla usługi i konfigurowanie [proces odzyskiwania po awarii](service-fabric-disaster-recovery.md) i przetestować działanie.
16. Planowanie [skalowanie](service-fabric-cluster-scaling.md) klastra ręcznie lub programowo.
17. Planowanie [poprawek](service-fabric-patch-orchestration-application.md) węzły klastra. 
18. Tak, aby najnowsze zmiany są testowane stale, należy ustanowić potoku CI/CD. Na przykład za pomocą [DevOps platformy Azure](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) lub [usługi Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
19. Programowanie i przemieszczania klastrów w ramach obciążenia za pomocą testów [usługa analizy błędów](service-fabric-testability-overview.md) i wywoływać kontrolowanego [chaos](service-fabric-controlled-chaos.md). 
20. Planowanie [skalowanie](service-fabric-concepts-scalability.md) aplikacji. 


Jeśli używasz modelu programowania usług Reliable Services usługi Service Fabric lub elementów Reliable Actors następujące elementy muszą zostać zaznaczone:
21. Uaktualnianie aplikacji podczas programowania lokalnego, aby sprawdzić, czy kodu usługi jest zapewniane token anulowania w `RunAsync` metody i zamykanie odbiorników komunikacji niestandardowych.
22. Należy unikać [typowych pułapek](service-fabric-work-with-reliable-collections.md) przy użyciu niezawodnych kolekcji.
23. Monitorowanie wydajności pamięci środowiska .NET CLR liczników podczas uruchamiania testów obciążenia i sprawdź, czy wysoki stopień wyrzucania elementów bezużytecznych lub wzrost braków sterty.
24. Obsługa kopia zapasowa offline [usług Reliable Services i Reliable Actors](service-fabric-reliable-services-backup-restore.md) i przetestuj proces przywracania. 


## <a name="optional-best-practices"></a>Opcjonalne najlepszych rozwiązań

Mimo że powyższe listy warunków wstępnych, aby przejść do środowiska produkcyjnego, należy również rozważyć następujące elementy:
25. Podłącz do [model kondycji usługi Service Fabric](service-fabric-health-introduction.md) ocenę kondycji wbudowanych i raportowania.
26. Wdrażanie niestandardowego strażnika, który jest monitorowanie aplikacji i raporty [obciążenia](service-fabric-cluster-resource-manager-metrics.md) dla [równoważenia zasobów](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Kolejne kroki
* [Wdrażanie klastra usługi Service Fabric Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Wdrażanie klastra usługi Service Fabric systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.
