---
title: Omówienie platformy Azure i autonomicznych klastrów sieci szkieletowej usług
description: Klastry sieci szkieletowej usług można tworzyć na dowolnej maszynie wirtualnej lub na komputerach z systemem Windows Server lub Linux. Oznacza to, że można wdrażać i uruchamiać aplikacje sieci szkieletowej usług w dowolnym środowisku, w którym masz zestaw komputerów z systemem Windows Server lub Linux, które są ze sobą połączone lokalnie, microsoft azure lub z dowolnym dostawcą chmury.
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751141"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Porównywanie klastrów platformy Azure i autonomicznej sieci szkieletowej usług w systemach Windows Server i Linux

Klaster sieci szkieletowej usług to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem klastra. Klastry można skalować do tysięcy węzłów. Jeśli dodasz nowe węzły do klastra, sieci szkieletowej usług równoważe repliki partycji usługi i wystąpienia w zwiększonej liczbie węzłów. Ogólna wydajność aplikacji poprawia i rywalizacji o dostęp do pamięci zmniejsza. Jeśli węzły w klastrze nie są używane efektywnie, można zmniejszyć liczbę węzłów w klastrze. Sieci szkieletowej usług ponownie równoważy repliki partycji i wystąpień w całej zmniejszonej liczby węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

Sieci szkieletowej usług umożliwia tworzenie klastrów sieci szkieletowej usług na dowolnej maszynie wirtualnej lub komputerach z systemem Windows Server lub Linux. Oznacza to, że można wdrażać i uruchamiać aplikacje sieci szkieletowej usług w dowolnym środowisku, w którym masz zestaw komputerów z systemem Windows Server lub Linux, które są ze sobą połączone, czy to lokalnie, microsoft azure lub z dowolnym dostawcą chmury.

## <a name="benefits-of-clusters-on-azure"></a>Korzyści płynące z klastrów na platformie Azure

Na platformie Azure zapewniamy integrację z innymi funkcjami i usługami platformy Azure, co sprawia, że operacje i zarządzanie klastrem są łatwiejsze i bardziej niezawodne.

* **Portal Azure:** Portal Azure ułatwia tworzenie klastrów i zarządzanie nimi.
* **Usługa Azure Resource Manager:** Korzystanie z usługi Azure Resource Manager umożliwia łatwe zarządzanie wszystkimi zasobami używanymi przez klaster jako jednostką i upraszcza śledzenie kosztów i rozliczenia.
* **Klaster sieci szkieletowej usług jako zasób platformy Azure** Klaster sieci szkieletowej usług jest zasobem platformy Azure, dzięki czemu można go modelować tak, jak inne zasoby na platformie Azure.
* **Integracja z infrastrukturą platformy Azure** Sieć szkieletowa usług koordynuje współrzędne z podstawową infrastrukturą platformy Azure dla systemu operacyjnego, sieci i innych uaktualnień, aby zwiększyć dostępność i niezawodność aplikacji.  
* **Diagnostyka:** Na platformie Azure zapewniamy integrację z diagnostyką platformy Azure i dziennikami usługi Azure Monitor.
* **Automatyczne skalowanie:** W przypadku klastrów na platformie Azure udostępniamy wbudowaną funkcję automatycznego skalowania dzięki zestawom skalowania maszyny wirtualnej. W środowiskach lokalnych i innych środowiskach w chmurze należy utworzyć własną funkcję automatycznego skalowania lub skalować ręcznie przy użyciu interfejsów API udostępnianych przez sieci szkieletowej usług do skalowania klastrów.

## <a name="benefits-of-standalone-clusters"></a>Zalety klastrów autonomicznych

* Możesz wybrać dowolnego dostawcę chmury do hostowania klastra.
* Aplikacje sieci szkieletowej usług, po zapisaniu, mogą być uruchamiane w wielu środowiskach hostingowych bez zmian.
* Znajomość tworzenia aplikacji sieci szkieletowej usług przenosi się z jednego środowiska hostingowego do drugiego.
* Doświadczenie operacyjne w zakresie uruchamiania klastrów sieci szkieletowej usług i zarządzania nimi przenosi się z jednego środowiska do drugiego.
* Szeroki zasięg klientów nie jest związany z ograniczeniami środowiska hostingu.
* Istnieje dodatkowa warstwa niezawodności i ochrony przed powszechnymi awariami, ponieważ można przenieść usługi do innego środowiska wdrażania, jeśli centrum danych lub dostawca chmury ma przerwę w dostawie prądu.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj omówienie [klastrów sieci szkieletowej usług na platformie Azure](service-fabric-azure-clusters-overview.md)
* Przeczytaj omówienie [autonomicznych klastrów sieci szkieletowej usług](service-fabric-standalone-clusters-overview.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)