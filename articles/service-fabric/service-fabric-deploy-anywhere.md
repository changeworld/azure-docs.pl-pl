---
title: Tworzenie klastrów usługi Service Fabric w systemie Windows Server i Linux | Dokumentacja firmy Microsoft
description: Klastry usługi Service Fabric z systemem Windows Server i Linux, co oznacza, że będziesz mieć możliwość wdrażania i hostów dowolnym miejscu aplikacji usługi Service Fabric można uruchomić system Windows Server lub Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.openlocfilehash: e4540076b29cf3cd51f03239a1868e18a41781d9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726529"
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Tworzenie klastrów usługi Service Fabric w systemie Windows Server lub Linux
Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem klastra. Klastry można skalować do tysięcy węzłów. Po dodaniu nowych węzłów do klastra usługi Service Fabric rebalances replik partycji usługi i wystąpień na większą liczbę węzłów. Ogólna zwiększa wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzły w klastrze, możesz zmniejszyć liczbę węzłów w klastrze. Usługa Service Fabric ponownie rebalances replik partycji i wystąpień na obniżenie liczby węzłów, aby lepiej wykorzystać możliwości sprzętu na każdym węźle.

Usługa Service Fabric umożliwia tworzenie klastrów usługi Service Fabric na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server lub Linux. To oznacza, że jesteś w stanie wdrażania i uruchamiania aplikacji usługi Service Fabric w każdym środowisku, w którym masz zestaw komputerów Windows Server lub Linux, które są połączone ze sobą, można go lokalnie, Microsoft Azure lub za pomocą dowolnego dostawcy chmury.

