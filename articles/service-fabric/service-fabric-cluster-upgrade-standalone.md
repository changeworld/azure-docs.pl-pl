---
title: Uaktualnianie autonomicznego klastra usługi Azure Service Fabric
description: Dowiedz się więcej o uaktualnianiu wersji lub konfiguracji autonomicznego klastra usługi Azure Service Fabric.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 6da9b4c6890895141ecc419382f05f667614fb31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451824"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Uaktualnianie i aktualizowanie autonomicznego klastra sieci szkieletowej usług

Dla każdego nowoczesnego systemu, projektowanie dla modernizacji jest kluczem do osiągnięcia długoterminowego sukcesu produktu. Klaster autonomicznej sieci szkieletowej usługi Azure to zasób, którego jesteś właścicielem. W tym artykule opisano, co można uaktualnić lub zaktualizować.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Sterowanie wersją sieci szkieletowej, która działa w klastrze
Upewnij się, że klaster zawsze uruchamia [obsługiwaną wersję sieci szkieletowej usług](service-fabric-versions.md). Gdy firma Microsoft ogłosi wydanie nowej wersji sieci szkieletowej usług, poprzednia wersja jest oznaczona do końca pomocy technicznej po co najmniej 60 dniach od daty ogłoszenia. Nowe wydania są ogłaszane [na blogu zespołu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nowa wersja jest dostępna do wyboru w tym momencie.

Można ustawić klaster tak, aby odbierał automatyczne uaktualnienia sieci szkieletowej w miarę ich zwalniania przez firmę Microsoft lub ręcznie wybierać obsługiwaną wersję sieci szkieletowej, w której ma być włączony klaster. Aby uzyskać więcej informacji, zobacz [Uaktualnianie wersji sieci szkieletowej usług, która działa w klastrze](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Dostosowywanie ustawień konfiguracji

W pliku *ClusterConfig.json* można ustawić wiele różnych [ustawień konfiguracji,](service-fabric-cluster-manifest.md) takich jak poziom niezawodności właściwości klastra i węzła.  Aby dowiedzieć się więcej, przeczytaj [artykuł Uaktualnianie konfiguracji autonomicznego klastra](service-fabric-cluster-config-upgrade-windows-server.md).  Wiele innych, bardziej zaawansowanych ustawień można również dostosować.  Aby uzyskać więcej informacji, zobacz [Ustawienia sieci szkieletowej klastra sieci szkieletowej sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definiowanie właściwości węzła
Czasami można upewnić się, że niektóre obciążenia są uruchamiane tylko na niektórych typach węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesorów GPU lub dysków SSD, podczas gdy inne nie. Dla każdego z typów węzłów w klastrze można dodać niestandardowe właściwości węzłów do węzłów klastra. Ograniczenia umieszczania są instrukcje dołączone do poszczególnych usług, które wybierają dla jednego lub więcej właściwości węzła. Ograniczenia umieszczania określają, gdzie usługi powinny być uruchamiane.

Aby uzyskać szczegółowe informacje na temat stosowania ograniczeń umieszczania, właściwości węzła i sposobu ich definiowania, przeczytaj [właściwości węzła i ograniczenia umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Dodawanie wskaźników pojemności
Dla każdego z typów węzłów można dodać niestandardowe metryki pojemności, których chcesz użyć w aplikacjach do raportowania obciążenia. Szczegółowe informacje na temat używania metryk pojemności do raportowania obciążenia można znaleźć w programie Dokumenty Menedżera zasobów klastra sieci szkieletowej usług w sprawie [opisu klastra](service-fabric-cluster-resource-manager-cluster-description.md) i [metryk oraz ładowania](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Poprawianie systemu operacyjnego w węzłach klastra
Aplikacja aranżacji poprawek (POA) to aplikacja sieci szkieletowej usług, która automatyzuje łatanie systemu operacyjnego w klastrze sieci szkieletowej usług bez przestojów. [Aplikacja aranżacji poprawek dla systemu Windows](service-fabric-patch-orchestration-application.md) można wdrożyć w klastrze, aby zainstalować poprawki w sposób zorganizowany, przy jednoczesnym zachowaniu usług dostępnych przez cały czas. 


## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak dostosować niektóre [ustawienia sieci szkieletowej klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md)
* Dowiedz się, jak [skalować klaster w](service-fabric-cluster-scale-up-down.md)
* Dowiedz się więcej o [uaktualnieniach aplikacji](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
