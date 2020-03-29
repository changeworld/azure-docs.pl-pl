---
title: Lista kontrolna gotowości do produkcji sieci szkieletowej usług Azure
description: Przygotuj aplikację sieci szkieletowej usług i produkcję klastra, postępając zgodnie z najlepszymi rozwiązaniami.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: 90d600b01aa870f7b3a58e70ef32e774e7107524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376804"
---
# <a name="production-readiness-checklist"></a>Lista kontrolna gotowości do produkcji

Czy aplikacja i klaster są gotowe do podjęcia ruchu produkcyjnego? Uruchamianie i testowanie aplikacji i klastra nie musi oznaczać, że jest gotowa do przejścia do produkcji. Utrzymuj bezproblemowe uruchamianie aplikacji i klastra, przechodząc przez następującą listę kontrolną. Zdecydowanie zalecamy, aby wszystkie te elementy zostały odewidencjonowane. Oczywiście można użyć alternatywnych rozwiązań dla określonego elementu zamówienia (na przykład własne struktury diagnostyczne).


## <a name="prerequisites-for-production"></a>Wymagania wstępne dotyczące produkcji
1. Najważniejsze wskazówki dotyczące sieci szkieletowej usług Azure: [Projektowanie aplikacji,](./service-fabric-best-practices-applications.md) [Bezpieczeństwo,](./service-fabric-best-practices-security.md) [Tworzenie sieci,](./service-fabric-best-practices-networking.md) [Planowanie i skalowanie pojemności,](./service-fabric-best-practices-capacity-scaling.md) [Infrastruktura jako kod](./service-fabric-best-practices-infrastructure-as-code.md)oraz Monitorowanie i [diagnostyka.](./service-fabric-best-practices-monitoring.md) 
1. Implementowanie konfiguracji zabezpieczeń Reliable Actors, jeśli używasz modelu programowania Aktorzy
1. W przypadku klastrów z więcej niż 20 rdzeniami lub 10 węzłami utwórz dedykowany typ węzła podstawowego dla usług systemowych. Dodaj [ograniczenia umieszczania,](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) aby zarezerwować typ węzła podstawowego dla usług systemowych.
1. Użyj jednostki SKU D2v2 lub wyższej dla typu węzła podstawowego. Zaleca się wybranie jednostki SKU o pojemności co najmniej 50 GB dysku twardego.
1. Klastry produkcyjne muszą być [bezpieczne](service-fabric-cluster-security.md). Aby uzyskać przykład konfigurowania bezpiecznego klastra, zobacz ten [szablon klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Używaj nazw pospolitych dla certyfikatów i unikaj używania certyfikatów z podpisem własnym.
1. Dodaj [ograniczenia zasobów dla kontenerów i usług,](service-fabric-resource-governance.md)aby nie zużywały więcej niż 75% zasobów węzła. 
1. Zrozumieć i ustawić [poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Srebrny lub wyższy poziom trwałości jest zalecane dla typów węzłów z obciążeniami stanowymi. Typ węzła podstawowego powinien mieć poziom trwałości ustawiony na Srebrny lub wyższy.
1. Zrozumienie i wybierz [poziom niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) typu węzła. Zaleca się niezawodność srebra lub wyższą.
1. Załaduj i skaluj testuj obciążenia, aby [zidentyfikować wymagania dotyczące pojemności](service-fabric-cluster-capacity.md) klastra. 
1. Usługi i aplikacje są monitorowane, a dzienniki aplikacji są generowane i przechowywane z alertami. Na przykład zobacz [Dodawanie rejestrowania do aplikacji sieci szkieletowej usług](service-fabric-how-to-diagnostics-log.md) i [kontenerów monitora z dziennikami usługi Azure Monitor](service-fabric-diagnostics-oms-containers.md).
1. Klaster jest monitorowany za pomocą alertów (na przykład z [dziennikami usługi Azure Monitor).](service-fabric-diagnostics-event-analysis-oms.md) 
1. Podstawowa infrastruktura zestawu skalowania maszyny wirtualnej jest monitorowana za pomocą alertów (na przykład za pomocą [dzienników usługi Azure Monitor](service-fabric-diagnostics-oms-agent.md).
1. Klaster ma [certyfikaty podstawowe i pomocnicze](service-fabric-cluster-security-update-certs-azure.md) zawsze (więc nie zostanie zablokowany).
1. Obsługa oddzielnych klastrów dla rozwoju, przemieszczania i produkcji. 
1. [Uaktualnienia aplikacji](service-fabric-application-upgrade.md) i [uaktualnienia klastra](service-fabric-tutorial-upgrade-cluster.md) są najpierw testowane w klastrach programowych i przemieszczania. 
1. Wyłącz automatyczne uaktualnienia w klastrach produkcyjnych i włącz go dla klastrów programistycznych i przemieszczania (w razie potrzeby wycofywanie). 
1. Ustal cel punktu odzyskiwania (RPO) dla usługi i skonfiguruj [proces odzyskiwania po awarii](service-fabric-disaster-recovery.md) i przetestuj go.
1. Planowanie [skalowania](service-fabric-cluster-scaling.md) klastra ręcznie lub programowo.
1. Planowanie [poprawek](service-fabric-patch-orchestration-application.md) węzłów klastra. 
1. Ustal potok ciągłej integracji/ciągłej integracji, aby najnowsze zmiany były stale testowane. Na przykład przy użyciu [usługi Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) lub [Usługi Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Przetestuj swój program & klastrów przejściowych pod obciążeniem za pomocą [usługi analizy błędów](service-fabric-testability-overview.md) i wywołaj kontrolowany [chaos.](service-fabric-controlled-chaos.md) 
1. Planowanie [skalowania](service-fabric-concepts-scalability.md) aplikacji. 


Jeśli używasz modelu programowania Niezawodne usługi sieci szkieletowej usług lub niezawodne podmioty, należy odeprzeć następujące elementy:
1. Uaktualnij aplikacje podczas tworzenia lokalnego, aby sprawdzić, `RunAsync` czy kod usługi honoruje token anulowania w metodzie i zamyka detektory komunikacji niestandardowej.
1. Unikaj [typowych pułapek](service-fabric-work-with-reliable-collections.md) podczas korzystania z niezawodnych kolekcji.
1. Monitoruj liczniki wydajności pamięci .NET CLR podczas uruchamiania testów obciążenia i sprawdź, czy nie ma wysokich stawek wyrzucania elementów bezużytecznych lub wzrostu sterty ucieczki.
1. Obsługa kopii zapasowej w trybie offline [niezawodnych usług i wiarygodnych aktorów](service-fabric-reliable-services-backup-restore.md) i przetestowanie procesu przywracania.
1. Liczba wystąpień maszyny wirtualnej typu podstawowego powinna być równa minimalnej dla warstwy niezawodności klastrów; warunki, gdy jest to właściwe, aby przekroczyć minimum warstwy obejmuje: tymczasowo podczas skalowania w pionie jednostki SKU zestaw skalowania maszyny wirtualnej podstawowego.

## <a name="optional-best-practices"></a>Opcjonalne najlepsze rozwiązania

Chociaż powyższe listy są warunkiem wstępnym, aby przejść do produkcji, należy również wziąć pod uwagę następujące pozycje:
1. Podłącz do [modelu kondycji sieci szkieletowej usług,](service-fabric-health-introduction.md) aby rozszerzyć wbudowaną ocenę kondycji i raportowanie.
1. Wdrażanie niestandardowego watchdog, który monitoruje aplikację i raportuje [obciążenie](service-fabric-cluster-resource-manager-metrics.md) do [równoważenia zasobów](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Następne kroki
* [Wdrażanie klastra systemu Windows sieci szkieletowej usług](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Wdrażanie klastra linuksa sieci szkieletowej usług](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.
