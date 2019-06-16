---
title: Zestawy skalowania maszyn wirtualnych i typy węzłów usługi Service Fabric platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zestawy węzłów usługi Azure Service Fabric, których typy odnoszą się do skalowania maszyn wirtualnych i sposób zdalnego nawiązywania skalowania Ustaw wystąpienia lub węzła klastra.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: a5f8735df2b230de2b0ddcdcccff09430bada9e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684691"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych i typy węzłów usługi Service Fabric platformy Azure
[Zestawy skalowania maszyn wirtualnych](/azure/virtual-machine-scale-sets) to zasób obliczeniowy platformy Azure. Zestawy skalowania można użyć do wdrożenia i zarządzania kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który definiuje w klastrze usługi Azure Service Fabric konfiguruje oddzielne skali.  Środowisko uruchomieniowe usługi Service Fabric, zainstalowana na każdej maszynie wirtualnej w zestawie przy użyciu rozszerzenia maszyny wirtualnej Microsoft.Azure.ServiceFabric skalowania. Można niezależnie skalować każdy typ węzła w górę lub w dół, zmiana jednostki SKU systemu operacyjnego, uruchomione w każdym węźle klastra, mają różne zestawy otwartych portów i użyj różne metryki pojemności.

Na poniższej ilustracji przedstawiono klastra, który ma dwa typy węzłów, o nazwie frontonu i zaplecza. Każdy typ węzła złożony z pięciu węzłów.

![Klaster, który ma dwa typy węzłów][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapowanie wystąpień zestawu skalowania maszyn wirtualnych do węzłów
Jak pokazano na poprzednim rysunku, wystąpień zestawu skalowania rozpoczynają się od wystąpienia 0, a dopiero potem zwiększyć o 1. Numerowanie znajduje odzwierciedlenie w nazwy węzłów. Na przykład węzeł BackEnd_0 jest wystąpienie 0 zaplecza zestawu skalowania. Ten zestaw skalowania w szczególności ma pięć wystąpień, o nazwie BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 i BackEnd_4.

Skalowanie w górę zestaw skalowania, tworzone jest nowe wystąpienie. Nazwa nowego wystąpienia zestawu skalowania jest zazwyczaj zestawu skalowania, nazwy i dalej liczby wystąpień. W naszym przykładzie jest BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapowanie usługi równoważenia obciążenia zestawu skalowania na typy węzłów i zestawów skalowania
Jeśli wdrożeniu klastra w witrynie Azure portal lub użyć przykładowego szablonu usługi Azure Resource Manager, są wyświetlane wszystkie zasoby w grupie zasobów. Możesz zobaczyć modułów równoważenia obciążenia dla każdego zestawu lub węzeł typu skali. Nazwa modułu równoważenia obciążenia używa następującego formatu: **LB -&lt;Nazwa typu węzła&gt;** . Przykładem jest LB-sfcluster4doc-0, jak pokazano na poniższej ilustracji:

![Zasoby][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Rozszerzenia maszyny wirtualnej usługi Service Fabric
Rozszerzenia maszyny wirtualnej usługi Service Fabric jest używana do uruchamiania usługi Service Fabric na maszynach wirtualnych platformy Azure i skonfigurować rozwiązanie Security węzła.

Poniżej przedstawiono fragment rozszerzenia maszyny wirtualnej usługi w sieci szkieletowej:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Poniżej przedstawiono opisy właściwości:

| **Nazwa** | **Dozwolone wartości** | ** --- ** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
| name | string | --- | Unikatowa nazwa rozszerzenia |
| type | "ServiceFabricLinuxNode" lub "ServiceFabricWindowsNode | --- | Identyfikuje system operacyjny usługi Service Fabric jest uruchamianie do |
| autoUpgradeMinorVersion | wartość PRAWDA lub FAŁSZ | --- | Włącz automatyczne uaktualnianie środowiska uruchomieniowego SF wersje pomocnicze |
| publisher | Microsoft.Azure.ServiceFabric | --- | Nazwa wydawcy rozszerzenie usługi Service Fabric |
| clusterEndpont | string | --- | URI:port do punktu końcowego zarządzania |
| elementu nodeTypeRef | string | --- | Nazwa elementu nodeType |
| wartość durabilityLevel | brązowy, srebrny, złoty platynowy | --- | czas, mogą wstrzymać niezmienna infrastruktura platformy Azure |
| enableParallelJobs | wartość PRAWDA lub FAŁSZ | --- | Włącz ParallelJobs obliczeniowe, takie jak usuwanie maszyny Wirtualnej i uruchom ponownie maszynę Wirtualną w tej samej skali ustawiony w sposób równoległy |
| nicPrefixOverride | string | --- | Prefiks podsieci, takich jak "10.0.0.0/24" |
| commonNames | ciąg] | --- | Nazwy pospolite certyfikatów zainstalowanych klastra |
| x509StoreName | string | --- | Nazwa Store, w którym znajduje się certyfikat zainstalowany klastra |
| typeHandlerVersion | 1.1 | --- | Wersja rozszerzenia. Zaleca się, aby uaktualnić 1.1 1.0 klasycznej wersji rozszerzenia |

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [omówienie funkcji "Wdrażanie w dowolnym miejscu" oraz porównanie ich z klastrami usługi Azure managed](service-fabric-deploy-anywhere.md).
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* [Połączenie zdalne](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) do wystąpienia w zestawie skalowania określonych
* [Aktualizowanie wartości zakresu portów protokołu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) w klastrze maszyn wirtualnych po wdrożeniu
* [Zmień nazwę i hasło administratora](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) klastra maszyn wirtualnych

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
