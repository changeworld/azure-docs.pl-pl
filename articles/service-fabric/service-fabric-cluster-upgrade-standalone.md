---
title: Uaktualnianie klastra autonomicznego usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Więcej informacji na temat uaktualniania wersji lub konfiguracji klastra autonomicznego usługi Azure Service Fabric.  T
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 1d96a2e81917af5e80bb847ea25610ccb71ad70f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711060"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Uaktualnianie i aktualizowanie klastra autonomicznego usługi Service Fabric

Dla każdego nowoczesnego systemu projektowanie pod kątem możliwość jest kluczem do osiągnięcia długoterminowym sukcesie produktu. Klaster autonomiczny usługi Azure Service Fabric jest zasobem, którego jesteś właścicielem. W tym artykule opisano, jakie można ją uaktualnić lub zaktualizować.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrolowanie wersja sieci szkieletowej, która działa w klastrze
Upewnij się, że klaster jest zawsze uruchamiany [obsługiwana wersja usługi Service Fabric](service-fabric-versions.md). Microsoft zapowiada wydaniu nowej wersji usługi Service Fabric, poprzednia wersja jest oznaczony do zakończenia wsparcia po co najmniej 60 dni od daty anonsu. Nowe wersje są anonsowane [na blogu zespołu usługi Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nowa wersja jest dostępna do wyboru, w tym momencie.

Można ustawić do klastra, aby otrzymywać uaktualnienia sieci szkieletowej automatyczne, zgodnie z ich wydaniu przez firmę Microsoft lub ręcznym wybraniu pozycji wersji obsługiwanych w sieci szkieletowej, że chcesz użyć do klastra, aby znajdować się na. Aby uzyskać więcej informacji, przeczytaj [uaktualniania wersji usługi Service Fabric, który działa w klastrze](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Dostosowywanie ustawień konfiguracji

Wielu różnych [ustawienia konfiguracji](service-fabric-cluster-manifest.md) można ustawić w *ClusterConfig.json* plików, takich jak poziom niezawodności właściwości klastra i węzła.  Aby dowiedzieć się więcej, przeczytaj [uaktualnić konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).  Wiele innych, bardziej zaawansowane ustawienia można również dostosować.  Aby uzyskać więcej informacji, przeczytaj [ustawień sieci szkieletowej klastra usługi Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Zdefiniuj właściwości węzła
Czasami możesz chcieć upewnij się, że niektóre obciążenia uruchamiane tylko na niektóre rodzaje węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesorów GPU lub dyski SSD, podczas gdy inne nie. Dla każdego z typów węzłów w klastrze można dodać właściwości niestandardowe węzła do węzłów klastra. Ograniczeniami dotyczącymi umieszczania są instrukcje dołączone do poszczególnych usług, które wybierz co najmniej jedną właściwość węzła. Ograniczeniami dotyczącymi umieszczania definiują, gdzie należy uruchamiać usług.

Szczegółowe informacje dotyczące korzystania z ograniczeniami dotyczącymi umieszczania, właściwości węzła i sposób definiowania ich można odczytać [— właściwości węzła i ograniczeniami dotyczącymi umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Dodawanie metryki pojemności
Dla każdego typu węzła możesz dodać niestandardowe metryki pojemności, którą chcesz używać w aplikacjach w celu raportowania obciążenia. Szczegółowe informacje dotyczące użycia metryki pojemności w celu raportowania obciążenia można odwoływać się do dokumentów, Menedżer zasobów klastra usługi w sieci szkieletowej na [opisujący Twój klaster](service-fabric-cluster-resource-manager-cluster-description.md) i [metryki i obciążenia](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Stosowanie poprawek systemu operacyjnego w węzłach klastra
Aplikacja orchestration patch (POA) jest aplikacja usługi Service Fabric, który automatyzuje systemu operacyjnego poprawek w klastrze usługi Service Fabric, bez przestojów. [Patch Orchestration Application dla Windows](service-fabric-patch-orchestration-application.md) można wdrożyć w klastrze do instalowania poprawek w zorganizowany sposób przy jednoczesnym zachowaniu usług dostępnych przez cały czas. 


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak dostosować niektóre [usługi ustawień sieci szkieletowej klastra Service fabric](service-fabric-cluster-fabric-settings.md)
* Dowiedz się, jak [skalowanie klastra w poziomie i pionie](service-fabric-cluster-scale-up-down.md)
* Dowiedz się więcej o [uaktualnień aplikacji](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