## <a name="create-service-fabric-clusters-on-azure"></a>Tworzenie klastrów usługi Service Fabric na platformie Azure
Tworzenie klastra na platformie Azure odbywa się za pośrednictwem szablonu Model zasobów lub [witryny Azure portal](https://portal.azure.com). Odczyt [tworzenia klastra usługi Service Fabric za pomocą szablonu usługi Resource Manager](service-fabric-cluster-creation-via-arm.md) lub [Tworzenie klastra usługi Service Fabric w witrynie Azure portal](service-fabric-cluster-creation-via-portal.md) Aby uzyskać więcej informacji.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Obsługiwane systemy operacyjne dla klastry na platformie Azure
Jesteś w stanie Tworzenie klastrów na maszynach wirtualnych z tymi systemami operacyjnymi:

* Windows Server 2012 R2
* Windows Server 2016 
* System Windows Server w wersji 1709
* System Windows Server w wersji 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (Obsługa wersji zapoznawczej)

> [!NOTE]
> Jeśli zdecydujesz się wdrożyć usługi Service Fabric w systemie Windows Server w wersji 1709, należy pamiętać, że (1) nie jest długoterminowej, gałąź, obsługi, więc może trzeba będzie przenosić w przyszłości wersji i (2) Jeśli wdrażanie kontenerów, kontenery utworzone w systemie Windows Server 2016 nie działają w systemie Windows Server  1709 i na odwrót (trzeba będzie ponownie skompilować je wdrażać).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Tworzenie autonomicznego usługi Service Fabric clusters lokalnie lub za pomocą dowolnego dostawcy chmury
Usługa Service Fabric udostępnia pakiet instalacji w celu utworzenia autonomicznego usługi Service Fabric clusters lokalnie lub na wszystkich dostawców chmury.

Więcej informacji na temat konfigurowania autonomicznego usługi Service Fabric klastrów w systemie Windows Server, przeczytaj [tworzenia klastra usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Klastry autonomiczne nie są obecnie obsługiwane dla systemu Linux. Linux jest obsługiwana w jedną pole rozwoju i w klastrach obejmujących wiele maszyn z systemem Linux platformy Azure.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Wszystkie wdrożenia chmury, a lokalnych wdrożeń
Proces tworzenia klastra usługi Service Fabric w środowisku lokalnym jest podobny do procesu tworzenia klastra w dowolnej chmurze wybranych przez użytkownika za pomocą zestawu maszyn wirtualnych. Początkowe kroki, aby zainicjować obsługę maszyn wirtualnych podlegają dostawcy usług w chmurze lub środowisku lokalnym, którego używasz. Po utworzeniu zestawu maszyn wirtualnych z połączeniem sieciowym włączone między nimi, następnie kroki, aby skonfigurować pakiet usługi Service Fabric, edytować ustawienia klastra i uruchom Tworzenie klastra i skrypty zarządzania są identyczne. Dzięki temu swojej wiedzy i doświadczenia, obsługi i zarządzania klastrami usługi Service Fabric przenoszeniu po wybraniu pod kątem nowego środowiska hostingu.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Korzyści wynikające z tworzenia autonomicznych klastrów usługi Service Fabric
* Masz swobodę wyboru wśród dostawców chmury do obsługi klastra.
* Aplikacje usługi Service Fabric, napisane raz, można uruchomić w wielu środowiskach hostingu z minimalnym nie zmian.
* Wiedzę na temat tworzenia aplikacji usługi Service Fabric są przenoszone z jednego środowiska hostingu do innego.
* Środowisko operacyjne uruchamiania i zarządzania usługi Service Fabric clusters wykonuje za pośrednictwem z jednego środowiska do innego.
* Możliwości dotarcia do klientów szerokiego jest nieograniczona ograniczeń środowiska hostingu.
* Dodatkową warstwę niezawodności i ochrony przed awarii powszechne istnieje, ponieważ można przenosić usług za pośrednictwem do innego środowiska wdrażania, gdy dostawca danych Centrum lub w chmurze ma niedostępności.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Obsługiwane systemy operacyjne dla autonomicznych klastrów
Jesteś w stanie Tworzenie klastrów na maszynach wirtualnych lub komputerach z tymi systemami operacyjnymi (Linux nie jest jeszcze obsługiwany):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Korzyści wynikające z klastrami usługi Service Fabric na platformie Azure za pośrednictwem autonomicznego usługi Service Fabric clusters utworzone w środowisku lokalnym
Z klastrami usługi Service Fabric na platformie Azure zapewnia opcję zalet w porównaniu z lokalnym, więc jeśli nie ma określonych potrzeb, na którym uruchamiasz klastry usługi, a następnie Sugerujemy, uruchamiaj je na platformie Azure. Na platformie Azure firma Microsoft zapewnia integrację z usługą innych funkcji platformy Azure i usług, co sprawia, że operacje i zarządzania klastrem łatwiejsze i bardziej niezawodne.

* **Witryna Azure portal:** witryny Azure portal umożliwia łatwe tworzenie klastrów i zarządzanie nimi.
* **Usługa Azure Resource Manager:** użycia usługi Azure Resource Manager umożliwia łatwe zarządzanie wszelkie zasoby używane przez klaster jako jednostki i ułatwia śledzenie kosztów i rozliczeń.
* **Klaster usługi Service Fabric jako zasobu platformy Azure** klastra usługi Service Fabric jest zasobem platformy Azure, dzięki czemu można modelować je tak jak inne zasoby na platformie Azure.
* **Integracja z infrastrukturą Azure** usługi Service Fabric koordynuje z podstawową infrastrukturą platformy Azure dla systemu operacyjnego, sieci i innych uaktualnień w celu zwiększenia dostępności i niezawodności aplikacji.  
* **Diagnostyka:** na platformie Azure, firma Microsoft zapewnia integrację z diagnostyki platformy Azure i usługi Log Analytics.
* **Automatyczne skalowanie:** przypadku klastry na platformie Azure, firma Microsoft oferuje wbudowane funkcje automatycznego skalowania ze względu na zestawach skalowania maszyn wirtualnych. W lokalnych i innych środowisk w chmurze musisz utworzyć własne automatyczne skalowanie się funkcji lub skalowania ręcznie przy użyciu interfejsów API, który udostępnia usługi Service Fabric Skalowanie klastrów.

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [tworzenia klastra usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

