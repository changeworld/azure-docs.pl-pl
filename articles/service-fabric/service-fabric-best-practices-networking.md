---
title: Usługa Azure Service Fabric sieć najlepsze rozwiązania | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące zarządzania sieci usługi Service Fabric.
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
ms.openlocfilehash: d221b828624e649a0d04a89c4394fe5a7fa857dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237319"
---
# <a name="networking"></a>Networking

Podczas tworzenia i zarządzania klastrami usługi Azure Service Fabric są zapewnienie łączności sieciowej dla węzłów i aplikacji. Zasoby sieciowe, obejmują zakresy adresów IP, sieci wirtualne, usługi równoważenia obciążenia i sieciowych grup zabezpieczeń. W tym artykule dowiesz się, najlepsze rozwiązania dotyczące tych zasobów.

Przejrzyj Azure [usługi Service Fabric sieć wzorców](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) informacje na temat tworzenia klastrów, które korzystają z następujących funkcji: Moduł równoważenia obciążenia istniejącej sieci wirtualnej lub podsieci, statyczny publiczny adres IP, moduł równoważenia obciążenia tylko do wewnętrznego lub wewnętrzne i zewnętrzne.

## <a name="infrastructure-networking"></a>Infrastruktura sieci
Maksymalizuj wydajność maszyny wirtualnej z przyspieszonej sieci, deklarując właściwości enableAcceleratedNetworking w szablonie usługi Resource Manager, poniższy fragment kodu NetworkInterfaceConfigurations zestawu skalowania maszyn wirtualnych, Włącza przyspieszonej sieci:

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
Klaster usługi Service Fabric mogą być udostępniane na [systemu Linux przy użyciu Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), i [Windows dzięki przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Przyspieszona sieć jest obsługiwana dla jednostki SKU serię maszyn wirtualnych platformy Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 i Ms/Mms. Przyspieszona sieć został przetestowany pomyślnie za pomocą jednostki SKU Standard_DS8_v3 2019 1/23/dla klastra usługi Service Fabric Windows i używaniu Standard_DS12_v2 dla 2019-01/29 klaster usługi Service Fabric systemu Linux.

Aby Accelerated Networking w istniejącym klastrze usługi Service Fabric, trzeba wcześniej [skalowanie klastra usługi Service Fabric na zewnątrz, przez dodanie zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), wykonaj następujące czynności:
1. Aprowizowanie NodeType dzięki przyspieszonej sieci włączona
2. Migrowanie usług i ich stan do elastycznie NodeType dzięki przyspieszonej sieci włączona

Skalowanie infrastruktury jest wymagany do włączenia Accelerated Networking w istniejącym klastrze, ponieważ włączenie przyspieszonej sieci w miejscu mogłoby spowodować Przestój, ponieważ wymaga ona wszystkie maszyny wirtualne w zestawie dostępności można [zatrzymać i Cofnij Przydział przed włączeniem Accelerated networking na istniejącą kartą interfejsu Sieciowego](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Sieć klastrów

* Klastry usługi Service Fabric można wdrożyć w istniejącej sieci wirtualnej, wykonując czynności opisane w temacie [usługi Service Fabric sieć wzorców](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Sieciowe grupy zabezpieczeń (NSG) są zalecane w przypadku typów węzłów, które ograniczania ruchu przychodzącego i wychodzącego do ich klastra. Upewnij się, że wymagane porty są otwarte w sieciowej grupie zabezpieczeń. Na przykład: ![Reguły sieciowej grupy zabezpieczeń usługi Service Fabric][NSGSetup]

* Podstawowy typ węzła, który zawiera usługi systemowe Service Fabric nie muszą być udostępniane za pośrednictwem modułu równoważenia obciążenia zewnętrzne i które można uwidocznić przez [wewnętrznego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Użyj [statyczny publiczny adres IP](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) dla klastra.

## <a name="application-networking"></a>Aplikacja sieci

* Aby uruchomić obciążeń kontenerów Windows, użyj [Otwórz tryb sieci](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) ułatwienie komunikacji service-to-service.

* Używanie zwrotnego serwera proxy, takiej jak [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) lub [usługi Service Fabric zwrotny serwer proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) do udostępnienia typowe porty aplikacji, takich jak 80 i 443.

* Dla kontenerów Windows air gapped maszyn, których nie można ściągnąć warstwy podstawowa z magazynem w chmurze platformy Azure w serwisie zastąpić zachowanie obcego warstwy za pomocą [— Zezwalaj na nondistributable artefaktów](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) Flaga demona platformy Docker.

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
