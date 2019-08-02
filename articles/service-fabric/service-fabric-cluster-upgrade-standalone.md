---
title: Uaktualnianie autonomicznego klastra platformy Azure Service Fabric | Microsoft Docs
description: Dowiedz się więcej o uaktualnianiu wersji lub konfiguracji autonomicznego klastra platformy Azure Service Fabric.  T
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: bf99d5d59354745508d8ca88abfc4b42fe608025
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599800"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Uaktualnianie i aktualizowanie Service Fabric autonomicznego klastra

W przypadku każdego nowoczesnego systemu projektowanie pod kątem wydajności jest kluczem do osiągnięcia długotrwałego sukcesu produktu. Autonomiczny klaster usługi Azure Service Fabric to zasób, którego jesteś członkiem. W tym artykule opisano, co można uaktualnić lub zaktualizować.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrolowanie wersji sieci szkieletowej działającej w klastrze
Upewnij się, że w klastrze jest zawsze uruchomiona [obsługiwana wersja Service Fabric](service-fabric-versions.md). Gdy firma Microsoft ogłasza wydanie nowej wersji Service Fabric, poprzednia wersja zostanie oznaczona do końca wsparcia po upływie co najmniej 60 dni od daty powiadomienia. Nowe wersje są ogłaszane [na blogu zespołu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nowa wersja jest dostępna do wyboru w tym momencie.

Można ustawić, aby klaster odbierał automatyczne uaktualnienia sieci szkieletowej, gdy zostaną wydane przez firmę Microsoft, lub można ręcznie wybrać obsługiwaną wersję sieci szkieletowej, w której ma być włączony klaster. Aby uzyskać więcej informacji, przeczytaj artykuł [Uaktualnij wersję Service Fabric działającą w klastrze](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Dostosuj ustawienia konfiguracji

W pliku *ClusterConfig. JSON* można ustawić wiele różnych [ustawień konfiguracji](service-fabric-cluster-manifest.md) , takich jak poziom niezawodności klastra i właściwości węzła.  Aby dowiedzieć się więcej, przeczytaj artykuł [Uaktualnij konfigurację klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).  Wiele innych, bardziej zaawansowane, można również dostosować ustawienia.  Aby uzyskać więcej informacji, Przeczytaj [Service Fabric ustawienia sieci szkieletowej klastra](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definiowanie właściwości węzła
Czasami warto upewnić się, że pewne obciążenia są uruchamiane tylko na niektórych typach węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesora GPU lub dysków SSD, a inne mogą nie być. Dla każdego z typów węzłów w klastrze można dodać niestandardowe właściwości węzła do węzłów klastra. Ograniczenia umieszczania to instrukcje dołączone do poszczególnych usług, które wybierają jedną lub więcej właściwości węzła. Ograniczenia umieszczania definiują, gdzie mają być uruchamiane usługi.

Aby uzyskać szczegółowe informacje na temat używania ograniczeń umieszczania, właściwości węzła i sposobu ich definiowania, Odczytaj [właściwości węzła i ograniczenia umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Dodawanie metryk pojemności
Dla każdego typu węzła można dodać niestandardowe metryki pojemności, które mają być używane w aplikacjach do raportowania obciążenia. Aby uzyskać szczegółowe informacje na temat używania metryk pojemności do raportowania obciążenia, zapoznaj się z klastrem Service Fabric Menedżer zasobów dokumenty dotyczące [opisywania klastra](service-fabric-cluster-resource-manager-cluster-description.md) i [metryk i obciążenia](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Poprawianie systemu operacyjnego w węzłach klastra
Aplikacja aranżacji patch (POA) jest aplikacją Service Fabric, która automatyzuje stosowanie poprawek systemu operacyjnego w klastrze Service Fabric bez przestojów. [Aplikacja aranżacji poprawek dla systemu Windows](service-fabric-patch-orchestration-application.md) może zostać wdrożona w klastrze w celu zainstalowania poprawek w zorganizowany sposób, dzięki czemu usługi są dostępne przez cały czas. 


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak dostosować niektóre [Ustawienia sieci szkieletowej klastra usługi Service Fabric](service-fabric-cluster-fabric-settings.md)
* Dowiedz się [, jak skalować klaster w i na zewnątrz](service-fabric-cluster-scale-up-down.md)
* Informacje o [uaktualnieniach aplikacji](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
