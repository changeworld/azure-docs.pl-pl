---
title: Najważniejsze wskazówki dotyczące sieci szkieletowej usług Azure
description: Najważniejsze wskazówki i zagadnienia dotyczące projektowania dotyczące zarządzania łącznością sieciową przy użyciu sieci szkieletowej usługi Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551798"
---
# <a name="networking"></a>Obsługa sieci

Podczas tworzenia klastrów sieci szkieletowej usług Azure i zarządzania nimi zapewniasz łączność sieciową dla węzłów i aplikacji. Zasoby sieciowe obejmują zakresy adresów IP, sieci wirtualne, moduły równoważenia obciążenia i sieciowe grupy zabezpieczeń. W tym artykule dowiesz się najlepszych rozwiązań dla tych zasobów.

Przejrzyj [wzorce sieci sieciowej usługi Azure,](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) aby dowiedzieć się, jak tworzyć klastry korzystające z następujących funkcji: Istniejąca sieć wirtualna lub podsieć, statyczny publiczny adres IP, moduł równoważenia obciążenia tylko wewnętrznego lub wewnętrzny i zewnętrzny moduł równoważenia obciążenia.

## <a name="infrastructure-networking"></a>Tworzenie sieci infrastrukturalnych
Zmaksymalizuj wydajność maszyny wirtualnej dzięki przyspieszonej sieci, deklarując właściwość enableAcceleratedNetworking w szablonie Menedżera zasobów, poniższy fragment kodu jest zgodny z konfiguracją networkinterfaceconfigurations zestawu skalowania maszyny wirtualnej, która umożliwia przyspieszoną sieć:

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
Klaster sieci szkieletowej usług można aprowizować w [systemie Linux z przyspieszoną siecią](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)i [windowsem z przyspieszoną siecią](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Przyspieszona sieć jest obsługiwana dla jednostek SKU z serii maszyn wirtualnych platformy Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 i Ms/Mms. Przyspieszona sieć została pomyślnie przetestowana przy użyciu jednostki SKU Standard_DS8_v3 w dniu 23.1.2019 r. dla klastra systemu Windows sieci szkieletowej usług i przy użyciu Standard_DS12_v2 w dniu 29.01.2019 r. dla klastra linuksa sieci szkieletowej usług.

Aby włączyć przyspieszoną sieć w istniejącym klastrze sieci szkieletowej usług, należy najpierw [skalować klaster sieci szkieletowej usług, dodając zestaw skalowania maszyny wirtualnej,](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)aby wykonać następujące czynności:
1. Aprowizowanie typu węzła z włączoną obsługą przyspieszonej sieci
2. Migrowanie usług i ich stanu do aprowizowanego typu NodeType z włączoną obsługą przyspieszonej sieci

Skalowanie w poziomie infrastruktury jest wymagane do włączenia przyspieszonej sieci w istniejącym klastrze, ponieważ włączenie przyspieszonej sieci w miejscu spowodowałoby przestoje, ponieważ wymaga, aby wszystkie maszyny wirtualne w zestawie dostępności [były zatrzymywały i zwalniane przed włączeniem przyspieszonej sieci sieciowej na dowolnej istniejącej karcie sieciowej](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Sieć klastrów

* Klastry sieci szkieletowej usług można wdrożyć w istniejącej sieci wirtualnej, wykonując kroki opisane w [wzorcach sieci szkieletowej usług.](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)

* Sieciowe grupy zabezpieczeń (NSG) są zalecane dla typów węzłów, które ograniczają ruch przychodzący i wychodzący do ich klastra. Upewnij się, że niezbędne porty są otwarte w nsg. Na przykład: ![Reguły sieciowej sieciowej sieci szkieletowej usług][NSGSetup]

* Typ węzła podstawowego, który zawiera usługi systemu sieci szkieletowej usług, nie musi być narażony za pośrednictwem zewnętrznego modułu równoważenia obciążenia i może być narażony przez [wewnętrzny moduł równoważenia obciążenia](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Użyj [statycznego publicznego adresu IP](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) dla klastra.

## <a name="application-networking"></a>Sieć aplikacji

* Aby uruchomić obciążenia kontenerów systemu Windows, użyj [trybu otwartej sieci,](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) aby ułatwić komunikację między usługami.

* Użyj odwrotnego serwera proxy, takiego jak [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) lub [odwrotnego serwera proxy sieci szkieletowej usług,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) aby udostępnić typowe porty aplikacji, takie jak 80 lub 443.

* W przypadku kontenerów systemu Windows hostowanych na komputerach typu air-gapped, które nie mogą pobierać warstw bazowych z magazynu w chmurze platformy Azure, zastąp zachowanie warstwy obcej, używając flagi [--allow-nondistributable-artifacts](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) w demonie platformy Docker.

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra sieci szkieletowej usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
