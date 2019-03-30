---
title: Omówienie platformy Azure i autonomicznego usługi Service Fabric clusters | Dokumentacja firmy Microsoft
description: Na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server lub Linux, możesz utworzyć klastry usługi Service Fabric. Oznacza to, że jesteś w stanie wdrażania i uruchamiania aplikacji usługi Service Fabric w każdym środowisku, w którym masz zestaw systemu Windows Server lub komputery z systemem Linux, które są połączone ze sobą rozwiązań lokalnych, Microsoft Azure lub za pomocą dowolnego dostawcy chmury.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 6d5169d8ea4480e95e09228f9eb02bd78fdd0be8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661331"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Porównanie platformy Azure i autonomicznego usługi Service Fabric klastrów w systemie Windows Server i Linux
Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem klastra. Klastry można skalować do tysięcy węzłów. Po dodaniu nowych węzłów do klastra usługi Service Fabric rebalances replik partycji usługi i wystąpień na większą liczbę węzłów. Ogólna zwiększa wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzły w klastrze, możesz zmniejszyć liczbę węzłów w klastrze. Usługa Service Fabric ponownie rebalances replik partycji i wystąpień na obniżenie liczby węzłów, aby lepiej wykorzystać możliwości sprzętu na każdym węźle.

Usługa Service Fabric umożliwia tworzenie klastrów usługi Service Fabric na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server lub Linux. To oznacza, że jesteś w stanie wdrażania i uruchamiania aplikacji usługi Service Fabric w każdym środowisku, w którym masz zestaw komputerów Windows Server lub Linux, które są połączone ze sobą, można go lokalnie, Microsoft Azure lub za pomocą dowolnego dostawcy chmury.

## <a name="benefits-of-clusters-on-azure"></a>Zalety klastry na platformie Azure
Na platformie Azure firma Microsoft zapewnia integrację z usługą innych funkcji platformy Azure i usług, co sprawia, że operacje i zarządzania klastrem łatwiejsze i bardziej niezawodne.

* **Witryna Azure portal:** Azure portal ułatwia tworzenie klastrów i zarządzanie nimi.
* **Usługa Azure Resource Manager:** Korzystanie z usługi Azure Resource Manager umożliwia łatwe zarządzanie wszelkie zasoby używane przez klaster jako jednostki i ułatwia śledzenie kosztów i rozliczeń.
* **Klaster usługi Service Fabric jako zasobu platformy Azure** klastra usługi Service Fabric jest zasobem platformy Azure, dzięki czemu można modelować je tak jak inne zasoby na platformie Azure.
* **Integracja z infrastrukturą Azure** usługi Service Fabric koordynuje z podstawową infrastrukturą platformy Azure dla systemu operacyjnego, sieci i innych uaktualnień w celu zwiększenia dostępności i niezawodności aplikacji.  
* **Diagnostyka:** Na platformie Azure firma Microsoft zapewnia integrację z usługą Diagnostyka Azure i dzienniki usługi Azure Monitor.
* **Skalowanie automatyczne:** W przypadku klastrów na platformie Azure firma Microsoft zapewnia wbudowane funkcje automatycznego skalowania ze względu na zestawach skalowania maszyn wirtualnych. W lokalnych i innych środowisk w chmurze musisz utworzyć własne automatyczne skalowanie się funkcji lub skalowania ręcznie przy użyciu interfejsów API, który udostępnia usługi Service Fabric Skalowanie klastrów.

## <a name="benefits-of-standalone-clusters"></a>Zalety klastry autonomiczne
* Masz swobodę wyboru wśród dostawców chmury do obsługi klastra.
* Aplikacje usługi Service Fabric, napisane raz, można uruchomić w wielu środowiskach hostingu z minimalnym nie zmian.
* Wiedzę na temat tworzenia aplikacji usługi Service Fabric są przenoszone z jednego środowiska hostingu do innego.
* Środowisko operacyjne uruchamiania i zarządzania usługi Service Fabric clusters wykonuje za pośrednictwem z jednego środowiska do innego.
* Możliwości dotarcia do klientów szerokiego jest nieograniczona ograniczeń środowiska hostingu.
* Dodatkową warstwę niezawodności i ochrony przed awarii powszechne istnieje, ponieważ można przenosić usług za pośrednictwem do innego środowiska wdrażania, gdy dostawca danych Centrum lub w chmurze ma niedostępności.

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się z omówieniem programu [klastrów usługi Service Fabric, na platformie Azure](service-fabric-azure-clusters-overview.md)
* Zapoznaj się z omówieniem programu [klastry autonomicznego usługi Service Fabric](service-fabric-standalone-clusters-overview.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)