---
title: Typy węzłów i zestawy skalowania maszyn wirtualnych
description: Dowiedz się, jak typy węzłów usługi Azure Service łączą się z zestawami skalowania maszyn wirtualnych i jak zdalnie łączyć się z wystąpieniem zestawu skalowania lub węzłem klastra.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199720"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Typy węzłów sieci szkieletowej usługi Azure i zestawy skalowania maszyny wirtualnej

[Zestawy skalowania maszyny wirtualnej](/azure/virtual-machine-scale-sets) są zasobem obliczeniowym platformy Azure. Zestawy skalowania można używać do wdrażania i zarządzania kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła zdefiniowany w klastrze sieci szkieletowej usługi Azure konfiguruje dokładnie jeden zestaw skalowania: wiele typów węzłów nie może być obsługiwanych przez ten sam zestaw skalowania, a jeden typ węzła nie powinien (w większości przypadków) być wspierany przez wiele zestawów skalowania. Wyjątek stanowi rzadka sytuacja [skalowania pionowego](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) typu węzła, gdy tymczasowo masz `nodeTypeRef` dwa zestawy skalowania o tej samej wartości, podczas gdy repliki są migrowane z oryginalnego do uaktualnionego zestawu skalowania.

Środowisko uruchomieniowe sieci szkieletowej usług jest instalowane na każdej maszynie wirtualnej w skali ustawionej przez rozszerzenie maszyny wirtualnej *Microsoft.Azure.ServiceFabric.* Można niezależnie skalować każdy typ węzła w górę lub w dół, zmienić jednostkę SKU systemu operacyjnego działającą w każdym węźle klastra, mieć otwarte różne zestawy portów i używać różnych metryk pojemności.

Na poniższej ilustracji przedstawiono klaster, który ma dwa typy węzłów o nazwie *FrontEnd* i *BackEnd*. Każdy typ węzła ma pięć węzłów.

![Klaster z dwoma typami węzłów][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapowanie wystąpień zestawu skalowania maszyny wirtualnej na węzły

Jak pokazano na powyższej ilustracji, wystąpienia zestawu skalowania rozpoczynają się od wystąpienia 0, a następnie zwiększają się o 1. Numerowanie jest odzwierciedlone w nazwach węzłów. Na przykład węzeł BackEnd_0 jest wystąpienie 0 zestawu skalowania zaplecza. Ten zestaw skali określonego ma pięć wystąpień, o nazwach BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 i BackEnd_4.

Podczas skalowania w górę zestawu skalowania tworzone jest nowe wystąpienie. Nowa nazwa wystąpienia zestawu skalowania zazwyczaj jest nazwą zestawu skalowania oraz numerem następnego wystąpienia. W naszym przykładzie jest to BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Skala mapy zestaw modułów równoważenia obciążenia do typów węzłów i zestawów skalowania

Jeśli wdrożono klaster w witrynie Azure portal lub użyto przykładowego szablonu usługi Azure Resource Manager, zostaną wyświetlone wszystkie zasoby w ramach grupy zasobów. Można zobaczyć moduły równoważenia obciążenia dla każdego zestawu skalowania lub typu węzła. Nazwa modułu równoważenia obciążenia używa następującego formatu: **nazwa&lt;&gt;typu węzła LB**. Przykładem jest LB-sfcluster4doc-0, jak pokazano na poniższym rysunku:

![Resources][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Rozszerzenie maszyny wirtualnej sieci szkieletowej usług

Rozszerzenie maszyny wirtualnej sieci szkieletowej usługi jest używane do uruchamiania sieci szkieletowej usługi azure i konfigurowania zabezpieczeń węzła.

Poniżej znajduje się fragment rozszerzenia maszyny wirtualnej sieci szkieletowej usługi:

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
         "dataPath": "D:\\\\SvcFab",
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

| **Nazwa** | **Dozwolone wartości** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
| name | ciąg | Unikatowa nazwa rozszerzenia |
| type | "ServiceFabricLinuxNode" lub "ServiceFabricWindowsNode" | Identyfikuje sieci szkieletowej usług systemu operacyjnego jest boottrapping do |
| autoUpgradeMinorVersion | true lub false | Włącz automatyczne uaktualnianie wersji pomocniczych środowiska wykonawczego SF |
| wydawca | Microsoft.Azure.ServiceFabric | Nazwa wydawcy rozszerzenia sieci szkieletowej usług |
| clusterEndpont | ciąg | Identyfikator URI:PORT do punktu końcowego zarządzania |
| węzełTypeRef | ciąg | Nazwa węzłaType |
| trwałośćPoziom | brąz, srebro, złoto, platyna | Czas dozwolony na wstrzymanie niezmiennej infrastruktury platformy Azure |
| włączParallelJobs | true lub false | Włącz compute ParallelJobs, takie jak usuwanie maszyn wirtualnych i ponowne uruchamianie maszyny Wirtualnej w tej samej skali ustawionej równolegle |
| nianiasekcja | ciąg | Prefiks podsieci w stylu "10.0.0.0/24" |
| commonNames (nazwy wspólne) | ciąg[] | Nazwy zwykłe zainstalowanych certyfikatów klastra |
| x509Sklepname | ciąg | Nazwa sklepu, w którym znajduje się zainstalowany certyfikat klastra |
| typHandlerVersion | 1.1 | Wersja rozszerzenia. 1.0 klasyczna wersja rozszerzenia zaleca się uaktualnienie do wersji 1.1 |
| ścieżka danych | ciąg | Ścieżka do dysku używanego do zapisywania stanu usług systemu sieci szkieletowej usług i danych aplikacji.

## <a name="next-steps"></a>Następne kroki

* Zobacz [omówienie funkcji "Wdrażanie w dowolnym miejscu" i porównanie z klastrami zarządzanymi przez platformę Azure.](service-fabric-deploy-anywhere.md)
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* [Zdalne łączenie się z](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) określonym wystąpieniem zestawu skalowania
* [Aktualizowanie wartości zakresu portów RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) na maszynach wirtualnych klastra po wdrożeniu
* [Zmienianie nazwy użytkownika i hasła administratora](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) dla maszyn wirtualnych klastra

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
