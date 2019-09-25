---
title: Najlepsze rozwiązania dotyczące sieci Service Fabric platformy Azure | Microsoft Docs
description: Najlepsze rozwiązania związane z zarządzaniem Service Fabric sieciami.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 317977af9d41163013545a6e5f60bee887da596c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262246"
---
# <a name="networking"></a>Networking

Podczas tworzenia klastrów Service Fabric platformy Azure i zarządzania nimi można zapewnić łączność sieciową dla węzłów i aplikacji. Zasoby sieci obejmują zakresy adresów IP, sieci wirtualne, moduły równoważenia obciążenia i sieciowe grupy zabezpieczeń. W tym artykule przedstawiono najlepsze rozwiązania dotyczące tych zasobów.

Przejrzyj [wzorce sieci Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) platformy Azure, aby dowiedzieć się, jak tworzyć klastry korzystające z następujących funkcji: Istniejąca sieć wirtualna lub podsieć, statyczny publiczny adres IP, moduł równoważenia obciążenia wyłącznie wewnętrznie lub wewnętrzny i zewnętrzny moduł równoważenia obciążenia.

## <a name="infrastructure-networking"></a>Sieć infrastruktury
Zmaksymalizuj wydajność maszyny wirtualnej za pomocą przyspieszonej sieci, deklarując Właściwość enableAcceleratedNetworking w szablonie Menedżer zasobów, Poniższy fragment kodu jest NetworkInterfaceConfigurations zestawu skalowania maszyn wirtualnych. Włącza przyspieszone sieci:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Klaster Service Fabric może być inicjowany w systemie [Linux przy użyciu przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)i [systemu Windows z przyspieszoną siecią](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Obsługiwane są przyspieszone sieci dla jednostek SKU serii maszyn wirtualnych platformy Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 i MS/MMS. Przyspieszona sieć została pomyślnie przetestowana przy użyciu jednostki SKU Standard_DS8_v3 na 1/23/2019 Service Fabric dla klastra systemu Windows, a przy użyciu Standard_DS12_v2 w systemie 01/29/2019 dla klastra Service Fabric Linux.

Aby włączyć przyspieszone sieci w istniejącym klastrze Service Fabric, należy najpierw [skalować klaster Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), aby wykonać następujące czynności:
1. Inicjowanie obsługi administracyjnej NodeType z włączoną obsługą przyspieszonej sieci
2. Migrowanie usług i ich stanu do aprowizacji NodeType z włączoną obsługą przyspieszonej sieci

Skalowanie w górę infrastruktury jest wymagane do włączenia przyspieszonej sieci w istniejącym klastrze, ponieważ włączenie przyspieszonej sieci może spowodować przestoje, ponieważ wymaga ono [zatrzymania i cofnięcia przydziału wszystkich maszyn wirtualnych w zestawie dostępności Włączanie przyspieszonej sieci na wszystkich istniejących KARTAch](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)sieciowych.

## <a name="cluster-networking"></a>Sieć klastrów

* Klastry Service Fabric można wdrożyć w istniejącej sieci wirtualnej, wykonując czynności opisane w temacie [Service Fabric wzorców sieci](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są zalecane w przypadku typów węzłów, które ograniczają ruch przychodzący i wychodzący do klastra. Upewnij się, że wymagane porty są otwarte w sieciowej grupy zabezpieczeń. Na przykład: ![Service Fabric reguł sieciowej grupy zabezpieczeń][NSGSetup]

* Typ węzła podstawowego, który zawiera Service Fabric usług systemu nie musi być narażony za pośrednictwem zewnętrznego modułu równoważenia obciążenia i może być narażony przez [wewnętrzny moduł równoważenia obciążenia](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Użyj [statycznego publicznego adresu IP](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) dla klastra.

## <a name="application-networking"></a>Sieć aplikacji

* Aby można było uruchomić obciążenia kontenerów systemu Windows, należy użyć [trybu otwartej sieci](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) do ułatwienia komunikacji między usługami.

* Użyj zwrotnego serwera proxy, takiego jak [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) lub [Service Fabric zwrotnego serwera proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) w celu udostępnienia typowych portów aplikacji, takich jak 80 lub 443.

* W przypadku kontenerów systemu Windows hostowanych na maszynach gapped powietrznych, które nie mogą pobierać warstw podstawowych z magazynu w chmurze platformy Azure, Zastąp zachowanie warstwy obcej przy użyciu flagi [--Allow-undystrybucyjne-artefakty](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) w demona platformy Docker.

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
