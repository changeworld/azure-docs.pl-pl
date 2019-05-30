---
title: Usługa Azure Container Instances — często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Container Instances
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 99882bd23d7b94afc550247172e5b70deb23bec9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791397"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Często zadawane pytania dotyczące usługi Azure Container Instances

W tym artykule opisano często zadawane pytania dotyczące usługi Azure Container Instances.

## <a name="deployment"></a>Wdrożenie

### <a name="how-large-can-my-container-image-be"></a>Jak duży może być Mój obraz kontenera?

Maksymalny rozmiar obrazu kontenera do wdrożenia w usłudze Azure Container Instances to 15 GB. Może mieć możliwość wdrażania obrazów większy w zależności od dostępności dokładnie w momencie wdrażania, ale to nie jest gwarantowana.

Wpływa na rozmiar obrazu kontenera, jak długo trwa wdrażanie, dlatego zazwyczaj chcesz zachować możliwie najmniejsze obrazów kontenerów.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Jak można przyspieszyć wdrożenie kontener?

Ponieważ jeden z głównych czynnikach określających czas wdrożenia jest rozmiar obrazu, należy szukać sposobów zmniejszyć rozmiar. Usuń warstwy, nie potrzebujesz lub Zmniejsz rozmiar warstw obrazu (wybierając jaśniejszy obraz podstawowy systemu operacyjnego). Na przykład jeśli korzystasz z kontenerów systemu Linux, rozważ użycie Alpine jako obrazu podstawowego, a nie cały serwer Ubuntu. Podobnie dla kontenerów Windows Użyj obrazu podstawowego systemu Nano Server, jeśli to możliwe. 

Należy także sprawdzić listę wstępnie pamięci podręcznej obrazów w obrazów kontenerów platformy Azure, dostępne za pośrednictwem [obrazów z pamięci podręcznej listy](/rest/api/container-instances/listcachedimages) interfejsu API. Można przełączyć warstwa obrazu dla jednego z wstępnie pamięci podręcznej obrazów. 

Zobacz więcej [szczegółowe wskazówki](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) na skrócenie czasu uruchamiania kontenera.

### <a name="what-windows-base-os-images-are-supported"></a>Jakie podstawowe obrazy systemu operacyjnego Windows są obsługiwane?

#### <a name="windows-server-2016-base-images"></a>Podstawowe obrazy systemu Windows Server 2016

