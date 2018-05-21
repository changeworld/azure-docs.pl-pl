---
title: Tworzenie klastrów usługi sieć szkieletowa usług Azure w systemie Windows Server i Linux | Dokumentacja firmy Microsoft
description: Klastrów sieci szkieletowej usług w systemie Windows Server i Linux, co oznacza, że będziesz mieć możliwość wdrażania i dowolnego miejsca aplikacji sieci szkieletowej usług hosta można uruchomić serwera systemu Windows lub Linux.
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
ms.date: 02/28/2018
ms.author: dekapur
ms.openlocfilehash: 3d427d99f6919991c29fc5947ebe0082670a1cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Tworzenie klastrów sieci szkieletowej usług na serwerze z systemem Windows lub Linux
Klaster sieci szkieletowej usług Azure to zestaw połączonych z siecią maszyn wirtualnych lub fizycznych, w których są wdrożone i zarządzane z mikrousług. Komputer lub maszynę Wirtualną, która jest częścią klastra jest nazywany węzłem klastra. Klastrów można skalować do tysięcy węzłów. Po dodaniu nowych węzłów do klastra usługi sieć szkieletowa rebalances replik partycji usługi i wystąpień przez zwiększenie liczby węzłów. Ogólne poprawia wydajność aplikacji i zmniejsza rywalizacji o dostęp do pamięci. Jeśli nie są wydajnie używane węzłów w klastrze, można zmniejszyć liczbę węzłów w klastrze. Sieć szkieletowa usług ponownie rebalances replik partycji i wystąpień między zmniejszyć liczbę węzłów w celu lepszego wykorzystania sprzętu na każdym węźle.

Sieć szkieletowa usług pozwala na tworzenie klastrów sieci szkieletowej usług na dowolnym maszyn wirtualnych lub komputerach z systemem Windows Server lub Linux. Oznacza to, możliwe jest wdrażanie i uruchamianie aplikacji sieci szkieletowej usług w każdym środowisku, w którym znajduje się zestaw komputerów systemu Windows Server lub Linux, które są połączone ze sobą, można go w infrastrukturze lokalnej, Microsoft Azure lub u innego dostawcy chmury.