* [Serwer nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Obrazy Windows oparte na wersji półroczny kanał 1709 lub 1803 nie są obsługiwane.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>2019 r Server systemu Windows i klientem obrazy podstawowe (wersja zapoznawcza)

* [Serwer nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Jakie platformy .NET lub .NET Core warstwy obrazu należy używać w kontener? 

Za pomocą najmniejszej obrazu, który spełnia Twoje wymagania. Dla systemu Linux, można użyć *alpine środowiska uruchomieniowego* obrazu platformy .NET Core, który ma obsługiwane począwszy od wersji programu .NET Core 2.1. Dla Windows, jeśli używasz pełny program .NET Framework, należy użyć obrazu systemu Windows Server Core (obraz tylko do środowiska uruchomieniowego, takich jak *4.7.2-windowsservercore-ltsc2016*). Tylko do obsługi obrazów są mniejsze, ale nie obsługują obciążeń, które wymagają zestawu .NET SDK.

## <a name="availability-and-quotas"></a>Dostępność i przydziały

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Jak wiele rdzeni i pamięci mam przydzielić dla mojego kontenerów lub grupę kontenerów?

To tak naprawdę zależy od obciążenia. Zacznij od czegoś małego i testowanie wydajności, aby zobaczyć, jak kontenerów. [Monitorowanie użycia zasobów procesora CPU i pamięci](container-instances-monitor.md), a następnie dodaj rdzeni lub pamięci, w oparciu o rodzaju procesy, które można wdrożyć w kontenerze. 

Upewnij się również [dostępności zasobów](container-instances-region-availability.md#availability---general) dla regionu są wdrażane w górną granicę na liczbę rdzeni Procesora i pamięci dostępnej dla każdej grupy kontenerów. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Jakie podstawowej infrastruktury czy działa ACI?

Usługa Azure Container Instances ma na celu się bez użycia serwera usługi kontenerów na żądanie, dzięki czemu firma Microsoft ma możesz koncentrować się na temat tworzenia kontenerów i nie martw się o infrastrukturę! Dla osób, które ciekawości lub żąda porównywania na wydajność, ACI działa na zestawy maszyn wirtualnych platformy Azure z różnych jednostek SKU, przede wszystkim z F i serii D. Oczekujemy, że ten element, aby w przyszłości zmianom w miarę kontynuowania do tworzenia i optymalizowania usługi. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Chcę przeprowadzić wdrożenie tysięcy rdzeni w usłudze ACI - Moje zwiększenia limitu przydziału można uzyskać?
 
Tak (czasami). Zobacz [limity przydziału i limity](container-instances-quotas.md) artykuł bieżące limity przydziału i które limity można zwiększyć przez żądanie.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Czy mogę wdrożyć więcej niż 4 rdzeni i 16 GB pamięci RAM?

Jeszcze nie. Obecnie są to maksymalne wartości dla grupy kontenerów. Przy użyciu określonych wymagań lub żądania, skontaktuj się z działem pomocy technicznej systemu Azure. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Kiedy będzie ACI w określonym regionie?

Bieżący dostępność w poszczególnych regionach jest publikowany [tutaj](container-instances-region-availability.md#availability---general). Jeśli masz wymagane dla określonego regionu, skontaktuj się z działem pomocy technicznej systemu Azure.

## <a name="features-and-scenarios"></a>Funkcje i scenariusze

### <a name="how-do-i-scale-a-container-group"></a>Jak skalować grupy kontenerów?

Skalowanie nie jest obecnie dostępny dla kontenerów i grupy kontenerów. Jeśli musisz uruchomić większej liczby wystąpień, należy użyć naszego interfejsu API do automatyzacji i Utwórz więcej żądań tworzenia grupy kontenerów w usłudze. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Jakie funkcje są dostępne dla wystąpień uruchomionych w niestandardowych sieci wirtualnej?

Wdrażanie grup kontenerów w sieci wirtualnej platformy Azure w wybranym i delegować prywatne adresy IP do grupy kontenerów do kierowania ruchu w sieci wirtualnej między zasobami platformy Azure. Wdrażanie grupy kontenerów w sieci wirtualnej jest obecnie w wersji zapoznawczej, a niektóre aspekty tej funkcji może ulec zmianie przed ogólnodostępnej (GA). Zobacz [ograniczenia wersji zapoznawczej](container-instances-vnet.md#preview-limitations) zaktualizowane informacje.

## <a name="pricing"></a>Cennik

### <a name="when-does-the-meter-start-running"></a>Gdy licznik rozpocząć uruchomiona?

Czas korzystania z grupy kontenerów jest obliczonym na podstawie czasu, który Rozpoczniemy pracę ściągnąć obraz swój pierwszy kontener (na potrzeby nowego wdrożenia) lub przez grupę kontenerów zostanie uruchomione ponownie (jeśli jest już wdrożony), dopóki nie zostanie zatrzymana grupy kontenerów. Szczegółowe informacje o [cenach wystąpień kontenera](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Zatrzymać naliczane, gdy mój kontenery są zatrzymane?

Liczniki zatrzymać po zatrzymaniu grupy całego kontenera. Tak długo, jak działa kontenerów w grupie kontenerów, firma Microsoft przechowywania zasobów, w przypadku, gdy chcesz uruchamiać kontenery ponownie. 

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej](container-instances-overview.md) o usłudze Azure Container Instances.
* [Rozwiązywanie typowych problemów z](container-instances-troubleshooting.md) w usłudze Azure Container Instances.