## <a name="create-service-fabric-clusters-on-azure"></a>Tworzenie klastrów sieci szkieletowej usług na platformie Azure
Tworzenie klastra na platformie Azure jest przeprowadzana albo za pomocą szablonu modelu zasobów lub [portalu Azure](https://portal.azure.com). Odczyt [tworzenia klastra usługi sieć szkieletowa usług za pomocą szablonu usługi Resource Manager](service-fabric-cluster-creation-via-arm.md) lub [Tworzenie klastra sieci szkieletowej usług z portalu Azure](service-fabric-cluster-creation-via-portal.md) Aby uzyskać więcej informacji.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Obsługiwane systemy operacyjne dla klastrów w systemie Azure
Masz możliwość tworzenia klastrów na maszynach wirtualnych z tymi systemami operacyjnymi:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Linux Ubuntu 16.04

> [!NOTE]
> Jeśli zdecydujesz się wdrożyć sieć szkieletowa usług na 1709 serwera systemu Windows, należy pamiętać, że (1) nie jest długim okresie obsługi gałęzi, więc musisz przenieść wersji w przyszłości i (2) Jeśli wdrażanie kontenerów, kontenery oparty na systemie Windows Server 2016 nie działają w systemie Windows Server  1709 i na odwrót (należy odbudować je, aby wdrożyć je).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Tworzenie autonomicznej usługi sieć szkieletowa klastrów lokalnymi lub z każdego dostawcy chmury
Usługa Service Fabric realizuje pakiet instalacyjny do utworzenia autonomicznej usługi sieć szkieletowa klastrów lokalnymi lub u innego dostawcy chmury.

Więcej informacji na temat konfigurowania autonomicznej usługi sieć szkieletowa klastrów w systemie Windows Server, przeczytaj [tworzenia klastra usługi sieć szkieletowa usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Klastry autonomicznego obecnie nie są obsługiwane dla systemu Linux. Linux jest obsługiwana w jednego pola do prac deweloperskich i Azure Linux klastrach obejmujących wiele maszyn.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Wszystkie wdrożenia chmury i wdrożeniach lokalnych
Proces tworzenia lokalnego klastra usługi sieć szkieletowa jest podobny do procesu tworzenia klastra na żadną chmurą dowolnego z zestawem maszyn wirtualnych. Początkowe kroki do obsługi administracyjnej maszyn wirtualnych są regulowane dostawcy usług w chmurze lub środowiska lokalnego, którego używasz. Po utworzeniu zestawu maszyn wirtualnych z połączeniem sieciowym włączone między nimi, następnie kroki, aby skonfigurować pakiet sieci szkieletowej usług, Edytuj ustawienia klastra, a następnie uruchom utworzenie klastra i skrypty zarządzania są identyczne. Dzięki temu Twojej wiedzy i doświadczenia, obsługi i zarządzania klastrami usługi sieć szkieletowa usług są przeniesieniu po wybraniu pod kątem nowych środowiskach hostingu.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Korzyści wynikające z tworzenia autonomicznych klastrów sieci szkieletowej usług
* Jesteś swobodę każdego dostawcy chmury do obsługi klastra.
* Aplikacji usługi Service Fabric, raz zapisywane, może działać w wielu środowiskach hostingu z minimalnym do żadnych zmian.
* Wiedzę na temat tworzenia aplikacji sieci szkieletowej usług są przenoszone z jednego środowiska hostingu na inny.
* Środowisko operacyjne uruchamiania i zarządzania sieci szkieletowej usług klastrów wykonuje za pośrednictwem z jednego środowiska do innego.
* Zasięg szerokie klienta jest niepowiązany, udostępniając ograniczenia środowiska.
* Dodatkową warstwę niezawodności i ochrony przed powszechnie awarie istnieje, ponieważ można przenosić usług za pośrednictwem innego środowiska wdrażania, gdy dostawca chmury lub centrum danych ma niedostępności.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Obsługiwane systemy operacyjne dla autonomicznych klastrów
Masz możliwość tworzenia klastrów na maszynach wirtualnych lub komputerach z tymi systemami operacyjnymi (Linux nie jest jeszcze obsługiwana):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Zalety klastrów sieci szkieletowej usług na platformie Azure za pośrednictwem klastrów sieci szkieletowej usług autonomiczny utworzony lokalnie
Działające klastry usługi sieć szkieletowa usług Azure zapewnia opcję zalet w porównaniu z lokalną, tak więc jeśli nie ma określonych potrzeb, na którym uruchamiasz klastrów, a następnie Sugerujemy, uruchom je na platformie Azure. Na platformie Azure udostępniamy integracji z innymi usługami, dzięki czemu operacje i zarządzania klastrem, łatwiejsze i bardziej niezawodny i funkcje platformy Azure.

* **Portalu Azure:** portalu Azure ułatwia tworzenie i zarządzanie klastrami.
* **Usługa Azure Resource Manager:** użycia usługi Azure Resource Manager umożliwia łatwe zarządzanie wszelkie zasoby używane przez klaster jako jednostki i upraszcza śledzenie kosztów i rozliczeń.
* **Klastra sieci szkieletowej usług jako zasób Azure** klastra sieci szkieletowej usług A jest zasobem platformy Azure, dlatego można ją modelu, jak innymi zasobami na platformie Azure.
* **Integracja z infrastrukturą platformy Azure** sieci szkieletowej usług koordynuje z podstawowej infrastruktury platformy Azure dla systemu operacyjnego, sieci i innych uaktualnień w celu zwiększenia dostępności i niezawodności aplikacji.  
* **Diagnostyka:** na platformie Azure, udostępniamy integracji z diagnostyki Azure i analizy dzienników.
* **Automatyczne skalowanie:** klastrów na platformie Azure, firma Microsoft udostępnia wbudowaną funkcję skalowania automatycznego z powodu zestawy skalowania maszyny wirtualnej. W lokalnych i dostępnych w innych środowiskach chmury należy do tworzenia własnych automatyczne skalowanie się funkcji lub ręcznie przy użyciu interfejsów API, które udostępnia usługi sieć szkieletowa skalowania klastrów skali.

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [tworzenia klastra usługi sieć szkieletowa usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [utworzyć klaster systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

